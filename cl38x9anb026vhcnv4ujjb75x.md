## Java Stream - Domine as listas/coleções de uma vez por todas

## Introdução
No meu ultimo artigo, eu falei da grande dificuldade de encontrar alguém que utilize o **Optional** além do JPA ( [ Chega de NullPointer, comece a utilizar Optional em sua codificação ](https://blog.sassine.dev/chega-de-nullpointer-comece-a-utilizar-optional-em-sua-codificacao) ), o mesmo acontece com o Stream no java, muitos me dizem " eu já usei o forEach, o filter e algumas vezes  o map...", mas o **Stream** vai muito além disso, e também existem outras coisinhas nativas (dependendo da versão do java) que vou mostrar a você como utilizar cada uma delas, para que domine a manipulação de suas coleções/listas e com o máximo de performance de uma vez por todas ✌

## Stream
Em resumo: A interface API do Java incorporou na atualização do **Java 8** uma nova abstração chamada Stream, que permite processar dados de forma declarativa. Ainda mais, as streams permitem aproveitar as arquiteturas de núcleos múltiplos sem ter que programar linhas de código multiprocesso...

Vou listar suas principais funções, como usar e quais suas diferenças e também funções nativas que simplificam algumas delas

**Obs:** Todos os códigos de exemplo vão estar fazendo uso de duas variáveis **list e consumer**, e pra deixarmos código mais objetivo e direto, já vou deixar elas listadas aqui. 
 
```java
var list = List.of("A", "B", "C", "D", "1", "1", "A", "B");
Consumer<Object> consumer = out::print;
``` 

<details><summary markdown="span">Também teremos uma classe **Person** </summary>

```java
private class Person {
		private String name;

		public Person(String name) {
			this.name = name;
		}

		public String getName() {
			return name;
		}

		@Override
		public int hashCode() {
			final int prime = 31;
			int result = 1;
			result = prime * result + getEnclosingInstance().hashCode();
			result = prime * result + ((name == null) ? 0 : name.hashCode());
			return result;
		}

		@Override
		public boolean equals(Object obj) {
			if (this == obj)
				return true;
			if (obj == null)
				return false;
			if (getClass() != obj.getClass())
				return false;
			Person other = (Person) obj;
			if (!getEnclosingInstance().equals(other.getEnclosingInstance()))
				return false;
			if (name == null) {
				if (other.name != null)
					return false;
			} else if (!name.equals(other.name))
				return false;
			return true;
		}

		private StreamUseCase getEnclosingInstance() {
			return StreamUseCase.this;
		}

		@Override
		public String toString() {
			return "Person [name=" + name + "] ";
		}

	}
```
</details>
</br>

Todo conteúdo abordado neste artigo está disponível no Github > [optional-stream-usecase-example/StreamUseCase.java](https://github.com/Sassine/optional-stream-usecase-example/blob/main/src/main/java/com/github/sassine/api/StreamUseCase.java) quiser acompanhar durante a leitura ou até mesmo executar em sua maquina 😉

### forEach
Hoje temos duas opções de usar o **forEach**, a nativa e a do stream que também é a nativa.
Ambas versões irão percorrer a lista e imprimir todos os elementos, não ah diferença alguma neste caso. 

```java
list.forEach(consumer);
list.stream().forEach(consumer);
``` 

Valor de saída:
```CSS
> ABCD11AB
> ABCD11AB
``` 


###  parallelStream
O **parallelStream** é a mesma coisa que o forEach mas desta forma é executada em várias threads e nesta situação a ordem é indefinida. 

em listas pequenas a performance ou talvez a ordem de resultado não sejam alterados, mas quando se trata de listas com milhares de dados imputados, a diferença é gigante!

```java
list.parallelStream().forEach(consumer);
``` 

Valor de saída:
```CSS
> 11BABDAC
``` 


### filter
Podemos utilizar o **filter** para executar apenas a condicional separando por verdadeiro ou então podemos escrever uma regra mais complexa de filtro e determinar uma ação durante a filtragem sem precisar chamar um forEach depois.

mas é preciso chamar alguma função após o filtro para que ela percorra a lista, neste caso chamei o findAny por ser a função mais performática para este caso, vou falar dele mais pra frente. 

```java
list.stream().filter(value -> "B".equals(value)).forEach(consumer);

list.stream().filter(value -> {
	if (value.matches("[0-9]")) {
		// call method/class/function...
		out.print("value matched");
		return true;
	} else {
		// call other method/class/function...
		return false;
	}
}).findAny();
``` 

Valor de saída:
```CSS
> BB
> value matched
``` 

### distinct 
Se estiver no java 8 utilizar o **distinct** é uma ótima alternativa para remover valores e objetos duplicados, mas lembrando que para diferenciar um objeto de outro a função equals deve estar implementada, mas se estiver utilizando uma versão do java mais recente, O **Set.copyOf** é mais pratico e acredito eu, que até mais performático.

```java
list.stream().distinct().forEach(consumer);
		
Set.copyOf(list).forEach(consumer);
	
var lperson= List.of(new Person("Sassine"), new Person("Sassine2"), new Person("Sassine"));
lperson.stream().distinct().forEach(consumer);
		
Set.copyOf(lperson).forEach(consumer);
``` 

Valor de saída:
```CSS
> ABCD1
> 1DCBA
> Person [name=Sassine] Person [name=Sassine2] Person [name=Sessin] 
> Person [name=Sessin] Person [name=Sassine] Person [name=Sassine2] 
```

### map
Utilizamos o **Map **para manipular os valores de entrada podendo resultar em uma nova Lista de Objetos criados com os valores de entrada ou apenas alterar o valor para outro.


```java
list.stream().map(Person::new).forEach(consumer);
		
list.stream().map(Person::new).map(p -> p.getName()).forEach(consumer);
	
list.stream().map(Person::new).map(p -> {
	String name = p.getName();
	if ("1".equals(name)) return "Number";
	else return name;
}).forEach(consumer);
``` 

Valor de saída:
```CSS
> Person [name=A] Person [name=B] Person [name=C] Person [name=D] Person [name=1] Person [name=1] Person [name=A] Person [name=B] 
> ABCD11AB
> ABCDNumberNumberAB
```

### collect 
Utilizamos **Collect **para juntar todo o conjunto de elementos capturado após a operação de "Filter,Distinc,Map..." e transformar em um objeto List, Set, Map...


```java
var collectPerson = list.stream().map(Person::new).collect(Collectors.toList());
collectPerson.forEach(consumer);

var collectPersonAndDistinctUsginSetListType = list.stream().map(Person::new).collect(Collectors.toSet());
collectPersonAndDistinctUsginSetListType.forEach(consumer);

var collectFiltred = list.stream().filter(value -> "B".equals(value)).collect(Collectors.toList());
collectFiltred.forEach(consumer);
		
var collecDistinctValue = list.stream().distinct().collect(Collectors.toList());
collecDistinctValue.forEach(consumer);
``` 

Valor de saída:
```CSS
> Person [name=A] Person [name=B] Person [name=C] Person [name=D] Person [name=1] Person [name=1] Person [name=A] Person [name=B] 
> Person [name=D] Person [name=C] Person [name=B] Person [name=A] Person [name=1] 
> BB
> ABCD1
```

### findAny e findFirst
Quando precisamos recuperar o primeiro valor da lista para darmos sequencia a outra chamada ou qualquer outra coisa, temos os métodos **findFirst **e **findAny**, que podem ser utilizados direto na stream, ou após de uma chamada de "Filter, Map, Distinct...", findAny irá retornar o elemento aleatório buscando a maior performance para encontrar ele, já o findAny irá retornar sempre o primeiro valor da coleção


```java
Optional<String> findAny = list.stream().findAny();
if (findAny.isPresent()) out.print(findAny.get());

Optional<String> findFirst = list.stream().filter("1"::equals).findFirst();
if (findFirst.isPresent()) out.print(findFirst.get());
``` 

Valor de saída:
```CSS
> A
> 1
```

### skip e limit
Com **limit **conseguimos dizer a quantidade de elementos que iremos percorrer da lista, mesmo que o tamanho que definirmos seja maior que a quantidade da lista e com **skip **conseguimos também pular alguns elementos como se fosse um mecanismo de paginação.


```java
list.stream().limit(3).forEach(consumer);

list.stream().skip(3).limit(100).forEach(consumer);	
``` 

Valor de saída:
```CSS
> ABC
> D11AB
```

### anyMatch e count
Quando precisamos validar se um valor existe na lista sem precisar manipular o restante da lista, podemos utilizar o **anyMatch**, e temos também o **count **que nos permite saber quantos valores estão na lista desde o princípio ou também após o uso de "filter,map..." como isso conseguimos validar também se o número do contador for maior que 0 consequentemente tal valor / regra do filter resultou em um objeto  existente na lista. Porem para este caso o anyMatch é muito mais performático que o count e faz mais sentido. 😅

```java
out.println("List contains B value ? " + list.stream().anyMatch("B"::equals));

out.println("List contains how many B ? " + list.stream().filter("B"::equals).count());

out.println("List contains B value by count ? " + (list.stream().filter("B"::equals).count() > 0));
``` 

Valor de saída:
```CSS
> List contains B value ? true
> List contains how many B ? 2
> List contains B value by count ? true
```

## Conclusão 
Sei que durante a leitura você relembrou uma velha implementação que fez, utilizando "for", "if e else", count, interector ..., e pensou "Nossa desse jeito fica bem melhor" 🤦‍♂️ e sei também que teve outras que talvez não tenham surtido "eficácia" olhando para o que implementa hoje, mas fica tranquilo aos poucos vão surgir casos que vai precisar utilizar estas funções, e no primeiro instante que perceber que irá precisar manipular uma lista... corre aqui que com certeza vai ter algo que vai te ajudar. 

**Bonus**:
Se juntarmos o que já sabemos do Optional, e tudo isso aqui quando formos manipular coleções...
conseguimos reduzir a poluição, manutenção, confiabilidade do código e aumentar a performance absurdamente 🚀

**Se quiser dar uma olhada/testar o projeto está em meu github ;)**
 [![MyGithub badge](https://img.shields.io/badge/GHITHUB-Optional_Stream_usecase_example-blueviolet.svg?style=for-the-badge)](https://github.com/Sassine/optional-stream-usecase-example)