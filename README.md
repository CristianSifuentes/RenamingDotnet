# Renaming a Console Project in .NET and Updating References

## Overview

Renaming a .NET console project requires updating various references within the solution to prevent build errors. This guide provides a step-by-step approach to correctly rename a .NET console project and update all relevant references.

## Steps to Rename a .NET Console Project

### 1. Rename the Project Folder and `.csproj` File

```sh
mv OldProjectName NewProjectName
mv NewProjectName/OldProjectName.csproj NewProjectName/NewProjectName.csproj
```

### 2. Update the `.csproj` File

Open the `.csproj` file in a text editor and update the `RootNamespace` and `AssemblyName`:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net7.0</TargetFramework>
    <RootNamespace>NewProjectName</RootNamespace>
    <AssemblyName>NewProjectName</AssemblyName>
  </PropertyGroup>
</Project>
```

Alternatively, use the following command to update all occurrences:

```sh
sed -i 's/OldProjectName/NewProjectName/g' NewProjectName/NewProjectName.csproj
```

### 3. Update the Solution (`.sln` File)

Remove the old project reference and add the renamed project:

```sh
dotnet sln remove OldProjectName/OldProjectName.csproj
dotnet sln add NewProjectName/NewProjectName.csproj
```

### 4. Update Namespaces in All `.cs` Files

Find and replace all occurrences of the old namespace in C# files:

```sh
find NewProjectName -type f -name "*.cs" -exec sed -i 's/namespace OldProjectName/namespace NewProjectName/g' {} +
```

### 5. Update `Program.cs` and Other Internal References

Manually check `Program.cs`, `Startup.cs`, and any other files that might contain references to `OldProjectName`.

### 6. Restore and Build the Solution

```sh
dotnet restore
dotnet build
```

### 7. Run the Renamed Console Project

```sh
dotnet run --project NewProjectName/NewProjectName.csproj
```

## Automating the Renaming Process

For ease of use, you can create a script to automate the renaming process:

```sh
#!/bin/bash

OLD_NAME=$1
NEW_NAME=$2

# Rename project folder
mv $OLD_NAME $NEW_NAME

# Rename .csproj file
mv $NEW_NAME/$OLD_NAME.csproj $NEW_NAME/$NEW_NAME.csproj

# Replace references in .csproj
sed -i "s/$OLD_NAME/$NEW_NAME/g" $NEW_NAME/$NEW_NAME.csproj

# Update .sln file
dotnet sln remove $OLD_NAME/$OLD_NAME.csproj
dotnet sln add $NEW_NAME/$NEW_NAME.csproj

# Replace namespace in all C# files
find $NEW_NAME -type f -name "*.cs" -exec sed -i "s/namespace $OLD_NAME/namespace $NEW_NAME/g" {} +

# Restore and build
dotnet restore
dotnet build

echo "Project renamed successfully from $OLD_NAME to $NEW_NAME!"
```

Usage:

```sh
chmod +x rename_project.sh
./rename_project.sh OldProjectName NewProjectName
```

## Summary of Steps

|                                  |                                                                                                                      |   | **Step** | **Command** |
| -------------------------------- | -------------------------------------------------------------------------------------------------------------------- | - | -------- | ----------- |
| Rename project folder            | `mv OldProjectName NewProjectName`                                                                                   |   |          |             |
| Rename `.csproj` file            | `mv NewProjectName/OldProjectName.csproj NewProjectName/NewProjectName.csproj`                                       |   |          |             |
| Update `.csproj` references      | `sed -i 's/OldProjectName/NewProjectName/g' NewProjectName/NewProjectName.csproj`                                    |   |          |             |
| Update `.sln` references         | `dotnet sln remove OldProjectName/OldProjectName.csproj && dotnet sln add NewProjectName/NewProjectName.csproj`      |   |          |             |
| Update namespaces in `.cs` files | `find NewProjectName -type f -name "*.cs" -exec sed -i 's/namespace OldProjectName/namespace NewProjectName/g' {} +` |   |          |             |
| Restore and Build                | `dotnet restore && dotnet build`                                                                                     |   |          |             |

This guide ensures that your .NET console project is fully renamed and all references are correctly updated. 🚀

