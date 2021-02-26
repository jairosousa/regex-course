# Regex

`\d` qualquer numero

`[]` indica classe de objetos

`?` - zero ou uma vez

`*` - zero ou mais vezes

`+` - uma ou mais vezes
  
`\s` espaço, tab ...

`{n}` - extamente o numero de vezes

`{n,}` - no minimo n vezes

`{n,m}` - no minimo n+1, no maximo m vezes

## Âncora

É forma de encontrar uma posição dentro do texto

`\b` word boundary procura limite de caracteres

Uma âncora não casa caracteres como as classes fazem, e nem definem quantidades. **Âncoras marcam uma posição específica no alvo**, por isso não é possível combiná-las com um `quantifier`.

Existem várias âncoras predefinidas, mas as mais comuns são `^`, e `\b`. Lembrando também que os caracteres `^` e `$` são *meta-chars*.

## Âncoras de início e fim

A ideia de definir uma âncora não só existe para palavras, existem outras que podem ser muito úteis. Há situações onde precisamos avaliar uma string inteira e não só uma parte. Um bom exemplo são as URIs que vemos o tempo todo no desenvolvimento. Que tal avaliar?

Vamos utilizar o caminho do arquivo html que representa o nosso formulário:

```
file:///Users/nico/Downloads/regex/index.html
```

Queremos avaliar a linha inteira e temos que garantir que nada vem antes. Para isso existe a âncora `^`. Igualmente tem uma âncora que avaliar o fim da string: `$`. Sabendo disso vamos definir:

```
^file.+html$
```

Ou seja, tem que começar com `file` e terminar com `html`. Ao colocar algo antes ou depois do alvo, a regex já não aplica mais:

Não pega:

```
xfile:///Users/nico/Downloads/regex/index.html
```

Também não pega:

```
file:///Users/nico/Downloads/regex/index.htmlx
```

Há muito a melhorar nessa regex para sermos mais específicos, e vamos atacar isso no próximo capítulo mas agora é a hora dos exercícios.

### Validar E-Mail

A validação de um email é um dos exemplos clássicos sobre expressões regulares e claro que não pode faltar neste curso.

Seguem alguns emails que devem ser pegos pela regex:

```
donkey.kong@kart.com.br
bowser1@games.info 
super-mario@nintendo.JP
TEAM.donkey-kong@MARIO.kart1.nintendo.com
```

E aqui alguns exemplos do que não pegar:

```
toad@kart...com
wario@kart@nintendo.com
yoshi@nintendo
daisy@nintendo.b
..@email.com
```

Segue uma possível solução (já bastante complexa!):

```
^([\w-]\.?)+@([\w-]+\.)+([A-Za-z]{2,4})+$
```

#### Vamos por partes na explicação:

* a regex usa âncoras no início `^` e fim `$` para garantir o match **inteiro**;

* antes do `@`, temos: `^([\w-]\.?)+`
  * definimos uma classe com word-chars e hífen, seguido por um ponto opcional: `[\w-]\.?`
  * essa classe pode se repetir uma ou mais vezes, então criamos um grupo e + ao final: `([\w-]\.?)+`
* depois do `@`, temos:
  * `([\w-]+\.)+`, que é bastante parecido com o anterior ao `@`, porém com o . obrigatório,
  * `([A-Za-z]{2,4})+$`, que é o final da nossa regex, seleciona o domínio do email, como `br`, `com`, `us`. O mínimo de letras dessa parte final devem ser **2** e no máximo **4**.

Há vários exemplo complexos disponíveis na web, mas lembre-se que a autenticidade de um email só pode ser verificada enviando um email para usuário.















