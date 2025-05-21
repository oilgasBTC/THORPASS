# THORPASS
**THORPASS** system as a practical, privacy-conscious, decentralized solution to the **node operator notification problem**:

---

## 📣 **How Node Operators Get Notified — THORPASS Explained**

> **Developer concern:**
> *“How would node operators get notified though? There’s no on-chain registry of handles to contact them on.”*

Totally valid. Right now, Thorchain lacks a native way to coordinate with node operators because there’s:

* No on-chain identity layer (no ENS-style profile or DNS)
* No contact or messaging system
* No formal, off-chain directory of operators who are open to coordination

This is exactly what **THORPASS** is designed to solve — without compromising decentralization, privacy, or wallet UX.

---

## 🔐 **What Is THORPASS?**

**THORPASS** is a lightweight identity + notification system that allows node operators to **opt in** by publishing a signed profile (`thorpass.json`) that dApps (like Vultisig) can use to:

* Identify node operators who want to be reachable
* Understand how they want to be contacted
* Notify them in a non-intrusive, secure, and structured way

No on-chain change is required.

---

## 📦 **How It Works (Step-by-Step)**

### 🟢 1. Node Operators Create a THORPASS Profile

Each node operator can publish a signed profile like this:

```json
{
  "node_address": "thor1xyzabc...",
  "operator_name": "OdinNode",
  "contact": {
    "email": "odin@valhallanodes.io",
    "discord": "Odin#1234",
    "webhook": "https://valhallanodes.io/notify"
  },
  "preferences": {
    "receiveWhitelistRequests": true
  },
  "signature": "0xabc123...(signed by node wallet)"
}
```

They host this file:

* In a public **GitHub repo**
* On **IPFS**
* (Later) in DNS TXT records or ENS

The signature proves authenticity — only the node operator can generate this file.

---

### 🟢 2. Vultisig (or another dApp) Fetches Profiles

When a user clicks **"Request Whitelist"**, Vultisig:

1. Selects the top 10 eligible node operators (e.g., by age or performance).
2. Pulls their THORPASS profiles from GitHub, IPFS, or a cached directory.
3. Verifies the signature of each file to ensure it’s authentic.
4. Sends notifications to the listed contact methods.

---

### 🟢 3. Notifications Are Sent via Operator Preferences

Depending on the operator’s preferences, Vultisig or a coordinating bot could:

* Send a **Discord DM or role mention**
* Send an **email** using a role-based inbox
* Trigger a **webhook** to their own infrastructure
* Add a **message to a shared approval dashboard**

All these are **opt-in** and privacy-respecting. There’s no public exposure of data unless the node operator chooses to be contacted.

---

## 🧠 Why This Is Safe and Scalable

| Concern                  | THORPASS Answer                                                               |
| ------------------------ | ----------------------------------------------------------------------------- |
| No on-chain contact info | THORPASS works off-chain with wallet-signed metadata                          |
| Spam/fake requests       | Profiles are cryptographically signed; only approved nodes are contacted      |
| Privacy                  | All fields are optional; operators control how/if they are contacted          |
| Centralization risk      | Anyone can host/verify THORPASS; GitHub/IPFS/DNS compatible                   |
| Notification fatigue     | Preferences let operators opt in only to the types of signals they care about |

---

## 🧩 Real-World Precedents

* Like [ENS Text Records](https://docs.ens.domains/ens-improvement-proposals/ensip-5-text-records), but wallet-signed and off-chain
* Similar to Keybase or GitHub signed identity files
* Similar to EIP-4361 “Sign-In with Ethereum” profiles

---

## 🚧 MVP Implementation Plan

1. Start with a public GitHub repo (`/thorpass-registry`) of `thorpass.json` profiles.
2. Provide a simple CLI or script to help node operators generate and sign profiles.
3. Vultisig integrates profile discovery + notification triggers.
4. Add Discord/webhook bots to route messages.

---

## 🚀 Future Enhancements

* Cache profiles via ENS or DNS
* Mirror to IPFS + ENS TXT records
* Add “last seen” or activity pings
* Add scoring (e.g., uptime, trust level)
* Build a public dashboard of participating nodes

---

## ✅ Summary

So — while there’s **no on-chain registry today**, THORPASS gives us a **secure, decentralized, opt-in identity layer** that dApps like Vultisig can use to solve the notification problem immediately. It balances:

* Trustless verification (via signatures)
* Privacy (via optional fields)
* Decentralization (via distributed storage)
* UX simplicity (for both users and operators)



---



**How can Vultisig send notifications to node operators** using THORPASS, in a way that’s practical, lightweight, and works **without any on-chain messaging**.

---

## 📣 **How Vultisig Can Send Notifications via THORPASS**

### 🔍 **Step-by-Step Overview**

1. **User clicks "Request Whitelist"** in Vultisig.
2. Vultisig:

   * Confirms wallet balance is ≥ 5,000 RUNE.
   * Identifies eligible node operators (e.g., top 10 oldest or best-performing).
3. Vultisig:

   * Pulls each eligible operator’s `thorpass.json` profile.
   * Verifies it was signed by the correct node wallet.
   * Checks if they’ve opted in to receive whitelist requests.
4. Vultisig **sends notifications** using contact methods from the profile.

---

## 📦 Contact Methods in `thorpass.json`

```json
"contact": {
  "email": "odin@valhallanodes.io",
  "discord": "OdinNode#1234",
  "webhook": "https://valhallanodes.io/notify"
}
```

Each of these is used by Vultisig as follows:

---

### 1. 📬 **Email Notification**

* Vultisig integrates with a simple email-sending service (e.g., SMTP, SendGrid, Mailgun).
* Sends a templated message like:

> “You’ve received a bonding whitelist request from THOR address thor1xyz… via Vultisig.”

✅ Easy
✅ Low-tech
❌ Slightly centralized unless you self-host

---

### 2. 🤖 **Discord DM or Bot Mention**

* Vultisig can run or interface with a **Discord bot**.
* If operator lists their Discord handle, bot can:

  * Send a direct message
  * Ping them in a validator Discord channel
* Message could contain:

  * Who requested
  * Their wallet
  * How much they want to bond
  * Approve/Reject links

✅ Community-native
✅ Fast
❌ Requires Discord bot permissions
❌ Not always DM-able (based on user settings)

---

### 3. 🌐 **Webhook Trigger**

* If the operator provides a webhook URL, Vultisig can `POST` a JSON payload to their server:

```json
{
  "type": "whitelist_request",
  "from": "thor1xyz...",
  "bond_amount": 7000,
  "message": "Excited to contribute to Thorchain!",
  "timestamp": "2025-05-21T14:32Z"
}
```

* The operator can receive this in real-time and handle it however they want.

✅ Programmable
✅ Real-time
✅ Private
❌ Operators must maintain a backend

---

### 4. 📥 **Public Dashboard Ping (Optional)**

* If all else fails, Vultisig can also:

  * Post to a **shared notification log or dashboard**
  * Show “pending whitelist request” for each eligible node

Node operators can check the dashboard manually, even if other methods fail.

✅ Always available
✅ Great fallback
❌ Not real-time unless polled
❌ Adds another UI layer

---

## 🔧 Developer Options Inside Vultisig

To send these notifications, Vultisig devs can:

* Use libraries like `nodemailer` for email
* Interface with the Discord API
* Use `fetch()` or `axios` to POST to webhooks
* Verify `thorpass.json` signatures with existing crypto libraries
* Cache `thorpass.json` files for performance

It’s **not complex**, and it’s all optional/opt-in for the operator side.

---

## 🧠 Bonus: Notification Logic in Pseudocode

```ts
for (node of eligibleNodes) {
  let profile = loadTHORPASS(node);
  if (verifySignature(profile)) {
    if (profile.preferences.receiveWhitelistRequests) {
      if (profile.contact.email) {
        sendEmail(profile.contact.email, requestPayload);
      }
      if (profile.contact.discord) {
        sendDiscordDM(profile.contact.discord, requestPayload);
      }
      if (profile.contact.webhook) {
        sendWebhook(profile.contact.webhook, requestPayload);
      }
    }
  }
}
```

---

## ✅ Summary

**Vultisig can notify node operators through any or all of these:**

| Method    | What It Does                     | Tech Needed                    |
| --------- | -------------------------------- | ------------------------------ |
| Email     | Sends a message to inbox         | SMTP or API (Mailgun/SendGrid) |
| Discord   | Sends DM or channel ping         | Discord bot + API              |
| Webhook   | Sends real-time data to a server | Simple POST request            |
| Dashboard | Posts to shared UI               | Optional web app / log viewer  |

All of these methods:

* Use data from the signed `thorpass.json` file
* Respect operator preferences
* Are secure, off-chain, and decentralized

---



