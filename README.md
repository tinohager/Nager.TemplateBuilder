Nager.TemplateBuilder
==========

Create automated Visual Studio Solutions and Projects

### Features
- [x] Project references
- [x] Nuget references
- [x] Add Folders
- [x] Add Files
- [x] Choose Framework Version

### nuget
The package is available on [nuget](https://www.nuget.org/packages/Nager.TemplateBuilder)
```
PM> install-package Nager.TemplateBuilder
```

##### Example
```cs

//Set solution path
var folder = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
var path = System.IO.Path.Combine(folder, @"Visual Studio 2017\Projects");

//Solution infos
var solutionName = "Nager.SystemManagement";
var solutionInfo = new SolutionInfo(solutionName, path);

//Configure "Common" project
var commonProject = new ProjectInfo($"{solutionName}.Common", ProjectTemplate.WindowsClassicDesktopClassLibrary);
commonProject.RemoveFiles = new List<ProjectFile> { new ProjectFile("Class1.cs") };
commonProject.DotNetFrameworkVersion = new Version(4, 6, 1);
commonProject.NugetPackages = new List<string> { "log4net", "System.ServiceModel.NetTcp", "System.Runtime.Serialization.Xml" };
commonProject.Folders = new List<string>() { "WCF", "Helper", "Model", "Factory" };
commonProject.Files = new List<ProjectFile>();
commonProject.Files.Add(new ProjectFile("RequestStatus.cs", "Model"));
commonProject.Files.Add(new ProjectFile("RequestResult.cs", "Model"));
commonProject.Files.Add(new ProjectFile("BindingFactory.cs", "Factory"));
commonProject.Files.Add(new ProjectFile("WrapperBase.cs", "WCF"));
commonProject.Files.Add(new ProjectFile("SingleWrapper.cs", "WCF"));
commonProject.Files.Add(new ProjectFile("DuplexWrapper.cs", "WCF"));
commonProject.Files.Add(new ProjectFile("ICommunicationService.cs", "WCF"));
commonProject.Files.Add(new ProjectFile("Client.cs", "WCF"));

//Configure "Library" project
var libraryProject = new ProjectInfo($"{solutionName}.Library", ProjectTemplate.WindowsClassicDesktopClassLibrary);
libraryProject.RemoveFiles = new List<ProjectFile> { new ProjectFile("Class1.cs") };
libraryProject.DotNetFrameworkVersion = new Version(4, 6, 1);
libraryProject.ProjectReferences = new List<ProjectInfo> { commonProject };
libraryProject.NugetPackages = new List<string> { "log4net", "Mapster", "System.ServiceModel.NetTcp", "System.Runtime.Serialization.Xml" };
libraryProject.Folders = new List<string>() { "WCF", "Core", "Contract", "Model" };
libraryProject.Files = new List<ProjectFile>();
libraryProject.Files.Add(new ProjectFile("Controller.cs"));
libraryProject.Files.Add(new ProjectFile("SystemCore.cs", "Core") { AddNamespaces = new List<string> { $"{solutionName}.Common.WCF", $"{solutionName}.Common.Factory" } });
libraryProject.Files.Add(new ProjectFile("CommunicationService.cs", "WCF") { AddNamespaces = new List<string> { $"{solutionName}.Common.Model", $"{solutionName}.Common.WCF" } });
libraryProject.ChangeFile = new List<ChangeFile> { new ChangeFileAdd("AssemblyInfo.cs", "Properties", "[assembly: log4net.Config.XmlConfigurator(ConfigFile = \"log4net.config\", Watch = true)]") };

//Configure "Service" project
var serviceProject = new ProjectInfo($"{solutionName}.Service", ProjectTemplate.WindowsClassicDesktopConsoleApp);
serviceProject.RemoveFiles = new List<ProjectFile> { new ProjectFile("Program.cs") };
serviceProject.DotNetFrameworkVersion = new Version(4, 6, 1);
serviceProject.NugetPackages = new List<string> { "Topshelf" };
serviceProject.ProjectReferences = new List<ProjectInfo> { libraryProject };
serviceProject.Files = new List<ProjectFile>();
serviceProject.Files.Add(new ProjectFile("log4net.config") { CopyToOutputDirectory = true });
serviceProject.Files.Add(new ProjectFile("Program.cs") { AddNamespaces = new List<string> { $"{solutionName}.Library" } });

//Configure "TestUI" project
var testUiProject = new ProjectInfo($"{solutionName}.TestUI", ProjectTemplate.WindowsClassicDesktopWindowsFormsApp);
testUiProject.DotNetFrameworkVersion = new Version(4, 6, 1);
testUiProject.ProjectReferences = new List<ProjectInfo> { commonProject };
testUiProject.NugetPackages = new List<string> { "System.ServiceModel.NetTcp", "System.Runtime.Serialization.Xml" };
testUiProject.Files = new List<ProjectFile>();
testUiProject.Files.Add(new ProjectFile("Example.cs") { AddNamespaces = new List<string> { $"{solutionName}.Common.WCF", $"{solutionName}.Common.Factory" } });

//Add projects to solution
solutionInfo.ProjectInfos.Add(commonProject);
solutionInfo.ProjectInfos.Add(libraryProject);
solutionInfo.ProjectInfos.Add(serviceProject);
solutionInfo.ProjectInfos.Add(testUiProject);

//Start building machine
var buildingMachine = new SolutionBuildingMachine();
buildingMachine.Build(solutionInfo);
buildingMachine.Dispose();
```

