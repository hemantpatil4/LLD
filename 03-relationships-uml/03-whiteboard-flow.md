# Whiteboard flow

How to draw an LLD in about five minutes without freezing.

## The order

```text
1. Clarify the story in one sentence
2. List actors
3. List main nouns
4. Mark ownership (composition / association)
5. Mark swappable actions (interfaces)
6. Write 2-4 public methods on the key classes
7. Say multiplicity out loud
```

Do not start by naming Strategy or Factory. Draw the objects first.

## Minute-by-minute

### Minute 1 — story

> A vehicle enters a lot, gets a ticket, parks in a spot, and pays at exit.

Write that sentence at the top. It keeps you from designing a hotel by accident.

### Minute 2 — boxes

Write the nouns that deserve a box:

```text
ParkingLot
Floor
ParkingSpot
Vehicle
Ticket
ExitGate
```

Cross out words that are only fields: `entryTime`, `isOccupied`, `fee`.

### Minute 3 — lines

```text
ParkingLot <*> Floor <*> ParkingSpot
Ticket ---- Vehicle
ExitGate ----> IPaymentMethod
```

Say why each line exists in one short sentence.

### Minute 4 — methods

On each important box, write the methods an interviewer will ask about:

```text
ParkingLot: Park, Exit
ParkingSpot: CanFit, Park, Leave
Ticket: CalculateFee, Close
IPaymentMethod: Pay
```

### Minute 5 — follow-ups

Leave space for:

```text
What if EV charging?
What if two cars take one spot?
```

You will point at the diagram when those questions arrive.

## Phrases that sound strong

> Floor is composition under the lot because a floor has no meaning outside this lot.

> Payment is an interface because card and UPI should be swappable.

> Spot to vehicle is association, not composition. The vehicle is not part of the spot.

> One spot holds zero or one vehicle. That is why I wrote 0..1.

## Phrases that sound weak

> I drew a diamond because UML requires it.

> Everything implements an interface.

> I will add the relationships later.

## Tiny template you can reuse

```text
Problem: ____________________

Actors:  ____________________

Classes:
  [A]  [B]  [C]

Relationships:
  A <*>---- B
  B ---- C
  Service ----> ISomething

Key methods:
  A.
  B.
  ISomething.
```

Fill this before writing a long C# file.
