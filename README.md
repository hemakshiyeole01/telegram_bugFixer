# Bugfix Room: human-approved AI bug fixing in Telegram

Built for the **Flip the Switch Hackathon** (Track 1: Best Business Workflow) by team **DevDope**.

A small dev team reports a bug in a Telegram group. An AI coding agent, connected with **Switch**, reads the repo, finds the root cause, and proposes a fix. Nothing is changed until a human in the chat replies `approved`.

## The problem

Small teams lose time on triage: someone reports a bug in chat, someone else opens the code, finds the cause, and writes a fix. AI agents can do this, but running them blindly on a codebase is risky. This workflow keeps the agent fast and the human in control, all inside the chat the team already uses.

## How Switch is used

| Piece | Setup |
|---|---|
| Agent | **OpenCode** (managed agent in Switch Console), named `Hemakshi-BugFixer` |
| Chat platform | **Telegram** (a group with the team and a Switch bot) |
| Room | The Telegram group is linked to a Switch room. The agent and people share the same room history |
| Agent working directory | A Spring Boot project (Student Management Portal) |
| Permissions | Auto mode: safe actions run on their own, risky ones ask first |

Switch is what makes this work: it puts the agent into the group as a named teammate, keeps one shared room for everyone, and routes `@mentions` to the agent. Without Switch there would be no shared channel between the team and the agent.

## The workflow

1. A teammate posts a bug in the Telegram group and mentions the agent.
2. The agent reads the relevant code and explains the root cause (file and method).
3. The agent proposes a fix and asks for approval.
4. A human replies `approved` (or asks for changes).
5. Only then does the agent apply the fix and report which files it changed.

The agent's instructions are in [`agent-instructions.md`](agent-instructions.md). The room rule is:

> Agent proposes, a human approves. No code changes without an explicit "approved" from a person in this room.

## How to run it

**Requirements:** Docker Desktop (running), Node.js, a Telegram account, an API key for any model OpenCode supports.

1. Download and install **Switch Console** from https://github.com/sandbox-quantum/switch/releases/latest and start Docker Desktop first.
2. Create a Telegram bot with **@BotFather** (`/newbot`), copy the token, then run `/setprivacy` and choose **Disable** so the bot can read group messages.
3. In Switch Console, go to **Home → Messaging apps → Connect** and add the Telegram bot token.
4. Install OpenCode: `npm i -g opencode-ai`. Run `opencode` in your project folder, use `/connect` to add your model provider key, and check that it replies.
5. In Switch Console, go to **Your Agents → New agent**, choose OpenCode, name it with your own name (e.g. `Yourname-BugFixer`), set **Directory** to your project folder, paste the contents of `agent-instructions.md`, and set the model explicitly to one that works with your key. Create the agent.
6. Create a Telegram group, add the bot and your teammates, then in the group run:
   `!invite-agent @Yourname-BugFixer`
7. Report a bug: `@Yourname-BugFixer bug: <describe it>`. Reply `approved` when you like the proposed fix.

## Notes and limitations

- The agent is only as good as the model behind it. During the build we hit a free-tier quota error because the default model Switch picked had no quota, and setting the model explicitly fixed it.
- The approval step relies on the agent following its instructions and on Switch's permission modes. For a real team, keep auto mode on and review the diff before merging.

## Team

DevDope: Hemakshi Yeole, Chhavi Sharma
