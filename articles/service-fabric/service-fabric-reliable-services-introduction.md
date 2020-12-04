---
title: Visão geral do modelo de programação do Serviço Fiável
description: Saiba mais sobre o modelo de programação do Serviço Fiável da Service Fabric e começa a escrever os seus próprios serviços.
author: masnider
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: masnider
ms.openlocfilehash: 50b8cae11cef5ee8670e2e0270fd8506b1dad4d4
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576541"
---
# <a name="reliable-services-overview"></a>Descrição geral do Reliable Services

A Azure Service Fabric simplifica a escrita e a gestão de serviços apátridas e estatais. Este tópico abrange:

* O modelo de programação dos Serviços Fidedigstão para serviços apátridas e estatais.
* As escolhas que tem de fazer ao escrever um Serviço Fiável.
* Alguns cenários e exemplos de quando usar serviços fiáveis e como são escritos.

*A Reliable Services* é um dos modelos de programação disponíveis no Service Fabric. Outro é o modelo de programação *do Reliable Ator,* que fornece uma estrutura de aplicação [de Ator Virtual](https://research.microsoft.com/en-us/projects/orleans/) no topo do modelo Reliable Services. Para obter mais informações sobre atores fiáveis, consulte [Introdução ao Tecido de Serviço Fiáveis Atores.](service-fabric-reliable-actors-introduction.md)

A Service Fabric gere a vida útil dos serviços, desde o fornecimento e implantação através da atualização e eliminação, através da gestão de [aplicações do Service Fabric.](service-fabric-deploy-remove-applications.md)

## <a name="what-are-reliable-services"></a>O que são Serviços Fiáveis

O Reliable Services oferece-lhe um modelo de programação simples, poderoso e de alto nível para o ajudar a expressar o que é importante para a sua aplicação. Com o modelo de programação dos Serviços Fidedigte, obtém-se:

* Acesso a APIs de Tecido de Serviço. Ao contrário dos serviços de Tecido de Serviço modelados como [Executáveis de Hóspedes,](service-fabric-guest-executables-introduction.md)os Serviços Fiáveis podem utilizar apis de tecido de serviço diretamente. Isto permite que os serviços:
  * Consultar o sistema
  * Reportar saúde sobre entidades no cluster
  * Receber notificações sobre configuração e alterações de código
  * Encontrar e comunicar com outros serviços,
  * Use as [Coleções Fiáveis](service-fabric-reliable-services-reliable-collections.md)
  * Aceda a muitas outras capacidades, todas a partir de um modelo de programação de primeira classe em várias linguagens de programação.
* Um modelo simples para executar o seu próprio código que se parece com outros modelos de programação familiares. O seu código tem um ponto de entrada bem definido e um ciclo de vida facilmente gerido.
* Um modelo de comunicação pluggable. Utilize o transporte à sua escolha, como HTTP com [Web API,](./service-fabric-reliable-services-communication-aspnetcore.md)WebSockets, protocolos TCP personalizados, ou qualquer outra coisa. Os Serviços Fiáveis fornecem algumas ótimas opções fora da caixa que você pode usar, ou você pode fornecer o seu próprio.
* Para serviços estatais, o modelo de programação Reliable Services permite-lhe armazenar de forma consistente e fiável o seu estado dentro do seu serviço utilizando [Coleções Fiáveis.](service-fabric-reliable-services-reliable-collections.md) As Coleções Fiáveis são um conjunto simples de classes de coleção altamente disponíveis e fiáveis que serão familiares a qualquer pessoa que tenha usado coleções C#. Tradicionalmente, os serviços precisavam de sistemas externos para uma gestão fiável do Estado. Com as Coleções Fidedignas, pode armazenar o seu estado junto ao seu cálculo com a mesma elevada disponibilidade e fiabilidade que espera de lojas externas altamente disponíveis. Este modelo também melhora a latência porque está a co-localizar o cálculo e a indicar que precisa de funcionar.

## <a name="what-makes-reliable-services-different"></a>O que torna os Serviços Fiáveis diferentes

Os Serviços Fiáveis são diferentes dos serviços que já escreveu antes, porque o Service Fabric fornece:

* **Fiabilidade** - O seu serviço mantém-se em ambientes pouco fiáveis onde as suas máquinas falham ou atingem problemas de rede, ou nos casos em que os próprios serviços encontram erros e falham ou falham. Para serviços estatais, o seu estado é preservado mesmo na presença de rede ou outras falhas.
* **Disponibilidade** - O seu serviço é acessível e responsivo. O Service Fabric mantém o número desejado de cópias em execução.
* **Escalaability** - Os serviços são dissociados de hardware específico, e podem crescer ou encolher conforme necessário através da adição ou remoção de hardware ou outros recursos. Os serviços são facilmente divididos (especialmente no caso do estado) para garantir que o serviço pode escalar e lidar com falhas parciais. Os serviços podem ser criados e eliminados dinamicamente através do código, permitindo que mais instâncias sejam giradas conforme necessário, por exemplo, em resposta às solicitações dos clientes. Finalmente, a Service Fabric incentiva os serviços a serem leves. O Service Fabric permite que milhares de serviços sejam prestados num único processo, em vez de exigir ou dedicar instâncias ou processos inteiros de SO a uma única instância de um serviço.
* **Consistência** - Qualquer informação armazenada num Serviço Fiável pode ser garantidamente consistente. Isto é verdade mesmo em várias Coleções Fiáveis dentro de um serviço. As alterações entre as coleções dentro de um serviço podem ser feitas de forma transacional atómica.

## <a name="service-lifecycle"></a>Ciclo de vida de serviço

Quer o seu serviço seja imponente ou apátrida, a Reliable Services fornece um ciclo de vida simples que lhe permite ligar rapidamente o seu código e começar.  Obter um novo serviço em funcionamento requer que implemente dois métodos:

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** - Este método é onde o serviço define a(s) pilha de comunicação que pretende utilizar. A pilha de comunicação, como [a Web API,](./service-fabric-reliable-services-communication-aspnetcore.md)é o que define o ponto final de audição ou pontos finais para o serviço (como os clientes chegam ao serviço). Também define como as mensagens que aparecem interagem com o resto do código de serviço.
* **RunAsync** - Este método é onde o seu serviço executa a sua lógica de negócio, e onde iniciaria quaisquer tarefas de fundo que deveriam funcionar durante toda a vida do serviço. O sinal de cancelamento fornecido é um sinal para quando esse trabalho deve parar. Por exemplo, se o serviço precisar de retirar mensagens de uma fila fiável e processá-las, é aqui que esse trabalho acontece.

Se está a aprender sobre serviços de confiança pela primeira vez, leia! Se procura uma passagem detalhada do ciclo de vida de serviços fiáveis, consulte a visão geral do ciclo de vida dos [Serviços Fidedigdos](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Serviços de exemplo

Vamos ver mais de perto como o modelo De Serviços Fidedigdos funciona com serviços apátridas e estatais.

### <a name="stateless-reliable-services"></a>Serviços fiáveis apátridas

Um *serviço apátrida* é aquele em que não há estado mantido dentro do serviço através de chamadas. Qualquer estado que esteja presente é totalmente descartável e não requer sincronização, replicação, persistência ou alta disponibilidade.

Por exemplo, considere uma calculadora que não tem memória e que recebe todos os termos e operações para executar de uma só vez.

Neste caso, o `RunAsync()` (C#) ou `runAsync()` (Java) do serviço pode estar vazio, uma vez que não existe um processamento de tarefas de fundo que o serviço precisa de fazer. Quando o serviço de calculadora é criado, devolve um `ICommunicationListener` (C#) ou `CommunicationListener` (Java) (por [exemplo, Web API](./service-fabric-reliable-services-communication-aspnetcore.md)) que abre um ponto final de audição em alguma porta. Este ponto final de audição liga-se aos diferentes métodos de cálculo (exemplo: "Add(n1, n2)") que definem a API pública da calculadora.

Quando uma chamada é feita a partir de um cliente, o método apropriado é invocado, e o serviço de calculadora executa as operações nos dados fornecidos e devolve o resultado. Não armazena nenhum estado.

Não armazenar nenhum estado interno torna este exemplo calculador simples. Mas a maioria dos serviços não são verdadeiramente apátridas. Em vez disso, eles externam o seu estado para outra loja. (Por exemplo, qualquer aplicação web que dependa de manter o estado de sessão numa loja de apoio ou cache não é apátrida.)

Um exemplo comum de como os serviços apátridas são usados no Service Fabric é como um front-end que expõe a API virada para o público para uma aplicação web. O serviço frontal fala então com serviços estatais para completar um pedido do utilizador. Neste caso, as chamadas de clientes são direcionadas para um porto conhecido, como o 80, onde o serviço apátrida está a ouvir. Este serviço apátrida recebe a chamada e determina se a chamada é de uma parte de confiança e para que serviço está destinado.  Em seguida, o serviço apátrida remete o apelo para a correta divisão do serviço estatal e aguarda uma resposta. Quando o serviço apátrida recebe uma resposta, responde ao cliente original. Um exemplo de tal serviço é a amostra *de Service Fabric Getting Started* [(C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)  /  [Java),](https://github.com/Azure-Samples/service-fabric-java-getting-started)entre outras amostras de Tecido de Serviço nesse repo.

### <a name="stateful-reliable-services"></a>Serviços fiáveis estatais

Um *serviço estatal* é aquele que deve ter alguma parte do Estado mantido consistente e presente para que o serviço funcione. Considere um serviço que calcula constantemente uma média rolante de algum valor com base nas atualizações que recebe. Para isso, deve ter o conjunto atual de pedidos de entrada que precisa para processar e a média atual. Qualquer serviço que recupere, processe e armazene informações numa loja externa (como uma bolha Azure ou loja de mesa hoje) é imponente. Mantém o seu estado na loja externa do Estado.

A maioria dos serviços hoje em dia armazena o seu estado externamente, uma vez que a loja externa é o que fornece fiabilidade, disponibilidade, escalabilidade e consistência para esse estado. Na Service Fabric, os serviços não são obrigados a armazenar o seu estado externamente. A Service Fabric trata destes requisitos tanto para o código de serviço como para o estado de serviço.

Digamos que queremos escrever um serviço que processe imagens. Para isso, o serviço requer uma imagem e a série de conversões para realizar nessa imagem. Este serviço devolve um ouvinte de comunicação (vamos supor que é um WebAPI) que expõe uma API como `ConvertImage(Image i, IList<Conversion> conversions)` . Quando recebe um pedido, o serviço armazena-o `IReliableQueue` num, e devolve algum ID ao cliente para que possa acompanhar o pedido.

Neste serviço, `RunAsync()` poderia ser mais complexo. O serviço tem um loop dentro do seu `RunAsync()` que retira pedidos `IReliableQueue` e executa as conversões solicitadas. Os resultados são armazenados de `IReliableDictionary` forma a que, quando o cliente volta, possam obter as suas imagens convertidas. Para garantir que mesmo que algo falhe, a imagem não se perca, este Serviço Fiável retiraria da fila, realizaria as conversões e armazenaria o resultado numa única transação. Neste caso, a mensagem é removida da fila e os resultados são armazenados no dicionário de resultados apenas quando as conversões estiverem completas. Em alternativa, o serviço poderia retirar a imagem da fila e armazená-la imediatamente numa loja remota. Isto reduz a quantidade de estado que o serviço tem de gerir, mas aumenta a complexidade, uma vez que o serviço tem de manter os metadados necessários para gerir a loja remota. Com qualquer uma das abordagens, se algo falhou no meio, o pedido permanece na fila à espera de ser processado.

Embora este serviço soe como um serviço típico .NET, a diferença é que as estruturas de dados que estão a ser utilizadas ( `IReliableQueue` e `IReliableDictionary` ) são fornecidas pela Service Fabric, e são altamente fiáveis, disponíveis e consistentes.

## <a name="when-to-use-reliable-services-apis"></a>Quando utilizar APIs de Serviços Fiáveis

Considere apis de serviços fiáveis se:

* Pretende que o código do seu serviço (e opcionalmente estatal) seja altamente disponível e fiável.
* Precisa de garantias transacionais em várias unidades de estado (por exemplo, encomendas e artigos de linha de encomenda).
* O estado da sua aplicação pode ser naturalmente modelado como Dicionários e Filas Fiáveis.
* O código ou estado das suas aplicações tem de estar altamente disponível com leituras e escritos de baixa latência.
* A sua aplicação precisa de controlar a concordância ou a granularidade das operações transacionadas através de uma ou mais Coleções Fiáveis.
* Pretende gerir as comunicações ou controlar o esquema de partição do seu serviço.
* O seu código precisa de um ambiente de tempo de execução livre.
* A sua aplicação precisa de criar ou destruir dinamicamente Dicionários Fiáveis ou Filas ou Serviços inteiros em tempo de execução.
* Tem de controlar programáticamente as funcionalidades de backup e restauro do serviço fornecidos por sistemas de assistência ao serviço.
* A sua aplicação precisa de manter a história da mudança para as suas unidades de estado.
* Pretende desenvolver ou consumir fornecedores estatais personalizados e desenvolvidos por terceiros.

## <a name="next-steps"></a>Passos seguintes

* [Arranque rápido de serviços fiáveis](service-fabric-reliable-services-quick-start.md)
* [Coleções fiáveis](service-fabric-reliable-services-reliable-collections.md)
* [O modelo de programação dos Atores Fidedigdos](service-fabric-reliable-actors-introduction.md)
