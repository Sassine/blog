## Chega de NullPointer, comece a utilizar Optional em sua codificação!

## Introdução
Sempre que estou entrevistando um novo candidato eu o questiono sobre o uso ou conhecimento do Optional, e quase sempre ouço a mesma resposta "Já utilizei, quando usei o JPA "😢

📦 Caso queira acompanhar a classe completa enquanto lê o artigo acesse ele no [GitHub - Optional-Stream-usecase-example](https://github.com/Sassine/optional-stream-usecase-example/blob/main/src/main/java/com/github/sassine/api/OptionalUseCase.java)

## Optional
O uso do Optional vai muito além do JPA ele veio como uma solução para ser utilizado para simplificar as validações de objetos nulos, neste post vou mostrar a vocês todas as formas de utilizar ele no seu dia dia e com isso *reduzir em 99.9% as chances de se obter um NullPointer e tornar o seu código mais limpo*.

###  isPresent
Quando manipulamos Optional ele nos entrega uma função que é os **.isPresent()** que é responsável por nos informar se existe um valor ou se é nulo retorna um booleano falso e existir valor será verdadeiro

```java
 private boolean methodExampleReceivesOptional(Optional<String> opt) {
		return opt.isPresent();
 }
```

###  empty
Sempre que tiver um método que o retorno poderá ser nulo invés de retornar null retorne **.empty()**

```java
private void returnEmptyOptional() {
	Optional<String> opt = Optional.empty();
	if (methodExampleReceivesOptional(opt))
		msg(VALUE_FOUND);
	else
		msg(VALUE_NOT_FOUND);
}

// print: "value not found"
``` 

### of
Quando tiver certeza que o valor a ser retornado não será nulo, ou quando você for enviar um valor para um método/função que recebe Optional, você devera criar ele utilizando **.of(Object)**

```java
private void returnOptionalFixValue() {
	var opt = Optional.of("Sassine");
	if (methodExampleReceivesOptional(opt))
		msg(VALUE_FOUND);
	else
		msg(VALUE_NOT_FOUND);
}

// print: "value found"
``` 
Se tentarmos passar um valor direto para o método 
```java
methodExampleReceivesOptional("Sassine");
``` 
não funcionará pois ele não pode receber um valor que não seja Optional<T>

### ofNullable
Neste caso não temos certeza se o valor irá existir ou será nulo, vai depender do valor gerado então utilizamos o **.ofNullable esse cara é quem irá te acompanhar para evitar e tratar todos seus NullPointers agora** 


```java
private void returnDynamicOptional() {
	Random r = new Random();
	var n = r.nextInt((2 - 1) + 1) + 1;
	var opt = Optional.ofNullable(n == 1 ? null : "Sassine");
	if (methodExampleReceivesOptional(opt))
		msg(VALUE_FOUND);
	else
		msg(VALUE_NOT_FOUND);
}

// print: "value found" or "value not found"
``` 

### get
Quando temos a confirmação de que o valor existe com o "ifPresent" podemos recuperar o valor contido no Optional e manipularmos da forma que quisermos utilizando a chamada **.get()**

```java
private void getOptionalValue() {
	Optional<String> valueOpt = Optional.of("Sassine");
	if (methodExampleReceivesOptional(valueOpt))
		msg(valueOpt.get());
	else
		msg(VALUE_NOT_FOUND);
}

// print: Sassine
``` 
### orElse
Com Optional OfNullable se o seu valor for nulo conseguimos falar para ele nos entregar um valor padrão fixado ou até mesmo chamar outra função para recuperar outro valor utilizando **.orElse(Object)** ou **.orElseGet(() -> args())**

```java
	private void returnDefaultValueWhenNull() {
		String ex = null;
		String valueOpt = Optional.ofNullable(ex).orElse("Sassine - Default");
		msg("value found > %s".formatted(valueOpt));
		String valueOpt2 = Optional.ofNullable(ex).orElse(getName());
		msg("value found > %s".formatted(valueOpt2));
		String valueOpt3 = Optional.ofNullable(ex).orElseGet(Person::getName);
		msg("value found > %s".formatted(valueOpt3));
	}

/* 
print1: value found > Sassine - Default
print2: value found > Sassine - Function
print3: value found > Sassine - Class
*/

``` 
### orElseThrow
Conseguimos fazer com que o Optional lance uma Exception conforme a gente definir quando o valor for nulo utilizando **.orElseThrow(() -> new Exception())**

```java
private void returnCustomExecutionIfNull() {
	try {
		String ex = null;
		Optional.ofNullable(ex).orElseThrow(() -> new RuntimeException("value not found - exception"));
	} catch (RuntimeException e) {
		msg(e.getMessage());
	}
}

//print: value not found - exception
``` 
## Optional Java 9+

### ifPresentOrElse
Para simplificar os ifPresent e o get, no java 9 foi disponibilizado o **.ifPresentOrElse((),())** que simplifica a implementação e a tomada de decisão 

```java
private void ifPresentOrElse() {
	String ex = null;
	Optional.ofNullable(ex)
	.ifPresentOrElse(v -> msg(VALUE_FOUND_S.formatted(v)), () -> msg(VALUE_NOT_FOUND));

	Optional.ofNullable("Sassine")
	.ifPresentOrElse(v -> msg(VALUE_FOUND_S.formatted(v)), () -> msg(VALUE_NOT_FOUND));
}

//print: value not found
//print2:  value found > Sassine
``` 
### getValueOr
Com **.or(() -> ...)** se o 1 valor não estiver presente ele executa uma ação ... neste exemplo buscar o valor existente na função getName

```java
private void getValueOr() {
	String ex = null;
	Optional.ofNullable(ex)
	.or(() -> Optional.of(getName()))
	.ifPresent(this::msg);
}

//print: Sassine - Function
``` 
### optionalStream
Optional também permite o uso de **.stream()**, se um valor estiver presente ele retorna o stream caso contrario retorna uma stream vazia!

```java
private void optionalStream() {
	var list = List.of("AB", "CD");
	Optional.ofNullable(list).stream().forEach(l -> {
		l.forEach(this::msg);
	});
	List<String> x = null;
	Optional.ofNullable(x).stream().forEach(l -> {
		l.forEach(this::msg);
	});
}

//print: ABCD
//print2: 
``` 
**Se quiser dar uma olhada/testar o projeto está em meu github ;)**
 [![MyGithub badge](https://img.shields.io/badge/GHITHUB-Optional_Stream_usecase_example-blueviolet.svg?style=for-the-badge)](https://github.com/Sassine/optional-stream-usecase-example)
