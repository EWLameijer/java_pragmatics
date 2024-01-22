## Tips en opmerkingen 


1. Welke files wel en niet in GitHub
2. Formatteren van files (spacing en namen)
	- 2b: zelfs al zien formatters het niet altijd, lege regel na methode is goed, dubbele lege regel of lege regel na '}' is vaak overbodig
	- 2c: laat regels niet van scherm aflopen (lastig voor version control en reviews)
	- 2d: naamgeving: user-defined types (klassen, records, enums) UpperCamelCase, final static fields en enum-waarden MACRO_CASE, rest lowerCamelCase.
3. Welke loop kiezen
4. DRY: niet telkens dezelfde constante definieren
5. Minimaliseer scopes en lifetimes (liever private dan public, liever local dan veld, liever zo laat mogelijk definiëren)
6. @Override is nuttig
7. Vermijd magische constanten: geef waarden waarvan hun betekenis niet gelijk duidelijk is, waarbij intypen lang of foutgevoelig is, en/of die makkelijk aangepast kunnen worden hun eigen naam 
8. Commentaren: commentaren kunnen heel nuttig zijn, maar vooral als ze niet gelijk zeggen wat de code zegt
9. Map, ArrayList en Set zijn erg nuttig, maar maak ze niet 'raw' (geef ze een datatype mee)
10. Let dat je dubbele declaratie van generic types kunt vermijden door de 'diamond operator' toe te passen.
11. Iets geavanceerder: ken de 4 keuze-statements (if/else, ternaire operator, switch statement, switch expression), en weet welke het beste past 
12. Weet wanneer je welk type gebruikt!
13. Probeer methodes relatief klein en simpel te houden; minder dan een scherm, cyclomatische complexiteit liefst onder 5, zeker onder 10!
14. Een paar dingen die het OCA-boek niet noemt zijn records, switch expressions en vars (ook omdat die na Java 8 kwamen). Of Set.of, Map.of, List.of. Maar ze werken wel erg fijn!

-- 

1. Welke files wel en niet in GitHub: 
	WEL: .java files, algemene simpele tekst-files (.txt, .md, .adoc, .xml), files die horen bij Maven of Gradle (inclusief de Maven/Gradle jars)
	NIET: editor-configuratie-files, .class-files, niet-Maven/Gradle-jar-files liefst niet in GitHub. Als die er per ongeluk toch op staan (overkomt mij ook weleens): git rm --cached -r .idea 

2. Indentatie en spaties 'standaard' houden maakt code leesbaarder - in elk geval voor anderen, en je ziet er zelf soms ook makkelijker bugs door. Advies: bij IntelliJ: Settings (Ctrl+Alt+S)>Tools>Actions on Save>Reformat Code, bij Notepad++: gebruik de Google Java Formatter 

- 2b: 
liever 
```
            } else {
                System.out.println("The winner is " + player1Name);
                break;
            }
        }
    }

```

dan 
```
            } else {
                System.out.println("The winner is " + player1Name);
                break;
            }

        }

    }
```

- 2c: laat regels niet van scherm aflopen (lastig voor version control en reviews)

- 2d: naamgeving: user-defined types (klassen, records, enums) UpperCamelCase, final static fields en enum-waarden MACRO_CASE, rest lowerCamelCase.
Dus liever:
```
	private FoodType type;
	private Ingredient mainIngredient;
	private Seasoning seasoning;

```

dan 
```
	private FoodType _type;
	private Ingredient _mainIngredient;
	private Seasoning _seasoning;
```



3. Soms zijn er loops die minstens 1x moeten worden doorlopen. Dat kan je regelen door code te herhalen, zoals bij 
```
        System.out.print("Player 1, how far away from the city do you want to station the Manticore?(0-100) ");
        int distanceOfManticore = input.nextInt();
        while(distanceOfManticore < 0 || distanceOfManticore > 100){
            System.out.println("that is not within range");
            System.out.print("Please enter a new range (0-100): ");
            distanceOfManticore = input.nextInt();
        }
```

maar onderhoudbaarder is een do-while-loop te gebruiken in zulke situaties. In gevallen als deze is het denk ik het netste om een aparte methode te maken 

```
int getManticoreDistance() {
    System.out.print("Player 1, how far away from the city do you want to station the Manticore?(0-100) ");
    do {
    	int distanceOfManticore = input.nextInt();
    	if (distanceOfManticore < 0 || distanceOfManticore > 100) return distanceOfManticore;
    	System.out.println("that is not within range");
        System.out.print("Please enter a new range (0-100): ");
    } while (true)
}
```

De hoofdloop kan overigens ook beter een do-whileloop zijn, omdat de stad minstens 1x moet schieten.

4. DRY (don't repeat yourself) is een nuttig principe om code onderhoudbaarder te maken. Wat als iemand een monitor heeft met hoge resolutie? Dan is `System.out.println("\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n");` mogelijk niet genoeg. Beter iets als `for (int i=0; i<MAX_SCREEN_HEIGHT;i++) System.out.println();`

5. Het kan goed zijn de scopes van waarden zo klein mogelijk te houden. Bij de Defense of Consolas: `public static final String ANSI_RESET = "\u001B[0m";`. Hier gebruikt alleen de klasse zelf ze, dus private static is dan beter. Als andere programma's die waarde ook gebruiken, maak er een aparte klasse voor!


6. Was vrees ik niet uitgelegd, maar best practice in Java is (al is het niet verplicht) @Override te gebruiken als je een methode (als toString) overridet.

Dus 

```
    public String toString(){
        return "Sword";
    }
```

In dit geval zou ik denk ik iets doen als de toString in Item de getClass().getSimpleName() te laten gebruiken, dan hoef je hem alleen in Food Rations te overriden (of je voegt logica toe dat als een na de eerste hoofdletter een hoofdletter is, dat je er een spatie voor toevoegt en de letter klein maakt) 

7. Vermijd magische constanten: geef waarden waarvan hun betekenis niet gelijk duidelijk is, waarbij intypen lang of foutgevoelig is, en/of die makkelijk aangepast kunnen worden hun eigen naam 

`if (!isEnabled || mCamRot.Value == new Vector3(-23232323, 232323, 23232323)) return;`

8. Commentaren: commentaren kunnen heel nuttig zijn, maar vooral als ze niet gelijk zeggen wat de code zegt

```
    // Constructor
    public Game() {
```
Dit is geen vreselijk commentaar, maar verreweg de meeste professionele developers zouden zien dat dit een constructor is.

9. Map, ArrayList en Set zijn erg nuttig, maar maak ze niet 'raw' (geef ze een datatype mee)
Liever 
```
HashMap<Integer, String> initialSquares; 
```
dan
```
private HashMap squares;
```

10. Let dat je dubbele declaratie van generic types kunt vermijden door de 'diamond operator' toe te passen.

Dus liever 
```
HashMap<Integer, String> initialSquares = new HashMap<>();
```
dan 
```
HashMap<Integer, String> initialSquares = new HashMap<Integer, String>();
```

11. Iets geavanceerder: ken de 4 keuze-statements (if/else, ternaire operator, switch statement, switch expression), en weet welke het beste past 

liever
```
        String player2Choice = player1Choice.equalsIgnoreCase("x") ? "O" : "X";
        return player2Choice;

```

dan 
```
        String player2Choice;

        if (player1Choice.equalsIgnoreCase("x")) {
            player2Choice = "O";
        } else {
            player2Choice = "X";
        }

        return player2Choice;
``` 
(al moet ik zeggen dat sommige developers ?: te complex vinden. Maar switch expressions zijn altijd waardevol!)

12. Weet wanneer je welk type gebruikt!
- gehele getallen: int 
- metingen uit de fysieke (en soms wiskundige) wereld: double
- geldbedragen: BigDecimal
- iets dat geen of niet altijd een getal is maar slechts een relatief beperkt aantal waarden heeft: enum
- tekst: String
- collectie items: 
	- volgorde is belangrijk, duplicaten kunnen voorkomen: List, SOMS array
	- volgorde is niet belangrijk, geen duplicaten: Set / HashSet
	- een andere index dan 0...N zou handig zijn: Map/HashMap


13. Probeer methodes relatief klein en simpel te houden; minder dan een scherm, cyclomatische complexiteit liefst onder 5, zeker onder 10! 
- Visser raadt maximaal 16 niet-wit+niet commentaarregels in een methode aan.  Maar af en toe iets langer is niet erg. Van een scherm aflopen kan wel problematisch zijn!
- cyclomatische complexiteit van een methode: 1(voor de methode zelf)+ 1 voor elke if, for en while; 1 voor elke || en && // sommigen rekenen ook case-statements mee
--

Bug-territory 

A) Let op bij ints, zeker als je erdoor deelt: je krijgt niet altijd het correcte resultaat 

```
int area = baseInt * heightInt / 2;
```
Wat als baseInt en heightInt allebei 1 zijn?

Beter: 

```
double area = baseInt * heightInt / 2.0;
```

B) input in Java. Normaal kan je scanner.next(), scanner.nextLine(), en scanner.nextInt() [en scanner.nextDouble() etc.] gebruiken.

``` 
System.out.print("Please give a number: ");
int number = scanner.nextInt();
System.out.print("Please give a word: ");
String word = scanner.nextLine();
``` 
levert echter een probleem op: het woord wordt een lege String, omdat de nextInt() het getal inleest maar de newline laat staan, en een nextLine leest alles wat er is tot en met de newline, dus leest een lege String!

Hoe voorkom je dat?
Één manier is geen nextInt te gebruiken maar iets te doen als
```
int number = Integer.parseInt(scanner.nextLine());
```

Maar dit zijn twee aparte methodes voor 1 ding, als je iets beter kan programmeren maak je normaal een eigen library-methode als 

```
class AskFor {
	public static int anInt() {
		Scanner in = new Scanner(System.in);
		return Integer.parseInt(in.nextLine());
	}
}

...

int number = AskFor.anInt();
```









