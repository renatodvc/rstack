# rstack

This repository is a Claude Code plugin that holds the skills and prompt preferences I use in my day-to-day with Claude Code.

- `skills/` holds one directory per skill, and the plugin loads them all. 
- `hooks/hooks.json` registers a `UserPromptSubmit` hook that appends `appendPrompt.md` to every prompt sent by the user. That text sets the format of the final response to be more objective. It can be disabled by setting the environment variable `CLAUDE_DONT_APPEND=1` at startime. 

## Skills

Installed as a plugin, the skills are namespaced by the plugin name, for example `/rstack:unslop`.

Skills marked manual only set `disable-model-invocation: true`. The model never starts them, they must be called with the slash command.

| Skill | What it does | Manual only |
| --- | --- | --- |
| `bro` | Restates the last message in plain language, with no jargon. | ✅ |
| `ears-spec` | Interviews the user regarding a project to then write the `spec.md` in EARS format, plus a traceable `tasks.md`. | ❌ |
| `explain` | Explains the previous reply, or the excerpts passed after the command, separated by `\|`. | ❌ |
| `fix-findings` | Takes an `independent-review` report, settles the open decisions with the user, then applies the fixes. | ✅ |
| `grill-me` | Interviews the user about a plan one question at a time, down each branch of the design tree. | ❌ |
| `grilling` | Same goal as `grill-me`, but asks the whole frontier of open questions in numbered rounds. | ❌ |
| `im-not-reading-all-that` | Asks for a shorter, objective summary of the last message. | ✅ |
| `independent-review` | Reviews finished work with fresh subagents that never saw the conversation, then verifies the findings with another fresh subagent. Reports only, fixes nothing. | ❌ |
| `resurrect` | Resumes an inactive session with a short summary of what it was about, what is done, and what is left. | ✅ |
| `sleep-deprived` | The model engages the user with shorter and simpler answers with a high signal-to-noise ratio. | ❌ |
| `unslop` | Cuts AI tells from writing and puts voice back in. | ❌ |

## Install

In Claude Code:

```
/plugin marketplace add renatodvc/rstack
/plugin install rstack@rstack
```

Claude Code clones the repository into `~/.claude/plugins/cache/`. Nothing is written anywhere else. The plugin brings the skills and the prompt hook.

To work on the plugin from a local clone, point the marketplace at the clone instead:

```
/plugin marketplace add ~/path/to/rstack
/plugin install rstack@rstack
```

## Credits

Some skills come from other repositories. Thanks to their authors.

| Skill | Author | Source |
| --- | --- | --- |
| `bro` | @dmmulroy | https://github.com/dmmulroy/skills/ |
| `grill-me`, `grilling` | @mattpocock | https://github.com/mattpocock/skills |
| `unslop` | @poteto | https://github.com/cursor/plugins/blob/main/pstack/ |

## License

MIT. See [LICENSE](LICENSE).
