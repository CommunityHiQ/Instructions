-----------------

:construction: :construction: :construction: **DOCUMENT IS STILL ON CONSTRUCTION !!!** :construction: :construction: :construction: 

-----------------
# Instructions for creating Community Tasks

## Introduction
- [Instructions for creating Community Tasks](#instructions-for-creating-community-tasks)
  * [Introduction](#introduction)
  * [<TL;DR>](#tldr)
  * [Version control, naming, and structures](#version-control-naming-and-structures)
  * [Pattern for naming:](#pattern-for-naming)
  * [Parameters](#parameters)
  * [Unit tests](#unit-tests)
  * [Documentation](#documentation)
  * [Review](#review)
  * [Review corresponds](#review-corresponds)
  * [NuGet-feed and auto build](#nuget-feed-and-auto-build)
  * [Review check list](#review-check-list)
  * [Billing](#billing)
- [Converting FRENDS Community Tasks to .NET Standard or multi-target (.NET Standard 2.0 and .NET Framework)](#converting-frends-community-tasks-to-net-standard-or-multi-target-net-standard-20-and-framework)
  * [Why?](#why)
  * [Why multi-target? Why not just use .NET Standard?](#why-multi-target-why-not-just-use-net-standard)
  * [Why not multi-target? Why just .NET Standard?](#why-not-multi-target-why-just-net-standard)
  * [What needs to be done?](#what-needs-to-be-done)


## <TL;DR>
- One solution per one repo. One project for a Task and one for tests. If needed, the project can have multiple Tasks. Repos must be here: https://github.com/CommunityHiQ
	
- Do not name class, Task, and namespace the same!
	
- Remember to do XML documentation and README.md documentation.
	
- The output of the Task is one class, the input is one or two class(es) (eg. input and options). Remember to add and use "CancellationToken cancellationToken" so the Task can be cancelled.
	
- Good examples: https://github.com/CommunityHiQ/Frends.Community.Web/tree/FCOM-106_DownloadFileTask and https://github.com/CommunityHiQ/FRENDS.Community.Files/tree/Community.Files
	
- If it is more valuable that the output class is JToken, just create it so. The same with the methods .ToXML() or .ToJSON().
	
- Frends.Tasks.Attributes are deprecated, don't use them.
	
- In the future in VSTS only local unit tests are driven so do not add tests that require external resources, or at least have them ignored by default. While coding it is good to have comprehensive tests, but in VSTS you can take those off with NuNit's Ignore("Reason") command. **Do not put passwords to GitHub.** If your code can not be tested locally in any way, the test can be disabled in VSTS and then you don't have to ignore all test separately in the code.

## Version control, naming, and structures

Repositories can be created here https://github.com/CommunityHiQ. If you cannot create a repo, ask help from one of the reviewers or support@frends.com.

Input and output classes of the Task should be located to their own files.

## Pattern for naming:

Task name = What does the Task do? (eg. DownloadFile OR CreateZipArchive) 

Namespace = Unique and descriptive. Where task relates? Use Frends.Community as prefix (eg. Frends.Community.DownloadWebFiles OR Frends.Community.Zip)

Class = Something convenient (this doesn't show in FRENDS, except in the Task listing). If you cannot come up with another name write "Task name" or last part of namespace + "Tasks" or "operations. (eg. DownloadFilesTasks or ZipTasks)

Repository's name = Same as namespace (otherwise there will be problems in CI).

Do not name the class, Task, and namespace the same! And use plurals in class and namespace names whenever possible. More info: https://blogs.msdn.microsoft.com/ericlippert/2010/03/09/do-not-name-a-class-the-same-as-its-namespace-part-one/ (Google for parts 2-4).

## Parameters

The return value should always be strongly typed class that includes own property/field for every value.

Incoming parameters should always be divided into the next groups, but all groups are not always needed. Groups must be in this order "input", "output":

- Input: all data that comes in and the parameters that are closely related to data, such as "connection strings" (i.e. the stuff the Task absolutely needs to execute)

- Options: Configurations such as timeout value.

All parameters need metadata:

- [DisplayName] – Name that is shown in the UI, otherwise the name of the Task method is used

- [DefaultValue] – Default value to a variable, this can be also "null" or "String.Empty"

- XML documentation notations:

    - summary – A description about parameters or Task in a general level
	
    - returns – A description of an object returned by the Task. It is good to list the properties/fields of the return object so that it can be used in the next Tasks

## Unit tests

- Done to the project <Namespace>.Tests. For example Frends.Community.Zip has it test under Frends.Community.Zip.Tests

- Each Task has must at least one happy testcase

- Unit tests are done with NUnit framework so they work on the build server

- If unit tests are using files, add a separate folder for them

- Remember to add the possible files to the VS test project so those will be copied while the test project is compiled

You can read environment variables in C# with:

`Environment.GetEnvironmentVariable("EXAMPLE_ENVIROMENT_VARIABLE")`

You can set environment variables locally in PowerShell by

`[System.Environment]::SetEnvironmentVariable('EXAMPLE_ENVIROMENT_VARIABLE','foobar')`

In the GitHub Action environment variables are added via Secrets (under Settings).

**It is important to use only capital letters in  environment variable/Secret names to make them work everywhere.**

## Documentation

Write the documentation in a README.md file located at the root of the repository so GitHub shows it nicely
Structure of the documentation:

- General description of what the Task does

- Input - description
	
- Options – description
	
- \#result – description
	
- An example scenario of the use within the solution, an example of the configuration

- If used with a specific system, also an instruction on how to use with that system
	
Do not use XML documentation classes unless it has a real extra value. XML documentation is for properties and methods. A user or developer cannot utilize "Input class" typed summary (if you don't like the error message "missing XML documentation" just add '#pragma warning disable 1591' under using sentences).

## Review

The review is mandatory. Ask from the reviewer separately when you've created a pull request since the reviewer cannot know about the pull request (unless Reviewer field is specified). Use Skype or Flowdock to make sure you both know about the changes.

## Review corresponds

[hiqesalep](https://github.com/hiqesalep)

[KRuusuv](https://github.com/KRuusuv)

[VilleVuorela](https://github.com/VilleVuorela)

[juralaakkonen](https://github.com/juralaakkonen)

[OssiGalkin](https://github.com/OssiGalkin)

## NuGet-feed and auto build

Task are added to the frends-community NuGet feed on myget.org. The best way to browse it is via the gallery: https://www.myget.org/gallery/frends-community

GitHub Actions are used to build the Task on every push and then to make a release build when code is merged to master. When creating a new Task it is better to use the Task template and then manually edit json files under .github/workflows to edit build or add passwords etc. for unit tests. See the section on Unit tests to see how passwords etc. should be saved.

## Review check list
Goals

    Mitigate errors in the code
    Basic functionality is achieved
    Knowledge transfer
    Check solution from another perspective
    Repetition → Quality at once

Code check list

    Get the code from version control, pull request
    Solution compiles without errors and warnings
    Version number correct
    Unit tests have been done
    Task has been documented
    Parameters are documented
    Structure of the code and modular seams correct
    The Task is presented correctly in FRENDS
    Class/Task naming is correlating what the code does eg. FRENDS.Common.Files.Operations.MoveFile()
    Names of the parameters are representative
    The names of the variables are descriptive: tmp <-> googleService
    The code is commented and understandable
    Scopes of the variables and resources are as minimal as possible. Can you find any unnecessary global variables?
    NULL pointers and getting the resource are always checked
    Error messages are unique and descriptive: "An error occurred" vs $"Error connecting to server {server}, error: {exception.Message}"
    Task failures are communicated as exceptions in frends, so it is fine to throw them out from the Task. It is usually good the catch and enrich the Exception with what was happening in the Task at the point of the error and include the original exception as an inner exception: try { Connect(server)} catch (Exception ex) { throw new Exception($"Error while connecting to server {server}, error {ex.Message}", ex); }
    Do not reinvent something that has been done earlier
    Timeouts and cancellationToken should be used, especially in loops. This ensures that the Process and Task can be terminated
    Remove all credentials, passwords, certificates, etc. from the code
    The most general injections have been considered:
        SQL
        CGI parameters
        LDAP

 

    External resources (SQL, AWS, Google, REST, files etc.)
        Scope of the resources is as minimal as possible
        Do not reserve a resource if not needed: loops, in the beginning, or end of the code
        Free a resource after use (using / resource.Close() / resource.Dispose()) / using (var resource = new Resource()) {...}
        Error situations of the external resources are handled in code and closed possible resources: exceptions, status codes
            Not like this: HTTP Task that does not return an exception if the server returns an error code, not 200/202. The requester did not receive this code either just a blank document.
            Way to go: HTTP Task throws an exception by default if returned something else than 200/202/xxx, this could be modified with an option parameter for the Task

FRENDS check list

    Task versioned correctly
    Parameters are named and documented
    Secured parameters are marked [PasswordPropertyText(true)]
    Default parameters make sense; e.g. do not use your email
    One Task does one coherent action (method) Not like CopyFilesFromSFTPAndTransferToGDrive()
    Task returns data that can be used in the next Task
        Not like a string that includes status code and a message
        Yes: Data structure that has a status code and a message separated

 
# Billing

Creating common Tasks are billable work. Do not write this to directly to internal work, but discuss with who gave this Task to you.

# Converting FRENDS Community Tasks to .NET Standard or multi-target (.NET Standard 2.0 and .NET Framework)

## Why?

The new FRENDS Agent which is developed with .NET Core and can run on Linux machines that cannot execute Tasks developed for .NET Framework.

## Why multi-target? Why not just use .NET Standard?

To ensure compatibility with older FRENDS installations, without .NET Standard support, use .NET 4.6.1 for maximum compatibility.

## why not multi-target? Why just .NET Standard?

A bit less hassle with differences in using libraries.

## What needs to be done?

To avoid multiple hiccups, you should also convert your project to use new standards and tools. I.e. you should use new [(SDK-style)](https://docs.microsoft.com/en-us/nuget/resources/check-project-format) project format, [PackageReferences](https://docs.microsoft.com/en-us/nuget/consume-packages/package-references-in-project-files)
6. [Update](https://docs.microsoft.com/en-us/nuget/reference/msbuild-targets#pack-target) instead of .nuspec file, and use dotnet tools instead of nuget.exe and old MSBuild.

1. Check if the used libraries support .NET Standard.
2. Check if the Task uses Windows-specific functionality, such as impersonation.
3. Update the project .csproj file to the new format. The easiest way to accomplish this is to create a new project in Visual Studio targeting .NET Standard and re-add files from the old project. You can use the [Task template](TODO) to easily create a new project. Keep the project/assembly name the same as before so the Task signature stays the same and is handled as an update instead of a new Task
4. Setup multi-target by changing the project's .csproj-file, if you want to use multi-targeting.
    <pre>&lt;TargetFramework&gt;netstandard2.0&lt;/TargetFramework&gt;</pre>
    to
    <pre>&lt;TargetFrameworks&gt;netstandard2.0;net461&lt;/TargetFrameworks&gt;</pre>
5. Re-add NuGet package references. Now they use new PackageReferences NuGet package information to the .csproj and delete the .nuspec file.
7. If you use multi-targeting use preprocessor directives to add target specific functionality, e.g.
    <pre>&#35;if NET461
    FrameworkOrWindowsSpecificClass.DoSomething();
    &#35;else
    throw new Exception("Only supported on .NET Framework");
    &#35;endif</pre>
8. If necessary you may have target specific references using conditional ItemGroups, e.g.
    <pre>&lt;ItemGroup Condition="'$(TargetFramework)'=='net471'"&gt;
        &lt;Reference Include="System.Net.Http" /&gt;
    &lt;/ItemGroup&gt;</pre>
9. If using target specific functionality, you should also convert test projects to multi-target. Note that unit test can not be run if they target .NET Standard, so they need to target .NET Core or Framework.

Example conversions, using multitargeting:

* [Frends.Web](https://github.com/FrendsPlatform/Frends.Web/pull/25/files)
* [Frends.File](https://github.com/FrendsPlatform/Frends.File/pull/10/files)
* [Frends.Json](https://github.com/FrendsPlatform/Frends.Json/pull/11/files)



-----------------

Still only in Finnish

# Internal procedure in HiQ when our customer reports bug to us and want's it to be fixed.

## Trivial bug

- Make a new branch 
- Fix the bug
- Make tests to ensure that bug is fixed. Run all tests.
- Write description about the change in commit messages and to the ticket. And possible to the release notes and documentation. E.g. `FRENDS.File.Write: New Encoding options added to task.`
- Pack the Task and test it also in FRENDS.
- Make a pull request and agree with a reviewer that he/she will do the review. Don't just leave the PR open in GitHub.
- Fix things that are raised in the review.

## Breaking Change
eli taskin rikkova muutos (käytännössä parametrit, yms muuttuu):
Bugi-korjauksen yhteydessä erota Task, esim. FRENDS.File.Write omaan Visual Studio Projektiin ja Nugettipakettiin (mutta käytä samaa ratkaisua (solution, sln-tiedosto).
Nimeä se fiksusti, esim FRENDS.File.Write.V2. Älä välitä vaikka vanhassa projektissa on muitakin taskeja, niitä ei vielä ole ehditty forkkaamaan.
Muista kopioida myös yksikkötestit ja laittaa ne testaamaan uutta taskia.
Uuden komponenttiversion Release notes luominen & tiedottaminen. Lyhyt lauseen pari kuvaus, sekä selitys parametreistä ja outputista dokumentaatioon
Tarkista että taski täyttää katselmoinnissa katsottavat asiat.
Noudata muuten ylempänä annettuja peer review, pull request, ilmoitus ja yksikkötestaus ohjeita.


## Uusi taski

Uudet Taskit lisätään samalla kaavalla Common version hallintaan, kuin nyt sovittu taskien purkaminen omiin Visual Studio projekteihin, noudattaen yllä kuvattuja periaatteita. Muista ottaa uusi branchi ja tehdä yksikkötestit ja dokumentaatio.

apuprojektin tai apufunktionpäivitys

Päivitä apu projekti Breaking Change ohjeiden mukaan ja sitä käyttävä taskit bugi korjaus ohjeiden mukaan.

Tausta kirjaston päivitys

Mikäli olet 100 % varma, että tausta kirjastoa ei käytetä missään muussa taskissa, korjaa taski bugi korjaus ohjeiden mukaan. Muussa tapauksessa kysy apua review vastaavilta ja tai r&d:ltä. Ongelmia syntyy jos samassa prosessissa olevat taskit yrittävät käyttää eri versioita samasta dll:stä.
	
	
## Asiakkaalta löytyy hyvä taski,
eli mitä jos asiakkaalla on super hyvä taski, mutta sitä ei ole tarpeen muokata, mutta siitä voisi olla hyötyä commoneissa. Eli siirtoa ei voida laskuttaa, taski pitäisi saada commoneihin.
        Tee uusi branch commoneista kopioi koodi asiakkaan reposta, lisää koodi commoneihin ja kirjoita pikana committiin mistä on kyse. Älä säädä mitään turhaa visual studion projektien tai ratakisujen (solution) kanssa . Tähän pitäisi mennä aikaa 5 min, joten sen voi leipoa vaikka kuuluisten kiky tuntien sisään. Jos taskista on oikeasti hyötyä jollekin muulle, se löytyy commonien branchistä ja sitten kun joku toinen asiakas haluaa taskin itselleen kaikki tarvittavat muutokset taskiin ja dokumentointi yms. voidaan laskuttaa.


Versionumerointi

Versiointi jakautuu 3 numeroon
    Taskit numeroidaan kolmella numerolla ja ensimmäinen masteriin viety versio on lähtökohtaisesti 1.0.0 ja devaamien aloitetaan numerosta 0.1.0
    Devaaja päivittää viimeistä numera miten haluaa
    Kun pull request hyväksytään ja mergetään masteriin hyväksyjä nostaa keskimmäistä numeroa yhdellä ja nollaa viimeisen.
    Paitsi jos kyseessä on breaking change, eli taskia joudutaan (mahdollisesti) päivittämään käsin FRENDSissä (esim. taskin return tai parametrit muuttuvat, tai taski muutetaan .Net standardiksi) pull requestin hyväksyjä inkrementoi ensimmäistä versionumeroa yhdellä ja nollaa muut.
    
Sellaisia päivityksiä jotka hävittävät paramaterit FRENDSin editorissa ei pitäisi tehdä, joten sille ei ole numerointia.

## FAQ:
Miksi pull requestin hyväksyjä nostaa keskimmäistä numeroa? V: Koska tällöin PR:ää voi päivittää vielä jälkikäteen.
Mitä jos devaaja päivittääkin keskimmäitä numeroa? V: Ei se niin vakavaa ole.
Miksi community taskeissa ei käytetä puhdasta Semantic Versioning https://semver.org/ käytäntöä V: Käytännössä community taskeja devataan siten että niistä tehdään useita nugettipaketteja FRENDSiin jolloin viimeistä numeroa joutuu päivittämään mahdollisesti useita kertoja. Tämän vuoksi on kätevämpää päivittää keskimmäistä numeroa myös bugikorjauksien yhteydessä, sillä tällöin kukaan ei ihmettele miksi versiosta 1.3.4 siirrytään versioon 1.3.17, lisäksi päivittämällä keskimmäistä numeroa FRENDS jossa kehitystyö on tehty hyväksyy uudne paketin feedistä.


##  Frends.Tasks.Attributes referenssin poisto 

- Poista nuget 'Frends.Tasks.Attributes' projektista ja dependency .nuspec filestä,
- Lisää System.ComponentModel.DataAnnotations referenssi
- Korvaa attribuutit koodista
```
[DefaultDisplayType(DisplayType.Text)]    →      [DisplayFormat(DataFormatString = "Text")]
[ConditionalDisplay(nameof(Method), Method.POST)]    -->  [UIHint(nameof(Method),"", Method.POST)]
[CustomDisplay(DisplayOption.Tab)]    →    [PropertyTab]
```
