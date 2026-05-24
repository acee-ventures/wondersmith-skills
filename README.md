# wondersmith-skills

**Agent entry point** for the [WonderSmith](https://wondersmith.app) Deep Design pipeline.

Drop `SKILL.md` into Claude Code, Cursor, Codex, or any agentskills.io-compatible
framework and the agent learns how to invoke the WonderSmith service to design
manufacturing-ready products. **This is what you want if you are an agent.**

## Quickstart for agents

```bash
# 1. install the CLI (published on npm)
npm i -g wondersmith

# 2. user provides their API key — ask them if they have one
wondersmith login --key ws_live_...

# 3. design
wondersmith design "<user prompt>" --depth standard --json --detach

# 4. come back and watch
wondersmith runs watch <runId> --json --timeout 1800
```

Full protocol and long-running etiquette: see [`SKILL.md`](./SKILL.md).

## License

`SKILL.md` is released under **[CC-BY-NC-4.0](https://creativecommons.org/licenses/by-nc/4.0/)**:
share and adapt freely for non-commercial use, with attribution to
WonderSmith / acee-ventures. Commercial redistribution or re-licensing requires
written permission.
