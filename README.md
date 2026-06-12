# apicrawl-skill

Agent skills for the [api-crawl](https://api.apicrawl.dev) catalog, packaged for install with
the [Vercel `skills` CLI](https://github.com/vercel-labs/skills).

## Skills

| Skill | What it does |
|-------|--------------|
| [`api-crawl`](skills/api-crawl/SKILL.md) | Search and explore API documentation indexed in the api-crawl catalog via its REST API. |

## Install

Install all skills into the current project:

```bash
# From GitHub (after pushing this repo)
npx skills add <your-user>/apicrawl-skill

# From a local checkout
npx skills add ./apicrawl-skill
```

Install a single skill into a specific agent:

```bash
npx skills add <your-user>/apicrawl-skill -s api-crawl -a claude-code
```

Install globally (into `~/<agent>/skills/`):

```bash
npx skills add <your-user>/apicrawl-skill -g
```

Other useful commands:

```bash
npx skills list                # list installed skills
npx skills find api            # search for skills
npx skills update              # update installed skills
npx skills remove api-crawl    # uninstall
```

## Repository layout

```
.
├── README.md
├── .claude-plugin/
│   └── marketplace.json        # Claude Code plugin-marketplace manifest
└── skills/
    └── api-crawl/
        └── SKILL.md            # the skill
```

To add more skills, create another `skills/<name>/SKILL.md` directory and add an entry
to `.claude-plugin/marketplace.json`.
