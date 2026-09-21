# Project Arena

An online 1v1 / 2v2 browser shooter. No install, no server to run: open the page,
create a match, send the link, play.

Built on the engine from Project Valorant — same movement, weapons, hitscan and
viewmodel — with the bots and the spike replaced by real players over the network.

## Playing

1. Open the page and pick a name, a mode (1v1 or 2v2) and a rifle.
2. **CREATE MATCH** opens a room and gives you an invite link.
3. Send that link to whoever is playing. They open it and press **JOIN**.
4. When the lobby is full, the host presses **START MATCH**.

First side to 7 rounds wins. A round ends when one side is wiped out, or the
90-second clock runs out.

## Controls

WASD move · Shift walk · C crouch · Space jump · LMB fire · RMB aim · R reload
· 1/2/3 weapons · G drop or pick up a gun · Y inspect the knife · Tab scoreboard
· Esc pause. Everything is rebindable under CONTROLS.

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
