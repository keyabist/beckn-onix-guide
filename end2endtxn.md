# End-to-End Transaction Testing Guide

This guide walks you through a complete Beckn Protocol transaction cycle on a local Onix setup — from discovery to order confirmation and post-fulfillment.

---

## ✅ Pre-requisites

- Onix network is running (BAP, BPP, Registry, Gateway)
- Registry status is `SUBSCRIBED` for BAP/BPP
- `retail:1.1.0` domain is added
- Layer 2 config is optional but recommended
- Redis/Mongo are active
- Postman is installed

---

## 📦 Import Postman Collection

👉 [Local Retail Sandbox Collection](https://github.com/beckn/beckn-sandbox/blob/main/artefacts/local-retail/Local-Retail-Sandbox-110.postman_collection.json)

---

## 🌐 Setup Postman Environment

Create or edit an environment with the following variables:

| Key                   | Example Value             |
|-----------------------|---------------------------|
| `bap_client_url`      | `http://localhost:5001`   |
| `bap_uri`             | `http://bap-network:5001` |
| `bap_id`              | `bap-network`             |
| `bpp_id`              | `bpp-network`             |
| `bpp_uri`             | `http://localhost:6002`   |


---

## 🧪 Run the Transaction Flow

Send the requests in the following sequence:

1. `search`
2. `select`
3. `init`
4. `confirm`
5. *(Optional)*: `status`, `cancel`, `track`

> ⚠️ Always generate new `transaction_id` and `message_id` for clean testing.

---

## 🪵 Logs & Debugging

Use Docker logs to trace callbacks:

```bash
docker logs -f bap-client
docker logs -f bpp-network
docker logs -f gateway
docker logs -f registry
```

---

## 🧹 Clear Redis (Optional)

If your flow seems stuck or returns cached results:

```bash
docker exec -it redis redis-cli FLUSHALL
```

---

🎉 You now have a successful end-to-end Beckn protocol transaction running on your local Onix setup.

