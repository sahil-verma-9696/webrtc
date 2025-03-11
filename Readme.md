# 📹 WebRTC Video Call Setup Guide

This guide helps **anyone** (technical or non-technical) set up a WebRTC video call easily!

---

## 🚀 How to Use

### 1️⃣ Start Your Camera & Microphone 🎥🎤
Click the **"Allow"** button when prompted.

```js
startMyStream();
```

➡ **Your video appears on the left side**

---

### 2️⃣ Create an Offer 📡
🔹 Click **"Create Offer"** button.
🔹 Copy the generated **Offer**.

```js
createOffer();
```

⬇️ **Share Offer with the other person**

---

### 3️⃣ Accept an Offer & Create an Answer ✅
🔹 Paste the received **Offer**.
🔹 Click **"Set Remote Offer"**.
🔹 Copy & share the generated **Answer**.

```js
setRemoteOffer();
```

➡ **Send the Answer back** ⬅️

---

### 4️⃣ Set the Remote Answer 📨
🔹 Paste the received **Answer**.
🔹 Click **"Set Remote Answer"**.

```js
setRemoteAnswer();
```

📞 **Connection Established! 🎉**

---

### 5️⃣ Exchange ICE Candidates ❄️
🔹 Copy your **ICE Candidates** & send them.
🔹 Paste received **ICE Candidates** & click **"Add ICE Candidates"**.

```js
addIceCandidates();
```

🔄 **This helps improve connection stability.**

---

## 🎯 Summary of Data Exchange 🔄

**Offerer (You) 🏠 → Answerer 🏠**
1. 📡 **Offer** ➡️ **(Send)**
2. ✅ **Answer** ⬅️ **(Receive & Set)**
3. ❄️ **ICE Candidates** 🔄 **(Exchange for better connection)**

💡 **Now, you can enjoy real-time video calling!** 🎥📞

