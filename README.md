# SLS Queue Manager prototype

A local Flask web application for configuring and running reusable Str8lines queue subscribers.

## Included

- Add/list/delete subscribers
- Start and pause ingestion workers
- Client-credentials authentication
- Repeated POST fetches with configurable batch limit
- Raw JSON batch files written atomically to a configurable folder
- ACK only after file write succeeds
- Immediate re-fetch while records exist; configured wait when queue is empty
- Live browser console
- SQLite configuration store

## Run

```bash
python -m venv .venv
# Windows: .venv\Scripts\activate
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
python app.py
```

Open `http://127.0.0.1:5000`.

## Important prototype assumptions

1. Authentication returns `access_token`, `token`, or `accessToken`.
2. Fetch accepts `{ "limit": 500 }`. Adjust this once the exact vendor contract is known.
3. Fetch records are found under `items`, `records`, `deliveries`, `data`, or `results`, or returned as a list.
4. Every record contains `delivery_id` and `lease_token` for ACK.
5. ACK body is `{ "outcomes": [{ "delivery_id": ..., "lease_token": ..., "ok": true }] }`.
6. The client secret is stored in local SQLite for this prototype. Production should use AWS Secrets Manager, Azure Key Vault, or another approved secret store.
7. Workers run inside the Flask process. Production should use a durable worker/scheduler such as ECS, Kubernetes, Windows Service, Celery, or a managed job platform.

## Recommended next changes

- Confirm exact token, fetch, batching, and ACK schemas.
- Add S3/Parquet destinations behind an output adapter interface.
- Add retry policy, idempotency keys, health metrics, persistent logs, and alerts.
- Encrypt or externalize secrets.
- Add edit/test-connection actions and authentication authorization.
