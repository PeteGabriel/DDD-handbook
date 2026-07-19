---
title: Value Objects
description: Learn how Value Objects model immutable concepts without identity in Domain-Driven Design.
tags:
  - ddd
  - tactical-design
  - value-objects
status: review
last_updated: 2026-07-19
---

# Value Objects

## Why this matters

Many concepts in a business domain are defined solely by their attributes rather than by an identity. Examples include money, email addresses, physical dimensions, and geographic coordinates. Treating these concepts as simple primitive types or mutable data structures often leads to duplicated validation logic, inconsistent state, and code that is harder to reason about.

Value Objects provide a way to model these concepts explicitly, ensuring they are always valid, immutable, and easy to use throughout the application.

---

## The concept

A **Value Object** is an object that represents a descriptive aspect of the domain and is defined entirely by the values it contains.

Unlike an Entity, a Value Object has **no identity**. Two Value Objects containing the same values are considered equal because they represent the same concept.

For example, these two amounts of money are indistinguishable from a business perspective:

```go
Money{100, "USD"}
Money{100, "USD"}
```

It does not matter which instance is used—the only thing that matters is the value it represents.

Typical Value Objects include:

- Money
- Email Address
- Phone Number
- Postal Code
- URL
- Percentage
- Date Range
- Coordinates
- Dimensions
- Temperature
- Color

---

## Key characteristics

A well-designed Value Object has the following characteristics:

### No identity

Value Objects do not have identifiers.

An email address is identified by its value, not by an ID.

### Immutable

Once created, a Value Object never changes.

Any operation that modifies a value returns a **new** Value Object rather than altering the existing one.

### Equality by value

Two Value Objects are equal if all of their attributes are equal.

```go
a, _ := NewMoney(100, "USD")
b, _ := NewMoney(100, "USD")

fmt.Println(a == b) // true
```

### Encapsulated

The internal representation should not be directly mutable.

Consumers interact with the Value Object through its public API rather than modifying its fields.

### Always valid

A Value Object should never exist in an invalid state.

Validation occurs when the object is created, ensuring every instance satisfies its business rules.

---

## Design guidelines

### Validate during construction

A Value Object should enforce its invariants when it is created.

Instead of allowing invalid objects to exist and validating them repeatedly throughout the codebase, validate once in a constructor.

```go
func NewMoney(amount int64, currency string) (Money, error) {
    if amount < 0 {
        return Money{}, errors.New("amount cannot be negative")
    }

    if currency == "" {
        return Money{}, errors.New("currency is required")
    }

    return Money{
        amount: amount,
        currency: currency,
    }, nil
}
```

Once construction succeeds, every consumer can safely assume the object is valid.

---

### Encapsulate internal state

Fields should not be directly accessible.

```go
type Money struct {
    amount   int64
    currency string
}
```

Encapsulation prevents callers from bypassing business rules by modifying internal state.

---

### Prefer immutable operations

Instead of changing an existing object, operations should return a new Value Object.

```go
total := subtotal.Add(tax)
```

After this operation:

- `subtotal` remains unchanged.
- `total` contains the result.

This greatly reduces unintended side effects.

---

## Implementation in Go

Go naturally supports immutable Value Objects by combining:

- Unexported fields
- Constructor functions
- Value receivers
- Methods that return new instances

Example:

```go
type Money struct {
    amount   int64
    currency string
}

func NewMoney(amount int64, currency string) (Money, error) {
    if amount < 0 {
        return Money{}, errors.New("amount cannot be negative")
    }

    if currency == "" {
        return Money{}, errors.New("currency is required")
    }

    return Money{
        amount: amount,
        currency: currency,
    }, nil
}

func (m Money) Add(other Money) Money {
    return Money{
        amount: m.amount + other.amount,
        currency: m.currency,
    }
}
```

Because the receiver is never modified, every `Money` instance remains immutable.

---

### Equality

If all fields are comparable, Go's `==` operator is sufficient.

```go
priceA, _ := NewMoney(100, "USD")
priceB, _ := NewMoney(100, "USD")

fmt.Println(priceA == priceB)
```

If a Value Object contains slices, maps, or other non-comparable types, implement an explicit equality method.

```go
func (m Money) Equal(other Money) bool {
    return m.amount == other.amount &&
        m.currency == other.currency
}
```

---

### Zero-value considerations

One characteristic of Go is that every type has a zero value.

This means callers can create an uninitialized Value Object without using the constructor.

```go
var money Money
```

or

```go
money := Money{}
```

Such values may not satisfy the invariants enforced by the constructor.

A common approach is to provide an `IsZero()` method that allows consumers to determine whether the object has been properly initialized.

```go
func (m Money) IsZero() bool {
    return m.amount == 0 && m.currency == ""
}
```

This pattern is similar to types in Go's standard library, such as `time.Time`.

---

## Practical examples

Some common Value Objects include:

| Value Object | Represents |
|--------------|------------|
| Money | Amount and currency |
| Email | A valid email address |
| PhoneNumber | A validated phone number |
| DateRange | A start and end date |
| Coordinate | Latitude and longitude |
| Percentage | A percentage between valid bounds |

Each encapsulates its own validation rules and behavior instead of exposing raw primitives throughout the domain.

---

## Common pitfalls

### Using primitive types everywhere

Representing concepts such as money or email addresses with `string`, `float64`, or `int` loses domain meaning and spreads validation throughout the codebase.

---

### Mutable Value Objects

Allowing callers to modify internal state breaks invariants and makes reasoning about the code more difficult.

---

### Exposing internal fields

Exported fields enable callers to place an object into an invalid state after construction.

---

### Mixing identity with value

If the business distinguishes two objects by an identifier, it is likely an Entity rather than a Value Object.

---

## Best practices

- Model descriptive domain concepts as Value Objects.
- Validate invariants during construction.
- Keep fields unexported.
- Design Value Objects to be immutable.
- Prefer behavior over exposing internal data.
- Compare Value Objects by their values rather than by identity.
- Replace primitive types with Value Objects when they carry business meaning.

---

## Related concepts

- Entities
- Aggregates
- Domain Model
- Ubiquitous Language

---

## Key takeaways

- Value Objects represent concepts that are defined entirely by their values.
- They have no identity and are compared by value.
- They should always be immutable.
- Validation belongs in the constructor so invalid state cannot be created through the public API.
- Encapsulation protects business invariants.
- Value Objects make domain models safer, more expressive, and easier to maintain.
