# kclaude.marketplace

Kevin's personal [Claude Code](https://claude.com/claude-code) plugin marketplace.

## Plugins

| Plugin | Description |
| --- | --- |
| `andrej-karpathy-skills` | `karpathy-guidelines`: behavioral guidelines to reduce common LLM coding mistakes. Referenced from [multica-ai/andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills) (MIT), pinned to a commit. |
| `matt-pocock-skills` | Matt Pocock's engineering, planning and writing skills (`tdd`, `diagnosing-bugs`, `code-review`, `grilling`, `to-spec`, `triage`, ...). |

## Activation

### 1. Add the marketplace

In Claude Code:

```
/plugin marketplace add kbosirany/kclaude.marketplace
```

Or with the full URL:

```
/plugin marketplace add https://github.com/kbosirany/kclaude.marketplace.git
```

For a local clone (handy while developing):

```
/plugin marketplace add D:\Local\75_Dev\Repositories\kclaude.marketplace
```

### 2. Install a plugin

```
/plugin install matt-pocock-skills@kclaude
```

(`kclaude` is the marketplace `name` in `.claude-plugin/marketplace.json`, not the repo name.)
Restart the session or run `/reload-plugins` if the skills do not show up.

Skills are then available as `/matt-pocock-skills:<skill>` (for example `/matt-pocock-skills:tdd`),
and Claude can also invoke them on its own based on each skill's `description`.

### 3. Update / remove

```
/plugin marketplace update kclaude
/plugin uninstall matt-pocock-skills@kclaude
/plugin marketplace remove kclaude
```

### Enable for a whole team or project

Add to the project's `.claude/settings.json` so collaborators are prompted to install it:

```json
{
  "extraKnownMarketplaces": {
    "kclaude": {
      "source": { "source": "github", "repo": "kbosirany/kclaude.marketplace" }
    }
  },
  "enabledPlugins": {
    "matt-pocock-skills@kclaude": true
  }
}
```

## Repository layout

```
.claude-plugin/marketplace.json      # marketplace catalog (lists the plugins)
plugins/
  <plugin-name>/
    .claude-plugin/plugin.json       # plugin manifest
    skills/
      <skill-name>/
        SKILL.md                     # the skill (frontmatter + instructions)
        ...                          # optional supporting files and scripts
```

## Adding a skill to an existing plugin

1. Create `plugins/<plugin>/skills/<skill-name>/SKILL.md`:

   ```markdown
   ---
   name: my-skill
   description: What it does and when Claude should use it. This is what triggers the skill.
   ---

   # My skill

   Instructions for Claude...
   ```

2. Put extra reference files or scripts next to `SKILL.md` and link them from it.
3. Bump `version` in the plugin's `plugin.json` (users only receive updates when the version changes).
4. Commit and push, then users run `/plugin marketplace update kclaude`.

Keep `name` equal to the folder name, and write the `description` as a trigger ("Use when ...").

## Adding a new plugin

1. Create `plugins/<new-plugin>/.claude-plugin/plugin.json`:

   ```json
   {
     "name": "new-plugin",
     "version": "0.1.0",
     "description": "What this plugin provides",
     "author": { "name": "Kevin" }
   }
   ```

2. Add skills under `plugins/<new-plugin>/skills/`. A plugin can also ship `commands/`, `agents/`
   and `hooks/hooks.json` at its root.
3. Register it in `.claude-plugin/marketplace.json`:

   ```json
   {
     "name": "new-plugin",
     "source": "./plugins/new-plugin",
     "description": "What this plugin provides"
   }
   ```

4. Commit and push.

A plugin can also live in another repository: use a `source` such as
`{ "source": "github", "repo": "owner/repo", "sha": "<commit>" }` instead of a relative path
(this is how `andrej-karpathy-skills` is wired). Pinning `sha` means upstream changes are only
picked up when you bump it after reviewing them.

## Validate before pushing

```
claude plugin validate .
```

or, inside Claude Code, `/plugin validate .`.

## Credits

`matt-pocock-skills` is a copy of skills authored by [Matt Pocock](https://github.com/mattpocock/skills);
see that repository for the original work and its license. Individual skills may carry their own
credits (for example `skills/pr/CREDITS.md`).
