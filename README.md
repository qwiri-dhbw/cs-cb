  # Aufgabe 1 (Compilephasen)

> Nennen Sie die 6 Compilephasen

1. Lexikalische Analyse
2. Syntaktische Analyse
3. Semantische Analyse
4. Zwischencodegenerierung
5. Optimierung
6. Maschinencodegenerierung

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

# Aufgabe 2 (Reguläre Ausdrücke)

> Hexadezimalzahlen bestehen aus dem Prefix 0x und mindestens eine Ziffer aus 0-9 und A-F.
> Beispiel: 0xA4, 0x00C3
> 
> Beschreiben Sie Hexadezimalzahlen mit einem regulären Ausdruck

0x[0-9A-F]+

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

## Aufgabe 3 (Grammatik)

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

> Gegeben [sind] die folgenden Grammatikproduktionen
>
> ```
> P1) Call ::= IDENTIFIER ArgList ")"
> P2) ArgList ::= ArgList Arg | "("
> ```
>
> Die Produktion P2 ist linksrekursiv. Geben Sie das allgemeine Schema an, um linksrekursive Produktionen in rechtsrekursive umzuwandeln

```
X ::= X α | β
wird zu
X ::= β X'
X' ::= α X' | ɛ 
```

> Wandeln Sie P2 in äquivalente rechtsrekursive Produktionen um

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

## Aufgabe 4 (Recursive Descent Parser)

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

> Warum ist die Verwendung von Keywords (z. B. `if`, `while`) als Variablennamen in den meisten Programmiersprachen verboten?

Die Keywords erlauben es Sprachkonstrukte eindeutig zu erkennen. Dadurch reduziert sich das Risiko von Grammatikkonflikten.

---

## Aufgabe 5 (LR Parser)

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

## Aufgabe 6 (Optimierung)

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

## Aufgabe 7 (Zwischencodegenerierung)

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
