# Association, aggregation, composition, dependency

These words answer one interview question: how do these two objects know each other, and who owns the lifetime?

## Association

### Simple explanation

**Association** means two objects have a lasting link. Each can exist on its own. One usually stores the other in a field.

### Analogy

A customer places orders over the years. The customer and the order are linked. Deleting a marketing campaign does not decide whether the customer exists.

### Why we need it in LLD

Most "has a reference to" lines on a whiteboard are associations. You are saying these objects work together, and you have not yet claimed that one is a physical part of the other.

### Small C# example

```csharp
public class Customer
{
    public string Id { get; }
    public List<Order> Orders { get; } = new();

    public Customer(string id) => Id = id;
}
```

`Customer` is associated with `Order`.

```text
Customer 1 ---- * Order
```

`1` and `*` are multiplicity: one customer, many orders.

### Where it appears

Customer and accounts. Member and loans. Driver and trips, once both already exist as their own records.

## Aggregation

### Simple explanation

**Aggregation** is a whole-and-part link where the part is independent. The whole holds the part. The part was created outside, and it survives if the whole goes away.

### Analogy

A department has professors. The professors were hired as people. Close the department, and the professors still exist. They can join another department.

### Why we need it in LLD

It stops you from destroying shared things by accident. A `Professor` created inside `Department` and deleted with it would be the wrong lifetime.

### Small C# example

```csharp
public class Department
{
    private readonly List<Professor> _professors = new();

    public void Add(Professor professor)
    {
        _professors.Add(professor);
    }
}
```

The caller creates `Professor` and hands it in. `Department` does not construct the professor.

```text
Department <>---- Professor
```

The hollow diamond sits on the whole side. In ASCII, `<>` is the hollow diamond.

### Where it appears

A university and departments that share a professor. A playlist and songs that also live in other playlists. Employees assigned to a meeting room.

## Composition

### Simple explanation

**Composition** is a whole-and-part link where the part's life is tied to the whole. The whole creates the part, or fully owns it. When the whole is gone, the part is gone. The part is not shared with a second whole.

### Analogy

A house and its rooms. You do not build a room in the street and later screw it onto a house as a shared room. Tear down the house, and those rooms are gone.

### Why we need it in LLD

Some objects are meaningless alone. A parking floor that outlives the parking lot, or a chess square that outlives the board, confuses the model. Composition tells the interviewer who creates the object and who is allowed to throw it away.

### Small C# example

```csharp
public class Car
{
    private readonly Engine _engine;

    public Car()
    {
        _engine = new Engine();
    }
}
```

`Car` creates `Engine`. No one outside receives that engine and installs it in a second car.

```text
Car <*>---- Engine
```

The filled diamond sits on `Car`. In ASCII, `<*>` means composition.

Another common case: the part is created outside and then fully transferred, and the design forbids reuse. In interviews, the clearer signal is "created inside" or "cannot exist without the parent."

### Where it appears

`ParkingLot` owns `Floor`. `Floor` owns `ParkingSpot`. `Board` owns `Square`. `Order` owns `OrderLine` when a line has no meaning outside that order.

```text
ParkingLot <*>---- Floor <*>---- ParkingSpot
```

### Engine: created outside, inside, injected, or shared?

| Choice | Meaning | Use when |
| --- | --- | --- |
| Created inside `Car` | Composition | The engine exists only for this car |
| Created outside and passed in, stored, not shared | Composition, with the creation moved out so tests can substitute an engine | The car still owns it after the handoff |
| Injected interface | The car depends on "something that starts," and a test or a factory supplies it | You need to swap the engine behavior |
| Shared | Aggregation or association | Two cars somehow use one engine. That is a strange car. It is normal for a professor in two committees |

For a real car, share is the wrong model. Create it inside, or inject one engine that this car alone owns.

## Dependency

### Simple explanation

A **dependency** is a short use. A method receives an object, uses it, and does not keep it as a field.

### Analogy

A trader borrows a calculator, prices the deal, and hands it back. The trader does not own the calculator.

### Why we need it in LLD

Not every use is ownership. If you store every parameter as a field, the class looks like it owns the world. A dependency keeps the whiteboard honest: this class needs that one only for this action.

### Small C# example

```csharp
public class TicketPrinter
{
    public void Print(Ticket ticket)
    {
        Console.WriteLine(ticket.Id);
    }
}
```

`TicketPrinter` depends on `Ticket` for the length of `Print`. There is no `Ticket` field.

```text
TicketPrinter - - -> Ticket
```

A dashed arrow means dependency.

### Where it appears

`PricingService.Quote(order, rate)`, validators, printers, mappers. Constructor injection that is stored in a field is a stronger link (association). A parameter used and dropped is a dependency.

## One picture

```text
Association   Customer ---- Order          lasting link, independent lives
Aggregation   Department <>---- Professor  whole-part, part survives
Composition   Car <*>---- Engine           whole-part, part dies with the whole
Dependency    Printer - - -> Ticket        used for one call, not stored
Inheritance   Bike --|> Vehicle            bike is a vehicle
```
