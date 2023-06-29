# Compilephasen

> **` 🔴 HOT `** Phasen angeben, Input, Output, Typische Fehlermeldungen

| Phase                    | Input        | Output                           | Typische Fehlermeldung                   |
| ------------------------ | ------------ | -------------------------------- | ---------------------------------------- |
| Lexikalische Analyse     | Zeichen      | Token                            | Fehlendes `"` am Ende des Stringliterals |
| Syntaktische Analyse     | Token        | AST                              | Fehlende schließende Klammer             |
| Semantische Analyse      | AST          | AST mit TypeInfo + SymbolTabelle | Variable nicht deklariert                |
| Zwischencodegenerierung  | AST          | Symboltabelle -> Zwischencode    |                                          |
| Optimierung              | Zwischencode | Zwischencode                     |                                          |
| Maschinencodegenerierung | Zwischencode | Ausführbarer Code                |                                          |

---

> Ab dem Ende welcher Phase ist garantiert, dass das Programm fehlerfrei übersetzbar ist?

Semantische Analyse

---

> Geben Sie für die folgenden Fehlermeldungen an, von welcher Compilephase sie gemeldet werden
>
> 1) Fehlende schließende Klammer

Syntaktische Analyse

> 2) Fehlendes Hochkomma am Ende eines Stringliterals

Lexikalische Analyse

> 3) Fehlendes Return in einer Funktion

Semantische Analyse

> 4) Fehlendes Semikolon eines Statements

Syntaktische Anaylse

---

> Gegeben [ist] der Programmausschnitt: `mwst = preis + 0.19 * preis;`. Skizzieren Sie das Ergebnis der 1. und 2. Compilephase für diesen Programmausschnitt

**Phase 1**: `mwst|=|preis|+|0.19|*|preis|;`

**Phase 2**:

```
assignStmt
	IDENT mwst
	sumExpr
		IDENT preis
		mulExpr
			LITERAL 0.19
			IDENT preis
```

---

# Reguläre Ausdrücke

> Hexadezimalzahlen bestehen aus dem Prefix 0x und mindestens eine Ziffer aus 0-9 und A-F.
> Beispiel: 0xA4, 0x00C3
> 
> Beschreiben Sie Hexadezimalzahlen mit einem regulären Ausdruck
> **Note** ggf. zusätzlich mit Kleinbuchstaben, auf Aufgabe achten!

```re
0x[0-9A-F]+
oder:
0x[0-9A-Fa-f]+
```

> Erstellen Sie einen endlichen Automaten, der Hexadezimalzahlen akzeptiert

```
Start: (0, expectX)
expectX: (x, expectFirstDigit)
expectFirstDigit: (0-9A-F, digitIter)
digitIter: (0-9A-F, digitIter) Endzustand
```

> Erstellen Sie einen weiteren Automaten, der Namen, die aus mindestens einem Kleinbuchstaben (a-z) bestehen, akzeptiert. Beispiel: `adc`

```
Start: (a-z, letterIter)
letterIter: (a-z, letterIter) Endzustand
```

> Fügen Sie die beiden Automaten zu einem Automaten zusammen, der sowohl Hexadezimalzahlen, als auch Namen akzeptiert

```
Start: (0, expectX) (a-z, letterIter)
expectX: (x, expectFirstDigit)
expectFirstDigit: (0-9A-F, digitIter)
digitIter: (0-9A-F, digitIter) Endzustand
letterIter: (a-z, letterIter) Endzustand
```

---

> Erstellen Sie einen regulären Ausdruck, der alle natürlichen Zahlen > 0 erlaubt (z. B. `347`)

```re
[1-9][0-9]*
```

> Ergänzen Sie den regulären Ausdruck so, dass nach jeweils 3 Stellen von hinten ein `.` aks Tausendertrennzeichen erlaubt ist
> z. B. `1234`, `1.234`, `3.689755`, `2067.418`

```re
[1-9]\d{0,2}(\.?\d{3})
```

> Erstellen Sie einen endlichen Automaten, der Namen die mit einem Buchstaben beginnen, und im folgenden Buchstaben oder Ziffern beinhalten können, akzeptiert
> z. B. `abc`, `A22`, `c5E`

```
Start: (a-zA-Z, charIter)
charIter: (a-zA-Z0-9, charIter) Endzustand
```

> Erstellen Sie einen endlichen Automaten, der eine natürliche Zahl gefolgt von einem Namen akzeptiert

```
Start: ([1-9], digitIter)
digitIter: ([0-9], letterStart)
letterStart: ([a-zA-Z], letterIter) Endzustand
letterIter: ([a-zA-Z0-9], letterIter) Endzustand
```

---

> Erstellen Sie einen endlichen Automaten, der Python Stringliterale (mit 3 Anführungszeichen) akzeptiert.

```
Start: (", hochkomma1)
hochkomma1: (", hochkomma2)
hochkomma2: (", innerLiteral)
innerLiteral: (", innerhochkomma1), ([^"], innerLiteral)
innerhochkomma1: (", innerhochkomma2) , ([^"], innerLiteral)
innerhochkomma2: (", ende) , ([^"], innerLiteral)
ende: Endzustand
```

---

> Erstelle für den regulären Ausdruck `([A-Z][0-9])*|(xy)+` einen äquivalenten deterministischen endlichen Automaten

```
Start: (A-Z, afterBigLetter) (x, afterX) Endzustand
afterBigLetter: (0-9, afterDigit)
afterDigit: (A-Z, afterBigLetter) Endzustand
afterX: (y, afterY)
afterY: (x, afterX) Endzustand
```

---

> Gegeben sind zwei endliche Automaten `A` und `B`. Wie lautet der Ansatz, um einen endlichen Automaten `AB` zu erstellen, der alle Worte akzeptiert, die von Automat `A` oder von Automat `B` akzeptiert werden?

Gemeinsamer Startzustand

---

# Grammatik (-analyse)

> Materialnummer besteht aus Folge von mind. 2 Ziffern, optional gefolgt von genau 3 Buchstaben.
>
> Beispiele: `74BeD`, `5277`
>
> Beschreiben Sie Materialnummer als kontextfreie Grammatik

```
Materialnummer ::= Ziffer Ziffer (Ziffer)* (Buchstabe Buchstabe Buchstabe)?
Ziffer ::= [0-9]
Buchstabe ::= [a-z] [A-Z]
```

---

> Schreiben Sie die Grammatikproduktion `a ::= (b)? (c)*` in äquivalente Grammatikproduktionen um, die weder `?`, noch `*` benutzt

```
a ::= bOpt cIter;
bOpt ::= b | ɛ;
cIter ::= c cIter | ɛ;
```

> Schreiben Sie die Grammatikproduktion `a ::= b | c+` in äquivalente Grammatikproduktion um, die keines der Abkürzungen `+`, `*`, `?` verwenden

```
a ::= b
a ::= c cIter
cIter ::= c cIter
cIter ::= ɛ
```

> Schreiben Sie die Grammatikproduktion `a ::= b? c* | d` in äquivalente Grammatikproduktion um, die keines der Abkürzungen `+`, `*`, `?` verwenden

```
a: bOpt cIter
bOpt: b
bOpt: ɛ 
cIter: c cIter 
cIter: ɛ
a: d
```

---

> Gegeben [sind] die folgenden Grammatikproduktionen für das NonTerminal ifStmt
>
> ```
> P1) ifStmt ::= IF cond THEN LBRACE stmtlist RBRACE
> P2) ifStmt ::= IF cond THEN LBRACE stmtlist RBRACE ELSE LBRACE stmtlist RBRACE
> IF, THEN, ELSE, RBRACE und LBRACE sind TerminalSymbole
> ```
>
> Warum sind die Produktionen für ifStmt nicht in LL(1) Form?

Die Produktionen P1 und P2 für das NonTerminal ifStmt beginnen mit demselben Terminal IF. Daher sind die Selection Sets nicht disjunkt.

> Welches Verfahren müssen Sie anwenden, um die obigen Produktionen für ifStmt in LL(1) Form zu bringen?

Linksfaktorisierung

> Wandeln Sie die Produktionen für ifStmt so um, dass sie in LL(1) Form sind.

```
ifStmt ::= IF cond THEN LBRACE stmtlist RBRACE elseOpt
elseOpt ::= ELSE LBRACE stmtlist RBRACE | ɛ
```

---

> **` 🔴 HOT `** Geben Sie das allgemeine Schema an, um linksrekursive Produktionen in rechtsrekursive umzuwandeln

```
X ::= X α | β
wird zu
X ::= β X'
X' ::= α X' | ɛ 
```

---

> Gegeben [sind] die folgenden linksrekursive Grammatikproduktion.
> Wandeln Sie diese in äquivalente rechtsrekursive Grammatikproduktionen.
>
> Hinweis: Es genügt die Linksrekursion zu beseitigen. Weiteres kürzen ist nicht erforderlich.

> ```
> P1) identifier : identifier ZIFFER  
> P2) identifier : BUCHSTABE identifier
> P3) identifier: UNDERSCORE
> ```

```
α = ZIFFER
β = BUCHSTABE identifier | UNDERSCORE
X = identifier
identifier : BUCHSTABE identifier identifierRecursive  | UNDERSCORE identifierRecursive
identifierRecursive : ZIFFER identifierRecursive  | ɛ
```

**---**

> ```
> P1) nonT : nonT RTERM
> P2) nonT: MTERM nonT MTERM
> P3) nonT: LTERM nonT
> ```

```
α = LTERM
β = MTERM nonT MTERM | LTERM nonT 
X = nonT
nonT: (MTERM nonT MTERM | LTERM nonT) nonTRecursive
nonTRecursive: RTERM nonTRecursive | ɛ 
```

---

> Gegeben [sind] die folgenden Grammatikproduktionen
>
> ```
> P1) Call ::= IDENTIFIER ArgList ")"
> P2) ArgList ::= ArgList Arg | "("
> ```
>
> Die Produktion P2 ist linksrekursiv. 
> 
> Wandeln Sie P2 in äquivalente rechtsrekursive Produktionen um.

```
ArgList ::= "(" ArgListRecursive
ArgListRecursive ::= Arg ArgListRecursive | ɛ
```

> Das First Set von Arg enthält nicht ɛ. Welche Bedingung muss für das First Set von Arg außerdem gelten, damit die Grammatik LL(1) ist?

Die Selection Sets für Produktionen für ArgListRecursive müssen disjunkt sein. Daher muss First(Arg) diskunkt mit Follow(ArgListRecursive) sein, darf also nicht ")" enthalten

---

> Boolesche Ausdrücke benutzen die Literale `true` und `false`, die binären Operatoren `and` und `or`, den unären Operator `not`, sowie runde Klammern.
>
> Beispiel: `true and not (false or true and not true)`
>
> Geklammerte Ausdrücke haben die höchste Priorität. Danach folgen `not`, `and` und mit der niedrigsten Priorität `or`.
> Geben Sie eine vollständige LL(1) Grammatik für Boolesche Audrücke an, die diese Prioritäten korrekt abbildet.

```
Terminals = { "true", "false", "and", "or", "not", "(", ")" }
NonTerminals = { innerExpr, notExpr, andExpr, orExpr }
StartSymbol = orExpr

Produktionen

innerExpr ::= "true" | "false" | "(" orExpr ")"
notExpr ::= ("not")+ innerExpr | innerExpr
andExpr ::= notExpr ("and" notExpr)*
orExpr ::= andExpr ("or" andExpr)*
```

---

> Erstellen Sie eine LL(1) Grammatik für Conditions, die folgendes erlaubt:
>
> - Vergleiche mit (<, >)
> - Und/Oder Verknüpfung von Vergleichen und anderen Und/Oder Verknüpfungen
> - Klammerung von Und/Oder Verknüpfungen
>
> Die Behandlung von Leerzeichen können Sie ignorieren. Ebenso müssen Sie keine Regeln für die Terminalsymbole angeben.

```
TerminalSymbole: NUMBER, AND, OR, LPAREN, RPAREN, LESS, GREATER
NonTerminalSymbole: comparison, logicalExpression, atomicLogicalExpression
StartSymbol: logicalExpression
Produktionen:
logicalExpression : atomicLogicalExpression ((AND|OR) atomicLogicalExpression)*
atomicLogicalExpression : comparison | LPAREN logicalExpression RPAREN
comparison : NUMBER (LESS|GREATER) NUMBER
```

---

> Geben Sie eine Grammatik für das `while` Statement in Java an. (`expression` und `statement` gegeben)

```
whilestmt: "while" "(" expression ")" "{" stmtlist "}"
stmtlist: statement*
```

---

> Geben Sie eine Grammatik für das `try .. catch` in Java an.

```
trystmt: try LBRACE stmtlist RBRACE catchlist
catchlist: catch*
catch: CATCH LPAREN exceptionSpecifier RPAREN LBRACE stmtlist RBRACE
stmtlist: statment*
```

---

# Semantische Analyse

> Gegeben die Grammatikregel für eine Variablendeklaration:
>
> ```
> varDecl ::= IDENTIFIER IDENTIFIER ASSIGN expr SEMICOL
> ```
>
> Welche Aktionen geschehen für eine Variablendeklaration während der semantischen Analyse?

- Prüfung ob eine Variable mit dem gegebenen (zweiten) `IDENTIFIER` bereits existiert.
- Einfügen der Variable mit dem gegebenen `IDENTIFIER` und dem aktuellen Scope in die Symboltabelle
- Feststellen des Typs der Variable anhand des (ersten) `IDENTIFIER`
- Prüfen ob `expr` einen dazu kompatiblen Typ hat
- Gegebenenfalls eine implizite Typkonvertierung einfügen

# Recursive Descent Parser

> Gegeben [ist] die Grammatik G mit
>
> ```
> Terminals = { LBRACKET RBRACKET, COMMA, IDENTIFIER, NUMBER }
> NonTerminals = { ArrrayDefinition, DimensionList, DimensionListRecursive }
> StartSymbol = ArrayDefinition
> Produktionen
> P1)	ArrayDefinition 		::= IDENTIFIER LBRACKET DimensionList RBRACKET.
> P2)	DimensionList :			::= NUMBER DimensionListRecursive
> P3)	DimensionListRecursive 	::= COMMA NUMBER DimensionListRecursive
> P4)	DimensionListRecursive	::= ɛ
> ```
>
> Bestimmen Sie die First Sets für die Produktion P1 bis P4

```
First(P1) = { IDENTIFIER }
First(P2) = { NUMBER }
First(P3) = { COMMA }
First(P4) = { ɛ }
```

> Bestimmen Sie das Follow Set für das NonTerminal DimensionListRecursive

```
FOLLOW(DimensionListRecursive) = FOLLOW(DimensionList) = { RBRACKET }
```

> Bestimmen Sie das Selection Set für die Produktionen P3 und P4

```
SELECTION(P3) = FIRST(P3) = { COMMA }
SELECTION(P4) = FIRST(P4) / ɛ vereinigt FOLLOW(DimensionList) = { RBRACKET }
```

> Implementieren Sie den recursive descent Parser für die Grammatik in Pseudocode

```java
void parseArrayDefinition() {
	lexer.expect(IDENTIFIER);
	lexer.expect(LBRACKET);
	parseDimensionList();
	lexer.expect(RBRACKET);
}

void parseDimensionList() {
	lexer.expect(NUMBER);
	parseDimensionListRecursive();
}

void parseDimensionListRecursive() {
	if (lexer.lookAheadToken() == COMMA) {
		lexer.advance();
		lexer.expect(NUMBER);
		parseDimensionListRecursive();
	}
}
```

> Gegeben die Grammatik G mit
>
> ```
> Terminals = { LETTER, NUMBER, LPAREN, RPAREN }
> NonTerminals = { start, name, parantheses }
> StartSymbol = start
> 
> Produktionen
> PS1)	start	 	: name
> PS2)	start		: parantheses
> PN1)	name		: LETTER name NUMBER
> PN2)	name		: LETTER NUMBER
> PP1)	parantheses	: LPAREN start RPAREN
> PP2)	parantheses	: ɛ 
> ```
>
> Bestimme die First Sets für alle Produktionen `PS1` bis `PP2` und die First Sets der NonTerminal Symbole `start`, `name`, `parantheses`

```
First(PN1) = { LETTER }
First(PN2) = { LETTER }
First(PP1) = { LPAREN }
First(PP2) = { ɛ }
First(parantheses) =First(PP1) u First(PP2) =  { LPAREN, ɛ }
First(name) = First(PN1) u First(PN2) = { LETTER }
First(PS1) = First(name) = { LETTER }
First(PS2) = First(parantheses) = { LPAREN, ɛ }
First(start)= First(PS1) u First(PS2) = { LETTER, LPAREN, ɛ }
```

> Berechne die Follow Sets der NonTerminalSymbole `start`, `name`, `parantheses`

```
Follow(start) = { # } u {RPAREN} = { #, RPAREN }
Follow(name) = { NUMBER } u Follow(start) = { NUMMBER, #, RPAREN}
Follow(parantheses) = Follow(start) = { #, RPAREN }
```

> Berechne die Selection Sets aller Produktionen `PS1` bis `PP2`

```
Select(PS1) = First(PS1) = { LETTER }
Select(PS2) = First(PS2) / { ɛ } u Follow(start)  = { LPAREN, RPAREN, # }
Select(PN1) = First(PN1) = { LETTER }
Select(PN2) = First(PN2) =  { LETTER }
Select(PP1) = First(PP1) = { LPAREN }
Select(PP2) = Follow(parantheses) = { #, RPAREN }
```

> Entfernen Sie die Produktion `PN1` und implementieren Sie den recursive descent Parser für die verbleibende Grammatik in Pseudocode.

```java
void processStart() {
    if (lexer.lookAheadToken() == LETTER) {
        processName();
    } else if (lexer.lookAheadToken() in (LPAREN, RPAREN, EOF)) {
        processParantheses();
    } else {
        throw error;
    }
}

void processName() {
    lexer.expect(LETTER);
    lexer.expect(NUMBER);
}

void processParantheses() {
    if (lexer.lookAhead() == LPAREN) {
        lexer.advance();
        processStart();
        lexer.expect(RPAREN);
    } else if (lexer.lookAhead() in (RPAREN, EOF)) {
    } else {
        throw error;
    }
}
```

---

> Warum ist die Verwendung von Keywords (z. B. `if`, `while`) als Variablennamen in den meisten Programmiersprachen verboten?

Die Keywords erlauben es Sprachkonstrukte eindeutig zu erkennen. Dadurch reduziert sich das Risiko von Grammatikkonflikten.

---

# LR Parser

> Was ist der Vorteil eines LR Parsers gegenüber einem LL Parser?

Da der LR Parser erst nach dem Einlesen einer kompletten Regel entscheidet, ob er sie anwendet, sind Grammatikkonflikte leichter zu vermeiden

> Gegeben [ist] die Grammatik mit dem Startsymbol `a` und den Produktionen
>
> ```
> a ::= bc
> b ::= "b" | "B"
> c ::= "c" c | "c"
> ```
>
> Zeigen Sie die Schritte, mit denen ein LR Parser das Eingabewort "Bcc" analysiert

```
shift "B"
reduce "B" to b
shift "c"
shift "c"
reduce "c" to c
reduce "c" c to c
reduce bc to a
```

> Geben Sie eine Grammatik und ein Eingabewort an, die zu einem shift-reduce Konflikt in einem LR(1) Parser führen. An welcher Stelle tritt der Konflikt auf?

```
Grammatikproduktionen
x ::= "a"
y ::= "a" "b"
z ::= x "b"

Eingabewort
"a" "b"

Nach shift von "a" und lookahead "b" ist sowohl der reduce von "a" nach x möglich, als auch der shift von "b"
```

---

> Gegeben [ist] die Grammatik G mit
>
> ```
> Terminals { NUMBER, KOMMA }
> NonTerminals {arglist, arg }
> Startsymbol: arglist
> Produktionen:
> arglist: arg
> arglist: arglist KOMMA arg
> arg: NUMBER
> ```
>
> Gegeben das Eingabewort: `45,7,12`. Zeigen Sie die Schritte mit denen ein LR(1)-Parser (BottomUp Parser) das Eingabewort analysiert

```
shift NUMBER 45
reduce NUMBER => arg
reduce arg => arglist
shift KOMMA
shift NUMBER 7
reduce NUMBER => arg
reduce arglist KOMMA arg => arglist
reduce arg => arglist 
shift KOMMA
shift NUMBER 12
reduce NUMBER => arg
reduce arglist KOMMA arg => arglist
```

> Nach welchem Kriterium entscheidet der LR(1)-Parser, ob er die Produktion
> `arglist: arg` oder `arglist: arglist KOMMA arg` anwendet?

Anhand des look ahead symbols. Im Falle von `KOMMA` wird die Reduktion `arg => arglist` angewendet im Falle von `EOF` die Reduktion `arglist KOMMA arg => arglist`.

---

# Optimierung

> Gegeben ist folgende Java Methode:
>
> ```java
> int getValue(int input) {
>     int x = 4 + 3;
>     int y = x + input;
>     if (x > 5) {
>         return y;
>     }
>     return x;
> }
> ```
>
> Wenden Sie Constant Folding und Constant Propagation auf die Methode `getValue` an

```java
int getValue(int input) {
    int x = 7;
    int y = 7 + input;
    if (true) {
        return y;
    }
    return 7;
}
```

> Wenden Sie anschließend Dead Code Elimination an

```java
int getValue(int input) {
    int x = 7;
    int y = 7 + input;
    return y;
}
```

> Welche weitere Optimierung kann angewendet werden?

Dead Store Elimination für `int x = 7`

---

# Zwischencodegenerierung

> Gegeben [ist] der folgende Java Codeausschnitt
>
> ```java
> {
>     int index = 0;
>     while (index < 10) {
>         index++;
>     }   
> }
>
> Schreiben Sie Zwischencode für diesen Codeausschnitt

```
entry:
alloc int index;
store index, 0;
branch while_head;

while_head:
load r0, index;
r1 = cmpless r0, 10;
jumpconditional r1, while_body, after_while;

while_body:
load r2, index;
r3 = add r2, 1;
store index, r3;
jump while_head;

after_while:
```

---

> Gegeben ist der folgende Java Codeausschnitt
>
> ```java
> {
>     int input = 10;
>     int output;
>     if (input < 6) {
>         output = 4;
>     } else if (input < 11) {
>         output = 3;
>     } else {
>         output = 2;
>     }
> }
> ```
>
> Schreiben Sie Zwischencode für diesen Codeausschnitt

```
entry:
alloca i32, %input
store %input, 9
alloca i32, %output
load r0, %input
r1 = cmpless r0, 5
jump r1, if, elseifcheck

if:
store %output, 3
jump exit;

elseifcheck:
load r2, %input
r3 = cmpless r2, 10
jump r3, elseif, else

elseif:
store %output, 2
jump exit;

else:
store %output, 1
jump exit;

exit:
```

# Interpreter

> Gegeben die Grammatikregel für den Operator `add`:
>
> ```
> sum : summand (PLUS summand)*
> ```
> Skizzieren Sie in Pseudocode die Methode `parseSum()` welche die Eingabe analysiert und Instruktionen für den Interpreter erzeugt 

```python
def parse_sum(self):
    parse_summand()
    while self.lexer.look_ahead_token() == PLUS:
        parse_summand()
        Instruction add_instr = create_add_instruction()
        self.current_block.add_instruction(add_instr)
```

> Skizzieren Sie in Pseudocode die Methode `execute(ExecutionEnv)` der Klasse `InstrAdd`, um die Addition im Interpreter auszuführen

```python
class InstrAdd:
    def execute(env: ExecutionEnv):
        int op0 = env.pop_number()
        int op1 = env.pop_number()
        int result = op0 + op1
```
