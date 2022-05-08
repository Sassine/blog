## RESTful ou Restbut

**Você conhece o Modelo de Maturidade de Richardson?**

Leonard Richardson propôs um Modelo de Maturidade para avaliar APIs REST que foi usado na Bíblia do REST, o livro REST in Practice, do Jin Webber de 2010 (sim, publicado a 11 anos!).
⠀
Nesse modelo, são propostos 4 níveis de maturidade:

## Nível 0
Definitivamente neste nível não podemos nem chamar de REST.
Neste nível estamos apenas usando o protocolo HTTP, sem nenhuma padronização POX (Plain Old XML).

Identificação de recursos – você usa o padrão URI (IRI) para identificar um recurso. Nesse caso, um recurso é um documento da web.

## Nível 1
Agrupamos as capacidades em Recursos, tornando suas APIs um pouco mais organizadas, mas ainda não é uma RESTful.

Manipulação de recursos por meio dessas representações – você usa o padrão HTTP para descrever a comunicação. Portanto, por exemplo, GET significa que você deseja recuperar dados sobre o recurso identificado pelo URI. Você pode descrever uma operação com um método HTTP e um URI.

## Nível 2 – RESTful
Aqui usamos corretamente os verbos HTTP (ou seja, as APIs usam a Uniform Interface).
Neste ponto, eu já consigo chamar a API de RESTful, apesar de ser difícil encontrar APIs que atingiram completamente os 2 primeiros níveis

Mensagens autodescritivas – você usa tipos MIME padrão e vocabs RDF (padrão) para tornar as mensagens autodescritivas. Assim, o cliente pode encontrar os dados verificando a semântica e não precisa saber a estrutura de dados específica do aplicativo que o serviço usa.

## Nível 3
Finalmente no nível 3 usamos Controles Hypermedia, chegando ao HATEOAS.

Hipermídia como mecanismo de estado do aplicativo (também conhecido como HATEOAS) – Você usa hiperlinks e possivelmente modelos de URI para separar o cliente da estrutura URI específica do aplicativo. Você pode anotar esses hiperlinks com semântica, por exemplo, relações de link da IANA, para que o cliente entenda o que eles significam.


```json 
{
    "links": {
        "self": "https://sassine.dev/artigos",
        "next": "https://sassine.dev/artigos?page[offset]=1",
        "last": "https://sassine.dev/artigos?page[offset]=40"
    },
    "data": [{
        "type": "artigos",
        "id": "4",
        "attributes": {
            "titulo": "REST(ful) ou REST(but)"
        },
        "relationships": {
            "autor": {
                "links": {
                    "self": "https://sassine.dev/articles/4/author",
                    "related": "https://sassine.dev/articles/4/author"
                }
            },
            "comentarios": {
                "links": {
                    "self": "https://sassine.dev/articles/4/comments",
                    "related": "https://sassine.dev/articles/4/comments"
                }
            }
        },
        "links": {
            "self": "https://sassine.dev/artigos/4"
        }
    }]
}
``` 

Apenas analisando esta única resposta, um cliente sabe:

1. O que foi consultado ( **articles **neste exemplo)
2. Como está estruturado artigos objetos ( **id, titulo, autor, comentarios** )
3. Como recuperar objetos relacionados (ou seja **autor**, a lista de **comentarios**)
4. Que há mais artigos (com 10 base no comprimento da resposta atual e links de paginação)

Agora que já conhece um pouco do modelo, onde sua API está? 
Podemos chama-la de RESTful ou está mais para RESTbut?


