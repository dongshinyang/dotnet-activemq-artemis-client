# .NET Client for ActiveMQ Artemis

![Build status](https://github.com/Havret/dotnet-activemq-artemis-client/workflows/Build/badge.svg)
[![NuGet](https://img.shields.io/nuget/vpre/Unofficial.ActiveMQ.Artemis.Client.svg)](https://www.nuget.org/packages/Unofficial.ActiveMQ.Artemis.Client)

Unofficial [ActiveMQ Artemis](https://activemq.apache.org/components/artemis/) .NET Client for .NET Core and .NET Framework.

Apache ActiveMQ Artemis is an open-source project to build a multi-protocol, embeddable, very high performance, clustered, asynchronous messaging system.

This lightweight .NET client library built on top of [AmqpNetLite](http://azure.github.io/amqpnetlite/) tries to fully leverage Apache ActiveMQ Artemis capabilities.

## Compatibility

The library is tested against **ActiveMQ Artemis 2.13.0**. Most of the features should work with older versions of the broker, but **Topology Management** uses API surface that was introduced in **ActiveMQ Artemis 2.12.0** and will be fully operational after the release of the incoming **ActiveMQ Artemis 2.14.0** (see [known issues](#known-issues)).

## Quickstart

Add ActiveMQ.Artemis.Client NuGet package to your project using dotnet CLI:

```
dotnet add package Unofficial.ActiveMQ.Artemis.Client
```

The API interfaces and classes are defined in the `ActiveMQ.Artemis.Client` namespace:

```
using ActiveMQ.Artemis.Client;
```

Before any message can be sent or received, a connection to the broker endpoint has to be opened. Create a connection using `ConnectionFactory` object.

```
var connectionFactory = new ConnectionFactory();
var endpoint = Endpoint.Create("localhost", 5672, "guest", "guest");
var connection = await connectionFactory.CreateAsync(endpoint);
```

In order to send a message you will need a message producer:

```
var producer = await connection.CreateProducerAsync("a1", RoutingType.Anycast);
await producer.SendAsync(new Message("foo"));
```

To receive a message you have to create a message consumer:

```
var consumer = await connection.CreateConsumerAsync("a1", RoutingType.Anycast);
var message = await consumer.ReceiveAsync();
```

## Documentation

Detailed documentation is available on [the project website](https://havret.github.io/dotnet-activemq-artemis-client/).

## Known issues

- Because of a bug in ActiveMQ Artemis 2.13.0 queue declaration doesn't work properly for queues created without FilterExpression. The queue actually gets updated (if necessary), but the exception is being thrown from `ITopologyManager.DeclareQueueAsync`.

## Features

The following table shows what features are currently supported.

|Feature|Status|Comments|
|:-:|:-:|:-|
|Connection|✔|Allows to asynchronously create producers and consumers.|
|Session|✔|Sessions are created transparently for each link (producer or consumer).|
|Message Consumer|✔|`ReceiveAsync` available.|
|Message Producer|✔|`SendAsync` for *durable* messages and non-blocking fire and forget `Send` for *nondurable* messages.|
|Anonymous Message Producer|✔||
|Message Payload|✔|All primitive types, `System.Guid`, `System.DateTime`, `byte[]` and `Amqp.Types.List`.|
|Message Durability|✔||
|Message Priority|✔||
|Message Creation Time|✔|All messages have `CreationTime` set, but it can be disabled via producer configuration.|
|Message Grouping|✔||
|Message Expiry|✔||
|Message Acknowledgement|✔|Messages can be either *accepted* or *rejected*.|
|Scheduled Messages|✔|Message delivery can be scheduled either by `ScheduledDeliveryTime` or `ScheduledDeliveryDelay` property.
|Filter Expressions|✔||
|NoLocal filter|✔|Allows your consumers to subscribe to messages sent by producers created by other connections.|
|Consumer Credit|✔||
|Auto-recovery|✔|3 built-in recovery policies `ConstantBackoff`, `LinearBackoff`, `ExponentialBackoff`, and the option to implement your own recovery policy via `IRecoveryPolicy` interface.|
|Address Model|✔|Advanced routing strategies use FQQN, thus require queues to be pre-configured using `TopologyManager`.|
|Topology Management|✔|`TopologyManager` supports: getting all address names, getting all queue names, address creation, queue creation, address declaration, queue declaration, address removal, queue removal.|
|Logging|✔|Uses [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).|
|Queue Browser|❌||
|Transactions|✔||
|Consumer Priority|❌||