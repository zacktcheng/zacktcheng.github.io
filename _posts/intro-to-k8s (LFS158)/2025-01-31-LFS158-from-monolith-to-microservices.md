# From Monolith to Microservices

### Table of contents:
- [What is a Monolith Applicaiton and What are Its Challenges](#what-is-a-monolith-applicaiton-and-what-are-its-challenges)
- [What is a Microservice](#what-is-a-microservice)
- [Path from Monolith to Microservices](#path-from-monolith-to-microservices)

## What is a Monolith Applicaiton and What are Its Challenges

<img src="/assets/images/monolith.drawio.png" />

A monolith application is an all-in-one system where all features are built within a single codebase, meaning the entire application is considered one unit, with all components tightly coupled and deployed as a single entity.

One intuitive way to understand the infrastructure of a monolith application is to think it as a waterfall of many steps, each step represents a feature or a component tighlty stacked on top another. When we deliver a new change, the flow has to go from the very top of waterfall down and then go through each component and finally down to the riverbed. This nature of the monolith style infrastructure has many advantages and disadvantages to discuss further.

<img src="/assets/images/waterfall-by-Danussa.jpg" width="auto" height="256" >

*Waterfall drawing, flowing river sketch Stock Vector by ©Danussa*

### Why Do Companies Build Software in a Monolith Way?

#### 1. Simplicity

As every piece of the application is bundled into one unified codebase, developers can work on different parts of the application without needing to navigate between multiple repositories or services. This advantage makes the development easier to understand, manage, and oversee compared to more complex, distributed systems.

#### 2. Single Development Environment

Thanks to the single environment where everything is contained within, developers can test the entire application as a whole, without needinf to mock or simulate interactions between different services. Similarly, debugging is also straightforward within a single codebase.

#### 3. Single Process Leads to Consistent Performance

Because all components run within the same process, monolithic applications can deliver consistent performance. Since everything is tightly knit into one system, there’s no need to worry about network latency or communication overhead between different services.

### Challenges of Monolith applications

#### 1. Development Complexity

In time, the new features and improvements added to code complexity, making development more challenging - loading, compiling, and building times increase with every new update.

#### 2. Hardware Requirement

Being a large, single piece of software which continuously grows, it has to run on a single system which has to satisfy its compute, memory, storage, and networking requirements. The hardware of such capacity is not only complex and extremely pricey, but at times challenging to procure.

#### 2. Scaling

Since the entire monolith application runs as a single process, the scaling of individual features of the monolith is almost impossible. Scaling the entire application can be achieved by manually deploying a new instance of the monolith on another server, typically behind a load-balancing appliance - another pricey solution.

#### 3. Inavitable Downtime

During upgrades, patches or migrations of the monolith application downtime is inevitable. Also, keeping all systems at the same patch level and may introduce new possible licensing costs.

## What is a Microservice

<img src="assets/images/microservices.drawio.png" />

A microservices is an independent service that communicate with each other over a network. It can be developed, deployed, and scaled independently. Microservices are loosly coupled, distributed components, each microservice can be deployed individually on separate servers provisioned with fewer resources to perform a specific busniess function; Together, microservices make up the entire system.

Microservices architecture aligns with Event-driven Architecture and Service-Oriented Architecture (SOA) principles, where complex applications are composed of small independent processes which communicate with each other through Application Programming Interfaces (APIs) over a network.

### Benifits of Microservices

#### 1. Scalability

Microservices can be scaled individually, allowing for growth without affecting other services. With the overall application becoming modular, each microservice can be scaled individually, either manually or automated through demand-based autoscaling.

#### 2. Flexibility

Seamless upgrades and patching processes are other benefits of microservices architecture. There is virtually no downtime and no service disruption to clients because upgrades are rolled out seamlessly.

#### 3. Faster Development and Agility

Benefited from the development flexibility, businesses are able to develop and roll-out new features and updates a lot faster, in an agile approach, having separate teams focusing on separate features, thus being more productive and cost-effective.

## Path from Monolith to Microservices

The Natural step in the path of the monolith to microservices transition was refactoring; However, migrating a decades-old application to the cloud through refactoring poses serious challenges and the enterprise faces the refactoring approach dilemma: a "Big-bang" approach or an incremental refactoring.

### The "Big-bang" Refactoring Approach (Not Recommended)

The "Big-bang" approach attemps to comopletely rewrite and deploy the entire systems as microservices all at once, which is generally considered as risky and impractical strategy due to the potential for large disruptions

### The Incremental Refactoring Approach

Incremental refactoring involves gradually transforming a monolithic system into a microservices architecture. This strategy allows you to progressively decompose a monolith into microservices, reducing the risk of business disruption.

This approach guarantees that new features are developed and implemented as modern microservices which are able to communicate with the monolith through APIs, without appending to the monolith's code. In sum, it offers a gradual transition from a legacy monolith to modern microservices architecture and allows for phased migration of application features into the cloud.

### Challenges of Refactoring and Application Containers

[WIP]
