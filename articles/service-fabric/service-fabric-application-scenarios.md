---
title: Cenários e design de aplicativos | Microsoft Docs
description: Visão geral das categorias de aplicativos em nuvem no Service Fabric. Discute o design de aplicativos que usa serviços com e sem estado.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/24/2019
ms.author: atsenthi
ms.openlocfilehash: 6563d6e7c454f44e1a70d725191e56d3f90315c2
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "67052595"
---
# <a name="service-fabric-application-scenarios"></a>Cenários de aplicativo Service Fabric
O Azure Service Fabric oferece uma plataforma confiável e flexível em que você pode escrever e executar vários tipos de aplicativos e serviços de negócios. Esses aplicativos e microserviços podem ser sem estado ou com estado e são balanceados por recursos entre máquinas virtuais para maximizar a eficiência. 

A arquitetura exclusiva do Service Fabric permite que você execute análise de dados quase em tempo real, computação na memória, transações paralelas e processamento de eventos em seus aplicativos. Você pode facilmente dimensionar seus aplicativos para cima ou para baixo (na verdade, dentro ou fora), dependendo dos seus requisitos de recursos de alteração.

Para obter diretrizes de design sobre a criação de aplicativos, leia a [arquitetura de microserviços no Azure Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) e [as práticas recomendadas para o design de aplicativos usando Service Fabric](service-fabric-best-practices-applications.md).

Considere o uso da plataforma Service Fabric para os seguintes tipos de aplicativos:

* **Coleta de dados, processamento e IOT**: Service Fabric manipula grande escala e tem baixa latência por meio de seus serviços com estado. Ele pode ajudar a processar dados em milhões de dispositivos em que os dados para o dispositivo e a computação estão colocalizados.

    Os clientes que criaram serviços de IoT usando Service Fabric incluem [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), a [construção PCL](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)e [sistemas de malha](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Jogos e aplicativos interativos baseados em sessão**: Service Fabric será útil se seu aplicativo exigir leituras e gravações de baixa latência, como em jogos online ou mensagens instantâneas. Service Fabric permite que você crie esses aplicativos interativos e com estado sem precisar criar um armazenamento ou cache separado. Visite as [soluções de jogos do Azure](https://azure.microsoft.com/solutions/gaming/) para obter diretrizes de design sobre como [usar Service Fabric em serviços de jogos](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).

    Os clientes que criaram serviços de jogos incluem os [próximos jogos](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) e [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Os clientes que criaram sessões interativas incluem [Honeywell com o Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens).

* **Análise de dados e processamento de fluxo de trabalho**: Aplicativos que devem processar com confiança eventos ou fluxos de dados se beneficiam das leituras e gravações otimizadas em Service Fabric. O Service Fabric também dá suporte a pipelines de processamento de aplicativos, onde os resultados devem ser confiáveis e passados para o próximo estágio de processamento sem perda. Esses pipelines incluem sistemas transacionais e financeiros, em que a consistência de dados e as garantias de computação são essenciais.

    Os clientes que criaram serviços de fluxo de trabalho de negócios incluem [Zeiss Group](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform), [Quorum Business Solutions](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)e [Société General](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric).

* **Computação de dados**: Service Fabric permite que você crie aplicativos com estado que fazem computação intensiva de dados. Service Fabric permite a colocação de processamento (computação) e dados em aplicativos. 

   Normalmente, quando seu aplicativo requer acesso a dados, a latência de rede associada a um cache de dados externo ou a uma camada de armazenamento limita o tempo de computação. Os serviços de Service Fabric com estado eliminam essa latência, permitindo leituras e gravações mais otimizadas. 
   
   Por exemplo, considere um aplicativo que executa seleções de recomendação quase em tempo real para clientes, com um requisito de tempo de ida e volta de menos de 100 milissegundos. As características de latência e desempenho dos serviços de Service Fabric fornecem uma experiência responsiva ao usuário, em comparação com o modelo de implementação padrão de ter que buscar os dados necessários do armazenamento remoto. O sistema é mais responsivo porque o cálculo da seleção de recomendação é colocado com os dados e as regras.

    Os clientes que criaram serviços de computação incluem [Solidsoft resposta](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) e [infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura).

* **Serviços altamente disponíveis**: Service Fabric fornece um failover rápido criando várias réplicas de serviço secundário. Se um nó, processo ou serviço individual falhar devido a um hardware ou outra falha, uma das réplicas secundárias será promovida para uma réplica primária com perda mínima de serviço.

* **Serviços escalonáveis**: Serviços individuais podem ser particionados, permitindo que o estado seja escalado horizontalmente no cluster. Serviços individuais também podem ser criados e removidos em tempo real. Você pode escalar horizontalmente os serviços de algumas instâncias em alguns nós para milhares de instâncias em muitos nós e, em seguida, dimensioná-los novamente conforme necessário. Você pode usar Service Fabric para criar esses serviços e gerenciar seus ciclos de vida completos.

## <a name="application-design-case-studies"></a>Estudos de caso de design de aplicativo
Estudos de caso que mostram como Service Fabric é usado para projetar aplicativos são publicados nas [histórias do cliente](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/) e nos microserviços [nos sites do Azure](https://azure.microsoft.com/solutions/microservice-applications/) .

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Criando aplicativos compostos de microserviços com e sem estado
A criação de aplicativos com as funções de trabalho dos serviços de nuvem do Azure é um exemplo de um serviço sem estado. Por outro lado, os microserviços com estado mantêm seu estado autoritativo além da solicitação e sua resposta. Essa funcionalidade fornece alta disponibilidade e consistência do estado por meio de APIs simples que fornecem garantias transacionais apoiadas pela replicação. 

Os serviços com estado no Service Fabric trazem alta disponibilidade para todos os tipos de aplicativos, não apenas para bancos de dados e outros armazenamentos. Essa é uma progressão natural. Os aplicativos já foram movidos do uso de bancos de dados unicamente relacionais para alta disponibilidade para bancos de dados NoSQL. Agora, os próprios aplicativos podem ter seu estado "quente" e os dados gerenciados neles para obter ganhos de desempenho adicionais, sem sacrificar a confiabilidade, a consistência ou a disponibilidade.

Quando você estiver criando aplicativos que consistem em microservices, normalmente terá uma combinação de aplicativos Web sem estado (como ASP.NET e node. js) chamando em serviços de camada intermediária de negócios com e sem estado. Os aplicativos e serviços são todos implantados no mesmo cluster Service Fabric por meio dos comandos de implantação Service Fabric. Cada um desses serviços é independente em relação à escala, confiabilidade e uso de recursos. Essa independência melhora a agilidade e a flexibilidade no desenvolvimento e no gerenciamento do ciclo de vida.

Os microserviços com estado simplificam os designs de aplicativos porque eles eliminam a necessidade de filas e caches adicionais que tradicionalmente eram necessários para atender aos requisitos de disponibilidade e latência de aplicativos puramente sem estado. Como os serviços com estado têm alta disponibilidade e baixa latência, há menos detalhes a serem gerenciados em seu aplicativo. 

Os diagramas a seguir ilustram as diferenças entre a criação de um aplicativo sem estado e um com estado. Aproveitando os modelos de programação [Reliable Services](service-fabric-reliable-services-introduction.md) e [Reliable Actors](service-fabric-reliable-actors-introduction.md) , os serviços com estado reduzem a complexidade do aplicativo enquanto alcançam alta taxa de transferência e baixa latência.

Aqui está um exemplo de aplicativo que usa serviços sem estado: ![Aplicativo que usa serviços sem estado][Image1]

Aqui está um exemplo de aplicativo que usa serviços com estado: ![Aplicativo que usa serviços sem estado][Image2]

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [padrões e cenários](service-fabric-patterns-and-scenarios.md).

* Comece a criar serviços com e sem estado com os modelos de programação Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md) e [Reliable Actors](service-fabric-reliable-actors-get-started.md) .
* Visite a Centro de Arquitetura do Azure para obter diretrizes sobre a criação de microserviços [no Azure](https://docs.microsoft.com/azure/architecture/microservices/).
* Acesse o [Azure Service Fabric aplicativos e práticas recomendadas de cluster](service-fabric-best-practices-overview.md) para obter diretrizes de design de aplicativo.

* Consulte também os seguintes tópicos:
  * [Conte-me sobre os microserviços](service-fabric-overview-microservices.md)
  * [Definir e gerenciar o estado do serviço](service-fabric-concepts-state.md)
  * [Disponibilidade de serviços de Service Fabric](service-fabric-availability-services.md)
  * [Dimensionar Service Fabric serviços](service-fabric-concepts-scalability.md)
  * [Serviços de Service Fabric de partição](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
