# The implementation of the Strategy Design Pattern using Keyed Services in .NE T8

https://medium.com/@baristanriverdi/implementing-the-strategy-design-pattern-with-keyed-services-in-net-8-9bf6995651d3

Introduction

The Strategy Design Pattern is a commonly utilized pattern in software development that enables us to define a group of algorithms and encapsulate each one. Interfaces and abstract classes can create a set of related algorithms that clients can select from at runtime. This approach encourages using patterns that allow flexibility in choosing the appropriate implementation. This article clarifies how dependency injection and built-in service providers are used to implement the Strategy Design Pattern with Keyed Services in .NET 8. This approach enhances the flexibility and maintainability of software systems and improves integration with other services and components.

Strategy Design Pattern Overview

Let’s take a brief overview of the Strategy Design Pattern’s core concepts before we jump into the implementation details.

Context: The Context specifies and executes the Strategy through a provided method. (e.g., ExecuteStrategy).
Strategy: The Strategy is a contract for all concrete strategies, usually an interface or an abstract class with an encapsulated algorithm method (e.g., Execute).
The Strategy Design Pattern is implemented in this code example to carry out arithmetic operations by considering user-defined keys. These keys could include addition and subtraction.

Keyed Services Overview

Understanding Keyed Services is crucial when implementing the Strategy Design Pattern in .NET 8 using dependency injection. We can dynamically manage dependencies and resolve services based on specific keys or identifiers by utilizing Keyed Services. This enhances the flexibility of selecting and injecting different implementations at runtime. So, let’s delve into the primary components of this approach:

Registration of Keyed Services: In .NET 8, services are registered in the dependency injection container using specific keys, such as enums or strings, uniquely identifying each service implementation. This approach allows multiple implementations of the same interface to coexist within the container, each with a distinct key.

Resolving Services by Key: A service implementation can be retrieved using its key from the dependency injection container during runtime. This approach can be helpful when selecting a service implementation dynamically based on application state, user input, or configuration.

Integration with the Strategy Pattern: Combining Keyed Services with the Strategy Design Pattern enables the registration of every concrete strategy as a keyed service. By integrating this approach, the context can dynamically choose the appropriate strategy implementation based on a given key. This results in improved flexibility and maintainability of the application since strategies can be modified or added without requiring changes to the client code.

The Strategy Design Pattern, when combined with Keyed Services, can offer .NET 8 applications a high degree of flexibility and dynamic behavior. This approach adheres to the principles of the Strategy Design Pattern while leveraging the robust dependency injection framework provided by .NET 8. By selecting and executing various algorithms at runtime, applications can achieve high adaptability. The application’s architecture facilitates the implementation of this approach, which can result in significant benefits for businesses and academia.

Implementation Details

Acquisition: By implementing this approach, we will have a context that takes input, output, and a strategy to run dynamically. It means that by using this context, we can have several strategy patterns with different inputs and outputs if we so desire.

I have defined the necessary interfaces and classes for implementing the Strategy Design Pattern with Keyed Services in .NET 8.

```
public interface IContext
{
    Task<TOut> ExecuteStrategy<TIn, TOut, TStrategy>(string key, TIn model)
        where TStrategy : IStrategy<TIn, TOut>;
}
public class Context(IServiceProvider serviceProvider) : IContext
{
    public async Task<TOut> ExecuteStrategy<TIn, TOut, TStrategy>(string key, TIn model)
        where TStrategy : IStrategy<TIn, TOut>
    {
        ArgumentException.ThrowIfNullOrWhiteSpace(key);
        ArgumentNullException.ThrowIfNull(model);

        var strategy = serviceProvider.GetKeyedService<TStrategy>(key);

        StrategyNotFoundException.ThrowIfNull(strategy);

        return await strategy!.Execute(model);
    }
}
public interface IStrategy<in TIn, TOut>
{
    public Task<TOut> Execute(TIn input);
}
```

In the code snippets above, I have defined the "IContext" interface, which contains a method called "ExecuteStrategy" for executing strategies. The "Context" class implements this interface and leverages the .NET Core "IServiceProvider" to retrieve the appropriate strategy based on the provided key.

Next, I have defined a simple "ContextModel" class to represent the input data for our mathematical operations and created two concrete strategies, "SumStrategy" and "SubtractStrategy," that implement the "IMathStrategy" interface.

```
public class ContextModel
{
    public int FirstNumber { get; set; }
    public int SecondNumber { get; set; }
}
public interface IMathStrategy : IStrategy<ContextModel, int>
{
}
public class SumStrategy : IMathStrategy
{
    public Task<int> Execute(ContextModel input)
    {
        var response = input.FirstNumber + input.SecondNumber;
        return Task.FromResult(response);
    }
}
public class SubtractStrategy : IMathStrategy
{
    public Task<int> Execute(ContextModel input)
    {
        var response = input.FirstNumber - input.SecondNumber;
        return Task.FromResult(response);
    }
}
```

Dependency Injection Configuration

We should configure services in the Program.cs class to utilize dependency injection and keyed services.

```
builder.Services.AddScoped<IContext, Context>();

builder.Services.AddKeyedScoped<IMathStrategy, SumStrategy>(nameof(SumStrategy));
builder.Services.AddKeyedScoped<IMathStrategy, SubtractStrategy>(nameof(SubtractStrategy));
```

In the Program.cs, I register the "IContext" interface with the "Context" class implementation. Moreover, I utilize the "AddKeyedScoped" method to register the "IMathStrategy" interface with the "SumStrategy" and "SubtractStrategy" implementations, associating them with unique keys.

Usage Example

Now that I have set up services and defined my strategies, let’s explore how I can utilize the "IContext" interface to implement these strategies.

```
var response = await _context.ExecuteStrategy<ContextModel, int, IMathStrategy>("SumStrategy", new ContextModel()
{
    FirstNumber = 6,
    SecondNumber = 2
});
```

In this usage example, I call the "ExecuteStrategy" method on an instance of "IContext" (retrieved through dependency injection), specifying the key "SumStrategy" to execute the addition operation on the provided "ContextModel" data.

Conclusion

In this article, we have explored the implementation of the Strategy Design Pattern using Keyed Services in .NET 8. By leveraging dependency injection and the .NET Core service provider, we have created a context that executes strategies based on user-defined keys. The ability to select and exchange different algorithms at runtime provides significant adaptability and scalability, making it an effective tool for developing software systems that are both flexible and easy to maintain.

Please do not hesitate to contact me if you have any questions.

Peace | Love | Code
