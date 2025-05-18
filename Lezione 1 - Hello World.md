Data: 18/05/2025
Video: https://www.youtube.com/watch?v=HjXBXBgfKyk&t=1s

scrivo file *hello_world.c*
```c
#include <stdio.h>

int main(void){
        printf("Hello World\n");
        return 0;
}
```
compilo con cc 
```bash
cc hello_world.c
```

- [i] cc è il collegamento al compilatore predefinito quindi in pratica spesso è un link a gcc. gcc è il compilatore di GNU

con `cc -S` vedo traduzione in assembly (utile per capire i livelli di ottimizzazione).

Il compilatore c può infatti essere chiamato in diversi modi e, a seconda di come noi lo chiamiamo, potrà generare diversi assembly, con gradi di ottimizzazione e prestazione notevolmente diversi!
Ad esempio se uso `cc -02` userò ottimizzazione di livello 2: il compilatore 'si accorge' di cose che può scrivere meglio di me, per cui l'assembly generato sarà diverso, a parità di semantica del programma in c che ho scritto!

es se lancio `cc -02 -S hello_world.c` vedo che l'assembly, oltre ad avere molte meno righe, usa la funzione `puts` invece di `printf`, che è molto più veloce.
Tra l'altro puts aggiunge da sola il newline (mentre con printf lo devo aggiungere io), infatti nell'assembly generato da compilatore ha rimosso la newline.
> Il fatto che un compilatore maturo manipoli l'input se necessario è molto interessante

*Consultare il manuale*
```man <function>``` per consultare il manuale gnu
```man 3 <function>``` per consultare il manuale c

*Vediamo hello_world.c riga per riga*
> in ogni riga succedono molte più cose di ciò che si possa pensare

Le direttive del processore di c iniziano con il `#`. Sono delle righe "a parte" che vengono gestite dal pre-processore, un programma (spesso parte del compilatore) che effettua delle trasformazioni necessarie per il compilatore prima della compilazione.

`include` vuol dire che deve includere un file di libreria, che include tutte le intestazioni che io userò nel mio programma, ad esempio printf.
Queste intestazioni infatti non fanno parte del c stesso (il c in sè include solo poche keyword, tipo return, int, void, if, while...) - `printf` è definita nella libreria standard del c ma non è parte del c stesso.
`.h` sta per 'header' e appunto contiene intestazioni.
I files c possono avere estensione:
* `.c`, sono i programmi effettivi
* `.h`, che è sempre codice sorgente c ma che contiene elementi di contorno che io posso dover includere in diversi programmi .c attraverso il preprocessore

- [I] possiamo includere anche files `.c` in un `.c`!
es scrivo file.c
*file.c*
```c
printf("Hello World\n")
```

e poi lo includo in hello_world.c
```c
#include <stdio.h>

int main(void){
        include "file.c";
        return 0;
}
```

se lo compilo funzionerà esattamente come prima perchè, al momento della precompilazione, il precompilatore spacchetta file.c dentro a hello_world.c.

cosa succede se compilo senza la libreria? SPOILER: ho errore
```
hello_world.c: In function ‘main’:
hello_world.c:2:9: warning: implicit declaration of function ‘printf’ [-Wimplicit-function-declaration]
    2 |         printf("Hello World\n");
      |         ^~~~~~
hello_world.c:1:1: note: include ‘<stdio.h>’ or provide a declaration of ‘printf’
  +++ |+#include <stdio.h>
    1 | int main(void){
hello_world.c:2:9: warning: incompatible implicit declaration of built-in function ‘printf’ [-Wbuiltin-declaration-mismatch]
    2 |         printf("Hello World\n");
      |         ^~~~~~
hello_world.c:2:9: note: include ‘<stdio.h>’ or provide a declaration of ‘printf’
```

se vado però ad includere la definizione di printf (che leggo con `man 3 printf`), il compilatore riesce comunque a far funzionare il programma:
`int printf(const char *format, ...);`
questo è un prototipo di funzione (manca main, le {} ecc) che definisce solo il tipo che verrà ritornato dalla funzione e gli argomenti che accetta in ingresso, ma è quanto basta al compilatore per compilare! infatti il codice così funziona:

```c
int printf(const char *format, ...);
int main(void){
        printf("Hello World\n");
        return 0;
}
```

