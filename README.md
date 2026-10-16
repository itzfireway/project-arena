# Project Arena

An online 1v1 / 2v2 browser shooter. No install, no server to run: open the page,
create a match, send the link, play.

Built on the engine from Project Valorant — same movement, weapons, hitscan,
viewmodel and agents — with the bots and the spike replaced by real players over
the network.

## Playing

1. Open the page and pick a name, a mode (1v1 or 2v2) and an agent.
2. **CREATE MATCH** opens a room and gives you an invite link.
3. Send that link to whoever is playing. They open it and press **JOIN**.
4. When the lobby is full, the host presses **START MATCH**.

First side to 7 rounds wins. A round ends when one side is wiped out, or the
90-second clock runs out.

## Agents

Four agents, each with three abilities and an ultimate, carried over from Project
Valorant: **Jett**, **Phoenix**, **Sage** and **Brimstone**. Character select is
in the lobby, once you have created or joined a match: everyone picks their own
agent under **YOUR AGENT** and can change it right up until START MATCH.

Abilities are on **Q / E / Z**, the ultimate on **X**. The three ability charges
refill at the start of every round. The ultimate is charged by points — one for a
kill, one for dying, one for each round you turn up to — and the cost is shown on
the agent card. Abilities are locked during the pre-round countdown, except the
two that only move you (Updraft and Tailwind).

Smokes, fires, flashes and walls all appear for everyone: the ability runs on the
caster's machine and is relayed to the other players, so the same cloud lands in
the same place on every screen. Whether a flash blinded you is worked out by your
own browser from where you were looking, for the same reason hits are decided by
the shooter.

Two abilities need a teammate and so only do anything in 2v2: Sage's
**RESURRECTION**, and healing someone other than yourself.

### What the host controls

Two of the three character-select settings belong to the host, and both sit in
the lobby next to the agent cards, changeable before every match. Guests see them
but cannot touch them.

- **AGENTS ALLOWED** — which agents are in play. Switch one off and it is locked
  out of the match: its card greys out for everybody and nobody can pick it. At
  least one has to stay in. Your own preference is remembered while an agent is
  locked, so it comes straight back if the host reopens it, and a guest who is
  already on a locked agent is moved to the first one still in play.
- **AGENTS THIS MATCH** — whether agents are used at all. Switch to **GUNS ONLY**
  and nobody gets abilities or an ultimate for that match; the ability bar
  disappears and it is a pure gunfight.

## Guns

There is no loadout to choose before the match and nothing to pay for. Press
**B** for the armory and take whatever you want: every weapon is free. It opens
during the five seconds before a round, between rounds, and any time you are
waiting to respawn — not while you are alive and the round is live. What you
pick stays yours for the rest of the match and is re-issued every round.

## Controls

WASD move · Shift walk · C crouch · Space jump · LMB fire · RMB aim · R reload
· 1/2/3 weapons · G drop or pick up a gun · B armory · Y inspect the knife
· **Q/E/Z abilities · X ultimate** · **Enter chat** · Tab scoreboard · Esc pause. Everything is
rebindable under CONTROLS.

## Talking to each other

Press **Enter** during a match and a **SAY** bar opens at the bottom of the screen.
Type, press Enter again to send it to everyone, or Escape to throw it away. The bar
holds on to the keyboard while it is open, so WASD types rather than moves, and you
keep your mouse look the whole time — no pause, no lost round. You can talk while you
are waiting to respawn. Messages show for a few seconds above your health, teammates
in teal and the other side in red, and the key is rebindable under CONTROLS.

A guest's message goes up to the host, who stamps it with the name and team it
actually came from before relaying it, so nobody can talk as somebody else.

## The arenas

Two small, tight symmetric maps: **Crossfire**, a box with a long sightline down
the middle, and **Lantern**, a central hall with two flanking lanes. Planks and
brown crates are thin enough for a bullet (a shot keeps a share of its damage per
wall, up to two walls); concrete, pillars and the teal metal crates stop it.

## How the networking works

Peer-to-peer over WebRTC, using the public PeerJS broker purely to introduce the
two browsers to each other. After that, game traffic goes **directly between
players** — nothing passes through a server, and there is nothing to pay for or
keep running.

- The player who creates the match is the **host**. Their browser owns the round
  clock, everyone's health and the score, and relays messages between guests.
- Guests hold one connection up to the host and send their position 20 times a
  second. Remote players are drawn ~100 ms in the past and interpolated, which is
  what keeps other players moving smoothly between packets.
- **Abilities are owned by the caster.** The player who threw it decides where it
  landed and reports the burn damage; healing and resurrection are confirmed by
  the host, the same as a gunshot, so nobody's health can be changed twice.
- **Hits are decided by the shooter.** Your client does the raycast and tells the
  host what it hit. That is why you never have to lead your shots to compensate
  for someone else's ping — and it is also the reason this is a game for people
  you know, since a modified client could claim hits it did not land.

### Known limits

- Some strict networks (corporate firewalls, a few mobile carriers) block direct
  peer connections. Fixing that needs a TURN relay, which costs money to run.
  Everywhere else, including across countries, works.
- The host's browser runs the match, so if the host closes the tab the match ends.
- A tabbed-out host keeps the match alive on a throttled timer, but the round
  clock runs slowly until they come back.

## Hosting it

Any static host works — it is one HTML file. For GitHub Pages:

```sh
gh auth login                 # run this yourself; it is interactive
gh repo create project-arena --public --source=. --push
gh api -X POST repos/:owner/project-arena/pages -f 'source[branch]=main' -f 'source[path]=/'
```

The game is then at `https://<your-username>.github.io/project-arena/`, and the
invite links it generates point at that address.
