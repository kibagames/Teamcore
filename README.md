<div align="center">

# ⚔️ TeamCore

**Advanced Team System for Minecraft Paper 1.21.6+**

[![Paper](https://img.shields.io/badge/Paper-1.21.6+-orange?style=for-the-badge&logo=data:image/png;base64,)](https://papermc.io)
[![Java](https://img.shields.io/badge/Java-21-blue?style=for-the-badge&logo=java)](https://adoptium.net)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)
[![Version](https://img.shields.io/badge/Version-1.0.0-purple?style=for-the-badge)](https://github.com/yourname/TeamCore/releases)

*A production-ready, feature-packed team plugin with roles, wars, betrayal tracking, leveling, GUI menus, and a full developer API.*

**Created by [KIBA OFFICIAL](https://github.com/kibagames)**

</div>

---

## 📋 Table of Contents

- [Features](#-features)
- [Requirements](#-requirements)
- [Installation](#-installation)
- [Configuration](#️-configuration)
- [Commands](#-commands)
- [Permissions](#-permissions)
- [Team Roles](#-team-roles)
- [Systems](#-systems)
  - [Unique Team Colors](#unique-team-colors)
  - [Friendly Fire Protection](#friendly-fire-protection)
  - [Team Chat](#team-chat)
  - [Team Invitations](#team-invitations)
  - [Team Home](#team-home)
  - [Ally System](#ally-system)
  - [Rival System](#rival-system)
  - [War System](#war-system)
  - [Betrayal System](#betrayal-system)
  - [Trust & Reputation](#trust--reputation)
  - [Team Leveling](#team-leveling)
  - [Team Vault](#team-vault)
  - [GUI Menus](#gui-menus)
  - [Leaderboards](#leaderboards)
- [Database](#-database)
- [Developer API](#-developer-api)
- [Building from Source](#️-building-from-source)
- [FAQ](#-faq)

---

## ✨ Features

| Feature | Description |
|---------|-------------|
| 🎨 **Unique Colors** | Every team gets an auto-assigned color shown in chat, nametags, and tab list |
| 🛡️ **Friendly Fire** | Blocks all damage types between teammates and allies |
| 💬 **Team Chat** | Toggle or direct-send to team-only chat with `/tc` |
| 📜 **Role Hierarchy** | 5 roles: Owner → Co-Leader → Captain → Member → Recruit |
| 🤝 **Ally System** | Request/accept alliances, shared FF protection |
| ⚔️ **Rival System** | Declare rivals, track rival kills |
| 🔥 **War System** | Declare wars, track kill counters, award XP/trophies on win |
| 🗡️ **Betrayal** | Players who betray get marked with a cooldown and reputation hit |
| 📈 **Team Leveling** | Gain XP through kills, mobs, and activities — unlock member slots, homes, vault rows |
| 🏠 **Team Home** | Set and teleport to a shared team location with combat-tag protection |
| 📦 **Team Vault** | Shared inventory chest that scales with team level |
| 🖥️ **GUI Menus** | Inventory-based GUIs for all management actions |
| 🏆 **Leaderboards** | Top teams by kills, wealth, level, wars, members |
| 🗄️ **Multi-DB** | SQLite (default), MySQL, and MariaDB support with HikariCP pooling |
| 🔌 **Developer API** | 6 custom events + TeamCoreAPI class for addon developers |

---

## 📦 Requirements

| Requirement | Minimum Version |
|-------------|----------------|
| Minecraft Server | **Paper 1.21.6+** (Spigot is **not** supported) |
| Java | **21** |

> ⚠️ **Important:** This plugin uses Paper-specific APIs (`AsyncChatEvent`, `sendActionBar`, Adventure components). It will **not** work on vanilla Spigot.

---

## 🔧 Installation

1. Download `TeamCore-1.0.0.jar` from the [Releases](https://github.com/yourname/TeamCore/releases) page.
2. Drop the jar into your server's `plugins/` folder.
3. Start (or restart) the server — config files are generated automatically.
4. Edit `plugins/TeamCore/config.yml` to configure your database and settings.
5. Reload with `/teamadmin reload` or restart the server.

---

## ⚙️ Configuration

All configuration files are in `plugins/TeamCore/`.

### `config.yml` — Main Settings

```yaml
database:
  type: SQLITE          # SQLITE | MYSQL | MARIADB
  host: localhost
  port: 3306
  name: teamcore
  username: root
  password: secret
  pool-size: 10

teams:
  max-name-length: 16
  min-name-length: 3
  default-max-members: 10
  creation-cost:
    enabled: true
    material: DIAMOND
    amount: 2

friendly-fire:
  enabled: true
  allies-protected: true

home:
  teleport-countdown: 3   # seconds to stand still before teleporting
  combat-tag-duration: 10 # seconds after taking/dealing damage

invitations:
  expiry-seconds: 120

wars:
  require-both-leaders: true
  min-members: 3
  default-duration-hours: 24
  rewards:
    xp-per-kill: 50
    trophies-per-win: 10

betrayal:
  rejoin-cooldown-days: 7
  mark-duration-days: 30

levels:
  xp-per-mob-kill: 10
  xp-per-player-kill: 50
  xp-per-block-mined: 1
```

### `messages.yml` — All Player Messages

Every message is configurable. Use `&` for color codes and `{placeholder}` for variables.

```yaml
prefix: "&8[&bTeamCore&8] "
team-created: "&aTeam &f{team} &ahas been created!"
friendly-fire-blocked: "&cYou cannot attack your teammate."
# ... and many more
```

### Color Pool

By default 20 unique colors are available (CRIMSON, AZURE, EMERALD, GOLD, VIOLET, TEAL, CORAL, INDIGO, AMBER, JADE, ROSE, SLATE, COPPER, AQUA, MAGENTA, LIME, NAVY, BRONZE, SILVER, SCARLET).

You can add or remove colors from the `colors` list in `config.yml`:
```yaml
colors:
  - name: CRIMSON
    chat-code: "&c"
    hex: "#DC143C"
  - name: AZURE
    chat-code: "&9"
    hex: "#007FFF"
  # Add as many as you like
```

> Colors are reserved per active team and freed automatically when a team disbands.

---

## 📜 Commands

### `/team` (alias: `/t`)

Main team command. Opening `/team` or `/team gui` when in a team opens the GUI menu.

| Command | Description | Min Role |
|---------|-------------|----------|
| `/team help` | Show all commands | Anyone |
| `/team create <name>` | Create a new team (costs 2 Diamonds by default) | Anyone |
| `/team disband` | Permanently delete your team | **Owner** |
| `/team invite <player>` | Invite a player to your team | Captain+ |
| `/team accept [team]` | Accept a pending invitation | Anyone |
| `/team deny [team]` | Deny a pending invitation | Anyone |
| `/team leave` | Leave your current team | Member |
| `/team kick <player>` | Kick a member from the team | Captain+ |
| `/team promote <player>` | Promote a member one rank | Co-Leader+ |
| `/team demote <player>` | Demote a member one rank | Co-Leader+ |
| `/team sethome` | Set the team's home location | Captain+ |
| `/team home` | Teleport to the team home (combat-tag check) | Member |
| `/team chat` | Toggle team-only chat mode | Member |
| `/team chat <message>` | Send a single message to team chat | Member |
| `/team description <text>` | Set the team's description | Co-Leader+ |
| `/team motd <text>` | Set the team MOTD (shown on join) | Co-Leader+ |
| `/team info [team]` | View team information | Anyone |
| `/team stats [team]` | Open the statistics GUI | Anyone |
| `/team list` | List all teams | Anyone |
| `/team top [kills\|wealth\|level\|wars\|members]` | View leaderboard | Anyone |
| `/team gui` | Open the team management GUI | Member |

#### Ally Commands

| Command | Description | Min Role |
|---------|-------------|----------|
| `/team ally add <team>` | Send an alliance request to a team | Co-Leader+ |
| `/team ally accept <team>` | Accept an incoming alliance request | Co-Leader+ |
| `/team ally remove <team>` | Break an alliance | Co-Leader+ |
| `/team ally list` | List current allies | Member |

#### Rival Commands

| Command | Description | Min Role |
|---------|-------------|----------|
| `/team rival add <team>` | Declare another team as a rival | Co-Leader+ |
| `/team rival remove <team>` | Remove a rival | Co-Leader+ |
| `/team rival list` | List current rivals | Member |

#### War Commands

| Command | Description | Min Role |
|---------|-------------|----------|
| `/team war declare <team>` | Declare war on another team | Co-Leader+ |
| `/team war accept <team>` | Accept a war declaration | Co-Leader+ |
| `/team war deny <team>` | Deny a war declaration | Co-Leader+ |
| `/team war status` | View active war info & kill counts | Member |

#### Betrayal

| Command | Description |
|---------|-------------|
| `/team betray` | Start betrayal confirmation (30-second window) |
| `/team betray confirm` | Confirm the betrayal |

---

### `/tc <message>`

Shortcut to send a message directly to team chat without toggling.

```
/tc Hello teammates!
```

---

### `/teamadmin` (alias: `/tadmin`)

Admin-only team management commands.

| Command | Description | Permission |
|---------|-------------|-----------|
| `/teamadmin create <name> <owner>` | Force-create a team for a player | `teamcore.admin.create` |
| `/teamadmin delete <team>` | Delete any team | `teamcore.admin.delete` |
| `/teamadmin disband <team>` | Alias for delete | `teamcore.admin.disband` |
| `/teamadmin color <team> <color>` | Change a team's color | `teamcore.admin.color` |
| `/teamadmin stats <team>` | View detailed stats for any team | `teamcore.admin.stats` |
| `/teamadmin reset <team>` | Reset a team's statistics | `teamcore.admin.reset` |
| `/teamadmin bypass` | Toggle admin bypass (skip FF, costs, etc.) | `teamcore.admin.bypass` |
| `/teamadmin reload` | Reload `config.yml` and `messages.yml` | `teamcore.admin.*` |
| `/teamadmin info <team>` | View team details | `teamcore.admin.stats` |
| `/teamadmin list` | List all teams with info | `teamcore.admin.*` |

---

## 🔒 Permissions

### Player Permissions

| Permission | Default | Description |
|-----------|---------|-------------|
| `teamcore.team.*` | `true` | All team permissions |
| `teamcore.team.create` | `true` | Create teams |
| `teamcore.team.join` | `true` | Accept invitations |
| `teamcore.team.leave` | `true` | Leave teams |
| `teamcore.team.invite` | `true` | Invite players |
| `teamcore.team.kick` | `true` | Kick members |
| `teamcore.team.promote` | `true` | Promote members |
| `teamcore.team.demote` | `true` | Demote members |
| `teamcore.team.sethome` | `true` | Set team home |
| `teamcore.team.home` | `true` | Teleport to home |
| `teamcore.team.chat` | `true` | Use team chat |
| `teamcore.team.ally` | `true` | Manage allies |
| `teamcore.team.rival` | `true` | Manage rivals |
| `teamcore.team.war` | `true` | Use war system |
| `teamcore.team.betray` | `true` | Betray a team |
| `teamcore.team.vault` | `true` | Access team vault |
| `teamcore.team.info` | `true` | View team info |
| `teamcore.team.list` | `true` | List all teams |
| `teamcore.team.top` | `true` | View leaderboards |

### Admin Permissions

| Permission | Default | Description |
|-----------|---------|-------------|
| `teamcore.admin.*` | `op` | All admin permissions |
| `teamcore.admin.create` | `op` | Force-create teams |
| `teamcore.admin.delete` | `op` | Delete any team |
| `teamcore.admin.disband` | `op` | Disband any team |
| `teamcore.admin.color` | `op` | Change team colors |
| `teamcore.admin.stats` | `op` | View any team's stats |
| `teamcore.admin.reset` | `op` | Reset team stats |
| `teamcore.admin.bypass` | `op` | Toggle admin bypass |
| `teamcore.nofriendlyfire.bypass` | `op` | Bypass FF protection |

---

## 👑 Team Roles

Roles form a strict hierarchy. Higher roles can manage all roles below them.

```
OWNER      (5) ──── Full control. Cannot leave (must disband). Cannot be kicked.
   │
CO-LEADER  (4) ──── Can promote/demote up to Captain. Can manage allies, rivals, wars.
   │
CAPTAIN    (3) ──── Can invite players, kick Members/Recruits, set team home.
   │
MEMBER     (2) ──── Standard access. Can use vault, home, and team chat.
   │
RECRUIT    (1) ──── New joiners. Limited access (no vault, cannot use /team home by default).
```

- Players join as **Recruit**.
- Each rank above can manage every rank below it.
- The Owner is the only one who can disband the team.
- Ownership transfer is done by promoting another player to Co-Leader, then reassigning via server admin.

---

## 🔧 Systems

### Unique Team Colors

When a team is created, it is automatically assigned the next available color from a pool of 20 (configurable). The color appears:
- As a **chat prefix**: `[CRIMSON] Steve: Hello!`
- **Above player nametags** (via Scoreboard API)
- In the **tab list**
- In all **team messages**

No two active teams share a color. When a team is deleted, its color is freed and can be assigned to a new team.

**Example:**
```
[CRIMSON] KIBA
[AZURE] Steve
[EMERALD] Alex
```

---

### Friendly Fire Protection

Team members **cannot damage each other**. The following damage sources are all blocked:

| Blocked Source | Notes |
|---------------|-------|
| Melee attacks | All weapons |
| Arrows & projectiles | Bow, crossbow, snowball |
| Tridents | Thrown or riptide |
| TNT | Primed by a teammate |
| Fireballs | Ghast or blaze fireballs caused by player |
| Potions | Splash and lingering |
| Area Effect Clouds | From potions or lingering effects |
| Wolves / Tamed Pets | Tamed animal attacks |

When an attack is blocked, the attacker sees an **actionbar message**: `You cannot attack your teammate.`

> 💡 **Allies** are also protected by default (configurable with `friendly-fire.allies-protected`).
> 💡 **Wars** override friendly fire — attacking war enemies is always allowed.
> 💡 **Admin bypass** (`/teamadmin bypass`) lets admins deal damage regardless.

---

### Team Chat

- `/team chat` — Toggle team-chat mode. While enabled, **all** messages go to team chat only.
- `/team chat <message>` — Send a single message without toggling.
- `/tc <message>` — Quick shortcut, same as above.

**Chat format:**
```
[TEAM] Steve: Hello everyone!
```
The prefix color matches the team's unique color.

---

### Team Invitations

1. A Captain+ runs `/team invite <player>`.
2. The target receives a notification with instructions.
3. Target runs `/team accept` or `/team deny` within the expiry window (default 120 seconds).
4. Invitations are saved to the database so **offline players** can see them when they next log in.
5. Maximum pending invitations per player is configurable.

**Betrayal block:** If a player has betrayed a team, they cannot be re-invited for the cooldown period.

---

### Team Home

- `/team sethome` — Sets the team's home to your current location (Captain+ only).
- `/team home` — Teleports you to the team home.

**Rules:**
- **Countdown**: You must stand still for N seconds (configurable, default 3) before teleporting.
- **Combat tag**: If you've dealt or received damage recently (default 10s window), teleportation is blocked.
- Moving during the countdown cancels it with a message.

---

### Ally System

Teams can form alliances. Allies are protected from each other's friendly fire (configurable).

| Step | Command | Who |
|------|---------|-----|
| 1 | `/team ally add <team>` | Your Co-Leader+ |
| 2 | `/team ally accept <team>` | Their Co-Leader+ |
| Alliance formed | — | — |

To break an alliance: `/team ally remove <team>` (one side can break it unilaterally).

---

### Rival System

Declaring a team as a rival is one-sided — you don't need their consent.

- `/team rival add <team>` — Declare a team as your rival.
- `/team rival remove <team>` — End the rivalry.
- Rival kills are tracked in team statistics.

---

### War System

Wars are mutual PvP engagements between two teams with tracked kill scores and time limits.

**Starting a war:**
1. Co-Leader+ of Team A: `/team war declare TeamB`
2. Co-Leader+ of Team B: `/team war accept TeamA`
3. War starts — kill counters begin, friendly fire between the two teams is **enabled**.
4. War ends when the timer expires (default 24 hours). The team with more kills wins.

**During war:**
- Kill announcements broadcast to both teams.
- `/team war status` shows current kill counts and time remaining.

**War rewards (for the winner):**
- Team XP
- Trophies (added to team wealth)
- Win recorded in team statistics

**Requirements (configurable):**
- Both teams must have a minimum number of members (default 3).

---

### Betrayal System

A dramatic mechanic allowing players to abandon their team and face consequences.

**Steps:**
1. Run `/team betray` — receive a confirmation prompt.
2. Run `/team betray confirm` within 30 seconds.
3. Player is removed from the team. The entire team is notified.

**Betrayer effects:**
- 🔴 Marked as **Betrayer** for a configurable number of days (default 30).
- ⏳ **Cooldown** prevents rejoining the betrayed team for N days (default 7).
- 📉 **Trust level** drops to Betrayer.
- 🔔 All online team members receive a notification.

---

### Trust & Reputation

Every player has a **Trust Level** based on their betrayal history:

| Trust Level | Condition | Color |
|-------------|-----------|-------|
| 🟢 **Loyal** | 0 betrayals | Green |
| 🟦 **Trusted** | Long membership, good history | Dark Green |
| ⬜ **Neutral** | Default | Gray |
| 🟡 **Suspicious** | 1 betrayal | Yellow |
| 🔴 **Betrayer** | Active betrayer mark | Dark Red |

Trust levels are visible in `/team info` and the members GUI.

---

### Team Leveling

Teams earn XP and level up from **Level 1 to 100**.

**XP sources:**

| Action | XP Gained (default) |
|--------|-------------------|
| Kill a mob | 10 |
| Kill a player | 50 |
| Mine a block | 1 |
| Harvest a crop | 2 |

**Level unlocks:**

| Milestone | Unlock |
|-----------|--------|
| Every 10 levels | +1 member slot |
| Every 20 levels | +1 home slot |
| Every 25 levels | +1 vault row (max 6) |

The XP required per level scales with level: `level × 100 XP`.

---

### Team Vault

A shared inventory accessible to all Members (Recruit rank is excluded by default).

- Opens via `/team gui` → Team Vault, or directly from the GUI.
- **Rows** start at 1 and increase with team level (up to 6 = full double chest).
- **Locked during active wars** (configurable).
- Contents persist across sessions.

---

### GUI Menus

All menus are accessible via `/team gui` or `/team menu`.

#### Main Team GUI (`/team gui`)
Shows team info, color, level, member count, allies, rivals, and quick-action buttons:
- 👥 Open Members GUI
- 📊 Open Stats GUI
- 🤝 View Allies
- ⚔️ View Rivals
- 🏠 Set/Teleport Home
- 📦 Open Team Vault
- 💬 Toggle Team Chat
- 🚪 Leave Team / Disband (Owner)

#### Members GUI
- See all members with their role, online status, kills/deaths/KDR
- **Left-click** a member → Promote (Captain+ only)
- **Right-click** a member → Demote (Captain+ only)
- **Shift-click** a member → Kick (Captain+ only)

#### Statistics GUI
- Combat stats (kills, deaths, KDR, wars won/lost)
- Member stats (joined/left/betrayals)
- Progression stats (level, XP, age, wealth)

#### Team Vault
- Shared inventory GUI
- Rows scale with team level
- No-theft protection: only Members+ can access

---

### Leaderboards

`/team top [category]` — view the top 10 teams by:

| Category | Description |
|----------|-------------|
| `kills` (default) | Total team kills |
| `wealth` | Total team wealth (money + trophies) |
| `level` | Team level |
| `wars` | Wars won |
| `members` | Current member count |

---

## 🗄️ Database

TeamCore supports three database backends:

| Backend | Config value | Notes |
|---------|-------------|-------|
| SQLite | `SQLITE` | Default. No setup required. File: `plugins/TeamCore/teamcore.db` |
| MySQL | `MYSQL` | Requires a MySQL 8+ server |
| MariaDB | `MARIADB` | Requires a MariaDB 10.5+ server |

**Connection pooling:** All backends use **HikariCP** for efficient connection management.

**All DB operations are asynchronous** — database writes never block the main server thread.

### Schema (auto-created)

| Table | Contents |
|-------|---------|
| `teams` | Team data: name, color, owner, description, level, XP, home, stats |
| `team_members` | Per-member data: role, kills, deaths, join date, last seen |
| `team_allies` | Ally relationships (bidirectional) |
| `team_rivals` | Rival relationships (one-directional) |
| `player_data` | Per-player: betrayal count, trust level, cooldowns, history |
| `invitations` | Persistent invitations for offline players |

---

## 🔌 Developer API

### Setup

Add TeamCore to your `plugin.yml`:
```yaml
depend:
  - TeamCore
# or softdepend if optional:
softdepend:
  - TeamCore
```

### Getting the API

```java
TeamCoreAPI api = TeamCoreAPI.getInstance();
```

### Common Usage

```java
// Get the team a player is in
Optional<Team> team = api.getPlayerTeam(player.getUniqueId());

team.ifPresent(t -> {
    String name = t.getName();
    int level   = t.getLevel();
    int members = t.getMemberCount();
});

// Get a team by name
Optional<Team> namedTeam = api.getTeam("Warriors");

// Relationship checks
boolean isTeammate = api.areTeammates(uuid1, uuid2);
boolean isAllied   = api.areAllied(uuid1, uuid2);
boolean atWar      = api.areAtWar(uuid1, uuid2);

// Player reputation
boolean isBetrayer = api.isBetrayer(uuid);
boolean inCombat   = api.isInCombat(uuid);

// Get all teams
Collection<Team> allTeams = api.getAllTeams();
```

### Custom Events

Listen to TeamCore events in your plugin:

```java
@EventHandler
public void onTeamCreate(TeamCreateEvent event) {
    Team team    = event.getTeam();
    Player owner = event.getCreator();
    // Cancel if you want to prevent creation:
    // event.setCancelled(true);
    Bukkit.broadcastMessage("New team created: " + team.getName());
}

@EventHandler
public void onTeamJoin(TeamJoinEvent event) {
    Team team       = event.getTeam();
    TeamMember member = event.getMember();
    Player player   = event.getPlayer();
}

@EventHandler
public void onTeamLeave(TeamLeaveEvent event) {
    TeamLeaveEvent.Reason reason = event.getReason();
    // LEAVE | KICK | BETRAYAL | DISBAND
}

@EventHandler
public void onTeamBetray(TeamBetrayEvent event) {
    Team team        = event.getTeam();
    Player betrayer  = event.getBetrayerPlayer();
}

@EventHandler
public void onWarStart(TeamWarStartEvent event) {
    TeamWar war   = event.getWar();
    Team challenger = event.getChallenger();
    Team defender   = event.getDefender();
    // event.setCancelled(true); // to cancel war start
}

@EventHandler
public void onWarEnd(TeamWarEndEvent event) {
    Team winner = event.getWinner(); // null if draw
    boolean draw = event.isDraw();
}
```

### Full Event Reference

| Event | Cancellable | When fired |
|-------|-------------|-----------|
| `TeamCreateEvent` | ✅ Yes | A new team is created |
| `TeamJoinEvent` | ✅ Yes | A player joins a team via invitation |
| `TeamLeaveEvent` | ❌ No | A player leaves, is kicked, or is disbanded |
| `TeamBetrayEvent` | ❌ No | A player betrays their team |
| `TeamWarStartEvent` | ✅ Yes | Both leaders have agreed to a war |
| `TeamWarEndEvent` | ❌ No | A war timer expires or is ended |

---

## 🏗️ Building from Source

### Prerequisites

- Java 21
- Maven 3.8+
- Git

### Steps

```bash
# Clone the repository
git clone https://github.com/yourname/TeamCore.git
cd TeamCore

# Build the shaded JAR (all dependencies bundled)
mvn clean package -DskipTests

# Output
ls target/TeamCore-1.0.0.jar
```

The build uses the **Maven Shade Plugin** to bundle SQLite JDBC, MySQL Connector, and HikariCP into a single fat JAR with relocated package paths to avoid conflicts with other plugins.

### Project Structure

```
TeamCore/
├── pom.xml
└── src/main/
    ├── resources/
    │   ├── plugin.yml       Commands + permission declarations
    │   ├── config.yml       All plugin settings
    │   └── messages.yml     All player-facing messages
    └── java/dev/teamcore/
        ├── TeamCore.java           Main plugin class
        ├── api/
        │   ├── TeamCoreAPI.java    Public developer API
        │   └── events/             6 Bukkit events
        ├── models/                 Data models (Team, Member, War, etc.)
        ├── managers/               Business logic managers
        ├── database/               DB interface + SQLite + MySQL implementations
        ├── commands/               /team, /tc, /teamadmin
        ├── gui/                    Inventory GUIs
        ├── listeners/              Event listeners
        └── utils/                  Chat, item, time utilities
```

---

## ❓ FAQ

**Q: Does this work with Spigot?**
> No. TeamCore uses Paper-only APIs (`AsyncChatEvent`, Adventure components, etc.). Paper 1.21.6+ is required.

**Q: Can I use MySQL instead of SQLite?**
> Yes. Set `database.type: MYSQL` in `config.yml` and fill in your connection details.

**Q: How many teams / players does it support?**
> TeamCore is designed for 500+ concurrent players. All DB writes are async and the in-memory cache is efficient.

**Q: How do I change the team creation cost?**
> Edit `teams.creation-cost` in `config.yml`. Set `enabled: false` to remove the cost entirely.

**Q: Can I add more colors?**
> Yes — add entries to the `colors` list in `config.yml`. Any `Material` color code works.

**Q: How do I give players a team without the cost?**
> Use `/teamadmin create <name> <player>`. Admin-created teams skip the cost.

**Q: A player is stuck with the Betrayer mark — how do I clear it?**
> Currently via database directly. A `/teamadmin clearmark <player>` command will be added in a future release.

**Q: The vault contents disappeared after a restart.**
> Vault contents are stored in memory in v1.0.0. A persistent vault (DB/file storage) is planned for v1.1.0. For now, treat the vault as session-only.

---

## 📝 Planned for Future Releases

- [ ] Persistent vault storage (DB/NBT file)
- [ ] `/teamadmin clearmark <player>` — remove betrayer mark
- [ ] Hologram leaderboards (using Display Entities)
- [ ] PlaceholderAPI support (`%teamcore_team%`, `%teamcore_role%`, etc.)
- [ ] Team quests / challenges
- [ ] Economy integration (Vault/CMI)
- [ ] Team chat spy for admins
- [ ] Configurable war-end conditions (e.g. flag capture)

---

## 🤝 Contributing

1. Fork the repository
2. Create your feature branch: `git checkout -b feature/my-feature`
3. Commit your changes: `git commit -m 'Add my feature'`
4. Push to the branch: `git push origin feature/my-feature`
5. Open a Pull Request

---

## 📄 License

```
MIT License

Copyright (c) 2024 KIBA OFFICIAL

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
```

---

<div align="center">

Made with ❤️ for the Minecraft community

**[⭐ Star this repo](https://github.com/yourname/TeamCore)** — it helps other server owners find it!

</div>
