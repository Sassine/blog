## Java 8 ao Java 18 em dez minutos 🚀

Vou apresentar as grandes melhorias de cada versão do Java, agora oferece suporte total para lambdas , programação funcional, digite inferência via **var**, coleções imutáveis com construtores simples e mais performáticos e string multi-linhas.... além disso, existem novos recursos  empolgantes, como classes de dados (**record**), switch 2.0 e **sealed** classes.


## Java 8

Programação Funcional 

No Java 8, programação funcional e lambdas foram adicionados como recursos de linguagem.
Os dois paradigmas principais de programação funcional estão valores imutáveis e elevando funções para cidadãos de primeira classe.

Os dados passam por um pipeline de etapas de modificação, onde cada etapa pega alguma entrada e mapeia para uma nova saída, que podem ser usadas com **Streams** e **Optional** .

### Streams 

Para um programa de computador comum, geralmente você precisa trabalhar com uma lista de valores e realizar uma determinada transformação em cada valor . Antes do Java 8, você tinha que usar um for loop para essa transformação, mas a partir de agora, você pode usar **Streams** da seguinte maneira:


```java
Stream.of("Oi", "E ai")
    .map(s -> s + " Sassine")
    .forEach(System.out::println);
> Oi Sassine
> E ai Sassine
``` 

A função map recebe como entrada um lambda, que será aplicado a todos os elementos do fluxo.

Streams pode trabalhar em **Lists, Sets e Maps** (via transformação). 
Graças ao Streams, você pode se livrar de praticamente todos os loops em seu código! top né ?

Depois vou escrever um post explicando melhor streams e editar aqui; ✨

### Optional
Outro problema comum em Java eram as exceções de ponteiro nulo. 
Portanto, Java introduziu Optional –  que envolve uma referência que pode ou não ser nula. A aplicação de atualizações a este opcional pode ser feita de maneira funcional:.


```java
Optional.of(new Random().nextInt(10))
    .filter(i -> i % 2 == 0)
    .map(i -> "número é par: " + i)
    .ifPresent(System.out::println);
> número é par: 6
``` 
No trecho acima, criamos um número aleatório, envolvemos dentro de um objeto opcional e, em seguida, imprimimos o número apenas se for par.


Depois vou escrever um post explicando melhor como utilizar o Optional e editar aqui; ✨

## Java 9

### JShell
Finalmente, temos um **REPL** para Java, e seu nome é JShell ! 😊 Em poucas palavras, JShell permite experimentar trechos de Java sem escrever e compilar uma classe Java completa. Em vez disso, você pode executar um comando por vez e ver o resultado imediatamente. Aqui está um exemplo simples:


```
$ <JDK_HOME>/bin/jshell > System.out.println("Sassine.dev")
> Sassine.dev
``` 

Pessoas familiarizadas com linguagens interpretadas como JavaScript ou Python tiveram o prazer de um REPL por muito tempo, mas até agora, esse recurso estava faltando em Java. JShell permite definir variáveis , mas também entidades mais complexas, como funções multilinhas , classes e executar loops.

Além disso, o JShell oferece suporte ao preenchimento automático, o que é útil se você não souber os métodos exatos oferecidos por uma determinada classe Java


### Fábrica para coleções imutáveis
A **inicialização simples do Lists** está faltando no Java há muito tempo, mas esses tempos acabaram agora. 😅 Anteriormente, você tinha que fazer algo assim:


```
jshell> List<Integer> list = Arrays.asList(1, 2, 3, 4)
list ==> [1, 2, 3, 4]

# Isso agora é simplificado da seguinte forma:

jshell> List<Integer> list = List.of(1, 2, 3, 4)
list  ==> [1, 2, 3, 4]
``` 

Este of(...) método sofisticado **existe para List , Set e Map** . Todos eles criam um objeto imutável em apenas uma linha simples de código.

## Java 10

### var 
Java 10 introduziu a nova palavra-chave var que permite omitir o tipo de uma variável.

```java
var baos = new ByteArrayOutputStream();
``` 

Esse recurso ajuda a reduzir o código clichê e melhorar a legibilidade. 
Porém, há algumas limitações: você só pode usar var dentro dos corpos dos métodos, e o compilador inferirá o tipo em tempo de compilação, então tudo **ainda está estaticamente tipado**.

## Java 11

### Arquivo de fonte única

Anteriormente, quando você escrevia um programa Java simples consistindo em um arquivo, era necessário primeiro compilar o arquivo com javac e depois executá-lo java. 
No Java 11, você pode executar as duas etapas com um comando.

Classe exemplo:

```java
public class TesteCompile {
  public static void main(String[] args) {
    System.out.println("olá mundo");
  }
}
``` 
Executando a classe:

```
java ./TesteCompile.java
> olá mundo
``` 

Para programas iniciais simples ou experimentos que consistem em apenas uma classe Java, esse recurso para iniciar arquivos de código-fonte único tornará sua vida mais fácil.

## Java 12

### Switch 2.0

Aqui está uma demonstração rápida de como a expressão difere da antiga instrução switch.

A antiga instrução switch define o fluxo do programa:

```java
jshell> var i = 2
jshell> String s;
jshell> switch(i) {
   ...>     case 1: s = "um"; break;
   ...>     case 2: s = "dois"; break;
   ...>     case 3: s = "três"; break;
   ...>     default: s = "número desconhecido";
   ...> }
jshell> s
s ==> "dois"
``` 

Agora a nova expressão switch retorna um valor:


```java
jshell > var i = 2 ;
jshell > var x = switch (i) {
   ... >      case 1 ->  "um";
   ... >      case 2 ->  "dois";
   ... >      case 3 ->  "três";
   ... >      default -> "número desconhecido";
   ... > } ;
x ==>  "dois"
``` 

Para resumir, a instrução switch antiga é para o fluxo do programa e a nova expressão switch resolve para um valor.

Observe que essa nova instrução switch é uma espécie de função de mapeamento : há uma entrada (no caso acima i) e há uma saída (aqui x). Na verdade, esse é um recurso de correspondência de padrões que ajuda a tornar o Java mais compatível com os princípios de programação funcional . Uma instrução switch semelhante está disponível no Scala há algum tempo.

Algumas coisas a serem observadas:
- Em vez de pontos duplos, usamos setas ->
- Não há necessidade de break
- O caso padrão pode ser omitido quando todos os casos possíveis são considerados

**Para habilitar este recurso com Java 12, use --enable-preview --source 12**

## Java 13

### Strings multilinhas

Você já teve que definir uma string longa de várias linhas como JSON ou XML? Até agora, você provavelmente esmagaria tudo em uma linha e usaria caracteres de nova linha \n, mas isso torna a String muito mais difícil de ler. Aí vem o Java 13 com strings de várias linhas ! 💪

Se liga só !!


```java
public class TesteCompile { 
  public static void main(String [] args) {
    var s = """
        {
            "nome": "Sassine El-Asmar",
            "idade": "23",
            "stacks": ["backend", "frontend", "mobile"]
        } """;
    System.out.println(s);
  }
}
``` 

Agora, executamos o método principal por meio de inicialização de arquivo único:


```
java --enable-preview --source 13 TesteCompile.java

{
  "nome": "Sassine El-Asmar",
  "idade": "23",
  "stacks": ["backend", "frontend", "mobile"]
}
``` 

A String resultante se estende por várias linhas, as aspas "" são deixadas intactas e até as guias \t são preservadas!


## Java 14

### Classes de dados: record  (Chega de lombok ?)
Agora existem classes de dados em Java! Essas classes são declaradas com a palavra-chave record e têm Getters automáticos, um construtor e o método equals () etc.
Você pode se livrar de um grande pedaço de código clichê! 🙌


```
jshell> record Pessoa(String nome, int idade) {}
|  created record Pessoa

jshell> var x = new Employee("Sassine", 23);
x ==> Pessoa[nome=Sassine, idade=23]

jshell> x.name()
$2 ==> "Sassine"

``` 

Scala tem um recurso semelhante com classes de caso e Kotlin com classes de dados . Em Java, muitos desenvolvedores usaram o Lombok até agora, que oferecia praticamente os recursos que agora inspiravam o records Java 14.
Mais detalhes podem ser encontrados neste artigo do [Baeldung](https://www.baeldung.com/java-record-keyword).


## Instanceof já devolvendo o objeto tipado
Primeiro verificamos se **s é do tipo e String**, em seguida, lançamos novamente para recuperar seu comprimento… né?

Não mais! agora, com o Java 14, o compilador é inteligente o suficiente para inferir o tipo automaticamente após a verificação de instância :


```java
Object obj = new String("Oi");
if (obj instanceof String s) {
  System.out.println("String length: " + s.length());
}
``` 

## Java 15

### Classes seladas - sealed
Com a palavra-chave sealed , você pode restringir quais classes podem estender uma determinada classe ou interface.

Aqui está um exemplo:

```
public sealed interface Fruit permits Apple, Pear {
    String getName();
}

public final class Apple implements Fruit {
    public String getName() { return "Apple"; }
}

public final class Pear implements Fruit {
    public String getName() { return "Pear"; }
}
``` 

Então, como isso nos ajuda? Bem, agora você sabe quantos Fruits são. Este é realmente um passo importante na direção de correspondência de padrões totalmente suportada , onde você pode tratar as classes como enums. 

Esse sealed recurso combina perfeitamente com a nova switch expressão explicada anteriormente.

## Java 16

### Todos preview listados acima do java 14+ liberado definitivamente
Ou seja não precisa mais do ” –enable-preview ” para os itens listados acima .

### API Vector 
A API de vetor fornece um mecanismo para os desenvolvedores tornarem explícito para o compilador que as operações de vetor devem ser usadas. No entanto, isso torna o código mais complicado. Primeiro, é necessário obter uma espécie de vetor. 


```java
static final VectorSpecies<Float> SPECIES = FloatVector.SPECIES_256;

void vectorComputation(float[] a, float[] b, float[] c) {
    for (int i = 0; i < a.length; i += SPECIES.length()) {
        var m = SPECIES.indexInRange(i, a.length);

        // FloatVector va, vb, vc;
        var va = FloatVector.fromArray(SPECIES, a, i, m);
        var vb = FloatVector.fromArray(SPECIES, b, i, m);
        var vc = va.mul(va)
           .add(vb.mul(vb))
           .neg();

        vc.intoArray(c, i, m);
    }
}
``` 


## Java 17

### Correspondência de padrões para switch (preview)

- Expanda a expressividade e a aplicabilidade das expressões switch, permitindo que os padrões apareçam nos rótulos de maiúsculas e minúsculas sem precisar de Instaceof.

- Introduziu dois novos padrões: padrões protegidos e entre parênteses.

Implementação antes do JDK17: 
```java
public String getValue(Object o) {
		String result = "";
		if(o instanceof Integer i) {
			result = "Integer %d".formatted(i);
		} else if(o instanceof Double d) {
			result = "Double %d".formatted(d);
		} else if(o instanceof Long l) {
			result = "Long %d".formatted(l);
		} else if(o instanceof String s) {
			result = "String %d".formatted(s);
		}
		return result;
}
``` 
Vamos refatorar esse método para a nova feature do JDK17: 

```java
public String getValue(Object o) {
		return switch(o) { 
		case Integer i -> "Integer %d".formatted(i);
		case Double d -> "Double %d".formatted(d);
		case Long l -> "Long %d".formatted(l);
		case String s -> "String %d".formatted(is);
		default -> o.toString();
		};
}
``` 

Dê uma olhada e um exemplo utilizando os novos padrões:


```java
public void quantasPortasTem(Veiculo o) {
		switch(o){ 
		case Carro c && (c.getPortas == 2) -> 
		System.out.println("é um carro duas portas");
		case Carro c && (c.getPortas == 4) ->
		System.out.println("é um carro quatro portas");
		default -> System.out.println("não é um carro.");
		};
}
``` 

## Java 18

### UTF-8 por padrão 
A codificação padrão atual pode ser lida em tempo de execução por meio Charset.defaultCharset()da propriedade do sistema "file.encoding". Desde o Java 17 , a propriedade do sistema "native.encoding" pode ser usada para ler a codificação, que – antes do Java 18 – seria a codificação padrão se nenhuma for especificada:

Sendo assim o file.encoding poderia vir como UTF-8 em alguns sistemas, Cp1252 ou default. 

Antes: 

```
Default charset : US-ASCII
file.encoding   : default
native.encoding : UTF-8
``` 

Agora

```
Default charset : UTF-8
file.encoding   : UTF-8
native.encoding : Cp1252
``` 

Agora no java 18 default não é mais reconhecido e **por padrão "file.encoding" é UTF-8**

No Linux e MacOS tanto o java 17 e 18 já resultam em todas as opções como UTF-8

### Snippets para Java API Doc
Até agora, se quiséssemos integrar trechos de código de várias linhas ao JavaDoc, tínhamos que fazer isso de maneira bastante complicada via <'pre>…</pre'> e algumas regrinhas que tinhamos que seguir.

Agora com a tag **@ snippet** conseguimos exibir o trecho do código-fonte completo.

```
/**
 * Escrevendo um arquivo:
 *
 * {@snippet :
 * try (BufferedWriter writer = Files.newBufferedWriter(path)) {
 *   writer.write(text);
 * }
 * }
 */
```

**Integrar trechos de outros arquivos** 

No FileWriter.java , marcaríamos o código da seguinte forma:

```
// @start region="writeFile"
try (BufferedWriter writer = Files.newBufferedWriter(path)) {
  writer.write(text);
}
// @end
``` 
e utilizamos a referencia dele assim: 

```
/**
 * Escrevendo um arquivo:
 *
 * {@snippet file="FileWriter.java" region="writeFile"}
 */
``` 
### fim
Bom, listei as principais funções novas que tivemos entre as versões 8 a 18 😎 
mas claro que existem muitas outras além de depreciações de lib; 

Quiser saber mais acesse a [documentação oficial da Oracle](https://docs.oracle.com/en/java/javase/index.html)
