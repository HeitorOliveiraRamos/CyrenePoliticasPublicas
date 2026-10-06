# Política de Privacidade — CyreneBot

**Proposta de atualização: 6 de outubro de 2026 — ainda não publicada nem em vigor.** *(English version below / versão em inglês no fim.)*

CyreneBot é um bot do Discord sobre o jogo Honkai: Star Rail. Esta página explica, sem rodeio, o
que ele guarda sobre você, por quanto tempo, com quem isso é compartilhado, como pedir a exclusão
e quais registros permanecem.

**Responsável pelos dados:** Heitor Oliveira Ramos — contato em [Contato](#contato).
As regras de uso do bot estão nos [Termos de Serviço](TERMOS.md).

---

## 1. O que eu guardo, e por quê

| O quê | Quando entra | Pra quê |
|---|---|---|
| Seu ID de usuário do Discord e seu nome de exibição atual | Na primeira vez que você fala comigo ou usa um comando | É o que liga uma coisa à outra: sua nota, sua memória, seu UID. O nome existe pra eu te chamar pelo nome |
| O texto que você escreve no `/memoria` | Só quando você abre o `/memoria` e escreve | Lembrar de você entre uma conversa e outra. É livre: você decide o que vai ali |
| Seu UID do jogo (`/uid`) | Só quando você roda o `/uid` | Buscar sua vitrine pública no jogo pros comandos `/build`, `/perfil` e `/rank` |
| Conteúdo das mensagens que você endereça a mim | Menção, resposta a uma mensagem minha, ou uma sessão aberta com `/iniciar-conversa` | Entender a pergunta e responder. Sessões do `/iniciar-conversa` guardam o par pergunta/resposta pra conversa ter fio |
| Perguntas e respostas sobre o jogo | Quando você me pergunta algo sobre HSR | Cache: a mesma pergunta não precisa ser processada duas vezes |
| Nota das suas builds, apelido no jogo, nível e eidolon | Quando você roda o `/build` | Montar o card e o ranking do `/rank` |
| Guias (`/guia`) e tier lists (`/tierlist`) que você escreve, com as imagens que você mesma envia | Enquanto você usa esses comandos | São o conteúdo que você está criando; ficam ligados a você pra você poder editar e continuar de onde parou |
| Votos em respostas (👍/👎), IDs da pergunta e resposta, canal, servidor, votante, rota e data | Quando uma resposta é registrada e alguém vota | Avaliar a qualidade das respostas, sem copiar o texto para esses registros |
| Advertências de moderação (`/avisar`) | Quando um moderador do servidor te adverte | Histórico de moderação daquele servidor. Guarda também quem aplicou a advertência |

Dados do jogo (personagens, relíquias, banners, materiais) não são seus dados pessoais e não estão
nesta lista.

### Leitura de contexto do Discord

O bot solicita acesso ao conteúdo de mensagens (`MESSAGE_CONTENT`) e a informações de membros
(`GUILD_MEMBERS`), mas não à presença (`GUILD_PRESENCES`). Ter acesso técnico não significa guardar
todo o histórico: há leituras pontuais de mensagens que não foram dirigidas ao bot.

O `/contexto-do-canal` consulta as últimas 50 mensagens por padrão (configurável entre 1 e 100).
O recorte enviado ao modelo configurado inclui nome e ID do autor, horário, link e texto visível,
com até 2.000 caracteres de texto por mensagem e orçamento de 20.000 caracteres para o recorte.
No servidor, quem pede precisa poder ver o canal e ler seu histórico. As regras de `/permissoes`
também se aplicam; sem bloqueio configurado, permitem o uso. O resumo é efêmero, visível só a quem
pediu, e essa função não grava o histórico consultado no banco do bot.

Uma resposta pode também consultar até quatro mensagens recentes do canal para entender a conversa
ao redor, sob demanda e com verificação das permissões de leitura do solicitante. Esse contexto pode
conter falas de terceiros e ser enviado ao modelo. Informações de membros citados, como nome,
cargos e datas de entrada/criação, e informações dos canais visíveis e do servidor também podem
compor o contexto. Para o `/rank` do servidor, o bot verifica se os jogadores estão no servidor
e consulta os apelidos dos
IDs que já têm notas de build; esse fluxo não enumera todos os membros.

**A permissão de leitura de quem pede um resumo não é consentimento individual dos autores.**
Não mencionar o bot não impede essas leituras de contexto. Durante uma sessão aberta com
`/iniciar-conversa`, suas mensagens naquele canal são processadas mesmo sem menção; os pares de
pergunta e resposta ficam guardados. Use `/encerrar-conversa` para encerrar a sessão.

### Memórias aprendidas (recurso opcional)

Quando o responsável habilita a conversa com **Sign in with ChatGPT**, o bot pode aprender
preferências ou fatos pessoais úteis fornecidos por você nas próprias conversas com a Cyrene.
Quando o recurso está habilitado, a aprendizagem começa ligada por padrão; não exige um
`/memoria acao:ligar` inicial. Há até 20 entradas recuperáveis por escopo, com até 400 caracteres
cada. São registros separados da memória manual: conteúdo, chave do fato, seu ID Discord, escopo,
IDs da mensagem/canal de origem e datas. Não substituem o texto que você escreveu no `/memoria`.
Memórias de servidor só são usadas para você naquele servidor; DMs ficam separadas. A memória
manual continua global, conforme sua escolha explícita. Não envie segredos; filtros não são
uma garantia de detecção de toda informação sensível.

Use `/memoria acao:ver`, `corrigir` (com `chave` e `texto`), `apagar` (com `chave`), `limpar`,
`desligar` ou `ligar`. A inspeção é privada. Limpar atua no escopo atual; desligar interrompe
novas gravações automáticas em todos os escopos, inclusive propostas em andamento, sem apagar as
entradas existentes. Elas continuam no contexto e nos controles de consulta, correção e exclusão;
para retirar uma memória do contexto, apague a entrada ou limpe o escopo.
Alterações invalidam propostas pendentes. `/apagar-meus-dados` remove as memórias, recibos e
preferências de aprendizagem, conforme as exceções gerais da seção 5.
Entradas expiram da recuperação após 30 dias sem atualização; o código prevê remoção na próxima
poda diária, sujeita a falha ou indisponibilidade. O script de dump exclui o conteúdo das entradas e dos recibos; a preferência de aprendizado
segue o perfil no dump. Isso descreve o script, não comprova quais backups existem em produção
ou seus prazos de rotação; veja a seção 6.

Para impedir que uma mensagem antiga recrie fatos apagados, guardamos também recibos sem texto:
seu ID, escopo, ID do evento Discord, identificadores internos de tentativa/autorização e data.
O código prevê poda diária após 30 dias, exclusão dos dumps feitos pelo script e remoção com seu perfil.
Eventos anteriores ao início do processo, à criação do perfil ou com mais de 24 horas não geram
novas memórias, mesmo após apagar recibos. Retomar um evento parcial não autoriza novos efeitos.
Uma mensagem nova pode propor fatos novos. A aprendizagem pode ocorrer em conversa ou pergunta
sobre o jogo, mas usa somente sua mensagem original; não aprende de imagens, ferramentas ou terceiros.
Interpretação ambígua pode não ser gravada; filtros e IA não são garantias semânticas infalíveis.

## 2. Limites da coleta

- **Imagens usadas pela visão:** os bytes são processados em memória, sem gravação por essa
  função no banco do bot. A imagem pode vir de um anexo seu ou da mensagem à qual você responde
  e pode ser enviada ao provedor selecionado. A descrição extraída pode compor o contexto e uma
  troca guardada. Arte enviada para ilustrar uma guia no `/guia` é guardada como material da guia.
- **Seu status, seu jogo aberto, sua presença.** O bot nem recebe essa informação do Discord.
- **Senhas, e-mail, telefone, dados de pagamento, sua conta HoYoverse.** Nada disso é pedido para usar o bot. Se você escrever esses dados numa mensagem ou memória,
  eles podem ser processados e guardados conforme a função; não os envie. Seu UID do jogo é um número público de perfil, não é login.
- **Nada sobre você em DMs com outras pessoas.** Eu só enxergo minhas próprias DMs.

Os registros técnicos do servidor não gravam perguntas, fontes ou respostas completas. Para diagnosticar
consultas de equipes, podem registrar IDs de personagens e estados dos campos consultados; o verificador
pode registrar até três problemas validados de até 240 caracteres por revisão, com identificadores conhecidos
de conta e UID substituídos. Esses diagnósticos não incluem os identificadores conhecidos de dono ou UID.

## 3. Por quanto tempo

- **Conversas guardadas, caches e memórias aprendidas:** o código agenda uma poda diária dos
  registros com mais de 30 dias. Trocas usam a data de criação; memórias aprendidas, a última
  atualização; sessões encerradas, a data de encerramento (ou de início, se ela faltar). A linha
  de uma sessão ativa não entra nessa poda. Registros que passam do limite entre execuções
  aguardam a próxima; falha ou indisponibilidade pode atrasar a exclusão. Não é um máximo exato
  de 30 dias. A recuperação das memórias aprendidas deixa de usar entradas sem atualização há
  mais de 30 dias.
- **Perfil, UID, memória manual, notas e histórico de builds, preferências de aprendizagem e
  registros de votos/IDs de respostas:** ficam fora da poda diária de 30 dias. O comando de
  exclusão remove os dados vinculados ao seu perfil e seus votos, conforme a seção 5; os IDs de
  perguntas/respostas, canal, servidor e rota sem vínculo direto com o perfil permanecem.
- **Advertências de moderação:** não expiram. Elas são o registro do servidor sobre o que aconteceu
  lá, incluem dados pessoais e não são apagadas por esse comando. Pedidos sobre esses registros
  devem ser encaminhados pelo Contato.
- **Guias e tier lists publicadas:** o conteúdo fica no ar pra comunidade, mas perde o vínculo com
  você quando você apaga seus dados. Rascunhos não publicados somem junto com o resto.

## 4. Com quem isso é compartilhado

**Com ninguém, para fins comerciais. Nada é vendido, alugado ou usado pra publicidade.**

O que sai da minha infraestrutura, e só o necessário:

- **Discord** — porque é onde o bot vive.
- **Enka.Network e Mihomo** (serviços públicos de vitrine de HSR) — recebem **o UID do jogo** quando
  você roda `/build` ou `/perfil`. Nada do seu Discord vai junto.
- **Buscadores** — quando eu não sei responder algo sobre o jogo com o que tenho, uma busca sobre
  **o assunto da pergunta** é enviada, através de uma instância própria de metabusca, a mecanismos
  de busca públicos. Vai o assunto pesquisado, não sua identidade.
- **Serviços de dados do jogo** (calendário de eventos, imagens de personagens) — recebem só o
  pedido do dado; nenhum dado seu.

**A IA pode usar processamento local ou provedores externos, conforme a configuração da instância.**
O responsável informa que alterna provedores durante testes e operação e avalia frequentemente
o bot com ChatGPT/Luna. A Cyrene pode enviar mensagens e contexto necessário a diferentes
provedores para gerar respostas e avaliar o funcionamento do bot em testes de modelos. Testar
essas respostas é diferente de o provedor usar o conteúdo para treinar ou melhorar seus modelos.

O código suporta Ollama, llama.cpp e um adaptador de classificação para processamento local;
a localização efetiva depende da implantação, ainda não confirmada nesta proposta. Quando o
roteador **Jev/TypeSafe via Vercel AI Gateway** está configurado para o serviço externo, ele recebe
a mensagem atual e até seis turnos anteriores para identificar a intenção. Nomes, menções e outros
dados pessoais escritos nesses textos podem fazer parte desse envio. O roteador não recebe o
banco inteiro nem uma cópia separada do perfil ou da memória.

Se um provedor externo de geração, como **OpenCode Zen**, for habilitado pelo responsável, ele
poderá receber a pergunta, o histórico e o contexto necessário à resposta, inclusive os recortes
de terceiros da seção 1 e memórias do autor; o slot de visão também poderá enviar a imagem endereçada ao bot. Não envie
segredos ou informações sensíveis ao bot. Consulte o responsável para saber a configuração ativa.

Com **Sign in with ChatGPT**, a OpenAI pode receber as instruções da Cyrene, o contexto da
conversa (inclusive os recortes de terceiros descritos na seção 1), memórias manuais e aprendidas
do autor, resultados de ferramentas necessários e imagens anexadas
quando a visão está habilitada. A conta ChatGPT
do operador serve para autorizar a assinatura, não para importar conversas, memórias pessoais,
perfil ou sessões de outros agentes. `store: false` não garante ausência de retenção ou logs no
provedor. A ativação depende de decisão do responsável e aviso à comunidade, não do simples deploy.

No fluxo de mensagens examinado, não foi encontrada coleta automática para treinamento. Existem
ferramentas de treino local que exigem dados fornecidos separadamente e confirmação explícita de
quem as executa; isso não comprova quais dados foram usados fora desse fluxo. Não há aqui garantia
sobre treinamento ou retenção de terceiros. O processamento, os prazos de retenção e a
localização dos dados nos serviços externos dependem das condições desses provedores; a regra
local de 30 dias e o comando de exclusão não são uma garantia sobre cópias de terceiros. Para
informações ou pedidos relativos a esse processamento, use o [Contato](#contato).

### OpenAI: melhoria de modelos e retenção

Segundo a documentação oficial, conteúdo elegível de ChatGPT/Codex pessoal pode ser usado para
melhoria conforme os controles da conta. Os controles de treinamento do ChatGPT aplicam-se ao
Codex. Isso não comprova treinamento com mensagens específicas da Cyrene.
([Codex com plano ChatGPT](https://help.openai.com/en/articles/11369540-using-codex-with-your-chatgpt-plan))

O responsável informou que a conta usada é **ChatGPT Plus pessoal**, que **Improve the model for
everyone** estava ligado e que o desativou, com confirmação em **6 de outubro de 2026, às 19:09 UTC**.
Segundo as regras oficiais, após o opt-out novas conversas ChatGPT e tarefas Codex não são usadas
para treinamento, ressalvado o feedback voluntário descrito abaixo. Essa confirmação não demonstra
se conteúdo anterior foi usado, não apaga o histórico nem desfaz eventual uso anterior.
**Include environments** controla contexto adicional separadamente e ainda precisa ser confirmado.
O opt-out desta conta não se estende automaticamente a outros provedores ou contas usados no futuro;
cada configuração exige avaliação própria.
([Controles de dados](https://help.openai.com/en/articles/7730893-data-controls-in-chatgpt))

Business, Enterprise, Edu e API não usam entradas/saídas para melhorar modelos por padrão, com
regras próprias. Feedback voluntário enviado à OpenAI pode permitir uso da conversa associada
mesmo após opt-out; isso não equivale ao voto guardado apenas no banco da Cyrene.
([Uso de dados para melhoria](https://help.openai.com/en/articles/5722486-how-your-data-is-used-to-improve-model-performance))

**Sem treinamento não significa retenção zero.** A retenção depende do serviço, configuração e
políticas aplicáveis. Não atribuímos ao acesso OAuth do bot prazos da API nem regras de Temporary
Chat sem confirmar sua aplicabilidade.
([Retenção no ChatGPT](https://help.openai.com/en/articles/8983778-chat-and-file-retention-in-chatgpt),
[Dados na API](https://developers.openai.com/api/docs/guides/your-data))

**Pendência para publicação:** revisar as condições efetivamente aplicáveis ao acesso OAuth do bot
e os controles adicionais, inclusive **Include environments**, quando aplicável. Informar uma
possibilidade de treinamento não concede permissão para usar
conteúdo do Discord nem declara conformidade com as políticas do Discord ou do provedor.

## 5. Seus direitos

Pela LGPD (Lei 13.709/2018) você pode pedir acesso, correção, exclusão ou informação sobre o uso dos
seus dados. Na prática, sem pedir nada a ninguém:

| Você quer | Comando |
|---|---|
| Ver ou trocar o que eu lembro de você | `/memoria` |
| Trocar ou tirar seu UID do jogo | `/uid` |
| Excluir dados vinculados ao seu perfil, com as exceções abaixo | `/apagar-meus-dados` e confirmação no formulário |

`/apagar-meus-dados` abre um formulário com a prévia e uma caixa de confirmação. Sem marcar a
caixa, nada é apagado. Confirmando, a exclusão não tem desfazer: remove perfil, nome guardado, UID,
memória manual e aprendida, preferências e recibos de aprendizagem, sessões e trocas vinculadas,
notas e histórico de builds, seus votos e rascunhos. Guias e tier lists publicadas são anonimizadas;
advertências de moderação permanecem. Caches e IDs de respostas sem vínculo direto com o perfil
não são apagados por esse comando; o conteúdo dos caches segue a poda da seção 3. O comando não
apaga mensagens do Discord nem cópias mantidas por terceiros ou backups já existentes.

Pra qualquer outro pedido — inclusive uma cópia dos seus dados — use o [Contato](#contato). Respondo
em até 7 dias.

Para parar uma sessão, use `/encerrar-conversa`; para impedir novas memórias aprendidas, use
`/memoria acao:desligar` e apague ou limpe as entradas que não quer no contexto. Parar de mencionar
o bot não impede a leitura de seus textos em recortes solicitados por outras pessoas. A administração
pode desligar a IA no servidor com `/ia` e bloquear canais com `/permissoes`. Para outros pedidos
ou oposição ao uso de seus dados, use o Contato.

## 6. Segurança e onde os dados ficam

**Segundo confirmação do responsável em 6 de outubro de 2026, a criptografia em repouso está
ativa.** Esta é uma declaração do responsável, não uma auditoria independente; ela não especifica
algoritmo nem comprova a cobertura de cada cópia de segurança.

**Pendência desta proposta antes de entrar em vigor:** confirmar a localização, os acessos,
a cobertura de cifragem dos backups e os backups efetivamente usados em produção. A declaração
da versão anterior é preservada abaixo para revisão; a confirmação acima não comprova seus
demais detalhes:

> Os dados ficam num banco de dados num servidor privado na Alemanha, sem acesso público: só eu
> acesso, por conexão administrativa autenticada. **O volume onde o banco e as cópias de segurança
> ficam é cifrado em repouso.** Cópias de segurança são feitas diariamente e rodadas fora depois de
> 14 dias.

O `bot.sh` exclui seis tabelas de conteúdo dos dumps que produz: sessões, trocas, cache, respostas
paginadas, memórias aprendidas e recibos. Isso não comprova execução, existência de outras cópias,
cifragem nem rotação. Os provedores de IA ativos e suas condições também precisam ser confirmados.

Nenhum sistema é perfeito, e eu não posso prometer segurança absoluta. As funções de contexto
podem processar dados de pessoas que não chamaram o bot, conforme a seção 1.

## 7. Menores de idade

O Discord exige 13 anos, ou mais, conforme o país. Este bot segue a mesma regra e não é dirigido a
crianças. Se souber de dados de uma criança guardados aqui, me avise pelo [Contato](#contato) e eu
apago.

## 8. Mudanças nesta política

Mudanças ficam registradas no histórico deste repositório, com data. Se alguma delas mudar de
verdade o que é guardado, por quanto tempo ou com quais provedores é compartilhado, aviso no servidor de suporte antes de valer.

## Contato

- Servidor de suporte: https://discord.gg/pompom
- E-mail: pompomnius@gmail.com

---
---

# Privacy Policy — CyreneBot

**Proposed update: October 6, 2026 — not yet published or effective.** *(Portuguese is the version written for the bot's users; this
English translation says the same things.)*

CyreneBot is a Discord bot about the game Honkai: Star Rail. This page explains what it stores about
you, for how long, who it is shared with, and how to request deletion and which records remain.

**Data controller:** Heitor Oliveira Ramos — see [Contact](#contact-1).
The rules for using the bot are in the [Terms of Service](TERMOS.md).

## 1. What is stored, and why

| What | When it is collected | What for |
|---|---|---|
| Your Discord user ID and current display name | The first time you talk to the bot or use a command | It is what ties everything together: your score, your memory, your UID. The name is so the bot can address you by name |
| The text you write in `/memoria` | Only when you open `/memoria` and write something | Remembering you between conversations. It is free-form: you decide what goes there |
| Your in-game UID (`/uid`) | Only when you run `/uid` | Fetching your public in-game showcase for `/build`, `/perfil` and `/rank` |
| The content of messages you address to the bot | An @mention, a reply to one of the bot's messages, or a session you opened with `/iniciar-conversa` | Understanding and answering the question. `/iniciar-conversa` sessions store the question/answer pair so the conversation keeps its thread |
| Game questions and their answers | When you ask the bot about HSR | A cache, so the same question is not processed twice |
| Your build scores, in-game nickname, level and eidolon | When you run `/build` | Drawing the card and the `/rank` leaderboard |
| Guides (`/guia`) and tier lists (`/tierlist`) you write, including art you upload yourself | While you use those commands | It is the content you are creating; it stays linked to you so you can edit it and resume it |
| Answer votes (👍/👎), question and answer IDs, channel, server, voter, route and timestamp | When an answer is registered and someone votes | Assess answer quality, without copying message text into these records |
| Moderation warnings (`/avisar`) | When a server moderator warns you | That server's moderation history. It also records which moderator issued it |

Game data (characters, relics, banners, materials) is not personal data and is not in this list.

### Reading Discord context

The bot requests message content access (`MESSAGE_CONTENT`) and member information
(`GUILD_MEMBERS`), but not presence (`GUILD_PRESENCES`). Technical access does not mean the entire
history is stored: messages not addressed to the bot can be read for specific context requests.

`/contexto-do-canal` fetches the latest 50 messages by default (configurable from 1 to 100).
The excerpt sent to the configured model includes author name and ID, timestamp, link and visible
text, with up to 2,000 text characters per message and a 20,000-character excerpt budget.
In a server, the requester must be able to view the channel and read its history. `/permissoes`
rules also apply; use is allowed unless a block is configured. The summary is ephemeral, visible
only to the requester, and this feature does not save the fetched history in the bot's database.

A response may also fetch up to four recent channel messages to understand the surrounding
conversation, on demand and with the requester's reading permissions checked. This context can
include third-party speech and be sent to the model. Information about mentioned members, such as
names, roles and join/account-creation dates, and information about visible channels and the server
can also form context. Server `/rank` checks server membership and nicknames for IDs that already
have build scores; this flow does not enumerate all members.

**The requester's reading permission is not individual consent from the authors.** Not mentioning
the bot does not prevent these context reads. During a session opened with `/iniciar-conversa`,
your messages in that channel are processed even without mentions; question/answer pairs are stored.
Use `/encerrar-conversa` to end the session.

### Learned memories (optional feature)

When the operator enables conversations through **Sign in with ChatGPT**, the bot may learn useful
preferences or personal facts you provide in your own Cyrene conversations. These are separate
records: content, fact key, Discord owner ID, scope, source message/channel IDs and timestamps.
When the feature is enabled, learning defaults to on; no initial `/memoria acao:ligar` is required.
There are up to 20 retrievable entries per scope, with up to 400 characters each.
They never overwrite your manually authored `/memoria` text. Server memories are used only for
you in that server; DM memories stay separate. Manual memory remains global by your explicit choice.
Do not send secrets; filters cannot guarantee detection of every sensitive detail.

Use `/memoria acao:ver`, `corrigir` (with `chave` and `texto`), `apagar` (with `chave`), `limpar`,
`desligar` or `ligar`. Inspection is private. Clearing affects the current scope; disabling stops
new automatic writes across all scopes, including pending proposals, without deleting existing entries.
Existing memories remain in context and available for viewing, correction and deletion; delete an entry
or clear its scope to remove it from context. Changes
invalidate pending proposals. `/apagar-meus-dados` removes all entries and learning preferences.
Entries stop being retrieved after 30 days without updates; the code schedules removal at the
next daily pruning run, subject to failure or downtime. The dump script excludes entry and receipt content; the learning preference follows the profile
in the dump. This describes the script, not which backups exist in production or their rotation
periods; see section 6.

To prevent old messages from recreating deleted facts, we also retain receipts without message text:
your ID, scope, Discord event ID, internal attempt/authorization identifiers and timestamp. Receipts
are scheduled for daily pruning after 30 days, excluded from dumps made by the script and removed with your profile. Events predating
process startup or profile creation, or older than 24 hours, cannot create memories even after receipts
are deleted. Resuming a partial event does not authorize new effects; a new message may propose new
facts. Learning can occur in chat or game questions but uses only your original message, not images,
tools or third-party speech. Ambiguous content may not be saved; filters and AI are not infallible
semantic guarantees.

## 2. Collection limits

- **Images used by vision:** bytes are processed in memory without being written to the bot's
  database by this feature. The image may come from your attachment or the message you reply to
  and may be sent to the selected provider. Its extracted description can form context and a stored
  exchange. Art uploaded to illustrate a `/guia` guide is stored as guide material.
- **Your status, your current game, your presence.** The bot does not even receive that from
  Discord.
- **Passwords, email, phone number, payment details, your HoYoverse account.** None of it is requested to use the bot. If you write those details in a message or memory,
  they may be processed and stored depending on the feature; do not send them. Your in-game UID is a public profile number, not a login.
- **Anything about you in other people's DMs.** The bot only sees its own DMs.

Server logs do not record full questions, sources, or answers. Team-query diagnostics may include
character IDs and queried-field states. The verifier may log up to three validated issues of up to
240 characters per review, with known account identifiers and game UIDs replaced. These diagnostics
do not include known owner identifiers or game UIDs.

## 3. How long it is kept

- **Stored conversations, caches and learned memories:** the code schedules daily pruning of
  records older than 30 days. Exchanges use creation time; learned memories, their last update;
  closed sessions, their closing time (or start time if absent). An active session row is excluded
  from this pruning. Records crossing the limit between runs await the next run; failure or downtime
  can delay deletion. This is not an exact 30-day maximum. Learned-memory retrieval stops using
  entries that have not been updated for more than 30 days.
- **Profile, UID, manual memory, build scores and history, learning preferences and vote/answer-ID
  records:** excluded from daily 30-day pruning. The deletion command removes profile-linked data
  and your votes as described in section 5; question/answer IDs, channel, server and route without
  a direct profile link remain.
- **Moderation warnings:** they do not expire. They are the server's record of what happened there
  and include personal data. This command does not delete them; use Contact for requests about
  these records.
- **Published guides and tier lists:** the content stays up for the community, but loses its link to
  you when you erase your data. Unpublished drafts are deleted along with everything else.

## 4. Who it is shared with

**No one, for commercial purposes. Nothing is sold, rented or used for advertising.**

What leaves the bot's infrastructure, and only as needed:

- **Discord** — because that is where the bot lives.
- **Enka.Network and Mihomo** (public HSR showcase services) — they receive **the in-game UID** when
  you run `/build` or `/perfil`. Nothing about your Discord account goes with it.
- **Search engines** — when the bot cannot answer a game question from what it already has, a search
  about **the subject of the question** is sent, through a self-hosted metasearch instance, to
  public search engines. The subject is sent, not your identity.
- **Game data services** (event calendar, character images) — they receive only the request for the
  data; none of yours.

**AI processing may be local or use external providers, depending on the instance configuration.**
The operator reports switching providers during testing and operation and frequently evaluating
the bot with ChatGPT/Luna. Cyrene may send messages and necessary context to different providers
to generate answers and evaluate the bot's operation during model tests. Testing these answers
differs from a provider using the content to train or improve its models.

The code supports Ollama, llama.cpp and a classification adapter for local processing; the actual
location depends on deployment, which has not been confirmed in this proposal. When the
**Jev/TypeSafe router through Vercel AI Gateway** is configured to use the external service, it
receives the current message and up to six previous turns to identify intent. Names, mentions and
other personal data written in those texts may be included. The router does not receive the
entire database or a separate copy of your profile or memory.

If an external generation provider, such as **OpenCode Zen**, is enabled by the operator, it may
receive the question, history and context needed to answer, including third-party excerpts from
section 1 and the author's memories; the
vision slot may also send an image addressed to the bot. Do not send secrets or sensitive
information to the bot. Contact the operator to learn which configuration is active.

With **Sign in with ChatGPT**, OpenAI may receive Cyrene instructions, conversation context
(including third-party excerpts described in section 1), the author's manual and learned memories,
necessary tool results and attached images when vision is enabled.
The operator's ChatGPT account
only authorizes subscription use; personal conversations, saved memories, account profile and
other agents' sessions are not imported. `store: false` does not guarantee absence of provider
retention or logs. Activation requires an operator decision and community notice, not merely deployment.

No automatic collection for training was found in the examined message flow. Local training tools
require separately supplied data and explicit confirmation from their operator; this does not establish
which data has been used outside that flow. This policy makes no guarantee about third-party training
or retention. Processing, retention periods and
data locations at external services depend on those providers' terms; the local 30-day rule and
deletion command are not a guarantee about third-party copies. For information or requests
concerning this processing, use [Contact](#contact-1).

### OpenAI: model improvement and retention

According to official documentation, eligible personal ChatGPT/Codex content may be used for
improvement depending on account controls. ChatGPT training controls apply to Codex. This does
not establish training on specific Cyrene messages.
([Codex with a ChatGPT plan](https://help.openai.com/en/articles/11369540-using-codex-with-your-chatgpt-plan))

The operator reported using a **personal ChatGPT Plus account**, that **Improve the model for
everyone** had been on, and that it was turned off, confirmed on **October 6, 2026, at 19:09 UTC**.
Under the official rules, after opting out, new ChatGPT conversations and Codex tasks are not used
for training, subject to the voluntary-feedback exception below. This confirmation does not establish
whether earlier content was used, delete history or undo any earlier use.
**Include environments** separately controls additional context and still needs confirmation.
This account's opt-out does not automatically extend to other providers or accounts used in the future;
each configuration requires its own assessment.
([Data controls](https://help.openai.com/en/articles/7730893-data-controls-in-chatgpt))

Business, Enterprise, Edu and API inputs/outputs are not used for model improvement by default,
under their own rules. Voluntary feedback submitted to OpenAI may allow use of its associated
conversation even after opting out; this differs from a vote stored only in Cyrene's database.
([Data use for improvement](https://help.openai.com/en/articles/5722486-how-your-data-is-used-to-improve-model-performance))

**No training does not mean zero retention.** Retention depends on the service, configuration and
applicable policies. API retention periods and Temporary Chat rules are not attributed to the bot's
OAuth access without confirming their applicability.
([ChatGPT retention](https://help.openai.com/en/articles/8983778-chat-and-file-retention-in-chatgpt),
[API data controls](https://developers.openai.com/api/docs/guides/your-data))

**Pending before publication:** review the terms actually applicable to the bot's OAuth access and
additional controls, including **Include environments**, where applicable. Disclosing possible
training does not grant permission to use Discord content
or declare compliance with Discord or provider policies.

## 5. Your rights

Under Brazil's LGPD (Law 13.709/2018) you may request access, correction, deletion, or information
about how your data is used. In practice, without asking anyone:

| What you want | Command |
|---|---|
| See or change what the bot remembers about you | `/memoria` |
| Change or remove your in-game UID | `/uid` |
| Delete profile-linked data, subject to the exceptions below | `/apagar-meus-dados` and confirmation in the form |

`/apagar-meus-dados` opens a form with a preview and a confirmation checkbox. Leaving it unchecked
deletes nothing. Confirming performs irreversible deletion of your profile, stored name, UID, manual
and learned memory, learning preferences and receipts, linked sessions and exchanges, build scores
and history, your votes and drafts. Published guides and tier lists are anonymized; moderation
warnings remain. Caches and answer IDs without a direct profile link are not deleted by this command;
cache content follows section 3 pruning. The command does not delete Discord messages, third-party
copies or existing backups.

For any other request — including a copy of your data — use [Contact](#contact-1). Answered within
7 days.

Use `/encerrar-conversa` to end a session; use `/memoria acao:desligar` to stop new learned-memory
writes, and delete or clear entries you do not want in context. Not mentioning the bot does not
prevent your text from being read in excerpts requested by others. Server administrators can disable
AI with `/ia` and block channels with `/permissoes`. Use Contact for other requests or objections
to processing your data.

## 6. Security and where the data lives

**According to the operator's confirmation on October 6, 2026, encryption at rest is active.**
This is the operator's statement, not an independent audit; it specifies no algorithm and does
not establish encryption coverage for each backup copy.

**Pending before this proposal takes effect:** confirm the location, access, backup encryption
coverage and backups actually used in production. The previous version's statement is preserved
below for review; the confirmation above does not establish its other details:

> Data is stored in a database on a private server in Germany, not publicly reachable: only the
> developer accesses it, over an authenticated administrative connection. **The volume holding the
> database and its backups is encrypted at rest.** Backups run daily and are rotated out after 14
> days.

`bot.sh` excludes six content tables from the dumps it produces: sessions, exchanges, cache, paged
answers, learned memories and receipts. This does not establish execution, other copies, encryption
or rotation. Active AI providers and their applicable conditions also require confirmation.

No system is perfect and absolute security cannot be promised. Context features may process data
about people who did not invoke the bot, as described in section 1.

## 7. Children

Discord requires users to be 13, or older depending on the country. This bot follows the same rule
and is not directed at children. If you know of a child's data stored here, tell the developer
through [Contact](#contact-1) and it will be deleted.

## 8. Changes to this policy

Changes are recorded in this repository's history, with dates. If one of them materially changes
what is stored, for how long or which providers receive it, it will be announced in the support server before taking effect.

## Contact

- Support server: https://discord.gg/pompom
- Email: pompomnius@gmail.com
