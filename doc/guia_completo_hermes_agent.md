# Hermes Agent: Complete guide to creating and evolving your own AI personal assistant

Hermes Agent is an open source artificial intelligence agent that can run on your own infrastructure and function as a true personal assistant. It's not just a chatbot on a web page. It can use tools, remember preferences, run commands, create automations, work with files, respond via text or audio, access message channels, and improve over time.

Hermes' main proposal is to allow you to create an agent that grows with you. The more you use, correct, guide and structure your processes, the more useful it becomes. It can store context in memory files, transform repeated tasks into skills, create automatic routines and operate through interfaces such as Telegram, Discord, Slack, WhatsApp or even iMessage.

The most practical use to start with is Telegram. This way, you can talk to the agent on your cell phone, request quick tasks, follow automations and receive results without having to be on the computer all the time.

---

## What is Hermes Agent

Hermes Agent is an AI assistant that runs on its own infrastructure. It can be installed on a VPS, on a laptop, on a Mac Mini, inside a Docker container or even on an Android via Termux.

It comes with several capabilities already available, such as:

- use of tools;
- execution of commands in the terminal;
- vision;
- browser automation;
- image generation;
- text to speech;
- audio response;
- task planning;
- creation and use of skills;
- scheduled automations;
- search in previous conversations;
- integration with messaging platforms.

The big difference is that it was designed to work as a continuous agent, not as an isolated conversation. It can remember preferences, learn processes, create routines and adapt over time.

---

## What is it for

Hermes can be used for personal, professional and operational tasks. Some usage examples:

- receive a daily news summary;
- monitor comments on social networks;
- respond to interactions based on context;
- perform server checks;
- generate research reports;
- create follow-up reminders;
- summarize tasks for the day;
- monitor projects;
- organize files;
- create documents;
- run scripts;
- maintain automatic backups;
- install and use new skills;
- help configure the environment itself.

The idea is that it works as a personal or operational assistant. You can ask it for something in natural language, and it tries to figure out what tools to use, what commands to run, and how to deliver the result.

---

## The correct mindset for using Hermes

One of the most important points is to understand that you don't need to know everything before using Hermes. If you don't know how to configure something, you can ask Hermes to research it, read the documentation and explain the process.

For example, you might say:

> “I want to connect you to GitHub. Research how to do that and let me know what you need from me.”

Or:

> “This error appeared in the terminal. Explain what it means and tell me how to fix it.”

Or even:

> “I found an interesting tool at this link. Read it, understand it and see if it can be implemented here.”

This is one of the agent's biggest differentiators: it can help build and maintain the environment in which it runs.

---

## Hermes, Claude Code and OpenCode

These tools do not need to compete with each other. Each one can have a different function.

**Claude Code** is best suited for in-depth work on the computer, mainly programming, project editing, code analysis and complex tasks performed on the terminal. It is a good choice when you are sitting at the computer and want to work calmly.

**OpenCode** is also an open source tool aimed at code and automation agents. It can be useful for quick tasks, remote work, and experimentation, but may require more maintenance depending on updates and your environment.

**Hermes** stands out for being light, fast, aimed at self-improvement and very useful for automations via Telegram. It is especially interesting when you want to order things on your cell phone, create crons, monitor tasks and operate an agent on the move.

A good flow is to use each tool in the right role:

- Claude Code for heavy work on the computer;
- Hermes for automations, recurring tasks and quick commands;
- GitHub as a central repository for context, skills and configurations;
- Telegram as remote agent control.

---

## The five pillars of Hermes

Hermes is easier to understand when you think of it in terms of five main pillars: memory, skills, soul, crons and self-improvement loop.

---

## 1. Memory

Memory is the durable context that the agent carries between sessions. Because an AI typically starts each session without remembering everything, you need to give it context files that explain who you are, how you work, and what projects are in progress.

The main memory files are:

###`user.md`This file stores information about you:

- your name;
- your work style;
- preferences;
- things you like;
- things you don't like;
- preferred response tone;
- habits;
- personal rules of use.

###`memory.md`This file stores information about the environment and projects:

- current projects;
- business context;
- used tools;
- important paths;
- integrations;
- decisions already taken;
- recurring information.

Memory must store durable information. It's not the right place for passwords, tokens, API keys, or temporary tasks.

Good examples for memory:

> “I prefer direct and organized answers.”

> “My main project uses GitHub as its source of truth.”

> “Whenever you create an important automation, also create a skill.”

Bad memory examples:

> “Today I need to respond to João at 3pm.”

> “My API key is…”

> “The current status of this task is pending.”

Memory is for stable context, not for secrets or passing information.

---

## 2. Skills

Skills are reusable procedures. They work like recipes that teach the agent how to do a task the right way.

If you ask the same thing several times, it should probably become a skill.

For example:

- generate a weekly report;
- revise a text in your style;
- backup to GitHub;
- monitor comments;
- create posts;
- review VPS security;
- open the dashboard;
- organize project files.

A skill is normally in a file called`skill.md`. It has an initial part in YAML, which explains when the skill should be used, and then instructions in Markdown explaining how to execute the process.

The advantage of skills is consistency. Instead of the agent trying to remember how to do something in his head, he consults the skill and follows the defined process.

The difference between memory and skill is simple:

**Memory is what you remember.**  
**Skill is like doing it again.**

Whenever you notice yourself repeating an instruction, tell the agent:

> “Turn this into a skill to always do it this way.”

And if the agent should use a skill, but didn't, you can correct it:

> "When I ask for something like this, use this skill. Update its description to be called correctly."

---

## 3. Soul

O`soul.md`defines the agent's personality. He controls the style, tone and way Hermes responds.

You can configure the agent to be:

- direct;
- formal;
- relaxed;
- technical;
- sarcastic;
- motivating;
- objective;
- detailed;
- more human;
- more operational.

This is especially useful if other people will also interact with the agent or if the agent will respond to comments, messages, customers or members of a community.

Examples of guidance for`soul.md`:

> “Be direct, helpful and without fluff.”

> “Have a professional but friendly tone.”

> “Use light humor when it makes sense, but never be rude.”

> “Prioritize practical actions over long explanations.”

The soul can also evolve. If the agent is too formal, too long, or off-key, you can ask:

> “Update your soul to be more direct and less verbose.”

---

## 4. Crons

Crons are scheduled automations. They transform Hermes from a reactive agent to a proactive agent.

You can say in natural language:

> “Every day at 8am, send me a summary of my tasks.”

Or:

> “Every 10 minutes for the next 12 hours, check the comments on my video.”

Or:

> “Every night at midnight, push changes to GitHub.”

Hermes can create the cron, configure the routine, execute the task in an isolated session and then send the result to the original chat.

Crons are useful for:

- daily reports;
- server checks;
- backups;
- monitoring of social networks;
- reminders;
- audits;
- recurring searches;
- operational tasks.

An important point: crons need to be well defined. Since they run alone, the prompt must be clear and complete.

A good cron explains:

- what to do;
- when to do it;
- where to perform;
- what tools to use;
- how to report results;
- what not to do.

---

## 5. Self-Improvement Loop

Hermes improves when learning becomes memory, skill or searchable history.

The cycle is:

1. You ask for a task.
2. The agent executes.
3. You correct or approve.
4. The agent saves the learning.
5. Repeated tasks become skills.
6. Preferences become memory.
7. The agent uses this context in future tasks.

But this is not magic. The user needs to train the agent.

If he makes a mistake, correct it.  
If he gets it right, ask to save the process.  
If he repeats a mistake, have the memory or skill updated.  
If you repeat an instruction, turn it into a skill.

Examples:

> "You got that wrong again. Refresh your memory so you don't repeat it."

> “This process was good. Create a skill with these steps.”

> “Next time, use this answer format.”

> “This should not be saved in memory, it is just a temporary task.”

---

## Project context file

In addition to global memory, there may be a local project context file, such as`agents.md`.

This file describes:

- project objective;
- folder structure;
- work rules;
- used tools;
- code standards;
- important commands;
- technical decisions;
- limits and restrictions.

It is most useful when Hermes is working on a specific project, especially with code or files.

The difference is:

-`user.md`: context about the user;
-`memory.md`: general and durable context;
-`soul.md`: agent personality;
-`agents.md`: context of a specific project;
-`skill.md`: reusable procedure.

---

## Where to install Hermes

You can run Hermes in several environments:

- VPS;
- Docker;
- laptop;
- Mac Mini;
- Android via Termux.

To get started practically, a common option is to use a VPS with Docker.

A VPS is like a rented computer in the cloud. You receive an IP, username and password, access it via terminal and install what you need. It stays on all the time, which is ideal for automations.

---

## Installation on root or Docker

There are two main ways to install:

### VPS root installation

In this case, Hermes runs directly in the main server environment.

Advantages:

- more direct;
- fewer layers;
- can be simple for a single agent.

Disadvantages:

- less insulation;
- more chance to mix files and settings;
- less organized for multiple agents.

### Installation with Docker

In this case, Hermes runs inside an isolated container.

Advantages:

- more organized;
- makes it easier to run multiple agents;
- each agent can have its own keys;
- each container can have its own memory;
- less conflict between environments;
- easier to separate functions.

The analogy is simple:

The VPS is the building.  
Each Docker container is a separate room.  
Each agent has their own room, their own keys, their own tools and their own memory.

For most cases, Docker is the most organized option.

---

## Recommended initial configuration

A simple way to get started:

1. Create a VPS.
2. Use Ubuntu 24.04 LTS.
3. Install Hermes via Docker.
4. Set administrator username and password.
5. Choose the AI ​​provider.
6. Choose the model.
7. Configure Telegram.
8. Create a bot in BotFather.
9. Authorize your Telegram user.
10. Test whether the bot responds.
11. Save all important information.
12. Connect Hermes to GitHub.
13. Create automatic backup.
14. Create the first skill.
15. Create the first cron.

---

## Organization of your agents

If you intend to use agents on VPS, it is very important to maintain a control project with all operational information.

This project may contain:

- VPS IP;
- hostname;
- user;
- administrator password;
- name of the container;
- location of files;
- environment variables;
- integrations;
- available tools;
- useful commands;
- security notes;
- cron status;
- observations about each agent.

This avoids confusion when you have more than one agent or when something breaks.

You can have a structure like this:```text
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
```Never save secrets directly in files that will be uploaded to GitHub. Use`.env`location and ensure it is in the`.gitignore`.

---

## Connecting Hermes to Telegram

Telegram is one of the most practical ways to use Hermes.

The basic flow is:

1. Open BotFather on Telegram.
2. Create a new bot.
3. Give the bot a name.
4. Create a unique username.
5. Copy the bot token.
6. Insert the token in the Hermes configuration.
7. Search for your own Telegram user ID.
8. Authorize this ID as a permitted user.
9. Test by sending a message to the bot.

If the bot doesn't respond, Hermes itself can help diagnose.

You can say:

> “I sent you a message on Telegram, but you didn't respond. Check the gateway and connection.”

It can check processes, restart services and explain the problem.

---

## CLI vs Telegram

The CLI and Telegram use the same agent but offer different experiences.

### CLI: the cockpit

The CLI is best for:

- programming;
- complex tasks;
- deep work;
- context management;
- use of advanced commands;
- higher risk tasks;
- analysis of files and projects.

In the CLI you have more visibility into what is happening, which commands are running, which model is in use and how the context is being consumed.

### Telegram: the remote control

Telegram is best for:

- quick commands;
- automations;
- check-ins;
- messages on the move;
- low risk tasks;
- cron monitoring;
- summaries;
- reminders;
- small operations.

Telegram is not ideal for creating complex projects, programming large systems, or performing high-risk tasks without supervision. It has less visibility into context and session state.

A good rule of thumb:

Use CLI to build.  
Use Telegram to activate, monitor and operate.

---

## Context: tokens, not messages

An important detail is that an agent's context is based on tokens, not just the number of messages.

The model needs to load:

- system instructions;
- memory;
- preferences;
- soul;
- project context;
- relevant history;
- recent messages;
- tool calls.

In long sessions, automatic context compression may occur. This means that the agent tries to summarize previous parts to continue working without exceeding the limit.

In the CLI it is easier to understand this. On Telegram, this process is less visible. Therefore, for large or sensitive tasks, it is better to use the CLI.

---

## Connecting to GitHub

Once Hermes is up and running, one of the first things to do is connect it to a private repository on GitHub.

This allows you to version:

- skills;
- memory files;
- non-sensitive settings;
- documentation;
- scripts;
- agent context;
- change history.

The advantage is that if the VPS breaks, you can recreate the agent from the repository.

GitHub becomes the agent's source of truth.

---

## Security with tokens and API keys

Never paste tokens, passwords or API keys directly into agent chat.

Even if the environment is private, this can be saved in the conversation history. Best practice is to save the keys in environment variables, usually within a file`.env`.

An example command would be:```bash
hermes config set GITHUB_TOKEN valor_do_token
```Thus, the key lies in the agent's environment and not in the conversation.

If you are using Docker, be careful to edit the`.env`correct. There may be a`.env`on the VPS and another inside the container. The Hermes that runs in the container normally needs to access the`.env`of the container.

Also important:

- use tokens with limited scope;
- give only necessary permissions;
- create different keys for different agents;
- name the keys by agent;
- rotate tokens when necessary;
- do not send`.env`to GitHub.

---

## Recommended first cron: daily backup

One of the first useful automations is the daily backup to GitHub.

You can order:

> "Every night at midnight, push changes from this agent to the private repository on GitHub. Create a skill for this and set up a cron."

Hermes can:

- create the synchronization skill;
- configure cron;
- commit;
- push;
- update memory;
- deal with time zone differences;
- keep the repository updated.

This is a great first workflow because it protects the work done on the agent.

---

## Permissions during execution

When Hermes tries to execute certain commands, it may ask for permission.

Typically there are options such as:

- allow once;
- allow during the session;
- always allow.

For repetitive and reliable actions, like a cron backup to GitHub, it may make sense to always allow. For new, sensitive, or risky actions, it is best to only allow it once and observe.

Don't give permanent permissions without understanding what the agent is doing.

---

## First conversation with the agent

After installing Hermes, it's worth having an initial onboarding conversation.

You can say:

> “My name is X. I want you to be my personal AI assistant. I will explain my goals, projects, tools and work style. Only save what is durable and important.”

Then talk about:

- who you are;
- what you work on;
- what tools you use;
- which projects are important;
- how you prefer to receive responses;
- what type of automations do you want to create;
- what the agent should not do;
- what limits he must respect.

This conversation helps Hermes build his initial memory.

---

## How to create good skills

A skill should be created when there is a repeatable process.

Order example:

> “Create a skill to revise my texts in my style. Always correct clarity, structure and tone, but without changing my voice too much.”

Another example:

> “Create a skill to generate a weekly project report, separating progress, blocks and next steps.”

A good skill should contain:

- when to use;
- when not to use;
- process steps;
- output format;
- necessary tools;
- care;
- examples;
- success criteria.

If the agent isn't using the right skill, adjust its description. Often, the problem is in the initial YAML, which does not explain well when the skill should be called.

---

## How to use feedback to improve Hermes

Feedback is essential.

When the agent does something wrong:

> "That's not correct. Next time, do it like this. Update the skill."

When he responds too long:

> “Be more direct. Update your soul to avoid long answers in this type of task.”

When he repeats a question:

> "You already have this information. Save it in your memory so you don't ask again."

When a process is good:

> “That flow worked. Turn it into a skill.”

When information is temporary:

> "Don't commit this to memory. It's only valid for this task."

This precaution prevents the memory from becoming polluted or outdated.

---

## Security: treat the agent like a new employee

An autonomous agent must receive only the necessary accesses.

Don't give full access at the beginning.

Good practices:

- create separate email for the agent;
- use specific accounts by function;
- avoid using your personal account;
- limit API scopes;
- use named keys;
- monitor expenses;
- separate agents by area;
- block unnecessary ports;
- use firewall;
- restrict access by IP when possible;
- review permissions frequently.

Think of it like this:

If you wouldn't give your bank password to a new intern, you shouldn't give it to the agent either.

---

## Firewall and security audit

If Hermes runs on a VPS, security matters.

You can configure:

- firewall;
- blocking of doors;
- access restricted by IP;
- separate users;
- backups;
- malware scanner;
- logs;
- periodic audit.

You can also create a security skill:

> “Once a week, review VPS configuration, open ports, permissions, key usage, and potential risks. Generate a simple report.”

Or:

> “Every night, check if essential services are active and if there is anything strange in the logs.”

This helps keep the environment healthy.

---

## When creating multiple agents

In the beginning, use just one main agent. Don't create multiple agents too early.

Create a new agent when it needs:
- own memory;
- own tools;
- own credentials;
- different permissions;
- own agenda;
- different audience;
- separate continuous function.

Examples:

- personal agent;
- marketing agent;
- financial agent;
- support agent;
- operations agent;
- research agent;
- content agent.

Avoid creating a “mega-agent” with all the keys, all the functions, all the tools and all the memories. This increases risk, confusion and difficulty in maintenance.

The ideal is to separate when there is a real reason.

---

## How to think about multiple agents

A good analogy:

The VPS is the building.  
Each Docker container is a room.  
Each Hermes is an employee.  
Each employee has their own tools, keys, memory and function.

A marketing agent may need access to social media, an editorial calendar, and design tools.

A financial agent may need to access spreadsheets, reports and the financial system.

A support agent may need access to email, knowledge base, and customer history.

They don't need to share all the keys with each other.

This reduces risk and improves organization.

---

## Hermes Dashboard

Hermes can also have a visual dashboard to monitor:

- recent sessions;
- connected channels;
- crons;
- skills;
- plugins;
- settings;
- keys;
- tasks;
- Kanban.

The dashboard can be useful when you have several agents or want a more visual view of what is happening.

However, in many cases, Telegram already solves daily use. The dashboard may require gateway or tunnel configuration, so opening it for the first time may take a bit of work.

Once it works, a good idea is to create a skill:

> “Whenever I ask to open the dashboard, show me the necessary commands and execute the process.”

---

## Ongoing maintenance

Hermes is not a tool that you set up once and forget about. He's more like a co-worker who needs to be trained.

Simple maintenance rules:

- if you make a mistake twice, update memory or skill;
- if you repeat instructions, create skill;
- if it is out of tune, adjust the soul;
- if something strange happens, review the memory;
- if you create an important routine, put it in cron;
- if you create cron, document it;
- if you use API key, limit permissions;
- if something works fine, save the process.

It is also worth asking periodically:

> “Read your memory and show me what’s saved.”

> “Read your soul and tell me which personality you are following.”

> “List your main skills.”

> “List your active crons.”

This helps you maintain control over the agent.

---

## Common mistakes

### Paste API key in chat

Avoid. Use environment variable.

### Use Telegram for very complex tasks

Telegram is great for quick command, but not for large projects.

### Create too many agents too soon

Start with a lead agent. Only separate when necessary.

### Store temporary thing in memory

Memory must be durable. Temporary task must be in the conversation or in a task system.

### Do not review skills

Bad skills generate bad results over and over again. Adjust when necessary.

### Mix`.env`of VPS with`.env`of the container

If you are using Docker, confirm where Hermes actually reads the variables.

### Give too many permissions

Use the principle of least privilege.

---

## Recommended flow to get started

The simplest way is:

1. Install Hermes in Docker.
2. Connect to Telegram.
3. Carry out initial onboarding.
4. Connect to GitHub.
5. Create private repository.
6. Configure`.gitignore`.
7. Save keys to`.env`, not in chat.
8. Create daily backup cron.
9. Create the first useful skill.
10. Adjust memory and soul.
11. Use for a few days.
12. Correct errors and transform processes into skills.
13. Create new automations.
14. Only then think about multiple agents.

---

## Examples of useful commands to ask Hermes

You can use phrases like:

> “Read your memory and show me what you know about me.”

> “Refresh your memory with this preference.”

> “Don’t commit this to memory.”

> “Create a skill for this process.”

> “Update this skill to be called whenever I ask for this type of task.”

> “Create a daily cron to run this at 8am.”

> “List all active crons.”

> “Pause this cron for now.”

> “Connect this project to a private repository on GitHub.”

> “Explain this terminal error.”

> “Check if Telegram is connected correctly.”

> “Open the dashboard and tell me which commands I need to run.”

> “Do a VPS security audit.”

---

## Example of ideal agent structure

A well-configured Hermes agent can have:

- Telegram connected;
- Private GitHub connected;
- automatic daily backup;
- clean memory;
- adjusted soul;
- skills for recurring tasks;
- documented crons;
- API keys in`.env`;
- configured firewall;
- minimum permissions;
- logs and audits;
- documentation in Markdown;
- maintenance routine.

With this, it stops being just a chatbot and starts functioning as an operational layer of automation.

---

## Central idea

Hermes Agent is a way to create your own personal AI assistant, running on your own infrastructure, with memory, personality, skills and automations. It can be used by Telegram as a remote control, by the CLI for deep work, and by GitHub as a source of truth.

The best way is to start simple: install, connect to Telegram, connect to GitHub, create automatic backup and transform repeated tasks into skills. Then, with use, the agent becomes more aligned with your style, your projects and your needs.

The secret is not to configure everything at once. The secret is to use, correct, save learning and evolve little by little.