## Chega de RestTemplate, Conheça o WebClient

## Introdução
Estava conversando com um desenvolvedor java sobre o seu projeto, que tinha a necessidade chamar uma API e persistir o retorno dela mas não precisava do seu retorno para seguir com a jornada... existem diversas formas de codificar isso, como isolando a responsabilidade para outro serviço, abrir uma Thread(essa foi a opção que ele tinha escolhido), utilizar um Event interno, entre muitas outras opções.... 

Então perguntei se ele conhecia o **WebClient**, pois ele atende exatamente essa necessidade, e obtive como resposta o seguinte:
> "Sim, realmente ele ia suprir muito bem isso, mas ele **é só para aplicações reativas.**"

Aí que foi onde ele cometeu o erro... o WebClient funciona para ambas aplicações e tipos de requisições, seja ela reativa e não bloqueante como o tradicional bloqueante. 
Tanto que o próprio Spring que até a sua versão 4 recomendava o uso do RestTemplate, agora com a chegada da versão 5 recomenda o uso do WebClient.

Bom, agora que já sabem que o WebClient é o cara, vamos ver como usamos ele? 😎


## WebClient
⚠Não vou explorar muito o lado Flux neste artigo e sim o bloqueante e reativo, mas depois pretendo sim criar um apenas para o lado Flux. ⚠

### Dependência 
Para usar a **WebClient**, devemos ter o módulo spring-boot-starter-webflux importado para o projeto.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
``` 
### Configurando o WebClient
Para criar um WebClient podemos seguir qualquer uma das abordagens a seguir. 

- Builder do próprio Spring em nosso Bean

```java
@Bean
public WebClient consultaCep(WebClient.Builder builder) {
		return builder.baseUrl("https://viacep.com.br/ws/").build();
}
``` 

- Método create sem url base ou com.

```java
var c1 = WebClient.create();

var c2 = WebClient.create("https://viacep.com.br/ws/");
``` 

- Builder próprio </br> (neste aqui estou customizando suas configurações de timeout)

```java
var http = HttpClient.create()
		.option(ChannelOption.CONNECT_TIMEOUT_MILLIS, 10000)
		.doOnConnected(conn -> conn.addHandlerLast(new ReadTimeoutHandler(10)).addHandlerLast(new WriteTimeoutHandler(10)));
		
var connector = new ReactorClientHttpConnector(http.wiretap(true));

var wclient = WebClient
				.builder()
				.baseUrl("http://localhost:8080")
				.clientConnector(connector)
				.defaultHeader(HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE)
				.build();
``` 

### Métodos HTTP e seu Payload de saída
Para enviar uma solicitação, podemos usar seus métodos fluentes pré-criados, method(HttpMethod) como get(), put(), post(), delete() e até patch().

```java
webClient.method(HttpMethod.GET);

webClient.post();
``` 

se a requisição tiver um corpo a ser enviado basta chamar .body e utilizar o Mono.just(Object) ou o Mono.justOrEmpty(Object) / Mono.justOrEmpty(Optional<T>) para converter seu objeto de entrada. 

Recomendo fortemente que **utilize o Mono.justOrEmpty** que ele ira evitar alguma possível quebra durante a conversão por estar Null e olha só que legal né... ele suporta nosso querido **Optional **!

```java
webClient
.post()
.body(Mono.justOrEmpty(Optional.of("17015311")), String.class)
``` 


### Definindo a rota da requisição e seus cabeçalhos
A gente já viu que desde o momento da criação podemos já definir a rota que será consumida ou um trecho dela para podermos complementar depois.

Aqui eu já tenho um webClient configurado com a baseUrl dele como "localhost:8080" e preciso chamar "localhost:8080/cep/{cep}"

Podemos juntar o trecho que falta "/cep/{cep}" utilizando o String formatted para atribuir o valor da minha variável a String e se tornar nossa URI e para adicionar um cabeçalho na requisições é só chamar .header("Key","Value")

```java
var cep = "17015311"
webClient
.method(HttpMethod.GET)
.uri("/cep/%s".formatted(cep))
.header("Content-Type", "application/json")
``` 
Outra forma de fazermos isso é usando o uriBuilder (bem melhor 😜)

```java
var cep = "17015311"
webClient
.get()
.uri(uriBuilder -> uriBuilder.path("/cep/{cep}")
      .queryParam("callback", "content")
      .build(cep))
.header("Content-Type", "application/json")
``` 

### Definindo o que iremos consumir do servidor
Existem dois tipos de consumo no WebClient o retrieve() e o exchange(). 

O **retrieve**() executa diretamente a solicitação HTTP e recupera o corpo da resposta. 

O **exchange**() método retorna ClientResponse com o status da resposta, os cabeçalhos e o corpo.  </br> (também tem o exchangeToMono e exchangeToFlux como sub implementações dele para facilitar o uso) 

no próximo tópico vou mostrar como são usados. 

### Definindo o formato da resposta retornada do servidor.
Depois de escolhermos qual vai ser a forma da nossa reposta precisamos definir qual objeto será a nossa resposta e para esta função temos o **bodyToMono** como o nome já diz a resposta esperada é única, e o **bodyToFlux** tem como resposta um fluxo de partes de resposta.

```java
Mono<CepDTO> flux = consultaCep
				.get()
				.uri(uriBuilder -> uriBuilder.path("/{cep}/json/").build(cep))
				.retrieve()
				.bodyToMono(CepDTO.class)
				.block();
``` 

```java
Flux<CepDTO> flux = consultaCep
				.get()
				.uri(uriBuilder -> uriBuilder.path("/{cep}/json/").build(cep))
				.exchangeToFlux(response -> {
					if (response.statusCode().equals(HttpStatus.OK)) {
						return response.bodyToFlux(CepDTO.class);
					} else {
						return response.createException().flatMapMany(Mono::error);
					}
				});

Mono<List<CepDTO>> fluxToMonoCollection = consultaCep
				.get()
				.uri(uriBuilder -> uriBuilder.path("/{cep}/json/").build(cep))
				.retrieve()
				.bodyToFlux(CepDTO.class)
				.collectList();
``` 

Não esta esperando nenhum conteúdo ? utilize Void.class

```java
.bodyToMono(Void.class);
``` 
Esta esperando uma List de objeto como resposta? utilize .**collectList**();

```java
List<CepDTO> monoList = consultaCep
				.get()
				.uri(uriBuilder -> uriBuilder.path("/{cep}/json/").build(cep))
				.retrieve()
				.bodyToMono(CepDTO.class)
				.collectList();
				.block();
``` 

Observe que os métodos bodyToMono() e bodyToFlux() sempre esperam um corpo de resposta de um determinado tipo de classe. </br> Se o código de status da resposta for 4xx (erro do cliente) ou 5xx (erro do servidor), ou seja, não houver corpo de resposta, esses métodos lançarão **WebClientException**.

### Realizando uma requisição de forma reativa e não bloqueante
Para tornar nossa request reativa precisamos chamar o "subscribe" e com isso conseguimos implementar a nossa logica para manipular a resposta obtida mesmo depois depois do fim da execução do método onde a chamada foi implementada. 

```java
//logica...

webClient
.get()
.uri(uriBuilder -> uriBuilder.path("/{cep}/json/").build(cep))
.retrieve()
.bodyToMono(String.class)
.subscribe(i -> {
	System.out.println("A request retornou agora ;) ");
});

//logica...
return "ok";
```

### Bloqueando a requisição (esperando ela responder)
Para tornar a nossa requisição bloqueante como é no padrão do RestTemplate, feign e jax-rs ... precisamos implementar o **.block** 🙃

vamos pegar de exemplo a chamada reativa que fizemos ali em cima, remover o subscribe e adicionar o trecho .block 

```java
//logica...

return webClient
.get()
.uri(uriBuilder -> uriBuilder.path("/{cep}/json/").build(cep))
.retrieve()
.bodyToMono(String.class)
.block();

```
Desta forma dizemos para nossa aplicação que precisamos aguardar a conclusão desta requisição para seguirmos com o resto da logica implementada ou até mesmo retornar ela em nosso método. ✨

### Tratando Exceções 
Enfim a parte mais importante da nossa implementação, não podíamos deixar de fora a forma de como tratamos as exceções conforme cada status code né.

Pra isso temos 2 formas,  o **onStatus** e o **filter**

- O **onStatus** utilizamos direto em nossa chamada, informamos qual vai ser o status code de nossa validação e em seguida declaramos um response function, convertemos o seu payload para String e usamos "map" assim com é no Java Stream para transformar o valor em uma exceção, aqui no caso utilizei o RuntimeException como nossa exceção.

```java
consultaCep
.get()
.uri(uriBuilder -> uriBuilder.path("/{cep}/json/").build(cep))
.retrieve()
.onStatus(
 HttpStatus.BAD_REQUEST::equals,
 response -> response.bodyToMono(String.class).map(RuntimeException::new)
)
.bodyToMono(CepDTO.class)
.block();


// Alternative 

.onStatus(status -> status.value() == HttpStatus.METHOD_NOT_ALLOWED.value(), 
 response -> Mono.error(new ServiceException("Metodo não autoziado", response.statusCode().value())))
                            
``` 

- Já o **filter** utilizamos ele como handler, no momento da criação do nosso WebClient podemos chamar ele e passarmos um ExchangeFilterFunction pra dentro, mas para criarmos um ExchangeFilterFunction precisamos criar um Mono<ClientResponse> . 

**Mono<ClientResponse>** é um método que vai receber a interface do ClientResponse que será a resposta da nossa requisição e vai passar por nossas validações e já fazer a devidas ações... neste exemplo vou retorna um RuntimeException e sua mensagem será o Body da requisição. 


```java
private Mono<ClientResponse> exchangeFilterResponseProcessor(ClientResponse response) {
		HttpStatus status = response.statusCode();
		if (HttpStatus.INTERNAL_SERVER_ERROR.equals(status)) {
			return response
			.bodyToMono(String.class)
			.flatMap(body -> Mono.error(new RuntimeException(body)));
		}
		return Mono.just(response);
	}
``` 

Agora que temos o nosso método criado, vamos passar para o filter, chamamos o  ExchangeFilterFunction.ofResponseProcessor e passamos o nosso método para dentro e setamos no filter.

```java
WebClient
.builder()
.baseUrl("https://blog.sassine.dev")
.filter(ExchangeFilterFunction.ofResponseProcessor(this::exchangeFilterResponseProcessor))
.build();
``` 

**Bonus**: se estiver utilizando as ultimas versões java, você consegue deixar o seu método de exchangeFilterResponseProcessor bem mais elegante e performático, até por que depois de 5 validações o swtich se torna mais performático que o if else 🚀


```java
private Mono<ClientResponse> exchangeFilterResponseProcessor(ClientResponse response) {
		return switch(response.statusCode()) {
		case INTERNAL_SERVER_ERROR, NOT_FOUND -> response.bodyToMono(String.class).flatMap(body -> Mono.error(new RuntimeException(body)));
		default -> Mono.just(response);
		};
	}
``` 

### Rastremaneto/Log
Assim como o error handler, podemos utilizar o filter para criarmos um filtro de Log em nossas requisições....

Mesma coisa, precisamos criar um ExchangeFilterFunction.ofRequestProcessor porem desta vez conseguimos simplificar a criação dos nossos métodos de request e response, aí depois é só implementarmos nossa logica de logging conforme a necessidade 🤗

```java
private ExchangeFilterFunction logRequest() {
	return ExchangeFilterFunction.ofRequestProcessor(cRequest -> {
		log.info("Request {} {}", cRequest.method(), cRequest.url());
		return Mono.just(cRequest);
	});
}

private ExchangeFilterFunction logResponse() {
	return ExchangeFilterFunction.ofResponseProcessor(cResponse -> {
		log.info("Response status code {} ", cResponse.statusCode());
		return Mono.just(cResponse);
	});
}
``` 

Métodos criados, agora só implementar eles no **filter** do nosso WebClient.

```java
WebClient
.builder()
.baseUrl("https://viacep.com.br/ws/")
.filter(ExchangeFilterFunction.ofResponseProcessor(this::exchangeFilterResponseProcessor))
.filter(logRequest())
.filter(logResponse())
.build();
``` 


## Conclusão 
Acho que agora que conhecemos o WebClient chegamos todos a mesma conclusão, que de fato é bem mais fácil implementar o WebClient, manipular nossas respostas e tratarmos as exceções, rastrear e ainda podemos utilizar ele de forma não bloqueante e reativa se precisarmos. 

Use e abuse desse conhecimento 😎

[![](https://img.shields.io/badge/PROJETO_DISPONIVEL_NO_GHITHUB_>-APRESENTACAO_WebClient_Project-blueviolet.svg?style=for-the-badge)](https://github.com/Sassine/apresentacao-webclient-non-reative-or-reative)