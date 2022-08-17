---
layout: default
---

<h2 id="intro">Introducão</h2>

Neste pequeno ebook eu irei te mostrar como você pode começar a escrever
código na linguagem assembly do 6502. O processador 6502 era
importantíssimo nos anos setenta e oitenta, sendo parte de computadores
como os 
[BBC Micro](http://pt.wikipedia.org/wiki/BBC_Micro),
[Atari 2600](http://pt.wikipedia.org/wiki/Atari_2600),
[Commodore 64](http://pt.wikipedia.org/wiki/Commodore_64),
[Apple II](http://pt.wikipedia.org/wiki/Apple_II), e o [Nintendo Entertainment
System](http://pt.wikipedia.org/wiki/Nintendo_Entertainment_System). O Bender de
Futurama [tem um processador 6502 como cérebro](http://www.transbyte.org/SID/SID-files/Bender_6502.jpg). [Até mesmo
 o Exterminador do Futuro foi programado em um
6502](http://www.pagetable.com/docs/terminator/00-37-23.jpg).

Então por que você iria querer aprender assembly de 6502? é uma linguagem
morta, não é? Bem, latim também o é, e ainda o ensinam. 
[Q.E.D.](http://pt.wikipedia.org/wiki/Q.E.D.)

(Na verdade, fui informado de fontes confiáveis que os processadores 6502
ainda estão sendo produzidos pela) [Western Design Center](http://www.westerndesigncenter.com/wdc/w65c02s-chip.cfm)
e [vendida para amadores](http://www.mouser.co.uk/Search/Refine.aspx?Keyword=65C02), então, claramente
a linguagem assembly de 6502 *não é* uma linguagem morta! Quem iria imaginar?)

Mas, falando sério, eu acho que é valioso ter uma compreensão de assembly.
A linguagem assembly é o menor nível de abstração em computadores, o ponto
final onde o código ainda é legível para humanos. As linguagens assembly
traduzem diretamente para os bytes que serão executados pelo processador
do seu computador. Se você entende como ele funciona, você basicamente
virou um [mago](http://skilldrick.co.uk/2011/04/magic-in-software-development/) dos computadores.

Então por que 6502? Por que não uma linguagem assembly *útil*, como [x86](http://pt.wikipedia.org/wiki/X86)?
Bem, eu não acho que aprender assembly de x86 é útil. Não acho que você
irá *escrever* assembly no dia a dia do seu trabalho.  Isso é puramente
um exercício acadêmico, algo para expandir sua mente e sua forma de
pensar. 6502 foi originalmente escrito em uma outra era, num tempo onde
os programadores escreviam em assembly diretamente, ao invés de usar
linguagens modernas e de alto nível. Então foi projetada para ser escrita
por humanos. Linguagens assembly contemporâneas são projetadas para serem
escritas por compiladores, então deixarêmo-nas para eles. Ademais, 6502
é *divertido*. Ninguém nunca chamou x86 de *divertido*.

<h2 id="first-program">Nosso primeiro programa</h2>

Então, mãos na massa! O que você vê abaixo é um pequeno [assembler e
simulador de 6502 em JavaScript](https://github.com/skilldrick/6502js) que eu adaptei para este livro.
Clique em **Montar** e então em **Executar** para montar e executar
o código em assembly de 6502.

{% include start.html %}
LDA #$01
STA $0200
LDA #$05
STA $0201
LDA #$08
STA $0202
{% include end.html %}

É esperado que a área preta à direita agora tenha três "pixels" coloridos
no canto superior esquerdo. (Se isso não funcionou como esperado, você
provavelmente terá que atualizar seu navegador para algo mais moderno,
como o Chrome ou Firefox.)

Então, o que esse programa está realmente fazendo? Vamos analizá-lo passo
a passo com o debugador. Clique em **Reiniciar**, então marque a caixa
que diz **Debugador** para iniciar o debugador. Clique em **Passo** uma
vez. Se você observou com atenção, terá notado que `A=`
mudou de `$00` para `$01`, e `PC=` mudou de `$0600` para `$0602`.

Quaisquer números prefixados com um `$` na linguagem assembly do 6502
(e, por extensão, nesse livro) estão no formato hexadecimal (hex).
Se você não está familiarizado com números hexadecimais, eu recomendo que
você leia [o artigo da Wikipédia](http://pt.wikipedia.org/wiki/Hexadecimal) sobre o  assunto.
Qualquer coisa prefixada com um `#` é um valor numérico literal. Qualquer
outro número se refere a um lugar na memória.

Equipado com esse conhecimento, você deve ser capaz de observar que a
instrução `LDA #$01` carrega o valor hexadecimal `$01` para o registrador
`A`. Entrarei em mais detalhes sobre registradores na próxima seção.

Clique em **Passo** novamente pare executar a segunda instrução. O
pixel no canto superior esquerdo do display do simulador deve agora
estar branco. Esse simulador usa o intervalo de memória entre `$0200`
e `$05ff` para desenhar pixels no display. Valores de `$00` a `$0f`
representam 16 cores diferentes (`$00` é preto e `$01` é branco),
então guardar o valor `$01` na memória no endereço `$0200` irá desenhar
o pixel branco no canto superior esquerdo. Isso é bem mais simples
quando comparado ao processo de processamento gráfico de computadores
reais, mas é o suficiente por agora.

Então a instrução `STA $0200` armazena o valor do registrador `A` para
o endereço `$0200` na memória. Clique em **Passo** mais quatro vezes para
executar o resto das instruções, se atentando às mudanças no registrador
`A`.

### Exercícios ###

1. Tente alterar a cor dos três pixels.
2. Mude um dos pixels para que ele seja mostrado no canto inferior direito (endereço de memória `05ff`).
3. Adicione mais instruções para desenhar pixels adicionais.


<h2 id='registers'>Registradores e flags</h2>

Já demos uma olhadinha na seção de status do processador (a parte com
`A`, `PC`, etc.), mas o que tudo isso significa?

A primeira linha mostra os registradores `A`, `X` e `Y` (O registrador
`A` é frequentemente chamado de "acumulador"). Cada registrador comporta
um único byte. A maioria das operações trabalha com o conteúdo desses
registradores.

`SP` é o ponteiro do stack. Não iremos entrar no tópico de stack ainda,
mas basicamente esse registrador é decrementado toda vez que um byte é
"empurrado" para a stack, e incrementado quando um byte é "puxado" da
stack.

`PC` é o Contador do Programa, é como o processador sabe onde no programa
ele está atualmente. É como o número da linha de execução atual. No simulador
em JavaScript o código é montado começando na localização `$0600`, então `PC`
sempre começará lá.

A última seção mostra os flags do processador. Cada flag é um bit, então
todos os sete flags ficam armazenados em um único byte. Os flags são
modificados pelo próprio processador para armazenar informações sobre a
última instrução executada. Falaremos mais disso depois.
[Leia mais sobre registradores e flags aqui.](http://www.obelisk.me.uk/6502/registers.html).


<h2 id='instructions'>Instruções</h2>

Instruções em uma linguagem assembly são como um pequeno conjunto de
funções predefinidas. Todas as instruções levam zero ou um argumento.
Aqui temos um código fonte comentado para introduzir algumas instruções
novas:

{% include start.html %}
LDA #$c0  ; Carrega o valor hexadecimal $c0 para o registrador A.
TAX       ; Transfere o valor do registrador A para X.
INX       ; Incrementa o valor no registrador X.
ADC #$c4  ; Adiciona o valor hexadecimal $c4 para o registrador A.
BRK       ; Quebra o fluxo, o programa termina.
{% include end.html %}

Monte o código, e então ligue o debugador e vá passo a passo pelo código,
atentando aos registradores `A` e `X`. Acontece algo ligeiramente estranho
na linha que diz `ADC #$c4`. Você poderia imaginar que adicionar `$c4` e `$c0`
resultaria em `$184`, mas o processador dá como resultado `$84`. O que aconteceu
aí?

O problema é que `$184` é grande demais para caber em um único byte (o máximo
é `$ff`), e os registradores podem apenas armazenar um byte. Não tem problema,
o processador não é bobo. Se você observar com atenção, terá notado que o flag
de carry mudou para `1` depois dessa operação. É assim que você sabe que o
resultado de uma operação não cabe em um único byte.

No simulador abaixo, **digite** (não copie e cole) o seguinte código:
    LDA #$80
    STA $01
    ADC $01

{% include widget.html %}

Algo importante de se notar aqui é a distinção entre `ADC #$01` e `ADC $01`.
O primeiro adiciona o valor `$01` ao registrador `A`, mas o segundo adiciona
o valor armazenado no endereço de memória `$01` ao registrador `A`.

Monte, marque a caixa que diz **Monitor**, e vá executando as três instruções
passo a passo. O monitor mostra seções da memória, e pode ser útil para
visualizar a execução de programas. `STA $01` armazena o valor do registrador
`A` no endereço de memória `$01` e `ADC $01` adiciona o valor armazenado no
endereço de memória `$01` ao registrador `A`. `$80 + $80` deve igualar `$100`,
mas por ser maior que um byte, o registrador `A` é alterado para `$00` e a flag
de carry é ativada. Além dessa, a flag zero também é ativada. A flag zero é
ativada por todas as instruções onde o resultado é zero.

Uma lista completa das instruções do 6502 está [disponível aqui](http://www.6502.org/tutorials/6502opcodes.html) e
[aqui](http://www.obelisk.me.uk/6502/reference.html) (Eu normalmente uso ambas as páginas pois ambas têm seus pros e contras).
Essas páginas detalham os argumentos de cada instrução, quais registradores
elas usam, e quais flags elas modificam. A referência é sua bíblia.

### Exercícios ###

1. Você já viu a instrução `TAX` e provavelmente pode deduzir o que
   as instruções `TAY`, `TXA` e `TYA` fazem, mas escreva algum código para
testá-las e confirmar seu palpite.
2. Reescreva o primeiro exemplo desta seção de forma que ele utilize
   o registrador `Y` ao invés do registrador `X`.
3. O oposto de `ADC` é `SBC` (Subtrair com carry). Escreva um programa
   que utilize esta instrução.


<h2 id='branching'>Ramificações</h2>

Até agora só fomos capazes de escrever programas básicos sem nenhuma
lógica que se utilize de ramificação. Vamos mudar isso.

A linguagem assembly do 6502 tem diversas funções de ramificação, todas
as quais ramificam o programa dependendo de quais flags do processador
estão ativadas ou não. Neste exemplo estaremos dando uma olhada na
instrução `BNE`: "Ramificar em desigualdade".

{% include start.html %}
  LDX #$08
decrementar:
  DEX
  STX $0200
  CPX #$03
  BNE decrementar
  STX $0201
  BRK
{% include end.html %}

Primeiro carregamos o valor `$08` no registrador `X`. A próxima linha é um
rótulo. Rótulos apenas marcam certos pontos no programa para que possamos
retornar a eles em algum momento. Depois do rótulo, decrementamos `X`,
guardamos ele em `$0200` (O pixel superior esquerdo), e então comparamos
ele com o valor `$03`.
[`CPX`](http://www.obelisk.me.uk/6502/reference.html#CPX) compara o valor no registrador `X` com algum outro valor. Se eles
forem iguais, a flag `Z` é alterada para `1`, caso contrário é alterada
para `0`.

A próxima linha, `BNE decrementar`, irá alterar o ponto de execução do programa
para o rótulo `decrementar` caso a flag `Z` tenha como valor `0` (significando
que os dois valores da comparação `CPX` não são iguais), caso contrário nada
será feito e guardaremos o registrador `X` no endereço `$0201`, e encerramos
o programa.

Nas linguagens assembly, você geralmente fará uso de rótulos com instruções
de ramificação. Porém, quando o programa é montado, o rótulo é convertido
para um único byte que representa um deslocamento relativo (Um número em
bytes que armazena a quantas instruções para frente ou para trás o programa
deve continuar a execução), o que significa que instruções de ramificação
podem pular mais ou menos 127 instruções para frente ou para trás. Isso
significa que elas podem ser usadas apenas para mover o fluxo do programa
através de código local. Para maiores distâncias, você irá precisar usar as
instruções de salto.

### Exercícios ###

1. O oposto de `BNE` é `BEQ`. Tente escrever um programa que faça uso de `BEQ`.
2. `BCC` e `BCS`("Ramificar ao desativar carry" e "Ramificar ao ativar carry")
   são usadas para ramificar de acordo com a flag de carry. Escreva um programa
   que use um desses dois.


<h2 id='addressing'>Modos de endereçamento</h2>

O 6502 usa um barramento de endereço de 16 bits, o que significa que existem
65536 bytes de memória disponíveis para o processador. Lembre-se que um byte
pode ser representado por dois caracteres hexadecimais, então os endereços são
geralmente representados como `$0000 $ffff`. Existem diversas formas de se
referir à estes endereços de memória, detalhados abaixo.

Com todos esses exemplos você pode achar útil usar o monitor de memória
para observar as mudanças na memória. O monitor recebe uma localização
de memória inicial e um número de bytes para monitorar daquele endereço
em diante. Ambos são valores hexadecimais. Por exemplo, para mostrar 16
bytes de memória de `$c000`, digite `c000` e `10` nos campos de
**Início** e **Comprimento**, respectivamente.

### Absoluto: `$c000` ###

Com o endereçamento absoluto, o endereço completo do local na memória é utilizado
como argumento para a instrução. Por exemplo:

    STA $c000 ; Armazena o valor do acumulador na memória no endereço $c000

### Página zero: `$c0` ###

Todas as instruções que suportam o endereçamento absoluto (com exceção
das instruções de salto) também tem a opção de receber um endereço de um único
byte. Esse tipo de endereçamento é chamado de "página zero", com ele, apenas
a primeira página (Os primeiros 256 bytes) de memória é acessível. Isso é
mais rápido, tendo em vista que apenas um byte precisa ser buscado, e ocupa
menos espaço no código montado.

### Página zero,X: `$c0,X` ###

Aqui é onde o endereçamento começa a ficar interessante. Nesse modo, um endereço
na página zero é dado, e então o valor do registrador `X` é adicionado.

This is where addressing gets interesting. In this mode, a zero page address is given, and then the value of the `X` register is added. Here is an example:

    LDX #$01   ;X é $01
    LDA #$aa   ;A é $aa
    STA $a0,X ; Armazena o valor de A no endereço $a1
    INX        ; Incrementa X
    STA $a0,X ; Armazena o valor de A no endereço $a2

Se o resultado da adição é maior que um único byte, o endereço irá retornar. Por exemplo:

    LDX #$05
    STA $ff,X ; Armazena o valor de A no endereço $04

### Página zero,Y: `$c0,Y` ###

Este é equivalente ao modo de endereçamento página zero,X, mas pode apenas
ser usado com as instruções `LDX` e `STX`.

### Absoluto,X e absoluto,Y: `$c000,X` e `$c000,Y` ###

Estas são as versões absolutas do modo de endereçamento página zero,X e
página zero,Y. Por exemplo:

    LDX #$01
    STA $0200,X ; Armazena o valor de A no endereço $0201

Diferentemente do modo página zero,Y, absoluto,Y não pode ser usado
com a instrução `STX`, mas pode ser usado com as instruções
`LDA` e `STA`.

### Imediato: `#$c0` ###

Endereçamento imediato não lida estritamente com endereços de memória, esse
é o modo onde valores literais são usados. Por exemplo, `LDX #$01` carrega
o valor `$01` para o registrador `X`. É bem diferente da instrução página
zero `LDX $01`, que carrega o valor armazenado no endereço de memória $01
para o registrador `X`.

### Relativo: `$c0` (ou rótulo) ###

Endereçamento relativo é usado para instruções de ramificação. Estas
instruções usam um único byte, que é usado para marcar um deslocamento
da próxima instrução.

Monte o código a seguir, e clique no botão **Hexdump** para ver o código montado.

{% include start.html %}
  LDA #$01
  CMP #$02
  BNE naoigual
  STA $22
naoigual:
  BRK
{% include end.html %}

O código hexadecimal deve se parecer com isso:

    a9 01 c9 02 d0 02 85 22 00

`a9` e `c9` são os opcodes do processador para  as instruções
`LDA` e `CMP` em modo de endereçamento imediato, respectivamente.
`01` e `02` são os argumentos dessas instruções. `d0` é o opcode
para a instrução `BNE` e seu argumento é `02`. Isso significa
"avance os próximos dois bytes e continue a execução de lá".
(`85 22`, a versão montada da instrução `STA $22`). Tente alterar
o código de forma que `STA` receba como argumento um endereço
absoluto de dois bytes ao invés de um único byte da página zero
(Por exemplo, mude `STA $22` para `STA $2222`). Remonte o código
e olhe novamente para o hexdump. O argumento para a instrução
`BNE` deve agora ser `03`, pois a instrução para a qual o
processador está avançando está agora a uma distância de
três bytes.

### Implícita ###

Algumas instruções não lidam com endereços de memória
(`INX` é um exemplo, incrementa o registrador `X`). Essas
são instruções que usam o modo de endereçamento implícito, já
que o argumento está implícito pela definição da instrução.

### Indirect: `($c000)` ###

Indirect addressing uses an absolute address to look up another address. The
first address gives the least significant byte of the address, and the
following byte gives the most significant byte. That can be hard to wrap your
head around, so here's an example:

{% include start.html %}
LDA #$01
STA $f0
LDA #$cc
STA $f1
JMP ($00f0) ;dereferences to $cc01
{% include end.html %}

In this example, `$f0` contains the value `$01` and `$f1` contains the value
`$cc`. The instruction `JMP ($f0)` causes the processor to look up the two
bytes at `$f0` and `$f1` (`$01` and `$cc`) and put them together to form the
address `$cc01`, which becomes the new program counter. Assemble and step
through the program above to see what happens. I'll talk more about `JMP` in
the section on [Jumping](#jumping).

### Indexed indirect: `($c0,X)` ###

This one's kinda weird. It's like a cross between zero page,X and indirect.
Basically, you take the zero page address, add the value of the `X` register to
it, then use that to look up a two-byte address. For example:

{% include start.html %}
LDX #$01
LDA #$05
STA $01
LDA #$07
STA $02
LDY #$0a
STY $0705
LDA ($00,X)
{% include end.html %}

Memory locations `$01` and `$02` contain the values `$05` and `$07`
respectively. Think of `($00,X)` as `($00 + X)`. In this case `X` is `$01`, so
this simplifies to `($01)`. From here things proceed like standard indirect
addressing - the two bytes at `$01` and `$02` (`$05` and `$07`) are looked up
to form the address `$0705`.  This is the address that the `Y` register was
stored into in the previous instruction, so the `A` register gets the same
value as `Y`, albeit through a much more circuitous route. You won't see this
much.


### Indirect indexed: `($c0),Y` ###

Indirect indexed is like indexed indirect but less insane. Instead of adding
the `X` register to the address *before* dereferencing, the zero page address
is dereferenced, and the `Y` register is added to the resulting address.

{% include start.html %}
LDY #$01
LDA #$03
STA $01
LDA #$07
STA $02
LDX #$0a
STX $0704
LDA ($01),Y
{% include end.html %}

In this case, `($01)` looks up the two bytes at `$01` and `$02`: `$03` and
`$07`. These form the address `$0703`. The value of the `Y` register is added
to this address to give the final address `$0704`.

### Exercise ###

1. Try to write code snippets that use each of the 6502 addressing modes.
   Remember, you can use the monitor to watch a section of memory.


<h2 id='stack'>The stack</h2>

The stack in a 6502 processor is just like any other stack - values are pushed
onto it and popped ("pulled" in 6502 parlance) off it. The current depth of the
stack is measured by the stack pointer, a special register. The stack lives in
memory between `$0100` and `$01ff`. The stack pointer is initially `$ff`, which
points to memory location `$01ff`. When a byte is pushed onto the stack, the
stack pointer becomes `$fe`, or memory location `$01fe`, and so on.

Two of the stack instructions are `PHA` and `PLA`, "push accumulator" and "pull
accumulator". Below is an example of these two in action.

{% include start.html %}
  LDX #$00
  LDY #$00
firstloop:
  TXA
  STA $0200,Y
  PHA
  INX
  INY
  CPY #$10
  BNE firstloop ;loop until Y is $10
secondloop:
  PLA
  STA $0200,Y
  INY
  CPY #$20      ;loop until Y is $20
  BNE secondloop
{% include end.html %}

`X` holds the pixel colour, and `Y` holds the position of the current pixel.
The first loop draws the current colour as a pixel (via the `A` register),
pushes the colour to the stack, then increments the colour and position.  The
second loop pops the stack, draws the popped colour as a pixel, then increments
the position. As should be expected, this creates a mirrored pattern.


<h2 id='jumping'>Jumping</h2>

Jumping is like branching with two main differences. First, jumps are not
conditionally executed, and second, they take a two-byte absolute address. For
small programs, this second detail isn't very important, as you'll mostly be
using labels, and the assembler works out the correct memory location from the
label. For larger programs though, jumping is the only way to move from one
section of the code to another.

### JMP ###

`JMP` is an unconditional jump. Here's a really simple example to show it in action:

{% include start.html %}
  LDA #$03
  JMP there
  BRK
  BRK
  BRK
there:
  STA $0200
{% include end.html %}


### JSR/RTS ###

`JSR` and `RTS` ("jump to subroutine" and "return from subroutine") are a
dynamic duo that you'll usually see used together. `JSR` is used to jump from
the current location to another part of the code. `RTS` returns to the previous
position. This is basically like calling a function and returning.

The processor knows where to return to because `JSR` pushes the address minus
one of the next instruction onto the stack before jumping to the given
location. `RTS` pops this location, adds one to it, and jumps to that location.
An example:

{% include start.html %}
  JSR init
  JSR loop
  JSR end

init:
  LDX #$00
  RTS

loop:
  INX
  CPX #$05
  BNE loop
  RTS

end:
  BRK
{% include end.html %}

The first instruction causes execution to jump to the `init` label. This sets
`X`, then returns to the next instruction, `JSR loop`. This jumps to the `loop`
label, which increments `X` until it is equal to `$05`. After that we return to
the next instruction, `JSR end`, which jumps to the end of the file. This
illustrates how `JSR` and `RTS` can be used together to create modular code.


<h2 id='snake'>Creating a game</h2>

Now, let's put all this knowledge to good use, and make a game! We're going to
be making a really simple version of the classic game 'Snake'.

Even though this will be a simple version, the code will be substantially larger
than all the previous examples. We will need to keep track of several memory
locations together for the various aspects of the game. We can still do
the necessary bookkeeping throughout the program ourselves, as before, but
on a larger scale that quickly becomes tedious and can also lead to bugs that
are difficult to spot. Instead we'll now let the assembler do some of the
mundane work for us.

In this assembler, we can define descriptive constants (or symbols) that represent
numbers. The rest of the code can then simply use the constants instead of the
literal number, which immediately makes it obvious what we're dealing with.
You can use letters, digits and underscores in a name.

Here's an example. Note that immediate operands are still prefixed with a `#`.
{% include start.html %}
  define  sysRandom  $fe ; an address
  define  a_dozen    $0c ; a constant
 
  LDA sysRandom  ; equivalent to "LDA $fe"

  LDX #a_dozen   ; equivalent to "LDX #$0c"
{% include end.html %}

The simulator widget below contains the entire source code of the game. I'll
explain how it works in the following sections.

[Willem van der Jagt](https://twitter.com/wkjagt) made a [fully annotated gist
of this source code](https://gist.github.com/wkjagt/9043907), so follow along
with that for more details.

{% include snake.html %}


### Overall structure ###

After the initial block of comments (lines starting with semicolons), the first
two lines are:

    jsr init
    jsr loop

`init` and `loop` are both subroutines. `init` initializes the game state, and
`loop` is the main game loop.

The `loop` subroutine itself just calls a number of subroutines sequentially,
before looping back on itself:

    loop:
      jsr readkeys
      jsr checkCollision
      jsr updateSnake
      jsr drawApple
      jsr drawSnake
      jsr spinwheels
      jmp loop

First, `readkeys` checks to see if one of the direction keys (W, A, S, D) was
pressed, and if so, sets the direction of the snake accordingly. Then,
`checkCollision` checks to see if the snake collided with itself or the apple.
`updateSnake` updates the internal representation of the snake, based on its
direction. Next, the apple and snake are drawn. Finally, `spinWheels` makes the
processor do some busy work, to stop the game from running too quickly. Think
of it like a sleep command. The game keeps running until the snake collides
with the wall or itself.


### Zero page usage ###

The zero page of memory is used to store a number of game state variables, as
noted in the comment block at the top of the game. Everything in `$00`, `$01`
and `$10` upwards is a pair of bytes representing a two-byte memory location
that will be looked up using indirect addressing.  These memory locations will
all be between `$0200` and `$05ff` - the section of memory corresponding to the
simulator display. For example, if `$00` and `$01` contained the values `$01`
and `$02`, they would be referring to the second pixel of the display (
`$0201` - remember, the least significant byte comes first in indirect addressing).

The first two bytes hold the location of the apple. This is updated every time
the snake eats the apple. Byte `$02` contains the current direction. `1` means
up, `2` right, `4` down, and `8` left.  The reasoning behind these numbers will
become clear later.

Finally, byte `$03` contains the current length of the snake, in terms of bytes
in memory (so a length of 4 means 2 pixels).


### Initialization ###

The `init` subroutine defers to two subroutines, `initSnake` and
`generateApplePosition`. `initSnake` sets the snake direction, length, and then
loads the initial memory locations of the snake head and body. The byte pair at
`$10` contains the screen location of the head, the pair at `$12` contains the
location of the single body segment, and `$14` contains the location of the
tail (the tail is the last segment of the body and is drawn in black to keep
the snake moving). This happens in the following code:

    lda #$11
    sta $10
    lda #$10
    sta $12
    lda #$0f
    sta $14
    lda #$04
    sta $11
    sta $13
    sta $15

This loads the value `$11` into the memory location `$10`, the value `$10` into
`$12`, and `$0f` into `$14`. It then loads the value `$04` into `$11`, `$13`
and `$15`. This leads to memory like this:

    0010: 11 04 10 04 0f 04

which represents the indirectly-addressed memory locations `$0411`, `$0410` and
`$040f` (three pixels in the middle of the display). I'm labouring this point,
but it's important to fully grok how indirect addressing works.

The next subroutine, `generateApplePosition`, sets the apple location to a
random position on the display. First, it loads a random byte into the
accumulator (`$fe` is a random number generator in this simulator). This is
stored into `$00`. Next, a different random byte is loaded into the
accumulator, which is then `AND`-ed with the value `$03`. This part requires a
bit of a detour.

The hex value `$03` is represented in binary as `00000011`. The `AND` opcode
performs a bitwise AND of the argument with the accumulator. For example, if
the accumulator contains the binary value `10101010`, then the result of `AND`
with `00000011` will be `00000010`.

The effect of this is to mask out the least significant two bits of the
accumulator, setting the others to zero. This converts a number in the range of
0&ndash;255 to a number in the range of 0&ndash;3.

After this, the value `2` is added to the accumulator, to create a final random
number in the range 2&ndash;5.

The result of this subroutine is to load a random byte into `$00`, and a random
number between 2 and 5 into `$01`. Because the least significant byte comes
first with indirect addressing, this translates into a memory address between
`$0200` and `$05ff`: the exact range used to draw the display.


### The game loop ###

Nearly all games have at their heart a game loop. All game loops have the same
basic form: accept user input, update the game state, and render the game
state. This loop is no different.


#### Reading the input ####

The first subroutine, `readKeys`, takes the job of accepting user input. The
memory location `$ff` holds the ascii code of the most recent key press in this
simulator. The value is loaded into the accumulator, then compared to `$77`
(the hex code for W), `$64` (D), `$73` (S) and `$61` (A). If any of these
comparisons are successful, the program branches to the appropriate section.
Each section (`upKey`, `rightKey`, etc.) first checks to see if the current
direction is the opposite of the new direction. This requires another little detour.

As stated before, the four directions are represented internally by the numbers
1, 2, 4 and 8. Each of these numbers is a power of 2, thus they are represented
by a binary number with a single `1`:

    1 => 0001 (up)
    2 => 0010 (right)
    4 => 0100 (down)
    8 => 1000 (left)

The `BIT` opcode is similar to `AND`, but the calculation is only used to set
the zero flag - the actual result is discarded. The zero flag is set only if the
result of AND-ing the accumulator with argument is zero. When we're looking at
powers of two, the zero flag will only be set if the two numbers are not the
same. For example, `0001 AND 0001` is not zero, but `0001 AND 0010` is zero.

So, looking at `upKey`, if the current direction is down (4), the bit test will
be zero. `BNE` means "branch if the zero flag is clear", so in this case we'll
branch to `illegalMove`, which just returns from the subroutine. Otherwise, the
new direction (1 in this case) is stored in the appropriate memory location.


#### Updating the game state ####

The next subroutine, `checkCollision`, defers to `checkAppleCollision` and
`checkSnakeCollision`. `checkAppleCollision` just checks to see if the two
bytes holding the location of the apple match the two bytes holding the
location of the head. If they do, the length is increased and a new apple
position is generated.

`checkSnakeCollision` loops through the snake's body segments, checking each
byte pair against the head pair. If there is a match, then game over.

After collision detection, we update the snake's location. This is done at a
high level like so: First, move each byte pair of the body up one position in
memory. Second, update the head according to the current direction. Finally, if
the head is out of bounds, handle it as a collision. I'll illustrate this with
some ascii art. Each pair of brackets contains an x,y coordinate rather than a
pair of bytes for simplicity.

      0    1    2    3    4
    Head                 Tail
    
    [1,5][1,4][1,3][1,2][2,2]    Starting position

    [1,5][1,4][1,3][1,2][1,2]    Value of (3) is copied into (4)
    
    [1,5][1,4][1,3][1,3][1,2]    Value of (2) is copied into (3)
    
    [1,5][1,4][1,4][1,3][1,2]    Value of (1) is copied into (2)
    
    [1,5][1,5][1,4][1,3][1,2]    Value of (0) is copied into (1)
    
    [0,5][1,5][1,4][1,3][1,2]    Value of (0) is updated based on direction

At a low level, this subroutine is slightly more complex. First, the length is
loaded into the `X` register, which is then decremented. The snippet below
shows the starting memory for the snake.

    Memory location: $10 $11 $12 $13 $14 $15

    Value:           $11 $04 $10 $04 $0f $04

The length is initialized to `4`, so `X` starts off as `3`. `LDA $10,x` loads the
value of `$13` into `A`, then `STA $12,x` stores this value into `$15`. `X` is
decremented, and we loop. Now `X` is `2`, so we load `$12` and store it into
`$14`. This loops while `X` is positive (`BPL` means "branch if positive").

Once the values have been shifted down the snake, we have to work out what to
do with the head. The direction is first loaded into `A`. `LSR` means "logical
shift right", or "shift all the bits one position to the right". The least
significant bit is shifted into the carry flag, so if the accumulator is `1`,
after `LSR` it is `0`, with the carry flag set.

To test whether the direction is `1`, `2`, `4` or `8`, the code continually
shifts right until the carry is set. One `LSR` means "up", two means "right",
and so on.

The next bit updates the head of the snake depending on the direction. This is
probably the most complicated part of the code, and it's all reliant on how
memory locations map to the screen, so let's look at that in more detail.

You can think of the screen as four horizontal strips of 32 &times; 8 pixels.
These strips map to `$0200-$02ff`, `$0300-$03ff`, `$0400-$04ff` and `$0500-$05ff`.
The first rows of pixels are `$0200-$021f`, `$0220-$023f`, `$0240-$025f`, etc.

As long as you're moving within one of these horizontal strips, things are
simple. For example, to move right, just increment the least significant byte
(e.g. `$0200` becomes `$0201`). To go down, add `$20` (e.g. `$0200` becomes
`$0220`). Left and up are the reverse.

Going between sections is more complicated, as we have to take into account the
most significant byte as well. For example, going down from `$02e1` should lead
to `$0301`. Luckily, this is fairly easy to accomplish. Adding `$20` to `$e1`
results in `$01` and sets the carry bit. If the carry bit was set, we know we
also need to increment the most significant byte.

After a move in each direction, we also need to check to see if the head
would become out of bounds. This is handled differently for each direction. For
left and right, we can check to see if the head has effectively "wrapped
around". Going right from `$021f` by incrementing the least significant byte
would lead to `$0220`, but this is actually jumping from the last pixel of the
first row to the first pixel of the second row. So, every time we move right,
we need to check if the new least significant byte is a multiple of `$20`. This
is done using a bit check against the mask `$1f`. Hopefully the illustration
below will show you how masking out the lowest 5 bits reveals whether a number
is a multiple of `$20` or not.

    $20: 0010 0000
    $40: 0100 0000
    $60: 0110 0000

    $1f: 0001 1111

I won't explain in depth how each of the directions work, but the above
explanation should give you enough to work it out with a bit of study.


#### Rendering the game ####

Because the game state is stored in terms of pixel locations, rendering the
game is very straightforward. The first subroutine, `drawApple`, is extremely
simple. It sets `Y` to zero, loads a random colour into the accumulator, then
stores this value into `($00),y`. `$00` is where the location of the apple is
stored, so `($00),y` dereferences to this memory location. Read the "Indirect
indexed" section in [Addressing modes](#addressing) for more details.

Next comes `drawSnake`. This is pretty simple too - we first undraw the tail
and then draw the head. `X` is set to the length of the snake, so we can index
to the right pixel, and we set `A` to zero then perform the write using the
indexed indirect addressing mode. Then we reload `X` to index to the head, set
`A` to one and store it at `($10,x)`. `$10` stores the two-byte location of
the head, so this draws a white pixel at the current head position. As only
the head and the tail of the snake move, this is enough to keep the snake
moving.

The last subroutine, `spinWheels`, is just there because the game would run too
fast otherwise. All `spinWheels` does is count `X` down from zero until it hits
zero again. The first `dex` wraps, making `X` `#$ff`.
