```C#

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Azure;
using Azure.Data.Tables;

class Program
{

    private const string ConnectionString = "UseDevelopmentStorage=true;";
    private const string TableName = "Deployments";

    static async Task Main(string[] args)
    {
        var repository = new TableStorageRepository<DeploymentEntity>(ConnectionString, TableName);

        Console.WriteLine("1️⃣ Adding a new deployment...");
        var deployment = new DeploymentEntity
        {
            PartitionKey = "Deployment",
            RowKey = Guid.NewGuid().ToString(),
            Name = "Deployment1",
            Status = "Success"
        };
        await repository.AddEntityAsync(deployment);
        Console.WriteLine("✅ Deployment added!");

        Console.WriteLine("\n2️⃣ Fetching all deployments...");
        var deployments = await repository.GetAllEntitiesAsync();
        foreach (var dep in deployments)
        {
            Console.WriteLine($"🆔 {dep.RowKey} - {dep.Name} ({dep.Status})");
        }

        Console.WriteLine("\nPress any key to exit...");
        Console.ReadKey();
    }
}

/// <summary>
/// Interface for Azure Table Storage Repository
/// </summary>
public interface ITableStorageRepository<T> where T : class, ITableEntity, new()
{
    Task AddEntityAsync(T entity);
    Task<List<T>> GetAllEntitiesAsync();
}

/// <summary>
/// Generic repository to handle Azure Table Storage operations
/// </summary>
public class TableStorageRepository<T> : ITableStorageRepository<T> where T : class, ITableEntity, new()
{
    private readonly TableClient _tableClient;

    public TableStorageRepository(string connectionString, string tableName)
    {
        _tableClient = new TableClient(connectionString, tableName);
        _tableClient.CreateIfNotExists();
        Console.WriteLine($"📦 Table '{tableName}' checked/created successfully.");
    }

    public async Task AddEntityAsync(T entity)
    {
        await _tableClient.AddEntityAsync(entity);
        Console.WriteLine($"📝 Added entity with RowKey: {entity.RowKey}");
    }

    public async Task<List<T>> GetAllEntitiesAsync()
    {
        return _tableClient.Query<T>().ToList();
    }
}

/// <summary>
/// Entity representing a deployment record in Table Storage
/// </summary>
public class DeploymentEntity : ITableEntity
{
    public string PartitionKey { get; set; } = "Deployment";
    public string RowKey { get; set; } = Guid.NewGuid().ToString();
    public string Name { get; set; }
    public string Status { get; set; }
    public DateTimeOffset? Timestamp { get; set; }
    public ETag ETag { get; set; }
}












```
