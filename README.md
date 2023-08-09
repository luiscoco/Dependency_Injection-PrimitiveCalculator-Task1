# Dependency_Injection-PrimitiveCalculator-Task1

This is the solution for "Task #1. initialize the calculator using Pure DI":

```csharp
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection.Extensions;
using Microsoft.Extensions.Options;
using System;
using System.Linq;

namespace Calculator;

class Program
{
    static void Main(string[] args)
    {
        PrimitiveCalculator calculator = CreateCalculator();
       
        
        ConsoleKey exitKey = ConsoleKey.A;
        while (exitKey != ConsoleKey.D0 && exitKey != ConsoleKey.NumPad0)
        {
            calculator.Run();

            Console.WriteLine("To exit press 0: ");
            exitKey = Console.ReadKey().Key;
        }
    }

    private static PrimitiveCalculator CreateCalculator()
    {
        IConfigurationRoot configuration = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .Build();

        var serviceProvider = new ServiceCollection()
            .AddOptions()
            .Configure<DisplayOptions>(configuration.GetSection(nameof(DisplayOptions)))
            .AddScoped<IMenu, Menu>()
            .AddScoped<IOperationRunner, OperationRunner>()
            .AddScoped<ICalculatingOperation, Add>()
            .AddScoped<ICalculatingOperation, Subtract>()
            .AddScoped<ICalculatingOperation, Multiply>()
            .AddScoped<ICalculatingOperation, Divide>()
            .AddScoped<ICalculatingOperation, Power>()
            .AddScoped<ICalculatingOperation, Root>()
            .BuildServiceProvider();

        var menu = serviceProvider.GetRequiredService<IMenu>();

        var operationRunner = serviceProvider.GetRequiredService<IOperationRunner>();

        var registeredOperations = serviceProvider.GetServices<ICalculatingOperation>();
        Console.WriteLine($"Number of registered operations luis2: {registeredOperations.Count()}");


        var displayOptions = serviceProvider.GetRequiredService<IOptions<DisplayOptions>>();

        return new PrimitiveCalculator(menu, operationRunner, displayOptions);
    }
}
```
