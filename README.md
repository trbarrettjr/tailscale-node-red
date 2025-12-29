# Node-RED over Tailscale (Docker Compose)

This is a self-taught lesson that runs **Node-Red** behind a **Tailscale** sidecar container so the editor and flows are only accessible over your Tailscale network.  No ports are exposed to the public internet, and Node-RED inherits the Tailscale network stack directly.

## 🚀 What this solves

If you want Node-RED reachable at a **Tailscale IP** (e.g., 100.x.y.z:1880) instead of exposing ports on your LAN or host, Docker doesn't make that obvious.  The key is to run Node-RED in the **same network namespace** as the Tailscale container.

The magic line:

`network_mode: service:tailscale`

This forces Node-RED to share the Tailscale container's network stack, so it receives traffic directly on the Tailscale interface.

## 🧩 docker-compose.yaml

**Note:** Replace the `TS_AUTHKEY` with your own ephemeral or reusable auth key.  Never commit a real key to a public repo.

## 🧠 How it works

### 1. Tailscale runs as a standalone container

It brings up a full Tailscale node with its own network namespace, TUN device, and persistent state.

### 2. Node-RED joins the Tailscale network namespace

By using:

`network_mode: service tailscale`

Node-RED doesn't get its own Docker network.  Instead, it binds to the Tailscale interface inside the Tailscale container.

### 3. Access Node-RED at the Tailscale IP

Once both containers are running:

- Run `tailscale ip` inside the Tailscale container
- Visit: http://<tailscale-ip>:1880

No host ports, no LAN exposure, no reverse proxy required.

## 🧭 Troubleshooting

- If Node-RED isn't reachable, exec into the Tailscale container and run:

```
tailscale status
tailscale ip
```

- Make sure your auth key hasn't expired
- Ensure your Tailscale ACLs allow access from your device
- Confirm Node-RED is listening on `0.0.0.0` (default behavior)

## 🙌 Why this repo exists

This setup isn't obvious, and most examples online either expose Node-RED publicly or rely on host networking.  If you're trying to keep your automation stack private-by-default, this pattern is clean, minimal solution.
