---
layout: page
title: Spark Structured Streaming MQTT
description: Spark Structured Streaming MQTT
group: nav-right
---
<!--
{% comment %}
Licensed to the Apache Software Foundation (ASF) under one or more
contributor license agreements.  See the NOTICE file distributed with
this work for additional information regarding copyright ownership.
The ASF licenses this file to you under the Apache License, Version 2.0
(the "License"); you may not use this file except in compliance with
the License.  You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
{% endcomment %}
-->

{% include JB/setup %}


[MQTT](http://mqtt.org/) is MQTT is a machine-to-machine (M2M)/"Internet of Things" connectivity protocol. It was designed as an extremely lightweight publish/subscribe messaging transport. It is useful for connections with remote locations where a small code footprint is required and/or network bandwidth is at a premium.

## Linking

Using SBT:

    libraryDependencies += "org.apache.bahir" %% "spark-streaming-mqtt" % "2.1.1"

Using Maven:

    <dependency>
        <groupId>org.apache.bahir</groupId>
        <artifactId>spark-streaming-mqtt_2.11</artifactId>
        <version>2.1.1</version>
    </dependency>

This library can also be added to Spark jobs launched through `spark-shell` or `spark-submit` by using the `--packages` command line option.
For example, to include it when starting the spark shell:

    $ bin/spark-shell --packages org.apache.bahir:spark-streaming-mqtt_2.11:2.1.1

Unlike using `--jars`, using `--packages` ensures that this library and its dependencies will be added to the classpath.
The `--packages` argument can also be used with `bin/spark-submit`.

This library is cross-published for Scala 2.10 and Scala 2.11, so users should replace the proper Scala version (2.10 or 2.11) in the commands listed above.

## Configuration options.

This source uses the [Eclipse Paho Java Client](https://eclipse.org/paho/clients/java/). Client API documentation is located [here](http://www.eclipse.org/paho/files/javadoc/index.html).

 * `brokerUrl` A url MqttClient connects to. Set this as the url of the Mqtt Server. e.g. tcp://localhost:1883.
 * `storageLevel` By default it is used for storing incoming messages on disk.
 * `topic` Topic MqttClient subscribes to.
 * `topics` List of topics MqttClient subscribes to.
 * `clientId` clientId, this client is assoicated with. Provide the same value to recover a stopped client.
 * `QoS` The maximum quality of service to subscribe each topic at. Messages published at a lower quality of service will be received at the published QoS. Messages published at a higher quality of service will be received using the QoS specified on the subscribe.
 * `username` Sets the user name to use for the connection to Mqtt Server. Do not set it, if server does not need this. Setting it empty will lead to errors.
 * `password` Sets the password to use for the connection.
 * `cleanSession` Setting it true starts a clean session, removes all checkpointed messages by a previous run of this source. This is set to false by default.
 * `connectionTimeout` Sets the connection timeout, a value of 0 is interpreted as wait until client connects. See `MqttConnectOptions.setConnectionTimeout` for more information.
 * `keepAlive` Same as `MqttConnectOptions.setKeepAliveInterval`.
 * `mqttVersion` Same as `MqttConnectOptions.setMqttVersion`.


## Examples

### Scala API

You need to extend `ActorReceiver` so as to store received data into Spark using `store(...)` methods. The supervisor strategy of
this actor can be configured to handle failures, etc.

    val lines = MQTTUtils.createStream(ssc, brokerUrl, topic)
    val lines = MQTTUtils.createPairedStream(ssc, brokerUrl, topic)

Additional mqtt connection options can be provided:

```Scala
val lines = MQTTUtils.createStream(ssc, brokerUrl, topic, storageLevel, clientId, username, password, cleanSession, qos, connectionTimeout, keepAliveInterval, mqttVersion)
val lines = MQTTUtils.createPairedStream(ssc, brokerUrl, topics, storageLevel, clientId, username, password, cleanSession, qos, connectionTimeout, keepAliveInterval, mqttVersion)
```

### Java API

You need to extend `JavaActorReceiver` so as to store received data into Spark using `store(...)` methods. The supervisor strategy of
this actor can be configured to handle failures, etc.

    JavaDStream<String> lines = MQTTUtils.createStream(jssc, brokerUrl, topic);
    JavaReceiverInputDStream<Tuple2<String, String>> lines = MQTTUtils.createPairedStream(jssc, brokerUrl, topics);

See end-to-end examples at [MQTT Examples](https://github.com/apache/bahir/tree/master/streaming-mqtt/examples)
