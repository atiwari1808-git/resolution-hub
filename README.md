# Resolution Hub v3.0.0

Email-first troubleshooting for one LCM deployment with **Inhouse, Enable, RPA and MATE** workspaces. Other teams customize and deploy independent copies. This is the implemented application, not the earlier in-memory preview.

## What changed

- Username/password login; hashed passwords; cookie sessions; forced temporary-password change; admin reset/revocation.
- Database-backed custom roles, backend permissions and workspace grants; protected Administrator role and last-admin safeguard.
- Two main knowledge fields: **Issue / Email / Extracted Content** and **Resolution**. Optional **Environment/Server**. No Type/Category/Review Due/Version form fields.
- Whole-email/file upload, sanitized rich-text paste, protected attachments and inline email images.
- Complete-document import: prefill both fields, review multiple pairs, confirm and save drafts. Missing solutions remain unresolved; no retyping required.
- Continuing private conversations, source evidence, follow-up context, optional approved vision input and OCR.
- Screenshot-inspired top navigation, blue actions, teal headings, rounded panels, light/dark themes and offline PNG branding.
- Real Users/Admin pages for roles, grants, settings, logo, security policy, imports, audit, search rebuild, knowledge export and history retention.

## Start locally on your existing port 8001

**Use a new directory and new database. Do not overwrite v2 or copy its database into v3.** Python 3.12 is required for this tested package; it is not a Python 3.8 build.

```bash
cd resolution-hub-v3
python3.12 -m venv .venv
. .venv/bin/activate
pip install -r requirements.txt
python scripts/bootstrap.py
python -m uvicorn app.main:app --host 127.0.0.1 --port 8001 --workers 1
```

Bootstrap creates `.env` from the example if absent, asks you to choose the first Administrator username/password, and creates the new database. No default password, API key, real knowledge or sample account is shipped. Stop your old process first if it occupies port 8001. Open [the local application](http://127.0.0.1:8001).

Create Engineer and Reviewer accounts in **Users**. Temporary passwords are shown once and must be changed on first sign-in. Deliver them securely. Publication requires an independent reviewer, including for Administrator-authored knowledge. Forgot password is administrator-assisted, not an email recovery service.

## AI settings: important difference from v2

On the **first initialization only**, `.env` seeds chat/provider settings. After that, edit them in **Admin > AI Provider**; changing `.env` alone does not replace saved database settings. Secrets, network allowlists, secure cookies, origins, OCR and scanner mode remain server environment settings and require restart.

Default mode is evidence-only. This release uses local FTS5 lexical retrieval and **does not call an embeddings API**. Old EMBED_PROVIDER/EMBED_MODEL variables are unused. Chat/vision/AI extraction need an approved compatible or Ollama endpoint and actual model access. Live model calls were not tested here. Public external AI must not be used for organizational data without the applicable approval.

## Read before rollout

- [Deployment guide](docs/DEPLOYMENT.md): local, AI, Docker, TLS, scanning, OCR, backup/restore.
- [Migration guide](docs/MIGRATION.md): v2 knowledge export to v3 drafts; no in-place upgrade.
- [Security and release gates](docs/SECURITY.md): controls and residual risks.
- [Feature coverage](docs/FEATURE_MATRIX.md): exact implemented scope and limitations.
- [HLD](docs/HLD.md) and [LLD](docs/LLD.md): architecture, schema, authorization and workflows.
- [Verification report](docs/TEST_REPORT.md): executed tests vs unverified production behavior.
- [User guide](docs/USER_GUIDE.md): daily workflow and admin controls.
- [API schema](docs/openapi.json): generated OpenAPI.

## Production status

Hardened **single-node release candidate**, not a production-certified or organization-approved system. SQLite is not an HA/multi-replica design. Before operational rollout: approve hosting/data/model use, configure HTTPS/secure cookies/allowed origins, require maintained malware scanning, isolate parsers, scan dependencies/images, test live models and representative mail formats, verify backup/restore and complete security/load review. See SECURITY.

## Tests

```bash
pip install -r requirements-dev.txt
python -m pytest -q
```

Optional browser regression requires a separately installed Playwright Chromium browser:

```bash
python -m playwright install chromium
python scripts/browser_smoke.py
```

The browser runner creates a temporary database and synthetic accounts/content. They are test fixtures, not shipped production credentials. Theme preference is the only item stored in browser localStorage; session credentials are not stored there.
