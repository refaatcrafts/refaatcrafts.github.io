---
title: "Understanding CQRS Pattern with Java Examples"
description: "A comprehensive guide to Command Query Responsibility Segregation (CQRS) pattern with practical Java implementations"
date: 2025-06-11
image: image.png
categories:
    - Software Architecture
    - Java
    - Design Patterns
tags:
    - cqrs
    - java
    - design-patterns
    - software-architecture
    - best-practices
---

Command Query Responsibility Segregation (CQRS) is an architectural pattern that separates read and write operations for a data store. In this post, we'll explore CQRS implementation in Java with practical examples.

## What is CQRS?

CQRS stands for Command Query Responsibility Segregation. The main concept is to separate the read and write operations of your application:

- **Commands**: Handle write operations (create, update, delete)
- **Queries**: Handle read operations (fetch, search, list)

This separation allows you to optimize each path independently and scale them according to their specific needs.

## Basic CQRS Implementation in Java

Let's implement a simple CQRS pattern for a product management system.

### Domain Model

```java
public class Product {
    private String id;
    private String name;
    private BigDecimal price;
    private int stock;

    // Constructor, getters, and setters
}
```

### Command Models

```java
public class CreateProductCommand {
    private String name;
    private BigDecimal price;
    private int initialStock;

    // Constructor, getters, and setters
}

public class UpdateStockCommand {
    private String productId;
    private int stockDelta;

    // Constructor, getters, and setters
}
```

### Query Models

```java
public class ProductQuery {
    private String id;

    public ProductQuery(String id) {
        this.id = id;
    }

    public String getId() {
        return id;
    }
}

public class ProductDto {
    private String id;
    private String name;
    private BigDecimal price;
    private int availableStock;

    // Constructor, getters, and setters
}
```

### Command Handlers

```java
@Service
public class ProductCommandHandler {
    private final ProductRepository repository;

    public ProductCommandHandler(ProductRepository repository) {
        this.repository = repository;
    }

    public String handle(CreateProductCommand command) {
        Product product = new Product(
            UUID.randomUUID().toString(),
            command.getName(),
            command.getPrice(),
            command.getInitialStock()
        );
        repository.save(product);
        return product.getId();
    }

    public void handle(UpdateStockCommand command) {
        Product product = repository.findById(command.getProductId())
            .orElseThrow(() -> new ProductNotFoundException(command.getProductId()));
        
        product.setStock(product.getStock() + command.getStockDelta());
        repository.save(product);
    }
}
```

### Query Handlers

```java
@Service
public class ProductQueryHandler {
    private final ProductReadRepository readRepository;

    public ProductQueryHandler(ProductReadRepository readRepository) {
        this.readRepository = readRepository;
    }

    public ProductDto handle(ProductQuery query) {
        return readRepository.findById(query.getId())
            .map(this::toDto)
            .orElseThrow(() -> new ProductNotFoundException(query.getId()));
    }

    private ProductDto toDto(Product product) {
        return new ProductDto(
            product.getId(),
            product.getName(),
            product.getPrice(),
            product.getStock()
        );
    }
}
```

## Benefits of CQRS

1. **Independent Scaling**: Read and write workloads can be scaled independently.
2. **Optimized Data Schemas**: Read and write models can be optimized for their specific uses.
3. **Better Security**: Finer-grained control over access to read and write operations.
4. **Improved Performance**: Each side can be tuned without affecting the other.

## Advanced CQRS with Event Sourcing

For more complex applications, CQRS is often combined with Event Sourcing. Here's a basic example:

```java
public interface Event {
    String getAggregateId();
    LocalDateTime getTimestamp();
}

public class ProductCreatedEvent implements Event {
    private final String aggregateId;
    private final String name;
    private final BigDecimal price;
    private final int initialStock;
    private final LocalDateTime timestamp;

    // Constructor, getters
}

public class StockUpdatedEvent implements Event {
    private final String aggregateId;
    private final int stockDelta;
    private final LocalDateTime timestamp;

    // Constructor, getters
}

@Service
public class EventSourcingProductCommandHandler {
    private final EventStore eventStore;

    public String handle(CreateProductCommand command) {
        String productId = UUID.randomUUID().toString();
        ProductCreatedEvent event = new ProductCreatedEvent(
            productId,
            command.getName(),
            command.getPrice(),
            command.getInitialStock(),
            LocalDateTime.now()
        );
        eventStore.store(event);
        return productId;
    }

    public void handle(UpdateStockCommand command) {
        StockUpdatedEvent event = new StockUpdatedEvent(
            command.getProductId(),
            command.getStockDelta(),
            LocalDateTime.now()
        );
        eventStore.store(event);
    }
}
```

## Best Practices

1. **Start Simple**: Don't implement CQRS unless you need it. Simple CRUD operations might be sufficient for basic applications.
2. **Consider Eventual Consistency**: In distributed systems, accept that read models might be slightly outdated.
3. **Use Event Sourcing Carefully**: While powerful, it adds complexity. Use it when you need audit trails or complex event replay.
4. **Monitor Performance**: Keep track of both read and write performance separately.

## Conclusion

CQRS is a powerful pattern that can help scale and maintain complex applications. While it adds some complexity, the benefits of separated read and write concerns can be substantial for the right use case. Start with a simple implementation and evolve as needed.

Remember that CQRS isn't a silver bullet - it's most beneficial in complex domains where the read and write requirements differ significantly. For simpler applications, traditional CRUD approaches might be more appropriate.
