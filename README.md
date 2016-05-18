# Replyer
# Node.js MQTT client with built-in request and response methods

Replyer is a wrapper of [MQTT.js](https://github.com/mqttjs/MQTT.js) client that expands its API with common use cases. It is completely compatible and interoperable, so in most projects you can upgrade by dropping:

```
var mqtt = require('replyer')
```
instead of ~~`var mqtt = require('mqtt')`~~.

### Hello world
```javascript
var mqtt = require('replyer')
var client  = mqtt.connect('mqtt://test.mosquitto.org')

client.on('connect', function () {
  client.subscribe('chat')
  client.publish('chat', 'Hello replyer!')
})

client.on('chat', function (data) {
  console.log(data) // 'Hello replyer!'
  client.end()
})
```

### Request and replyer
```javascript
var mqtt = require('replyer')
var client  = mqtt.connect('mqtt://test.mosquitto.org')

client.on('connect', function () {
  console.log('connected')
})

client.emit('network', { device: 'sensor' }, function (response) {
  console.log(response)
})

client.on('network', function (data, message) {
  console.log(data.device) // 'sensor'
  message.reply('got it')
  client.end()
})
```

### Replyer scheme
This package is real time communication similar trying to join the best of both [socket.io](http://socket.io/) and MQTT.

It implements request and response with the same API as in `socket.io` to ease refactor. All clients are listening on their own id path as:

#### `@client-id/message-id` + `/usual/mqtt/path`

> The ‘at’ symbol identifies a message as a MQTT request: it has to be addressed only to that particular client. #msg-id is an optional parameter to ensure that a certain reply is for a very particular request. In usual conditions [we can count on this](http://stackoverflow.com/questions/30955110/is-message-order-preserved). Messages may get lost, for which timeouts are implemented. In case of a series of packets lost before a timeout, a client may interpret a wrong order, thus failing. So this little overhead for request/reply is really useful.
