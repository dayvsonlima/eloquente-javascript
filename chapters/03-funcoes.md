# Funções

> “As pessoas pensam que Ciência da Computação é a arte de gênios. Na realidade é o oposto, são várias pessoas fazendo coisas que dependem uma das outras, como um muro de pequenas pedras.”
> — Donald Knuth

Você já viu valores de funções como `alert`, e como invocá-las. Funções são essenciais na programação JavaScript. O conceito de encapsular uma parte do programa em um valor tem vários usos. É uma ferramenta usada para estruturar aplicações de larga escala, reduzir repetição de código, associar nomes a subprogramas e isolar esses subprogramas uns dos outros.

A aplicação mais óbvia das funções é quando queremos definir novos vocabulários. Criar novas palavras no nosso dia-a-dia geralmente não é uma boa ideia, porém em programação é indispensável.

Um adulto típico tem por volta de 20.000 palavras em seu vocabulário. Apenas algumas linguagens de programação possuem 20.000 conceitos embutidos, sendo que o vocabulário que se tem disponível tende a ser bem definido, e por isso menos flexível do que a linguagem usada pelas pessoas. Por isso, normalmente temos que adicionar conceitos do nosso próprio vocabulário para evitar repetição.

## Definindo Uma Função

Uma definição de função nada mais é do que uma definição normal de uma variável na qual o valor recebido pela variável é uma função. Por exemplo, o código a seguir define uma variável `square` que se refere a uma função que retorna o quadrado do número dado:

```js
var square = function(x) {
  return x * x;
};

console.log(square(12));
// → 144
```

Uma função é criada através de uma expressão que se inicia com a palavra-chave `function`. Funções podem receber uma série de parâmetros (nesse caso, somente `x`) e um “corpo”, contendo as declarações que serão executadas quando a função for invocada. O corpo da função deve estar sempre envolvido por chaves, mesmo quando for formado por apenas uma simples declaração (como no exemplo anterior).

Uma função pode receber múltiplos parâmetros ou até mesmo nenhum. No exemplo a seguir, `makeNoise` não recebe nenhum parâmetro, enquanto `power` recebe dois:

```js
var makeNoise = function() {
  console.log("Pling!");
};

makeNoise();
// → Pling!

var power = function(base, exponent) {
  var result = 1;
  for (var count = 0; count < exponent; count++)
    result *= base;
  return result;
};

console.log(power(2, 10));
// → 1024
```

Algumas funções produzem um valor, como as funções `power` e `square` vistas acima, e outras não, como no exemplo de `makeNoise`, que produz apenas um “efeito colateral”. A declaração `return` é usada para determinar o valor de retorno da função. Quando o controle de execução interpreta essa declaração, ele sai imediatamente do contexto da função atual e disponibiliza o valor retornado para o código que invocou a função. A palavra-chave `return` sem uma expressão após a mesma, irá fazer com que a função retorne `undefined`.

## Parâmetros e Escopos

Os parâmetros de uma função comportam-se como variáveis regulares. Seu valor inicial é informado por quem invocou a função, e não pelo código da função em si.

Uma propriedade importante das funções é que variáveis definidas dentro do corpo delas, incluindo seus parâmetros, são *locais* à própria função. Isso significa, por exemplo, que a variável `result` no exemplo `power` irá ser criada novamente toda vez que a função for invocada, sendo que as diferentes execuções não interferem umas nas outras.

Esse “senso de localidade” das variáveis se aplica somente aos parâmetros e as variáveis que forem declaradas usando a palavra-chave `var` dentro do corpo da função. Variáveis declaradas fora do contexto de alguma função são chamadas de *globais* (não-locais), pois elas são visíveis em qualquer parte da aplicação. É possível acessar variáveis *globais* dentro de qualquer função, contanto que você não tenha declarado uma variável local com o mesmo nome.

O código a seguir demonstra esse conceito. Ele define e executa duas funções em que ambas atribuem um valor a variável `x`. A primeira função `f1` declara a variável como local e então muda apenas seu valor. Já a segunda função `f2` não declara `x` localmente, portanto sua referência a `x` está associada a variável global `x` definida no topo do exemplo:

```js
var x = "outside";

var f1 = function() {
  var x = "inside f1";
};
f1();
console.log(x);
// → outside

var f2 = function() {
  x = "inside f2";
};
f2();
console.log(x);
// → inside f2
```

Esse comportamento ajuda a previnir interferências acidentais entre funções. Se todas as variáveis fosse compartilhadas por toda a aplicação, iria ser muito trabalhoso garantir que o mesmo nome não fosse utilizado em duas situações com propósitos diferentes. E *se* fosse o caso de você reutilizar uma variável com o mesmo nome, você talvez possa se deparar com efeitos estranhos de códigos que alteram o valor da sua variável. Assumindo que variáveis locais existem apenas dentro do contexto da função, a linguagem torna possível ler e entender funções como “pequenos universos”, sem termos que nos preocupar com o código da aplicação inteira de uma só vez.

## Escopo Aninhado

O JavaScript não se distingue apenas pela diferenciação entre variáveis *locais* e *globais*. Funções também podem ser criadas dentro de outras funções, criando vários níveis de “localidades”.

Por exemplo, a função `landscape` possui duas funções `flat` e `mountain` declaradas dentro do seu corpo:

```js
var landscape = function() {
  var result = "";
  var flat = function(size) {
    for (var count = 0; count < size; count++)
      result += "_";
  };
  var mountain = function(size) {
    result += "/";
    for (var count = 0; count < size; count++)
      result += "'";
    result += "\\";
  };

  flat(3);
  mountain(4);
  flat(6);
  mountain(1);
  flat(1);
  return result;
};

console.log(landscape());
// → ___/''''\______/'\_
```

As funções `flat` e `mountain` podem “ver” a variável `result` porque elas estão dentro do mesmo escopo da função que as definiram. Entretanto, elas não conseguem ver a variável `count` uma da outra (somente a sua própria), pois elas estão definidas em escopos diferentes. O ambiente externo a função `landscape` não consegue ver as variáveis definidas dentro de `landscape`.

Em resumo, cada escopo local pode também ver todos os escopos locais que o contém. O conjunto de variáveis visíveis dentro de uma função é determinado pelo local onde aquela função está escrita na aplicação. Todas as variáveis que estejam em blocos *ao redor* de definições de funções são visíveis aos corpos dessas funções e também a aqueles que estão no mesmo nível. Essa abordagem em relação a visibilidade de variáveis é chamada de *escopo léxico*.

Pessoas com experiência em outras linguagens de programação podem talvez esperar que qualquer bloco de código entre chaves produza um novo “ambiente local”. Entretanto, no JavaScript as funções são as únicas coisas que podem criar novos escopos. É também permitido a utilização de “blocos livres”:

```js
var something = 1;
{
  var something = 2;
  // Do stuff with variable something...
}
// Outside of the block again...
```

Entretanto, a variável `something` dentro do bloco faz referência a mesma variável fora do bloco. Na realidade, embora blocos como esse sejam permitidos, eles são úteis somente para agrupar o corpo de uma declaração condicional `if` ou um loop.

Se você acha isso estranho, não se preocupe pois você não está sozinho. A próxima versão do JavaScript vai introduzir a palavra-chave `let`, que funcionará como `var`, mas criará uma variável que é local ao *bloco* que a contém e não a *função* que a contém.

## Funções Como Valores

As variáveis de função normalmente atuam apenas como nomes para um pedaço específico da aplicação. Tais variáveis são definidas uma vez e nunca se alteram. Isso faz com que seja fácil confundir a função com seu próprio nome.

Entretanto, são duas coisas distintas. Um valor de função pode fazer todas as coisas que outros valores podem fazer (você pode usá-lo em qualquer tipo de expressão e não apenas invocá-la). É possível armazenar um valor de função em um novo local, passá-lo como argumento para outra função e por aí vai. Não muito diferente, uma variável que faz referência a uma função continua sendo apenas uma variável regular e pode ser atribuída a um novo valor, como mostra o exemplo abaixo:

```js
var launchMissiles = function(value) {
  missileSystem.launch("now");
};
if (safeMode)
  launchMissiles = function(value) {/* do nothing */};
```

No capítulo 5, nós vamos discutir as coisas maravilhosas que podem ser feitas quando passamos valores de função para outras funções.

## Notação Por Declaração

Existe uma maneira mais simples de expressar “`var square = function…`”. A palavra-chave `function` também pode ser usada no início da declaração, como demonstrado abaixo:

```js
function square(x) {
  return x * x;
}
```

Isso é uma *declaração de função*. Ela define a variável `square` e faz com que ela referencie a função em questão. Até aí tudo bem, porém existe uma pequena diferença nessa maneira de definir uma função.

```js
console.log("The future says:", future());

function future() {
  return "We STILL have no flying cars.";
}
```

O exemplo acima funciona, mesmo sabendo que a função foi definida *após* o código que a executa. Isso ocorre porque as declarações de função não fazem parte do fluxo normal de controle que é executado de cima para baixo. Elas são conceitualmente movidas para o topo do escopo que as contém e podem ser usadas por qualquer código no mesmo escopo. Isso pode ser útil em algumas situações porque nos permite ter liberdade na hora de ordenar o código de uma maneira que seja mais expressiva, sem se preocupar muito com o fato de ter que definir todas as funções antes de usá-las.

O que acontece quando definimos uma declaração de função dentro de um bloco condicional (`if`), ou um laço de repetição? Bom, não faça isso. Diferentes plataformas JavaScript usadas em diferentes navegadores têm tradicionalmente feito coisas diferentes nessas situações, e a última versão basicamente proíbe essa prática. Se você deseja que suas aplicações se comportem de forma consistente, somente use essa forma de definição de função no bloco externo de uma outra função ou programa.

```js
function example() {
  function a() {} // Okay
  if (something) {
    function b() {} // Danger!
  }
}
```

## A Pilha de Chamadas

Será muito útil observamos como o fluxo de controle flui através das execuções das funções. Aqui temos um simples programa fazendo algumas chamadas de funções:

```js
function greet(who) {
  console.log("Hello " + who);
}
greet("Harry");
console.log("Bye");
```

A execução desse programa funciona da seguinte forma: a chamada à função `greet` faz com que o controle pule para o início dessa função (linha 2). Em seguida, é invocado `console.log` (uma função embutida no navegador), que assume o controle, faz seu trabalho e então retorna o controle para a linha 2 novamente. O controle chega ao fim da função `greet` e retorna para o local onde a função foi invocada originalmente (linha 4). Por fim, o controle executa uma nova chamada a `console.log`.

Podemos representar o fluxo de controle esquematicamente assim:

```
top
   greet
        console.log
   greet
top
   console.log
top
```

Devido ao fato de que a função deve retornar ao local onde foi chamada após finalizar a sua execução, o computador precisa se lembrar do contexto no qual a função foi invocada originalmente. Em um dos casos, `console.log` retorna o controle para a função `greet`. No outro caso, ela retorna para o final do programa.

O local onde o computador armazena esse contexto é chamado de *call stack* (pilha de chamadas). Toda vez que uma função é invocada, o contexto atual é colocado no topo dessa “pilha” de contextos. Quando a função finaliza sua execução, o contexto no topo da pilha é removido e utilizado para continuar o fluxo de execução.

Armazenar essa pilha de contextos necessita de espaço na memória do computador. Quando a pilha começa a ficar muito grande, o computador irá reclamar com uma mensagem do tipo “out of stack space” (sem espaço na pilha) ou “too much recursion” (muitas recursões). O código a seguir demonstra esse problema fazendo uma pergunta muito difícil para o computador, que resultará em um ciclo infinito de chamadas entre duas funções. Se o computador tivesse uma pilha de tamanho “infinito”, isso *poderia* ser possível, no entanto, iremos eventualmente chegar ao limite de espaço e “explodir a pilha”.

```js
function chicken() {
  return egg();
}
function egg() {
  return chicken();
}
console.log(chicken() + " came first.");
// → ??
```

## Argumentos Opcionais

O código abaixo é permitido e executa sem problemas:

```js
alert("Hello", "Good Evening", "How do you do?");
``

A função `alert` oficialmente aceita somente um argumento. No entanto, quando você a chama assim, ela não reclama. Ela simplesmente ignora os outros argumentos e lhe mostra "Hello".

O JavaScript é extremamente tolerante com a quantidade de argumentos que você passa para uma função. Se você passar mais argumentos que o necessário, os extras são ignorados. Se você passar menos argumentos, os parâmetros faltantes simplesmente receberão o valor `undefined`.

A desvantagem disso é que possivelmente - e provavelmente - você vai passar um número errado de argumentos de forma acidental para as funções e nada irá alertá-lo sobre isso.

A vantagem é que esse comportamento pode ser usado em funções que aceitam argumentos “opcionais”. Por exemplo, essa nova versão de `power` pode ser chamada tanto com um ou dois argumentos. No caso de ser invocada com apenas um argumento, ela irá assumir o valor 2 para o expoente e a função irá se comportar como `square`.

```js
function power(base, exponent) {
  if (exponent == undefined)
    exponent = 2;
  var result = 1;
  for (var count = 0; count < exponent; count++)
    result *= base;
  return result;
}

console.log(power(4));
// → 16
console.log(power(4, 3));
// → 64
```

No [TODO: adicionar link]próximo capítulo[/TODO], iremos ver uma maneira de acessar a lista que contém todos os argumentos que foram passados para uma função. Isso é útil pois torna possível uma função aceitar qualquer número de argumentos. Por exemplo, `console.log` tira proveito disso, imprimindo todos os valores que foram passados.

```js
console.log("R", 2, "D", 2);
// → R 2 D 2
```

## Closure

A habilidade de tratar funções como valores, combinado com o fato de que variáveis locais são “recriadas” todas as vezes que uma função é invocada, traz à tona uma questão interessante. O que acontece com as variáveis locais quando a função que as criou não está mais ativa?

O código a seguir mostra um exemplo disso. Ele define uma função `wrapValue` que cria uma variável local e retorna uma função que acessa e retorna essa variável.

```js
function wrapValue(n) {
  var localVariable = n;
  return function() { return localVariable; };
}

var wrap1 = wrapValue(1);
var wrap2 = wrapValue(2);
console.log(wrap1());
// → 1
console.log(wrap2());
// → 2
```

Isso é permitido e funciona como você esperaria: a variável ainda pode ser acessada. Várias instâncias da variável podem co-existir ao mesmo tempo, o que é uma boa demonstração do conceito de que variáveis locais são realmente recriadas para cada nova chamada, sendo que cada chamada não interfere nas variáveis locais uma das outras.

A funcionalidade de ser capaz de referenciar uma instância específica de uma variável local após a execução de uma função é chamada *closure*. Uma função que “closes over” (fecha sobre) variáveis locais é chamada de *closure*. Esse comportamento faz com que você não tenha que se preocupar com o tempo de vida das variáveis, como também permite usos criativos de valores de função.

Com uma pequena mudança, podemos transformar o exemplo anterior possibilitando criar funções que multiplicam por um valor arbitrário.

```js
function multiplier(factor) {
  return function(number) {
    return number * factor;
  };
}

var twice = multiplier(2);
console.log(twice(5));
// → 10
```

A variável explícita `localVariable` do exemplo com a função `wrapValue` não é necessária, pois o parâmetro em si já é uma variável local.

Pensar em programas que funcionam dessa forma requer um pouco de prática. Um bom modelo mental é pensar que a palavra-chave `function` “congela” o código que está em seu corpo e o envolve em um pacote (o valor da função). Quando você lê `return function(...) {...}`, pense como se estivesse retornando um manipulador que possibilita executar instruções computacionais que foram congeladas para um uso posterior.

No exemplo, `multiplier` retorna um pedaço de código congelado que fica armazenado na variável `twice`. A última linha do exemplo chama o valor armazenado nessa variável, fazendo com que o código congelado (`return number * factor;`) seja executado. Ele continua tendo acesso à variável `factor` que foi criada na chamada de `multiplier`, e além disso, ele tem acesso ao argumento que foi passado a ele (o valor 5), através do parâmetro `number`.

## Recursão

É perfeitamente possível para uma função chamar a si própria. Uma função que chama a si mesma é denominada *recursiva*. Recursão permite a algumas funções serem escritas de uma forma divertida. Por exemplo, esta implementação alternativa de `power`:

```js

function power (base, exponent) {
	if (exponent == 0)
		return 1;
	else
		return base * power(base, exponent - 1);
}

console.log(power(2, 3));

```

Essa é uma forma muito próxima que os matemáticos definem a exponenciação, e indiscutivelmente define o conceito de uma forma mais elegante que uma variação de loop faz. A função chama a si mesma múltiplas vezes com diferentes argumentos para alcançar a multiplicação repetida.

Temos um problema importante: Em implementações típicas no JavaScript, esta segunda versão é aproximadamente dez vezes mais lenta que a primeira. Rodar sobre um simples loop é muito mais barato que chamar uma função inúmeras vezes. Em cima disso, usando um expoente suficientemente grande para esta função pode fazer com que a pilha transborde.

O dilema da velocidade versus a elegância é interessante. Quase todo programa pode ser feito mais rápido, tornando-o maior e mais complicado. Você pode ver isso como um tipo de disputa entre amigabilidade para homens ou máquinas.

No caso da função `power` anterior, a versão não elegante (loop) é ainda assim simples e fácil de ser lida. Não tem muito sentido trocá-la pela versão recursiva. Muitas vezes, porém, os conceitos que um programa está lidando são tão complexos que dar mais eficiência ao invés de fazer programas mais simples se torna uma escolha atrativa.

A regra básica, que tem sido repetida por muitos programadores e com a qual eu concordo plenamente, é não se preocupar com eficiência até que você saiba com certeza que o programa está muito lento. Quando isso acontecer, encontre quais partes estão gastando maior tempo, e comece a trocar elegância por eficiência nestas partes.

Claro, a regra anterior não significa que vamos ignorar performance completamente. Em muitos casos, como na função `power`, não ganhamos muita simplicidade pela abordagem "elegante". Em outros casos, um programador experiente pode ver imediatamente que uma abordagem simples nunca vai ser rápida o suficiente.

A razão por eu estar salientando isso é que surpreendentemente muitos programadores iniciantes focam inicialmente em eficiência, mesmo nos mais pequenos detalhes. O resultado são programas maiores, mais complicados e as vezes menos corretos, que demoram mais para serem escritos do que equivalentes mais simples e que rodam somente um pouco mais rápidos.

Porém, recursão não é sempre uma alternativa menos eficiente para fazer loops. Alguns problemas são muito mais fáceis de resolver com recursão do que com loops. A maioria destes problemas requerem exploração ou processamento de vários "branches" (ramificações), cada um dos quais pode ramificar-se de novo em mais ramos.

Considere este quebra-cabeça: Iniciando com o número 1 e repetidamente adicionando 5 ou multiplicando por 3, uma infinita quantidade de números pode ser produzida. Como você pode escrever uma função que, dado um número, tente achar a sequência de adições e multiplicações que produzem este número?

Por exemplo, o número 13 pode ser alcançado primeiramente multiplicando por 3 e depois adicionando 5 duas vezes. O número 15 não pode ser alcançado de nenhuma forma.

Aqui uma solução recursiva:

```js

function findSolution (goal) {
	function find (start, history) {
		if (start == goal)
			return history;
		else if (start > goal)
			return null;
		else
			return find(start + 5, "(" + history + " + 5)") ||
				   find(start * 3. "(" + history + " * 3");
	}
	return find(1, "1");
}

console.log(findSolution(24));
// (((1 * 3) + 5) * 3)

```

Note que não é necessário encontrar uma sequência mais curta de operações - ela é satisfeita quando encontra qualquer sequência.

Eu não espero que você entenda como isso funciona imediatamente, mas vamos analisar o exemplo pois é um ótimo exercício para entender o pensamento recursivo.

A função interior `find` faz a recursão real. Ela pega dois argumentos, o número atual e a string que registra como chegamos neste número, e retorna uma string que mostra como chegar no número esperado ou `null`.

Em razão de fazer isso, é realizado três ações. Se o número atual é o número esperado, o histórico atual é uma forma de encontrar o objetivo, então ele é simplesmente retornado. Se o número atual é maior que o esperado, não há sentido em continuar explorando o histórico, desde que ambas as possibilidades farão o número ainda maior. E finalmente, se nós estamos abaixo do objetivo, a função tenta todos os caminhos possíveis que iniciam do número atual, chamando-o duas vezes, uma para ambos os próximos passos permitidos. Se a primeira chamada retorna algo que não é `null`, ela é retornada. De outra forma, a segunda chamada é retornada (independentemente se ela produz uma string ou null).

Como essa simples função produz o efeito que estamos procurando? Vamos ver as chamadas à `find` que são feitas quando buscamos por uma solução para o número 13:

```

find(1, "1")
  find(6, "(1 + 5)")
    find(11, "((1 + 5) + 5)")
      find(16, "(((1 + 5) + 5) + 5)")
        too big
      find(33, "(((1 + 5) + 5) * 3)")
        too big
    find(18, "((1 + 5) * 3)")
      too big
  find(3, "(1 * 3)")
    find(8, "((1 * 3) + 5)")
      find(13, "(((1 * 3) + 5) + 5)")
        found!

```

A indentação sugere a profundidade da pilha de chamadas. A primeira chamada a `find` chama `find` duas vezes, para explorar as soluções que começam com `(1 + 5)` e `(1 * 3)`. A primeira chamada falha para encontrar a solução que começa com `(1 + 5)` - usando recursão, ela explora todas as soluções que produzem um número menor que o número esperado. Então, ela retorna `null`, e o operador `||` causa a chamada que explora `(1 * 3)` e a faz acontecer. Esta teve mais sorte, sua primeira chamada recursiva, através de *outra* chamada recursiva, encontrou o número procurado 13. Então, uma string é retornada, e cada um dos operadores `||` no intermédio da chamada nos mostram a sequência, que é a nossa solução.

## Funções Crescentes

Existem duas formas mais ou menos naturais para as funções serem introduzidas nos programas.

A primeira é que você se encontra escrevendo código muito parecido muitas vezes. Nós queremos evitar isso - ter mais código significa mais espaço para erros acontecerem, e mais material para ser lido por pessoas tentando entender o programa. Então pegamos a funcionalidade repetida, encontramos um bom nome para isso, e a colocamos dentro de uma função.

A segunda forma é que você precisa de alguma funcionalidade que você ainda não escreveu, e isso soa como necessidade de uma função própria. Você vai começar nomeando a função, e então escrever seu corpo. Você pode até escrever primeiro outro pedaço de código que já usa a função, antes de criar a função em si.

A dificuldade de se encontrar um bom nome para uma função é um bom indicativo de quão claro está um conceito o qual você tenta envolver. Vamos ver um exemplo.

Nós queremos escrever um programa que imprime dois números, a quantidade de vacas e galinhas em uma fazenda, com as palavras `Cows` e `Chickens` depois deles, e zeros inseridos antes de ambos os números para que estes sempre sejam números de três dígitos.

```

007 Cows
011 Chickens

```

Bom, isso claramente é uma função com dois argumentos. Vamos codar.

```js

function printFarmInventory(cows, chickens) {
  var cowString = String(cows);
  while (cowString.length < 3)
    cowString = "0" + cowString;
  console.log(cowString + " Cows");
  var chickenString = String(chickens);
  while (chickenString.length < 3)
    chickenString = "0" + chickenString;
  console.log(chickenString + " Chickens");
}
printFarmInventory(7, 11);

```

Adicionando `.length` depois do valor de uma string vai nos fornecer o comprimento desta string. Então, o loop `while` continua colocando zeros na frente do número de strings até que tenha no mínimo três caracteres de comprimento.

Missão cumprida! Mas quando iríamos enviar o código a ele (juntamente com uma fatura pesada é claro), o fazendeiro ligou e disse que começou a criar porcos (pigs), e se poderíamos extender o software para também imprimir `pigs`.

Nós podemos claro. Mas antes de entrar no processo de copiar e colar estas quatro linhas mais uma vez, vamos parar e reconsiderar. Deve existir uma forma melhor. Aqui a primeira tentativa:

```js

function printZeroPaddedWithLabel(number, label) {
  var numberString = String(number);
  while (numberString.length < 3)
    numberString = "0" + numberString;
  console.log(numberString + " " + label);
}

function printFarmInventory(cows, chickens, pigs) {
  printZeroPaddedWithLabel(cows, "Cows");
  printZeroPaddedWithLabel(chickens, "Chickens");
  printZeroPaddedWithLabel(pigs, "Pigs");
}

printFarmInventory(7, 11, 3);

```

Funcionou! Mas este nome, `printZeroPaddedWithLabel` é um pouco estranho. Ele funde três coisas - printing, zero-padding e adding a label - em uma simples função.

Ao invés de ressaltar a parte repetida do programa destacado, vamos tentar escolher outro conceito. Podemos fazer melhor:

```js

function zeroPad(number, width) {
  var string = String(number);
  while (string.length < width)
    string = "0" + string;
  return string;
}

function printFarmInventory(cows, chickens, pigs) {
  console.log(zeroPad(cows, 3) + " Cows");
  console.log(zeroPad(chickens, 3) + " Chickens");
  console.log(zeroPad(pigs, 3) + " Pigs");
}

printFarmInventory(7, 16, 3);

```

`zeroPad` tem um belo e óbvio nome, que torna fácil para qualquer um ler o código e saber o que ele faz. E ele é útil em mais situações do que somente neste programa específico. Por exemplo, você pode usá-lo para imprimir belas tabelas alinhadas com números.

Quão esperta e versátil nossa função é? Nós podemos escrever qualquer coisa desde uma função extremamente simples que apenas formata um número para ter três caracteres de largura, até um complicado sistema de formatação de números fracionários, números negativos, alinhamento de pontos, formatação com diferentes caracteres e por ai vai...

Um princípio útil é não adicionar inteligência a menos que você tenha certeza absoluta que irá precisar. Pode ser tentador escrever "estruturas" gerais para cada pouco de funcionalidade que você se deparar. Resista a isso. Você não terá nenhum trabalho feito, e você vai acabar escrevendo um monte de código que ninguém nunca vai usar.

## Funções e Efeitos Colaterais

Funções podem ser dividas em aquelas que são chamadas pelos seus efeitos colaterais e aquelas que são chamadas pelo seu valor de retorno. (Embora também seja definitivamente possível de ter ambos efeitos colaterais e retorno de um valor.)

A primeira função auxiliar no exemplo da fazenda, `printZeroPaddedWithLabel`, é chamada pelo seu efeito colateral (ela imprimi uma linha). A segunda, `zeroPad`, é chamada pelo seu valor de retorno. Não é coincidência que a segunda é útil em mais situações que a primeira. Funções que criam valores são mais fáceis de ser combinadas em novas formas do que realizando diretamente um efeito colateral.

Uma função *pura* é um tipo específico de *função de produção de valor* que não somente não tem efeitos colaterais, como também não depende de efeitos colaterais de outro código - por exemplo, ela não lê variáveis que são ocasionalmente mudadas por outro código. Uma função pura tem a agradável propriedade que, quando chamada com os mesmos argumentos, ela sempre produz os mesmos valores (e não faz nada mais). Isso a torna fácil de ser pensada - uma chamada a ela pode ser mentalmente substituída por seu resultado, sem alterar o significado do código. Quando você não tem certeza se uma função está funcionando corretamente, você pode testá-la simplesmente chamando-a e sabendo que se ela funciona neste contexto, ela vai funcionar em qualquer contexto. Função impuras devem retornar valores diferentes baseado em vários tipos de valores e têm efeitos colaterais que podem ser difíceis de testar e pensar sobre.

Não há necessidade de se sentir mal ao escrever função impuras, nem começar uma guerra santa e limpar todos os códigos impuros. Efeitos colaterais são úteis também. Não existe uma forma pura de escrever um `console.log`, por exemplo, e o `console.log` é certamente útil. Algumas operações também podem ser mais caras quando feitas sem efeitos colaterais, quando todo os dados envolvidos precisam ser copiados, ao invés de modificados.

## Resumo

Este capítulo ensinou a você como escrever suas próprias funções. A `function keyword`, quando usada como uma expressão, pode criar um valor de função. Quando usada como uma declaração, pode ser usada para declarar uma variável e dar a ela uma função como valor.

```js

// Create a function and immediately call it
(function (a) { console.log(a + 2); })(4);
// → 6

// Declare f to be a function
function f(a, b) {
  return a * b * 3.5;
}

```
Um aspecto chave no entendimento das funções é entender os escopos locais. Parâmetros e variáveis declaradas dentro de uma função são locais a função, recriados todas as vezes que a função é chamada, e não visíveis do lado de fora. Funções declaradas dentro de outras funções têm acesso ao escopo das funções exteriores.

Separando as tarefas diferentes seu programa executa diferentes funções e isso é útil. Você não precisa de repetir a si mesmo várias vezes, e quando alguém tentar ler seu programa, ele poderá ter o mesmo papel que capítulos e seções de um texto normal.

## Exercícios

### Mínimo

O capítulo anterior introduziu a função padrão `Math.min` que retorna seu menor argumento. Nós podemos fazer isso nós mesmos agora. Escreva uma função `min` que recebe dois argumentos e retorna o valor mínimo.

```js
// Seu código aqui.

console.log(min(0, 10));
// → 0
console.log(min(0, -10));
// → -10
```

T> Se você tiver problemas para colocar as chaves e parênteses no lugar certo para ter uma definição de função válida, comece copiando um dos exemplos neste capítulo e modifique-o.
T>
T> Uma função pode conter múltiplas declarações `return`.

### Recursão

Vimos que o `%` (operador resto) pode ser usado para testar se um número é par ou ímpar usando `% 2` para verificar se o número é divisível por dois. Aqui uma outra maneira de verificar se um número inteiro positivo é par ou ímpar:

* Zero é par.

* Um é ímpar.

* Para qualquer outro número N, seu padrão é o mesmo que N - 2.

Defina uma função recursiva `isEven` correspondente a essa descrição. A função deve aceitar um número como parâmetro e retornar um `Boolean`. 

Teste isso com 50 e 75. Veja como se comporta com -1. Por que? Você pode pensar numa forma de consertar isso?

```js
// Your code here.

console.log(isEven(50));
// → true
console.log(isEven(75));
// → false
console.log(isEven(-1));
// → ??
```

T> Sua função vai parecer com a função `find` no exemplo recursivo `findSolution` neste capítulo, com uma cadeia `if/else if/else` que testa qual dos três casos será aplicado. Cada um dos *branches* (ramificações) deverá conter uma declaração `return` ou ser organizada de outra forma para um valor específica para ser retornado.
T>
T> Quando for passado um número negativo, a função vai ser chamada de novo e de novo, passando para si mesma um número cada vez mais negativo, indo sempre mais longe de retornar um resultado. Isso eventualmente vai sair do espaço da memória e abortar.

### Contando feijões

Você pode pegar o caracter *N*, ou letra, de uma string escrevendo `"string".charAt(N)`, similar a como você pega seu tamanho com `"s".length`. O valor retornado vai ser uma `string` contendo somente um caracter (por exemplo, `"b"`). O primeiro caracter tem posição zero, que faz com que o último seja encontrado na posição `string.length - 1`. Em outras palavras, uma string com dois caracteres tem `length` 2, e seus caracteres tem posição `0` e `1`.

Escreva uma função `countBs` que pega um string como como seu único argumento e retorna um número que indica quantos caracteres "B" em caixa alta (*uppercase*) existem na string.

Depois, escreva uma função chamada `countChar` que se comporta como `countBs`, exceto que ela recebe um segundo argumento que indica o caracter que será contado (ao invés de contar somente os caracteres "B"). Reescreva `countBs` para fazer o uso dessa nova função.

```js
// Seu código aqui.

console.log(countBs("BBC"));
// → 2
console.log(countChar("kakkerlak", "k"));
// → 4
```

T> Um loop em sua função vai olhar em cada caracter da string rodando um índice de zero até um a menos que seu tamanho (`< string.length`). Se o caracter na posição atual é o mesmo que a função está procurando, ele adiciona 1 a variável contadora (`counter`). Uma vez que o loop terminou, a variável `counter` pode ser retornada.
T>
T> Certifique-se de criar todas as variáveis usadas na função dentro da própria função usando a palavra-chave `var`.
