# Mogly-boxrr API Documentation

This document provides detailed API reference for Mogly-boxrr.

## 📚 Table of Contents

1. [Overview](#overview)
2. [Core APIs](#core-apis)
3. [Data Access](#data-access)
4. [Event System](#event-system)
5. [Configuration](#configuration)
6. [Utilities](#utilities)
7. [Error Handling](#error-handling)

## 🌟 Overview

The Mogly-boxrr API provides a comprehensive set of methods and classes for building robust applications. All APIs follow modern C# conventions and support async/await patterns.

### Namespaces

- `MoglyBoxrr.Core` - Core functionality
- `MoglyBoxrr.Data` - Data access and persistence
- `MoglyBoxrr.Events` - Event system
- `MoglyBoxrr.Configuration` - Configuration management
- `MoglyBoxrr.Utilities` - Helper utilities

## 🔧 Core APIs

### Application Class

The main entry point for the application.

```csharp
namespace MoglyBoxrr.Core;

public class Application
{
    // Constructor
    public Application();
    public Application(IConfiguration configuration);
    
    // Methods
    public Task InitializeAsync();
    public Task RunAsync(CancellationToken cancellationToken = default);
    public Task StopAsync();
    
    // Properties
    public IServiceProvider Services { get; }
    public ApplicationState State { get; }
}
```

#### Example Usage

```csharp
var app = new Application();
await app.InitializeAsync();
await app.RunAsync();
```

### IModule Interface

Defines a module that can be loaded into the application.

```csharp
namespace MoglyBoxrr.Core;

public interface IModule
{
    string Name { get; }
    string Version { get; }
    
    Task InitializeAsync(IServiceProvider services);
    Task ExecuteAsync(IContext context);
    Task ShutdownAsync();
}
```

## 💾 Data Access

### IRepository<T> Interface

Generic repository pattern for data access.

```csharp
namespace MoglyBoxrr.Data;

public interface IRepository<T> where T : class
{
    Task<T?> GetByIdAsync(int id);
    Task<IEnumerable<T>> GetAllAsync();
    Task<T> AddAsync(T entity);
    Task UpdateAsync(T entity);
    Task DeleteAsync(int id);
    Task<bool> ExistsAsync(int id);
}
```

#### Example Usage

```csharp
public class DataItem
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Description { get; set; }
}

// Usage
var repository = services.GetRequiredService<IRepository<DataItem>>();
var item = await repository.GetByIdAsync(1);
```

### IUnitOfWork Interface

Manages transactions and coordinates repositories.

```csharp
namespace MoglyBoxrr.Data;

public interface IUnitOfWork : IDisposable
{
    IRepository<T> Repository<T>() where T : class;
    Task<int> SaveChangesAsync();
    Task BeginTransactionAsync();
    Task CommitTransactionAsync();
    Task RollbackTransactionAsync();
}
```

## 📡 Event System

### IEventBus Interface

Publish-subscribe event system.

```csharp
namespace MoglyBoxrr.Events;

public interface IEventBus
{
    Task PublishAsync<TEvent>(TEvent @event) where TEvent : class;
    IDisposable Subscribe<TEvent>(Func<TEvent, Task> handler) where TEvent : class;
    IDisposable Subscribe<TEvent>(Action<TEvent> handler) where TEvent : class;
}
```

#### Example Usage

```csharp
// Define an event
public class DataChangedEvent
{
    public int ItemId { get; set; }
    public DateTime Timestamp { get; set; }
    public string ChangeType { get; set; }
}

// Subscribe to events
var subscription = eventBus.Subscribe<DataChangedEvent>(async evt =>
{
    Console.WriteLine($"Item {evt.ItemId} changed at {evt.Timestamp}");
});

// Publish event
await eventBus.PublishAsync(new DataChangedEvent
{
    ItemId = 123,
    Timestamp = DateTime.UtcNow,
    ChangeType = "Update"
});

// Unsubscribe when done
subscription.Dispose();
```

### Event Base Class

Base class for all events.

```csharp
namespace MoglyBoxrr.Events;

public abstract class Event
{
    public Guid EventId { get; } = Guid.NewGuid();
    public DateTime OccurredAt { get; } = DateTime.UtcNow;
    public string EventType => GetType().Name;
}
```

## ⚙️ Configuration

### IConfigurationManager Interface

Manages application configuration.

```csharp
namespace MoglyBoxrr.Configuration;

public interface IConfigurationManager
{
    T GetValue<T>(string key);
    T GetValue<T>(string key, T defaultValue);
    bool TryGetValue<T>(string key, out T value);
    void SetValue<T>(string key, T value);
    IConfigurationSection GetSection(string sectionName);
}
```

#### Example Usage

```csharp
var config = services.GetRequiredService<IConfigurationManager>();

// Get simple values
var appName = config.GetValue<string>("Application:Name");
var timeout = config.GetValue<int>("Timeout", defaultValue: 30);

// Get complex objects
var dbSettings = config.GetSection("Database").Get<DatabaseSettings>();
```

## 🛠️ Utilities

### Logger

Logging functionality.

```csharp
namespace MoglyBoxrr.Utilities;

public interface ILogger
{
    void LogDebug(string message, params object[] args);
    void LogInformation(string message, params object[] args);
    void LogWarning(string message, params object[] args);
    void LogError(Exception exception, string message, params object[] args);
    void LogCritical(Exception exception, string message, params object[] args);
}
```

### ISerializer Interface

Serialization and deserialization.

```csharp
namespace MoglyBoxrr.Utilities;

public interface ISerializer
{
    string Serialize<T>(T obj);
    T Deserialize<T>(string data);
    byte[] SerializeToBytes<T>(T obj);
    T DeserializeFromBytes<T>(byte[] data);
}
```

## ⚠️ Error Handling

### Exception Types

```csharp
namespace MoglyBoxrr.Exceptions;

// Base exception
public class MoglyException : Exception
{
    public MoglyException(string message) : base(message) { }
    public MoglyException(string message, Exception innerException) 
        : base(message, innerException) { }
}

// Specific exceptions
public class ConfigurationException : MoglyException { }
public class DataAccessException : MoglyException { }
public class ValidationException : MoglyException { }
public class OperationException : MoglyException { }
```

### Result Pattern

For operations that may fail without throwing exceptions.

```csharp
namespace MoglyBoxrr.Core;

public class Result<T>
{
    public bool IsSuccess { get; }
    public T Value { get; }
    public string Error { get; }
    
    public static Result<T> Success(T value);
    public static Result<T> Failure(string error);
}
```

#### Example Usage

```csharp
public async Task<Result<DataItem>> GetItemAsync(int id)
{
    try
    {
        var item = await repository.GetByIdAsync(id);
        if (item == null)
            return Result<DataItem>.Failure("Item not found");
            
        return Result<DataItem>.Success(item);
    }
    catch (Exception ex)
    {
        return Result<DataItem>.Failure(ex.Message);
    }
}

// Usage
var result = await GetItemAsync(123);
if (result.IsSuccess)
{
    Console.WriteLine($"Found: {result.Value.Name}");
}
else
{
    Console.WriteLine($"Error: {result.Error}");
}
```

## 🔌 Extension Methods

### Service Collection Extensions

```csharp
namespace MoglyBoxrr.Extensions;

public static class ServiceCollectionExtensions
{
    public static IServiceCollection AddMoglyBoxrr(
        this IServiceCollection services,
        Action<MoglyOptions> configure = null);
        
    public static IServiceCollection AddModule<TModule>(
        this IServiceCollection services) 
        where TModule : class, IModule;
}
```

### String Extensions

```csharp
namespace MoglyBoxrr.Extensions;

public static class StringExtensions
{
    public static bool IsNullOrEmpty(this string value);
    public static string ToSnakeCase(this string value);
    public static string ToCamelCase(this string value);
    public static string Truncate(this string value, int maxLength);
}
```

## 📊 Performance Considerations

### Best Practices

1. **Use Async/Await**: All I/O operations are async
2. **Dispose Resources**: Implement `IDisposable` properly
3. **Batch Operations**: Use bulk operations when possible
4. **Cache Wisely**: Cache expensive operations
5. **Monitor Performance**: Use built-in diagnostics

### Example: Efficient Data Processing

```csharp
// Good: Batch processing
var items = await repository.GetAllAsync();
await Parallel.ForEachAsync(items, async (item, ct) =>
{
    await ProcessItemAsync(item, ct);
});

// Better: Use specialized batch methods if available
await repository.BulkUpdateAsync(items);
```

## 🔐 Security

### Authentication

```csharp
namespace MoglyBoxrr.Security;

public interface IAuthenticationService
{
    Task<AuthResult> AuthenticateAsync(string username, string password);
    Task<bool> ValidateTokenAsync(string token);
    Task RevokeTokenAsync(string token);
}
```

### Authorization

```csharp
namespace MoglyBoxrr.Security;

public interface IAuthorizationService
{
    Task<bool> IsAuthorizedAsync(string userId, string resource, string action);
    Task GrantPermissionAsync(string userId, string permission);
    Task RevokePermissionAsync(string userId, string permission);
}
```

## 📝 Additional Resources

- [User Guide](USER_GUIDE.md) - Comprehensive usage guide
- [Getting Started](GETTING_STARTED.md) - Quick start tutorial
- [Examples](../examples/) - Code examples and samples

---

For more information or questions, please visit our [GitHub repository](https://github.com/bujjibox/Mogly-boxrr).
