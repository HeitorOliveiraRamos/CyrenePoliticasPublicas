# Política de Privacidade — CyreneBot

**Última atualização: 13 de agosto de 2026.** *(English version below / versão em inglês no fim.)*

CyreneBot é um bot do Discord sobre o jogo Honkai: Star Rail. Esta página explica, sem rodeio, o
que ele guarda sobre você, por quanto tempo, com quem isso é compartilhado e como apagar tudo.

**Responsável pelos dados:** `[SEU NOME OU APELIDO]` — contato em [Contato](#contato).

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
| Advertências de moderação (`/avisar`) | Quando um moderador do servidor te adverte | Histórico de moderação daquele servidor. Guarda também quem aplicou a advertência |

Dados do jogo (personagens, relíquias, banners, materiais) não são seus dados pessoais e não estão
nesta lista.

## 2. O que eu NÃO guardo

- **Mensagens que não são pra mim.** Eu só processo uma mensagem se ela me menciona, responde uma
  mensagem minha, ou faz parte de uma sessão que você abriu. Todo o resto do canal é descartado no
  mesmo instante em que chega: não é lido, não é guardado, não vai pro modelo.
- **Imagens que você anexa numa mensagem pra mim.** Elas são lidas na memória pra eu descrever o que
  aparece nelas, e os bytes são jogados fora em seguida. (Exceção: a arte que você **envia de
  propósito** pra ilustrar uma guia no `/guia` — essa é guardada, porque é o material da guia.)
- **Seu status, seu jogo aberto, sua presença.** O bot nem recebe essa informação do Discord.
- **Senhas, e-mail, telefone, dados de pagamento, sua conta HoYoverse.** Nada disso é pedido nem
  aceito. Seu UID do jogo é um número público de perfil, não é login.
- **Nada sobre você em DMs com outras pessoas.** Eu só enxergo minhas próprias DMs.

Os registros técnicos do servidor (logs de erro) não gravam conteúdo de mensagem na configuração
normal de operação.

## 3. Por quanto tempo

- **Conteúdo de mensagem: no máximo 30 dias.** Uma rotina automática apaga diariamente tudo que
  passou disso — conversas guardadas, perguntas e respostas em cache. Não depende de ninguém pedir.
- **Perfil, UID e notas de build:** ficam enquanto você quiser. Somem no instante em que você rodar
  `/apagar-meus-dados`.
- **Advertências de moderação:** não expiram. Elas são o registro do servidor sobre o que aconteceu
  lá, não um dado da sua conta, e por isso não saem num pedido de exclusão.
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

**A inteligência artificial roda na minha própria máquina.** O modelo de linguagem que escreve as
respostas é executado localmente, em hardware meu. Suas mensagens não são enviadas pra OpenAI,
Google, Anthropic ou qualquer outro provedor de IA.

**Suas mensagens nunca são usadas pra treinar modelo nenhum.** Nem meu, nem de terceiros. O modelo
lê a pergunta pra responder e não guarda nada disso.

## 5. Seus direitos

Pela LGPD (Lei 13.709/2018) você pode pedir acesso, correção, exclusão ou informação sobre o uso dos
seus dados. Na prática, sem pedir nada a ninguém:

| Você quer | Comando |
|---|---|
| Ver ou trocar o que eu lembro de você | `/memoria` |
| Trocar ou tirar seu UID do jogo | `/uid` |
| **Apagar tudo, agora** | `/apagar-meus-dados confirmar:Verdadeiro` |

`/apagar-meus-dados` roda na hora e não tem desfazer. Ele apaga seu perfil, seu nome guardado, seu
UID, sua memória, suas conversas, suas notas de build e seus rascunhos. Rodando com
`confirmar:Falso` ele só te mostra o que sairia, sem apagar nada.

Pra qualquer outro pedido — inclusive uma cópia dos seus dados — use o [Contato](#contato). Respondo
em até 7 dias.

Você também pode simplesmente parar: não me mencionar é o suficiente pra eu não ler nem guardar mais
nada seu. E a dona ou dono de um servidor pode desligar toda a parte de IA ali com `/ia`.

## 6. Segurança e onde os dados ficam

Os dados ficam num banco de dados num servidor privado em `[PAÍS DO SERVIDOR]`, sem acesso público:
só eu acesso, por conexão administrativa autenticada. **[CONFIRME ANTES DE PUBLICAR: o volume onde o
banco e as cópias de segurança ficam é cifrado em repouso.]** Cópias de segurança são feitas
diariamente e rodadas fora depois de 14 dias.

Nenhum sistema é perfeito, e eu não posso prometer segurança absoluta. O que eu posso prometer é o
mínimo de dado guardado: o bot não coleta nada que não seja usado por uma função que você mesma
chamou.

## 7. Menores de idade

O Discord exige 13 anos, ou mais, conforme o país. Este bot segue a mesma regra e não é dirigido a
crianças. Se souber de dados de uma criança guardados aqui, me avise pelo [Contato](#contato) e eu
apago.

## 8. Mudanças nesta política

Mudanças ficam registradas no histórico deste repositório, com data. Se alguma delas mudar de
verdade o que é guardado ou por quanto tempo, aviso no servidor de suporte antes de valer.

## Contato

- Servidor de suporte: `[LINK DO CONVITE PERMANENTE]`
- E-mail: `[SEU E-MAIL DE CONTATO]`

---
---

# Privacy Policy — CyreneBot

**Last updated: August 13, 2026.** *(Portuguese is the version written for the bot's users; this
English translation says the same things.)*

CyreneBot is a Discord bot about the game Honkai: Star Rail. This page explains what it stores about
you, for how long, who it is shared with, and how to erase all of it.

**Data controller:** `[YOUR NAME OR HANDLE]` — see [Contact](#contact-1).

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
| Moderation warnings (`/avisar`) | When a server moderator warns you | That server's moderation history. It also records which moderator issued it |

Game data (characters, relics, banners, materials) is not personal data and is not in this list.

## 2. What is NOT stored

- **Messages that are not addressed to the bot.** A message is only processed if it mentions the
  bot, replies to the bot, or belongs to a session you opened. Everything else in the channel is
  discarded the moment it arrives: not read, not stored, never sent to the model.
- **Images you attach to a message for the bot.** They are read in memory so the bot can describe
  what they show, and the bytes are discarded immediately after. (Exception: art you **deliberately
  upload** to illustrate a guide in `/guia` — that is stored, because it is the guide's material.)
- **Your status, your current game, your presence.** The bot does not even receive that from
  Discord.
- **Passwords, email, phone number, payment details, your HoYoverse account.** None of it is asked
  for or accepted. Your in-game UID is a public profile number, not a login.
- **Anything about you in other people's DMs.** The bot only sees its own DMs.

Server logs do not record message content under normal operating configuration.

## 3. How long it is kept

- **Message content: 30 days at most.** An automatic daily job deletes anything older — stored
  conversations and cached questions and answers. It does not depend on anyone asking.
- **Profile, UID and build scores:** kept for as long as you want them. They are gone the moment you
  run `/apagar-meus-dados`.
- **Moderation warnings:** they do not expire. They are the server's record of what happened there
  rather than data about your account, which is why they are not covered by a deletion request.
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

**The AI runs on the developer's own machine.** The language model that writes the replies is
executed locally, on owned hardware. Your messages are not sent to OpenAI, Google, Anthropic or any
other AI provider.

**Your messages are never used to train any model.** Not the developer's, not a third party's. The
model reads the question in order to answer it and retains nothing.

## 5. Your rights

Under Brazil's LGPD (Law 13.709/2018) you may request access, correction, deletion, or information
about how your data is used. In practice, without asking anyone:

| What you want | Command |
|---|---|
| See or change what the bot remembers about you | `/memoria` |
| Change or remove your in-game UID | `/uid` |
| **Erase everything, right now** | `/apagar-meus-dados confirmar:Verdadeiro` |

`/apagar-meus-dados` runs immediately and cannot be undone. It erases your profile, stored name,
UID, memory, conversations, build scores and drafts. Running it with `confirmar:Falso` only shows
you what would be removed, without deleting anything.

For any other request — including a copy of your data — use [Contact](#contact-1). Answered within
7 days.

You can also simply stop: not mentioning the bot is enough for it to stop reading or storing
anything about you. A server owner can also switch off every AI feature there with `/ia`.

## 6. Security and where the data lives

Data is stored in a database on a private server in `[SERVER COUNTRY]`, not publicly reachable: only
the developer accesses it, over an authenticated administrative connection. **[CONFIRM BEFORE
PUBLISHING: the volume holding the database and its backups is encrypted at rest.]** Backups run
daily and are rotated out after 14 days.

No system is perfect and absolute security cannot be promised. What can be promised is how little is
kept: the bot collects nothing that is not used by a feature you invoked yourself.

## 7. Children

Discord requires users to be 13, or older depending on the country. This bot follows the same rule
and is not directed at children. If you know of a child's data stored here, tell the developer
through [Contact](#contact-1) and it will be deleted.

## 8. Changes to this policy

Changes are recorded in this repository's history, with dates. If one of them materially changes
what is stored or for how long, it will be announced in the support server before taking effect.

## Contact

- Support server: `[PERMANENT INVITE LINK]`
- Email: `[YOUR CONTACT EMAIL]`
