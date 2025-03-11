# WebRTC ( Web Real time communication) blog

- `boilerplate code`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>WebRTC | Blog</title>
  </head>
  <body></body>
  <script src="script.js"></script>
</html>
```

- Declaring some important variables

- `script.js`

```js
let localStream; // variable for local stream
let remoteStream; // variable to store remote stream
let peerConnection; // for instance of RTCPeerConnection interface
let iceCandidates = []; // holding ICE (Interactive Connectivity Establishment) Devices exchange network addresses, called ICE candidates, to find the most efficient path for a connection, on `Internet`
```

- `set the STUN or TURN servers configurations`

```js
const config = { iceServers: [{ urls: "stun:stun.l.google.com:19302" }] };
```

- `Important required functions`

1.  startMyStream
2.  createPeerConnection
3.  createOffer
4.  setRemoteOffer
5.  setRemoteAnswer
6.  addIceCandidates

- `Utility functions`

1. showNotification
2. copyToClipboard

### 1\. Starting Your Local Media Stream

Before you can share video or audio with another peer, you need to capture your local media. The `startMyStream` function does just that:

- `1. startMyStream`

```js
async function startMyStream() {
  localStream = await navigator.mediaDevices.getUserMedia({
    video: true,
    audio: true,
  });
  document.getElementById("myStream").srcObject = localStream;
  showNotification("Local stream started");
}
```

**What It Does:**

- **Capturing Media:**
  The function uses navigator.mediaDevices.getUserMedia to request access to the camera and microphone. This returns a media stream (localStream) containing both video and audio.

- **Displaying Your Stream:**
  Once the stream is obtained, it is assigned to the `srcObject` property of a media element (such as a `<video>` tag) with the ID `"myStream"`. This allows you to see your own video feed in the browser.

- **User Notification:**
  A notification is displayed (via showNotification) to inform you that the local stream has started.

### 2. Creating the Peer Connection

- `2. createPeerConnection`
  The next step is to create an `RTCPeerConnection` which serves as the `backbone` of the WebRTC connection. The `createPeerConnection` function sets up the connection and `defines event handlers` to manage incoming streams and ICE candidates:

```js
async function createPeerConnection() {
  peerConnection = new RTCPeerConnection(config);
  localStream
    .getTracks()
    .forEach((track) => peerConnection.addTrack(track, localStream));
  peerConnection.ontrack = (event) => {
    document.getElementById("remoteStream").srcObject = event.streams[0];
    showNotification("Remote stream received");
  };
  peerConnection.onicecandidate = (event) => {
    if (event.candidate) {
      iceCandidates.push(event.candidate);
      document.getElementById("iceCandidatesBox").value =
        JSON.stringify(iceCandidates);
    }
  };
}
```

**Key Points:**

- **Establishing the Connection:**
  An `RTCPeerConnection` object is instantiated using a configuration object `(config)`. This configuration typically includes details like `ICE server URLs`, which are essential for NAT traversal.

- **Adding Local Tracks:**
  Each track (`audio` or `video`) from the local stream is added to the `peer connection`. This ensures that when the connection is established, `your media is sent to the remote peer`.

- **Handling Remote Media:**
  The `ontrack` event is triggered when **remote media arrives**. The **incoming stream** is assigned to an element with the ID `"remoteStream"` so that you can view the other party’s video.

- **Exchanging ICE Candidates:**
  As the connection gathers potential network paths (ICE candidates), the `onicecandidate` event is fired. The candidates are stored in an array (iceCandidates) and displayed in a designated element, `facilitating the signaling process`.

### 3. Creating an Offer

- `3. createOffer`
  To initiate the connection, one peer needs to create an offer. This is done in the `createOffer` function:

```js
async function createOffer() {
  await createPeerConnection();
  const offer = await peerConnection.createOffer();
  await peerConnection.setLocalDescription(offer);
  document.getElementById("offerBox").value = JSON.stringify(offer);
  showNotification("Offer created, copy and share");
}
```

**How It Works:**

- **Peer Connection Setup:**
  The function starts by calling `createPeerConnection` to ensure the connection and local tracks are ready.

- **Generating the Offer:**
  Using `peerConnection.createOffer()`, the function generates an SDP (Session Description Protocol) `offer` that describes the media capabilities and connection details.

- **Setting the Local Description:**
  The `generated offer` is then set as the local description, which informs the browser of the session parameters.

- **Sharing the Offer:**
  The offer is converted into a JSON string and placed into an HTML element with the ID `"offerBox"`. This allows you to copy the offer and share it with the remote peer via `your signaling mechanism.`

- **User Notification:**
  A notification indicates that the offer has been created and is ready to be shared.

### 4. Receiving the Offer and Creating an Answer

On the `remote peer`, the offer must be received and answered. The setRemoteOffer function handles this process:

- `4. setRemoteOffer`

```js
async function setRemoteOffer() {
  await createPeerConnection();
  const offer = JSON.parse(document.getElementById("pasteOfferBox").value);
  await peerConnection.setRemoteDescription(new RTCSessionDescription(offer));
  const answer = await peerConnection.createAnswer();
  await peerConnection.setLocalDescription(answer);
  document.getElementById("answerBox").value = JSON.stringify(answer);
  showNotification("Answer created, copy and share");
}
```

**Detailed Explanation:**

- **Creating the Peer Connection:**
  Similar to the offer creation process, this function begins by setting up the peer connection.

- **Setting the Remote Description:**
  The remote offer (received from the initiating peer) is parsed from an HTML element (pasteOfferBox) and set as the remote description. This informs the browser about the connection parameters and media capabilities of the remote peer.

- **Generating the Answer:**
  With the remote offer in place, peerConnection.createAnswer() generates an answer that acknowledges and agrees upon the connection parameters.

- **Finalizing Local Description:**
  The generated answer is set as the local description and then output as a JSON string in an element with the ID "answerBox", ready to be shared back.

- **User Notification:**
  A notification is shown to inform that the answer has been created and is ready for sharing.

### 5. Finalizing the Connection with the Remote Answer

Once the initiating peer receives the answer from the remote side, it must set it as the remote description. The `setRemoteAnswer` function takes care of this step:

- `5. setRemoteAnswer`

```js
async function setRemoteAnswer() {
  const answer = JSON.parse(document.getElementById("pasteAnswerBox").value);
  await peerConnection.setRemoteDescription(new RTCSessionDescription(answer));
  showNotification("Answer set, connection established");
}
```

**Key Steps:**

- **Receiving the Answer:**
  The function retrieves the answer from an HTML element (pasteAnswerBox) and parses it.

- **Setting Remote Description:**
  The parsed answer is used to set the remote description on the peer connection. This completes the handshake process between the peers.

- **Connection Established:**
  Once the remote description is set, the connection is considered established, allowing real-time media exchange.

- **Notification:**
  A final notification confirms that the connection has been successfully established.

### 6. Exchanging ICE Candidates

To ensure that both peers can communicate even behind firewalls or NATs, ICE (Interactive Connectivity Establishment) candidates need to be exchanged. The addIceCandidates function handles this:

- `6. addIceCandidates`

```js
async function addIceCandidates() {
  const candidates = JSON.parse(
    document.getElementById("pasteIceCandidatesBox").value
  );
  candidates.forEach(async (candidate) => {
    await peerConnection.addIceCandidate(new RTCIceCandidate(candidate));
  });
  showNotification("ICE Candidates added");
}
```
**What Happens Here:**
- **Receiving ICE Candidates:**
ICE candidates—potential network routes—are received as a JSON string from an element with the ID "pasteIceCandidatesBox".

- **Adding Candidates to the Connection**:
Each candidate is parsed and added to the peer connection using peerConnection.addIceCandidate. This step helps negotiate the best possible connection route between the peers.

- **Confirmation:**
After processing all candidates, a notification confirms that the ICE candidates have been added, which aids in stabilizing the connection.