# Event Storming

## Purpose

Software projects rarely fail because of a lack of technology. Modern frameworks, cloud platforms, and even AI can generate large portions of an application with remarkable speed. The greater challenge is understanding **what the software should actually do**.

A technically well-built application can still fail if it solves the wrong problem, models the domain incorrectly, or becomes impossible to evolve as business requirements change.

Domain-Driven Design emphasizes building a deep understanding of the business domain before focusing on implementation. Event Storming is one of the most effective techniques for achieving that shared understanding.

---

# What is Event Storming?

Event Storming is a collaborative workshop technique created by Alberto Brandolini for exploring, discovering, and designing complex business domains.

Rather than starting with classes, databases, APIs, or user interfaces, participants begin by identifying **business events**—important things that happen within the domain.

The workshop brings together people with different perspectives, including:

* Domain experts
* Business stakeholders
* Developers
* Architects
* Product owners
* Testers

The objective is not to produce perfect documentation, but to create a **shared understanding** of how the business operates.

---

# Why Event Storming Matters

One of the biggest risks in software development is implementing the wrong solution. Even if the code is technically correct, it may fail to satisfy business needs if the team lacks a common understanding of the domain.

Event Storming helps teams:

* Discover how the business actually works.
* Build a common language between technical and non-technical participants.
* Reveal missing knowledge and assumptions.
* Identify inconsistencies in the current understanding.
* Establish clear boundaries within the domain.
* Create a foundation for Domain-Driven Design models.

The discussions that occur during an Event Storming session are often more valuable than the final board itself.

---

# How Event Storming Works

Participants collaboratively build a timeline of business behavior using sticky notes placed from left to right.

Each note represents a different concept within the system.

The timeline gradually tells the story of how the business operates.

As participants add events, gaps naturally emerge where nobody is certain what happens next. These gaps expose missing knowledge and encourage valuable discussions that improve the team's understanding of the domain.

The emphasis is on collaboration rather than formal modeling. Unlike UML diagrams or technical documentation, Event Storming is designed so everyone—regardless of technical background—can actively participate.

---

# Common Uses

Event Storming can be applied throughout the software lifecycle.

### Discovering a New Domain

When starting a new project, Event Storming helps stakeholders and developers build a shared understanding of the business before implementation begins.

### Designing Solutions

Development teams can use Event Storming to discuss implementation strategies, divide work into manageable pieces, and identify domain boundaries.

### Understanding Existing Systems

Event Storming is equally valuable for legacy systems. Reconstructing the intended business process often reveals differences between how the system was designed and how it currently behaves.

### Facilitating Team Discussions

Even informal design conversations can benefit from Event Storming. Creating a shared visual representation reduces misunderstandings and provides a lasting reference for future discussions.

---

# Sticky Note Types

Although the notation can vary slightly between organizations, the following color convention is widely used.

| Color           | Represents         | Description                                                                       |
| --------------- | ------------------ | --------------------------------------------------------------------------------- |
| 🟧 Orange       | Domain Event       | Something important that happened in the business.                                |
| 🟦 Blue         | Command            | An action requested from the system.                                              |
| 🟨 Small Yellow | Actor              | The person or system initiating a command.                                        |
| ⚪ White         | Input / Notes      | Additional information supplied to a command or explanatory notes.                |
| 🟩 Green        | View / Read Model  | Information presented to users or consulted when making decisions.                |
| 🩷 Pink         | External System    | A system outside the current bounded context.                                     |
| 🟨 Large Yellow | Entity / Aggregate | The domain object responsible for handling commands and enforcing business rules. |

The exact colors are less important than understanding the concepts they represent.

---

# Domain Events

Domain Events are the central element of Event Storming.

A Domain Event represents a meaningful change that has already occurred within the business.

For this reason, events are always written in the **past tense**.

Good examples include:

* Customer Registered
* Payment Received
* Order Shipped
* Invoice Paid

Poor examples include:

* Register Customer
* Payment
* Shipping
* User Registration

The difference is subtle but important.

A phrase such as **"Register Customer"** describes an intention or action.

A phrase such as **"Customer Registered"** identifies the exact moment when the business state changed.

Domain-Driven Design places great importance on these state transitions because they represent facts that cannot be undone.

---

# Two Levels of Event Storming

## Big Picture Event Storming

Big Picture Event Storming focuses on the business as a whole.

The primary goal is to understand the sequence of business events without becoming distracted by technical implementation.

Typical characteristics include:

* Mostly Domain Events
* High-level business flow
* Cross-team collaboration
* Identification of major business processes

This level helps everyone agree on how the business operates before discussing software design.

---

## Process-Level Event Storming

Process-Level Event Storming zooms into a specific part of the business flow.

Additional elements are introduced around each Domain Event, including:

* Commands
* Actors
* Inputs
* Entities
* Aggregates
* External systems
* Read models

The resulting board contains enough information to begin designing the software solution, identifying API endpoints, defining aggregates, and planning implementation work.

---

# Key Takeaways

* Event Storming is a collaborative modeling technique for exploring complex business domains.
* The primary objective is building a shared understanding rather than producing documentation.
* Domain Events are the foundation of the model and are always expressed in the past tense.
* The timeline exposes gaps in knowledge and encourages productive discussions.
* Big Picture Event Storming focuses on business processes, while Process-Level Event Storming introduces implementation-oriented concepts.
* Event Storming serves as an effective bridge between business knowledge and software design, making it one of the foundational practices of Domain-Driven Design.
