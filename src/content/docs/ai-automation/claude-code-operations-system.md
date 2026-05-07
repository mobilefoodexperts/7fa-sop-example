---
title: "AI — Claude Code Operations System: Your Agency's AI Brain"
description: How to build a full Claude Code operations system for a marketing agency — context layer, skills library, SOP wiki, command centers, MCP tool connections, and persistent memory. Real implementation with a live reference.
---

## What

A Claude Code operations system turns a general-purpose AI into an AI that knows your agency specifically — your clients, your processes, your team, your tools. It doesn't just answer questions; it runs workflows, creates ClickUp tasks, searches your CRM, reads your SOPs, and remembers context across sessions.

This SOP documents the full architecture built by Fire Marketing Pros (firemarketingpros.com), a fire protection marketing agency, as a real working implementation. The live SOP wiki built using this system is at [mfe-operations-wiki.pages.dev](https://mfe-operations-wiki.pages.dev) — you can browse it as a reference before building your own.

The system has six layers. Each layer is independently useful but they compound. A solo founder can have the first three layers running in a week.

## Who Owns It

**Agency Owner** — sets up and maintains the CLAUDE.md, approves the skills library, owns the SOP wiki. This is not delegatable to a VA at setup time. Once running, VAs can use the system within clearly defined boundaries (see Step 3). The owner is the brain; this system is the memory.

---

## Steps

### Step 1 — The Context Layer (CLAUDE.md)

Claude Code reads a `CLAUDE.md` file automatically when you open it from a folder. This is your agency's standing context — the master document that tells Claude everything it needs to know about your operation without you re-explaining it every session.

**What goes in CLAUDE.md:**

- Business overview (what you do, what niche, who you serve)
- All active clients and what stage they're at
- Team roster (names, roles, email, ClickUp IDs)
- Folder structure (where everything lives on your computer)
- Connected tools (which MCP servers are running and what they do)
- Current priorities (top 3–5 things happening right now)
- Rules for Claude (what it can do without asking, what requires confirmation, what is off-limits)

**The key principle:** CLAUDE.md is the source of truth. Every time something changes — new client, new priority, new team member — you update CLAUDE.md. Claude reads it fresh every session. No stale context.

**VA access rules (critical):** Your CLAUDE.md should define exactly what VAs can and cannot do using Claude Code. The Fire Marketing Pros implementation enforces these rules at the CLAUDE.md level:
- VAs cannot delete files, modify configs, or send messages without showing drafts first
- VAs cannot access restricted folders (finances, legal, private client data)
- VAs can create ClickUp tasks, run research, draft content — and everything is shown before it goes out

**Folder structure:** Keep one master folder on your desktop (`MFE-HQ` in this implementation, rename to your agency name). Everything — client files, SOPs, skills, the wiki repo, command centers — lives inside it. CLAUDE.md is at the root. When Claude opens from this folder, it has full context.

---

### Step 2 — Skills Library and Recipes

Skills are reusable prompts stored as markdown files in a `skills/` folder. A skill for a discovery call, a skill for prospect research, a skill for weekly review — each one is a complete prompt you run once and reuse forever.

**Skills vs. recipes:**
- **Skills** are standalone workflows — run them independently with `run [skill-name]`
- **Recipes** are multi-step sequences that combine skills — the weekly CEO review recipe runs agenda prep → ClickUp audit → decision log update in sequence

**The Fire Marketing Pros skills library (13 skills built):**

| Folder | Skills |
|---|---|
| `skills/fmp/` | prospect-research, gbp-audit, cold-outreach, discovery-call, objection-handler, client-onboarding |
| `skills/content/` | video-outline, seo-brief, brain-dump, meeting-synthesis |
| `skills/food-service/` | fair-application, employee-handbook, event-debrief |
| `skills/ob1/` | memory-migration, quick-capture, weekly-review, spark, second-brain-migration |

**How to build your first skill:**

1. Identify a task you do repeatedly (prospect research before a sales call)
2. Write the prompt you'd give Claude to do it well — include your agency's context, the expected output format, and any guardrails
3. Save it as `skills/[department]/[task-name].md`
4. Run it with `run prospect-research`

The value compounds: once a skill is written, anyone on your team uses it the same way. Discovery calls always follow the same framework. Prospect research always hits the same data points. Consistency at the cost of writing one prompt.

**Example skill file structure:**

```markdown
# Skill: prospect-research

**Trigger:** run prospect-research [company name]

**What it does:** Full research brief on a prospect before a discovery call.
Pulls GBP status, website audit, estimated revenue, social presence, 
competitor landscape, and 3 likely objections based on company size.

**Output:** A 1-page brief ready to review 10 minutes before the call.

---

## The Prompt

[Complete prompt goes here — specific to your niche]
```

---

### Step 3 — SOP Wiki

The SOP wiki is a static site (Astro + Starlight) that lives in your agency folder and deploys to Cloudflare Pages for free. It's where your SOPs live in a searchable, linkable, team-accessible format.

**Why a wiki instead of a Google Doc folder:**
- VAs find things without asking
- Every page is version-controlled in git
- Claude Code can read and update SOPs directly
- It deploys automatically on every push — changes are live in 60 seconds
- Search is built in (Pagefind, no backend required)

**The Fire Marketing Pros wiki has 120+ pages** across: YouTube content production, food service operations, fire protection service delivery, agency sales and onboarding, team management, finance, and tools. The full wiki is public at [mfe-operations-wiki.pages.dev](https://mfe-operations-wiki.pages.dev).

**Setup (30 minutes):**

1. Clone the 7FA SOP starter: `git clone https://github.com/ojhurst/7fa-sop-example your-agency-wiki`
2. Edit `astro.config.mjs` to update the title and sidebar
3. `npm install && npm run dev` — running at localhost:4321
4. Push to GitHub, connect to Cloudflare Pages — auto-deploys on every push
5. Add pages as you build them

**SOP writing rule:** Every page follows the four-heading format from this wiki (What / Who Owns It / Steps / Definition of Done). Consistency is the whole value. A VA who reads any SOP in your library knows exactly where to find the information they need because every page is the same shape.

---

### Step 4 — Command Centers

A command center is a single HTML file that gives you a live dashboard of everything that matters. No build step, no backend — pure HTML/CSS with links to every tool, every client, every active project.

**One HTML file per business or vertical:**
- `command-center.html` — master dashboard (all businesses/clients)
- `[client]-command-center.html` — per-client views for larger clients

**What a command center shows:**
- Revenue and pipeline status (manually updated or pulled via API)
- Links to every active client's folder, GHL pipeline, and ClickUp space
- Quick links to the wiki sections relevant to that business
- Team assignments and current sprint focus
- Key dates (renewals, deliverable deadlines, campaign launches)

**The philosophy:** Command centers replace the 15-tab workflow. One file, bookmark it, open it every morning. Everything is one click away. No hunting through Notion, Drive, ClickUp separately.

**Deploy once to Cloudflare Pages** alongside the wiki — same repo, same deploy. Both are static files. Update them locally, push to git, live in 60 seconds.

---

### Step 5 — MCP Tool Connections

MCP (Model Context Protocol) connections give Claude Code direct access to your tools without browser automation. You connect once; Claude uses the tool in every session.

**The Fire Marketing Pros MCP stack:**

| Tool | What Claude Can Do |
|---|---|
| **ClickUp** | Create tasks, update status, search tasks, assign to team |
| **GoHighLevel (GHL)** | Search contacts, pull pipeline status, send messages |
| **Gmail** | Draft and send emails, search inbox, read threads |
| **Google Calendar** | Create events, check availability, list upcoming |
| **Discord** | Post updates to channels, read messages, notify team |
| **Playwright** | Browser automation — SEMrush research, scraping, form fills |
| **Canva** | Search designs, export assets, create from templates |
| **Open Brain** | Save and retrieve persistent memory (see Step 6) |

**How to connect a tool:**

MCP servers are configured in `claude_desktop_config.json`. Most popular tools have pre-built MCP servers you install via npm or run as Docker containers. The config file maps each tool to its server and credentials.

```json
{
  "mcpServers": {
    "clickup": {
      "command": "npx",
      "args": ["-y", "@anthropic/mcp-server-clickup"],
      "env": { "CLICKUP_TOKEN": "your-token" }
    }
  }
}
```

Once connected, Claude uses these tools naturally. Instead of "go to ClickUp and create a task," you say "create a ClickUp task for Lorelie to edit the video — due Friday" and it happens in the session.

---

### Step 6 — Open Brain (Persistent Memory)

Open Brain is the memory layer. It's a Supabase database with pgvector that stores every decision, insight, meeting debrief, and key piece of context — indexed by meaning, not keywords.

Without persistent memory, every Claude session starts from zero. Open Brain means context from a prospect call last Tuesday shows up when you're writing their proposal next week.

**The stack:**
- Supabase (free tier covers most agencies) — PostgreSQL + pgvector extension
- OpenRouter (~$5/month) — generates the embeddings that make semantic search work
- A Supabase Edge Function (open-brain-mcp) — exposes the database as an MCP server
- Claude Code connects via HTTP MCP transport

**Five capture templates (use these daily):**

```
Decision: [what]. Context: [why]. Owner: [who].

[Name] — [what you learned about them or what happened].

Insight: [what you realized]. Triggered by: [what prompted it].

Meeting with [who] about [topic]. Key points: [X]. Action items: [Y].

Saving from Claude: [the key output or framework worth keeping].
```

**The weekly review (every Sunday, 5 minutes):**
Ask Claude to pull everything from Open Brain in the last 7 days, surface patterns, identify open loops, and tell you what to focus on next week. After 3–4 weeks of consistent capturing, this becomes one of the most useful rituals in the business.

---

## Definition of Done

**Layer 1 (CLAUDE.md) complete when:**
- Claude opens from your agency folder and correctly describes your business, clients, and team without being asked
- VA safety rules are defined and Claude refuses to violate them when tested

**Layer 2 (Skills) complete when:**
- At least 5 skills built for your highest-frequency workflows
- Any team member can run a skill without understanding the underlying prompt

**Layer 3 (Wiki) complete when:**
- SOP wiki live on Cloudflare Pages
- At least 10 pages covering your core delivery processes
- VAs using it to find answers without asking you

**Layer 4 (Command Centers) complete when:**
- Master command center open every morning within 30 seconds of sitting down
- All active clients and projects visible in one view

**Layer 5 (MCP Connections) complete when:**
- ClickUp + GHL + Gmail connected
- Can create a ClickUp task and send an email draft without leaving Claude Code

**Layer 6 (Open Brain) complete when:**
- First memory migration done (Claude extracts everything it knows about your business and saves it)
- 7+ captures per week as an established habit
- Weekly review ritual running every Sunday

**Full system healthy when:**
- Monday morning: open Claude from agency folder → reads CLAUDE.md → searches Open Brain → gives you a briefing on priorities and open loops from last week → without being asked
- Team members using the wiki to answer their own questions
- New skills added after every repeated task type is identified
- Weekly review generating actionable insights from the previous week's captures
