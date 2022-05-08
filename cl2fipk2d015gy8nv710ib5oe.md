## Você conhece os 12 Fatores?

A Heroku lançou um manifesto chamado “The Twelve Factor App”.
Este manifesto descreve doze práticas para serem seguidas ao criar aplicações voltadas para nuvem.

Vou listar os 12 fatores deste manifesto com breve resumo de cada um, se quiser ler a documentação oficial "[https://12factor.net/pt_br/](https://12factor.net/pt_br/)"

## Os Doze Fatores
### I. Base de Código
> Uma base de código com rastreamento utilizando controle de revisão, muitos deploys

Todos os ativos relacionados a um aplicativo, desde o código-fonte, o script de provisionamento e as definições de configuração, são armazenados em um repositório de **código-fonte acessível à equipe de desenvolvimento, teste e administração do sistema. **

**O repositório de código-fonte também é acessível a todos os scripts de automação** que fazem parte dos processos de Integração Contínua/Entrega Contínua (CI/CD) que fazem parte do Ciclo de Vida de Desenvolvimento de Software da empresa. (SDLC).

### II. Dependências
> Declare e isole as dependências

Apenas o código exclusivo e relevante para a finalidade do aplicativo é armazenado no controle do código-fonte. 

Artefatos externos como pacotes Node.js, arquivos Java .jar ou DLLs .NET devem ser referenciados em um manifesto de dependências carregado na memória no desenvolvimento, teste e tempo de execução da produção. 

**Você deseja evitar o armazenamento de artefatos junto com o código-fonte no repositório de código-fonte.**

### III. Configurações
> Armazene as configurações no ambiente

As informações de configuração são injetadas no ambiente de tempo de execução como **variáveis ​​de ambiente ou como configurações definidas em um arquivo de configuração independente**. 

Embora, em certos casos, seja permitido armazenar configurações padrão que podem ser substituídas diretamente no código, configurações como número de porta, URLs de dependência e configurações de estado como DEBUG **devem existir separadamente e ser aplicadas na implantação**.

A vantagem de manter as definições de configuração separadas da lógica do aplicativo é que você pode aplicar as definições de configuração de acordo com o caminho de implantação.

### IV. Serviços de Apoio
> Trate os serviços de apoio, como recursos ligados

**Tratar componentes externos**, como bancos de dados, servidores de e-mail, agentes de mensagens e serviços independentes que podem ser provisionados e mantidos pela equipe de sistemas como recursos anexados. 

**Tratar recursos como serviços de apoio** promove flexibilidade e eficiência no ciclo de vida de desenvolvimento de software (SDLC).

### V. Construa, lance, execute (Build, Release, Run)
> Separe estritamente os builds e execute em estágios

Divida o processo de implantação em três estágios replicáveis ​​que podem ser instanciados a qualquer momento. 

O estágio **Build **é onde o código é recuperado do sistema de gerenciamento de código-fonte e compilado/compilado em artefatos armazenados em um repositório de artefatos, como o Docker Hub ou um repositório Maven. 

Depois que o código é compilado, as definições de configuração são aplicadas no estágio **Release **.

 Em seguida, no estágio **Run **, um ambiente de tempo de execução é provisionado por meio de scripts usando uma ferramenta como o Ansible. 

O aplicativo e suas dependências são implantados no ambiente de tempo de execução recém-provisionado, o processo é completamente efêmero.

### VI. Processos
> Execute a aplicação como um ou mais processos que não armazenam estado

Nenhum processo único acompanha o estado de outro processo e que nenhum processo acompanha informações como o status da sessão ou do fluxo de trabalho.

**Um processo sem estado facilita o dimensionamento**. 

Quando um processo é sem estado, as instâncias podem ser adicionadas e removidas para lidar com uma carga de carga específica em um determinado momento.

### VII. Vínculo de porta
> Exporte serviços por ligação de porta

Um serviço ou aplicativo é identificável para a rede pelo número da porta, não por um nome de domínio. O raciocínio é que nomes de domínio e endereços IP associados podem ser atribuídos dinamicamente por manipulação manual e mecanismos automatizados de descoberta de serviços. 

Assim, usá-los como ponto de referência não é confiável. No entanto, **expor um serviço ou aplicativo à rede de acordo com o número da porta** é mais confiável e fácil de gerenciar.

### VIII. Concorrência
> Dimensione por um modelo de processo

**Organizar os processos de acordo com sua finalidade** e, em seguida, separar esses processos para que possam ser ampliados e reduzidos de acordo com a necessidade

Caso a carga sobre os servidores da Web aumente, esse grupo pode ser ampliado de maneira isolada para atender às demandas em questão. No entanto, caso ocorra um gargalo devido a uma carga colocada no Serviço de Negócios, essa camada pode ser ampliada de forma independente.

### IX. Descartabilidade
> Maximizar a robustez com inicialização e desligamento rápido

Os aplicativos devem iniciar e parar normalmente. 

Isso significa fazer todas as "limpezas" necessárias antes que um aplicativo seja disponibilizado aos consumidores.

Por exemplo, uma inicialização normal garantirá que todas as conexões de banco de dados e acesso a outros recursos de rede estejam operacionais. Além disso, qualquer outro trabalho de configuração que precise ser realizado já ocorreu.

Em termos de desligamento, a descartabilidade defende a **garantia de que todas as conexões de banco de dados e outros recursos de rede sejam encerrados **corretamente e que todas as atividades de desligamento sejam registradas.

### X. Dev/prod semelhantes
> Mantenha o desenvolvimento, teste, produção o mais semelhante possível

Basicamente todos os caminhos de **implantação são semelhantes, mas independentes** e que nenhuma implantação "Salta"  para outro passo de implantação.



### XI. Logs
> Trate logs como fluxo de eventos

O **envio de dados de log** em um fluxo que vários consumidores interessados ​​podem acessar.

O processo de envio de dados de log precisa ser separado do processamento de dados de log. 

Por exemplo, um consumidor pode estar interessado apenas em dados de erro, enquanto outro consumidor pode estar interessado em dados de solicitação/resposta. 

Outro consumidor pode estar interessado em **armazenar todos os dados de log para arquivamento de eventos**. Um benefício adicional é que, mesmo que um aplicativo morra, os dados de log permanecem vivos bem depois.

### XII. Processos de Admin
> Executar tarefas de administração/gerenciamento como processos pontuais

Processos administrativos são a primeira classe no ciclo de vida de desenvolvimento de software. 

## Conclusão

Os princípios do aplicativo de 12 fatores são projetados para permitir que os desenvolvedores criem aplicativos destinados a serem executados em escala da web. Eles podem parecer esmagadores no início e, de muitas maneiras, são. Ter que repensar a própria natureza de como você cria software pode ser uma tarefa assustadora.

Felizmente, implementar os princípios do The 12 Factor App não é um negócio de tudo ou nada. Você pode levá-los em pequenos pedaços digeríveis, começando com o primeiro e depois progredindo pelos restantes. O truque é se comprometer a seguir os princípios e então dar o primeiro passo.


