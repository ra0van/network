#microservices
## Tag line - Friends don’t let friends do dual writes!


### Why Outbox? - the problem with dual write
Imagine, there are two microservices. the Order service & the Shipping service. The business logic states that the order service should notify the Shipping service about a new order creation to prepare a shipment.

![](https://miro.medium.com/max/1400/1*9y1sztLbXlRTES8iJ6o0rw.png)

#### What are the options we have?
**Synchronous approach:** The order service synchronously invokes an API of shipping service to notify. The drawback is that this introduces a coupling between two services. The order service must depend on the shipping service's availability, dealing with retries, rate limits, etc. Considering scalability, we can rule out this approach.

**Async approach:** The order service inserts the new order into its database. Then publishes a domain event to a message broker describing the state change that happened. The shipping service that subscribed to that event eventually receives this event and prepares shipment.

![](https://miro.medium.com/max/1400/1*5svc7TChGSLj5Qu0Hm_1DA.png)

The async approach appears to be fitting solution in terms of scalability & low coupling. But what could be the problems with this approach?

The problems is that order service has to update two systems simultaneously - the db & the message broker. That is called a dual write, which is considered a sin in distributed systems literature.

***A failure to update two systems atomically leaves the entire system in an inconsistent state.***

![](https://miro.medium.com/max/1400/1*9MaF8X-9vcRza6KtKix3BA.png)

Sequence of two operations.

As per the above diagram, if the event publishing fails due to broker outage, we will have an order in the system but without a shipment. Also, if the new order insertion fails due to db error, the event would still be published. That creates a shipment without a corresponding order.

This problem related to dual writes demands us to be on the lookout for a solution to make the database update & event publishing atomic. 

### The Transaction outbox pattern
The outbox pattern solves this problem by writing to two database tables, the aggregate table and an outbox table, within the same transaction scope and then use the content written to the outbox table to drive the event publishing process.

The pattern comprises two components
- the outbox table
- the message relay

**The outbox table**
The pattern introduces a supplementary table, called outbox, to the service's database. This table stores the event notifications that are supposed to send from the service to the message broker. When service writes to the aggregate table, it also writes a record to the outbox table as a part of the same transaction.

The record written to the outbox table describes a change event that happened in the service. For example, it could be a new customer registration or change in email address.

**The message relay**
The message relay component asynchronously monitors the outbox table for new entries. If any, they will be transformed into events and published to the message broker.

Once published, the message will be deleted from the outbox table to prevent reprocessing and the table growth.

![](https://miro.medium.com/max/1400/1*FvKGbWicxxqOBSqCQZ3PHA.png)


### The outbox table format

The outbox table should have the following structure at a minimum.

create table OUTBOX (  
 id varchar(255) primary key,  
 aggregate_type varchar(255) not null,  
 aggregate_id varchar(255) not null,  
 type varchar(255) not null,  
 payload text not null  
);

-   **_id_:** unique id of each message; can be used by consumers to detect any duplicate events.
-   **_aggregate_type_**: the type of the _aggregate root_ to which a given event is related. That comes from the Domain-Driven Design (DDD), where the exported event should be associated with an aggregate root. In our example, this is the Order.
-   **_aggregate_id:_** this is the ID of the aggregate object affected by the update operation. That can be the order ID here. The Shipping service will use it as a reference to the shipment record.
-   **_type_**: type of the event. For example, “_OrderCreated_.”
-   **_payload_**: a JSON representation of the actual event content. For instance, it contains the order ID, customer ID, total, etc.


### How this solves the dual write problem?
The Outbox pattern enables achieving atomicity when writing to the database and publishing events to the broker. We can leverage the power of local transactions to do both actions or nothing.

### At least once delivery of change events
By writing a record to the OUTBOX table, we benefit from the at-least-once delivery guarantee for the change event. In case of a broker outage, the message relay can retry reliably after reading OUTBOX messages. The broker may not be reachable for hours or days. But we have a persistent record of the message to be sent when the broker is back online. That way, we can guarantee that the **change** **event will reach the broker at least once**.

### Prevent fake event publication with clean rollbacks
Furthermore, we can benefit from a local ACID transaction when writing to both tables at the same time. **If writing to either of the tables fails, we will have a clean rollback**. That prevents fake messages from getting published to the broker. Fake message as in, the aggregate table update could’ve failed, but an event has been published to the broker.

### Challenges — Sending duplicate events
After publishing an event, the message relay deletes the corresponding record in the OUTBOX table to prevent reprocessing.

But the message relay may fail to do so if it crashes during the attempt to delete the record. When it restarts, it sees the same record, thus publishes it to the broker for the second time.

That is one challenge often associated with the Outbox pattern. We can fix it by making the downstream event consumer idempotent.

### Duplicate detection with an idempotent consumer
A failure in the message relay to delete the OUTBOX record, a message broker restart, or an unknown error may cause the event consumer to receive duplicate events. Achieving exactly once semantics in a distributed system is a challenge we all have to face.

Shipping service in our example might receive the same Order twice so that it would prepare the same shipment twice, which is not acceptable. The consumer can prevent this by checking whether the event with the given UUID has been processed before. If so, any further calls for that same event will be ignored.

Similar to the OUTBOX table, we can maintain an INBOX table inside the consumer service’s database. It simply keeps track of what events were processed by recording their UUIDs.

After processing an event for the first time, the consumer marks the event as processed in the INBOX table. That should be transactional — making it possible to trap any rollbacks at the consumer level so that it can retry receiving the event.

### Polling publisher
This publisher publishes messages by polling the OUTBOX table for new entries. Frequent polling will exhaust the database soon. Hence this is not a scalable solution. 

### Message relay based on Change Data Capture (CDC)
Here, you have a transaction log mining component that tails the database transaction log to capture the changes made to the OUTBOX table. The transaction log records all transactions committed against the database in a strongly ordered manner. The miner reads the transaction log and publishes each change as a message to the message broker.

This process is often referred to as [[Log-based Change Data Capture]]. Unlike the polling counterpart, event capture happens with a very low overhead in near-real-time.

## Practical use cases
In general, this pattern can be used to propagate state changes among Microservices reliably. Some use cases are as follows.

-   **Sending reliable notifications to other services:** The sender can guarantee that the message will be delivered to the receiver at least once.
-   **Sending event notifications in choreography-based** [**Sagas**](https://microservices.io/patterns/data/saga.html): One service can reliably notify others about its state change.
-   **Updating query-side materialised views:** To reliably notify query-side Microservices about the state changes on the command side so that they can update their materialised views.

### Takeaways

-   When sending notifications amongst microservices, use an asynchronous, event-driven approach for better reliability and scalability.
-   While doing so, dual writes are inevitable. Hence use the Outbox pattern to avoid that and introduce reliable delivery for notifications.
-   There are few implementation choices for the pattern, but a log-based CDC approach seems the popular choice.
-   The pattern might deliver duplicate notifications. But you should make the consumers idempotent to avoid that.


TODO : 
- [ ] Read Kamil Grzybek’s [post](http://www.kamilgrzybek.com/design/the-outbox-pattern/) about implementing a polling message relay with .NET.
- [ ] Checkout Debezium for CDC. 
- [ ] Checkout CDC [blog](https://debezium.io/blog/2018/07/19/advantages-of-log-based-change-data-capture/) by Debezium
- [ ] Checkout data exchange patterns in Microservices [blog](https://debezium.io/blog/2019/02/19/reliable-microservices-data-exchange-with-the-outbox-pattern/)
- [ ] Checkout overusage of outbox [blog](https://www.squer.at/en/blog/stop-overusing-the-outbox-pattern/)
- [ ] https://medium.com/engineering-varo/event-driven-architecture-and-the-outbox-pattern-569e6fba7216

References :
https://medium.com/event-driven-utopia/sending-reliable-event-notifications-with-transactional-outbox-pattern-7a7c69158d1b
