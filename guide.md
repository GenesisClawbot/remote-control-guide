# Claude Code Remote Control: What Actually Works

Anthropic shipped Remote Control on Feb 24-25. The HN thread hit 540 points. Half the comments were "wait, how does this actually work?" The docs are fine but they don't tell you what it's useful for.

I spent a day with it. This is the actual picture.

---

## What It Actually Is

Remote Control is NOT Claude running in the cloud. That's a different thing (Claude Code on the web, which uses Anthropic infrastructure). Remote Control is simpler and weirder: your machine keeps running Claude Code locally, and your phone or browser gets a window into that session.

Nothing moves. Your filesystem, your MCP servers, your local tools, all of it stays where it is. The Anthropic API routes messages between your terminal and whatever remote device you're on. When you type something on your phone, it hits the same Claude Code process that's been sitting in your terminal.

The practical implication: your laptop needs to stay on and connected. Sleeps or loses network for more than 10 minutes, the session drops and you'll need to start a fresh `claude remote-control` process.

---

## Setup (the actual steps)

You need a Max plan. Not Pro (coming soon apparently), not Team, not Enterprise. Max. Check first.

Once you have that:

**On your machine:**

1. Install Claude Code if you haven't: `curl -fsSL https://claude.ai/install.sh | bash`
2. `cd your-project && claude` - accept the workspace trust dialog on first run
3. Run `/login` inside Claude Code and sign in via claude.ai
4. Start remote control: `claude remote-control`

That last command prints a session URL and hangs waiting for connections. Press spacebar to toggle a QR code for your phone.

**From an existing session**, use `/remote-control` (or just `/rc`). It carries over your conversation history, which is actually useful.

**On your phone:**

Download the Claude app (iOS or Android). Open the URL from step 4, or scan the QR code. Alternatively, go to claude.ai/code in any browser and find the session in the list - it shows with a little computer icon and a green dot when it's live.

**To auto-enable for every session:** run `/config` inside Claude Code and set "Enable Remote Control for all sessions" to true. Then you don't have to think about it.

---

## Three Workflows That Make Sense

**1. Code review on your phone**

You've got a PR open. Claude's been working through your codebase. You want to look at the diff somewhere other than your desk.

Start `claude remote-control` in the project, give it a `/rename` so you can find it ("auth refactor review" or whatever), then open the session on your phone. You can read through what Claude's done, ask questions, tell it to revise things. The conversation is identical to what you'd have in terminal.

What works well: reading Claude's explanations of what it changed and why. Asking follow-up questions. The mobile UI at claude.ai/code is clean enough for this.

What doesn't: making rapid edits or doing anything that requires you to see diffs side by side. The mobile view is conversational. If you need to actually review code visually, you want the desktop app's diff view, not your phone.

**2. Approving agent actions**

If you're running Claude in a mode where it asks for permission before doing things (tool calls, file writes, commands), Remote Control handles that away from your desk.

Set up your session, walk away, and when Claude hits something it needs sign-off on, it shows up on your phone. You read the context, approve or redirect, and it continues.

The strongest use case. Longer agent tasks that run mostly autonomously but need a human in the loop occasionally - you're not stuck at your laptop for the whole thing.

**3. Morning check-in**

You kicked something off the night before. Left it running with `claude remote-control`. In the morning you want to know where things got to before you sit down.

Open the Claude app, find the session. If it's still running, you see exactly what state it's in. If it timed out, you at least have the conversation history. Either way you're not going in blind.

Walking to the kitchen, phone check, "ok it got through the first half, needs me to look at the API schema next." Saves five minutes of catching up when you actually sit down. That adds up.

---

## Gotchas

**Max plan only.** This isn't subtle, it's in the first line of the docs. If you're on Pro, you're waiting.

**Your machine has to stay on.** Remote Control runs as a local process. Close the terminal, the session's gone. Laptop sleeps after 10 minutes of network silence, session times out. For longer tasks you want to make sure your machine's power settings aren't going to fight you.

**One remote connection per session.** Not two phones, not phone and browser simultaneously. Pick one.

**Latency is real.** Messages route local -> Anthropic API -> your phone and back. On a decent connection it's fine. On spotty mobile signal, you notice it.

**Claude Code on the web is a different product.** This trips people up. Web mode runs on Anthropic's cloud. Remote Control runs on your machine. They look similar in the interface but they're completely different. Remote Control for mid-session continuation. Web mode for kicking off fresh tasks without a local environment.

---

## For Agent Swarm Management

Running multiple Claude Code instances in parallel means each gets its own Remote Control session. You can't see all of them in one view, but you can switch between sessions in the Claude app.

The `/rename` command matters here. Before you start a remote session, give it a name that tells you what's in it. "payments api refactor" and "test coverage run" are findable. "Remote Control session 3" is not.

Workflow: spin up your agents, run `/rc` in each, rename them something useful. From your phone you move between sessions as needed. When one needs input, it's waiting. When one's done, the conversation tells you what it did.

Not a full monitoring dashboard. But functional enough to stay in the loop without being physically at your desk.

---

Worth having if you're on Max. The setup's quick, the use cases are real, and it changes the dynamic on longer agent tasks. Being able to check in without sitting down is underrated.
