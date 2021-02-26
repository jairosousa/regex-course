# Regex

## data extensa: `11 de Abril de 1995`. Que tal definir uma regex para esse padrão textual?

Vamos copiar a data e colocar no nosso formulário. É sempre útil analisar a regex por partes e testar passo a passo. O início é bem simples, temos dois dígitos e já sabemos descrever essa classe:

```
 \d\d
```

No entanto, para ser uma data, isso é muito abrangente e por isso definimos nossa própria classe de dígitos, mais específica:

```
 [0-3]?\d
```

Isso pega os dias com um ou dois dígitos, mas o primeiro dia pode ser apenas 0, 1, 2 ou 3. Dentro da classe até podemos deixar isso mais explicito:

```
 [0123]?\d
```

### Trabalhando com espaços

Depois do dia, vem um espaço e a sílaba `de`. Como poderíamos definir um espaço dentro de uma regex? E se não for o espaço e sim um tab? Felizmente já existe uma classe predefinida, a `\s`. `\s` significa *whitespace*. A definição do `\s` pode variar um pouco entre as implementações, mas normalmente é um atalho para `[ \t\r\n\f]` onde:

* O primeiro caractere é um espaço branco.
* `\t` é um tab.
* `\r` é carriage return.
* `\n` é newline.
* `\f` é form feed.

É muito coisa para se lembrar, então é melhor usar o `\s`. Vamos aplicar isso na nossa regex, já usando um *quantifier*, pois podemos ter mais do que um espaço:

```
 [0123]?\d\s{1,}
```

Nos colchetes colocamos `1`, que significa um ou mais. Novamente existe um atalho, já que isso é muito comum:

```
 [0123]?\d\s+
```

O símbolo `+` é um outro atalho para definir a quantidade e agora já conhecemos todos:

* `?` - zero ou uma vez.
* `*` - zero ou mais vezes.
* `+` - uma ou mais vezes.
* `{n}` - exatamente **n** vezes.
* `{n,}` - no mínimo **n** vezes.
* `{n,m}` - no mínimo **n** vezes, no máximo **m** vezes.

Agora sim podemos continuar com o valor literal de e o mês. O primeiro é muito simples pois basta colocar a palavra seguida por um ou mais *whitespaces*:

```
 [0123]?\d\s+de\s+
```
Dá match em: `11 de`

### Classes de letras

Para descrever o mês, devemos usar uma nova classe de letras, seguem alguns exemplos:

* `[A-Z]` significa de A até Z, sempre maiúscula.
* `[a-z]` significa de a até z, sempre minúscula,
* `[A-Za-z]` significa A-Z ou a-z.
* `[abc]` significa a, b ou c.

Sabendo disso, podemos combinar a classe com um *quantifier*. Vamos começar com uma letra maiúscula ,seguida por uma quantidade de letras minúsculas, adicionando ainda a letra `ç` (do mês março):

```
 [A-Z][a-zç]
```

Também podemos ser mais restritivos ainda, como um mês escrito tem no máximo 8 caracteres (depois da primeira letra), temos:

```
 [0123]?\d\s+de\s+[A-Z][a-zç]{1,8}
```
Dá match em: `21 de Maio`

Continuando, tem mais uma vez a sílaba de, repetindo a expressão anterior:

```
 [0123]?\d\s+de\s+[A-Z][a-zç]{1,8}\s+de\s+
```
Dá match em: `21 de Maio de`

Por fim podemos declarar o ano que é composto por quatro dígitos:

```
 [0123]?\d\s+de\s+[A-Z][a-zç]{1,8}\s+de\s+\d\d\d\d
```
Dá match em: `21 de Maio de 1993`

Daria também para melhorar um pouco mais a expressão, deixando claro que o ano deve começar com 1 ou 2 seguido por 3 dígitos:

```
 [0123]?\d\s+de\s+[A-Z][a-zç]{1,8}\s+de\s+[12]\d{3}
```
Dá match em:` 21 de Maio de 1993`

Por fim, não podemos esquecer de mencionar a classe de word char. Um word char é apresentado com `\w` e é um atalho para `[A-Za-z0-9_]`.

## Selecionando com âncoras

Vamos continuar com nosso exemplo CSV e copiar a segunda linha para nosso formulário:

```
denise teste, 987.654.321-00,28 de Dezembro de 1991,(31)45562712,SCS Qd. 8 Bl. B-50,11,70333-900,Rio Grande
```
Um problema comum para resolver no dia a dia é a procura de uma palavra especifica. Nesse exemplo, vamos tentar achar a preposição `de`. A primeira ideia seria colocar o valor literal `de`. Ao testar, percebemos que a regex pega mais do que o desejado, como `denise` ou `Grande`. Com aquilo que já aprendemos, vamos refinar a regex e colocar um *whitespace* no início e no fim da preposição: `\sde\s`

Agora sim, a regex só encontra as palavras corretas, no entanto também selecionou o espaço no início e fim da preposição.

### Word boundary

O que queremos na verdade é encontrar o início e o fim de uma palavra, que podemos definir pela regex `\b`. `\b` é uma das **âncoras**, chamada de **word boundary**. Uma âncora não seleciona um caractere, ela seleciona apenas uma posição no texto alvo. Ou seja, a âncora `\b` sozinha devolve nada *(zero-length)* mas combinada com um valor literal, ou outra regex, podemos finalmente definir como encontrar a preposição `de`:

```
\bde\b
```

Repare que usamos o `\b` no início e no fim, ou seja, queremos buscar exatamente a preposição `de`. Testando isso no nosso formulário, agora sim só selecionamos apenas a palavra `de`. Vamos testar a âncora em um outro exemplo para deixar bem claro como ela funciona. Abaixo temos uma sequância de letras `a` e queremos selecionar as sequências com três `a` `(a{3})`:

```
aaa aaaa aaa aaaa aaa
```

Vamos testar uma vez sem `\b`, uma vez com `\b` no início, e outra com `\b` no início e fim:

```
a{3}
```

![](img/image_4.png)

```
a{3}\b
```

![](img/image_5.png)

```
\ba{3}\b
```
![](img/image_6.png)


##  Trabalhando com Grupos

Vamos voltar novamente no nosso exemplo CSV e relembrar a regex que extrai a data:

```
[0123]?\d\s+de\s+[A-Z][a-zç]{1,8}\s+de\s+[12]\d{3}
```

Ao executar, a regex devolve a data completa, como planejamos. Agora imagine que queremos sim fazer um *match* da data, mas selecionar apenas o ano. Para tal existem os grupos. Um grupo é definido através de parênteses `()`. Ou seja, basta colocar a parte da regex que define o ano em parênteses:

```
[0123]?\d\s+de\s+[A-Z][a-zç]{1,8}\s+de\s+([12]\d{3})
```
Para ver os grupos dentro do nosso formulário, devemos habilitar o checkbox "Mostra grupos":

![](img/image_7.png)

Dentro do código JavaScript, o método `exec` devolve um array, com o *match* inteiro e os grupos da regex. Esses resultados é que estamos mostrando no formulário. Sabendo disso, podemos definir mais grupos para também selecionar o dia e o mês:

```
([0123]?\d)\s+de\s+([A-Z][a-zç]{1,8})\s+de\s+([12]\d{3})
```

A regex com o alvo `21 de Maio de 1993` devolve, além do match inteiro, os grupos `21`, `Maio` e `1993`.


### Grupos opcionais

Através de um grupo, podemos também definir um conjunto de caracteres como opcional. Já conhecemos o *quantifier* que significa opcional, o `?` (zero ou uma vez). Agora só falta combinar o `?` com um grupo. Por exemplo, podemos deixar a preposição `de` como opcional: `(de\s+)?`, e aplicando isso na regex nas duas preposições:

```
([0123]?\d)\s+(de\s+)?([A-Z][a-zç]{1,8})\s+(de\s+)?([12]\d{3})
```

Essa regex pega datas como: `21 Maio 1993`, `21 Maio de 1993` e `21 de Maio de 1993`

### Non-capturing groups

Agora temos um novo problema: usamos os grupos justamente para selecionar o dia, mês e ano. Com os dois novos grupos, também recebemos as preposições como resposta. Por exemplo, usando o alvo 21 de Maio de 1993, o nosso formulário devolve os grupos:

```
21 ||| de  ||| Maio ||| de  ||| 1993
```

Os caracteres `|||` são apenas o separador dos grupos, utilizado no nosso código JavaScript e não importam na avaliação. Agora, a pergunta é: como podemos usar um grupo que não é devolvido pela regex? Para tal existem os **non-capturing groups!**

Novamente usaremos o simbolo `?`, mas agora no início do grupo junto com os dois pontos, por exemplo para a preposição `de`: `(?:de\s+)?`

Aplicando isso na regex inteira, no alvo `21 de Maio de 1993`:

```
([0123]?\d)\s+(?:de\s+)?([A-Z][a-zç]{1,8})\s+(?:de\s+)?([12]\d{3})
```

Isso devolve os grupos:

```
21 de Maio de 1993 ||| 21 ||| Maio ||| 1993
```

Perfeito, a preposição `de`, que faz parte da regex, não aparece como grupo!

Muito cuidado para não confundir **non-capturing groups** com os *quantifier*. Ambos utilizam o mesmo símbolo (`?`), contudo possuem objetivos diferentes. Relembrando:

* **Non-Capturing group -** `(?:de\s+)` (não deve devolver o grupo formado pela preposição `de` e por um `\s`)

* **Quantifier -** `[a-z]?` (a classe deve ocorrer **zero** ou **uma** vez)

## Expressões gananciosas

Vamos continuar com os grupos, mas ver um novo exemplo. Praticar é tudo com Regex! O nosso alvo agora é uma tag HTML, escolhemos um cabeçalho (`<h1>`) da nossa página e a tarefa é extrair o conteúdo. O alvo é:

```
<h1 class="text-left">Expressões regulares</h1>
```

Mãos à obra para definir a regex!

### Ganancioso ou preguiçoso?

Nossa regex começa com os valores literais `<h1` seguido por qualquer caractere (`.`), uma ou mais vezes. Assim garantimos que todos os atributos da tag serão encontrados. No final colocamos o fechamento da tag (`>`):

```
<h1.+>
```

Ao testar percebemos que a tag inteira foi selecionada e não só a primeira parte:

<center>
    <img src="img/image_9.png">
</center>

Como assim? Nossa regex é gananciosa por padrão e selecionou todos os caracteres até o último `>`. O *meta-char*, que na verdade é ganancioso, é o `+`, igualmente `*` e `{}` são também assim, e sempre selecionam o máximo de caracteres possíveis, se não for configurado diferente. Ou seja, podemos dizer que não queremos "ganância" e sim preguiçoso ou hesitante. Isso se faz, novamente pelo caractere `?`:

```
<h1.+?>
```
Isso faz que a regex só seleciona até o primeiro >:

<center>
<img src="img/image_10.png">
</center>

Para entender melhor, um bom teste pode ser testar a regex: `<h1.{1,10}` gananciosa, e depois a preguiçoso: `<h1.{1,10}?`. A primeira seleciona *10* caracteres depois do `<h1`, a segunda apenas `1` caractere.


Continuando com a elaboração da regex, vamos definir o conteúdo dentro do parágrafo, aproveitando as classes de caracteres já vistas:

```
<h1.+?>([\w\sõãí.]+)
```

Dentro dos colchetes, podemos declarar mais caracteres do alfabeto português, mas para o nosso texto isso já é suficiente. Por fim falta selecionar o fechamento da tag:

```
<h1.+?>([\w\sõãí.]+)</h1>
```
Repare que já usamos um grupo para receber o conteúdo do parágrafo de volta.


## Entendendo quantifiers gananciosos

**Todos os quantifiers são gananciosos por padrão**. Isso significa que eles automaticamente selecionam o máximo de caracteres por padrão.

Lembrando que temos os seguintes quantifiers:

* `?` (zero ou um caractere)
* `+` (um ou mais caracteres)
* `*` (zero ou mais caracteres)
* `{n,m}` (min n, max m caracteres)

Use agora o nosso testador com um alvo simples, por exemplo a palavra alura e com a regex `[a-z]+`:

<center><img src="img/image_11.png"/></center>

A nossa regex seleciona toda a palavra (1 match). Agora deixe o quantifier preguiçoso.

Para deixar a regex (o quantifier) preguiçoso usamos `?`: `[a-z]+?`

Repare que a regex seleciona um caractere por vez, temos **5 matches** no caso do alvo `alura`:

<center><img src="img/image_12.png"/></center>

Teste também o mesmo alvo com a regex:

* Gananciosa: `[a-z]{1,5}`
* Preguiçosa: `[a-z]{1,5}?`

Percebeu a diferença?

## O recurso de backreference

Nossa regex funciona como o esperado, mas agora ela também deve funcionar para uma tag `h2`. Ok, nada mais fácil do que isso, podemos pensar, basta adicionar a tag no inicio e fim, junto com a tag `<h1>`:

```
<(h1|h2).+?>([\w\sõãí.]+)</(h1|h2)>
```

O agrupamento foi necessário para o `|` funcionar, para não interpretar o `|` literalmente. Ao testar, aparentemente a regex funciona, mas ela tem um erro grave. Ela também aceita alvos com `h1` no início e `h2` no final!

A regex pega (`h1` no início, `h2` no final):

```
<h1 id="regex" class="form">Expressões regulares não são tão difíceis.</h2>
```

O que queremos, quando a tag for uma `h1`, no final também deve estar uma `h1`, igualmente para a tag `h2`. Essa dependência podemos definir através de **back-references**, uma **referência** para um texto já encontrado. A sintaxe é simples, usa-se um `\` seguido pelo número do grupo, começando com 1.

Na nossa regex, a tag é definida no primeiro grupo. Ou seja, vamos usar a *back-reference* `\1`:

```
<(h1|h2).+?>([\w\sõãí.]+)<\/\1>
```

## Para saber mais : Tudo que não é!

Na aula vimos como selecionar uma tag específica dentro de um alvo e chegamos juntos a regex abaixo:

```
<h1.+?>([\w\sõãí.]+)</h1>
```
Com ela pegamos a tag `<h1>`, e o grupo devolve o conteúdo da tag. Repare também que deixamos o *quantifier* preguiçoso para não pegar a tag inteira.

Por exemplo a regex `<h1.+>` seleciona o tag inteira:

<center><img src="img/image_13.png"/></center>

Mas se usarmos o quantifier preguiçosamente selecionamos apenas a abertura da tag:

```
<h1.+?>
```
<center><img src="img/image_14.png"/></center>

Há uma alternativa para resolver esse problema de abertura da tag. Podemos definir uma classe de caracteres que seleciona *tudo que não é um* `>`. Essa negação é feita através da meta-char `^`.

Teste o exemplo abaixo sem usar um quantifier preguiçoso:

```
<h1[^>]+>
```
<center><img src="img/image_15.png"/></center>

Repare que usamos a classe em conjunto com o quantifier ganancioso: `[^>]+`

Mesmo assim foi encontrado apenas a abertura da tag pois a nossa classe de caractere **não inclui** a caractere `>`

Essa negação é algo muito comum nas regexes. Há circunstâncias em que é mais fácil definir o que **não queremos** em vez de dizer o que queremos. A negação `^` ajuda nisso. Isso também é a razão da existência de classes como `\W` (com W maiúsculo) e `\D` (com D maiúsculo).

O `\W` é a ***non-word char***, ou seja tudo que não é um ***word char***. `\W` é um atalho para `[^\w]`.

A classe `\D`, por sua vez, é um ***non-digit***, ou seja, `\D` é um atalho para `[^\d]`


Repare também que** não usamos a meta-char** `^` **como âncora** pois aparece dentro de uma classe `[^>]`.

## Para saber mais: Tudo que não é #2

No exercício anterior falamos que há casos onde é mais simples definir o que *não estamos buscando* de que contrário.

Se lembra do exercício onde era preciso analisar uma mensagem "criptografada"?

O nosso alvo era: `Z171PZ7AZ23PZ7819AZ78GZ1AZ99IZ34O`

E a nossa regex especificou que queremos buscar a letra Z com alguns números e depois vem uma letra maiúscula:

```
Z\d+(\w)
```

Repare que nossa regex na verdade devolve caracteres que não queremos `(Z\d+)` e usamos o grupo para realmente especificar que estamos buscando `(\w)`:

<center>
    <img src="img/image_16.png">
</center>


Vamos inverter a lógica e definir o que a regex **NÃO** deveria devolver, ou seja, não estamos interessados na letra `Z` nem no numero `\d`.

Em outras palavras, teste a regex:

```
[^Z\d]
```

A regex só não ficou menor como também o resultado melhorou, nem foi necessário usar um grupo!

<center>
    <img src="img/image_17.png">
</center>

**O que aprendemos?**

Aprendemos que *quantifiers* são gananciosos por padrão e que podemos utilizar um `?` logo após o quantifier, deixando-o preguiçoso. Também aprendemos como podemos referenciar o texto de um grupo dentro da regex, aonde `n` é o número do grupo.