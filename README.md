[README.md](https://github.com/user-attachments/files/27448211/README.md)
# 🐎 Ride a Horse

A FiveM resource that lets you spawn and ride a horse — fully synced so every player on the server can see you riding.

Originally based on the *Ride a Deer* concept by **IllidanS4** ([gta5-mods.com](https://www.gta5-mods.com/)), modernized for current FiveM (cerulean, Lua 5.4) and reworked into a proper horse mount with multiplayer sync.

> **Note:** This repo ships **two resources**:
> - **`ride-a-horse`** — the script (mount/dismount logic, sync, controls)
> - **`horse`** — the model swap that streams horse assets (`.ydd`/`.yft`/`.ytd`) in place of the `a_c_deer` ped, so you actually look like you're on a horse
>
> You need **both** running for it to look right. With only the script, you'll be riding a deer.

---

## ✨ Features

- 📢 **Network synced** — other players see you mounted on the horse, not floating next to it
- 🏇 **Actually fast** — trot and gallop speeds tuned so the mount feels like a horse, not a slow walk
- 🎮 **Controller + keyboard** support out of the box
- 🪄 Simple `/horse` command to spawn and despawn
- 🛡️ Invincibility and ragdoll toggles to stop your mount from dying mid-ride
- 🧹 Clean shutdown — no ghost peds left behind on resource stop or player disconnect

---

## 📦 Installation

1. Download both folders from this repo into your server's `resources/` directory:
   ```
   resources/horse/           ← model swap (must load first)
   resources/ride-a-horse/    ← the script
   ```
2. Add them to your `server.cfg` **in this order**:
   ```
   ensure horse
   ensure ride-a-horse
   ```
   The skin must be ensured before the script so the horse assets are streamed in by the time the ped is spawned.
3. Restart the server.

> Already have another resource that overwrites `a_c_deer`? Only one will win — make sure no other stream pack is replacing the same model files.

---

## 🎮 Usage

| Action       | Keyboard      | Controller       |
| ------------ | ------------- | ---------------- |
| Spawn / despawn horse | `/horse` command | `/horse` command |
| Mount / dismount      | `E`              | D-Pad Right      |
| Sprint (gallop)       | `Left Shift`     | `A` button       |
| Steer                 | `WASD`           | Left stick       |

Walk up to the horse, get the *Press **E** to ride* prompt, hop on, and go.

---

## ⚙️ Configuration

All tunables live at the top of `client.lua` in the `Config` table:

| Key | Default | Description |
| --- | --- | --- |
| `HorseModel` | `` `a_c_deer` `` | Underlying ped model (keep as deer if reskinning) |
| `Speeds.Walk` | `6.0` | Trot speed (m/s) |
| `Speeds.Run` | `14.0` | Gallop speed (m/s) |
| `MoveRange.Walk` / `MoveRange.Run` | `8.0` / `18.0` | How far ahead the AI commits to its target — higher = smoother movement |
| `InteractionRadius` | `3.0` | Distance to show the mount prompt |
| `HorseInvincible` | `true` | Block damage to the mount |
| `HorseCanRagdoll` | `false` | Whether the mount can ragdoll |

---

## 🛠️ How sync works

- The horse ped is spawned as a networked entity, so all clients can see it.
- On mount, the local client sends `rideAHorse:mount` with the horse's network ID.
- The server rebroadcasts it to every client via `rideAHorse:remoteMount`.
- Each client resolves the horse from its network ID and applies the same attachment + sitting animation to the rider's ped — this is what makes the rider visible to everyone, since attachments and `TaskPlayAnim` are local-only by default in FiveM.
- Late-coming clients are handled by a retry loop that waits for the entity to enter network scope.
- A `playerDropped` handler cleans up so disconnects never leave ghost riders attached.

---

## 📁 File structure

```
resources/
├── horse/                       ← model swap
│   ├── fxmanifest.lua
│   └── stream/
│       ├── a_c_deer.ydd         (horse mesh)
│       ├── a_c_deer.yft         (skeleton / fragment)
│       └── a_c_deer.ytd         (textures)
│
└── ride-a-horse/                ← the script
    ├── fxmanifest.lua
    ├── client.lua
    └── server.lua
```

---

## 🙏 Credits

- **Original concept:** IllidanS4 — *Ride a Deer* on gta5-mods
- **Modernized & reworked for FiveM:** [shotbyuno](https://github.com/shotbyuno)

---

## 📜 License

Free to use and modify. Credit appreciated but not required.
