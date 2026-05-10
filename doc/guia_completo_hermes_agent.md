# Hermes Agent: guia completo para criar e evoluir seu próprio assistente pessoal de IA

O Hermes Agent é um agente de inteligência artificial open source que pode rodar na sua própria infraestrutura e funcionar como um assistente pessoal de verdade. Ele não é apenas um chatbot em uma página da web. Ele pode usar ferramentas, lembrar preferências, executar comandos, criar automações, trabalhar com arquivos, responder por texto ou áudio, acessar canais de mensagem e melhorar com o tempo.

A proposta principal do Hermes é permitir que você crie um agente que cresça junto com você. Quanto mais você usa, corrige, orienta e estrutura seus processos, mais útil ele se torna. Ele pode guardar contexto em arquivos de memória, transformar tarefas repetidas em skills, criar rotinas automáticas e operar por interfaces como Telegram, Discord, Slack, WhatsApp ou até iMessage.

O uso mais prático para começar é pelo Telegram. Assim, você consegue conversar com o agente pelo celular, pedir tarefas rápidas, acompanhar automações e receber resultados sem precisar estar no computador o tempo todo.

---

## O que é o Hermes Agent

O Hermes Agent é um assistente de IA que roda em infraestrutura própria. Ele pode ser instalado em um VPS, em um laptop, em um Mac Mini, dentro de um container Docker ou até em um Android via Termux.

Ele vem com várias capacidades já disponíveis, como:

- uso de ferramentas;
- execução de comandos no terminal;
- visão;
- automação de navegador;
- geração de imagens;
- texto para fala;
- resposta por áudio;
- planejamento de tarefas;
- criação e uso de skills;
- automações agendadas;
- busca em conversas anteriores;
- integração com plataformas de mensagem.

A grande diferença é que ele foi pensado para funcionar como um agente contínuo, não como uma conversa isolada. Ele pode lembrar preferências, aprender processos, criar rotinas e se adaptar com o tempo.

---

## Para que ele serve

O Hermes pode ser usado para tarefas pessoais, profissionais e operacionais. Alguns exemplos de uso:

- receber um resumo diário de notícias;
- monitorar comentários em redes sociais;
- responder interações com base em contexto;
- fazer checagens de servidor;
- gerar relatórios de pesquisa;
- criar lembretes de follow-up;
- resumir tarefas do dia;
- acompanhar projetos;
- organizar arquivos;
- criar documentos;
- executar scripts;
- manter backups automáticos;
- instalar e usar novas skills;
- ajudar a configurar o próprio ambiente.

A ideia é que ele funcione como um assistente pessoal ou operacional. Você pode pedir algo em linguagem natural, e ele tenta descobrir quais ferramentas usar, quais comandos executar e como entregar o resultado.

---

## A mentalidade correta para usar o Hermes

Um dos pontos mais importantes é entender que você não precisa saber tudo antes de usar o Hermes. Se você não sabe como configurar algo, pode pedir para o próprio Hermes pesquisar, ler a documentação e explicar o processo.

Por exemplo, você pode dizer:

> “Quero conectar você ao GitHub. Pesquise como fazer isso e me diga o que precisa de mim.”

Ou:

> “Esse erro apareceu no terminal. Explique o que significa e me diga como corrigir.”

Ou ainda:

> “Achei uma ferramenta interessante neste link. Leia, entenda e veja se dá para implementar aqui.”

Esse é um dos maiores diferenciais do agente: ele pode ajudar a construir e manter o próprio ambiente onde está rodando.

---

## Hermes, Claude Code e OpenCode

Essas ferramentas não precisam competir entre si. Cada uma pode ter uma função diferente.

O **Claude Code** é mais indicado para trabalho profundo no computador, principalmente programação, edição de projetos, análise de código e tarefas complexas feitas no terminal. Ele é uma boa escolha quando você está sentado no computador e quer trabalhar com calma.

O **OpenCode** também é uma ferramenta open source voltada a agentes de código e automação. Pode ser útil para tarefas rápidas, trabalho remoto e experimentação, mas pode exigir mais manutenção dependendo das atualizações e do ambiente.

O **Hermes** se destaca por ser leve, rápido, voltado para autoaperfeiçoamento e muito útil para automações via Telegram. Ele é especialmente interessante quando você quer pedir coisas pelo celular, criar crons, monitorar tarefas e operar um agente em movimento.

Um bom fluxo é usar cada ferramenta no papel certo:

- Claude Code para trabalho pesado no computador;
- Hermes para automações, tarefas recorrentes e comandos rápidos;
- GitHub como repositório central de contexto, skills e configurações;
- Telegram como controle remoto do agente.

---

## Os cinco pilares do Hermes

O Hermes é mais fácil de entender quando você pensa nele a partir de cinco pilares principais: memória, skills, soul, crons e loop de autoaperfeiçoamento.

---

## 1. Memória

A memória é o contexto durável que o agente carrega entre sessões. Como uma IA normalmente começa cada sessão sem lembrar de tudo, você precisa dar a ela arquivos de contexto que expliquem quem você é, como trabalha e quais projetos estão em andamento.

Os principais arquivos de memória são:

### `user.md`

Esse arquivo guarda informações sobre você:

- seu nome;
- seu estilo de trabalho;
- preferências;
- coisas que você gosta;
- coisas que você não gosta;
- tom de resposta preferido;
- hábitos;
- regras pessoais de uso.

### `memory.md`

Esse arquivo guarda informações sobre o ambiente e os projetos:

- projetos atuais;
- contexto de negócio;
- ferramentas usadas;
- caminhos importantes;
- integrações;
- decisões já tomadas;
- informações recorrentes.

A memória deve guardar informações duráveis. Não é o lugar certo para senhas, tokens, API keys ou tarefas temporárias.

Exemplos bons para memória:

> “Prefiro respostas diretas e organizadas.”

> “Meu projeto principal usa GitHub como fonte de verdade.”

> “Sempre que criar uma automação importante, também criar uma skill.”

Exemplos ruins para memória:

> “Hoje preciso responder João às 15h.”

> “Minha API key é…”

> “O status atual dessa tarefa é pendente.”

A memória é para contexto estável, não para segredos nem informações passageiras.

---

## 2. Skills

Skills são procedimentos reutilizáveis. Elas funcionam como receitas que ensinam o agente a fazer uma tarefa do jeito certo.

Se você pede a mesma coisa várias vezes, aquilo provavelmente deveria virar uma skill.

Por exemplo:

- gerar um relatório semanal;
- revisar um texto no seu estilo;
- fazer backup para o GitHub;
- monitorar comentários;
- criar posts;
- revisar segurança do VPS;
- abrir o dashboard;
- organizar arquivos de um projeto.

Uma skill normalmente fica em um arquivo chamado `skill.md`. Ela possui uma parte inicial em YAML, que explica quando a skill deve ser usada, e depois instruções em Markdown explicando como executar o processo.

A vantagem das skills é consistência. Em vez de o agente tentar lembrar de cabeça como fazer algo, ele consulta a skill e segue o processo definido.

A diferença entre memória e skill é simples:

**Memória é o que lembrar.**  
**Skill é como fazer novamente.**

Sempre que você perceber que está repetindo uma instrução, diga ao agente:

> “Transforme isso em uma skill para fazer sempre desse jeito.”

E se o agente deveria usar uma skill, mas não usou, você pode corrigir:

> “Quando eu pedir algo parecido com isso, use essa skill. Atualize a descrição dela para ser chamada corretamente.”

---

## 3. Soul

O `soul.md` define a personalidade do agente. Ele controla o estilo, o tom e a forma como o Hermes responde.

Você pode configurar o agente para ser:

- direto;
- formal;
- descontraído;
- técnico;
- sarcástico;
- motivador;
- objetivo;
- detalhado;
- mais humano;
- mais operacional.

Isso é útil principalmente se outras pessoas também forem interagir com o agente ou se ele for responder comentários, mensagens, clientes ou membros de uma comunidade.

Exemplos de orientação para o `soul.md`:

> “Seja direto, útil e sem enrolação.”

> “Tenha um tom profissional, mas amigável.”

> “Use humor leve quando fizer sentido, mas nunca seja rude.”

> “Priorize ações práticas em vez de explicações longas.”

O soul também pode evoluir. Se o agente estiver muito formal, muito longo ou fora do tom, você pode pedir:

> “Atualize seu soul para ser mais direto e menos verboso.”

---

## 4. Crons

Crons são automações agendadas. Eles transformam o Hermes de um agente reativo em um agente proativo.

Você pode dizer em linguagem natural:

> “Todo dia às 8h, me envie um resumo das minhas tarefas.”

Ou:

> “A cada 10 minutos pelas próximas 12 horas, verifique os comentários do meu vídeo.”

Ou:

> “Toda noite à meia-noite, envie as mudanças para o GitHub.”

O Hermes pode criar o cron, configurar a rotina, executar a tarefa em uma sessão isolada e depois enviar o resultado para o chat original.

Crons são úteis para:

- relatórios diários;
- checagens de servidor;
- backups;
- monitoramento de redes sociais;
- lembretes;
- auditorias;
- pesquisas recorrentes;
- tarefas operacionais.

Um ponto importante: crons precisam ser bem definidos. Como eles rodam sozinhos, o prompt deve ser claro e completo.

Um bom cron explica:

- o que fazer;
- quando fazer;
- onde executar;
- quais ferramentas usar;
- como reportar resultado;
- o que não fazer.

---

## 5. Loop de autoaperfeiçoamento

O Hermes melhora quando o aprendizado vira memória, skill ou histórico pesquisável.

O ciclo é:

1. Você pede uma tarefa.
2. O agente executa.
3. Você corrige ou aprova.
4. O agente salva o aprendizado.
5. Tarefas repetidas viram skills.
6. Preferências viram memória.
7. O agente usa esse contexto em tarefas futuras.

Mas isso não é mágico. O usuário precisa treinar o agente.

Se ele errar, corrija.  
Se ele acertar, peça para salvar o processo.  
Se ele repetir um erro, mande atualizar a memória ou a skill.  
Se você repetir uma instrução, transforme em skill.

Exemplos:

> “Você errou isso de novo. Atualize sua memória para não repetir.”

> “Esse processo ficou bom. Crie uma skill com esses passos.”

> “Da próxima vez, use esse formato de resposta.”

> “Isso não deve ser salvo na memória, é só uma tarefa temporária.”

---

## Arquivo de contexto do projeto

Além da memória global, pode existir um arquivo de contexto local do projeto, como `agents.md`.

Esse arquivo descreve:

- objetivo do projeto;
- estrutura das pastas;
- regras de trabalho;
- ferramentas usadas;
- padrões de código;
- comandos importantes;
- decisões técnicas;
- limites e restrições.

Ele é mais útil quando o Hermes está trabalhando em um projeto específico, principalmente com código ou arquivos.

A diferença é:

- `user.md`: contexto sobre o usuário;
- `memory.md`: contexto geral e durável;
- `soul.md`: personalidade do agente;
- `agents.md`: contexto de um projeto específico;
- `skill.md`: procedimento reutilizável.

---

## Onde instalar o Hermes

Você pode rodar o Hermes em vários ambientes:

- VPS;
- Docker;
- laptop;
- Mac Mini;
- Android via Termux.

Para começar de forma prática, uma opção comum é usar um VPS com Docker.

Um VPS é como um computador alugado na nuvem. Você recebe um IP, usuário e senha, acessa por terminal e instala o que precisa. Ele fica ligado o tempo todo, o que é ideal para automações.

---

## Instalação no root ou em Docker

Existem duas formas principais de instalar:

### Instalação no root do VPS

Nesse caso, o Hermes roda diretamente no ambiente principal do servidor.

Vantagens:

- mais direto;
- menos camadas;
- pode ser simples para um único agente.

Desvantagens:

- menos isolamento;
- mais chance de misturar arquivos e configurações;
- menos organizado para múltiplos agentes.

### Instalação com Docker

Nesse caso, o Hermes roda dentro de um container isolado.

Vantagens:

- mais organizado;
- facilita rodar vários agentes;
- cada agente pode ter suas próprias chaves;
- cada container pode ter sua própria memória;
- menos conflito entre ambientes;
- mais fácil separar funções.

A analogia é simples:

O VPS é o prédio.  
Cada container Docker é uma sala separada.  
Cada agente tem sua própria sala, suas próprias chaves, suas próprias ferramentas e sua própria memória.

Para a maioria dos casos, Docker é a opção mais organizada.

---

## Configuração inicial recomendada

Um caminho simples para começar:

1. Criar um VPS.
2. Usar Ubuntu 24.04 LTS.
3. Instalar o Hermes via Docker.
4. Definir usuário e senha de administrador.
5. Escolher o provedor de IA.
6. Escolher o modelo.
7. Configurar o Telegram.
8. Criar um bot no BotFather.
9. Autorizar seu usuário do Telegram.
10. Testar se o bot responde.
11. Salvar todas as informações importantes.
12. Conectar o Hermes ao GitHub.
13. Criar backup automático.
14. Criar a primeira skill.
15. Criar o primeiro cron.

---

## Organização dos seus agentes

Se você pretende usar agentes em VPS, é muito importante manter um projeto de controle com todas as informações operacionais.

Esse projeto pode conter:

- IP do VPS;
- hostname;
- usuário;
- senha de administrador;
- nome do container;
- localização dos arquivos;
- variáveis de ambiente;
- integrações;
- ferramentas disponíveis;
- comandos úteis;
- notas de segurança;
- status dos crons;
- observações sobre cada agente.

Isso evita confusão quando você tiver mais de um agente ou quando algo quebrar.

Você pode ter uma estrutura assim:

```text
vps-agents/
  pessoal-hermes/
    README.md
    env.example
    setup-notes.md
    security.md
  marketing-hermes/
    README.md
    setup-notes.md
  financeiro-hermes/
    README.md
    setup-notes.md
```

Nunca salve segredos diretamente em arquivos que serão enviados ao GitHub. Use `.env` local e garanta que ele esteja no `.gitignore`.

---

## Conectando o Hermes ao Telegram

O Telegram é uma das formas mais práticas de usar o Hermes.

O fluxo básico é:

1. Abrir o BotFather no Telegram.
2. Criar um novo bot.
3. Dar um nome ao bot.
4. Criar um username único.
5. Copiar o token do bot.
6. Inserir o token na configuração do Hermes.
7. Buscar seu próprio ID de usuário no Telegram.
8. Autorizar esse ID como usuário permitido.
9. Testar enviando uma mensagem ao bot.

Se o bot não responder, o próprio Hermes pode ajudar a diagnosticar.

Você pode dizer:

> “Enviei mensagem no Telegram, mas você não respondeu. Verifique o gateway e a conexão.”

Ele pode checar processos, reiniciar serviços e explicar o problema.

---

## CLI vs Telegram

A CLI e o Telegram usam o mesmo agente, mas oferecem experiências diferentes.

### CLI: o cockpit

A CLI é melhor para:

- programação;
- tarefas complexas;
- trabalho profundo;
- gerenciamento de contexto;
- uso de comandos avançados;
- tarefas de maior risco;
- análise de arquivos e projetos.

Na CLI você tem mais visibilidade sobre o que está acontecendo, quais comandos estão rodando, qual modelo está em uso e como o contexto está sendo consumido.

### Telegram: o controle remoto

O Telegram é melhor para:

- comandos rápidos;
- automações;
- check-ins;
- mensagens em movimento;
- tarefas de baixo risco;
- acompanhamento de crons;
- resumos;
- lembretes;
- pequenas operações.

O Telegram não é ideal para criar projetos complexos, programar sistemas grandes ou executar tarefas de alto risco sem supervisão. Ele tem menos visibilidade sobre contexto e estado da sessão.

Uma boa regra:

Use CLI para construir.  
Use Telegram para acionar, acompanhar e operar.

---

## Contexto: tokens, não mensagens

Um detalhe importante é que o contexto de um agente é baseado em tokens, não apenas em número de mensagens.

O modelo precisa carregar:

- instruções do sistema;
- memória;
- preferências;
- soul;
- contexto do projeto;
- histórico relevante;
- mensagens recentes;
- chamadas de ferramentas.

Em sessões longas, pode acontecer compactação automática de contexto. Isso significa que o agente tenta resumir partes anteriores para continuar trabalhando sem ultrapassar o limite.

Na CLI é mais fácil perceber isso. No Telegram, esse processo fica menos visível. Por isso, para tarefas grandes ou sensíveis, é melhor usar a CLI.

---

## Conectando ao GitHub

Depois que o Hermes estiver funcionando, uma das primeiras coisas a fazer é conectá-lo a um repositório privado no GitHub.

Isso permite versionar:

- skills;
- arquivos de memória;
- configurações não sensíveis;
- documentação;
- scripts;
- contexto do agente;
- histórico de mudanças.

A vantagem é que, se o VPS quebrar, você consegue recriar o agente a partir do repositório.

O GitHub vira a fonte de verdade do agente.

---

## Segurança com tokens e API keys

Nunca cole tokens, senhas ou API keys diretamente no chat do agente.

Mesmo que o ambiente seja privado, isso pode ficar salvo no histórico da conversa. A melhor prática é salvar as chaves em variáveis de ambiente, geralmente dentro de um arquivo `.env`.

Um exemplo de comando seria:

```bash
hermes config set GITHUB_TOKEN valor_do_token
```

Assim, a chave fica no ambiente do agente e não na conversa.

Se estiver usando Docker, tome cuidado para editar o `.env` correto. Pode existir um `.env` no VPS e outro dentro do container. O Hermes que roda no container normalmente precisa acessar o `.env` do container.

Também é importante:

- usar tokens com escopo limitado;
- dar apenas permissões necessárias;
- criar chaves diferentes para agentes diferentes;
- nomear as chaves por agente;
- rotacionar tokens quando necessário;
- não enviar `.env` para o GitHub.

---

## Primeiro cron recomendado: backup diário

Uma das primeiras automações úteis é o backup diário para o GitHub.

Você pode pedir:

> “Toda noite à meia-noite, envie as mudanças deste agente para o repositório privado no GitHub. Crie uma skill para isso e configure um cron.”

O Hermes pode:

- criar a skill de sincronização;
- configurar o cron;
- fazer commit;
- fazer push;
- atualizar a memória;
- lidar com diferenças de fuso horário;
- manter o repositório atualizado.

Esse é um ótimo primeiro workflow porque protege o trabalho feito no agente.

---

## Permissões durante a execução

Quando o Hermes tenta executar certos comandos, ele pode pedir permissão.

Normalmente existem opções como:

- permitir uma vez;
- permitir durante a sessão;
- permitir sempre.

Para ações repetitivas e confiáveis, como um cron de backup para GitHub, pode fazer sentido permitir sempre. Para ações novas, sensíveis ou arriscadas, é melhor permitir apenas uma vez e observar.

Não dê permissões permanentes sem entender o que o agente está fazendo.

---

## Primeira conversa com o agente

Depois de instalar o Hermes, vale a pena fazer uma conversa inicial de onboarding.

Você pode dizer:

> “Meu nome é X. Quero que você seja meu assistente pessoal de IA. Vou te explicar meus objetivos, projetos, ferramentas e estilo de trabalho. Salve apenas o que for durável e importante.”

Depois, fale sobre:

- quem você é;
- no que trabalha;
- quais ferramentas usa;
- quais projetos são importantes;
- como prefere receber respostas;
- que tipo de automações quer criar;
- o que o agente não deve fazer;
- quais limites ele deve respeitar.

Essa conversa ajuda o Hermes a construir a memória inicial.

---

## Como criar boas skills

Uma skill deve ser criada quando existe um processo repetível.

Exemplo de pedido:

> “Crie uma skill para revisar meus textos no meu estilo. Sempre corrija clareza, estrutura e tom, mas sem mudar demais minha voz.”

Outro exemplo:

> “Crie uma skill para gerar relatório semanal dos projetos, separando progresso, bloqueios e próximos passos.”

Uma boa skill deve conter:

- quando usar;
- quando não usar;
- passos do processo;
- formato de saída;
- ferramentas necessárias;
- cuidados;
- exemplos;
- critérios de sucesso.

Se o agente não estiver usando a skill certa, ajuste a descrição dela. Muitas vezes, o problema está no YAML inicial, que não explica bem quando a skill deve ser chamada.

---

## Como usar feedback para melhorar o Hermes

O feedback é essencial.

Quando o agente fizer algo errado:

> “Isso não está correto. Da próxima vez, faça assim. Atualize a skill.”

Quando ele responder longo demais:

> “Seja mais direto. Atualize seu soul para evitar respostas longas nesse tipo de tarefa.”

Quando ele repetir uma pergunta:

> “Você já tem essa informação. Salve na memória para não perguntar de novo.”

Quando um processo ficar bom:

> “Esse fluxo funcionou. Transforme em skill.”

Quando uma informação for temporária:

> “Não salve isso na memória. É válido só para esta tarefa.”

Esse cuidado impede que a memória fique poluída ou ultrapassada.

---

## Segurança: trate o agente como um funcionário novo

Um agente autônomo deve receber apenas os acessos necessários.

Não dê acesso total logo no começo.

Boas práticas:

- criar email separado para o agente;
- usar contas específicas por função;
- evitar usar sua conta pessoal;
- limitar escopos de API;
- usar chaves nomeadas;
- monitorar gastos;
- separar agentes por área;
- bloquear portas desnecessárias;
- usar firewall;
- restringir acesso por IP quando possível;
- revisar permissões com frequência.

Pense assim:

Se você não daria sua senha bancária para um estagiário novo, também não deve dar ao agente.

---

## Firewall e auditoria de segurança

Se o Hermes roda em VPS, segurança importa.

Você pode configurar:

- firewall;
- bloqueio de portas;
- acesso restrito por IP;
- usuários separados;
- backups;
- scanner de malware;
- logs;
- auditoria periódica.

Também pode criar uma skill de segurança:

> “Uma vez por semana, revise a configuração do VPS, portas abertas, permissões, uso de chaves e possíveis riscos. Gere um relatório simples.”

Ou:

> “Toda noite, verifique se os serviços essenciais estão ativos e se há algo estranho nos logs.”

Isso ajuda a manter o ambiente saudável.

---

## Quando criar múltiplos agentes

No começo, use apenas um agente principal. Não crie vários agentes cedo demais.

Crie um novo agente quando ele precisar de:

- memória própria;
- ferramentas próprias;
- credenciais próprias;
- permissões diferentes;
- agenda própria;
- público diferente;
- função contínua separada.

Exemplos:

- agente pessoal;
- agente de marketing;
- agente financeiro;
- agente de suporte;
- agente de operações;
- agente de pesquisa;
- agente de conteúdo.

Evite criar um “mega-agente” com todas as chaves, todas as funções, todas as ferramentas e todas as memórias. Isso aumenta risco, confusão e dificuldade de manutenção.

O ideal é separar quando houver motivo real.

---

## Como pensar em múltiplos agentes

Uma boa analogia:

O VPS é o prédio.  
Cada container Docker é uma sala.  
Cada Hermes é um funcionário.  
Cada funcionário tem suas ferramentas, chaves, memória e função.

Um agente de marketing talvez precise acessar redes sociais, calendário editorial e ferramentas de design.

Um agente financeiro talvez precise acessar planilhas, relatórios e sistema financeiro.

Um agente de suporte talvez precise acessar email, base de conhecimento e histórico de clientes.

Eles não precisam compartilhar todas as chaves entre si.

Isso reduz risco e melhora organização.

---

## Dashboard do Hermes

O Hermes também pode ter um dashboard visual para acompanhar:

- sessões recentes;
- canais conectados;
- crons;
- skills;
- plugins;
- configurações;
- chaves;
- tarefas;
- Kanban.

O dashboard pode ser útil quando você tem vários agentes ou quer uma visão mais visual do que está acontecendo.

Porém, em muitos casos, o Telegram já resolve o uso diário. O dashboard pode exigir configuração de gateway ou túnel, então a primeira abertura pode dar um pouco de trabalho.

Depois que funcionar, uma boa ideia é criar uma skill:

> “Sempre que eu pedir para abrir o dashboard, me mostre os comandos necessários e execute o processo.”

---

## Manutenção contínua

O Hermes não é uma ferramenta que você configura uma vez e esquece. Ele é mais parecido com um colega de trabalho que precisa ser treinado.

Regras simples de manutenção:

- se errar duas vezes, atualize memória ou skill;
- se repetir instrução, crie skill;
- se estiver fora do tom, ajuste o soul;
- se algo estranho acontecer, revise o memory;
- se criar rotina importante, coloque em cron;
- se criar cron, documente;
- se usar API key, limite permissões;
- se algo funcionar bem, salve o processo.

Também vale pedir periodicamente:

> “Leia sua memória e me mostre o que está salvo.”

> “Leia seu soul e me diga qual personalidade você está seguindo.”

> “Liste suas skills principais.”

> “Liste seus crons ativos.”

Isso ajuda a manter controle sobre o agente.

---

## Erros comuns

### Colar API key no chat

Evite. Use variável de ambiente.

### Usar Telegram para tarefas complexas demais

Telegram é ótimo para comando rápido, mas não para projetos grandes.

### Criar muitos agentes cedo demais

Comece com um agente principal. Separe só quando houver necessidade.

### Guardar coisa temporária na memória

Memória deve ser durável. Tarefa temporária deve ficar na conversa ou em um sistema de tarefas.

### Não revisar skills

Skills ruins geram resultados ruins repetidamente. Ajuste quando necessário.

### Misturar `.env` do VPS com `.env` do container

Se estiver usando Docker, confirme onde o Hermes realmente lê as variáveis.

### Dar permissões demais

Use o princípio do menor privilégio.

---

## Fluxo recomendado para começar

O caminho mais simples é:

1. Instalar o Hermes em Docker.
2. Conectar ao Telegram.
3. Fazer o onboarding inicial.
4. Conectar ao GitHub.
5. Criar repositório privado.
6. Configurar `.gitignore`.
7. Salvar chaves no `.env`, não no chat.
8. Criar cron de backup diário.
9. Criar a primeira skill útil.
10. Ajustar memória e soul.
11. Usar por alguns dias.
12. Corrigir erros e transformar processos em skills.
13. Criar novas automações.
14. Só depois pensar em múltiplos agentes.

---

## Exemplos de comandos úteis para pedir ao Hermes

Você pode usar frases como:

> “Leia sua memória e me mostre o que você sabe sobre mim.”

> “Atualize sua memória com essa preferência.”

> “Não salve isso na memória.”

> “Crie uma skill para esse processo.”

> “Atualize essa skill para ser chamada sempre que eu pedir esse tipo de tarefa.”

> “Crie um cron diário para executar isso às 8h.”

> “Liste todos os crons ativos.”

> “Pause esse cron por enquanto.”

> “Conecte este projeto a um repositório privado no GitHub.”

> “Explique esse erro do terminal.”

> “Verifique se o Telegram está conectado corretamente.”

> “Abra o dashboard e me diga quais comandos preciso rodar.”

> “Faça uma auditoria de segurança do VPS.”

---

## Exemplo de estrutura ideal de agente

Um agente Hermes bem configurado pode ter:

- Telegram conectado;
- GitHub privado conectado;
- backup diário automático;
- memória limpa;
- soul ajustado;
- skills para tarefas recorrentes;
- crons documentados;
- API keys em `.env`;
- firewall configurado;
- permissões mínimas;
- logs e auditorias;
- documentação em Markdown;
- rotina de manutenção.

Com isso, ele deixa de ser apenas um chatbot e passa a funcionar como uma camada operacional de automação.

---

## Ideia central

O Hermes Agent é uma forma de criar seu próprio assistente pessoal de IA, rodando em infraestrutura própria, com memória, personalidade, skills e automações. Ele pode ser usado pelo Telegram como controle remoto, pela CLI para trabalho profundo e pelo GitHub como fonte de verdade.

O melhor caminho é começar simples: instalar, conectar ao Telegram, conectar ao GitHub, criar backup automático e transformar tarefas repetidas em skills. Depois, com o uso, o agente vai ficando mais alinhado ao seu estilo, seus projetos e suas necessidades.

O segredo não é configurar tudo de uma vez. O segredo é usar, corrigir, salvar aprendizados e evoluir aos poucos.
