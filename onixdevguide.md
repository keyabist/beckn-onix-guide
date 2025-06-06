# Beckn Onix – Local Setup & Developer Guide

This guide is a comprehensive walkthrough for developers who want to set up and run a Beckn-compliant network locally using [Beckn Onix](https://github.com/beckn/beckn-onix).

For a complete ONIX setup guide, refer to the [ONIX Dev Guide](https://github.com/beckn/beckn-onix/blob/main/docs/user_guide.md).

It provides detailed steps to configure your local environment, run containers, make key configuration changes, and perform end-to-end transactions using Postman.

---

## 🧠 Who This Is For

This guide is intended for:

- Developers new to the Beckn protocol
- Anyone contributing to or testing Beckn-based apps locally
- Those who have **basic knowledge of Docker, Postman, and Linux**

---

## 🧰 Prerequisites

Make sure you have the following installed:

- **Docker** and **Docker Compose**
- **Node.js** and **npm**
- **Postman**
- **Git**
- A Unix-based terminal (Linux/macOS or WSL on Windows)

---

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/beckn/beckn-onix.git
cd beckn-onix
```

### 2. Make the Script Executable

```bash
chmod +x beckn-onix.sh
```

### 3. Run the Setup Script

This command sets up the full network using Docker:

```bash
./beckn-onix.sh
```

Once completed, the containers for BAP, BPP, registry, gateway, etc. will be up and running.

---

👉 After you've completed this base setup, continue below for configuration changes and deeper usage.


# Setting Up a Beckn Network Locally

Here are some of the key commands to be used.

---

## 1. Configuration Changes

```bash
vi default.conf
:wq   # to save and exit
```

---

## 2. Container Commands

```bash
docker restart gateway
docker restart registry
docker compose down -v
docker stop <filename>  # eg: docker stop sandbox-api
```

---

## 3. Admin Login

Use the following credentials to log in:

- **Username**: `root`  
- **Password**: `root`

These commands were part of the configuration process for setting up the system, particularly when working with sandbox API and network configurations.

---

## 4. Remove Unused Docker Images

```bash
docker image ls
docker image prune
```

---

## 5. Change User Status to `SUBSCRIBED`

1. Login as admin (use `root`)
2. Go to the **Admin** tab
3. Click on **Network Participant**
4. Click on the **edit** icon for `bap-network`
5. Navigate to the **Network Role** tab
6. Click on the **edit** icon
7. Under **Status**, change to `SUBSCRIBED`
8. Click on **Done**

## 6. Adding a Network Domain

1. Go to the **Beckn** tab.
2. Click on **Network Domain**.
3. Click on **Add**.
4. Fill in the fields:
   - **Name**: `retail:1.1.0`
   - **Description**: `retail:1.1.0`
5. Click on **Save & More**.

---

## 7. Import Postman Collection

If you want to test locally and perform an end-to-end transaction, you can import the following Postman collection into Postman:

👉 [Local Retail Sandbox: Postman Collection](https://github.com/beckn/beckn-sandbox/blob/main/artefacts/local-retail/Local-Retail-Sandbox-110.postman_collection.json)

---

# Editing `config/default.yml` for Beckn Adapters

Each Beckn Adapter (also known as a Protocol Server or PS) has its own configuration file that defines runtime behavior such as telemetry, Layer 2 usage, etc.

To disable telemetry or modify runtime configs, follow these steps for each Protocol Server container.

---

## 1. Open the Config File

Inside the terminal, run:

```bash
docker exec -it sandbox-api sh
vi config/default.yml
```

---

## 2. Delete Existing Block

- Press the `i` key to enter insert mode.
- Move your cursor to the **bottom-most line** of the YAML file.
- Hold down the `d` key and **move the cursor upward** until you've deleted everything up to the `telemetry` block.

---

## 3. Paste the Following Configuration

Replace the deleted section with:

```yaml
telemetry:
    enabled: false
    url: ""
    batchSize: 100
    # In minutes
    syncInterval: 30
    redis_db: 3
useLayer2Config: false
mandateLayer2Config: false
```

---

## 4. Save and Exit

- Press `Esc`
- Type `:wq` and hit Enter to save and quit

---

## 5. Restart Processes

Once you've exited `config/default.yml`, run:

```bash
pm2 restart 0 1 2
exit
```

---

## 6. Repeat for Other Services

Repeat the same process for the following services by replacing `sandbox-api` in the first command:

- `bap-client`
- `bap-network`
- `bpp-client`
- `bpp-network`

**Example**:

```bash
docker exec -it bap-client sh
```

---
## For Debugging

```bash
docker logs -f bap-client  # or bpp-network etc.
