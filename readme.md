# WEBSOCKET


```javascript
// server side code
import WebSocket, { WebSocketServer } from "ws";
import http from 'http'

const server = http.createServer(function (request: any, response: any) {
    console.log((new Date() + 'Received request for '+ request.url));
    response.end("hi there");
})

const wss = new WebSocketServer({ server });

wss.on('connection', function connection(ws){
    ws.on('error', console.error)

    ws.on('message', function message(data, isBinary){
        wss.clients.forEach(function each(client) {
            if(client.readyState === WebSocket.OPEN) {
                client.send(data, {binary: isBinary});
            }
        })
    })

    ws.send('Hello! Message from server!!')
})

server.listen(8080, function() {
    console.log((new Date()) + 'Server is listening on port: 8080')
})
```


```javascript
// client side code
import { useEffect, useState } from 'react'
import './App.css'

function App() {
  const [socket, setSocket] = useState<null | WebSocket>(null);
  const [messages, setMessages] = useState([])
  const [message, setMessage] = useState<string>('') 

  useEffect(() => {
    const socket = new WebSocket('ws://localhost:8080')
    socket.onopen = () => {
      console.log('Connected')
      setSocket(socket);
    }

    socket.onmessage = (message) => {
      console.log('Received message: '+  message.data);
      setMessages(mssg => [...mssg, message.data])
    }

  },[])

  if(!socket){
    return <div>Connection to socket server...</div>
  }
  return (
    <>
      <h1>All Messages</h1>
      {messages && messages.map((msg, index) => (<ul> <li key={index}>{msg}</li></ul>))}
      <h2>Send message</h2>
      <input type='text' value={message} onChange={(e) => setMessage(e.target.value)}/>
      <button onClick={() => socket.send(message)}>Send</button>
    </>
  )
}

export default App
```