---
layout: post
title: Using Refleciton to read items in DLL's Part 1
comments: true
---

1: Get Loaded Assemblies
========================

In this example we will review how to view all of the modules loaded in an assembly

It looks so Simple.

`` powershell
[System.AppDomain]::CurrentDomain.GetAssemblies()
``

The Command returns a RuntimeAssembly Object

``` powershell
PS C:\> [System.AppDomain]::CurrentDomain.GetAssemblies()[0] | gm

   TypeName: System.Reflection.RuntimeAssembly

Name                      MemberType Definition
----                      ---------- ----------
ModuleResolve             Event      System.Reflection.ModuleResolveEventHandler ModuleResolve(System.Object, System.ResolveEventArgs)
CreateInstance            Method     System.Object CreateInstance(string typeName), System.Object CreateInstance(string typeName, bool ignoreCase), System.Object CreateInstance(...
Equals                    Method     bool Equals(System.Object o), bool _Assembly.Equals(System.Object other)
GetCustomAttributes       Method     System.Object[] GetCustomAttributes(bool inherit), System.Object[] GetCustomAttributes(type attributeType, bool inherit), System.Object[] _A...
GetCustomAttributesData   Method     System.Collections.Generic.IList[System.Reflection.CustomAttributeData] GetCustomAttributesData()
GetExportedTypes          Method     type[] GetExportedTypes(), type[] _Assembly.GetExportedTypes()
GetFile                   Method     System.IO.FileStream GetFile(string name), System.IO.FileStream _Assembly.GetFile(string name)
GetFiles                  Method     System.IO.FileStream[] GetFiles(bool getResourceModules), System.IO.FileStream[] GetFiles(), System.IO.FileStream[] _Assembly.GetFiles(), Sy...
GetHashCode               Method     int GetHashCode(), int _Assembly.GetHashCode()
GetInterface              Method     System.Runtime.InteropServices.CustomQueryInterfaceResult ICustomQueryInterface.GetInterface([ref] guid iid, [ref] System.IntPtr ppv)
GetLoadedModules          Method     System.Reflection.Module[] GetLoadedModules(bool getResourceModules), System.Reflection.Module[] GetLoadedModules(), System.Reflection.Modul...
GetManifestResourceInfo   Method     System.Reflection.ManifestResourceInfo GetManifestResourceInfo(string resourceName), System.Reflection.ManifestResourceInfo _Assembly.GetMan...
GetManifestResourceNames  Method     string[] GetManifestResourceNames(), string[] _Assembly.GetManifestResourceNames()
GetManifestResourceStream Method     System.IO.Stream GetManifestResourceStream(type type, string name), System.IO.Stream GetManifestResourceStream(string name), System.IO.Strea...
GetModule                 Method     System.Reflection.Module GetModule(string name), System.Reflection.Module _Assembly.GetModule(string name)
GetModules                Method     System.Reflection.Module[] GetModules(bool getResourceModules), System.Reflection.Module[] GetModules(), System.Reflection.Module[] _Assembl...
GetName                   Method     System.Reflection.AssemblyName GetName(bool copiedName), System.Reflection.AssemblyName GetName(), System.Reflection.AssemblyName _Assembly....
GetObjectData             Method     void GetObjectData(System.Runtime.Serialization.SerializationInfo info, System.Runtime.Serialization.StreamingContext context), void _Assemb...
GetReferencedAssemblies   Method     System.Reflection.AssemblyName[] GetReferencedAssemblies(), System.Reflection.AssemblyName[] _Assembly.GetReferencedAssemblies()
GetSatelliteAssembly      Method     System.Reflection.Assembly GetSatelliteAssembly(cultureinfo culture), System.Reflection.Assembly GetSatelliteAssembly(cultureinfo culture, v...
GetType                   Method     type GetType(string name, bool throwOnError, bool ignoreCase), type GetType(string name), type GetType(string name, bool throwOnError), type...
GetTypes                  Method     type[] GetTypes(), type[] _Assembly.GetTypes()
IsDefined                 Method     bool IsDefined(type attributeType, bool inherit), bool _Assembly.IsDefined(type attributeType, bool inherit), bool ICustomAttributeProvider....
LoadModule                Method     System.Reflection.Module LoadModule(string moduleName, byte[] rawModule, byte[] rawSymbolStore), System.Reflection.Module LoadModule(string ...
ToString                  Method     string ToString(), string _Assembly.ToString()
CodeBase                  Property   string CodeBase {get;}
CustomAttributes          Property   System.Collections.Generic.IEnumerable[System.Reflection.CustomAttributeData] CustomAttributes {get;}
DefinedTypes              Property   System.Collections.Generic.IEnumerable[System.Reflection.TypeInfo] DefinedTypes {get;}
EntryPoint                Property   System.Reflection.MethodInfo EntryPoint {get;}
EscapedCodeBase           Property   string EscapedCodeBase {get;}
Evidence                  Property   System.Security.Policy.Evidence Evidence {get;}
ExportedTypes             Property   System.Collections.Generic.IEnumerable[type] ExportedTypes {get;}
FullName                  Property   string FullName {get;}
GlobalAssemblyCache       Property   bool GlobalAssemblyCache {get;}
HostContext               Property   long HostContext {get;}
ImageRuntimeVersion       Property   string ImageRuntimeVersion {get;}
IsDynamic                 Property   bool IsDynamic {get;}
IsFullyTrusted            Property   bool IsFullyTrusted {get;}
Location                  Property   string Location {get;}
ManifestModule            Property   System.Reflection.Module ManifestModule {get;}
Modules                   Property   System.Collections.Generic.IEnumerable[System.Reflection.Module] Modules {get;}
PermissionSet             Property   System.Security.PermissionSet PermissionSet {get;}
ReflectionOnly            Property   bool ReflectionOnly {get;}
SecurityRuleSet           Property   System.Security.SecurityRuleSet SecurityRuleSet {get;}
```

Ok so what does this mean exactly?  
We can see currently loaded modules... Havnt you been paying attention?  


2: Compiled Resources
=====================

First we will need to find a specific Assembly. We will be using something that is included in every PowerShell Session so everyone can follow along.
System.Management.Automation

### Find a Specific Assembly

``` powershell
$Assembly = [System.AppDomain]::CurrentDomain.GetAssemblies() | Where-Object FullName -Like "System.Management.Automation, *"
```

### View Resources Names

In this example we are going to use:  
Command = `[String] GetManifestResourceNames()`
``` powershell
# Get A list of names
$ResourceNames = $Assembly.GetManifestResourceNames()

# Output:
# =====
# Authenticode.resources
# CatalogStrings.resources
# AutomationExceptions.resources
# ...
```

### View Resource Info
From the Resource list we should only be looking at one as there are a ton of resources in this module and can easily get confused with Information overload.


In this example we are going to use:  
Command     = `[ManifestResourceInfo] GetManifestResourceInfo([string] ResourceName)`  
ResourceName = `"GetErrorText.resources"`


``` powershell
$ResourceName = "GetErrorText.resources"
$Assembly.GetManifestResourceInfo($ResourceName)

# Output:
# =====
# ReferencedAssembly :
# FileName           :
# ResourceLocation   : Embedded, ContainedInManifestFile
```


### View the ResourceData

In this Example we are going to use:
Command = ` [UnmanagedMemoryStream] GetManifestResourceStream([string] ResourceName) `

What Wont be covered:  
What I wont cover in this section is how to logically parse the content of these resources as there are many different types of resources available.


``` powershell
$Stream = [System.IO.MemoryStream]::New()
$Assembly.GetManifestResourceStream($ResourceName).CopyTo($Stream)
[string]::new($stream.ToArray())

# Output:
# =====
# <a resulting string of the source item>
```


### View DefinedTypes
``` powershell
$Assembly.DefinedTypes
```

In the next blog post we will cover grabbing more information in `$Assembly.DefinedTypes`