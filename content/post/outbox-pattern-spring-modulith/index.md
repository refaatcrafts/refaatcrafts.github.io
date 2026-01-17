---
title: "How to Implement the Outbox Pattern in a Simple Way"
description: "Learn how to implement the Outbox Pattern easily using Spring Boot and Spring Modulith with domain events"
date: 2026-01-17T10:00:00+00:00
categories: ["Architecture", "Microservices", "Backend"]
image: image.png
tags: ["Spring Boot", "Spring Modulith", "Outbox Pattern", "Design Patterns", "Kotlin", "Distributed Systems"]
---

# How to Implement the Outbox Pattern in a Simple Way!

If you are building a **modular monolith** using **Spring Boot** and **Spring Modulith**, implementing the **Outbox Pattern** can be very straightforward.

**Spring Modulith** allows you to guarantee message delivery by using domain events. You only need to annotate your event consumer with `@TransactionalEventListener`.

```kotlin
@Component
class OrderEventListener(
    private val notificationService: NotificationService
) {

    @TransactionalEventListener
    fun handleOrderCreated(event: OrderCreatedEvent) {
        notificationService.sendOrderConfirmation(event.orderId)
    }
}
```

Spring Modulith will automatically:

- Create the `event_publication` table
- Retry failed event deliveries
- Ensure messages are eventually delivered

You don't need to implement a custom outbox table or write retry logic yourself.

## One Thing to Be Aware Of!

By default, event publishing and event listeners run synchronously in the same thread. This means the user request will wait until the listener finishes processing.

## How to Improve This

You can make the listener run asynchronously by adding the `@Async` annotation. This allows the request to finish without waiting for the listener.

```kotlin
@Component
class OrderEventListener(
    private val notificationService: NotificationService
) {

    @Async
    @TransactionalEventListener
    fun handleOrderCreated(event: OrderCreatedEvent) {
        notificationService.sendOrderConfirmation(event.orderId)
    }
}
```

By default, `@Async` uses **platform threads**, which can be expensive. To improve performance and scalability, you can enable Java virtual threads:

```properties
spring.threads.virtual.enabled=true
```
