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

client.on('users/#', function (data, topic) {
  console.log(data.alias) // 'schroedinger'
  // find users in database by alias...
  client.reply(topic, { status: '404 Not found' })
})
```

### Replyer scheme
This package is real time communication similar trying to join the best of both [socket.io](http://socket.io/) and MQTT.

It implements request and response with a similar API as in `socket.io` to ease refactor. All clients are listening on their own id path as:

#### `@client-id/message-id`

> The ‘at’ symbol identifies a message as a MQTT request: it has to be addressed only to that particular client and request. #msg-id is an optional parameter to ensure that a certain reply is for a very particular request. Under some conditions [we can count on this](http://stackoverflow.com/questions/30955110/is-message-order-preserved) (qos > 0). Messages may get lost, for which timeouts are implemented. In case of a series of packets lost before a timeout, a client may interpret a wrong order, thus failing. So this little overhead for request/reply is really useful.

Replyer requests must indicate –somehow– the `clientId` and the `messageId`. We have to take into account the the messageId parameter in the packet is not always mandatory (qos 0), so depending in `qos` for a request we have or have not to append it.

#### `mqtt/api/path` + `@client-id/message-id`

On a request if `qos === 2` message id would never be necessary.

### MQTT URLs
Now that we can make requests to an API and receive an answer on plain MQTT protocol, we could make use of HTTP URL scheme to indicate resources.

This is the skeleton of a MQTT URL
#### `mqtt://host:port/path/to/api/call/@issuer/mid` as emitter URL
#### `mqtt://host:port/@issuer/mid/` as listener URL

## Next steps
* [ ] Use MqEmitter to better listen for requests
* [ ] Improve scheme and API
* [ ] Increase examples and tests
* Write blog post

---
The code has tried to follow MQTT best practices as in http://www.hivemq.com/blog/mqtt-essentials-part-5-mqtt-topics-best-practices
