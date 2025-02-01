# Table of contents:
- [What is a Monolith Applicaiton and What are Its Challenges](#what-is-a-monolith-applicaiton-and-what-are-its-challenges)
- [Path from Monolith to Microservices](#path-from-monolith-to-microservices)

## What is a Monolith Applicaiton and What are Its Challenges

A monolith application is an all-in-one system where all functionalities are built within a single codebase, meaning the entire application is considered one unit, with all components tightly coupled and deployed as a single entity.

### Why do companies build software in the monolith way?

#### 1. Simplicity

ddddd

#### 2. Single Development Environment



#### 3. Single Process Leads to Consistent Performance
loading, compiling, and building times increase with every new update

Since the entire monolith application runs as a single process, the scaling of individual features of the monolith is almost impossible.

Scaling:
scaling the entire application can be achieved by manually deploying a new instance of the monolith on another server, typically behind a load-balancing appliance - another pricey solution.

Upgrades:
challenges for system engineers to keep all systems at the same patch level and may introduce new possible licensing costs.

## Path from Monolith to Microservices

loosely coupled microservices, each performing a specific business function.

Microservices-based architecture is aligned with Event-driven Architecture and Service-Oriented Architecture (SOA) principles.

Although the distributed nature of microservices adds complexity to the architecture, one of the greatest benefits of microservices is scalability.

Seamless upgrades and patching processes - no downtime and no service disruption to clients

## the refactoring approach dilemma: a "Big-bang" approach or an incremental refactoring.


