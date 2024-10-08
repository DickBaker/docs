---
title: "Connection String Builders"
description: Learn about the connection string builder classes used for different providers in ADO.NET, all of which inherit from DbConnectionStringBuilder.
ms.date: "03/30/2017"
dev_langs:
  - "csharp"
  - "vb"
---
# Connection string builders

In early versions of ADO.NET, compile-time checking of connection strings with concatenated string values did not occur, so that at run time, an incorrect keyword generated an <xref:System.ArgumentException>. Each of the .NET Framework data providers supported different syntax for connection string keywords, which made constructing valid connection strings difficult if done manually. To address this problem, ADO.NET 2.0 introduced new connection string builders for each .NET Framework data provider. Each data provider includes a strongly typed connection string builder class that inherits from <xref:System.Data.Common.DbConnectionStringBuilder>. The following table lists the .NET Framework data providers and their associated connection string builder classes.

| Provider                        | ConnectionStringBuilder class                                                              |
|---------------------------------|--------------------------------------------------------------------------------------------|
| <xref:System.Data.SqlClient>    | <xref:System.Data.SqlClient.SqlConnectionStringBuilder?displayProperty=nameWithType>       |
| <xref:System.Data.OleDb>        | <xref:System.Data.OleDb.OleDbConnectionStringBuilder?displayProperty=nameWithType>         |
| <xref:System.Data.Odbc>         | <xref:System.Data.Odbc.OdbcConnectionStringBuilder?displayProperty=nameWithType>           |
| <xref:System.Data.OracleClient> | <xref:System.Data.OracleClient.OracleConnectionStringBuilder?displayProperty=nameWithType> |

## Connection string injection attacks

 A connection string injection attack can occur when dynamic string concatenation is used to build connection strings that are based on user input. If the string is not validated and malicious text or characters not escaped, an attacker can potentially access sensitive data or other resources on the server. For example, an attacker could mount an attack by supplying a semicolon and appending an additional value. The connection string is parsed by using a "last one wins" algorithm, and the hostile input is substituted for a legitimate value.

 The connection string builder classes are designed to eliminate guesswork and protect against syntax errors and security vulnerabilities. They provide methods and properties corresponding to the known key/value pairs permitted by each data provider. Each class maintains a fixed collection of synonyms and can translate from a synonym to the corresponding well-known key name. Checks are performed for valid key/value pairs, and an invalid pair throws an exception. In addition, injected values are handled in a safe manner.

 The following example demonstrates how <xref:System.Data.SqlClient.SqlConnectionStringBuilder> handles an inserted extra value for the `Initial Catalog` setting.

```vb
Dim builder As New System.Data.SqlClient.SqlConnectionStringBuilder
builder("Data Source") = "(local)"
builder("Integrated Security") = True
builder("Initial Catalog") = "AdventureWorks;NewValue=Bad"
Console.WriteLine(builder.ConnectionString)
```

```csharp
System.Data.SqlClient.SqlConnectionStringBuilder builder =
  new System.Data.SqlClient.SqlConnectionStringBuilder();
builder["Data Source"] = "(local)";
builder["integrated Security"] = true;
builder["Initial Catalog"] = "AdventureWorks;NewValue=Bad";
Console.WriteLine(builder.ConnectionString);
```

[!INCLUDE [managed-identities](../../includes/managed-identities.md)]

The output shows that the <xref:System.Data.SqlClient.SqlConnectionStringBuilder> handled this correctly by escaping the extra value in double quotation marks instead of appending it to the connection string as a new key/value pair.

```output
data source=(local);Integrated Security=True;
initial catalog="AdventureWorks;NewValue=Bad"
```

## Build connection strings from configuration files

 If certain elements of a connection string are known beforehand, they can be stored in a configuration file and retrieved at run time to construct a complete connection string. For example, the name of the database might be known in advance, but not the name of the server.

 One of the overloaded constructors for a connection string builder takes a <xref:System.String> as an argument, which enables you to supply a partial connection string that can then be completed from user input. The partial connection string can be stored in a configuration file and retrieved at run time.

> [!NOTE]
> The <xref:System.Configuration> namespace allows programmatic access to configuration files that use the <xref:System.Web.Configuration.WebConfigurationManager> for web apps and the <xref:System.Configuration.ConfigurationManager> for Windows applications. For more information about working with connection strings and configuration files, see [Connection Strings and Configuration Files](connection-strings-and-configuration-files.md).

## See also

- [Connection Strings](connection-strings.md)
- [Privacy and Data Security](privacy-and-data-security.md)
- [ADO.NET Overview](ado-net-overview.md)
