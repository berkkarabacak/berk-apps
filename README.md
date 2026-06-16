# berkkarabacak.com — platform

Umbrella repo for the free, open-source apps behind **[berkkarabacak.com](https://berkkarabacak.com)**.
Each app/library is its **own repo**, wired in here as a **git submodule** — so the whole platform
can be cloned, pinned, and **moved to a new server** in one shot.

## Structure
| Path | Submodule repo | What it is |
|---|---|---|
| `web` | [website](https://github.com/berkkarabacak/website) | the personal homepage (single-file static) |
| `apps/accounts` | [accounts](https://github.com/berkkarabacak/accounts) | central accounts + Google SSO + encrypted Jira credential vault |
| `apps/cloud-site-reset` | [cloud-site-reset](https://github.com/berkkarabacak/cloud-site-reset) | Jira Cloud bulk-wipe tool |
| `lib/bk_common` | [bk-common](https://github.com/berkkarabacak/bk-common) | shared library (session / vault / jira / passwords) |

> The Jira Migration Crosschecker has its own repo (`berkkarabacak/JiraTester`) and can be added as a submodule the same way.

## Clone everything
```bash
git clone --recurse-submodules https://github.com/berkkarabacak/berk-apps.git
# or, after a plain clone:
git submodule update --init --recursive
```

## Update one app later
```bash
cd apps/accounts && git pull origin main && cd ../..
git add apps/accounts && git commit -m "bump accounts"
```

## Deploy / move to a new server
See [`deploy/README.md`](deploy/README.md). Each app is a FastAPI service on a private
localhost port, kept alive by `systemd`, reverse-proxied by `nginx` (TLS via Let's Encrypt),
with `bk_common` on `PYTHONPATH` and shared secret files in the service user's home dir.

MIT licensed.
