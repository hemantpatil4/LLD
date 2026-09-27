# Level 1 practice

Answer these after reading the level. Solutions are not in this file.

## 1. Encapsulation

`BankAccount` is used for FX settlement. Write the class so a caller cannot do `Balance = -50000`. Include a `Withdraw` method that refuses a non-positive amount and an amount larger than the balance.

## 2. Interface

A notification system sends email today. SMS and WhatsApp may be added later. The order flow should keep calling one method.

Show the interface and one implementing class. Say why you chose an interface.

## 3. Abstract class

Every dealer account stores an id and a balance, and `Deposit` works the same way. Each desk computes commission differently.

Sketch the types. Say why this is an abstract class.

## 4. Composition

A `ParkingLot` is made of `Floor` objects. A floor is not used outside that lot.

Name the relationship. Show the field and where the floor is created.

## 5. Aggregation

Employees exist before a meeting room is booked, and they still exist if the room is removed from the system. The room holds a list of them for a booking.

Name the relationship. Show the method that receives an employee.

## 6. Dependency

`TicketPrinter.Print` needs a ticket only while printing.

Name the relationship. Show the method signature.

## 7. Value object

Write a `Money` type for a quoted FX amount. After creation, the amount and currency cannot change. Adding money returns a new `Money`. Reject a mismatched currency.

## 8. Sorting types

Classify each as entity, value object, service, or DTO. One sentence on why.

- `Customer`
- `CurrencyPair`
- `PricingService`
- `PlaceOrderRequest`

## 9. Static

Someone suggests `static class ParkingLot` because the company has one lot. What problem does that create, and what would you do instead? A short answer is enough.

## 10. Interview problem

> Design a coffee machine. It can make espresso and latte. A drink has a name and a price. The machine tracks water and milk. Making a drink uses some of those amounts. Latte uses milk. Espresso does not.

Provide:

- the classes
- one interface or abstract class, if you use one, and why
- whether the machine's relationship to its water tank is composition or aggregation, and why
- which type is an entity, which is a value object, and which is a service, if you have one
- the public methods a caller is allowed to use

You do not need a full program. A short design plus the important C# types is enough.
