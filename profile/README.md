# Podmortem

> AI-powered Kubernetes postmortem pod failure analysis operator.

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-Operator-blue.svg)](https://kubernetes.io/)

**Podmortem** is a Kubernetes-native operator that automatically analyzes pod failures and provides intelligent explanations of what went wrong. Instead of manually digging through thousands of log lines, Podmortem uses community-driven pattern libraries and AI integration to identify root causes and suggest remediation steps.

## What Problem Does It Solve?

When pods fail in Kubernetes or OpenShift, operations teams typically spend significant time manually analyzing logs to understand what went wrong. Podmortem automates this initial analysis, providing immediate insights and reducing the burden of manual investigation. Podmortem delivers:

- **Instant root cause identification** from cryptic error logs
- **Community-driven pattern libraries** for common failure scenarios
- **AI-powered explanations** in human-readable language
- **Actionable remediation suggestions**

## How It Works

Podmortem combines **deterministic pattern matching** with **AI-powered explanation** through a two-layer approach:

```
Pod Failure → Log Collection → Pattern Analysis → AI Explanation → Human-Readable Report
```

### Layer 1: Pattern-Driven Analysis
- **Precise Detection**: Regex-based patterns identify known failure signatures
- **Community Knowledge**: Extensible pattern libraries for frameworks like Quarkus, Spring Boot, etc.
- **Smart Scoring**: Multi-dimensional scoring considers severity, proximity, and temporal relationships

### Layer 2: AI-Powered Explanation  
- **Natural Language**: Transform technical analysis into clear explanations
- **Context Aware**: AI understands broader failure context
- **Provider Agnostic**: Works with OpenAI, Ollama, vLLM, or enterprise AI services

## Architecture

| Component | Purpose | Technology |
|-----------|---------|------------|
| **[Operator](https://github.com/podmortem/operator)** | Kubernetes controller managing podmortem lifecycles | Quarkus + Kubernetes Java SDK |
| **[Log Parser](https://github.com/podmortem/log-parser)** | Pattern matching and context extraction engine | Quarkus REST services |
| **[AI Interface](https://github.com/podmortem/ai-interface)** | Connects to various AI providers for explanations | Quarkus with provider integrations |
| **[AI Provider Library](https://github.com/podmortem/ai-provider-lib)** | Implements provider SDKs (OpenAI-compatible, Ollama, vLLM) and prompt engine | Java library |
| **[Common Library](https://github.com/podmortem/common-lib)** | Shared models, utilities, and CRD definitions | Java library |

## Sample Output

See how Podmortem identifies an error in a pod log.

### Failed pod's logs

<details>
<summary><strong>Click to expand full pod logs (200+ lines)</strong></summary>

```
2024-01-15 10:30:00.123 INFO  [main] io.quarkus.runtime.Quarkus: Quarkus 3.6.4 starting up
2024-01-15 10:30:00.245 INFO  [main] io.quarkus.deployment.QuarkusAugmentor: Building application
2024-01-15 10:30:00.356 INFO  [main] io.quarkus.arc.deployment.ArcProcessor: Found 47 beans
2024-01-15 10:30:00.467 INFO  [main] io.quarkus.hibernate.orm.deployment.HibernateOrmProcessor: Setting up Hibernate ORM
2024-01-15 10:30:00.578 INFO  [main] org.hibernate.jpa.internal.util.LogHelper: HHH000204: Processing PersistenceUnitInfo [name: default]
2024-01-15 10:30:00.689 INFO  [main] org.hibernate.Version: HHH000412: Hibernate ORM core version 6.2.13.Final
2024-01-15 10:30:00.790 INFO  [main] org.hibernate.cfg.Environment: HHH000406: Using bytecode reflection optimizer
2024-01-15 10:30:01.123 INFO  [main] org.hibernate.service.internal.AbstractServiceRegistryImpl: HHH000094: Hibernate service registry initialized
2024-01-15 10:30:01.234 INFO  [main] io.agroal.pool.DataSource: Creating pool for datasource 'default'
2024-01-15 10:30:01.345 INFO  [main] io.agroal.pool.ConnectionPool: Connection pool 'default' set up with size 20
2024-01-15 10:30:01.456 INFO  [main] io.agroal.pool.wrapper.ConnectionWrapper: Successfully connected to PostgreSQL database
2024-01-15 10:30:01.567 INFO  [main] org.hibernate.engine.jdbc.env.internal.JdbcEnvironmentInitiator: HHH000342: Database metadata retrieved successfully
2024-01-15 10:30:01.678 INFO  [main] org.hibernate.tool.schema.internal.HibernateSchemaManagementTool: HHH000476: Executing schema validation
2024-01-15 10:30:01.789 INFO  [main] org.hibernate.engine.jdbc.connections.internal.DriverManagerConnectionProviderImpl: HHH000115: Connection pool size: 20 (min=5)
2024-01-15 10:30:01.890 INFO  [main] io.quarkus.hibernate.orm.runtime.HibernateOrmRecorder: Hibernate ORM core version 6.2.13.Final started successfully
2024-01-15 10:30:02.001 INFO  [main] io.undertow.servlet: Initializing Spring FrameworkServlet 'dispatcherServlet'
2024-01-15 10:30:02.112 INFO  [main] io.quarkus.smallrye.health.deployment.HealthOpenAPIFilter: Health check procedures exposed via OpenAPI
2024-01-15 10:30:02.223 INFO  [main] io.quarkus.resteasy.reactive.server.runtime.ResteasyReactiveRecorder: Resteasy Reactive initialized
2024-01-15 10:30:02.334 INFO  [main] io.vertx.core.impl.launcher.commands.VertxIsolatedDeployer: Succeeded in deploying verticle
2024-01-15 10:30:02.445 INFO  [main] io.quarkus.runtime.Quarkus: Profile prod activated. 
2024-01-15 10:30:02.556 INFO  [main] io.quarkus.runtime.Quarkus: Installed features: [agroal, cdi, config-yaml, hibernate-orm, hibernate-validator, narayana-jta, resteasy-reactive, resteasy-reactive-jackson, smallrye-context-propagation, smallrye-health, smallrye-openapi, undertow, vertx]
2024-01-15 10:30:02.667 INFO  [main] io.quarkus.runtime.Quarkus: Quarkus 3.6.4 on JVM started in 2.544s. Listening on: http://0.0.0.0:8080
2024-01-15 10:30:02.778 INFO  [main] io.quarkus.runtime.Quarkus: Quarkus application started successfully

2024-01-15 10:30:05.123 INFO  [executor-thread-1] com.example.order.resource.HealthResource: Health check endpoint accessed
2024-01-15 10:30:05.234 INFO  [executor-thread-1] org.hibernate.SQL: select o1_0.id,o1_0.customer_id,o1_0.order_date,o1_0.status,o1_0.total from orders o1_0 where o1_0.status=?
2024-01-15 10:30:05.345 DEBUG [executor-thread-1] org.hibernate.orm.jdbc.bind: binding parameter [1] as [VARCHAR] - [PENDING]
2024-01-15 10:30:05.456 INFO  [executor-thread-1] com.example.order.service.OrderService: Found 15 pending orders
2024-01-15 10:30:08.567 INFO  [executor-thread-2] com.example.order.resource.OrderResource: Processing order creation request
2024-01-15 10:30:08.678 INFO  [executor-thread-2] com.example.order.service.OrderService: Creating new order for customer: 12345
2024-01-15 10:30:08.789 DEBUG [executor-thread-2] org.hibernate.SQL: insert into orders (customer_id,order_date,status,total,id) values (?,?,?,?,?)
2024-01-15 10:30:08.890 INFO  [executor-thread-2] com.example.order.service.NotificationService: Sending order confirmation email
2024-01-15 10:30:09.001 INFO  [executor-thread-2] com.example.order.resource.OrderResource: Order created successfully with ID: 67890
2024-01-15 10:30:12.112 INFO  [executor-thread-3] com.example.order.resource.OrderResource: Retrieving order details for ID: 67890
2024-01-15 10:30:12.223 DEBUG [executor-thread-3] org.hibernate.SQL: select o1_0.id,o1_0.customer_id,o1_0.order_date,o1_0.status,o1_0.total from orders o1_0 where o1_0.id=?
2024-01-15 10:30:15.334 INFO  [executor-thread-4] com.example.order.service.PaymentService: Processing payment for order: 67890
2024-01-15 10:30:15.445 INFO  [executor-thread-4] com.example.order.integration.PaymentGateway: Initiating payment with external gateway
2024-01-15 10:30:16.556 INFO  [executor-thread-4] com.example.order.service.PaymentService: Payment processed successfully
2024-01-15 10:30:20.667 INFO  [executor-thread-5] com.example.order.service.InventoryService: Checking inventory for order: 67890
2024-01-15 10:30:20.778 DEBUG [executor-thread-5] org.hibernate.SQL: select i1_0.id,i1_0.product_id,i1_0.quantity,i1_0.reserved_quantity from inventory i1_0 where i1_0.product_id in (?,?,?)
2024-01-15 10:30:20.889 INFO  [executor-thread-5] com.example.order.service.InventoryService: Inventory check completed successfully
2024-01-15 10:30:25.990 INFO  [executor-thread-6] com.example.order.resource.OrderResource: Updating order status to CONFIRMED
2024-01-15 10:30:26.101 DEBUG [executor-thread-6] org.hibernate.SQL: update orders set customer_id=?,order_date=?,status=?,total=? where id=?
2024-01-15 10:30:30.212 INFO  [executor-thread-7] com.example.order.scheduled.OrderCleanupService: Starting scheduled order cleanup task
2024-01-15 10:30:30.323 INFO  [executor-thread-7] com.example.order.scheduled.OrderCleanupService: Cleaning up 3 expired orders
2024-01-15 10:30:35.434 INFO  [executor-thread-8] com.example.order.resource.ReportResource: Generating daily sales report
2024-01-15 10:30:35.545 DEBUG [executor-thread-8] org.hibernate.SQL: select sum(o1_0.total) from orders o1_0 where o1_0.order_date>=? and o1_0.order_date<?
2024-01-15 10:30:35.656 INFO  [executor-thread-8] com.example.order.service.ReportService: Daily sales total: $45,678.90

2024-01-15 10:31:00.767 INFO  [executor-thread-9] com.example.order.resource.OrderResource: Processing bulk order import
2024-01-15 10:31:00.878 INFO  [executor-thread-9] com.example.order.service.OrderService: Importing 150 orders from CSV file
2024-01-15 10:31:02.989 INFO  [executor-thread-9] com.example.order.service.OrderService: Successfully imported 134 orders
2024-01-15 10:31:02.100 WARN  [executor-thread-9] com.example.order.service.OrderService: 16 orders failed validation during import
2024-01-15 10:31:05.211 INFO  [executor-thread-10] com.example.order.resource.CustomerResource: Creating new customer profile
2024-01-15 10:31:05.322 DEBUG [executor-thread-10] org.hibernate.SQL: insert into customers (email,first_name,last_name,phone,id) values (?,?,?,?,?)
2024-01-15 10:31:08.433 INFO  [executor-thread-11] com.example.order.service.EmailService: Sending welcome email to new customer
2024-01-15 10:31:10.544 INFO  [executor-thread-12] com.example.order.resource.OrderResource: Processing order cancellation request
2024-01-15 10:31:10.655 INFO  [executor-thread-12] com.example.order.service.OrderService: Cancelling order: 67890
2024-01-15 10:31:10.766 INFO  [executor-thread-12] com.example.order.service.RefundService: Processing refund for cancelled order
2024-01-15 10:31:15.877 INFO  [executor-thread-13] com.example.order.resource.ProductResource: Updating product catalog
2024-01-15 10:31:15.988 DEBUG [executor-thread-13] org.hibernate.SQL: update products set category=?,description=?,name=?,price=? where id=?
2024-01-15 10:31:20.099 INFO  [executor-thread-14] com.example.order.service.CacheService: Refreshing product cache
2024-01-15 10:31:20.210 INFO  [executor-thread-14] com.example.order.service.CacheService: Cache refresh completed in 111ms

2024-01-15 10:32:00.321 INFO  [executor-thread-15] com.example.order.resource.OrderResource: High traffic detected - processing order creation
2024-01-15 10:32:00.432 INFO  [executor-thread-16] com.example.order.resource.OrderResource: High traffic detected - processing order creation
2024-01-15 10:32:00.543 INFO  [executor-thread-17] com.example.order.resource.OrderResource: High traffic detected - processing order creation
2024-01-15 10:32:00.654 WARN  [executor-thread-18] com.example.order.service.OrderService: Database connection pool near capacity: 18/20 connections active
2024-01-15 10:32:01.765 INFO  [executor-thread-19] com.example.order.resource.OrderResource: Processing concurrent order creation
2024-01-15 10:32:01.876 INFO  [executor-thread-20] com.example.order.resource.OrderResource: Processing concurrent order creation
2024-01-15 10:32:02.987 WARN  [executor-thread-21] io.agroal.pool.ConnectionPool: Connection pool 'default' is at maximum capacity
2024-01-15 10:32:03.098 INFO  [executor-thread-22] com.example.order.service.OrderService: Queuing order creation due to high load
2024-01-15 10:32:05.209 WARN  [executor-thread-23] com.example.order.service.DatabaseService: Database response time increasing: 2.3s average
2024-01-15 10:32:07.320 INFO  [executor-thread-24] com.example.order.resource.OrderResource: Processing order with retries due to timeouts
2024-01-15 10:32:10.431 WARN  [executor-thread-25] org.hibernate.engine.jdbc.spi.SqlExceptionHelper: SQL Warning Code: 0, SQLState: 00000
2024-01-15 10:32:10.542 WARN  [executor-thread-25] org.hibernate.engine.jdbc.spi.SqlExceptionHelper: Connection has been closed by peer
2024-01-15 10:32:12.653 ERROR [executor-thread-26] com.example.order.service.OrderService: Database operation failed, retrying...
2024-01-15 10:32:12.764 WARN  [executor-thread-26] io.agroal.pool.ConnectionPool: Failed to validate connection, creating new one

2024-01-15 10:32:15.875 ERROR [executor-thread-27] org.postgresql.Driver: Connection to localhost:5432 refused. Check that the hostname and port are correct
2024-01-15 10:32:15.986 WARN  [executor-thread-28] com.example.order.service.OrderService: Retrying database operation (attempt 1/3)
2024-01-15 10:32:18.097 ERROR [executor-thread-29] io.agroal.pool.ConnectionPool: Unable to acquire connection from pool within timeout
2024-01-15 10:32:18.208 ERROR [executor-thread-30] com.example.order.service.OrderService: Database operation failed after 3 retries
2024-01-15 10:32:20.319 ERROR [executor-thread-31] org.hibernate.engine.jdbc.env.internal.JdbcEnvironmentInitiator: HHH000342: Could not obtain connection to query metadata
2024-01-15 10:32:22.430 WARN  [executor-thread-32] io.agroal.pool.wrapper.ConnectionWrapper: Validation query failed for connection: Connection refused
2024-01-15 10:32:25.541 ERROR [executor-thread-33] com.example.order.resource.OrderResource: Request processing failed due to database issues
2024-01-15 10:32:25.652 ERROR [executor-thread-33] com.example.order.exception.DatabaseException: Database connection pool exhausted
	at com.example.order.service.OrderService.createOrder(OrderService.java:127)
	at com.example.order.resource.OrderResource.createOrder(OrderResource.java:89)
	at com.example.order.resource.OrderResource$quarkusrestinvoker$createOrder_ab123cd456ef.invoke(Unknown Source)
	at org.jboss.resteasy.reactive.server.handlers.InvocationHandler.handle(InvocationHandler.java:29)
	at io.quarkus.resteasy.reactive.server.runtime.QuarkusResteasyReactiveRequestContext.invokeHandler(QuarkusResteasyReactiveRequestContext.java:141)
	at org.jboss.resteasy.reactive.common.core.AbstractResteasyReactiveContext.run(AbstractResteasyReactiveContext.java:147)
	at io.quarkus.vertx.core.runtime.VertxCoreRecorder$14.runWith(VertxCoreRecorder.java:576)
	at org.jboss.threads.EnhancedQueueExecutor$Task.run(EnhancedQueueExecutor.java:2513)
	at org.jboss.threads.EnhancedQueueExecutor$ThreadBody.run(EnhancedQueueExecutor.java:1538)
	at java.base/java.lang.Thread.run(Thread.java:833)

2024-01-15 10:32:28.763 ERROR [executor-thread-34] com.example.order.service.CircuitBreakerService: Circuit breaker opened for database operations
2024-01-15 10:32:30.874 WARN  [executor-thread-35] com.example.order.health.DatabaseHealthCheck: Database health check failed
2024-01-15 10:32:32.985 ERROR [executor-thread-36] com.example.order.service.OrderService: Critical: Unable to process any database operations
2024-01-15 10:32:35.096 ERROR [qtp-executor-37] io.quarkus.arc.impl.InstanceImpl: CDI bean lookup failed
java.lang.RuntimeException: Error injecting io.quarkus.hibernate.orm.runtime.EntityManagerFactoryProducer
	at io.quarkus.hibernate.orm.runtime.EntityManagerFactoryProducer_ProducerMethod_createEntityManagerFactory_Bean.create(Unknown Source)
	at io.quarkus.arc.impl.AbstractSharedContext.createInstanceHandle(AbstractSharedContext.java:119)
	at io.quarkus.arc.impl.AbstractSharedContext.access$000(AbstractSharedContext.java:85)
	at io.quarkus.arc.impl.AbstractSharedContext$1.get(AbstractSharedContext.java:175)
	at io.quarkus.arc.impl.AbstractSharedContext$1.get(AbstractSharedContext.java:172)
	at io.quarkus.arc.generator.Default_jakarta_persistence_EntityManagerFactory.get(Unknown Source)

2024-01-15 10:32:38.207 FATAL [executor-thread-38] com.example.order.service.DatabaseService: Database service completely unavailable
2024-01-15 10:32:40.318 ERROR [executor-thread-39] org.postgresql.Driver: Connection to localhost:5432 refused. Check that the hostname and port are correct and that the postmaster is accepting TCP/IP connections.
java.net.ConnectException: Connection refused
	at java.base/java.net.PlainSocketImpl.socketConnect(Native Method)
	at java.base/java.net.AbstractPlainSocketImpl.doConnect(AbstractPlainSocketImpl.java:412)
	at java.base/java.net.AbstractPlainSocketImpl.connectToAddress(AbstractPlainSocketImpl.java:255)
	at java.base/java.net.AbstractPlainSocketImpl.connect(AbstractPlainSocketImpl.java:237)
	at java.base/java.net.SocksSocketImpl.connect(SocksSocketImpl.java:392)
	at java.base/java.net.Socket.connect(Socket.java:609)
	at org.postgresql.core.PGStream.<init>(PGStream.java:81)
	at org.postgresql.core.v3.ConnectionFactoryImpl.tryConnect(ConnectionFactoryImpl.java:132)
	at org.postgresql.core.v3.ConnectionFactoryImpl.openConnectionImpl(ConnectionFactoryImpl.java:258)
	at org.postgresql.core.ConnectionFactory.openConnection(ConnectionFactory.java:49)
	at org.postgresql.jdbc.PgConnection.<init>(PgConnection.java:247)
	at org.postgresql.Driver.makeConnection(Driver.java:434)
	at org.postgresql.Driver.connect(Driver.java:291)
	at io.agroal.pool.ConnectionFactory.createConnection(ConnectionFactory.java:226)
	at io.agroal.pool.ConnectionPool.newConnection(ConnectionPool.java:455)
	at io.agroal.pool.ConnectionPool.housekeeping(ConnectionPool.java:555)
	at io.agroal.pool.ConnectionPool.fillInitialConnections(ConnectionPool.java:214)
	at io.agroal.pool.ConnectionPool.<init>(ConnectionPool.java:193)
	at io.agroal.pool.DataSource.getConnectionPool(DataSource.java:114)
	at io.agroal.pool.DataSource.getConnection(DataSource.java:128)
	at io.quarkus.hibernate.orm.runtime.boot.QuarkusConnectionProviderInitiator.initialize(QuarkusConnectionProviderInitiator.java:25)
	at org.hibernate.boot.registry.internal.StandardServiceRegistryImpl.initiateService(StandardServiceRegistryImpl.java:101)
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.createService(AbstractServiceRegistryImpl.java:263)
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.initializeService(AbstractServiceRegistryImpl.java:237)
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.getService(AbstractServiceRegistryImpl.java:214)
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.injectDependencies(AbstractServiceRegistryImpl.java:246)
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.initializeService(AbstractServiceRegistryImpl.java:240)
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.getService(AbstractServiceRegistryImpl.java:214)
	at org.hibernate.boot.internal.InFlightMetadataCollectorImpl.<init>(InFlightMetadataCollectorImpl.java:173)
	at org.hibernate.boot.model.process.spi.MetadataBuildingProcess.complete(MetadataBuildingProcess.java:127)
	at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.metadata(EntityManagerFactoryBuilderImpl.java:1460)
	at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.build(EntityManagerFactoryBuilderImpl.java:1494)
	at org.hibernate.jpa.HibernatePersistenceProvider.createEntityManagerFactory(HibernatePersistenceProvider.java:56)
	at io.quarkus.hibernate.orm.runtime.FastBootHibernatePersistenceProvider.createEntityManagerFactory(FastBootHibernatePersistenceProvider.java:72)
	at io.quarkus.hibernate.orm.runtime.RuntimeSettings.createEntityManagerFactory(RuntimeSettings.java:127)
	at io.quarkus.hibernate.orm.runtime.HibernateOrmRecorder.initializeJpa(HibernateOrmRecorder.java:138)

2024-01-15 10:32:42.429 ERROR [executor-thread-40] io.quarkus.runtime.ApplicationLifecycleManager: Application degraded - database connectivity lost
2024-01-15 10:32:45.540 FATAL [executor-thread-41] io.quarkus.runtime.Quarkus: Unable to recover from database failure
java.lang.RuntimeException: Failed to start quarkus
	at io.quarkus.runner.ApplicationImpl.doStart(Unknown Source)
	at io.quarkus.runtime.Application.start(Application.java:101)
	at io.quarkus.runtime.ApplicationLifecycleManager.run(ApplicationLifecycleManager.java:111)
	at io.quarkus.runtime.Quarkus.run(Quarkus.java:71)
	at io.quarkus.runtime.Quarkus.run(Quarkus.java:44)
	at io.quarkus.runtime.Quarkus.run(Quarkus.java:124)
	at io.quarkus.runner.GeneratedMain.main(Unknown Source)
Caused by: java.lang.RuntimeException: Error injecting io.quarkus.hibernate.orm.runtime.EntityManagerFactoryProducer_ProducerMethod_createEntityManagerFactory_d8b32c98ad6b45a50e7d0b5a98fbad80eb6b0a84_Bean
	at io.quarkus.hibernate.orm.runtime.EntityManagerFactoryProducer_ProducerMethod_createEntityManagerFactory_d8b32c98ad6b45a50e7d0b5a98fbad80eb6b0a84_Bean.create(Unknown Source)
	at io.quarkus.hibernate.orm.runtime.EntityManagerFactoryProducer_ProducerMethod_createEntityManagerFactory_d8b32c98ad6b45a50e7d0b5a98fbad80eb6b0a84_Bean.create(Unknown Source)
	at io.quarkus.arc.impl.AbstractSharedContext.createInstanceHandle(AbstractSharedContext.java:119)
	at io.quarkus.arc.impl.AbstractSharedContext.access$000(AbstractSharedContext.java:85)
	at io.quarkus.arc.impl.AbstractSharedContext$1.get(AbstractSharedContext.java:175)
	at io.quarkus.arc.impl.AbstractSharedContext$1.get(AbstractSharedContext.java:172)
	at io.quarkus.arc.generator.Default_jakarta_persistence_EntityManagerFactory_d8b32c98ad6b45a50e7d0b5a98fbad80eb6b0a84.get(Unknown Source)
	at io.quarkus.arc.generator.Default_jakarta_persistence_EntityManagerFactory_d8b32c98ad6b45a50e7d0b5a98fbad80eb6b0a84.get(Unknown Source)
	at io.quarkus.arc.impl.ArcContainerImpl.beanInstanceHandle(ArcContainerImpl.java:494)
	at io.quarkus.arc.impl.ArcContainerImpl.beanInstanceHandle(ArcContainerImpl.java:507)
	at io.quarkus.arc.impl.ArcContainerImpl.beanInstanceHandle(ArcContainerImpl.java:499)
	at io.quarkus.hibernate.orm.runtime.HibernateOrmRecorder.callHibernateFeatureInit(HibernateOrmRecorder.java:204)
	at io.quarkus.deployment.steps.HibernateOrmProcessor$callHibernateFeatureInit1744392237.deploy_0(Unknown Source)
	at io.quarkus.deployment.steps.HibernateOrmProcessor$callHibernateFeatureInit1744392237.deploy(Unknown Source)
	... 7 more
Caused by: jakarta.persistence.PersistenceException: [PersistenceUnit: default] Unable to build Hibernate SessionFactory
	at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.persistenceException(EntityManagerFactoryBuilderImpl.java:1015)
	at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.build(EntityManagerFactoryBuilderImpl.java:1497)
	at org.hibernate.jpa.HibernatePersistenceProvider.createEntityManagerFactory(HibernatePersistenceProvider.java:56)
	at io.quarkus.hibernate.orm.runtime.FastBootHibernatePersistenceProvider.createEntityManagerFactory(FastBootHibernatePersistenceProvider.java:72)
	at io.quarkus.hibernate.orm.runtime.RuntimeSettings.createEntityManagerFactory(RuntimeSettings.java:127)
	at io.quarkus.hibernate.orm.runtime.HibernateOrmRecorder.initializeJpa(HibernateOrmRecorder.java:138)
	at io.quarkus.hibernate.orm.runtime.EntityManagerFactoryProducer.createEntityManagerFactory(EntityManagerFactoryProducer.java:25)
	at io.quarkus.hibernate.orm.runtime.EntityManagerFactoryProducer_ProducerMethod_createEntityManagerFactory_d8b32c98ad6b45a50e7d0b5a98fbad80eb6b0a84_Bean.create(Unknown Source)
	... 19 more
Caused by: org.hibernate.service.spi.ServiceException: Unable to create requested service [org.hibernate.engine.jdbc.env.spi.JdbcEnvironment] due to: Unable to determine database version, connection lost
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.createService(AbstractServiceRegistryImpl.java:276)
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.initializeService(AbstractServiceRegistryImpl.java:237)
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.getService(AbstractServiceRegistryImpl.java:214)
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.injectDependencies(AbstractServiceRegistryImpl.java:246)
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.initializeService(AbstractServiceRegistryImpl.java:240)
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.getService(AbstractServiceRegistryImpl.java:214)
	at org.hibernate.boot.internal.InFlightMetadataCollectorImpl.<init>(InFlightMetadataCollectorImpl.java:173)
	at org.hibernate.boot.model.process.spi.MetadataBuildingProcess.complete(MetadataBuildingProcess.java:127)
	at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.metadata(EntityManagerFactoryBuilderImpl.java:1460)
	at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.build(EntityManagerFactoryBuilderImpl.java:1494)
	... 25 more

2024-01-15 10:32:48.651 ERROR [executor-thread-42] io.quarkus.runtime.Quarkus: Application start failed
2024-01-15 10:32:50.762 INFO  [executor-thread-43] io.quarkus.runtime.ApplicationLifecycleManager: Stopping the application after failed start
2024-01-15 10:32:52.873 CRITICAL [shutdown-hook] Application crashed: Complete database connectivity failure during high load operation
2024-01-15 10:32:55.984 INFO  [Shutdown thread] io.quarkus.runtime.Quarkus: Quarkus stopped in 3.111s 
```
</details>

### Poodmortems response

| 🤖 **AI Analysis Result** | Generated automatically in under 5 seconds |
|---|---|

```
The highest scoring pattern is "Database Connection 
Failure (CRITICAL)" with a score of 13.772475369458128, which occurs at line 
80. The logs indicate that the application is unable to connect to the local 
PostgreSQL database, with the error message "Connection to localhost:5432 
refused. Check that the hostname and port are correct."
 
The context before the error indicates that the ProductResource is updating the 
product catalog, while the context after the error shows that the OrderService 
is retrying the database operation. This sequence of events suggests that the 
issue might have been caused by a sudden spike in database usage, which could 
have overwhelmed the database and caused it to stop responding.
 
To confirm this hypothesis, we should check if the PostgreSQL service is 
running by executing the command "systemctl status postgresql". If the service 
is not running, we should start it by running "sudo systemctl start postgresql".
```

---

### Technical Specifications

The output above was achived with the following model/hardware.

| Component | Specification |
|-----------|---------------|
| **AI Model** | `mistralai/Mistral-7B-Instruct-v0.2` |
| **GPU Hardware** | `1x NVIDIA A100 SXM (80GB)` |
| **Compute Resources** | `32 vCPU, 125GB RAM` |
| **GPU Utilization** | `95%` |
| **Inference Server** | `vLLM` |
| **Response Time** | `< 5 seconds` |


## Pattern Libraries

Podmortem uses YAML-based pattern libraries to identify failure signatures:

```yaml
patterns:
  - id: "quarkus_startup_failure"
    name: "Quarkus Application Startup Failure"
    
    primary_pattern:
      regex: "Failed to start|startup failed"
      confidence: 0.95
    
    secondary_patterns:
      - regex: "RuntimeException"
        weight: 0.4
        proximity_window: 20
    
    severity: "CRITICAL"
    category: ["startup", "runtime"]
    
    remediation:
      description: "Quarkus application failed to start"
      common_causes:
        - "Configuration errors"
        - "Missing dependencies"
        - "Port binding conflicts"
```

## Quick Start

### Prerequisites
- Kubernetes cluster
- kubectl configured
- AI provider (OpenAI API key or local Ollama)

### Installation

1. **Clone the charts repo** (temporary until a remote Helm repo is published):
```bash
git clone https://github.com/podmortem/helm-charts.git
cd helm-charts
```

1. **Install the chart**:
```bash
helm upgrade --install podmortem ./charts/podmortem-operator \
  --create-namespace -n podmortem-system
```

1. **Configure an AI provider** (example for OpenAI):
```bash
# Replace YOUR_KEY
kubectl create secret generic openai-secret -n podmortem-system \
  --from-literal=api-key=YOUR_KEY

# Create AIProvider resource referencing the secret (simplified)
kubectl apply -n podmortem-system -f - <<EOF
apiVersion: podmortem.redhat.com/v1alpha1
kind: AIProvider
metadata:
  name: openai-provider
spec:
  providerId: openai
  apiUrl: https://api.openai.com/v1
  modelId: gpt-4o
  authenticationRef:
    secretName: openai-secret
    secretKey: api-key
EOF
```

1. **Configure a Pattern Library**:

   **Option A: Public Repository (Community Patterns)**
   ```bash
   kubectl apply -n podmortem-system -f - <<EOF
   apiVersion: podmortem.redhat.com/v1alpha1
   kind: PatternLibrary
   metadata:
     name: quarkus-patterns
   spec:
     repository:
       url: https://github.com/podmortem/patterns-quarkus
       branch: main
       path: patterns
     syncEnabled: true
     syncInterval: "24h"
   EOF
   ```

   **Option B: Private Repository (Enterprise Patterns)**
   ```bash
   # First create credentials for your private pattern repository
   kubectl create secret generic pattern-repo-creds -n podmortem-system \
     --from-literal=username=< YOUR GIT USERNAME > \
     --from-literal=token=< YOUR GIT PAT >

   # Then create the PatternLibrary resource
   kubectl apply -n podmortem-system -f - <<EOF
   apiVersion: podmortem.redhat.com/v1alpha1
   kind: PatternLibrary
   metadata:
     name: quarkus-patterns
   spec:
     repository:
       url: https://github.com/your-org/patterns-quarkus-private
       branch: main
       path: patterns
       credentials:
         secretName: pattern-repo-creds
         usernameKey: username
         tokenKey: token
     syncEnabled: true
     syncInterval: "24h"
   EOF
   ```

1. **Create a Podmortem monitor CR**:
```bash
kubectl apply -n podmortem-system -f - <<EOF
apiVersion: podmortem.redhat.com/v1alpha1
kind: Podmortem
metadata:
  name: default-monitor
spec:
  podSelector:
    matchLabels:
      app: backed-api
  aiProviderRef:
    name: openai-provider
  patternLibraryRef:
    name: quarkus-patterns
EOF
```

1. **Check status:**
```bash
kubectl get podmortems -n podmortem-system -o yaml
```
