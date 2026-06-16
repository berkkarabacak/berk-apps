# Deploy / move to a new server

All apps run as one user on a single VM, behind nginx (TLS via Let's Encrypt).

| App | Port | Public path |
|---|---|---|
| accounts | 8820 | `/account` (`/account/internal` is blocked at nginx) |
| cloud-site-reset | 8810 | `/cloudsitereset` |
| website | — | `/` (static, served by nginx from `web/`) |
| (crosschecker) | 8800 | `/jiramigrationcrosschecker` |

## Per Python app
```bash
cd apps/accounts          # or apps/cloud-site-reset
python3 -m venv .venv && .venv/bin/pip install -r requirements.txt
PYTHONPATH=../../lib PORT=8820 .venv/bin/python -m webapp.server   # bk_common is at lib/bk_common
```
systemd unit: `ExecStart=<app>/.venv/bin/python -m webapp.server`, `Environment=PORT=...`,
`Environment=PYTHONPATH=<repo>/lib`.

## Shared secrets (auto-created on first run, chmod 600, in the service user's home)
`.bk_session_secret`, `.bk_internal_key`, `.bk_vault_key`, and (optional) `.bk_google_oauth.json`
(Google OAuth web-client `{client_id, client_secret}`).

## Moving servers
1. `git clone --recurse-submodules …` on the new box.
2. Recreate each app's venv.
3. Copy the secret files (above) and each app's data: `accounts/accounts.db`, `cloud-site-reset/runs/`.
4. Recreate the systemd units + nginx sites; point DNS; run certbot.
