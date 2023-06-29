# Compilephasen

> **` 🔥 HOT `** Phasen angeben, Input, Output, Typische Fehlermeldungen

| Phase                    | Input        | Output                           | Typische Fehlermeldung                   |
| ------------------------ | ------------ | -------------------------------- | ---------------------------------------- |
| Lexikalische Analyse     | Zeichen      | Token                            | Fehlendes `"` am Ende des Stringliterals |
| Syntaktische Analyse     | Token        | AST                              | Fehlende schließende Klammer             |
| Semantische Analyse*     | AST          | AST mit TypeInfo + SymbolTabelle | Variable nicht deklariert                |
| Zwischencodegenerierung  | AST          | Symboltabelle -> Zwischencode    |                                          |
| Optimierung              | Zwischencode | Zwischencode                     |                                          |
| Maschinencodegenerierung | Zwischencode | Ausführbarer Code                |                                          |

---

> Geben Sie für die folgenden Fehlermeldungen an, von welcher Compilephase sie gemeldet werden

| Fehler                                           | Compilephase         |
| ------------------------------------------------ | -------------------- |
| Fehlende schließende Klammer                     | Syntaktische Analyse |
| Fehlendes Hochkomma am Ende eines Stringliterals | Lexikalische Analyse |
| Fehlendes Return in einer Funktion               | Semantische Analyse  |
| Fehlendes Semikolon eines Statements             | Syntaktische Anaylse |

---

> ```java
> int result = i - 7 - 2 * value;
> ```
>
> Skizzieren Sie den AST für diesen Programmausschnitt

**1. Phase**: `int|result|=|i|-|7|-|2|*|value|;`

**2. Phase**:

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

> Hexadezimalzahlen bestehen aus dem Prefix 0x und mindestens eine Ziffer aus 0-9 und A-F.
> Beispiel: 0xA4, 0x00C3
> 
> Beschreiben Sie Hexadezimalzahlen mit einem regulären Ausdruck

```re
0x[0-9A-F]+
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

> Wie können Sie einen kombinierten nicht-deterministischen endlichen Automaten `BC` erstellen, der einen Base64-kodierten String mit nachfolgender Prüfsumme akzeptiert?

Epsilon Übergang von den Endzuständen von B in den Startzustand von C

---

> Gegeben sind zwei endliche Automaten `A` und `B`. Wie lautet der Ansatz, um einen endlichen Automaten `AB` zu erstellen, der alle Worte akzeptiert, die von Automat `A` oder von Automat `B` akzeptiert werden?

Gemeinsamer Startzustand

---

# Grammatik (-analyse)

## Grammatikproduktion umschreiben

Schreiben Sie die Grammatikproduktion `...` in äquivalente Grammatikproduktionen um, die weder `+`, `?`, noch `*`

`a ::= (b)? (c)*`

```
a ::= bOpt cIter;
bOpt ::= b | ɛ;
cIter ::= c cIter | ɛ;
```

---

`a ::= b | c+`

```
a ::= b
a ::= c cIter
cIter ::= c cIter
cIter ::= ɛ
```

---

> Geben Sie das allgemeine Schema für die Linksfaktorisierung an, um die Linksgleichheit von 2 Produktionen zu beseitigen

```
n: alpha beta
n: alpha gamma
zu
n: npre npost
npre: alpha
npost: beta | gamma
```

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

> Gegeben [sind] die folgenden Grammatikproduktionen
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

**`while` Statement**

```
whilestmt: "while" "(" expression ")" "{" stmtlist "}"
stmtlist: statement*
```

---

**`try..catch` Statement**

```
trystmt: try LBRACE stmtlist RBRACE catchlist
catchlist: catch*
catch: CATCH LPAREN exceptionSpecifier RPAREN LBRACE stmtlist RBRACE
stmtlist: statment*
```

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

**`LBRACKET, RBRACKET, COMMA, IDENTIFIER, NUMBER`**

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

---

**CSV (`COMMA, NUMBER, TEXT, LINEBREAK`)**

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

**`PRINT, LBRACE, RBRACE`**

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

---

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

> Code kann optimiert werden. Nennen Sie die dafür nötigen Optimierungsschritte. 
> Es genügt die Namen der Optimierungsschritte zu nennen oder die Optimierungsschritte kurz zu beschreiben.

Loop unrolling
Constant folding
Constant propagation
Dead branch elimination

---

# Zwischencodegenerierung

**`index` Counter to 10**

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
jmp while_head;

while_head:
%1 = load index;
%2 = cmp_less %1, 10;
jmp_cond %2, while_body, after_while;

while_body:
%3 = load index;
%4 = add %1, 1;
store index, %4;
jmp while_head;

after_while:
```

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
