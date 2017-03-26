# chat app demo

---

Create a new Phoenix app

Note:
`mix phoenix.new APP_NAME`

---

Set up the channel in `web/channels/user_socket.ex`

---

Create `web/channels/room_channel.ex` and write the RoomChannel module

Note:
```
defmodule HelloPhoenix.RoomChannel do
  use Phoenix.Channel

  def join("room:lobby", _message, socket) do
    {:ok, socket}
  end
  def join("room:" <> _private_room_id, _params, _socket) do
    {:error, %{reason: "unauthorized"}}
  end
end
```
---

Set up `web/static/js/socket.js` so that its channel matches the
RoomChannel module

---

`import from "./socket"` in `web/static/js/app.js`

---

Set up `web/templates/page/index.html.eex` to hold a list of chat
messages and an input to create new ones

Note:
```
<ul id="messages"></ul>
<input id="chat-input" type="text"></input>
```
---

Add event listeners to `web/static/js/socket.js` to write messages to
the `ul` and read messages from the input

Note:
```
let channel           = socket.channel("room:lobby", {})
let chatInput         = document.querySelector("#chat-input")
let messagesContainer = document.querySelector("#messages")

chatInput.addEventListener("keypress", event => {
  if(event.keyCode === 13){
    channel.push("new_msg", {body: chatInput.value})
    chatInput.value = ""
  }
})

channel.on("new_msg", payload => {
  let messageItem = document.createElement("li");
  messageItem.innerText = `[${Date()}] ${payload.body}`
  messagesContainer.appendChild(messageItem)
})

channel.join()
  .receive("ok", resp => { console.log("Joined successfully", resp) })
  .receive("error", resp => { console.log("Unable to join", resp) })

export default socket
```
---

Set up the room channel to broadcast new messages to all subscribers

Note:
```
def handle_in("new_msg", %{"body" => body}, socket) do
  broadcast! socket, "new_msg", %{body: body}
  {:noreply, socket}
end
```
---

all done!

Note:
next up is adding auth a lá https://meatherly.github.io/2015/05/11/phoenixauthentication/ and http://nithinbekal.com/posts/phoenix-authentication/
