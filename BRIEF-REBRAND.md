# DocyraDesk — two tasks

Repo: `/opt/data/projects/rustdesk-fork` (RustDesk 1.5.1, AGPL-3.0, clean at `55786aa`).

Do **task 1, then task 2**. Do not touch `/opt/data/projects/docyradesk` (unrelated parked project). Do not push anywhere; no git remote, no network writes other than the downloads named below.

---

## Task 1 — self-hosted relay (hbbs / hbbr)

1. Download `https://github.com/rustdesk/rustdesk-server/releases/download/1.1.16/rustdesk-server-linux-amd64.zip` to `/opt/data/cache/scratch/`.
2. Extract `hbbs` and `hbbr` to `/opt/data/apps/docyra-relay/bin/`. `chmod +x`. Data dir `/opt/data/apps/docyra-relay/data/`.
3. Start both **in the background with nohup** (not s6 — the human wires that up after verifying):
   - `hbbs -r <public-ip>:21117 -p 21116 -d` style; keep it simple: default `hbbs` in the data dir, `hbbr` likewise.
   - Get the public IP with `curl -s ifconfig.me`. Use it for `-r`.
4. Verify: `ss -lntu | grep -E '2111[5-9]'` shows listeners and `/opt/data/apps/docyra-relay/data/id_ed25519.pub` exists.
5. Print: the public key (`id_ed25519.pub`), the exact command lines used, and the listener list.

Do **not** install s6 services. Do **not** modify anything under `/run/service`, `/etc/s6-overlay`, or any existing run script.

---

## Task 2 — DocyraDesk rebrand

Target identity:
- Product name: `DocyraDesk`
- Binary: `DocyraDesk.exe`, service `DocyraDeskService`
- App id / bundle id: `com.docyra.desk`
- Company: `Docyra`
- Website: `https://docyradesk.com`

Rules, in priority order:

1. **Nothing that affects behaviour may break.** Keep intact: the `rustdesk://` URL scheme and its parser, the update/version-check endpoints, the rendezvous/relay protocol strings, any `hbbs`/`hbbr` identifiers, TOML/JSON config keys, registry key paths that an existing install would own (do not orphan state by renaming storage keys), and file names that the update mechanism depends on.
2. **Rename what the user sees**: window titles, tray menu, About box, installer strings, Flutter `MaterialApp` title, `.desktop` files, app metadata, `versionName` display.
3. Rename internal identifiers **only** where they are not load-bearing (Cargo package `name`, Dart package name, resource IDs, exe target name). Put an explicit allowlist/denylist in `REBRAND.md` at repo root listing every file you did and did not touch and why.
4. Default settings: set the default rendezvous server to the IP from task 1 (`custom-rendezvous-server` / `relay-server`), so a fresh install points at our relay. Leave `api-server` and `key` unset unless the protocol requires them.
5. Icons: reuse the existing `res/` PNGs — do not invent artwork. Only rename/regen the files that must carry the new name.

Output: `REBRAND.md` + a `git diff --stat` summary. Commit as one commit `DocyraDesk rebrand`.

## Acceptance

- `git status` clean after the commit.
- Print `REBRAND.md`, the diff stat, and the task-1 verification output.
- State plainly anything you deliberately skipped or were unsure about.
