# Compilephasen

> **` 🔥 HOT `** Phasen angeben, Input, Output, Typische Fehlermeldungen

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

> Gegeben ist der Programmausschnitt
>
> ```java
> int result = i - 7 - 2 * value;
> ```
>
> Skizzieren Sie den AST für diesen Programmausschnitt

```
VarDecl
	IDENTIFIER int
	IDENTIFIER result
	InitExpr
		MINUS
			MINUS
				IDENTIFIER i
				INTEGER 7
			MUL
				INTEGER 2
				IDENTIFIER value
```

> Beschreiben Sie die Aktivitäten der semantischen Analyse für diesen Programmausschnitt

- Prüfen ob `result` bereits existiert
- `result` in Symboltabelle einfügen
- Prüfen ob `value` bereits existiert
- Verbindung zur Definition von `value` herstellen
- Prüfen ob Operation `*` auf den Typen von `2` und `value` definiert ist
- Prüfen ob Operation `–` auf den Typen `i`, `7` und `2 * value` definiert ist
- Den Teilausdrücken Typen zuweisen
- `result` den Typ `int` zuweisen
- Prüfen ob der Typ der rechten Seite kompatibel zum Typ von `result` ist

---

# Reguläre Ausdrücke

## Endliche Automaten

<details>
<summary>Hexadezimalzahlen</summary>


> Hexadezimalzahlen bestehen aus dem Prefix 0x und mindestens eine Ziffer aus 0-9 und A-F.
> Beispiel: 0xA4, 0x00C3
> 
> Beschreiben Sie Hexadezimalzahlen mit einem regulären Ausdruck
> 
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

</details>

---

<details>
<summary>Natürliche Zahlen</summary>


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

</details>

---

<details>
<summary>Python Stringliterale</summary>

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

</details>

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

<details>
<summary>Base64</summary>

> Base64 ist ein Verfahren zur Kodierung von 8-Bit-Binärdaten mithilfe der druckbaren ASCII Zeichen A bis Z, a bis z, 0 bis 9, + und /. 
> Jedem der insgesamt 64 unterschiedlichen Zeichen ist ein eindeutiges Bitmuster mit 6 Bit zugeordnet.
>
> Die Kodierung erfolgt in Einheiten zu je 24 Bit. Es werden also immer drei Byte (`3*8 = 24 Bit`) des Eingabe-Bytestroms durch vier Base64-Zeichen (`4 * 6 = 24 Bit`) mit den passenden Bitmustern kodiert. 
> Die Anzahl der Zeichen in einem Base64-kodierten String ist also immer ein Vielfaches der Zahl Vier (Alignment).
> 
> Lässt sich die Eingabe nicht vollständig in Einheiten zu je 24 Bit einteilen, wird die Eingabe am Ende mit Füllbits (dem sogenannten Padding) ergänzt. 
> 6-Bit-Blöcke, die vollständig aus Füllbits bestehen, werden nicht durch die oben aufgeführten Base64-Zeichen sondern mit Gleichheitszeichen = kodiert. 
> Am Ende eines Base64-kodierten Strings können also 0, 1 oder 2 Gleichheitszeichen = stehen.
>
> Gültige Base64-Zeichenketten sind zum Beispiel AA==, AQ==, Qg==, RxE=, CBU=, DeadC0de, C0ffee==, QmFzZTY0, SGVsbG8gV29ybGQh. Das leere Wort wird ebenfalls akzeptiert.
>
> Geben Sie einen regulären Ausdruck für Base64-Zeichen an

```re
[a-zA-Z0-9+/]
```

> Erstellen Sie einen regulären Ausdruck für gültige Base64-kodierte Strings. Sie dürfen dabei den regulären Ausdruck für Base64-Zeichen mit x substituieren

```re
(xxxx)*(x===|xx==|xxx=)?
```

> Bei dem leicht modifizierten Verfahren Radix-64 wird eine CRC-24 Prüfsumme an die Base64-kodierten Daten angehängt. 
> Die kodierte Prüfsumme beginnt mit einem `=` Zeichen, gefolgt von vier Base64-Zeichen. 
> Erstellen Sie einen deterministischen endlichen Automaten für die CRC-24 Prüfsumme. 
> Die Menge der erlaubten Base64 Zeichen dürfen sie auch hier mit `x` substituieren

```
start: (=, base64_0)
base64_0: (x, base64_1)
base64_1: (x, base64_2)
base64_2: (x, base64_3)
base64_3: (x, end)
end: Endzustand
```

> Gegeben [ist] ein endlicher Automat, `B` der Base64-kodierte Strings akzeptiert und ein endlicher Automat, `C`, der die CRC-24 Prüfsumme akzeptiert. 
> Wie können Sie einen kombinierten nicht-deterministischen endlichen Automaten `BC` erstellen, der einen Base64-kodierten String mit nachfolgender Prüfsumme akzeptiert?

Epsilon Übergang von den Endzuständen von B in den Startzustand von C

</details>

---

## Sonstige Aufgaben

> Gegeben sind zwei endliche Automaten `A` und `B`. Wie lautet der Ansatz, um einen endlichen Automaten `AB` zu erstellen, der alle Worte akzeptiert, die von Automat `A` oder von Automat `B` akzeptiert werden?

Gemeinsamer Startzustand

---

# Grammatik (-analyse)

## Grammatikproduktion umschreiben

Schreiben Sie die Grammatikproduktion `...` in äquivalente Grammatikproduktionen um, die weder `+`, `?`, noch `*`

<details>
<summary><code>a ::= (b)? (c)*</code></summary>

```
a ::= bOpt cIter;
bOpt ::= b | ɛ;
cIter ::= c cIter | ɛ;
```

</details>

---

<details>
<summary><code>a ::= b | c+</code></summary>

```
a ::= b
a ::= c cIter
cIter ::= c cIter
cIter ::= ɛ
```

</details>

---

<details>
<summary><code>a ::= b? c* | d</code></summary>

```
a: bOpt cIter
bOpt: b
bOpt: ɛ 
cIter: c cIter 
cIter: ɛ
a: d
```

</details>

---

<details>
<summary><code>anton: (berta | caesar)? dora+</code></summary>

```
anton: berta_caesar_opt dora_recursive
berta_caesar_opt: berta_caesar
berta_caesar_opt: ɛ
berta_caeser: berta
berta_caesar: caesar
dora_recursive: dora dora_recursive
dora_recursive: dora
```

> Geben Sie das allgemeine Schema für die Linksfaktorisierung an, um die Linksgleichheit von 2 Produktionen zu beseitigen

```
n: alpha beta
n: alpha gamma
zu
n: npre npost
npre: alpha
npost: beta | gamma
```

</details>

---

## Grammatikproduktionen, LL(1)

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

...

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

---

```
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

> Gegeben [sind] die folgenden GRammatikproduktionen
>
> ```
> P1) ifstmt: IF expr THEN stmt
> P2) ifstmt: IF expr THEN stmt ELSE
> P3) ifstmt: IF SIGNAL THEN stmt
> ```
>
> Beseitigen Sie die Linksgleichheit durch wiederholte Anwendung der Linksfaktorisierung

```
n = ifstmt
alpha = IF
beta = (expr THEN stmt) | (expr THEN stmt ELSE)
gamma = SIGNAL THEN stmt

ifstmt : ifstmtpre ifstmtpost
ifstmtpre : IF
ifstmtpost : (expr THEN stmt) | (expr THEN stmt ELSE) | (SIGNAL THEN stmt)

n = ifstmtpost
alpha = expr THEN stmt
beta = epsilon
gamma = ELSE

ifstmt : ifstmtpre ifstmtpost
ifstmtpre : IF
ifstmtpost : ifstmtpostpre ifstmtpostpost
ifstmtpostpre : expr THEN stmt
ifstmtpostpost : epsilon | else
ifstmtpost : SIGNAL THEN stmt
```

---

> Ein Widerstandsnetzwerk werde folgendermaßen beschrieben:
> Widerstände werden durch `IDENT` bezeichnet.
> Parallel geschaltete Wiederstände werden durch operator `PAR` verknüpft.
> Seriell geschaltete Wiederstände werden durch operator `SEQ` verknüpft.
> operator `PAR` hat Priorität vor operator `SEQ`.
> Klammerung (`LPAREN`, `RPAREN`) ist erlaubt.
> Einzelne Widerstände können mit einem vorgeschobenen `L` als induktiv und mit `C` als kapazitiv markiert werden.
> Beispiele:
>
> ```
> R0 PAR R1
> R0 SEQ L R1 PAR R2
> (R0 SEQ C R5 SEQ  L R4) PAR R1
> ```
> 
> Erstellen sie eine LL(1) Grammatik für ein solches Widerstandsnetzwerk. 
> Die Behandlung von Leerzeichen können Sie ignorieren. 
> Ebenso müssen Sie keine Regeln für die Terminalsymbole (`IDENT`, `PAR`, `SEQ`, `LPAREN`, `RPAREN`) angegeben werden

```
TerminalSymbole: IDENT, PAR, SEQ, LPAREN, RPAREN
NonTerminalSymbole: seqExpression, parExpression, atomicExpression
StartSymbol: seqExpression
Produktionen:
seqExpression: parExpression (SEQ parExpression)*
parExpression: atomicExpression (PAR atomicExpression)*
atomicExpression: (L|C)? IDENT
atomicExpression: LPAR seqExpression RPAR
```

---

> Mengenoperationen werden folgendermaßen beschrieben:
> Mengen werden durch Bezeichner (`IDENT`) oder durch eine Liste von Elementen angegeben (begrenzt mit Mengenklammern {}). 
> Elemente werden durch das Token ELEM dargestellt.
> 
> Es gibt die Operationen `∪` (Vereinigung), `∩` (Schnitt), `∖` (Differenz) und `⨯` (karthesisches Produkt). 
> Neben diesen Operationen ist es möglich, Ausdrücke durch Klammern zu verschachteln.
> 
> Die Differenz hat die höchste Priorität. Die Junktoren `∪` und `∩` binden stärker als `⨯`.
> Beispiele für zulässige Mengenausdrücke sind: `(M1 ∖ M2 ∖ M3 ∪ M4) ⨯ { ELEM ELEM }`
> 
> Erstellen sie eine LL(1) Grammatik für ein solches Widerstandsnetzwerk. Die Behandlung von Leerzeichen können Sie ignorieren. 
> Ebenso müssen Sie keine Regeln für die Terminalsymbole (`IDENT`, `ELEM`, `LPAREN`, `RPAREN`, `LBRACE`, `RBRACE`, `∪,` `∩`, `∖`, `⨯`) angeben

```
Startsymbol: kartExpr
Terminalsymbole: (IDENT, ELEM, LPAREN, RPAREN, LBRACE, RBRACE, ∪, ∩, ∖,⨯,  )
NonTerminalsymbole: kartExpr, junktorExpr, junktor, diffExpr, parantheseExpr, literalExpr, elemlist
Produktionen:
kartExpr : junktorExpr (x junktorExpr)*
junktorExpr : diffExpr (junktor diffExpr)*
junktor: ∪ | ∩
diffExpr: parantheseExpr (\ parantheseExpr)*
parantheseExpr: LPAREN diffExpr RPAREN | IDENT | literalExpr
literalExpr: LBRACE elemlist RBRACE
elemlist: ELEM elemlist
elemlist: epsilon
```

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

## Grammatik in Java

<details>
<summary><code>while</code> Statement (<code>expression</code> und <code>statement</code> gegeben)</summary>

```
whilestmt: "while" "(" expression ")" "{" stmtlist "}"
stmtlist: statement*
```

</details>

---

<details>
<summary><code>try..catch</code></summary>

```
trystmt: try LBRACE stmtlist RBRACE catchlist
catchlist: catch*
catch: CATCH LPAREN exceptionSpecifier RPAREN LBRACE stmtlist RBRACE
stmtlist: statment*
```

</details>

---

## Sonstige

> **` 🔥 HOT `** Geben Sie das allgemeine Schema an, um linksrekursive Produktionen in rechtsrekursive umzuwandeln

```
X ::= X α | β
wird zu
X ::= β X'
X' ::= α X' | ɛ 
```

---

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

## First Sets etc.

<details>
<summary><code>LBRACKET, RBRACKET, COMMA, IDENTIFIER, NUMBER</code></summary>


> Gegeben [ist] die Grammatik G mit
>
> ```
> Terminals = { LBRACKET, RBRACKET, COMMA, IDENTIFIER, NUMBER }
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

</details>

---

<details>
<summary><code>LETTER, NUMBER, LPAREN, RPAREN</code></summary>

> Gegeben [ist] die Grammatik G mit
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

</details>

---

<details>
<summary>CSV (<code>COMMA, NUMBER, TEXT, LINEBREAK</code>)</summary>

> Gegeben [ist] die Grammatik G mit
>
> ```
> Terminals = { COMMA, NUMBER, TEXT, LINEBREAK }
> NonTerminals = { csvFile, csvLine, csvEntry }
> StartSymbol = csvFile
> 
> Produktionen und Selection Sets:
> PF1) csvFile: csvLine csvFile
> SELECT(PF1) = { NUMBER, TEXT, LINEBREAK }
> PF2) csvFile: epsilon
> SELECT(PF2) = { # }
> PL1) csvLine: csvEntry COMMA csvLine
> SELECT(PL1) = { NUMBER, TEXT }
> PL2) csvLine: LINEBREAK
> SELECT(PL2) = { LINEBREAK }
> PE1) csvEntry: NUMBER
> SELECT(PE1) = { NUMBER }
> PE2) csvEntry: TEXT
> SELECT(PE2) = { TEXT }
> ```
>
> Erstellen Sie einen recursive descent Parser für diese Grammatik in Pseudocode. 
> Ein Lexer mit den Methoden `getLookAheadToken()`, `advance()` und `expect(Token)`  ist vorhanden

```java
void processCsvFile() {
	if (lexer.lookAheadToken() in (NUMBER, TEXT, LINEBREAK)) {
		processCsvLine();
		processCsvFile();
	} else {
		lexer.expect(EOF);
	} 
}
void processCsvLine() {
	if (lexer.lookAheadToken() in (NUMBER, TEXT)) {
		processCsvEntry();
		lexer.expect(COMMA);
		processCsvLine();
	} else {
		lexer.expect(LINEBREAK)
	} 

void processCsvEntry() {
	if (lexer.lookAhead() in (NUMBER, TEXT)) {
		lexer.advance();
    } else {
		throw error;
    }
}

```

</details>

---

<details>
<summary><code>COMMA, IDENT, NUMBER, LPAREN, RPAREN</code></summary>

> Gegeben die Grammatik G für einen Funktionsaufruf mit
> 
> ```
> Terminals = { COMMA, IDENT, NUMBER, LPAREN, RPAREN }
> NonTerminals = { fctCall, arglist, arglistwithcomma, arg }
> StartSymbol = fctCall
> Produktionen und Selection Sets:
> PF1) fctCall: IDENT LPAREN arglist RPAREN
> SELECT(PF1) = { IDENT }
> PL1) arglist: epsilon
> SELECT(PL1) = { RPAREN }
> PL2) arglist: arg arglistcomma 
> SELECT(PL2) = { NUMBER, IDENT }
> PC1) arglistcomma: epsilon
> SELECT(PC1) = { RPAREN }
> PC2) arglistcomma: COMMA arg arglistcomma
> SELECT(PC2) = { COMMA }
> PA1) arg: IDENT
> SELECT(PA1) = { IDENT }
> PA2) arg: NUMBER
> SELECT(PA2) = { NUMBER }
> ```
>
> Erstellen sie einen recursive descent Parser für diese Grammatik in Pseudocode.

```java
void getFctCall() {
	lexer.expect(IDENT)
	lexer.expect(LPAREN)
	getArgList()
	lexer.expect(RPAREN)
}

void getArgList() {
	if (lexer.getLookAheadToken() == RPAREN) {
	} else if (lexer.getLookAheadToken() in (NUMBER, IDENT) {
		getArg();
		getArglistComma()
	} else {
		throw error;
	}
}

void getArgListComma() {
	if (lexer.getLookAheadToken() == RPAREN) {
	} else if (m_lexer.getLookAheadToken() == COMMA {
		lexer.expect(COMMA);
		getArg();
		getArgListComma()
	} else {
		throw error;
	}
}

void getArg() {
	if (lexer.getLookAheadToken() == IDENT) {
		lexer.expect(IDENT);
	} else if (lexer.getLookAheadToken() == NUMBER {
		lexer.expect(NUMBER);
	} else {
		throw error;
	}
}
```

</details>

---

<details>
<summary><code>PRINT, LBRACE, RBRACE</code></summary>

> Gegeben die Grammatik G mit
>
> ```
> Terminals = { PRINT, LBRACE, RBRACE}
> NonTerminals = { stmt, stmtList }
> StartSymbol = stmtList
> 
> Produktionen
> P1) stmt : LBRACE stmtlist RBRACE
> P2) stmt : PRINT
> P3) stmtlist: epsilon
> P4) stmtlist: stmt stmtlist
> ```
> Berechne die First Sets für alle Produktionen `P1` bis `P4` und die First Sets der NonTerminal Symbole `stmt` und `stmtlist`

```
FIRST(P1) = { LBRACE }
FIRST(P2) = { PRINT }
FIRST(stmt) = FIRST(P1) u FIRST(P2) = { LBRACE, PRINT }
FIRST(P3) = { epsilon }
FIRST(P4) = FIRST(stmt) = { LBRACE, PRINT }
FIRST(stmtlist) = FIRST(P3) u FIRST(P4) = { LBRACE, PRINT, epsilon }
```

> Berechne die Follow Sets der NonTerminal Symbole `stmt`, `stmtlist`

```
FOLLOW(stmtlist) = { RBRACE, # }
FOLLOW(stmt) = {#} u FIRST(stmtlist) / epsilon u FOLLOW(stmtlist) = { #, LBRACE, PRINT, RBRACE }
```

> Berechne die Selection Sets aller Produktionen `P1` bis `P4`

```
SELECT(P1) = FIRST(P1) = { LBRACE }
SELECT(P2) = FIRST(P2) = { PRINT }
SELECT(P3) = FOLLOW(stmtlist) = { RBRACE, # }
SELECT(P4) = FIRST(P4) = { LBRACE, PRINT }
```

> Zeigen Sie, dass diese Grammatik für einen LL(1) Parser eindeutig ist

`P1` ist disjunkt zu `P2` und `P3` ist disjunkt zu `P4`

> Wir ergänzen die Grammatik um die Produktion
>
> ```
> P5) stmt: otherstmt
> ```
>
> Was darf `FIRST(otherstmt)` nicht enthalten, damit die Grammatik eindeutig bleibt?

`SELECT(P5)` muss disjunkt zu `SELECT(P3)` und `SELECT(P4)` sein. 
Daher darf `FIRST(otherstmt)` [`RBRACE`, `LBRACE`, `PRINT` und *epsilon*] nicht enthalten

</details>

---

## Sonstige

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

> Gegeben ist folgender Java Code:
>
> ```java
> int index = 2;
> int output = 0;
> while (true) {
>     output = output + 1;
>     if (index == 5) {
>         break;
>     }
>     index = index + 1;
> }
> ```
> Dieser kann optimiert werden. Nennen Sie die dafür nötigen Optimierungsschritte. 
> Es genügt die Namen der Optimierungsschritte zu nennen oder die Optimierungsschritte kurz zu beschreiben.

Loop unrolling
Constant folding
Constant propagation
Dead branch elimination

---

# Zwischencodegenerierung

<details>
<summary><code>index</code> Counter to 10</summary>

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

</details>

---

<details>
<summary><code>input</code> if elseif else</summary>

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

</details>

---

<details>
<summary><code>index</code> und <code>output</code> in <code>while</code> mit <code>break</code></summary>

> Gegeben [sei] der folgende Java Codeausschnitt:
>
> ```java
> int index = 2;
> int output = 0;
> while (true) {
>     output = output + 1;
>     if (index == 5) {
>         break;
>     }
>     index = index + 1;
> }
> ```
>
> Schreiben Sie Zwischencode für diesen Codeausschnitt (ohne Optimierung)

```
entry:
alloca i32, %outerIndex
store %outerIndex, 2
alloca i32, %output
store %output, 0
jmp whileHead

whileHead:
load r0, 1
jmp r0, whileBody, exit

whileBody:
load r1, %output
r2 = add r1, 1
store %output
load r3, %index
r4 = cmpless r3, 5
jmp r4 ifThen, ifExit
ifThen:
jmp whileExit
ifExit:
load r5, %index
r6 = add r5, 1
store %index
jmp whileHead
whileExit:
```

</details>

---

<details>
<summary>Neues Java Statement: <code>NUMERIC_IF</code></summary>

> Wir ergänzen die Programmiersprache Java um ein neues Statement NUMERIC_IF:
>
> ```
> NUMERIC_IF expr POSITIVE blockstmt ZERO blockstmt NEGATIVE blockstmt
> NUMERIC_IF verzweigt
>   zum blockstmt nach POSITIVE wenn expr > 0
>   zum blockstmt nach ZERO wenn expr ==0
>   zum blockstmt nach NEGATIVE wenn expr < 0
> ```
> 
> Gegeben der folgende Java Codeausschnitt mit NUMERIC_IF
>
> ```java
> int output = 0;
> NUMERIC_IF (index) POSITIVE {
> output = 4;
> } ZERO {
> output = output + 1;
> } NEGATIVE {
> output = index + 1;
> }
> ```
> 
> Schreiben Sie Zwischencode für diesen Codeausschnitt (ohne Optimierung)

```
start:
alloca %output
store %output, 0
%1 = load %index
%2 = cmp greater %index, 0
jmp %2 positive, zero_or_negative
positive:
store %output, 4
jmp exit
zero_or_negative:
%3 = load %index
%4 = cmp less %index, 0
jmp %4 negative, zero
zero:
%5 = load %output
%6 = add %5, 1
store %output, %6
jmp exit
negative:
%7 = load %index
%8 = add %7, 1
store %output, %8
jmp exit
exit:
```

</details>

---

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

---

> Gegeben ist die Grammatikregel für den Select Operator:
>
> ```
> selectExpr: sumExpr QUESTIONMARK sumExpr COLON sumExpr
> ```
> 
> Das Ergebnis des Selectoperators ist folgendermaßen definiert
> 
> Wenn die erste `sumExpr` `true` ist, liefere den Wert der zweiten `sumExpr` sonst den Wert der dritten `sumExpr` 
>
> Beispiel: 
>
> ```cpp
> 1 ? 4 : 7 // ergibt 4
> 0 ? 2 : 5 + 9 // ergibt 14
> ```
>
> Skizzieren Sie in Pseudocode die Methode `parseSelectExpr()` welche die Eingabe analysiert und eine SelectInstruction für den Interpreter erzeugt.

```java
void parseSelectExpr() {
	parseSumExpr();
	expect(QUESTIONMARK);
	parseSumExpr();
	expect(DOUBLECOLON);
	parseSumExpr();
	Instruction selectInstr = createSelectInstruction();
	this.currentBlock.addInstruction(selectInst);
}
```

> Skizzieren Sie in Pseudocode die Methode `execute(ExecutionEnv)` der Klasse `InstrSelect` um die Selection im Interpreter auszuführen 

```cpp
void InstrSelect::execute(ExecutionEnv env) {
	int op2 = env.popNumber();
	int op1 = env.popNumber();
	int op0 = env.popNumber();
	int result = (op0 != 0) ? op1 : op2;
	env.pushNumber(result);
}
```
