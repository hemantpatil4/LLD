# UML for interviews

You need only the symbols that help you explain a design on a whiteboard. Skip sequence diagrams unless the interviewer asks.

## Class box

```text
+------------------+
|   ParkingSpot    |
+------------------+
| - id             |
| - isOccupied     |
+------------------+
| + Park()         |
| + Leave()        |
+------------------+
```

Top: name. Middle: important fields. Bottom: important public methods. In a real interview you often draw only the name and two or three methods.

`+` means public. `-` means private. Say that once. Do not decorate every field.

## Interface

```text
      <<interface>>
     IPaymentMethod
           ^
           |
     implements
           |
    +------+------+
    |             |
CardPayment   UpiPayment
```

ASCII options that interviewers accept:

```text
ExitGate ----> IPaymentMethod <---- CardPayment

        IPaymentMethod
              |
        implements
              |
        CardPayment
```

## Inheritance

```text
        Vehicle
           ^
           | extends
           |
    +------+------+
    |             |
   Bike          Car
```

Or:

```text
Bike --|> Vehicle
```

The hollow triangle points at the parent. In ASCII, `--|>` or a box under a parent with "extends" is enough.

## Association

A lasting link. No ownership claim.

```text
Customer 1 ---- * Order
Member * ---- * Book
```

A plain line, or a solid arrow toward the object that is known.

## Aggregation

Whole-part. The part can live alone. Hollow diamond on the whole.

```text
Department <>---- Professor
MeetingRoom <>---- Employee
```

## Composition

Whole-part. The part dies with the whole. Filled diamond on the whole.

```text
ParkingLot <*>---- Floor
Car <*>---- Engine
Order <*>---- OrderLine
```

## Dependency

Used for one call. Not stored. Dashed arrow.

```text
TicketPrinter - - -> Ticket
PricingService - - -> CurrencyPair
```

## Multiplicity and cardinality

These two words mean the same interview idea: how many of each side.

| Symbol | Meaning |
| --- | --- |
| `1` | exactly one |
| `0..1` | optional |
| `*` or `0..*` | zero or more |
| `1..*` | one or more |

Examples:

```text
ParkingLot 1 <*>---- 1..* Floor
Floor 1 <*>---- 1..* ParkingSpot
ParkingSpot 0..1 ---- 0..1 Vehicle
Ticket 1 ---- 1 Vehicle
Customer 1 ---- * Order
```

Say the sentence while you write the numbers:

> One lot has one or more floors. One floor has one or more spots. A spot may hold zero or one vehicle.

## One full parking sketch

```text
                 +----------------+
                 |  ParkingLot    |
                 +----------------+
                         <*>
                         |
                 +-------v--------+
                 |     Floor      |
                 +----------------+
                         <*>
              +----------+----------+
              |                     |
       +------v------+       +------v------+
       | ParkingSpot |       | ParkingSpot |
       +-------------+       +-------------+
              |
              | 0..1
              v
         +---------+         +--------+
         | Vehicle | <------ | Ticket |
         +---------+   1..1  +--------+

ExitGate ----> IPaymentMethod
                    ^
                    |
             CardPayment / UpiPayment
```

## Symbols cheat

```text
----      association
<>----    aggregation (hollow diamond on whole)
<*>----   composition (filled diamond on whole)
--|>      inheritance (triangle at parent)
- - ->    dependency
---->     "knows" / uses / points to
```

## What not to draw

- Every private helper
- Database tables unless asked
- Sequence arrows for every method call
- Fancy tools. Whiteboard ASCII and boxes are the interview skill
