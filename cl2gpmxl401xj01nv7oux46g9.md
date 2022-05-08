## Monolito e Micro serviços saiba usar cada um no seu contexto

## Introdução
De vez em quando, surge um novo paradigma ou tecnologia. Isso traz esperança e entusiasmo. Isso vai salvar todos nós! desta vez é são os Micros serviços… não tão novo assim né.

Porém, como tudo na vida, toda solução teve o seu problema. 
O hype faz você esquecer essa pequena cláusula no final do contrato. E os resultados são catastróficos.

O **padrão de Micros serviços não é nada novo** , na verdade, como quase todo design emergente (além de já existir há alguns anos).
É a Programação Orientada a Objetos aplicada em um nível superior: arquitetura de aplicativos.

Ao criar um programa usando a Programação Orientada a Objetos, você o divide em várias partes, Classes .
Cada classe representa um componente da solução e tem uma função específica.
Ele também funciona apenas em seu próprio subconjunto de dados.
**Isso soa familiar né?**

![MonolitoXMicro.drawio.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1651011118175/aHJSiDKUd.png)

## Conheça os dois princípios – o bom alfaiate

Ao projetar Classes, você deve seguir dois princípios principais:
Acoplamento fraco e alta coesão.

Para ser honesto, essas devem ser as estrelas orientadoras de cada projeto de software, não apenas as OOP. Mas essa é outra história…

**Alta coesão** é a situação em que cada componente é fortemente consistente. Todos os seus dados e métodos referem-se a uma área, significado ou função muito específica.

O **acoplamento fraco** é a situação em que cada componente tem a menor quantidade possível de interações com outros componentes. Ele pode funcionar por conta própria, ocultando detalhes de baixo nível e expõe as interfaces essenciais.

Para micros serviços é o mesmo.

Um** bom design OOP leva a um design de micros serviço ideal**. Não há meio termo, isso deve ser quase o suficiente para responder por que e quando usar micros serviços.
Mas já que há mais, vamos decompô-lo.

## Da pra dividir seu aplicativo em sub-processos?

Imagine o quanto é fácil fazer dois objetos interagirem. Você apenas tem que chamar o método de um objeto e você está pronto para ir. Tudo dentro de um único programa.

Em vez disso, os micros serviços vivem em processos diferentes. Possivelmente, em máquinas diferentes.
Você tem que se comunicar em uma rede, usando APIs. E isso adiciona complexidade!

Você deve ter excelentes razões para fazer isso.

Nem por tendência, nem por diversão .
Porque, garanto a você, se você usar micros serviços aleatoriamente, o gerenciamento deles não será nada divertido

APIs permitem ocultar todos os detalhes de implementação por trás delas.
Isso é essencial em alguns casos e oferece benefícios incríveis.

1. Escalabilidade para tráfego não uniforme
2. Resiliência a erros
3. Implementações separadas
4. Isolamento completo
5. Requisitos diferentes

Existem muitos motivos, alguns mais comuns do que outros.
Nesses casos, divida o aplicativo!

Provavelmente é mais fácil. **Mas lembre-se de respeitar os Dois Princípios!** Seja um bom alfaiate, ou você terá dificuldades para compartilhar dados entre seus micros serviços.

## Não é o seu caso? Vá para um Monólito 
Se o seu sistema estiver totalmente acoplado, use um único aplicativo. Um monólito.

Você terá um gerenciamento de dependência mais limpo e não precisará de orquestrações complexas ou sistemas distribuídos para rastrear erros, compartilhar dados, coletar logs, sincronizar chamadas, proteger interações de rede … e eu posso continuar.

Se seu aplicativo atende a uma longa sequência de chamadas de API , talvez porque uma API exija o resultado das anteriores, isso não é o ideal. Isso também introduziria atrasos na rede…
Se sua aplicação for um procedimento único que requer interação do usuário, bem, a frase é auto explicativa.

Você deve compartilhar um estado entre as chamadas, cada componente não é independente e um único erro na sequência o bloqueará totalmente . Dificilmente há benefício em dividir o aplicativo.

**E lembre-se, Monólito não significa bagunça**

Alguém vê o Monólito como um monte de código não ordenado.

Eles afirmam que o padrão de micros serviços é a única maneira de ter divisões, ordem e tudo.

 😒 Bem, vou revelar algo..

Não é verdade. O design do seu código não depende das divisões do processo.
A Programação Orientada a Objetos também não. Se você dividir seu código direito, se gerenciar suas dependências em uma hierarquia clara, assim como seus arquivos, você pode atingir o mesmo nível de coesão e desacoplamento.

E outro segredo: se você dividir suas APIs em pacotes / módulos / controladores / projetos / o que quer que seja em seu código, você pode decidir servi-los em um único processo ou dividi-los em micros serviços em tempo de construção ou em tempo de execução também, de graça. 🎆🚀

![Uau.gif](https://i.pinimg.com/originals/c3/c0/9d/c3c09dfacd87516c3ba1cdad29aa8aaa.gif)

## Conclusões
Se usados ​​no contexto certo , pelos motivos certos , eles resolvem problemas e melhoram o desempenho ou os custos.

Mas, por favor, pare de torná-los a única solução para todos os problemas.
Porque também podem ser a raiz de todo o mal.