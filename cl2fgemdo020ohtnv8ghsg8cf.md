## Conheça o Spring Native

Pois é meus amigos, recentemente ( março de 2021 ), foi lançado o **Spring Native** na versão experimental ou seja… BETA


![bannerAnuncio.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1650935104885/QyLdho4PG.png)

*– tá mas que diabos seria o spring native?*

De forma resumida:
> O Spring Native faz com que os projetos em Spring tenham suporte oficial para compilar aplicativos Spring para imagens nativas com GraalVM. 📦

Quarkus, Micronaut, Helidon… são frameworks java que já possuíam suporte para trabalhar de forma nativa, mas o Spring ainda não.

Com isso o Spring tem ganho de performance, diminuição de tempo de inicialização e uso de memória ao executar uma aplicação. 📈

O Spring Native permite que os desenvolvedores gerem uma imagem de contêiner otimizada com uma camada de sistema operacional mínima e um pequeno executável nativo com apenas os bits necessários da JDK, Spring e as dependências necessárias em um aplicativo.

📌 Exemplo:
Se criarmos uma imagem que tenha o Spring Native, Spring Boot, Spring MVC, Spring Data, Algum apache e a JDK, o start da aplicação é MUITO RÁPIDO.

![result.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1650935172672/JoUP79RkA.png)

Spring Native oferece suporte às linguagens de programação Java e Kotlin.

Situações em que Spring Native pode ser uma boa escolha incluem:
- ✔️Aplicativos de computação sem servidor com [Spring Cloud Function](https://spring.io/projects/spring-cloud-function).
- ✔️Microsserviços Spring.
- ✔️Trabalhar com plataformas Kubernetes, como [VMware Tanzu](https://tanzu.vmware.com/).

🛑Mas calma lá tá? 

> Aplicações com execuções a longo prazo… a JVM ainda faz mais sentido que o uso do Spring Native, pois a compilação da JVM em tempo de execução nestes casos consegue entregar mais performance.

