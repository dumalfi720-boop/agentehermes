# Direct step by step to use Hermes Agent

## 1. Create the VPS

1. Hire a VPS.
2. Choose Ubuntu 24.04 LTS.
3. Enable automatic backup if available.
4. Save:
   - VPS IP;
   - root user;
   - password;
   - hostname.

---

## 2. Install Hermes

The simplest way is via **Docker**.

1. Access the VPS dashboard.
2. Open Docker Manager.
3. Search for Hermes Agent.
4. Click install/deploy.
5. Define:
   - admin username;
   - admin password.
6. Save this data to a secure file.

---

## 3. Opening Hermes for the first time

1. Click **Open** on the Hermes container.
2. Enter the created username and password.
3. Start the initial setup.

---

## 4. Choose the AI model

During setup:

1. Choose your AI provider.
2. Choose the model.
3. Login/authorize if necessary.
4. Confirm that Hermes responds on the terminal.

---

## 5. Create the bot on Telegram

1. Open Telegram.
2. Search for **BotFather**.
3. Submit:```text
/newbot
```4. Choose the bot name.
5. Choose the bot's username.
6. Copy the generated token.

---

## 6. Connect Telegram to Hermes

In the Hermes setup:

1. Choose Telegram as your channel.
2. Paste the bot token.
3. Get your Telegram ID using a user info bot.
4. Paste your ID into Hermes.
5. Confirm that this user is authorized.

Then test:```text
Olá
```If he responds, Telegram is working.

---

## 7. If Telegram does not respond

Tell Hermes through the terminal:```text
O Telegram não está respondendo. Verifique o gateway e reinicie o serviço se necessário.
```He must diagnose and try to correct it.

---

## 8. Do initial onboarding

Send a message to Hermes telling him who you are and what you want from him.

Example:```text
Meu nome é [seu nome]. Quero que você seja meu assistente pessoal de IA. Vou te usar para automações, organização, pesquisa, criação de conteúdo e tarefas recorrentes. Salve na memória apenas informações duráveis e importantes.
```Then count:

- your projects;
- your tools;
- your response style;
- what he can do;
- what he shouldn't do.

---

## 9. Connect to GitHub

Ask Hermes:```text
Quero conectar este agente a um repositório privado no GitHub. Pesquise o melhor processo, crie um .gitignore seguro e me diga exatamente o que você precisa de mim.
```He will probably ask:

- your GitHub user;
- repository name;
- name for commits;
- GitHub token.

---

## 10. Create GitHub Token

On GitHub:

1. Go to Settings.
2. Developer settings.
3. Personal access tokens.
4. Create a token.
5. Give repo/content read-write permission.
6. Copy the token.

---

## 11. Save token securely

Do not paste the token into the chat.

In the container terminal, use:```bash
hermes config set GITHUB_TOKEN seu_token_aqui
```Then tell Hermes:```text
O token do GitHub está salvo no .env como GITHUB_TOKEN. Use ele para criar e conectar o repositório privado.
```---

## 12. Create automatic backup

Once GitHub is connected, ask:```text
Crie uma skill e um cron para fazer backup automático deste agente no GitHub todos os dias à meia-noite. Faça commit e push das mudanças importantes, sem enviar arquivos .env ou segredos.
```This should be the first cron.

---

## 13. Create the first useful skill

Choose a repetitive task.

Example:```text
Crie uma skill para revisar meus textos. Corrija clareza, estrutura e tom, mas mantenha minha voz.
```Or:```text
Crie uma skill para gerar um resumo diário das minhas tarefas e prioridades.
```---

## 14. Create useful crons

Direct examples:```text
Todo dia às 8h, me envie um resumo das minhas prioridades do dia.
```

```text
Toda sexta às 17h, gere um relatório semanal com avanços, pendências e próximos passos.
```

```text
Uma vez por semana, revise a segurança do VPS e me envie um relatório.
```---

## 15. Adjust memory

When he learns something important, say:```text
Salve isso na memória.
```When it's something temporary, say:```text
Não salve isso na memória.
```When he makes a mistake, say:```text
Atualize sua memória para não repetir esse erro.
```---

## 16. Adjust personality

If he is responding poorly, ask:```text
Atualize seu soul para ser mais direto, prático e menos verboso.
```Or:```text
Quero que você responda de forma profissional, objetiva e com passos claros.
```---

## 17. Review what he knows

From time to time, ask for:```text
Leia sua memória e me mostre o que está salvo.
```

```text
Leia seu soul e me mostre qual personalidade você está seguindo.
```

```text
Liste suas skills principais.
```

```text
Liste seus crons ativos.
```---

## 18. Use Telegram in everyday life

Use Telegram for quick commands:```text
Faça um resumo do meu dia.
```

```text
Crie um lembrete para eu responder esse cliente.
```

```text
Pesquise esse assunto e me traga os pontos principais.
```

```text
Rode o cron de backup agora.
```

```text
Liste o status dos meus projetos.
```---

## 19. Use terminal for heavy tasks

Use the CLI/terminal to:

- configure server;
- touch important files;
- programming;
- install tools;
- resolve errors;
- review context;
- long or risky tasks.

Simple rule:

**Telegram = remote control.**  
**Terminal = cockpit.**

---

## 20. Create new agents only when you need them

Don't start with multiple agents.

Only create another Hermes if he needs:

- separate memory;
- different tools;
- different keys;
- different permissions;
- own routine;
- specific function.

Examples:

- Hermes Marketing;
- Hermes Financeiro;
- Hermes Support;
- Hermes Content;
- Hermes Operations.

---

## Ideal order summarized```text
1. Criar VPS
2. Instalar Hermes via Docker
3. Escolher modelo de IA
4. Conectar Telegram
5. Fazer onboarding inicial
6. Conectar GitHub
7. Salvar token no .env
8. Criar backup automático
9. Criar primeira skill
10. Criar primeiro cron útil
11. Ajustar memória
12. Ajustar personalidade
13. Usar no dia a dia
14. Melhorar com feedback
15. Separar novos agentes só quando necessário
```---

## First command I would use after installation```text
Meu nome é [seu nome]. Quero que você seja meu assistente pessoal de IA. Primeiro, me ajude a conectar este agente a um repositório privado no GitHub, criar um backup automático diário e organizar sua memória, skills e configurações de forma segura.
```
