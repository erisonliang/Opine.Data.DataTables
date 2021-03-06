# Opine
Lightweight C# DataTable alternative providing filtering and grouping.

## Getting started

### Cloning and building

Clone the repository. Use the `dotnet build` command to build the projects.

```
git clone https://github.com/cpersona/Opine.Data.DataTables
cd Opine.Data.DataTables
dotnet build
```

## Motivation

The original .NET DataSet/DataTable provided a lot of useful functionality, but it came with a heavy price in terms of performance as well as the patterns and practices that formed around the dataset as developers tried to integrate them into their workflows. From a performance standpoint, DataSets were memory intensive, and serializing them across the wire meant high serialization times and large payloads. From a development perspective, DataSets provided too much functionality, eventually luring developers into treating them as domain objects and first class citizens in applications eschewing SOLID principles in the process. 

Opine DataTables provide some of the same functionality as the original DataSet datatables without all of the extra functionality that would otherwise leak into the design of clients of the library. 

## So what does it do?

At the core of DataTables is the `IDataTable` interface which is primarily an enumerable of `IDataRow` as well as providing access to the underlying data and data schema (e.g. column names and indices). `IDataRow` similarly provides access to the underlying data and data schema. 

### Data storage

The default implementation of `IDataTable` is `DataTable` which contains a single object array of all the values it contains and column information. 

```csharp
  var columns = new string[] 
  {
      "Name", "Age"
  };
  var values = new object[] 
  { 
      "Bob", 55,
      "Tom", 32,
      "Jim", 43
  };
  var table = new DataTable(columns, values);
```

### Data access

`IDataTable` provides an enumeration of the rows contained within as well as random access to rows or to individual data cells within the data. 

```csharp
  var table = GetDataTable();
  int i = 0;
  foreach (var r in table)
  {
      // All equivalent to "r"
      var sameRow = table.GetRow(i);
      var alsoSameRow = table.GetRow(r.GetRowIndex());
      i++;
  }
```

### Filtering

Filtering is done by providing a list of `IRowFilter` instances. Standard comparison filters are provided and custom filters can be created. Of course, LINQ can be used to perform filtering operations if needed. Filtered tables simply contain row indices and a pointer back to their source table making them very lightweight. 

```csharp
var filteredTable = GetDataTable().FilterBy(new IRowFilter[] { new MyCustomFilter(42) }, true);
```

A `ToDataTable` extension method has been added to `IEnumerable<IDataRow>` to make working with LINQ collections easier. See the example below or unit tests for a full example. 

```csharp
var rows = GetDataTable().Where(x => (int)x.GetValue("Age") > 40);
var table = rows.ToDataTable();
```

### Grouping

Grouping is done by providing a list of column names to group by. The result is an `IGroupedDataTable` which provides functionality for accessing the rows within a specific group. Data within a grouped table is provided as instances of `IGroupedDataRow` which similarly provides access to the grouped rows. As with filtered tables, grouped tables contain row indices and a pointer back to their source table. 

```csharp
var groupedTable = GetDataTable().GroupBy(new[] { "Age" });
```

### Serialization

Serialization of a DataTable is simple given the internal structure of the default DataTable which consists of an object array and limited column metadata. 

## Is it standalone?

Yes! This project is under the Opine project, but does not have code dependencies on Opine. Therefore, you can use this project anywhere with a simple reference. 

## Technologies
* .NET Core 2.0
* C# 7

## Optional Technologies
* N/A

## Third-party Libraries
* N/A

## References
* N/A
