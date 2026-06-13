
## Submit API docs for ingestion
Use this ONLY if you have api docs url... and you could not find the catalog_id in Step 1. 

1. Submit a docs URL for ingestion

POST /ingest queues the crawl and returns a job_id (for polling) and a catalog_id (the stable ID of the API entry).

```sh
curl -s -X POST https://api.apicrawl.dev/ingest \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://docs.stripe.com/api"
  }'
```
url (required) — the API docs URL to crawl.

Response (202 Accepted):
`{ "job_id": "<JOB_ID>", "catalog_id": "<CATALOG_ID>", "status": "queued" }`

Grab the job_id from this to poll. One-liner to capture it (needs jq):

```sh
JOB_ID=$(curl -s -X POST https://api.apicrawl.dev/ingest \
  -H "Content-Type: application/json" \
  -d '{"url":"https://docs.stripe.com/api"}' | jq -r .job_id)
```

2. Check status by job_id 

GET https://api.apicrawl.dev/jobs/{job_id} — polls live progress. Note: if the job hasn't updated in >5 min while running, it's auto-marked error (timeout).

```sh
curl -s https://api.apicrawl.dev/jobs/$JOB_ID | jq
```

Watch it until it finishes (state becomes finished or error):

```sh
watch -n 3 "curl -s https://api.apicrawl.dev/jobs/$JOB_ID | jq '{state, phase, status_message, pages_discovered, pages_ingested, error}'"
```

Key fields: state (running | finished | error), phase (discovering | crawling | ingesting | classifying), pages_discovered/pages_ingested, and error.
