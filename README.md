# Instoryview CLI

Requires Node.js 20 or newer.

```bash
npm install --global instoryview
instoryview --version
instoryview auth login
instoryview skill install   # optional: install the assistant Skill
instoryview tracking list
instoryview followers alice --json
```

`auth login` stores a verified Key in the macOS System Keychain. Linux, Windows, containers, and CI use `INSTORYVIEW_API_KEY` from their environment or secret manager. No plaintext credential file is created. `auth status` validates an existing credential through the tracking read endpoint. `auth logout` deletes the local Keychain credential; unset environment credentials separately and revoke the Key on the website to disable it everywhere.

`skill install` copies the bundled Skill to `~/.agents/skills/instoryview` (`--target claude` for `~/.claude/skills`, `--project` for the current directory, `--dir PATH` for anywhere) and refuses to overwrite an existing copy unless you pass `--force`. It needs no credentials.

Read commands: `tracking list`, `account USERNAME`, `followers USERNAME`, `following USERNAME`, and `changes USERNAME`. These only read existing data. Missing data does not trigger a paid collection. Lists return one page; pass `--cursor` explicitly to request another. `tracking list --include-archived` includes retained archived records. Changes default to the last seven calendar days in UTC; use paired `--from` / `--to`, `--timezone`, and `--list following` when needed.

```bash
instoryview changes alice --from 2026-10-01 --to 2026-10-07 --timezone UTC --json
instoryview followers bob --live --max-credits 2 --json
```

An online command requires both `--live` and a positive `--max-credits`, plus a Key with online-query permission. It never starts continuous tracking. Each page is a separate budgeted request. The CLI prints a recovery command and saves only its parameters and idempotency key under `${XDG_STATE_HOME:-~/.local/state}/instoryview/`, with private permissions. `request_in_progress` recovery uses the same body and key for at most 60 seconds. Unknown results and network failures stop with recovery instructions; never retry them with a new key unless you explicitly intend a new paid query.

Successful `--json` output is the API's unmodified `{data, meta}` envelope on stdout. Progress and errors go to stderr. Exit codes: `0` success, `1` network/service, `2` invalid arguments, `3` auth/permission, `4` unavailable data, `5` budget or pending result. Data timestamps and partial coverage matter: returned page size is not the full Instagram follower count, and change detection time is not the exact action time.

Set `INSTORYVIEW_API_URL` only when deliberately connecting to another environment. It defaults to `https://instoryview.com/open-api/v1`; HTTPS is required except for loopback development URLs. Stored Keychain credentials are scoped to the API URL. Redirects are refused. Run `instoryview --help` for all options.

To update, run `npm install --global instoryview@latest`. To uninstall, run `npm uninstall --global instoryview`.
