# Worldpass

Take turns hosting the same Minecraft server from your own PCs, with the world synced between you and only one host at a time.

> **Status: early development.** Nothing here is usable yet. This README describes what we are building. See the [roadmap](#roadmap) for progress.

## Why

Free hosting like Aternos is slow. Hosting from one PC works, but then that person always has to be online. This program lets two people (or more) share one world and hand it back and forth safely, even if they live in different houses.

## How it works

The world has exactly **one owner at a time**, and ownership is handed over like a baton.

1. Before starting, the program checks who holds the **host token**. If nobody holds a fresh one, you take it.
2. It pulls the newest world from shared storage, so you start where the last host stopped.
3. It starts the server and makes it reachable (see [connection modes](#connection-modes)).
4. While running, it uploads a snapshot every few minutes and sends a heartbeat.
5. On a clean stop, it uploads a final snapshot and releases the token.

**Safety rules**
- Internet is required. The program won't start without reaching shared storage, and it stops its own server if internet is lost longer than a timeout.
- Every upload says which version it is based on. If the stored world has moved on, the upload is refused and your copy is saved aside. Nothing is overwritten silently.
- If the host disappears (no heartbeat), the other person can take over from the last snapshot.

## Planned features

- Host token with heartbeat and version checks
- Background snapshots, with the last N kept so you can roll back
- Server types: Paper first, then Spigot, then maybe a Rust Minecraft server
- Wi-Fi loss protection (watchdog)
- Discord notifications ("X started the server", "Wi-Fi lost, snapshot saved")
- CLI first, GUI later
- Runs on **Linux, Windows, and macOS**

### Connection modes

Each PC picks how players reach it:

| Mode | Needs | Players type |
|---|---|---|
| **Raw IP** | Router port forwarding | The host's public IP |
| **Domain** | Port forwarding + a free or paid domain (e.g. DuckDNS) | A fixed domain name |
| **Tunnel** | A free [playit.gg](https://playit.gg) account | The tunnel address |

If your internet provider doesn't give you a public IP (CGNAT), use the tunnel mode.

## Planned usage

This is the design, not a working interface yet:

```
<name> start     # claim the token, pull the world, start the server
<name> stop      # stop the server, upload the world, release the token
<name> status    # who is hosting, last snapshot, connection address
```

Per-PC settings (server type, memory, connection mode, storage) will live in a `config.toml`. Copy `config.example.toml` and fill in your own values. Never commit your real config, because it will hold tokens and webhook URLs.

## Requirements

- [Rust](https://rustup.rs) (to build from source)
- Java (for the Minecraft server)

## Building

```
cargo build --release
```

## Roadmap

- [ ] Host token + local storage
- [ ] CLI, config, and server process (Paper)
- [ ] Snapshots and sync
- [ ] Orchestrator (the full host cycle)
- [ ] Connection modes
- [ ] Watchdog and Discord notifications
- [ ] Real shared storage, tested across two houses
- [ ] GUI, rollback, stats

## Contributing

We are not accepting outside contributions yet. Issues and ideas are welcome.

## License

- Licensed under the [GNU AGPL-3.0](https://www.gnu.org/licenses/agpl-3.0.txt). A separate commercial license may be available; contact us.

Copyright (c) 2026 `Dinanka Koirala` and `Aijan Subedi`
