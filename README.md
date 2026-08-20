<!-- Goes in a repo named exactly `banyourself`, as README.md at the root. GitHub renders it on your profile. -->
<!-- Banner is built by tools/build_mc_banner.py in the banyourself.github.io repo.
     No width attribute on purpose: it renders 1:1 at 876px so the pixels stay sharp. -->

<img src="https://banyourself.github.io/assets/img/mc-banner.gif" alt="Kevin Le, Security Operations and Cloud Security">

```
  SUBJECT .............. Le, Kevin · @banyourself
  LOCATION ............. Westminster, California
  EDUCATION ............ Coastline College, A.S. Cybersecurity, class of 2027 · GPA 3.47
  CREDENTIALS .......... CompTIA ×4 · Microsoft ×2 · & MORE · 2 in progress
  STATUS ............... Seeking 2027 Internship for Cyber · IT · Cloud/Network Security · & MORE
```

It's fun looking for vulnerabilities in the games I play everyday just because
it'll give me a challenge. I like to find flaws, check how it could be exploited
and the impact it could cause, create a patch, and then report the vulnerability
to put my name out there!

<p>
  <a href="https://banyourself.github.io"><img src="https://img.shields.io/badge/PORTFOLIO-9c2b21?style=for-the-badge" alt="Portfolio"></a>
  <a href="https://www.linkedin.com/in/kevin-le-cyber"><img src="https://img.shields.io/badge/LINKEDIN-0A66C2?style=for-the-badge" alt="LinkedIn"></a>
  <a href="https://www.credly.com/users/kevin-le-cyber"><img src="https://img.shields.io/badge/CREDLY-FF6B00?style=for-the-badge&logo=credly&logoColor=white" alt="Credly"></a>
  <a href="mailto:publicusekevin@gmail.com"><img src="https://img.shields.io/badge/GMAIL-EA4335?style=for-the-badge&logo=gmail&logoColor=white" alt="Gmail"></a>
  <a href="https://banyourself.github.io/assets/Kevin_Le_Resume.pdf"><img src="https://img.shields.io/badge/RESUME-9c2b21?style=for-the-badge&logo=adobeacrobatreader&logoColor=white" alt="Resume (PDF)"></a>
</p>

---

## <img src="https://banyourself.github.io/assets/img/enchanted-book.gif" align="absmiddle" alt=""> CASE FILES

Four bodies of work. Each one written up properly: what I found, why it happened,
how to fix it, and who I told. Full versions live on the
**[portfolio](https://banyourself.github.io)**.

<details>
<summary><b>CASE FILE 001 &nbsp;·&nbsp; Modded Minecraft - Missing Packet Authorization</b> &nbsp;<code>vuln research</code></summary>

<br>

Decompiled 400+ Forge mods that other mods depend on, or that ship inside
shipped inside modpacks totalling 50M+ downloads on CurseForge, Modrinth,
and other modded minecraft loaders. Through my own analysis, I was able to discover
numerous packets with no permission gate that nobody had reported, which might be
a simple overlook but it can be severely exploited to cause chaos by users with
malicious intent.

Every one of these is the same weakness, **CWE-862 Missing Authorization**: a handler
registered on `Side.SERVER` that acts on whatever the client sent without checking whether
the sender is allowed to. What changes between them is the severity, and that is graded on
what the packet actually lets you do.

| Severity | Mods | Packets | What it means | Status |
|:--|:--|:--|:--|:--|
| **Critical** | 4 | 32 | Wipes a whole dimension, or reaches level-2 command execution | Reported, fix committed |
| High | 22 | 71 | Changes any entity or tile by ID, arbitrary teleport, or attack with no reach check | Reported, fix committed |
| Medium | 13 | 44 | Self-contained or read-only, but the gate is still missing | Reported, fix committed |
| Low | 25 | 64 | `Side.CLIENT` so a client cannot send it, a no-op handler, or self-only | Reported, fix committed |

The low tier matters as much as the top one. Most of those turned out to be registered
server-to-client, which means a client cannot forge them at all, and calling those
vulnerabilities would have been wrong.

**Method:** decompile with Vineflower and [CFR](https://github.com/leibnitz27/cfr), find the network registration
(`SimpleNetworkWrapper#registerMessage`), trace every server-bound handler, then
check whether it validates the sender before doing anything that matters. Most
did. The interesting ones didn't.

**Scope:** all testing against my own local servers. Reported privately to
maintainers before publishing anything.

→ [Read the full writeups](https://banyourself.github.io/#/case/MC-001)

</details>

<details>
<summary><b>CASE FILE 002 &nbsp;·&nbsp; CS:GO Server Plugins - Anti-Cheat, Anti-VPN, Gamemodes</b> &nbsp;<code>defensive tooling</code></summary>

<br>

Fourteen SourcePawn plugins and one C++ Metamod extension for the CS:GO servers I run.
Roughly 30,000 lines of SourcePawn plus 800 of C++. Grouped below so you can open only
the part you care about.

<details>
<summary><b>Anti-Cheat</b> &nbsp;<code>2 projects</code></summary>

<br>

**KevAC** · SourcePawn · ~7,400 lines · mine

Server-sided anticheat, about 45 detectors across movement, aim, command cadence and
cvar state. Client-side anticheat trusts the machine you're trying to catch;
server-side only trusts what the server can observe, so it's behavioral detection
against a noisy signal. Same job as writing SIEM rules with a different hat on.

The detector I like best is the cheat-cvar probe: a legitimate client physically cannot
change an `FCVAR_CHEAT` cvar while the server has `sv_cheats 0`, so a mismatch means
patched cvar protection. Zero false positives, which is rare when everything else is
statistical.

Detections write to a ban queue rather than banning live, so there's a review step.
Banning one innocent regular costs more than missing one cheater.

**KevAC Extension** · C++ · ~800 lines · mine

In Source, the client tells the server which network events it wants, and you can't
touch that list from SourcePawn at all. Injected DLLs register extra listeners there,
which makes it the cleanest catch in the project: not a threshold, just a list that
shouldn't have that entry. So this half is a Metamod extension that detours
`ListenEvents`, built with AMBuild and safetyhook.

</details>

<details>
<summary><b>Network Defense</b> &nbsp;<code>1 project</code></summary>

<br>

**KevVPN** · SourcePawn · ~2,200 lines · mine

Two layers, cheapest first. Static CIDR ranges for datacenter and hosting ASNs held in
RAM, so no network call per connect, then a reputation API for what the ranges miss,
with results cached in SQL.

The decision worth asking me about: what happens when the lookup fails. I fail open,
and specifically, if the database is unreachable nobody gets punished, because an
unreachable database means the whitelist never loaded and punishing then kicks the exact
people who were explicitly exempted.

Mobile carrier ranges are recorded and never acted on: a cellular address proves nothing
and blocking it hits every 5G player. Cloudflare WARP and GeForce Now each needed
handling, because their egress reads as datacenter, so an admin allowlist gets the last
word over every feed.

</details>

<details>
<summary><b>Gamemodes</b> &nbsp;<code>4 projects</code></summary>

<br>

**hnsmix** · SourcePawn · ~11,800 lines · mine

Captain-based ranked matches, 1v1 to 10v10, with Elo persisted in SQL and live Discord
embeds through REST in Pawn. The status card edits one existing message instead of
posting a new one, which means storing a message id and having a way out when that
message gets deleted.

**hnsova** · SourcePawn · ~4,200 lines · fork of ceLoFaN's hidenseek

I added One Versus All: one T against everybody, and whoever lands the stab becomes the
new T where they stand. The in-place handover is the whole feel of the mode, so it uses
`CS_SwitchTeam` rather than a respawn, which means refreshing the player model by hand.
Stats and a Discord leaderboard in SQL, with a schema migration for tables made before
the stab columns existed.

**KevFJ** · SourcePawn · ~870 lines · fork of hiiamu's amuFJ

Funjump practice mode, voted on by players. The tricky bit is that three plugins
all want to own `mp_roundtime`, so this one hooks `round_start` as Post specifically
because hnsmix hooks it as Pre and always runs first.

**antifrag** · SourcePawn · ~620 lines · fork of oqyh's HNS Anti Frag

Knife damage cap. Upstream compensated for kevlar's 85% reduction with an
eighteen-branch if/else ladder of hand-tuned constants; I replaced it with the actual
calculation. Also hooked `OnTakeDamageAlive` so the engine's backstab bonus and
third-party perk plugins can't push a stab back over the cap.

</details>

<details>
<summary><b>Movement</b> &nbsp;<code>4 projects</code></summary>

<br>

**gstrafe** · SourcePawn · ~290 lines · fork of zwolof's EFRAG GStrafe

The original speed modifier was two-state, so speed sawtoothed around 400 forever
instead of settling. Mine multiplies by a gain and clamps to a max, so a boost lands
exactly on the cap. It also calls my own anticheat's `KevAC_IgnoreMovement` on the ticks
where it teleports someone, because otherwise the movement plugin trips the detector.

**MovementTweaker** · SourcePawn · ~690 lines · fork of danzayau's

Prestrafe and air acceleration tuning, with a ground cap that tracks the live prestrafe
modifier instead of pinning everyone at one number. Also tracked down constant
`DataTable` out-of-range spam: the engine packs snapshots straight from entity memory at
end of frame, so the fix was clamping the stored value in post-think.

**csgo_movement_unlocker** · SourcePawn · 85 lines · Peace-Maker's, my syntax pass

Finds `CGameMovement::WalkMove` by byte signature and NOPs the speed clamp. Not my work,
I only brought it forward to modern SourcePawn. It's here because it's the reason
MovementTweaker has to enforce its own cap.

**movementhud** · SourcePawn · 14 files · fork of Sikarii's MovementHUD

Speed and key-press readouts. Reading how it allocates HUD channels is what told me the
right fix for the spectator-list flicker was a synchronizer, not a hard-coded channel.

</details>

<details>
<summary><b>Quality of Life</b> &nbsp;<code>3 projects</code></summary>

<br>

**hextags** · SourcePawn · ~1,470 lines · fork of Hexer10's HexTags

Players kept losing their chosen tag after a reconnect. The saved selection was stored
as a KeyValues section symbol, and that symbol isn't stable when the config has
duplicate selector names, which every real config does. Fixed by storing the tag name,
with the old cookie read once for migration. Also added an external prefix API so
hnsmix can put a rank tag on the scoreboard without the two plugins fighting over it.

**speclist** · SourcePawn · ~510 lines · fork of MandoCSGO's Spectator-List

210 lines upstream, 510 here, almost all of it chasing one flicker with two causes: a
`-1` channel that made the engine pick a new slot every send, and the fact that
replacing a HUD message blanks its channel for a frame, so a slower refresh made it
worse rather than better. Fix was a channel synchronizer plus splitting rebuild from
draw.

**EasySpawnProtection** · SourcePawn · ~450 lines · fork of Invex and Byte's

Spawn protection, extended so One Versus All can hand the T role over mid-round without
handing the new T a few seconds of invulnerability. The exception went in the shared
function rather than the two call sites that looked like they needed it, because
guarding only `player_spawn` left the round-start loop still granting protection.

</details>

<br>

`Anti-Cheat` · `Anti-VPN` · `Custom Gamemodes` · `C++ / Metamod` · `SQL`

Every fork is published with credits to the original author to the best of my abilities. I run every single one of these SourcePawn plugins on two CS:GO servers (NA/EU), and I help manage an active CS2 network at [edan.gg](https://edan.gg/).

→ [Read the full writeups](https://banyourself.github.io/#/case/CS-002)

</details>

<details>
<summary><b>CASE FILE 003 &nbsp;·&nbsp; Minecraft & CS:GO Servers - Config and Network Management</b> &nbsp;<code>infrastructure</code></summary>

<br>

Running public game servers is a sysadmin job wearing a hoodie, and the two games are
genuinely different jobs. Minecraft was one box I owned end to end. CS:GO is two rented
servers on two continents.

<details>
<summary><b>Minecraft server</b> &nbsp;<code>owned end to end</code></summary>

<br>

**Networking.** The server sat on its own subdomain rather than a bare IP, and behind that
name connections were split across three proxies running on Proxmox. Players hold a name, I
hold the topology, and the two change independently. DDoS protection ran through Cloudflare,
with the caveat worth stating out loud: their edge is built for HTTP, and Minecraft is a TCP
protocol on a non-web port, so it is not the same "just proxy it" story as a website.

Minecraft also needs a record type nobody else meets. A `_minecraft._tcp` SRV record hands
the client both the target host and the port, so people connect with a bare domain even
though the server is not on 25565.

**Runtime.** Forge running RLCraft Dregora v1.1.2b on Temurin JDK 8, headless in tmux. Java 8
is not nostalgia, it is what Forge for that version supports, and a heavily modded pack is the
least forgiving place to go off the supported runtime. A Minecraft server is a long-running
JVM, so its performance problems are JVM problems: heap size and GC pauses are what a stutter
usually is, and more heap makes pauses longer, not shorter.

**Admin access.** rcon bound to localhost and reached over SSH, with an IP allowlist on SSH
itself. rcon then inherits SSH's key auth instead of relying on its own cleartext password,
and the same allowlist covers the friends who needed FileZilla. One door with real auth on it
instead of several with weak auth.

**Operations.** A 3AM restart on cron, and the box rebooted itself on the same schedule.
Restarting nightly is the pragmatic answer to a modded server's memory creeping up: chase a
leak through a hundred mods you did not write, or restart when nobody is online. The honest
gap is that tmux does not restart a crashed process the way a systemd unit with
`Restart=on-failure` would.

</details>

<details>
<summary><b>CS:GO servers</b> &nbsp;<code>NA + EU</code></summary>

<br>

**Hosting.** NA on NFOservers, EU on dathost. rcon has its own password, but I do not drive
the servers through it day to day: both hosts give you a web console, so the normal admin path
is an authenticated dashboard. That is the real win, because when the console you reach for
every day already sits behind a proper login, rcon stops being the thing you leave open for
convenience.

**FastDL.** EU serves it from dathost alongside the game server, NA from Cloudflare R2. R2 is
the better shape: object storage behind a CDN, so a map pack landing on twenty joining players
is bandwidth Cloudflare eats instead of bandwidth competing with tickrate. It also has no
egress fee, which for a pure-outbound workload is the whole argument. Worth knowing that
anything under `sv_downloadurl` is public and its paths are derivable, so what gets synced
there is an access-control decision, not a deployment detail.

**Config.** A Source server reads config from several places in a fixed order and the last
writer wins: `server.cfg`, then gamemode and map configs, then anything a plugin writes into
`cfg/sourcemod/`. That ordering is why my movement plugin re-applies its cvar on
`OnConfigsExecuted` rather than `OnMapStart`.

**Two databases, on purpose.** NA uses SQLite local to the game server, EU points at
MySQL on NFOservers web hosting. Same plugins, same schema, two different engines, which only
works because everything goes through SourceMod's database layer. `databases.cfg` makes
sharing trivial and I chose not to: one database would be a single point of failure for both
regions, cross-continent queries land mid-tick, and an Elo pool mixing two populations with
different ping is not really one ladder.

**Admins and secrets.** `admins.cfg` with two groups, Owner on root and Admin on a hand-picked
flag set. Root is a blast radius, not a seniority label. Credentials (rcon, database user,
proxycheck.io key, Discord webhooks) are all `FCVAR_PROTECTED` convars with empty defaults,
never in source. Backups are daily from both hosts, which on NA sweeps up the SQLite file for
free.

</details>

<br>

`Network Configuration` · `DNS & Domains` · `Load Balancing` · `Performance Configuration` · `Proxmox` · `Cloudflare`

→ [Read the full writeups](https://banyourself.github.io/#/case/SRV-003)

</details>

<details>
<summary><b>CASE FILE 004 &nbsp;·&nbsp; Home Security Lab - Build & Detection Log</b> &nbsp;<code>blue team</code></summary>

<br>

A segmented lab where I run attacks against myself and then try to catch them in
the logs. I keep a detection log with three columns: what I ran, what fired, and
**what didn't fire and why**. The third column is the one I actually learn from.

`Proxmox` · `pfSense` · `Suricata` · `Windows Server` · `Kali`

→ [Topology and detection log](https://banyourself.github.io/#/case/LAB-004)

</details>

<details>
<summary><b>CASE FILE 005 &nbsp;·&nbsp; Network-Wide DNS Filtering - Pi-hole on Raspberry Pi</b> &nbsp;<code>blue team</code></summary>

<br>

DNS sinkhole for the whole house. Started as an ad-blocker, turned into a lesson
in how much a network says when nobody is listening, and in treating DNS as a
detection surface rather than a convenience.

Also documented honestly: what it does **not** catch. DoH walks straight past it.

`Raspberry Pi` · `Pi-hole` · `Unbound` · `DNS`

→ [What the logs showed](https://banyourself.github.io/#/case/DNS-005)

</details>

---

## <img src="https://banyourself.github.io/assets/img/enchanted-book.gif" align="absmiddle" alt=""> CREDENTIALS

<!-- LinkedIn carries more than this. Nothing here should contradict it. -->

```
  EARNED

    CompTIA
      CS0-003  CySA+ .......................................................... May 2026
      SK0-005  Server+ ........................................................ May 2026
      N10-009  Network+ ....................................................... Dec 2025
      SY0-701  Security+ ...................................................... Aug 2025

    Microsoft
      SC-500   Cloud and AI Security Engineer ................................. Aug 2026
      SC-200   Security Operations Analyst .................................... May 2026

  IN PROGRESS

      SC-100   Microsoft Cybersecurity Architect
      CISSP    ISC2 Certified Information Systems Security Professional
```

Exam codes are included and the issue date as well. All CompTIA badges verifiable on
**[Credly](https://www.credly.com/users/kevin-le-cyber)**, the full list of certificates
are on **[LinkedIn](https://www.linkedin.com/in/kevin-le-cyber/details/certifications/)**
with a picture of certificate attached to each one as authentication.

---

## <img src="https://banyourself.github.io/assets/img/enchanted-book.gif" align="absmiddle" alt=""> ARSENAL

Color is the grade, not decoration. Green means I built or broke something real with
it, amber means I'm mid-way and would still reach for docs, grey means I've started and
that's all. No badge here is aspirational.

**DETECTION & MONITORING**

<p>
  <img src="https://img.shields.io/badge/Detection_engineering-3d6349?style=for-the-badge" alt="Detection engineering">
  <img src="https://img.shields.io/badge/False--positive_tuning-3d6349?style=for-the-badge" alt="False-positive tuning">
  <img src="https://img.shields.io/badge/Threat_intelligence_feeds-3d6349?style=for-the-badge" alt="Threat intelligence feeds">
  <img src="https://img.shields.io/badge/IP_reputation_%26_enrichment-3d6349?style=for-the-badge" alt="IP reputation & enrichment">
  <img src="https://img.shields.io/badge/Log_analysis-3d6349?style=for-the-badge" alt="Log analysis">
  <img src="https://img.shields.io/badge/Pi--hole_/_DNS_sinkholing-3d6349?style=for-the-badge&logo=pihole&logoColor=white" alt="Pi-hole / DNS sinkholing">
  <img src="https://img.shields.io/badge/Wireshark_/_tcpdump-3d6349?style=for-the-badge&logo=wireshark&logoColor=white" alt="Wireshark / tcpdump">
  <img src="https://img.shields.io/badge/Microsoft_Sentinel-87701d?style=for-the-badge" alt="Microsoft Sentinel">
  <img src="https://img.shields.io/badge/Microsoft_Defender_XDR-87701d?style=for-the-badge" alt="Microsoft Defender XDR">
  <img src="https://img.shields.io/badge/SIEM_%28Splunk%2C_QRadar%29-87701d?style=for-the-badge&logo=splunk&logoColor=white" alt="SIEM (Splunk, QRadar)">
  <img src="https://img.shields.io/badge/Suricata_/_Snort-87701d?style=for-the-badge" alt="Suricata / Snort">
  <img src="https://img.shields.io/badge/Incident_response-87701d?style=for-the-badge" alt="Incident response">
  <img src="https://img.shields.io/badge/MITRE_ATT%26CK-87701d?style=for-the-badge" alt="MITRE ATT&CK">
  <img src="https://img.shields.io/badge/EDR_%28Defender%2C_CrowdStrike%29-87701d?style=for-the-badge" alt="EDR (Defender, CrowdStrike)">
</p>

**CLOUD & IDENTITY**

<p>
  <img src="https://img.shields.io/badge/IAM_/_SSO_/_OAuth_2.0-3d6349?style=for-the-badge" alt="IAM / SSO / OAuth 2.0">
  <img src="https://img.shields.io/badge/MFA_rollout-3d6349?style=for-the-badge" alt="MFA rollout">
  <img src="https://img.shields.io/badge/Least_privilege_/_RBAC-3d6349?style=for-the-badge" alt="Least privilege / RBAC">
  <img src="https://img.shields.io/badge/SSH_hardening_%26_allowlists-3d6349?style=for-the-badge&logo=openssh&logoColor=white" alt="SSH hardening & allowlists">
  <img src="https://img.shields.io/badge/Microsoft_Azure-87701d?style=for-the-badge" alt="Microsoft Azure">
  <img src="https://img.shields.io/badge/Microsoft_Entra_ID-87701d?style=for-the-badge" alt="Microsoft Entra ID">
  <img src="https://img.shields.io/badge/Defender_for_Cloud-87701d?style=for-the-badge" alt="Defender for Cloud">
  <img src="https://img.shields.io/badge/AWS_%28IAM%2C_EC2%29-87701d?style=for-the-badge&logo=amazonwebservices&logoColor=white" alt="AWS (IAM, EC2)">
</p>

**INFRASTRUCTURE**

<p>
  <img src="https://img.shields.io/badge/Linux_administration-3d6349?style=for-the-badge&logo=linux&logoColor=white" alt="Linux administration">
  <img src="https://img.shields.io/badge/Windows_Server_/_Active_Directory-3d6349?style=for-the-badge" alt="Windows Server / Active Directory">
  <img src="https://img.shields.io/badge/Proxmox_/_VM_labs-3d6349?style=for-the-badge&logo=proxmox&logoColor=white" alt="Proxmox / VM labs">
  <img src="https://img.shields.io/badge/VirtualBox_/_Kali_Linux-3d6349?style=for-the-badge&logo=kalilinux&logoColor=white" alt="VirtualBox / Kali Linux">
  <img src="https://img.shields.io/badge/pfSense_/_firewalls-3d6349?style=for-the-badge&logo=pfsense&logoColor=white" alt="pfSense / firewalls">
  <img src="https://img.shields.io/badge/TCP/IP_%26_DNS-3d6349?style=for-the-badge" alt="TCP/IP & DNS">
  <img src="https://img.shields.io/badge/Raspberry_Pi_/_ARM-3d6349?style=for-the-badge&logo=raspberrypi&logoColor=white" alt="Raspberry Pi / ARM">
  <img src="https://img.shields.io/badge/Load_balancing-3d6349?style=for-the-badge" alt="Load balancing">
  <img src="https://img.shields.io/badge/Cloudflare_/_DDoS_mitigation-3d6349?style=for-the-badge&logo=cloudflare&logoColor=white" alt="Cloudflare / DDoS mitigation">
  <img src="https://img.shields.io/badge/Automation_%28cron%2C_systemd%29-3d6349?style=for-the-badge" alt="Automation (cron, systemd)">
  <img src="https://img.shields.io/badge/Network_segmentation-87701d?style=for-the-badge" alt="Network segmentation">
  <img src="https://img.shields.io/badge/Backup_%26_recovery-87701d?style=for-the-badge" alt="Backup & recovery">
</p>

**ANALYSIS & RESEARCH**

<p>
  <img src="https://img.shields.io/badge/Java_decompilation_%28Vineflower%2C_CFR%29-3d6349?style=for-the-badge" alt="Java decompilation (Vineflower, CFR)">
  <img src="https://img.shields.io/badge/Protocol_/_packet_analysis-3d6349?style=for-the-badge" alt="Protocol / packet analysis">
  <img src="https://img.shields.io/badge/Coordinated_disclosure-3d6349?style=for-the-badge" alt="Coordinated disclosure">
  <img src="https://img.shields.io/badge/Secure_code_review-3d6349?style=for-the-badge" alt="Secure code review">
  <img src="https://img.shields.io/badge/CWE_classification_%26_triage-3d6349?style=for-the-badge" alt="CWE classification & triage">
  <img src="https://img.shields.io/badge/Bash_scripting-3d6349?style=for-the-badge&logo=gnubash&logoColor=white" alt="Bash scripting">
  <img src="https://img.shields.io/badge/Vulnerability_management-87701d?style=for-the-badge" alt="Vulnerability management">
  <img src="https://img.shields.io/badge/Python_tooling-87701d?style=for-the-badge&logo=python&logoColor=white" alt="Python tooling">
  <img src="https://img.shields.io/badge/Burp_Suite-7a7263?style=for-the-badge&logo=burpsuite&logoColor=white" alt="Burp Suite">
</p>

**DEVELOPMENT & DATA**

<p>
  <img src="https://img.shields.io/badge/SourcePawn_/_SourceMod-3d6349?style=for-the-badge" alt="SourcePawn / SourceMod">
  <img src="https://img.shields.io/badge/C%2B%2B_%28Metamod_extensions%29-3d6349?style=for-the-badge&logo=cplusplus&logoColor=white" alt="C++ (Metamod extensions)">
  <img src="https://img.shields.io/badge/Function_hooking_/_detours-3d6349?style=for-the-badge" alt="Function hooking / detours">
  <img src="https://img.shields.io/badge/SQLite-3d6349?style=for-the-badge&logo=sqlite&logoColor=white" alt="SQLite">
  <img src="https://img.shields.io/badge/MySQL-3d6349?style=for-the-badge&logo=mysql&logoColor=white" alt="MySQL">
  <img src="https://img.shields.io/badge/Parameterized_/_escaped_SQL-3d6349?style=for-the-badge" alt="Parameterized / escaped SQL">
  <img src="https://img.shields.io/badge/REST_API_integration-3d6349?style=for-the-badge" alt="REST API integration">
  <img src="https://img.shields.io/badge/Discord_webhooks-3d6349?style=for-the-badge&logo=discord&logoColor=white" alt="Discord webhooks">
  <img src="https://img.shields.io/badge/Git_/_GitHub-3d6349?style=for-the-badge&logo=github&logoColor=white" alt="Git / GitHub">
  <img src="https://img.shields.io/badge/Secrets_hygiene-3d6349?style=for-the-badge" alt="Secrets hygiene">
  <img src="https://img.shields.io/badge/C_/_memory_safety-7a7263?style=for-the-badge" alt="C / memory safety">
</p>

**GOVERNANCE & SUPPORT**

<p>
  <img src="https://img.shields.io/badge/PII_handling_%28FERPA%29-3d6349?style=for-the-badge" alt="PII handling (FERPA)">
  <img src="https://img.shields.io/badge/Ticketing_%26_escalation-3d6349?style=for-the-badge" alt="Ticketing & escalation">
  <img src="https://img.shields.io/badge/Open_source_licensing_%28GPL%29-3d6349?style=for-the-badge" alt="Open source licensing (GPL)">
  <img src="https://img.shields.io/badge/OWASP_ASVS-87701d?style=for-the-badge&logo=owasp&logoColor=white" alt="OWASP ASVS">
  <img src="https://img.shields.io/badge/NIST_CSF-87701d?style=for-the-badge" alt="NIST CSF">
  <img src="https://img.shields.io/badge/PCI_DSS-87701d?style=for-the-badge" alt="PCI DSS">
</p>

### Roadmap

**Cleared**

- [x] Security+, Network+, CySA+, Server+
- [x] Microsoft SC-200 and SC-500
- [x] Segmented home lab standing, attacks run against it, detections logged
- [x] Pi-hole sinkhole live network-wide, including what it misses
- [x] Over 400+ modded Minecraft mods (.jar) from #1 most downloaded modpack (RLCraft *50M+ downloads* & RLCraft Dregora *1M downloads*) decompiled and scanned to find packets that are not permission gated. Around 50+ mods found with vulnerabilities that can be exploited, reported to their developer & RLCraft development team, privately tested by me through a client-side mod (C2S), graded the severity of packets found based on their impact, and documented

**In progress**

- [ ] Microsoft SC-100, Cybersecurity Architect
- [ ] ISC2 CISSP - Certified Information Systems Security Professional

**Next**

- [ ] Track the reported MC-001 findings through to a deployed patch
- [ ] A.S. Cybersecurity, Coastline College, 2027
- [ ] Accepted and enrolling into a University for a bachelor's degree in Cybersecurity, IT, or Informatics
- [ ] 2027 internship: Cybersecurity, IT, Cloud/Network Security, SOC, or Security Analyst field

---

## <img src="https://banyourself.github.io/assets/img/enchanted-book.gif" align="absmiddle" alt=""> DISCLOSURE ETHICS

Everything accomplished above was done within my own systems without compromising any
external devices, participating in illegal activities, or with malicious intent. All
findings get reported to the developer or maintainer first, and any further testing I
do afterwards stays private and is only for documentation. It all gets redacted until a patch is deployed for the
vulnerability or when I get permission from the developer/maintainer themselves.

```
════════════════════════════════════════════════════════════
  END OF FILE            github.com/banyourself
════════════════════════════════════════════════════════════
```
