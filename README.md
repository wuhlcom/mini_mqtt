[![Code Climate](https://codeclimate.com/github/antico5/mini_mqtt/badges/gpa.svg)](https://codeclimate.com/github/antico5/mini_mqtt)
[![Test Coverage](https://codeclimate.com/github/antico5/mini_mqtt/badges/coverage.svg)](https://codeclimate.com/github/antico5/mini_mqtt/coverage)
[![Issue Count](https://codeclimate.com/github/antico5/mini_mqtt/badges/issue_count.svg)](https://codeclimate.com/github/antico5/mini_mqtt)
[![Build Status](https://travis-ci.org/antico5/mini_mqtt.svg?branch=master)](https://travis-ci.org/antico5/mini_mqtt)

# MiniMqtt

A full ruby implementation of the client side of MQTT protocol. 

The philosophy behind this gem is to keep the code as minimal and tidy as possible, and to completely avoid dependencies.


## Features

* Supports all MQTT features up to QoS 1.
* 450 lines of code
* 98% test coverage
* Doesn't store messages once the connection has been closed/lost.

## Installation

`gem 'mini_mqtt'`


## How to test
Integration tests will hit mosquitto's test broker test.mosquitto.org.

`rake test`

## Usage

```ruby
require 'mini_mqtt'
```

### Create client instance
Possible params are host, port, user, password, keep_alive (seconds), client_id, and clean_session
client_id defaults to random client id
clean_session defaults to true
keep_alive defaults to 10

```ruby
client = MiniMqtt::Client.new host: 'test.mosquitto.org'
```

### Establish connection
Options are will_topic, will_message, will_retain(false) and will_qos(0)

```ruby
client.connect
```

You can check at any time if client is connected

```ruby
puts client.connected?
```

### Publish messages

```ruby
# Regular publish
client.publish '/topic', 'hello'

# Publish with retain
client.publish '/other_topic', 'retained_message', retain: true

# Publish with qos
client.publish '/qos_topic', 'message', qos: 1
```

### Subscribe to topics

```ruby
# Single topic
client.subscribe '/topic'

# Multiple topics
client.subscribe '/topic', '/other_topic'

# Specifying max qos for topic (default 0)
client.subscribe '/topic', '/qos_topic' => 1
```

### Get messages
The caller of these methods is blocked until a message arrives, or the connection is lost.

```ruby
# Get a single message
msg = client.get_message
puts msg.message, msg.topic, msg.qos, msg.retain, msg.packet_id, msg.dup

# Get messages in an infinite loop. Breaks if connection is lost.
client.get_messages do |msg, topic|
  puts "Received #{ msg } on topic #{ topic }"
end
```

### Gracefully disconnect
```ruby
client.disconnect
```
