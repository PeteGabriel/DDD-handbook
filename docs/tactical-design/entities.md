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

Many concepts within a business domain cannot be described solely by their attributes. A customer, bank account, order, or product has a lifecycle that evolves over time while remaining the same conceptual object.

Unlike Value Objects, which are immutable and interchangeable when their values are equal, Entities have an identity that persists throughout their lifetime. Their state may change, but their identity remains constant.

Designing Entities correctly is essential because they encapsulate business behavior and ensure that every state transition preserves the rules of the domain.

---

## The concept

An **Entity** is a domain object that is defined by its **identity** rather than by its attributes.

Two Entities may contain identical data yet represent completely different concepts if their identities differ.

For example, two customers may have the same name, address, and phone number, yet still represent different customer accounts.

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

Although their attributes are identical, they are distinct Entities because they have different identities.

---

## Key characteristics

A well-designed Entity has the following characteristics.

### Identity

Every Entity has a unique identity that distinguishes it from every other Entity.

The identity remains stable throughout the Entity's lifetime, even if its attributes change.

---

### Mutable state

Unlike Value Objects, Entities evolve over time.

Examples include:

- A bank account balance changes.
- An order changes status.
- A customer updates their address.
- A product price changes.

The Entity remains the same Entity despite these changes.

---

### Encapsulated behavior

An Entity should expose behavior rather than raw data.

Instead of allowing arbitrary modifications to its internal state, it provides methods that represent meaningful business operations.

---

### Business invariants

Every state transition must preserve the rules of the business.

The Entity is responsible for protecting these invariants regardless of who calls it.

---

## Designing Entities

### Give every Entity a clear identity

An Entity should always have an identifier that uniquely distinguishes it.

```go
type Customer struct {
    id      CustomerID
    name    string
    address Address
}
```

Notice that the `Address` is a Value Object embedded inside the Entity.

Entities often contain Value Objects that describe parts of their state.

---

### Validate during construction

Construction should guarantee that every newly created Entity starts in a valid state.

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

Once construction succeeds, the Entity satisfies its initial business rules.

---

### Protect internal state

Internal fields should remain private.

```go
type BankAccount struct {
    id      string
    owner   string
    balance int64
}
```

This prevents external code from modifying the Entity in ways that violate business rules.

---

### Expose meaningful behavior

Instead of exposing setters, expose operations that exist within the business domain.

Good examples include:

- Deposit
- Withdraw
- Cancel
- Approve
- Ship
- Reserve
- Activate

These methods describe business behavior rather than technical implementation.

---

## Implementation in Go

A typical Entity exposes business operations through methods.

```go
func (a *BankAccount) Deposit(amount int64) error {
    if amount <= 0 {
        return errors.New("deposit amount must be positive")
    }

    a.balance += amount
    return nil
}
```

Likewise:

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

Every mutation passes through business logic that enforces the Entity's invariants.

---

## Avoid setters

One of the most common design mistakes is exposing setters for every field.

For example:

```go
account.SetBalance(account.Balance() + 100)
account.AddTransfer(transfer)
```

These are two separate operations.

If the second call is forgotten or fails, the Entity enters an inconsistent state:

- Balance has changed.
- Transfer history has not.

Instead, expose a single operation representing the business action.

```go
err := account.Deposit(100)
```

Internally, `Deposit` can:

- Validate the amount.
- Update the balance.
- Record the transfer.
- Publish a Domain Event.
- Maintain every invariant.

The caller cannot accidentally leave the Entity partially updated.

---

## Ubiquitous Language

Business operations should be expressed using the language of the domain.

For example, a banking expert naturally speaks about:

- Depositing money
- Withdrawing money
- Closing an account

They do not think in terms of:

- Setting the balance
- Updating a database field
- Appending to a transaction list

Designing Entity methods using business terminology makes the code easier for both developers and domain experts to understand.

---

## Practical example

Consider a bank account.

Possible operations include:

```text
Bank Account

✓ Deposit
✓ Withdraw
✓ Freeze
✓ Close
✓ Reopen
```

Notice what is **not** exposed:

```text
SetBalance()

SetStatus()

SetTransactionHistory()
```

These methods describe implementation details rather than business behavior.

---

## Common pitfalls

### Treating Entities as data containers

An Entity should encapsulate business behavior, not simply expose fields.

---

### Exposing setters

Generic setters make it possible to violate business rules.

Instead, expose meaningful domain operations.

---

### Breaking invariants

Whenever state changes, every related business rule must remain valid.

Updating only part of an Entity often leads to inconsistent state.

---

### Comparing Entities by attributes

Entities are compared by identity, not by their data.

Two customers with identical names are still different customers if they have different identifiers.

---

## Best practices

- Give every Entity a stable identity.
- Keep fields private.
- Validate initial state during construction.
- Expose business operations instead of setters.
- Protect business invariants during every state change.
- Use Value Objects to model descriptive parts of an Entity.
- Design methods using the Ubiquitous Language of the domain.

---

## Related concepts

- Value Objects
- Aggregates
- Domain Events
- Ubiquitous Language

---

## Key takeaways

- Entities are defined by identity rather than by their attributes.
- Unlike Value Objects, Entities are mutable.
- Every state transition must preserve business invariants.
- Encapsulation prevents invalid state from being created.
- Business operations should be expressed through meaningful domain methods rather than generic setters.
- Well-designed Entities protect the integrity of the domain model while providing a clear and expressive API.
