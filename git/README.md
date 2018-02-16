# Git richtlijnen 

Dit is een richtlijn omtrent het gebruik van Git. Deze richtlijnen zijn geinspireerd op 
[*How to Get Your Change Into the Linux Kernel*](https://kernel.org/doc/html/latest/process/submitting-patches.html),
de [git man pages](http://git-scm.com/doc) en andere methode die vaak gebruikt worden door de community. 

## Inhoudstafel
 
1. [Branches](#branches)
2. [Commits](#commits)
  1. [Messages](#messages)
3. [Merging](#merging)
4. [Misc.](#misc)

### Branches 

* Opteer voor korte maar beshcrijfbare naam: 

```
# Goed
$ git checkout -b oauth-migration

# Slecht - te vaag 
$ git checkout -b login-fix
```

* Identificatie methodieken vanuit de gerelateerde tickes in een externe service (zoals een GitHub issue) is ook een goede kandidaat voor het gebruik in de benaming van branches. Bijvoordeeld: 

```
# GitHub issue #15 
$ git checkout -b issue-15
$ git checkout -b patch-15
```

* Gebruik een liggend streepje voor de onderscheiding van woorden. 

* Wanneer meerdere personen werken aan een dezelfde issue of feature, kan het handig zijn om persoonlijke branches te hebben naast de branch die bedoeld is voor teambrede functie. Gebruik de volgende conventie voor de benaming

```
$ git checkout -b feature-a/master # teambrede branch
$ git checkout -b feature-a/maria  # Maria haar persoonlijke branch
$ git checkout -b feature-a/nick   # Nick zijn persoonlijke branch
```

Merge all personlijke branch naar de teambrede branch (See ["Merging"](#merging)). Om vervolgends de teambrede branch te mergen naar de "master" branch. 


* Verwijder jouw branch van de upstream repository wanneer de Pull Request met wijzigingen is ingevoegd, tenzij er een specifieke reden omtrent het behoud van de branch. 

***Tip:***: Gebruik het volgende commando wanneer je in de "Master branch" zit, voor het weergeven van de branches die zijn ingevoegd: 

```
$ git branch --merged | grep -v "\*"
```

### Commits 

* Elke commit mag in de beste gevallen maar 1 *logische wijziging* bevaaten. Het is afgereaden om meerdere *logische wijzingen* te hebben in een commit. Bijvoorbeeld, als een patch een probleem verhelpt en optimalisering aanbrengt in eeen feature, zou dit opgesplitst moeten worden worden in 2 commits. 

***Tip:*** Gebruik `gut add -p` om specifieke gedeelten van de gewijzigde bestanden interactief te bewerken. 

* Splits een logische wijziging niet op in verschillende commits. De implementatie van een feature en de bijhorende tests moeten bijvoorbeeld in dezelfde commit zitten. 

* Begin en op tijd aan zelfstandige, duidelijke commits deze zijn makkelijke te begrijpen om terug te keren wanneer er iets misgaat.

* Commits moeten strategisch geordend worden.. Bijvoorbeeld, als commit X afhangt van wijzigingen die in commit Y worden doorgevoerd, moet commit Y voor commit X komen.

**Info:** Als je alleen aan een lokale branch werkt die nog gepush moet worden naar de remote, 
is het prima om commits te gebruiken als tijdelijke snapshots van je werk. Vergeet niet op het oment dat je de push uitvoerd
een rebase uit te voeren op de aangebrachte snapshots. 

### Messages 

* Gebruik een editor en niet de CLI, wanneer je commit bericht schrijft: 

```
# goed
$ git commit 

# slecht 
$ git commit -m "Quick fix"
```

Een commit vanuit de CLI moedigt de denkwijze aan om alles te laten passen in 1 lijn, wat meestal resulteert in
niet-informatieve en of dubbelzinnige commit berichten. 

* De samenvattingsregel (dwz de eerste regel van het bericht) moet *beschrijvend en toch beknopt* zijn. Idealiter mag het niet langer zijn dan *50 tekens*. De lijn moet beginnen met een hofdletter en gschreven worden in imperatieve tegenwoordige tijd. Het zou niet moeten eindige met een leesteken, omdat het in feite de titel van de commit is. 

```
# goed -  Imperatief tegenwoordige tijd, hoofdletter, minder dan 50 tekens
Mark huge records as obsolute when clearing hnting faultd 

# slecht 
fixed ActivModel::Errors deprecation messages failing when AR was used outside of Rails. 
```

* Daarna zou een regel moeten volgen, die op beurd zijn gevolgd word door een grondige beschrijving. Dit gelimiteerd word tot regels van max *72 tekens*. Deze beschrijving zou een uitleg moeten zijn waarom de wijziging in kwestie moet gebeuren. Hoe de wijziging word doorgevoerd en of welk probleem het aanpakt. Samen met een analyse omtrent de *neveneffecten* het kan veroorzaken.

Het moet ook all verwijzigingen naar gerelateerde bronnnen bevatten (bijv. Link naar het overeenkomstige probleem i een bugtracker):

```
Short (50 chars or fewer) summary of changes

More detailed explanatory text, if necessary. Wrap it to
72 characters. In some contexts, the first
line is treated as the subject of an email and the rest of
the text as the body.  The blank line separating the
summary from the body is critical (unless you omit the body
entirely); tools like rebase can get confused if you run
the two together.
    
Further paragraphs come after blank lines.

- Bullet points are okay, too

- Use a hyphen or an asterisk for the bullet,
  followed by a single space, with blank lines in
  between

The pointers to your related resources can serve as a footer 
for your commit message. Here is an example that is referencing 
issues in a bug tracker:

Resolves: #56, #78
See also: #12, #34

Source http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html
```

Uiteindelijk moet je bij het schrijven van een commit bericht nadenken over wat je zou moeten weten als 
je over een jaar de commit overloopt.

* Als *commit A* afhankelijk is van *commit B*, moet de afhankelijkheid worden vermeld in het bericht van *commit A*. Gebruik de SHA1 hash bij het verwijzen naar commits.

Alsook *commit A* een bug oplost die door *commit B* is veroorzaakt, moet dit ook worden vermeld in het bericht van *commit A*. 

Als een commit naar een andere commit word geduwd, gebruik dan respectievelijk de `--squash` en `--fixup` parameters om de intentie duidelijk te maken. 

```
$ git coommit --squash f387cab2
```

*(**Tip:** Gebruik de `--autosquash parameter wanneer je een rebase uitvoerd. De gemarkeerde commits zullen automatisch worden samengevoegd.`)*

### Merging 

* **Herschrijf de ge-publiceerde geschiedenis niet.** De geschiedenis van de repository is op zichzelf waardevol en het is erg belangrijk oom te kunnen vertellen wat er werkelijk is gebeurd. Het wijzigen van de gepubliceerde geschiedenis is een algemene bron van problemen die aan het project werkt. 

* Er zijn echter gevallen waarin het herschrijven van de geschiedenis legitiem is. Dit kan wanner:

  * Als u de enge bent die aan het project werkt en het project zelf nog is beoordeeld. 

  * Als u bestaande branches wilt opschonen (bijv. commits samenvoegen) en/of rabasen op een "master" branch om het later samen te voegen. 

  Dat gezegd hebbende, *herschrijf nooit de geschiedenis van de "master" branch * of een andere
  speciale branches (dwz gebruikt door productie- of CI-servers).

* Houd de geschiedenis *schoon* en *eenvoudig*. *Vlak voordat je* je branch fuseert:

    1. Make sure it conforms to the style guide and perform any needed actions
       if it doesn't (squash/reorder commits, reword messages etc.)

    2. Rebase op de branch waar je het later zal samenvoegen:

      ```shell
      [my-branch] $ git fetch
      [my-branch] $ git rebase origin/master
      # Nu kunt u samenvoegen
      ```

      Dit resulteert in een vartakking die direct kan worden toegepast op het einde van de 
      "master" branch en resulteert in een zeer eenvoudige geschiedenis.

      
    * (Opmerking: deze strategie is beter geschikt voor projecten met kortlopende branches. Anders is het misschien beter om af en toe de "master"branch samen te voegen in plaats van erop te rebasen.)

* Als uw branch meer dan één commit bevat, mag u deze niet samenvoegen met
  fast-forward:

  ```shell
  # goed - zorgt ervoor dat een merge commit wordt gemaakt
  $ git merge --no-ff my-branch

  # slecht
  $ git merge my-branch
  ```

## Misc.

* Er zijn verschillende workflows en elke heeft zijn sterke en zwakke punten.
  Of een workflow bij u past, hangt af van het team, het project en uw
  ontwikkelingsprocedures.

  Dat gezegd hebbende, is het belangrijk om *een workflow te kiezen* en erbij te blijven.

* *Wees consistent.* Dit hangt samen met de workflow maar breidt zich ook uit naar dingen
  zoals commit berichten, branch namen en tags. Een consistente stijl hebben
  in de hele repository maakt het gemakkelijk om te begrijpen wat er aan de hand is
  kijkend naar het logboek, een commit bericht etc.

* *Test voordat u pusht.* Duw niet half afgemaakt werk.

* Gebruik [annotated tags](http://git-scm.com/book/en/v2/Git-Basics-Tagging#Annotated-Tags)
  voor het markeren van releases of andere belangrijke punten in de geschiedenis. Verkies
  [lightweight tags](http://git-scm.com/book/en/v2/Git-Basics-Tagging#Lightweight-Tags)
  voor persoonlijk gebruik, zoals bookmark commits voor toekomstige referentie(s).

* Houd uw repositories in goede staat door af en toe onderhoudstaken uit te voeren:

  * [`git-gc(1)`](http://git-scm.com/docs/git-gc)
  * [`git-prune(1)`](http://git-scm.com/docs/git-prune)
  * [`git-fsck(1)`](http://git-scm.com/docs/git-fsck)
  
