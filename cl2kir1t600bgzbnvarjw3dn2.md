## Tenha um alto desempenho manipulando JSON em Java com Jackson API

Vejo que muita gente ainda tem desconhecimento do poder desta lib, mesmo que já tenha uso em algum desenvolvimento não explorou todo seu conteúdo, pensando nisso montei este artigo para te apresentar as principais funções do Jackson que vão te ajudar muito na manipulação de JSON em Java, quando falamos de REST com Java/Spring... é impossível não pensar no Jackson tratando nossos dados de entrada e saída do nosso serviço como também em outras API que consumimos internamente. 

## Jackson Object Mapper 
A classe Jackson ObjectMapper da FasterXML é a maneira mais simples de analisar JSON com Java. 

O **Jackson ObjectMapper pode analisar JSON de uma string, fluxo ou arquivo e criar um objeto Java** ou gráfico de objeto que representa o JSON analisado. Esse processo é conhecido como** desserialização do JSON**.
Assim como também podemos gerar JSON a partir de objetos Java que seria a  **serialização objetos Java para JSON**.

A biblioteca Jackson disponibiliza diversas anotações que podemos usar em nossas classes, facilitando nosso trabalho durante o mapeamento. 

Aqui vou falar apenas das que utilizamos no dia a dia mas se quiser saber mais pode acessar a documentação oficial [jackson-annotations.](https://javadoc.io/doc/com.fasterxml.jackson.core/jackson-annotations/latest/index.html)

1. JsonProperty
2. JsonInclude
3. JsonIgnore
4. JsonFormat
5. JsonSerialize
6. JsonDeserialize

### Json Property
É uma anotação de marcador para definir a propriedade lógica.
[@JsonProperty](#!) pode ser anotado em método setter ou getter não estático ou campo de objeto não estático. 
A propriedade lógica é usada na serialização e desserialização de JSON. 


```java
public class ExemploDTO {
   // ... outros campos

   @JsonProperty("nome_do_usuario")
   private String nomeUsuario;

   //... restante da implementação
}
``` 
<span class="r"> a </span>

Com o [@JsonProperty](#!) falamos o nome da chave de origem do JSON e definimos o seu valor a nossa variável. 

**O valor da chave “nome_do_usuario” será atrelado a nossa variável nomeUsuario  **

### Json Include
é usado em propriedades de **exclusão com valores nulos / vazios ou padrão**.

Classe sem [@JsonInclude](#!)

```java
public class ExemploDTO {
   private int id;
   private String nome;

  ExemploDTO (int id,String nome) {
    this.id = id;
    this.name = name;
 }
}
``` 

```json
{ "id": 15, "nome": null } 
``` 
Agora com [@JsonInclude](#!)

```java
@JsonInclude(JsonInclude.Include.NON_NULL)
public class ExemploDTO {
   private int id;
   private String nome;

  ExemploDTO (int id,String nome) {
    this.id = id;
    this.nome= nome;
 }
}
``` 

Saída : 

```json
{ "id": 15 }
``` 

por conta do valor da variável 'nome' ser nulla ela foi excluída do json final.

Existem 6 tipos de Include

- **ALWAYS**
 - Valor que indica que a propriedade deve ser **sempre incluída, independente do valor** da propriedade.

- **NON_ABSENT**
 - Valor que indica que as propriedades estão incluídas, a **menos que seu valor seja valor nulo de um tipo referencial **(como Java 8+ 'Optional' ou AtomicReference); isto é, algo que não respeitaria um valor não nulo.

- **NON_DEFAULT**
 - O significado dessa configuração depende do contexto: se a anotação é especificada para o tipo POJO (classe) ou não.

Se for tipo POJO apenas as propriedades com valores padrões serão excluídas

Exemplo: 

```java
@JsonInclude(JsonInclude.Include.NON_DEFAULT)
public class Empregado {
  private String nome;
  private String departamento;
  private Integer salario;
  private boolean tempoIntegral;
// ... 
``` 
```java
public class ExemploMain {
  public static void main(String[] args) throws IOException {
      Empregado empregado = new Empregado ();
      empregado .setNome("Sassine");
      empregado .setTempoIntegral(false);
      empregado .setSalario(Integer.valueOf(0));

      ObjectMapper om = new ObjectMapper();
      String jsonString = om.writeValueAsString(empregado );
      System.out.println(jsonString);
  }
}
``` 
Saída : 

```json
{"nome":"Sassine","salario":0 }
``` 

Em nível de campo ou global será excluído todas propriedades que tenham seus valores definidos com valores padrões ou vazios, listas vazias e afins...

Saída seria algo assim: 

```json
{"nome":"Sassine" }
``` 


- **NON_EMPTY**
 - Valor que indica que somente as propriedades com valor nulo, ou o que for considerado vazio, não devem ser incluídas.

- **NON_NULL**
 - Valor que indica que somente propriedades com valores não nulos devem ser incluídas.

- **USE_DEFAULTS**
 - Pseudo-valor para indicar que os padrões de nível superior fazem sentido, para evitar substituir o valor de inclusão.


### Json Ignore
Json ignore funciona semelhante ao Include porem ele pode ser usado para especificar apenas que aquele campo não será deserializado mesmo contendo valor. 

```java
public class ExemploDTO {
   @JsonIgnore
   private int id;
   private String nome;

  ExemploDTO (int id,String nome) {
    this.id = id;
    this.nome= nome;
 }
}
``` 

```json
{ "nome": null }
``` 

### Json Format
É uma anotação de Jackson usada para especificar como formatar campos e / ou propriedades para saída JSON.
Especificamente, essa anotação permite que você especifique como formatar os valores de Data e Calendário de acordo com um formato SimpleDateFormat.

```java
public class ExemploDTO {
   @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd")
   private Date dataVencimento;

   @JsonFormat(shape = JsonFormat.Shape.NUMBER)
   private ExemploEnum tipoUsuario = ExemploEnum.MODERADOR;
}
```
Saída: 
```json
{ "dataVencimento": "2022-04-26", "tipoUsuario" : 2}
```  

### Json Serialize
Indica um serializador personalizado a ser usado ao empacotar a entidade

Exemplo usando um serializador customziado

```java
public class ExemploDTO {

   private String name;

   @JsonSerialize(using =  CustomDateSerializer.class)
   private Date dataVencimento;
}
```

```java
public class CustomDateSerializer extends StdSerializer {

	private static SimpleDateFormat FORMATTER = new SimpleDateFormat("dd-MM-yyyy hh:mm:ss");
	
	public CustomDateSerializer() {
		this(null);
	}
	
	public CustomDateSerializer() {
		super(t);
	}
	
	@Override
	public void serialize(Date value, JsonGenerator gen, SerialzerProvider arg2) throws IOException, JsonProcessingException {
		gen.writeString(FORMATTER.format(value));
	}
}
```

**Bonus:**
Nas versões novas do java utilizamos a API do java.time, se precisar utilizar um serializador para tal, a [lib jsr310 do jackson](https://javadoc.io/doc/com.fasterxml.jackson.datatype/jackson-datatype-jsr310/latest/index.html) tem o seguinte import: 

**ToStringSerializer**


### Json Deserialize
Assim como o [@JsonSerialize](#!) indica um serializador custom, o json deserialize aponta um deserializador customizado. 


```java
public class JsonDeserializeDTO {
	public String nome;
	
	@JsonDeserialize(using = CustomDateDeserializer.class)
	public Date dataVencimento;
}
``` 

```java
public class CustomDateDeserializer extends StdDeserializer<Date> {
	private static SimpleDateFormat formatter = new SimpleDateFormat("dd-MM-yyyy");
	
	public CustomDateDeserializer() {
		this(null);
	}
	
	public CustomDateDeserializer(Class<?> vc) {
		this(vc);
	}
	
	@Override
	public Date deserialize(JsonParser jsonparser, DeserializationContext context) throws IOException {
		String date = jsonparser.getText();
		try {
			return formatter.parse(date);
		} catch (ParseException e) {
			throw new RuntimeException(e);
		}
	}
}
``` 

Saída: 
```json
{ "dataVencimento": "2022-04-26", "nome" : "Sassine"}
```  


**Bonus:**
Nas versões novas do java utilizamos a API do java.time, se precisar utilizar um deserializador para tal, a [lib jsr310 do jackson](https://javadoc.io/doc/com.fasterxml.jackson.datatype/jackson-datatype-jsr310/latest/index.html) tem os seguintes itens: 

**LocalDateTime** ==  **LocalDateTimeDeserializer**

**LocalDate** ==  **LocalDateDeserializer**

**LocalTime** ==  **LocalTimeDeserializer**

## Conclusão

Utilizando essas principais anotações em sua aplicação durante a manipulação dos dados de entrada no formato JSON você estará otimizando muito tempo e linhas de códigos que seriam responsáveis por essas conversões de forma manual. 

E isso tudo combinado com o a lib do **javax.validation** *(estarei fazendo um post sobre em breve)* torna a serialização e manipulação de JSON em sua API em um escopo extremamente seguro, limpo com diminuição de linhas de códigos para aplicar estas regras de conversão e de negócios e de fácil manutenção.








