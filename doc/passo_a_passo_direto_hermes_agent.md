# Passo a passo direto para usar o Hermes Agent

## 1. Criar o VPS

1. Contrate um VPS.
2. Escolha Ubuntu 24.04 LTS.
3. Ative backup automático, se disponível.
4. Salve:
   - IP do VPS;
   - usuário root;
   - senha;
   - hostname.

---

## 2. Instalar o Hermes

O jeito mais simples é via **Docker**.

1. Acesse o painel do VPS.
2. Abra o Docker Manager.
3. Procure por Hermes Agent.
4. Clique em instalar/deploy.
5. Defina:
   - admin username;
   - admin password.
6. Salve esses dados em um arquivo seguro.

---

## 3. Abrir o Hermes pela primeira vez

1. Clique em **Open** no container do Hermes.
2. Entre com o usuário e senha criados.
3. Inicie o setup inicial.

---

## 4. Escolher o modelo de IA

Durante o setup:

1. Escolha o provedor de IA.
2. Escolha o modelo.
3. Faça login/autorização, se necessário.
4. Confirme que o Hermes responde no terminal.

---

## 5. Criar o bot no Telegram

1. Abra o Telegram.
2. Procure por **BotFather**.
3. Envie:

```text
/newbot
```

4. Escolha o nome do bot.
5. Escolha o username do bot.
6. Copie o token gerado.

---

## 6. Conectar o Telegram ao Hermes

No setup do Hermes:

1. Escolha Telegram como canal.
2. Cole o token do bot.
3. Pegue seu ID do Telegram usando um bot de user info.
4. Cole seu ID no Hermes.
5. Confirme que esse usuário é autorizado.

Depois teste:

```text
Olá
```

Se ele responder, o Telegram está funcionando.

---

## 7. Se o Telegram não responder

Diga ao Hermes pelo terminal:

```text
O Telegram não está respondendo. Verifique o gateway e reinicie o serviço se necessário.
```

Ele deve diagnosticar e tentar corrigir.

---

## 8. Fazer o onboarding inicial

Envie uma mensagem para o Hermes dizendo quem você é e o que quer dele.

Exemplo:

```text
Meu nome é [seu nome]. Quero que você seja meu assistente pessoal de IA. Vou te usar para automações, organização, pesquisa, criação de conteúdo e tarefas recorrentes. Salve na memória apenas informações duráveis e importantes.
```

Depois conte:

- seus projetos;
- suas ferramentas;
- seu estilo de resposta;
- o que ele pode fazer;
- o que ele não deve fazer.

---

## 9. Conectar ao GitHub

Peça ao Hermes:

```text
Quero conectar este agente a um repositório privado no GitHub. Pesquise o melhor processo, crie um .gitignore seguro e me diga exatamente o que você precisa de mim.
```

Ele provavelmente vai pedir:

- seu usuário do GitHub;
- nome do repositório;
- nome para commits;
- token do GitHub.

---

## 10. Criar token do GitHub

No GitHub:

1. Vá em Settings.
2. Developer settings.
3. Personal access tokens.
4. Crie um token.
5. Dê permissão de repo/conteúdo read-write.
6. Copie o token.

---

## 11. Salvar token com segurança

Não cole o token no chat.

No terminal do container, use:

```bash
hermes config set GITHUB_TOKEN seu_token_aqui
```

Depois diga ao Hermes:

```text
O token do GitHub está salvo no .env como GITHUB_TOKEN. Use ele para criar e conectar o repositório privado.
```

---

## 12. Criar backup automático

Depois que o GitHub estiver conectado, peça:

```text
Crie uma skill e um cron para fazer backup automático deste agente no GitHub todos os dias à meia-noite. Faça commit e push das mudanças importantes, sem enviar arquivos .env ou segredos.
```

Esse deve ser o primeiro cron.

---

## 13. Criar a primeira skill útil

Escolha uma tarefa repetitiva.

Exemplo:

```text
Crie uma skill para revisar meus textos. Corrija clareza, estrutura e tom, mas mantenha minha voz.
```

Ou:

```text
Crie uma skill para gerar um resumo diário das minhas tarefas e prioridades.
```

---

## 14. Criar crons úteis

Exemplos diretos:

```text
Todo dia às 8h, me envie um resumo das minhas prioridades do dia.
```

```text
Toda sexta às 17h, gere um relatório semanal com avanços, pendências e próximos passos.
```

```text
Uma vez por semana, revise a segurança do VPS e me envie um relatório.
```

---

## 15. Ajustar a memória

Quando ele aprender algo importante, diga:

```text
Salve isso na memória.
```

Quando for algo temporário, diga:

```text
Não salve isso na memória.
```

Quando ele errar, diga:

```text
Atualize sua memória para não repetir esse erro.
```

---

## 16. Ajustar a personalidade

Se ele estiver respondendo mal, peça:

```text
Atualize seu soul para ser mais direto, prático e menos verboso.
```

Ou:

```text
Quero que você responda de forma profissional, objetiva e com passos claros.
```

---

## 17. Revisar o que ele sabe

De tempos em tempos, peça:

```text
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
```

---

## 18. Usar Telegram no dia a dia

Use o Telegram para comandos rápidos:

```text
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
```

---

## 19. Usar terminal para tarefas pesadas

Use a CLI/terminal para:

- configurar servidor;
- mexer em arquivos importantes;
- programar;
- instalar ferramentas;
- resolver erros;
- revisar contexto;
- tarefas longas ou arriscadas.

Regra simples:

**Telegram = controle remoto.**  
**Terminal = cockpit.**

---

## 20. Criar novos agentes só quando precisar

Não comece com vários agentes.

Crie outro Hermes apenas se ele precisar de:

- memória separada;
- ferramentas diferentes;
- chaves diferentes;
- permissões diferentes;
- rotina própria;
- função específica.

Exemplos:

- Hermes Marketing;
- Hermes Financeiro;
- Hermes Suporte;
- Hermes Conteúdo;
- Hermes Operações.

---

## Ordem ideal resumida

```text
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
```

---

## Primeiro comando que eu usaria depois da instalação

```text
Meu nome é [seu nome]. Quero que você seja meu assistente pessoal de IA. Primeiro, me ajude a conectar este agente a um repositório privado no GitHub, criar um backup automático diário e organizar sua memória, skills e configurações de forma segura.
```
