Here's a solution for implementing a **“blocked-by list”** (i.e., a list of users who have blocked a given user) using Agora Chat:

---

### ✅ **The Problem**
Agora Chat provides:
1. Each user's own block list (users they have blocked).
2. HTTP webhook events for `block` and `unblock` actions.

But it **does not** provide a built-in API to get a list of users who have blocked a particular user.

---

### 💡 **Proposed Solution: Build a Reverse Index Using Webhook Events**

#### 1. **Enable and listen to block/unblock webhook events**
You’ll receive webhook events like:

```json
{
  "action": "user.block",
  "from": "userA",
  "to": ["userB"]
}
```

This means `userA` has blocked `userB`.

Similarly, there is `user.unblock` for unblock events.

#### 2. **Maintain a "blocked-by" mapping in your backend**
In your server or database, maintain a data structure like this:

| Blocked User | Blocked By       |
|--------------|------------------|
| userB        | userA            |
| userC        | userA, userD     |

For every `user.block` event, **add a reverse mapping**: `to` is the blocked user, and `from` is the one who blocked them.

For every `user.unblock` event, **remove the mapping**.

#### 3. **Query the "blocked-by list" when needed**
Anytime you want to check which users have blocked a specific user, just query this mapping table.

---

### 🧠 Example Pseudocode
```go
func handleWebhook(event WebhookEvent) {
    switch event.Action {
    case "user.block":
        for _, blockedUser := range event.To {
            addBlockedBy(blockedUser, event.From)
        }
    case "user.unblock":
        for _, unblockedUser := range event.To {
            removeBlockedBy(unblockedUser, event.From)
        }
    }
}
```
