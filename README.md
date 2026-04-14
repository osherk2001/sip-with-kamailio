# SIP with Kamailio + OpenSearch

![Docker](https://img.shields.io/badge/Docker-ready-blue)
![OpenSearch](https://img.shields.io/badge/OpenSearch-monitoring-005EB8)
![Kamailio](https://img.shields.io/badge/Kamailio-SIP%20Proxy-green)
![License](https://img.shields.io/badge/License-Educational-lightgrey)

A Docker based SIP lab that uses **Kamailio** for SIP routing and **OpenSearch** for real time SIP event monitoring.

The project captures SIP call activity, parses structured events with **Fluent Bit**, stores them in **OpenSearch**, and visualizes them in **OpenSearch Dashboards**.

## Features

- SIP routing with **Kamailio**
- Containerized SIP endpoints using **PJSUA**
- Real time SIP event collection
- Log parsing with **Fluent Bit**
- Search and visualization with **OpenSearch**
- Dashboarding with **OpenSearch Dashboards**
- Indexed SIP events:
  - `sip_invite`
  - `sip_answer`
  - `sip_bye`

---

## Architecture

```text
SIP Clients
   -> Kamailio
      -> JSON SIP logs
         -> Fluent Bit
            -> OpenSearch
               -> OpenSearch Dashboards
```

---

## Project Structure

```text
.
├── docker-compose.yml
├── README.md
├── kamailio-default/
│   └── kamailio.cfg
├── fluent-bit/
│   ├── fluent-bit.conf
│   └── parsers.conf
└── sngrep/
    └── Dockerfile
```

---

## Prerequisites

Make sure you have the following installed:

- Docker
- Docker Compose

---

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/osherk2001/sip-with-kamailio.git
cd sip-with-kamailio
```

### 2. Create a `.env` file

Create a `.env` file in the project root:

```env
OPENSEARCH_INITIAL_ADMIN_PASSWORD=YourStrongPasswordHere
```

### 3. Start the stack

```bash
docker compose up -d
```

### 4. Verify containers

```bash
docker compose ps
```

---

## Main Components

### Kamailio
Acts as the SIP proxy and routes SIP traffic between clients.

### PJSUA Clients
Containerized SIP clients used to place and receive calls.

### Fluent Bit
Reads Docker logs, extracts SIP related events, and forwards them to OpenSearch.

### OpenSearch
Stores parsed SIP events in the `sip-calls` index.

### OpenSearch Dashboards
Used to explore events and build dashboards.

---

## Example Indexed Event

```json
{
  "@timestamp": "2026-04-14T18:15:27.731Z",
  "event": "sip_invite",
  "call_id": "gfPVEQpblaVTsybBWgH0ZqXo2uH8O6Pf",
  "from_uri": "sip:192.168.254.100",
  "to_uri": "sip:1@172.16.254.100",
  "src_ip": "192.168.254.100",
  "src_port": "5060",
  "ruri": "sip:osherk@172.16.254.100:5060"
}
```

---

## Accessing OpenSearch Dashboards

Open:

```text
http://localhost:5601
```

Login with:

```text
Username: admin
Password: value from .env
```

---

## Create a Data View

In OpenSearch Dashboards, create a data view with:

```text
sip-calls*
```

Use the following time field:

```text
@timestamp
```

---

## Useful Discover Queries

Show all call invites:

```text
event:"sip_invite"
```

Show all answered calls:

```text
event:"sip_answer"
```

Show all call terminations:

```text
event:"sip_bye"
```

Search by call ID:

```text
call_id:"YOUR_CALL_ID"
```

---

## Recommended Dashboard

A simple dashboard can include:

- Total `sip_invite`
- Total `sip_answer`
- Total `sip_bye`
- SIP activity over time
- Recent SIP events table

Suggested layout:

1. Top row with 3 counters
2. Middle row with time based activity chart
3. Bottom row with recent events table

---

## Useful Commands

List indices:

```bash
curl -k -u "admin:YOUR_PASSWORD" https://localhost:9200/_cat/indices?v
```

Search SIP events:

```bash
curl -k -u "admin:YOUR_PASSWORD" https://localhost:9200/sip-calls/_search?pretty
```

Watch Fluent Bit logs:

```bash
docker logs -f fluent-bit
```

Watch Kamailio logs:

```bash
docker logs -f kamailio-edge
```

---

## Troubleshooting

### No events in Dashboards
- Make sure `sip-calls*` data view exists
- Check the selected time range
- Verify documents exist in OpenSearch
- Confirm Fluent Bit is running

### No SIP documents in OpenSearch
Check:

```bash
docker logs -f fluent-bit
docker logs -f kamailio-edge
```

### OpenSearch authentication issues
Verify that the password in `.env` matches the one used by the containers.

### PowerShell note
In PowerShell, use `curl.exe` instead of `curl` when querying OpenSearch:

```powershell
curl.exe -k -u "admin:YOUR_PASSWORD" "https://localhost:9200/_cat/indices?v"
```

---

## What This Project Demonstrates

- Docker based SIP lab setup
- SIP proxying with Kamailio
- Structured SIP logging
- Real time log shipping
- OpenSearch indexing and querying
- Dashboard creation for call monitoring

---

## Notes

- It is better to pin OpenSearch images to a fixed version instead of `latest`
- Store secrets in `.env`
- The project can be extended with TLS, alerting, and more advanced call analytics

---

## License

For educational and demonstration purposes.
