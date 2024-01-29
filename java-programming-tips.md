## Tips en opmerkingen 


@Override (6)
collecties 
	- niet raw (9)
	- diamond operator (10)
commentaren (7)
delen (met ints) (15)
DRY (4)
formatteren (2): code formatteren (spaties en namen)
git (1) /welke files
keuze-statements (11)
lifetimes minimaliseren (5)
List.of(14)
loops (3) / welke kiezen
magische constanten (7)
Map.of(14)
methodes:
	fout gebruik niet 'compenseren' (22) 
	klein en simpel houden (13)	
record (14)
recursie (3, 17)
scopes minimaliseren (5)
Scanner: valkuilen (16)
Set.of (14)
System.out.println (21)
type (keuze) (12)
uitgecommentarieerde code (19)
var (14)
variabelen
	naamgeving (18)
	muteerbaarheid (19)

1. Welke files wel en niet in GitHub
2. Formatteren van files (spacing en namen)
	- 2b: zelfs al zien formatters het niet altijd, lege regel na methode is goed, dubbele lege regel of lege regel na '}' is vaak overbodig
	- 2c: laat regels niet van scherm aflopen (lastig voor version control en reviews)
	- 2d: naamgeving: user-defined types (klassen, records, enums) UpperCamelCase, final static fields en enum-waarden MACRO_CASE, rest lowerCamelCase.
3. Welke loop kiezen
4. DRY: niet telkens dezelfde constante definiëren
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
15. Pas op bij delen door ints!
16. Let op de valkuilen van de Scanner-klasse!
17. Vermijd recursie, zeker bij user-input
18. Vermijd zoveel mogelijk variabelennamen die het type bevatten
19. Maak variabelen liefst constant of invariant (en velden private final) 
20. Verwijder uitgecommentarieerde en ongebruikte code
21. System.out.println() is voldoende voor een lege regel!
22. Probeer fout gebruik van methoden niet te 'compenseren'. Beter: Fail Fast, Fail Cheap!

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

Dus gebruik
	
``` 
`System.out.println("The enemy appears to be coming from " + directionY + directionX);`

public int getNextInt(String question, int min, int max){
``` 

in plaats van 

``` 
`System.out.println("The enemy appears to be coming from " + DirectionY + DirectionX);`

public int GetNextInt(String question, int min, int max){
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


Je hebt in Java meerdere keuzes voor herhaling, zelfs zes!
- for-loops // for (int i = 0; i < 10; i++) { ... }
- enhanced for-loops // for (int number : numbers) { ... }
- while-loop // while (a < 4) { ... }
- do-while-loop // do {... } while (a < 4);
- streams: `List<String> bigWords = words.stream.map(w -> w.toUpperCase()).toList();`
- recursie: // void countDown(int n) { if (n>0) { System.out.println(n); countdown(n - 1);}}

Een basaal beslissingsdiagram:
- herhaal je over een collectie?
	+ JA: wil je nieuwe waarden produceren?
		* JA: probeer een stream; als dat te moeilijk is, enhanced for-loop
		* NEE: enhanced for-loop
	+ NEE: staat het aantal herhalingen van tevoren vast?
		* JA: for-loop 
		* NEE: moet de loop minstens 1x doorlopen worden?
			- JA: do-while-loop
			- NEE: while-loop
			
Twee opmerkingen:
	- Is het echt zo makkelijk/zwart-wit? Wel, soms zijn er twijfelgevallen; de gevechtsloop in 'Hunting the Manticore' is iets wat je noodzakelijkerwijs minstens 1x doorloopt en het aantal herhalingen staat niet van tevoren vast, wat een do-while-loop suggereert. Aan de andere kant, je houdt ook het aantal rondes bij, die maximaal 15 kunnen zijn, en een for-loop heeft die features al ingebakken. Je zou dus beide kunnen kiezen; hier is het verschil niet enorm belangrijk.
	- Wat is de plaats van recursie in dit schema? Eigenlijk gebruik je als professionele developer (in tegenstelling tot bij HackerRank of programmeerpuzzels) bijna nooit recursie; het kan te gemakkelijk tot een crashend programma leiden via een stack overflow, en is voor de meeste programmeurs ook moeilijker te begrijpen. De enige plaats waar ik recursie heb toegepast (en dat is 1x ten opzichte van honderden loops) was bij een boomstructuur van groepen en abonnees. Dus recursie is goed bij recursieve/boomachtige datastructuren, vermijd het voor de rest, en zeker als je als programmeur geen controle hebt over de input (lees: gebruikersinvoer).

4. DRY (don't repeat yourself) is een nuttig principe om code onderhoudbaarder te maken. Wat als iemand een monitor heeft met hoge resolutie? Dan is `System.out.println("\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n");` mogelijk niet genoeg. Beter iets als `for (int i=0; i<MAX_SCREEN_HEIGHT;i++) System.out.println();`
DRY gaat overigens dieper (zie ook https://en.wikipedia.org/wiki/Don%27t_repeat_yourself). 

Beschouw bijvoorbeeld deze code:

```
public class ColoredItem<T> {
    private String name;
    private ConsoleColor color;
    private String colorName;

    public ColoredItem(String name, ConsoleColor color) {
        this.name = name;
        this.color = color;
        this.colorName = color.name().toLowerCase();
    }


    void display() {
        System.out.println(color.getANSI() + colorName + " " + name + ConsoleColor.RESET.getANSI());
    }
```
De velden 'color' en 'colorName' zijn niet onafhankelijk - color heeft de colorName in zich. Dit is ook een duplicatie van data; als je niet oppast (zeker bij non-final fields) zou je een color kunnen krijgen met de verkeerde naam. Maar als je color.name().toLowerCase() niet telkens wilt herhalen: de volgende oplossing is DRY. Dit zie je overigens vaker met DRY: vervang data door berekening...

```
public class ColoredItem<T> {
    private final String name;
    private final ConsoleColor color;
    
    private String colorName() { return color.name().toLowerCase(); }

    public ColoredItem(String name, ConsoleColor color) {
        this.name = name;
        this.color = color;
    }

    void display() {
        System.out.println(color.getANSI() + colorName() + " " + name + ConsoleColor.RESET.getANSI());
    }
```

(Er is overigens iets eigenaardigs aan de hand met deze code: je gebruikt T als generic argument, maar nergens wordt een waarde T gebruikt in de klasse zelf. Waarschijnlijk heb je een T-veld nodig en krijg je de naam daarvan met .getClass().getSimpleName())


5. Het kan goed zijn de scopes van waarden zo klein mogelijk te houden. Bij de Defense of Consolas: `public static final String ANSI_RESET = "\u001B[0m";`. Hier gebruikt alleen de klasse zelf ze, dus private static is dan beter. Als andere programma's die waarde ook gebruiken, maak er een aparte klasse voor!

Een ander geval:
```
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		final int sisters = 4;
		int eggs;
		int eggsPP;
		int eggsDuckbear;
		
		//asking how many eggs
		System.out.print("how many eggs today? ");
		String eggsString = sc.nextLine();
		eggs = Integer.parseInt(eggsString);
		
		//calculation
		eggsPP = eggs / sisters; //when using an integer, java discards the numbers behind the ,/.  So deviding by the sisters is enough in this case
		eggsDuckbear = eggs % sisters;
		
		//print the result
		System.out.println("Each sister should get " + eggsPP + " eggs");
		System.out.println("The Duckbear should get " + eggsDuckbear + " eggs");
	}
```

kan beter als 

	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		
		//asking how many eggs
		System.out.print("how many eggs today? ");
		String eggsString = sc.nextLine();
		int eggs = Integer.parseInt(eggsString);
		
		//calculation
		final int sisters = 4;
		int eggsPP = eggs / sisters; //when using an integer, java discards the numbers behind the ,/.  So deviding by the sisters is enough in this case
		int eggsDuckbear = eggs % sisters;
		
		//print the result
		System.out.println("Each sister should get " + eggsPP + " eggs");
		System.out.println("The Duckbear should get " + eggsDuckbear + " eggs");
	}


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

Een diagram voor keuze-statements
- heb je meer dan twee keuzes?
	+ JA: Heb je een klein aantal mogelijke waarden, of een groot aantal?
		* klein: heb je een query (waarde) nodig, of juist een command (actie)?
			- query: switch-expression (String feedback = switch(grade) { case 10 -> "uitstekend"; } OF Map (tabel)
			- command: switch-statement (met : of ->)
		* groot: if-else-keten (soms kan je het doen met een 'grenzentabel' met een stream of loop, zoals [(4,0),(13,20),(64,30),(130,25)] waarbij je de eerste waarde zoekt die groter is dan de gegeven waarde, je zou de entreeprijs van een 12-jarig kind kunnen berekenen door te zien dat 12>4, maar 13>12, dus een 12-jarige moet 20 euro betalen)
	+ NEE: heb je een query (waarde) nodig, of juist een command (actie)?
		* query: ternaire operator
		* command: if-statement

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

14. Handig, maar niet in het OCA 
- records: record Person(String firstname, String lastname) {} // onveranderlijke datastructuur, afkorting van 
```
class Person {
	private final String firstname;
	
	private final String lastname;
	
	public Person(String firstname, String lastname) {
		this.firstname = firstname;
		this.lastname = lastname;
	}
	
	public firstname() { return firstname; }
	
	public lastname() { return lastname; }
	
	@Override String toString() {
		return "Person[firstname=" + firstname + ", lastname=" + lastname + "]"
	}
	
	@Override equals(Object other) {
		if (other == null) return false;
		if (other == this) return true;
		if (other instanceof Person otherPerson) return otherPerson.firstname().equals(firstname) && otherPerson.lastname().equals(lastname);
		return false; // other is not a Person
	}
} 
```
- List.of: te gebruiken als `List<Int> myNumbers = List.of(1,2,5,7,12);`
- Set.of: lijkt op List.of 
- Map.of: kan als `Map<String, String> dictionary = Map.of("white", "wit", "black", "zwart"); Helaas wel gelimiteerd tot 10 entries; anders moet je handmatig entries maken.
- enums: zijn handig bij beperkt aantal mogelijke waarden (HOND, KAT, GOUDVIS)
- var: je kunt in plaats van String name = "Frits"; ook `var name = "Frits";` zetten. Nu nog niet zo belangrijk, maar wordt handiger zodra je dingen krijgt als `AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(ProgramConfiguration.class);`


15. Let op bij ints, zeker als je erdoor deelt: je krijgt niet altijd het correcte resultaat 

```
int area = baseInt * heightInt / 2;
```
Wat als baseInt en heightInt allebei 1 zijn?

Beter: 

```
double area = baseInt * heightInt / 2.0;
```

16. input in Java. Normaal kan je scanner.next(), scanner.nextLine(), en scanner.nextInt() [en scanner.nextDouble() etc.] gebruiken.

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


17. Exceptie versus logica: normaal probeer je zoveel mogelijk logica te gebruiken en excepties alleen te gebruiken voor uitzonderlijke gevallen; zowel wegens performance als wegens leesbaarheid.

```
	public static int askForNumber(String text)
	{
		Scanner input = new Scanner(System.in);
		do {
			System.out.println(text);
			try {
				int number = input.nextInt();	
				input.nextLine();
				return number;
			} catch(NumberFormatException e) {
				System.out.println("\nERROR: your input was not a valid number. Please try again.\n\n");
			}
		} while (true);
	}
``` 

kan ook (en denk ik beter) als 	

```
	public static int askForNumber(String text)
	{
		Scanner input = new Scanner(System.in);
		do {
			System.out.println(text);
			if (input.hasNextInt()) {
				int number = input.nextInt();	
				input.nextLine();
				return number;
			} else {
				System.out.println("\nERROR: your input was not a valid number. Please try again.\n\n");
			}
		} while (true);
	}
``` 


17. Vermijd recursie, zeker bij user-input
```
	public static int askForNumber(String text)
	{
		Scanner input = new Scanner(System.in);
		int numberInt = 0;
		
		System.out.println(text);
		String number = input.next();
		try
		{
			return numberInt = Integer.parseInt(number);
		}
		catch(NumberFormatException e)
		{
			System.out.println("\nERROR: your input was not a valid number. Please try again.\n\n");
			return askForNumber(text);
		}
	}
``` 
Bovenstaande code kan leiden tot een StackOverflowError en bijbehorende programmacrash. Een do-while-loop zou hier beter zijn!	
	
18. Vroeger, toen IDEs slechter waren en je alle variabelen aan het begin van een functie moest declareren, gebruikten mensen vaak de 'Hungarian notation', bv sName (name is een String) of iCount (count is een int). Tegenwoordig wordt dat afgeraden, ook omdat het code moeilijker te veranderen maakt en extra hersencapaciteit kost bij het lezen. Beschouw

`xcoordinateInt`

Dit zou een probleem opleveren als je ooit besluit dat een xCoordinaat een double zou moeten zijn! Liefst scheid je "wat iets is" en "hoe iets geimplementeerd is" voor elkaar, net zoals een werkgever zoekt naar een "Java-developer", en niet naar een "Frieda Janssen". De enige uitzondering die ik zelf weleens gebruik is bij het parsen van input, dan heb ik xCoordinateAsString of xCoordinateAsText, om ze te onderscheiden van de echte xCoordinate. Maar buiten zulke conversiegevallen kan je types in variabelennamen beter vermijden.
	
19. Maak variabel liefst constant of invariant, om bugs tegen te gaan. Voor velden is "private final" de ideale default. (constant + minimale scope). Dus liever

```
public class InventoryItem{
	private final double weight;
	private final double volume;
	private final String name;

```
dan
```
public class InventoryItem{
	double weight;
	double volume;
	String name;

```

20. Verwijder uitgecommentarieerde en ongebruikte code

	//System.out.println(new Rope().toString());
Dode code

21. Liefst zorg je voor Command-Query separation: https://martinfowler.com/bliki/CommandQuerySeparation.html. Dat wil zeggen: een methode verandert iets OF geeft een waarde terug. Methodes die allebei doen zijn vaak lastiger te begrijpen.

Neem:

```
	public boolean addInventoryItem(InventoryItem item){
		if (currentWeight + item.getWeight() >= maxWeight){
			System.out.println("Couldn't add item, weight is maxed out");
			return false;
		} else if (currentVolume + item.getVolume() >= maxVolume){
			System.out.println("Couldn't add item, volume is maxed out.");
			return false;
		} else {
			items.add(item);
			currentWeight += item.getWeight();
			currentVolume += item.getVolume();
			packItemsAmount++;
			return true;
		}
	}
```

Hier zou je command-query-separation kunnen toevoegen via het execute/canExecute -patroon:


```
	public boolean canAddItem(InventoryItem item) {
		if (currentWeight + item.getWeight() >= maxWeight){
			System.out.println("Couldn't add item, weight is maxed out");
			return false;
		} else if (currentVolume + item.getVolume() >= maxVolume){
			System.out.println("Couldn't add item, volume is maxed out.");
			return false;
		} 
		return true;
	}

	public void addInventoryItem(InventoryItem item){
		if (!canAddItem(item)) throw new IllegalArgumentException("Item addition has not been checked with 'canAddItem'!");
		
		items.add(item);
		currentWeight += item.getWeight();
		currentVolume += item.getVolume();
		packItemsAmount++;
		return true;
	}
```

	
21. System.out.println() is voldoende voor een lege regel!
```
        String blankLine = " ";

        for (int i = 0; i < numberOfBlankLines; i++) {
            System.out.println(blankLine);

        }
```


kan ook als 
```
        for (int i = 0; i < numberOfBlankLines; i++) {
            System.out.println();
        }
```

22. Als een andere programmeur/gebruiker van je methode fouten maakt, laat dat duidelijk weten, in plaats van fouten te verdoezelen (wat later vaak tot problemen leidt)

Dus in plaats van 

```
public int GetNextInt(String question, int min, int max){
		if (min > max) return min;
``` 

probeer iets als 

```
public int GetNextInt(String question, int min, int max){
		if (min >= max) throw new IllegalArgumentException("The maximum value should be bigger than the minimum value, else input is meaningless!");
``` 
