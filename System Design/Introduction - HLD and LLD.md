## High Level Design (HLD)
**High-Level Design (HLD)** defines the overall architecture of a system and how the main components interact with each other. It provides a big-picture view of the system structure, services, and data flow.
- Identifies major modules, services, and other interactions.
- Focuses on system architecture and high-level decisions.
- Usually created by architects, stakeholders, and senior developers

### Pre-requisite Knowledge for HLD
1. Basic Coding Skills (Data Structures and Algorithms)
2. Compared to Low Level Design, High Level Design is typically done by more senior people who have hands-on experience on software projects.
3. Knowing the roles of components like databases (SQL and NoSQL), caches (Redis, Memcached, CDNs), and APIs.
4. In-depth understanding of [[Requirement Analysis#Functional Requirements|Functional Requirements]] and [[Requirement Analysis#Non-Functional Requirements|Non-Functional Requirements]].
5. Networking and Security Fundamentals like DNS, protocols (TCP/UDP, HTTP, WebSockets), OAuth, JWT, TLS/SSL, rate-limiting, API security, and basic DDOS protection.
6. Message queues and streaming tools like Kafka or RabbitMQ.
7. Knowledge of Microservices vs. Monoliths (When to split services and how to manage dependencies), fault tolerance, fallback strategies, redundancy, Load Balancer Types & Algorithms.
8. Observability tools like Prometheus, Grafana, ELK Stack (Elasticsearch, Logstash, Kibana), and Alerting systems (e.g., PagerDuty)
### Topics Covered in HLD

Focuses on system architecture, modules, and their interactions.

- **System architecture overview**: Defines the major components, modules, and how they interact (e.g., services, queues, databases).
- Data flow and component interaction: Illustrates how data moves between modules, along with key integrations and interfaces.
- Technology stack and infrastructure: High-level decisions on frameworks, platforms, hardware, databases, and hosting setups.
- **Module responsibilities**: Describe what each module does and how they relate to one another.
- **Performance & trade-offs**: Includes design trade-offs, performance considerations, scalability, security, cost, and other non-functional factors.
- **Artifacts**: Commonly include architecture diagrams, component and deployment diagrams, data flow diagrams, and possibly ER/DB schematic overviews.

### Real World Examples of HLD Decisions

High-Level Design (HLD) decisions in real-world systems focus on scalability, performance, and reliability to handle massive user bases and real-time workloads.

- Netflix transitioned its entire backend from a monolith to microservices (starting with encoding and UI services), completing the migration by 2011 to scale rapidly during high-load events like holiday seasons.
- Uber adopted an event-driven architecture where ride requests, location updates, and fare changes emit events that trigger real-time systems like driver matching, billing, and dynamic pricing.
- Twitter deployed a load-balanced architecture with caching of trending topics and tweets to quickly serve millions of users and handle real-time data flows efficiently.

---
## Low Level Design (LLD
Low-Level Design (LLD) focuses on the internal implementation details of each component in the system. It provides developers with a clear and detailed blueprint for how modules, classes, and functions should be built.

- Describes the internal logic, classes, methods, and data structures of each module.
- Converts the High-Level Design into detailed implementation plans.
- Usually created by senior developers or designers before coding begins.

### Prerequisite Technical Knowledge for LLD

Before creating a Low-Level Design, developers should have a strong understanding of core programming and software design concepts.
- Basic Coding Skills (Data Structures and Algorithms)
- Strong grasp of OOP concepts (Encapsulation, inheritance, polymorphism & abstraction)

### Topics Covered in LLD
Covers how each component is implemented, including classes, methods, and logic.
- **Component/module breakdown**: Detailed internal logic for each module—with class responsibilities, methods, attributes, interactions
- **Database schema & structure**: Designing tables, keys, indexes, relationships with SQL/NoSQL refinements
- **API & interface definitions**: Precise request/response formats, error codes, methods, endpoints, and internal interfacing
- **Error handling & validation logic**: Define how each module manages invalid inputs, failures, edge cases, and logging
- **Design patterns & SOLID**: Implement design patterns and SOLID principles to ensure clean, extensible, maintainable code
- **UML and pseudocode artifacts**: Class diagrams, sequence diagrams, pseudocode, or flowcharts to clarify logic paths and method calls

---

## Difference between Low-Level Design and High-Level Design

HLD defines the overall system architecture, while LLD focuses on the detailed implementation of individual components.

| HIGH-LEVEL DESIGN (HLD)                           | LOW-LEVEL DESIGN (LLD)                     |
| ------------------------------------------------- | ------------------------------------------ |
| Defines the overall system architecture           | Focuses on detailed component-level design |
| Describes modules and their interactions          | Describes internal logic of each module    |
| Also known as macro-level/system design           | Also known as micro-level/detailed design  |
| Created by solution architects                    | Created by developers/designers            |
| Based on SRS (Software Requirement Specification) | Based on reviewed HLD                      |


