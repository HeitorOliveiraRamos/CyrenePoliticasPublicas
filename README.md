# CyreneBot

Bot de Discord em Kotlin + Spring Boot para uma comunidade de **Honkai: Star Rail**, com IA local
(Ollama). Faz três coisas, por caminhos separados de propósito:

1. **Conversa** em persona (menções e sessões de chat);
2. **Responde sobre HSR** com dados reais — kits, cones, relíquias, builds, lore — sempre ancorado
   numa fonte, nunca na memória do modelo;
3. **Desenha cards PNG** (guia, ascensão, avaliação de vitrine, tier list) e faz moderação por
   comandos de barra.

Nada passa por API paga: LLM no Ollama local, Postgres local, busca web (opcional) num SearXNG em
Docker.

> **Instalar numa máquina nova:** [SETUP.md](SETUP.md). Este arquivo é a referência de como o bot
> funciona.

## Requisitos

| Ferramenta | Versão | Pra quê |
|---|---|---|
| **JDK** | 21+ | bytecode alvo é 21 |
| **Maven** | 3.9+ | build |
| **PostgreSQL** | 14+ (testado em 18) | banco + vector store |
| **pgvector** | recente | extensão de vetores — precisa de superuser pra criar, uma vez |
| **Ollama** | recente | LLM de chat + embeddings |
| **Docker** | qualquer | **opcional**, só pro SearXNG |

```bash
ollama pull nomic-embed-text     # embeddings, 768 dims — TEM que casar com pgvector.dimensions
```

O embedding é o único modelo fixo (trocá-lo muda a dimensão dos vetores e exige `reindex`). Os
três slots de chat — **voz**, **cérebro**, **visão** — são escolhidos em runtime pelo
`./bot.sh --model`, que lista o que já está instalado; um modelo multimodal serve os três de uma
vez, e é assim que roda hoje. Este README deliberadamente **não** fixa nomes: eles mudam mais
rápido que o texto, e a lista que vale é a do `ollama list`. Os defaults de fallback estão em
[`application.yml`](src/main/resources/application.yml).

macOS, Linux e Windows via WSL2. O `bot.sh` detecta a infra por capacidade (`brew` → `pg_ctl` →
`systemctl`), não por distro.

## Começando

```bash
# .env é por máquina e não tem template no repo — ver SETUP.md §5 pro mínimo
./bot.sh reindex           # 1x: builda, sobe infra e constrói a base de conhecimento
./bot.sh start             # sobe tudo e roda o bot em background
```

Migrations (Flyway) rodam sozinhas no primeiro boot. `mvn clean package` gera
`target/cyrene-bot-2.0.0.jar`, que é o que o `bot.sh` executa.

## Comandos

24 comandos, registrados no boot a partir dos beans que implementam
[`SlashCommand`](src/main/kotlin/com/cyrene/discord/command/SlashCommand.kt) — um `@Component` novo
já entra no roteamento.

**HSR**

| Comando | O que faz |
|---|---|
| `/hsr <pergunta>` | pergunta direta pra base — pula o portão de intenção, nunca é confundida com papo |
| `/build <personagem> [arte]` | avalia a build da sua vitrine: nota StarRailScore, peça a peça, o que farmar. Zero LLM |
| `/uid [uid]` | vincula seu UID do jogo (usado pelo `/build`). Sempre efêmero |
| `/guia <personagem> [arte]` | formulário que gera o card de guia de build |
| `/ascensao <personagem> [arte]` | card com o custo de ascensão + rastros |
| `/tierlist <modo> [versao] [anonimo]` | tier list de fim de jogo (MoC / Pura Ficção / Sombra Apocalíptica) |

**Conversa**

| Comando | O que faz |
|---|---|
| `/iniciar-conversa` · `/encerrar-conversa` | abre/fecha uma sessão: no meio dela não precisa mencionar |
| `/memoria` | modal onde você escreve o que o bot deve lembrar de você. Campo vazio apaga |
| `/apagar-meus-dados confirmar:` | apaga perfil, UID, memória, conversas e notas de build. Ficam os `/avisos` e o que você publicou, sem seu nome ([`Retencao`](src/main/kotlin/com/cyrene/conversation/Retencao.kt)) |
| `/contexto-do-canal` | resumo do que está rolando no canal |
| `/ia estado:ligar\|desligar` | chave geral da IA, só a dona do servidor. Desligada, moderação e `/build` continuam |

**Moderação** — `/banir` `/desbanir` `/expulsar` `/mutar` `/desmutar` `/avisar` `/avisos` `/limpar`
`/modo-lento` `/cargo` `/criar-canal` `/info-membro` `/info-servidor`.

Quem pode executar é declarado com `setDefaultPermissions(...)` e enforçado pelo **Discord**, antes
da interação chegar aqui. [`ModerationGuards`](src/main/kotlin/com/cyrene/discord/command/ModerationGuards.kt)
adiciona o que o Discord não checa: se o bot alcança o alvo e se quem chamou está **acima** dele na
hierarquia.

> **A IA não pode moderar**: o modelo não tem nenhuma ferramenta, toda passada é texto-entra/
> texto-sai. Pedido de moderação em prosa é respondido em código, apontando o comando certo.
>
> `TEST_CHANNEL_IDS` restringe só as features de IA. DMs nunca são restringidas.

## Como o bot responde

```
imagem anexada?  ──► VisionService transcreve e anexa ao turno
pedido de moderação em prosa?  ──► responde "use /banir" (sem LLM)
portão de intenção  ──►  CHAT (voz em persona)  |  CONHECIMENTO ──┐
                                                                  ▼
   1. serviços determinísticos  (Plan/Build/Kit/Lore/Item/Roster) — SQL nas tabelas V17,
      template fixo, sem LLM no corpo da resposta
   2. cache de resposta         (pergunta normalizada → resposta; zero LLM num acerto)
   3. grounding                 base local (pgvector) → web (SearXNG); notícia inverte a ordem
   4. voz                       o modelo só reconta o que a fonte disse
   5. juiz                      respostas de origem web passam por um veredito antes de sair
```

- **Contexto = cadeia de reply**, não o canal: menção nova começa limpa, responder sobe a thread.
  Duas threads no mesmo canal nunca se misturam.
- **A busca é decisão do código.** Pedir pra um modelo local "chame `lookupHsr`" falha de duas
  formas: ele não chama, ou chama, volta vazio e inventa. Por isso o
  [`KnowledgeGrounder`](src/main/kotlin/com/cyrene/knowledge/KnowledgeGrounder.kt) roda a
  recuperação em ordem fixa e entrega pra voz **só** o que uma fonte real devolveu.
- **Abstenção é a rede de segurança.** Sem fonte, o bot diz que não sabe. É o que torna "a
  personagem Lilita do caminho Eclipse" impossível.
- **Determinístico primeiro.** Toda pergunta que uma tabela responde vira template — pedir pra um
  modelo pequeno recompor isso da prosa só adiciona entropia (efeito de uma relíquia migrando pra
  outra).
- Na tela: "digitando…", linha de status com botão **Cancelar**, resposta longa paginada com ◀ ▶
  (o texto fica no Postgres, então os botões sobrevivem a restart), e no máximo 2 pipelines
  batendo no Ollama ao mesmo tempo (cheio = "tô ocupada" na hora).

## Base de conhecimento

Fontes, nenhuma com chave de API: **starrailstation** (kit em PT + ícones), **nanoka** (JSON, versão
do patch, arte de betas), **StarRailRes** (nomes en/pt/es), **fribbels** e **StarRailScore**
(scoring do `/build`), **SearXNG** (busca web opcional).

```
srs + nanoka ──populate──► tabelas V17 (Postgres) ──reindex──► vector_store (pgvector)
```

**Populate** busca upstream e escreve as tabelas ricas (upsert idempotente; colheita implausível é
abortada sem gravar). **Reindex** só lê essas tabelas e re-embeda — carrega os documentos *antes* do
truncate, então uma leitura vazia nunca apaga uma base que funciona. As tabelas são a fonte da
verdade; o vector store é o fallback semântico.

O [`KbFreshnessCheck`](src/main/kotlin/com/cyrene/knowledge/KbFreshnessCheck.kt) roda diário e, com
`HSR_AUTO_REINDEX=true` (padrão), refaz o ciclo sozinho quando sai patch. Manual: `./bot.sh reindex`.

> Trocar o modelo de **embedding** exige reindex — a dimensão tem que casar com
> `pgvector.dimensions`. Por isso é o único slot não trocável em runtime.

## `/build`

`UID (/uid) → Enka → (falhou) mihomo → BuildAnalyzer → card`. Sem LLM em lugar nenhum.

A Enka devolve o payload cru: os nomes são localizados pelo `StarRailResNames` e o painel de combate
é **calculado** pelo [`StatPanel`](src/main/kotlin/com/cyrene/hsr/StatPanel.kt) — ninguém serve esse
bloco pronto. O [`BuildAnalyzer`](src/main/kotlin/com/cyrene/hsr/BuildAnalyzer.kt) implementa a
fórmula StarRailScore, então a nota bate com os scorers da comunidade; o julgamento (main stat
errado, rolls mortos, o que farmar) também é código.

## Cards e formulários

Quatro cards em Java2D. O renderer é uma **função pura** do seu modelo de entrada (`Ficha`,
`Ascensao`, `Avaliacao`, `TierList`) — sem Spring, sem banco — o que faz os previews standalone
valerem: o que renderiza local renderiza igual no canal.

```bash
mvn -q -DskipTests compile spring-boot:run \
  -Dspring-boot.run.main-class=com.cyrene.card.AscensaoPreviewStandaloneKt \
  -Dspring-boot.run.arguments=Blade
```

- Nada é elipsado: o texto quebra linha e depois encolhe a fonte até caber.
- webp decodifica com `com.github.usefulness:webp-imageio` (libwebp via JNI). TwelveMonkeys deixa
  um amarelado medido em toda splash — nunca ponha os dois no classpath.
- Arte enviada pelo membro volta como preview privado com controles de enquadramento, compartilhados
  por `/guia`, `/ascensao` e `/build` ([`Enquadramento`](src/main/kotlin/com/cyrene/card/Enquadramento.kt)).
- **Formulários não têm mapa de sessão**: a chave do rascunho viaja no `customId` e as respostas
  moram no Postgres. Sobrevivem a restart e ao timeout de 15 min, e dois cliques nunca discordam.
  Guia publicado é imutável — o hash do spec é a identidade dele, então guias iguais reusam a imagem.

## Banco de dados

Flyway `V1`…`V33` em [`db/migration`](src/main/resources/db/migration), aplicadas no boot;
`ddl-auto: validate` (o Hibernate nunca altera schema).

| Área | Tabelas |
|---|---|
| Conversa / usuário | `conversa`, `troca_conversa`, `usuario` (nome, memória, `uid_hsr`) |
| Moderação | `aviso` |
| HSR | `personagem_hsr`, `reliquias`, `ornamentos_planos`, `cones_de_luz`, `builds`, `materiais`, `hsr_build_meta` |
| Conhecimento | `vector_store`, `kb_meta`, `resposta_cache`, `resposta_paginada` |
| Formulários | `guia`, `guia_arte`, `tier_list` |

Colunas descritas em [tabelas_info.txt](tabelas_info.txt). Duas coisas que economizam tempo:
`character_id` é o **id do jogo** (o mesmo de nanoka/mihomo/Enka/StarRailRes), e **nome não
identifica personagem** — 14 linhas compartilham nome (as duas 7 de Março, os caminhos do
Desbravador), o que é único é `(nome, caminho)`. Por isso o autocomplete manda id, não nome.

Tabelas das migrations iniciais que **não existem mais**: `conversation_message` (V5),
`perfil_usuario`/`fato_usuario`/`users_info` (V8), `mensagem_mencao`/`moderation_warning` (V9),
`hsr_character` (V18).

## Configuração

Precedência: **`.bot.runtime`** (relido a cada 5s) → **`.env`** (semente do boot) →
[`application.yml`](src/main/resources/application.yml) (defaults, com o porquê de cada valor
documentado — ~40 knobs a mais que os abaixo, todos sobrescrevíveis por env var).

| Variável | Default | O que é |
|---|---|---|
| `BOT_TOKEN` | — | obrigatório |
| `BRAIN_MODEL_NAME` | — | passadas curtas: portão, condensador, juiz, retelling |
| `VOICE_MODEL_NAME` | — | toda resposta visível — é esse que se troca pra melhorar a prosa |
| `VISION_MODEL_NAME` | vazio | vazio = imagens ignoradas, sem erro |
| `DB_USER` / `DB_PASSWORD` | `cyrene` | Postgres |
| `OLLAMA_BASE_URL` | `localhost:11434` | |
| `OLLAMA_NUM_CTX` | `16384` | precisa caber persona + cadeia de reply + texto de páginas web |
| `OLLAMA_KNOWLEDGE_NUM_PREDICT` | `2048` | orçamento do retelling (512 esmaga um kit completo) |
| `TEST_CHANNEL_IDS` | vazio | allow-list de canais, só features de IA |
| `SEARXNG_URL` | `localhost:8888` | vazio = busca web desligada |
| `HSR_REINDEX` | `false` | `true` reconstrói o vector store no próximo boot |
| `BOT_PERSONALITY_FILE` | (comentado) | sobrescreve a persona embutida — deixe comentado pro default |

**`.bot.runtime`** é um `chave=valor` (`voice=` `brain=` `vision=` `ai=`) escrito por
`./bot.sh --model` e pelo `/ia` no Discord, lido a cada 5s por
[`RuntimeConfig`](src/main/kotlin/com/cyrene/config/RuntimeConfig.kt). **Ganha do `.env`** — apague
pra voltar. Funciona com o bot parado; o próximo boot lê. Mesmo mecanismo do `.bot.activity`
(atividade do Discord sem restart). É arquivo e não banco/actuator porque isso é config da máquina,
e a app roda com `web-application-type: none`.

Persona: [`prompts/cyrene-personality.md`](src/main/resources/prompts/cyrene-personality.md). O
`{nome}` fica na **última** linha de propósito — tudo acima é idêntico entre usuários, logo
cacheável como prefixo KV.

## Operação

| Comando | O que faz |
|---|---|
| `./bot.sh start` | sobe infra + bot em background (PID em `.bot.pid`, log em `logs/bot.log`) |
| `./bot.sh restart` | recompila e reinicia só o bot — a infra fica de pé |
| `./bot.sh stop [--all]` | para o bot; `--all` derruba SearXNG/colima também |
| `./bot.sh status` · `logs` | o que está no ar · `tail -f` |
| `./bot.sh reindex` | reconstrói a base HSR até o fim (minutos) |
| `./bot.sh --model` | menu interativo pra trocar voz/cérebro/visão sem restart |
| `./bot.sh activity <tipo> "texto"` | muda a atividade sem restart |

Com o bot num host 24/7 ([SETUP.md §9](SETUP.md)), mais quatro — as duas primeiras rodam **no
Mac**, as duas últimas **no servidor**, por cron:

| Comando | Onde | O que faz |
|---|---|---|
| `./bot.sh deploy` | Mac | push do commit atual, e do lado de lá: backup → pull → restart → status. Recusa se houver coisa não commitada |
| `./bot.sh pull-db` | Mac | **DROPA** o banco local e põe a cópia da produção no lugar. Só neste sentido |
| `./bot.sh backup` | servidor | `pg_dump` diário (cron `17 4 * * *`), mantém os 14 últimos e rotaciona o log junto |
| `./bot.sh heartbeat` | servidor | cron de 1 min: Ollama fora do ar → desliga a IA sozinho; voltou → religa (só o que ele mesmo desligou) |

> `CYRENE_ROLE=ollama` no `.env` do Mac faz o `./bot.sh start` daqui recusar. Sem isso, duas
> instâncias no mesmo token respondem à mesma mensagem e nenhuma das duas avisa.

Conteúdo de mensagem não passa de **30 dias** no banco: a [`Retencao`](src/main/kotlin/com/cyrene/conversation/Retencao.kt)
poda `troca_conversa`, `conversa`, `resposta_cache` e `resposta_paginada` uma vez por dia, dentro do
próprio bot (sem cron). Perfil e notas do `/rank` ficam — é o que o formulário de intents do Discord
responde, e mudar um dos dois sem mudar o outro é o jeito de a resposta virar mentira.

Sem métricas HTTP (não há servidor web): o [`AiMetrics`](src/main/kotlin/com/cyrene/ai/AiMetrics.kt)
cronometra cada passada e despeja um resumo no log de tempos em tempos. `com.cyrene: DEBUG` mostra
as decisões do portão e do grounder.

## Testes

```bash
mvn test                                                # ~450 testes, sem banco e sem Ollama
RUN_DB_TESTS=true mvn test -Dtest=AnswerPathsLiveTest   # precisa de Postgres populado
RUN_EVAL=true     mvn test -Dtest=LlmEvalTest           # eval de roteamento contra o Ollama
```

Fixtures de JSON real (srs, nanoka, Enka) em `src/test/resources` — mudança de formato no upstream
aparece como teste vermelho, não como bug em produção.

## Estrutura

```
src/main/kotlin/com/cyrene/
├── ai/            pipeline de LLM: orquestração, portão, visão, métricas, gate de concorrência
├── card/          renderers Java2D + os modelos que eles desenham
├── config/        BotProperties (todos os knobs), RuntimeConfig (troca a quente)
├── conversation/  sessões, histórico, usuário e memória
├── discord/       JDA: bootstrap, 24 comandos, listeners, paginador/status/typing
├── guia/          formulário do /guia
├── hsr/           dados do jogo: harvesters, Enka/mihomo, gazetteer, scorer, painel de status
├── knowledge/     grounding, respostas determinísticas, ingestão, busca web, cache
├── moderation/    entidade de aviso
└── tier/          formulário do /tierlist

bot.sh · docker/searxng/ · SETUP.md · src/main/resources/{application.yml,db/migration,prompts}
skills/            legado da era de tool-calling — nenhum código lê essa pasta hoje
```

## Problemas comuns

- **`extension "vector" is not available`** — pgvector não instalado, ou instalado noutra versão do
  Postgres. Precisa de superuser uma vez: `CREATE EXTENSION vector;`.
- **Postgres "sumiu" depois de um `brew upgrade`** — formula versionada é keg-only; reinicie o
  serviço e desconfie de caminhos com `@14`/`@18` hardcoded.
- **"Não sei" sobre personagem que existe** — base não indexada ou patch mais novo que o índice:
  `./bot.sh reindex`.
- **Menção perde contexto** — `OLLAMA_NUM_CTX` baixo demais (o default 4096 do Ollama truncava o
  histórico e sobrava só a persona).
- **Primeira resposta após silêncio demora 30s** — Ollama descarregou os pesos; suba
  `OLLAMA_KEEP_ALIVE` (`-1m` = nunca).
- **Vitrine do `/build` vazia** — showcase precisa estar público no jogo. Em rede corporativa a Enka
  pode cair em bloqueio de categoria "Games"; `HSR_SHOWCASE_SOURCE=mihomo` contorna.
- **Trocou o modelo e nada mudou** — `.bot.runtime` ganha do `.env`.
