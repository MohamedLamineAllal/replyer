# Replyer
# Node.js MQTT client with built-in request and response methods

Replyer is a wrapper of [MQTT.js](https://github.com/mqttjs/MQTT.js) client that expands its API with common use cases. It is completely compatible and interoperable, so in most projects you can upgrade by dropping:

```
var mqtt = require('replyer')
```
instead of ~~`var mqtt = require('mqtt')`~~ or ~~`socket.io`~~.

### Hello world
```javascript
var mqtt = require('replyer')
var client  = mqtt.connect('mqtt://test.mosca.io')

client.on('connect', function () {
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
var client  = mqtt.connect('mqtt://test.mosca.io')

client.on('connect', function () {
  console.log('connected')
})

client.request('users', { alias: 'schroedinger' }, function (data) {
  console.log(data) // will be ['schroedinger']
})

client.on('users', function (data, packet) {
  console.log(data.alias) // 'schroedinger'
  // find users in database by alias...
  packet.reply({ id: 1725, alias: 'schroedinger', avatar: 'icon.png' })
  client.end()
})
```

### Replyer scheme
This package is real time communication similar trying to join the best of both [socket.io](http://socket.io/) and MQTT.

It implements request and response with a similar API as in `socket.io` to ease refactor. All clients are listening on their own id path as:

#### `@client-id/message-id`

> The ‘at’ symbol identifies a message as a MQTT request: it has to be addressed only to that particular client. #msg-id is an optional parameter to ensure that a certain reply is for a very particular request. In usual conditions [we can count on this](http://stackoverflow.com/questions/30955110/is-message-order-preserved). Messages may get lost, for which timeouts are implemented. In case of a series of packets lost before a timeout, a client may interpret a wrong order, thus failing. So this little overhead for request/reply is really useful.

Replyer requests must indicate –somehow– the `clientId`.

#### `mqtt/api/path` + `@client-id`

This way we can know if it is part of replyer _protocol_ and if the field is not present, fallback to MQTT.

### MQTT URLs
Now that we can make requests to an API and receive an answer on plain MQTT protocol, we could make use of HTTP URL scheme to indicate resources.

This is the skeleton of a MQTT URL
#### `mqtt://host:port/path/to/api/call/@issuer`
#### `mqtt://host:port/@issuer/message-id/`

## Next steps
* Use MqEmitter to better listen for requests
* Improve scheme and API
* Increase examples and tests
* Write article

---
The code has tried to follow MQTT best practices as in http://www.hivemq.com/blog/mqtt-essentials-part-5-mqtt-topics-best-practices
