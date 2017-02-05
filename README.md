# 1.0.0-**INTRO**

MSBuild is a process automation tool with *emphasis* on building .NET projects.

## 1.1.0-**Overview**

* **property** - simple string data (configuration, target architecture, output directory)
* **item** - list with *things* (files, references, resources, other)
* **task** - operation that can be performed (copy files, create folders, zip files, compile a dll)
* **target** - set of tasks that accomplishes something (Build, Rebuild, Clean)

# 2.0.0-**STRUCTURE**

## 2.1.0-**Project-element**

`Project` is the root element:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Project
  xmlns="http://schemas.microsoft.com/developer/msbuild/2003"
  DefaultTargets="Build;..."
  ToolsVersion="12.0">
  <!-- ... -->
</Project>

```

Additional project files can be imported:

```xml
<Import Project="path/to/project.targets" />
```

Standard projects that need to be imported for .NET development:

* at the beginning: `$(MSBuildExtensionsPath)\$(MSBuildToolsVersion)\Microsoft.Common.props`
* at the end: `$(MSBuildToolsPath)\Microsoft.CSharp.targets` or `$(MSBuildToolsPath)\Microsoft.VisualBasic.targets`

## 2.2.0-**Properties**

Can be added in `Project` but have to be in a `PropertyGroup`:

```xml
<PropertyGroup>
  <OutputDirectory>bin\Debug\</OutputDirectory>
  <ZipOutputDir>dist\Debug\</ZipOutputDir>
  <!-- ... -->
</PropertyGroup>
```

Properties provided by MSBuild:
<https://msdn.microsoft.com/en-us/library/ms164309.aspx>

Properties defined in default .NET targets:
<https://msdn.microsoft.com/en-us/library/bb629394.aspx>

## 2.3.0-**Items**

Can be added in `Project` but have in a `ItemGroup`:
```xml
<ItemGroup>
  <Compile Include="**/*.cs" Exclude="obj/**/*.cs" />
</ItemGroup>
```

Name of the node defines the type of the item (`Compile`, `Reference`, `EmbeddedResource` etc.).

Use `Include` attribute to define the elements that will be a part of the list (supports glob patterns).

Use `Exclude` attribute to **not** include some elements that would normally be according to the `Include` attribute.

Item types used in default .NET targets:
<https://msdn.microsoft.com/en-us/library/bb629388.aspx>

## 2.4.0-**Item-metadata**

Each item has a customizable set of metadata. To define metadata add a child node to the item:

```xml
<ItemGroup>
  <Compile Include="MainWindow.xaml.cs">
    <DependentUpon>MainWindow.xaml</DependentUpon>
  </Compile>
</ItemGroup>
```

Item type can have some default metadata defined if needed:

```xml
<ItemDefinitionGroup>
  <Compile>
    <Author>Me</Author>
  </Compile>
</ItemDefinitionGroup>
```

A list of metadata available on each item:
<https://msdn.microsoft.com/en-us/library/ms164313.aspx>

## 2.5.0-**Targets**

Target is a list of operations that needs to performed to achieve *something*.
Example targets: `Compile`, `CopyToOutput`. A target could look like this:

```xml
<ItemGroup>
  <Copyright Include="COPYRIGHT.txt" />
  <Copyright Include="LICENSE.txt" />
</ItemGroup>
<!-- ... -->
<Target Name="OutputCopyright">
  <Message Text="Starting 'OutputCopyright' target." />
  <Copy
    SourceFiles="@(Copyright)"
    DestinationFolder="$(OutputPath)" />
  <Message Text="'OutputCopyright' target finished." />
</Target>
```

The execution is governed by many factors. The most important ones:

* `/t:TargetName` switch on command-line MSBuild execution - tells MSBuild which targets to run
* `DefaultTargets` attribute on `Project` node - used when targets not specified on command-line
* `DependsOnTargets` attribute on `Target` node - lists targets which should be executed *before this one*
* `BeforeTargets` attribute on `Target` node - a list of targets before which this one should be executed
* `AfterTargets` attribute on `Target` node - a list of targets after which this one should be executed

## 2.6.0-**Tasks**

Task are operations that are used in targets (`Message`, `Copy`, `Csc`).
Tasks can take items and values as inpus as well as output them:

```xml
<SomeTaskWithOutputs>
  <Output
    TaskParameter="Output1"
    PropertyName="OutputProperty" />
  <Output
    TaskParameter="Output2"
    ItemName="OutputItem" />
</SomeTaskWithOutput>
```

List of tasks included with MSBuild:
<https://msdn.microsoft.com/en-us/library/7z253716.aspx>

MSBuild Extension Pack:
<http://www.msbuildextensionpack.com/>

MSBuild Community Tasks:
<https://github.com/loresoft/msbuildtasks>

## 2.7.0-**Conditions**

### 2.7.1-**`Condition`-attribute**

Almost every XML node can have a `Condtition` attribute, which signifies wheter this node will be taken into account:

```xml
<PropertyGroup Condition="'$(Configuration)|$(Platform)' == 'Debug|AnyCPU'">
  <DefineConstants>DEBUG;TRACE</DefineConstants>
  <!-- ... -->
</PropertyGroup>
<PropertyGroup Condition="'$(Configuration)|$(Platform)' == 'Release|AnyCPU'">
  <DefineConstants>TRACE</DefineConstants>
  <!-- ... -->
</PropertyGroup>
```

With conditions you can make the project more flexible by adjusting the behaviour to the state of the project or introduce build variants.
One such example is differentianig between Debug and Release build, other might be performing optional deployment based on a property flag.

For more info about conditions' sytax see:
<https://msdn.microsoft.com/en-us/library/7szfhaft.aspx>

### 2.7.2-**`Choose`-node**

The `Choose` node is a conditional structure that helps to deal with "one or the other" scenarios for top level elements (items, properties, targets).
The previous example could be rewritten:

```xml
<Choose>
  <When Condition="'$(Configuration)|$(Platform)' == 'Debug|AnyCPU'">
    <PropertyGroup>
      <DefineConstants>DEBUG;TRACE</DefineConstants>
      <!-- ... -->
    </PropertyGroup>
  </When>
  <When Condition="'$(Configuration)|$(Platform)' == 'Release|AnyCPU'">
    <PropertyGroup>
      <DefineConstants>TRACE</DefineConstants>
      <!-- ... -->
    </PropertyGroup>
  </When>
  <!--<Otherwise>
    <PropertyGroup>
      <DefineConstants>WTF</DefineConstants>
    </PropertyGroup>
  </Otherwise>-->
</Choose>
```

## 2.8.0-**Expressions**

```csharp
// TODO
```
```batch
REM TODO
```
```sql
-- TODO
```
```ruby
# TODO
```

# 3.0.0-**ADVANCED-TOPICS**

* batching
* custom tasks
  * code in XML
  * precompiled tasks
* visual studio integration

# 4.0.0-**THE-END**

Special thanks to:

* [Semantic Versioning](http://semver.org/) for the format of headers
* Adam P for the [Markdown Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)
* [Visual Studio Code](https://code.visualstudio.com/) for helping me write this
* [MSDN documentation](https://msdn.microsoft.com/en-us/library/dd393574.aspx) for being quite good
* [Vim extension](https://marketplace.visualstudio.com/items?itemName=vscodevim.vim) for VSCode for being a great help when editing
