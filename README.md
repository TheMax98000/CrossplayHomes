# CrossplayHomes

Homes, teleport requests, warps and `/back` for **cross-play servers** (Paper or Folia with Geyser/Floodgate), with
**native Bedrock menus** for Bedrock players and chest menus or clickable chat for Java players.

On a cross-play server, a Bedrock player on a phone struggles to type `/tpa Name` or `/home base`, and cannot click
the `[Accept]` of a chat message: Geyser does not forward chat clicks. With CrossplayHomes, a Bedrock player does
everything from menus, without typing a command argument.

> **Status:** version 1.0.0 is finished and tested, not released yet. Free edition: SpigotMC, CurseForge and
> [Releases](../../releases/latest); full edition: Payhip (links when published).

## Editions

| | Free | Full (9.99 USD) |
|---|---|---|
| Homes | up to 3 per player | unlimited, limit per rank (`crossplayhomes.homes.<n>`) |
| `/tpa`, `/tpahere`, accept, deny, cancel, block a player | yes | yes |
| Bedrock and Java menus | yes | yes |
| Warmup and cooldown | one value for everyone | per rank |
| Warps | no | yes |
| `/back` (last teleport and death) | no | yes |
| EssentialsX import (homes and warps) | no | yes |
| Home and warp icons | no | yes |
| Admin tools (`/homes <player>`, online or not) | no | yes |

## How it looks

- **Bedrock players** (detected by Floodgate) get native Bedrock forms: list of homes, "New home here", rename and
  delete, a three-button form for a teleport request (Accept, Deny, Block this player), the list of online players for
  `/tpa` without a name.
- **Java players** get chest menus (or clickable chat lines, `menus.java: chat`), and `[Accept]` `[Deny]` `[Block]`
  buttons in chat for requests.
- A request that arrives while a Bedrock player has a chest open waits until the chest is closed (Bedrock does not
  show forms over an open inventory), with a chat message saying so.

## Commands

| Command | What it does | Permission (default) |
|---|---|---|
| `/sethome [name]`, `/home [name]`, `/delhome <name>`, `/homes` | homes (`/homes` opens the menu) | `crossplayhomes.home` (everyone) |
| `/tpa [player]`, `/tpahere [player]` | send a request (no name: menu of online players) | `crossplayhomes.tpa`, `crossplayhomes.tpahere` (everyone) |
| `/tpaccept [player]`, `/tpdeny [player]`, `/tpacancel [player]` | answer or cancel a request | `crossplayhomes.tpa` |
| `/tpatoggle` | refuse all requests, and unblock players | `crossplayhomes.tpa` |
| `/warp [name]`, `/warps` | warps (full edition) | `crossplayhomes.warp` (everyone) |
| `/setwarp [name]`, `/delwarp <name>` | create (held item = icon) or delete a warp | `crossplayhomes.setwarp` (op) |
| `/back` | back to where you were before your last teleport, or where you died (full edition) | `crossplayhomes.back`, `crossplayhomes.back.ondeath` (everyone) |
| `/homes <player>` | see, join or delete a player's homes, online or not (full edition) | `crossplayhomes.admin` (op) |
| `/chomes reload`, `/chomes version`, `/chomes import essentials` | admin | `crossplayhomes.admin` (op) |

Other permissions: `crossplayhomes.homes.<number>` and `crossplayhomes.homes.unlimited` (home limit, full edition;
the highest wins), `crossplayhomes.rank.<rank>` (warmup and cooldown of a rank, full edition),
`crossplayhomes.warp.<name>` (when `warps.per-warp-permission: true`), `crossplayhomes.bypass.warmup`,
`crossplayhomes.bypass.cooldown` (op).

## Configuration

`plugins/CrossplayHomes/config.yml`, commented. It works without any change.

```yaml
language: en              # server log, and players whose game language has no text file
update-check: true
homes:
  default-limit: 3        # homes without a crossplayhomes.homes.<number> permission
teleport:
  warmup-seconds: 3       # moving to another block or taking damage cancels it
  cooldown-seconds: 5     # one cooldown for homes, requests, warps and /back
tpa:
  expire-seconds: 60
menus:
  java: chest             # or chat
warps:
  per-warp-permission: false
ranks:                    # full edition: first rank whose crossplayhomes.rank.<rank> the player has
  vip:
    warmup-seconds: 1
    cooldown-seconds: 2
```

Texts: `plugins/CrossplayHomes/lang/en.yml` and `fr.yml`. Each player gets the language of their game when a file
exists; add a language by copying a file (`de.yml`, `es.yml`...).

## Moving from EssentialsX

1. Install CrossplayHomes (full edition) next to EssentialsX and start the server.
2. Run `/chomes import essentials` (console or admin). It reads `plugins/Essentials/userdata/*.yml` (homes) and
   `plugins/Essentials/warps/*.yml` (warps), and never changes EssentialsX's files. A home or warp that already exists
   in CrossplayHomes is kept; the report counts what was imported and what was skipped (already there, world not
   found, unreadable file). You can run it again: nothing is imported twice.
3. EssentialsX also has `/home`, `/sethome`, `/tpa`, `/warp`, `/back`... At startup, CrossplayHomes says in the
   console which commands another plugin owns. Add them to `disabled-commands` in `plugins/Essentials/config.yml`
   and restart. Until then they work as `/crossplayhomes:home`, `/crossplayhomes:tpa`...

If you use a command filter that refuses `/plugin:command` forms (for example
[CrossplayShield](https://github.com/TheMax98000/CrossplayShield) full edition, `command-filter.block-namespaced`),
add `crossplayhomes` to its allowed namespaces (`allowed-namespaces: [crossplayhomes]`) so the
`/crossplayhomes:<command>` forms keep working.

## Requirements

- Paper or Folia 1.21.4 to 26.x, Java 21+ (tested on Paper 1.21.11, Paper 26.3 and Folia 26.3)
- Optional: [Floodgate](https://geysermc.org/download), for the Bedrock menus. Without it everyone gets the Java
  menus.
- No other dependency (no Vault, no database).

## Data

One YAML file per player in `plugins/CrossplayHomes/players/`, loaded when the player joins, written after each
change; warps in `warps.yml`. A file that cannot be read is left untouched and the player's homes are read-only
until it is fixed. An entry that cannot be used (a home without a world, an invalid name) is skipped with a line in
the server log, and the original file is kept next to it as `<file>.bak`.

## Support

Bug reports and questions: [issues](https://github.com/TheMax98000/CrossplayHomes/issues).

## Also by the author

- [CrossplayShield](https://www.spigotmc.org/resources/crossplayshield.139762/): anti-exploit for cross-play servers
  that does not kick Bedrock players.
- [BedrockGuard](https://www.spigotmc.org/resources/bedrockguard-free.139718/): anti-cheat built for Bedrock players.
- [BedrockAssist](https://www.spigotmc.org/resources/bedrockassist.139746/) (free): native Bedrock menus for what
  Geyser cannot translate.
