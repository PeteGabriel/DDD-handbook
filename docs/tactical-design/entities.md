---
title: Entities
description: Learn how Entities model concepts with identity while protecting business invariants through controlled state changes.
tags:
  - ddd
  - tactical-design
  - entities
status: review
last_updated: 2026-07-19
---

# Entities

## Why this matters

Not every concept in a domain can be described solely by its attributes.

A customer, an order, or a bank account exists over time. Its properties may change, but it remains the same business object throughout its lifecycle. This persistence is what distinguishes an Entity from a Value Object.

Understanding this distinction is fundamental to Domain-Driven Design. While Value Objects describe concepts, Entities model the business objects that evolve over time. Designing them correctly helps protect business rules, prevents inconsistent state, and creates APIs that naturally express the language of the domain.

---

## The concept

An **Entity** is an object that is defined by its **identity**, not by the values of its attributes.

Unlike a Value Object, two Entities are considered different even if all of their properties are identical. Their identity is what makes them unique.

Imagine two customer accounts:

```text
Customer
├── Id: 4e53...
├── Name: John Smith
└── Address: ...

Customer
├── Id: a92b...
├── Name: John Smith
└── Address: ...
```

Although they contain the same information, they represent different customers because their identifiers are different.

Entities often contain Value Objects that describe parts of their state.

For example:

```go
type Customer struct {
	id      CustomerID
	name    string
	address Address
}
```

Here:

- `Customer` is an Entity.
- `Address` is a Value Object.

The Entity owns the Value Object as part of its state.

---

## Key characteristics

### Identity

Every Entity has a unique identifier that distinguishes it from every other Entity.

Its identity remains constant throughout its lifetime, even if its attributes change.

### Mutable state

Unlike Value Objects, Entities evolve over time.

Examples include:

- A bank account balance changes.
- An order moves through different statuses.
- A customer updates their address.
- A product price changes.

The object changes, but its identity does not.

### Encapsulated behavior

An Entity should expose business behavior rather than allowing arbitrary modifications to its internal state.

Consumers interact with an Entity by performing meaningful business operations.

### Protected business invariants

Every state transition must preserve the rules of the domain.

An Entity is responsible for ensuring it never enters an invalid state.

---

## Design guidelines

### Give every Entity a stable identity

Identity is what distinguishes one Entity from another.

The identifier should remain stable for the lifetime of the Entity, even if other attributes change.

```go
type Customer struct {
	id      CustomerID
	name    string
	address Address
}
```

### Validate initial state

An Entity should be created in a valid state.

Constructors are responsible for validating the initial business rules.

```go
func NewBankAccount(id, owner string) (*BankAccount, error) {
	if id == "" {
		return nil, errors.New("id is required")
	}

	if owner == "" {
		return nil, errors.New("owner is required")
	}

	return &BankAccount{
		id:    id,
		owner: owner,
	}, nil
}
```

Once created, callers can safely assume the Entity satisfies its initial invariants.

### Keep internal state private

Fields should remain unexported so callers cannot bypass business rules.

```go
type BankAccount struct {
	id      string
	owner   string
	balance int64
}
```

Encapsulation ensures that every modification passes through the Entity's public API.

### Model behavior, not data

An Entity should expose methods that represent business operations.

Good examples include:

- Deposit
- Withdraw
- Approve
- Cancel
- Ship
- Reserve

These operations describe what happens in the business rather than how the state is updated internally.

---

## Implementation

A common mistake is exposing setters for every field.

```go
account.SetBalance(account.Balance() + 100)
account.AddTransfer(transfer)
```

Although this appears harmless, these are two independent operations.

If the second call is forgotten or fails, the Entity becomes inconsistent:

- The balance has changed.
- The transfer history has not.

Instead, expose a single operation that represents the business action.

```go
err := account.Deposit(100)
```

Internally, `Deposit` can:

- Validate the amount.
- Increase the balance.
- Record the transaction.
- Publish a Domain Event.
- Preserve every business invariant.

The caller cannot accidentally perform only part of the operation.

A simple implementation might look like this:

```go
func (a *BankAccount) Deposit(amount int64) error {
	if amount <= 0 {
		return errors.New("deposit amount must be positive")
	}

	a.balance += amount

	return nil
}
```

Likewise, withdrawals enforce their own business rules.

```go
func (a *BankAccount) Withdraw(amount int64) error {
	if amount <= 0 {
		return errors.New("withdraw amount must be positive")
	}

	if amount > a.balance {
		return errors.New("insufficient funds")
	}

	a.balance -= amount

	return nil
}
```

Notice that callers never manipulate the balance directly. They express business intent, and the Entity is responsible for maintaining a valid state.

---

## Practical examples

Entities commonly represent business concepts with an independent lifecycle.

| Entity | Typical operations |
|---------|--------------------|
| Customer | Register, Change Address, Deactivate |
| Order | Confirm, Ship, Cancel |
| Bank Account | Deposit, Withdraw, Close |
| Product | Change Price, Discontinue |
| Subscription | Renew, Suspend, Cancel |

Notice that the operations are expressed using the **Ubiquitous Language** of the domain.

A banking expert naturally speaks about **depositing money**, not **setting the balance**.

The API should reflect the language of the business.

---

## Common pitfalls

### Treating Entities as data structures

An Entity should encapsulate business behavior, not simply expose fields.

### Exposing setters

Generic setters make it possible to violate business rules.

Instead, expose meaningful domain operations.

### Breaking invariants

Whenever state changes, every related business rule must remain valid.

Updating only part of an Entity often leads to inconsistent state.

### Comparing Entities by attributes

Entities are compared by identity, not by their values.

Two customers with the same name are still different customers if they have different identifiers.

---

## Best practices

- Give every Entity a stable identity.
- Keep fields private.
- Validate the initial state during construction.
- Protect every state transition.
- Expose business operations instead of setters.
- Use Value Objects to model descriptive parts of the Entity.
- Design methods using the Ubiquitous Language of the domain.

---

## Related concepts

- Value Objects
- Aggregates
- Domain Events
- Ubiquitous Language

---

## Key takeaways

- Entities are defined by their identity rather than by their attributes.
- Unlike Value Objects, Entities evolve over time.
- Every state transition must preserve business invariants.
- Encapsulation protects the integrity of the domain model.
- Business operations should express intent using the language of the domain.
- Well-designed Entities provide a safe, expressive API that naturally enforces business rules.
