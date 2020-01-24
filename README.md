-----------------

:construction: :construction: :construction: **DOCUMENT IS STILL ON CONSTRUCTION !!!** :construction: :construction: :construction: 

-----------------
# Instructions for creating Community tasks

## Introduction


## <TL;DR>
- One solution per one repo. One project for a task and one for tests. If needed, the project can have multiple tasks. Repos must be here: https://github.com/CommunityHiQ
	
- Do not name class, task, and namespace the same!
	
- Remember to do XML documentation and READ.md documentation.
	
- The output of the task is one class, the input is one or two class(es) (eg. input and options). Remember to add "cancellationToken".
	
- Good examples: https://github.com/CommunityHiQ/Frends.Community.Web/tree/FCOM-106_DownloadFileTask and https://github.com/CommunityHiQ/FRENDS.Community.Files/tree/Community.Files
	
- If it is more valuable that the output class is JToken, just create it so. The same with the methods .ToXML() or .ToJSON().
	
- Frends.Tasks.Attributes are deprecating.
	
- In the future in VSTS only local unit tests are driven so do not do tests that requires external resources. While coding it is good to have comprehensive tests, but in VSTS you can take those off with NuNit's Ignore("Reason") command. Do not put passwords to GitHub. If your code can not be tested locally in any way, the test can be disabled in VSTS and then you don't have to ignore all test separately in the code.

## Version control, naming, and structures

Repos can be created here https://github.com/CommunityHiQ. If you cannot create a repo, ask help from one of the reviewers or support@frends.com.

Input and output classes of the task are located to their own files.

## Pattern for naming:

Task name = What the task does? (eg. DownloadFile OR CreateArchive) 
Class = Something convenient (this doesn't show in FRENDS). If you cannot come up with another name(?) write "task name" + "tasks". (eg. WebFilesTask OR ZipTask)
Namespace = unique and descriptive. (Frends.Community.Task is okay, if you're not in a creative mood) (eg. Frends.Community.DownloadWebFiles OR Frends.Community.Zip)
Repo's name = <Namespace>.<taskname> (eg. Frends.Community.Zip.CreateArchive OR Frends.Community.DownloadWebFiles.DownloadFile)

More info: https://blogs.msdn.microsoft.com/ericlippert/2010/03/09/do-not-name-a-class-the-same-as-its-namespace-part-one/ (Google for parts 2-4).

## Parameters

Return always strongly typed class that includes own field for every value.

Incoming parameters are always divided into the next groups, but all groups are not always needed. Groups must be in this order "input", "output":
- Input: all data that comes in and the parameters that are closely related to data, such as "connection strings"
- Options: Configurations such as timeout value.

All parameters need metadata:

- [DisplayName] – Name that is shown in the UI

- [DefaultValue] – Default value to a variable, this can be also "null" or "String.Empty"

- XML documentation notations:

    - summary – a description about parameters or task in a general level
	
    - returns – a description of an object returned by the task. It is good to list that this can be used in the next tasks x.x.

## Unit tests

- Done to the project FRENDS.Tests
- For each task a minimum requirement is one happy testcase
- Unit tests are done with Nunit framework so they work on the buildserver
- If unit tests are using files, do a separate folder for them
- Remember to add the possible files to VS project so those will be copied while the project is compiled

## Documentation

Documentation to README.md file, GitHub shows it nicely
Structure of the documentation:

- General description of what the task does

- Input - description
	
- Options – description
	
- \#result – description
	
- An example scenario of the use within the solution, an example of the configuration

- If used with a specific system, also an instruction on how to use with that system
	
Do not use XML documentation classes unless it has a real extra value. XML documentation is for properties and methods. A user or developer cannot utilize "Input class" typed summary (if you don't like the error message "missing XML documentation" just add '#pragma warning disable 1591' under using sentences).

## Review

The review is mandatory.Ask from the reviewer separately when you've done pull request since the reviewer cannot know about the pull request (unless Review field is spesified). Take Skype, Flowdock to make sure you both know about the changes.

## Review corresponds

[hiqesalep](https://github.com/hiqesalep)
[KRuusuv](https://github.com/KRuusuv)
[VilleVuorela](https://github.com/VilleVuorela)
[juralaakkonen](https://github.com/juralaakkonen)
[RedBraces](https://github.com/RedBraces)
[OssiGalkin](https://github.com/OssiGalkin)

## Nuget-feed and auto build

[OssiGalkin](https://github.com/OssiGalkin)
support@frends.com

## Review check list
Goals

    Mitigate errors in the code
    Basic functionality is achieved
    Knowledge transfer
    Check solution from another perspective
    Repetition → Quality at once

Code check list

    Get the code from version control, pull request
    Compile without errors and warnings
    Version number correct
    Unit tests have been done
    Task has been documented
    Parameters are documented
    Structure of the code and modular seams correct
    The task looks fine in FRENDS
    Class/task naming is correlating what the code does eg. FRENDS.Common.Files.Operations.MoveFile()
    Names of the parameters are representative
    The names of the variables are descriptive: tmp <-> googleService
    Comments in code are fine
    Scopes of the variables and resources are as minimal as possible. Can you find any unneeded global variables?
    NULL pointers and getting the resource are always checked
    Error messages are unique
    Normally the task is not handling exceptions (do not use try-catch structure), these are delivered to FRENDS so the errors can be shown in FRENDS UI
    Inner errors can generally return an exception
    Do not invent something that has been done earlier
    Time out and cancellationToken are used: task can be canceled
    Remove all credentials, passwords, certificates, etc. from code
    The most general injections have been considered:
        SQL
        CGI parameters
        LDAP

 

    External resources (SQL, AWS, Google, REST, files etc.)
        Scope of the resources is as minimal as possible
        Do not reserve a resource if not needed: loops, in the beginning, or end of the code
        Free a resource after use (using / resurssi.Close() / resurssi.Displose()) / using {}
        Error situations of the external resources are handled in code and closed possible resources: exceptions, status codes
            Not like this: http task that did not return an exception if the server returned error code, not 200/202. The requester did not receive this code either just a blank document.
            Way to go: http task returns an exception if returned something else than 200/202/xxx

FRENDS check list

    Task versioned correctly
    Parameters are named and documented
    Secured parameters are marked [PasswordPropertyText(true)]
    Default parameters make sense; eg. do not use your email
    One task does one coherent entity (method) Not like CopyFilesFromSFTPAndTransferToGDrive()
    Task returns data that can be used in the next task
        Not like a string that includes status code and a message
        Yes: Data structure that has a status code and a message separated

 
Billing

Creating common tasks are billable work. Do not write this to internal work, but discuss with who gave this task to you.

# Converting FRENDS Community Tasks to .NET Standard or multi-target (.NET Standard 2.0 and Framework)

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
3. Update the project .csproj file to the new format. The easiest way to accomplish this is to create a new project in Visual Studio targeting .NET Standard and re-add files from the old project. You can use [task template](TODO) to easily create a new project. Keep the project/assembly name the same as before so the Task signature stays the same and is handled as an update instead of a new Task
4. Setup multi-target by changing the project's .csprojs, if you want to use multi-targeting.
    <pre>&lt;TargetFramework&gt;netstandard2.0&lt;/TargetFramework&gt;</pre>
    to
    <pre>&lt;TargetFrameworks&gt;netstandard2.0;net461&lt;/TargetFrameworks&gt;</pre>
5. Re-add NuGet package references. Now they use new PackageReferences NuGet package information to the .csproj and delete the .nuspec file.
7. If you use muli-targeting use preprocessor directives to add target specific functionality, e.g.
    <pre>&#35;if NET461
    FrameworkOrWindowsSpecificClass.DoSomething();
    &#35;else
    throw new Exception("Only supported on .NET Framework");
    &#35;endif</pre>
8. If necessary you may have target specific references using conditional ItemGroups, e.g.
    <pre>&lt;ItemGroup Condition="'$(TargetFramework)'=='net471'"&gt;
        &lt;Reference Include="System.Net.Http" /&gt;
    &lt;/ItemGroup&gt;</pre>
9. If using target specific functionality, you should also convert test projects to multi target. Note that unit test can not be run if they target .NET Standard, so they need to targe .NET Core or Framework.

Example conversions, using multitargeting:

* [Frends.Web](https://github.com/FrendsPlatform/Frends.Web/pull/25/files)
* [Frends.File](https://github.com/FrendsPlatform/Frends.File/pull/10/files)
* [Frends.Json](https://github.com/FrendsPlatform/Frends.Json/pull/11/files)



-----------------

Still only in Finnish
Ohjeet eri tilanteisiin

    Triviaali bugi (Virheen korjaus yms.) korjaukset tehdään olemassa olevaan versioon taskista.
        Ota oma branch commoneista
        Korjaa bugi
        Perustestien tekeminen Taskeille (yksikkötestit!).
        Kirjoita lyhyt kuvaus, eli mitä muutit committiin ja mahdollisesti dokumentaatioon. Esim: FRENDS.File.Write: New Encoding options added to task.
        Viimeistään tässä vaiheessa käännä, aja testit ja nugetoi taski, ja lisää taski frendsiex:iin tai frendsvnext:iin.
        Rewiev: tee pull request bitbucketissa, töki tiimisi rewiev vastaavaa katselmoimaan tekeleesi ja kerro hänelle mille taskille tunnit voi kirjata.
        Mikäli on korjattavaa, niin korjaa ne.
        Tarkista että taski täyttää katselmoinnissa katsottavat asiat.
        Kirjoita lyhyt sähköposti Risto-Matille jossa: Branch nimi mistä olen ottanut Kehitys-branchin. Branch-nimi johon teit muutoksen. Mihin asiakkuuteen teit tämän korjauksen/muutoksen. Pyydät päivittämään feedit.
    Breaking Change, eli taskin rikkova muutos (käytännössä parametrit, yms muuttuu):
        Bugi korjauksen yhteydessä erota task, esim. FRENDS.File.Write omaan Visual Studio Projektiin ja Nugettipakettiin (mutta käytä samaa ratkaisua (solution, sln-tiedosto).
        Nimeä se fiksusti, esim FRENDS.File.Write.V2. Älä välitä vaikka vanhassa projektissa on muitakin taskeja, niitä ei vielä ole ehditty forkkaamaan.
        Muista kopioida myös yksikkötestit ja laittaa ne testaamaan uutta taskia.
        Uuden komponenttiversion Release notes luominen & tiedottaminen. Lyhyt lauseen pari kuvaus, sekä selitys parametreistä ja outputista dokumentaatioon
        Tarkista että taski täyttää katselmoinnissa katsottavat asiat.
        Noudata muuten ylempänä annettuja peer review, pull request, ilmoitus ja yksikkötestaus ohjeita.
    Uusi taski

        Uudet Taskit lisätään samalla kaavalla Common version hallintaan, kuin nyt sovittu taskien purkaminen omiin Visual Studio projekteihin, noudattaen yllä kuvattuja periaatteita. Muista ottaa uusi branchi ja tehdä yksikkötestit ja dokumentaatio.

    apuprojektin tai apufunktionpäivitys

        Päivitä apu projekti Breaking Change ohjeiden mukaan ja sitä käyttävä taskit bugi korjaus ohjeiden mukaan.

    Tausta kirjaston päivitys

        Mikäli olet 100 % varma, että tausta kirjastoa ei käytetä missään muussa taskissa, korjaa taski bugi korjaus ohjeiden mukaan. Muussa tapauksessa kysy apua review vastaavilta ja tai r&d:ltä. Ongelmia syntyy jos samassa prosessissa olevat taskit yrittävät käyttää eri versioita samasta dll:stä.
    Asiakkaalta löytyy hyvä taski, eli mitä jos asiakkaalla on super hyvä taski, mutta sitä ei ole tarpeen muokata, mutta siitä voisi olla hyötyä commoneissa. Eli siirtoa ei voida laskuttaa, taski pitäisi saada commoneihin.
        Tee uusi branch commoneista kopioi koodi asiakkaan reposta, lisää koodi commoneihin ja kirjoita pikana committiin mistä on kyse. Älä säädä mitään turhaa visual studion projektien tai ratakisujen (solution) kanssa . Tähän pitäisi mennä aikaa 5 min, joten sen voi leipoa vaikka kuuluisten kiky tuntien sisään. Jos taskista on oikeasti hyötyä jollekin muulle, se löytyy commonien branchistä ja sitten kun joku toinen asiakas haluaa taskin itselleen kaikki tarvittavat muutokset taskiin ja dokumentointi yms. voidaan laskuttaa.


Versionumerointi

Versiointi jakautuu 3 numeroon
    Taskit numeroidaan kolmella numerolla ja ensimmäinen masteriin viety versio on lähtökohtaisesti 1.0.0 ja devaamien aloitetaan numerosta 0.1.0
    Devaaja päivittää viimeistä numera miten haluaa
    Kun pull request hyväksytään ja mergetään masteriin hyväksyjä nostaa keskimmäistä numeroa yhdellä ja nollaa viimeisen.
    Paitsi jos kyseessä on breaking change, eli taskia joudutaan (mahdollisesti) päivittämään käsin FRENDSissä (esim. taskin return tai parametrit muuttuvat, tai taski muutetaan .Net standardiksi) pull requestin hyväksyjä inkrementoi ensimmäistä versionumeroa yhdellä ja nollaa muut.
    
Sellaisia päivityksiä jotka hävittävät paramaterit FRENDSin editorissa ei pitäisi tehdä, joten sille ei ole numerointia.

FAQ:
Miksi pull requestin hyväksyjä nostaa keskimmäistä numeroa? V: Koska tällöin PR:ää voi päivittää vielä jälkikäteen.
Mitä jos devaaja päivittääkin keskimmäitä numeroa? V: Ei se niin vakavaa ole.
Miksi community taskeissa ei käytetä puhdasta Semantic Versioning https://semver.org/ käytäntöä V: Käytännössä community taskeja devataan siten että niistä tehdään useita nugettipaketteja FRENDSiin jolloin viimeistä numeroa joutuu päivittämään mahdollisesti useita kertoja. Tämän vuoksi on kätevämpää päivittää keskimmäistä numeroa myös bugikorjauksien yhteydessä, sillä tällöin kukaan ei ihmettele miksi versiosta 1.3.4 siirrytään versioon 1.3.17, lisäksi päivittämällä keskimmäistä numeroa FRENDS jossa kehitystyö on tehty hyväksyy uudne paketin feedistä.


