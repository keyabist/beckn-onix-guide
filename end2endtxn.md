# 🧪 End-to-End Transaction Testing Guide

This guide walks you through a complete Beckn Protocol transaction cycle on a local Onix setup — from discovery to order confirmation and post-fulfillment.

---

## ✅ Pre-requisites

Ensure the following before testing:

- Beckn Onix containers are running (`bap`, `bpp`, `gateway`, `registry`)
- BAP/BPP status in Registry is set to `SUBSCRIBED`
- Domain `retail:1.1.0` is added in the Registry
- Postman is installed

> Optional (but recommended): Layer 2 config installed for retail domain.

---

## 📦 Import Postman Collection

Import this into Postman:

👉 [Local Retail Sandbox – Postman Collection](https://github.com/beckn/beckn-sandbox/blob/main/artefacts/local-retail/Local-Retail-Sandbox-110.postman_collection.json)

---

## 🌐 Set Up Postman Environment

Create or modify your environment with the following variables:

| Key               | Example Value             |
|-------------------|---------------------------|
| `bap_client_url`  | `http://localhost:5001`   |
| `bap_uri`         | `http://bap-network:5001` |
| `bap_id`          | `bap-network`             |
| `bpp_id`          | `bpp-network`             |
| `bpp_uri`         | `http://localhost:6002`   |

Set these in the **Variables** tab of the collection or Postman environment.

---

## 🔁 Run Transaction Flow

Call the following requests in order:

1. `search`
2. `select`
3. `init`
4. `confirm`
5. *(Optional)*: `status`, `cancel`, `track`

> 🧠 Always use fresh `message_id` and `transaction_id` per test run.

---

## 🪵 Logs & Debugging

If you're not seeing expected responses, check Docker logs:

```bash
docker logs -f bap-client
docker logs -f bpp-network
docker logs -f gateway
docker logs -f registry
```

---

## 🧹 Clear Redis (if stuck)

Clear cache if you suspect stale results or stuck message flows:

```bash
docker exec -it redis redis-cli FLUSHALL
```

---

🎉 You now have a working Beckn transaction running end-to-end on your local Onix setup. Explore further flows or plug in your own client logic!
