## Sua primeira Imagem - Spring Native

> INTRODUÇÃO SPRING NATIVE:
> 
>  O projeto Spring Native fornece suporte para empacotar aplicativos Spring como imagens nativas usando GraalVM.
> 
> Em comparação com os executáveis ​​JVM, as imagens nativas têm tempos de inicialização mais rápidos (<100 ms) e menor consumo de memória. No entanto, construir uma imagem nativa requer mais tempo do que uma baseada em JVM.
> 
> O projeto ainda está em beta, mas já suporta a maioria dos módulos do portfólio Spring, incluindo Spring Framework, Spring Boot, Spring Security e Spring Cloud.

Se você ainda não entendeu ou conhece o Spring Native nós já falamos dele em outro post ["Conheça o Spring Native"](https://blog.sassine.dev/conheca-o-spring-native) ✨'

Este artigo irá te mostrar como criar uma aplicação Spring Boot e compilando um executável nativa com Spring Native.

## Comecemos do começo né ?

![Spring1.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1650936309529/X2zMFq_eu.png)

Inicialize uma aplicação Spring Boot com Spring Reactive Web e Spring Native [StartProject](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.4.4.RELEASE&packaging=jar&jvmVersion=11&groupId=dev.sassine&artifactId=spring-native-graalvm&name=spring-native-graalvm&description=Demo%20project%20for%20Spring%20Boot&packageName=dev.sassine.demo%5C&dependencies=native,webflux)

O projeto gerado conterá uma dependência do projeto Spring Native e do plug-in Spring AOT usado para compilar o código-fonte da sua aplicação com execução nativa enquanto melhora a compatibilidade e a performance.

Você pode criar uma imagem nativa usando o conhecido plug-in Spring Boot (Gradle ou Maven) e seu suporte incorporado para Cloud Native Buildpacks. 

A escolha entre imagens JVM e GraalVM é definida pela propriedade **BP_NATIVE_IMAGE **, que vem pré-configurada quando você usa Spring Initialzr.

- Maven: ela vem pré-configurada no arquivo pom.xml
- Gradle: ela vem pré-configurada no arquivo build.gradle


```xml 
<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
				<configuration>
					<image>
						<builder>paketobuildpacks/builder:tiny</builder>
						<env>
							<BP_NATIVE_IMAGE>true</BP_NATIVE_IMAGE>
						</env>
					</image>
				</configuration>
</plugin>
``` 

## Definindo um endpoint REST com Spring WebFlux

Vamos definir um endpoint REST com Spring WebFlux para que possamos testar nossa aplicação.

Na classe **SpringNativeGraalvmApplication **gerada pelo Initializr, você pode adicionar um end-point REST usando uma função router ou em uma classe com anotação @RestController. Iremos com a primeira opção.


```java
package dev.sassine.demo;

import static org.springframework.boot.SpringApplication.run;
import static org.springframework.web.reactive.function.server.RequestPredicates.GET;
import static org.springframework.web.reactive.function.server.RouterFunctions.route;
import static org.springframework.web.reactive.function.server.ServerResponse.ok;
import static reactor.core.publisher.Mono.just;

import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.web.reactive.function.server.RouterFunction;
import org.springframework.web.reactive.function.server.ServerResponse;

@SpringBootApplication
public class SpringNativeGraalvmApplication {

	public static final String RESPONSE_VALUE = "Spring Native ok !";

	public static void main(String[] args) {
		run(SpringNativeGraalvmApplication.class, args);
	}
	
	@Bean
	RouterFunction<ServerResponse> routes() {
		return route(GET("/"), request -> ok().body(just(RESPONSE_VALUE), String.class));
	}

}
``` 

Criaremos uma classe para teste de integração da chamada REST, na classe SpringNativeGraalvmApplication também gerada pelo Initializr.


```java
package dev.sassine.demo;

import static dev.sassine.demo.SpringNativeGraalvmApplication.RESPONSE_VALUE;
import static org.springframework.boot.test.context.SpringBootTest.WebEnvironment.RANDOM_PORT;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.autoconfigure.web.reactive.AutoConfigureWebTestClient;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.web.reactive.context.ReactiveWebApplicationContext;
import org.springframework.test.web.reactive.server.WebTestClient;

@SpringBootTest(webEnvironment = RANDOM_PORT)
@AutoConfigureWebTestClient
class SpringNativeGraalvmApplicationTests {

	private WebTestClient webTestClient;

	@BeforeEach
	void beforeEach(ReactiveWebApplicationContext context) {
		webTestClient = WebTestClient.bindToApplicationContext(context).configureClient().build();
	}

	@Test
	void whenGetBooksThenReturn() {
		webTestClient.get().uri("/").exchange().expectStatus().is2xxSuccessful().expectBody(String.class).isEqualTo(RESPONSE_VALUE);
	}
}
``` 

##  Executando nossa aplicação como JAR

A dependência do Spring Native em seu projeto otimizará o tempo de inicialização do aplicativo e o consumo de memória, graças ao plugin Spring AOT, mesmo quando executado como um JAR.

No seu Terminal, navegue até a pasta raiz do seu projeto e execute o seguinte comando.

Será iniciado mais rápida do que sua versão sem Spring AOT. 

```bash
mvn spring-boot:run
``` 

se tudo der certo, sua aplicação vai estar rodando na 8080, bom vamos fazer o teste...


```shell
curl http://localhost:8080
``` 
o comando CURL deve resultar no seguinte valor "**Spring Native ok !**"

## Criando e executando nossa aplicação como imagem nativa 

Bora construir e executar uma imagem nativa usando o Spring Native com GraalVM.

Construir uma imagem nativa é muito simples com o plugin Spring Boot. **Certifique-se de que o Docker esteja em execução** ([download docker](https://www.docker.com/products/docker-desktop)) e execute o seguinte comando. Esteja ciente de que isso levará alguns minutos, dependendo muito das características da CPU e da memória do computador.


```
mvn spring-boot:build-image
docker run --name spring-native-graalvm -p 8080:8080 spring-native-graalvm:0.0.1-SNAPSHOT
``` 

Aplicações com Spring Native normalmente iniciam com menos de 100 ms ⏳, dependendo da sua máquina.


![start.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1650937087850/O-Gzps9HT.png)

Novamente, se tudo der certo, sua aplicação vai estar rodando na 8080, bom vamos fazer o teste...


```shell
curl http://localhost:8080
``` 
o comando CURL deve resultar no seguinte valor "**Spring Native ok !**"


✅ Parabéns você acabou de criar sua primeira imagem com Spring Native ;) 

Para obter mais informações sobre o projeto Spring Native, você pode consultar a [documentação oficial](https://docs.spring.io/spring-native/docs/current/reference/htmlsingle/).


📦 Este projeto está disponível no [GitHub - Spring Native GraalVM](https://github.com/Sassine/spring-native-graalvm)



