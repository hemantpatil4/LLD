# Level 3 practice

Answer after reading the level. Solutions are not in this file. Levels 1 and 2 practice stay open.

## 1. Ownership choice

For each pair, choose: created inside, created outside then owned, injected, or shared. One sentence why.

- `Car` and `Engine`
- `ExitGate` and payment method
- `MeetingRoom` and `Employee`
- `ParkingLot` and `Floor`
- `TicketPrinter` and `Ticket`

## 2. Draw the relationships

Design a library desk:

- a library has shelves
- a shelf holds copies
- a member borrows a copy and receives a loan

Draw ASCII with the correct diamonds or plain lines. Mark multiplicity.

## 3. Multiplicity

Fill in the blanks:

```text
ParkingLot _ <*>---- _ Floor
ParkingSpot _ ---- _ Vehicle
Customer _ ---- _ Order
Ticket _ ---- _ Vehicle
```

## 4. Composition vs inheritance

A duck can fly and quack. A rubber duck cannot fly.

Show a bad inheritance design, then a composition design for flying.

## 5. Whiteboard sketch

> Design a hotel booking desk. A hotel has floors. A floor has rooms. A guest books a room and receives a reservation. Payment may be card or UPI later.

Draw:

- class boxes
- relationships
- one interface
- key methods on two classes

## 6. Interview follow-up

Someone draws:

```text
ParkingSpot <*>---- Vehicle
```

What is wrong, and what relationship would you draw instead?
