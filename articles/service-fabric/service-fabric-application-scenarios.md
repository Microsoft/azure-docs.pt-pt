---
title: Cenários de aplicação e design
description: Visão geral das categorias de aplicações em nuvem em Tecido de Serviço. Discute o design de aplicações que usa serviços apátridas e apátridas.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 7d9459e6e0168657cf8e9062331fec0237f58c70
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045761"
---
# <a name="service-fabric-application-scenarios"></a>Cenários de aplicação de tecido de serviço

A Azure Service Fabric oferece uma plataforma fiável e flexível onde pode escrever e executar vários tipos de aplicações e serviços de negócio. Estas aplicações e microserviços podem ser apátridas ou stateful, e são equilibrados em recursos através de máquinas virtuais para maximizar a eficiência.

A arquitetura única do Service Fabric permite-lhe realizar análises de dados em tempo real, computação em memória, transações paralelas e processamento de eventos nas suas aplicações. Pode facilmente escalar as suas aplicações dentro ou fora, dependendo dos seus requisitos de recursos alterados.

Para orientar o projeto sobre aplicações de construção, leia [a arquitetura microservices no Azure Service Fabric](/azure/architecture/reference-architectures/microservices/service-fabric) e as [melhores práticas para design de aplicações usando o Service Fabric.](service-fabric-best-practices-applications.md)

Considere utilizar a plataforma Service Fabric para os seguintes tipos de aplicações:

* **Recolha de dados, processamento e IoT**: O Tecido de Serviço lida em larga escala e tem baixa latência através dos seus serviços estatais. Pode ajudar a processar dados em milhões de dispositivos onde os dados do dispositivo e do cálculo estão colocados.

    Os clientes que construíram serviços IoT utilizando o Service Fabric incluem [a Honeywell,](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure) [PCL Construction,](https://customers.microsoft.com/story/pcl-construction-professional-services-azure) [Crestron,](https://customers.microsoft.com/story/crestron-partner-professional-services-azure) [BMW,](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/) [Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)e Mesh [Systems.](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions)

* **Aplicações interativas baseadas em jogos e sessão**: O Service Fabric é útil se a sua aplicação necessitar de leituras e escritos de baixa latência, como em jogos online ou mensagens instantâneas. O Service Fabric permite-lhe construir estas aplicações interativas e imponentes sem ter de criar uma loja ou cache separados. Visite [soluções de jogos Azure](https://azure.microsoft.com/solutions/gaming/) para orientar o design sobre [a utilização do Service Fabric nos serviços de jogos.](/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

    Os clientes que construíram serviços de jogos incluem [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) e [Digamore.](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/) Os clientes que construíram sessões interativas incluem [Honeywell com Hololens.](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)

* **Análise de dados e processamento de fluxo de trabalho**: Aplicações que devem processar de forma fiável eventos ou fluxos de dados beneficiam das leituras e escritas otimizadas em Tecido de Serviço. A Service Fabric também suporta os gasodutos de processamento de aplicações, onde os resultados devem ser fiáveis e transmitidos para a fase seguinte de processamento sem qualquer perda. Estes oleodutos incluem sistemas transacionais e financeiros, onde a consistência dos dados e as garantias de cálculo são essenciais.

    Os clientes que construíram serviços de fluxo de trabalho de negócios incluem [o Grupo Zeiss,](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform) [a Quorum Business Solutions](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)e [a Société General.](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric)

* **Computação em dados**: O Tecido de Serviço permite-lhe construir aplicações imponentes que façam cálculo intensivo de dados. O Service Fabric permite a colocação de processamento (computação) e dados em aplicações. 

   Normalmente, quando a sua aplicação requer acesso a dados, a latência da rede associada a uma cache de dados externo ou ao nível de armazenamento limita o tempo de cálculo. Os serviços stateful Service Fabric eliminam essa latência, permitindo leituras e escritos mais otimizados.

   Por exemplo, considere uma aplicação que realiza seleções de recomendação perto de tempo real para os clientes, com um requisito de tempo de ida e volta inferior a 100 milissegundos. As características de latência e desempenho dos serviços de Service Fabric proporcionam uma experiência de resposta ao utilizador, em comparação com o modelo padrão de implementação de ter de obter os dados necessários do armazenamento remoto. O sistema é mais responsivo porque o cálculo da seleção de recomendações está cotado com os dados e regras.

    Os clientes que construíram serviços de computação incluem [solidsoft Answer](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) e [Infosupport.](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)

* **Serviços altamente disponíveis**: O Service Fabric fornece um rápido failover através da criação de múltiplas réplicas de serviço secundário. Se um nó, processo ou serviço individual cair devido a hardware ou outra falha, uma das réplicas secundárias é promovida a uma réplica primária com perda mínima de serviço.

* **Serviços escaláveis**: Os serviços individuais podem ser divididos, permitindo que o estado seja dimensionado através do cluster. Os serviços individuais também podem ser criados e removidos no voo. Você pode escalar os serviços de alguns casos em alguns nós para milhares de casos em muitos nós, e depois escaloná-los novamente se necessário. Você pode usar o Service Fabric para construir estes serviços e gerir seus ciclos de vida completos.

## <a name="application-design-case-studies"></a>Estudos de caso de design de aplicações

Estudos de caso que mostram como o Service Fabric é usado para conceber aplicações são publicados nas [histórias](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc) do Cliente e [Microserviços em sites Azure.](https://azure.microsoft.com/solutions/microservice-applications/)

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Conceber aplicações compostas por microserviços apátridas e estatais

A construção de aplicações com funções de trabalhador da Azure Cloud Services é um exemplo de um serviço apátrida. Em contrapartida, os microserviços estatais mantêm o seu estado autoritário para além do pedido e da sua resposta. Esta funcionalidade proporciona alta disponibilidade e consistência do Estado através de APIs simples que fornecem garantias transacionais apoiadas por replicação.

Os serviços estatais em Service Fabric trazem alta disponibilidade para todos os tipos de aplicações, não apenas bases de dados e outras lojas de dados. Esta é uma progressão natural. As aplicações já passaram de utilizar bases de dados puramente relacionais para uma elevada disponibilidade para bases de dados NoSQL. Agora, as próprias aplicações podem ter o seu estado "quente" e os dados geridos dentro delas para ganhos adicionais de desempenho sem sacrificar a fiabilidade, consistência ou disponibilidade.

Quando você está construindo aplicações que consistem em micro-serviços, você normalmente tem uma combinação de aplicações apátridas web (como ASP.NET e Node.js) chamando para serviços de classe média de negócios apátridas e stateful. As aplicações e serviços são todos implantados no mesmo cluster de Tecido de Serviço através dos comandos de implementação do Tecido de Serviço. Cada um destes serviços é independente no que diz respeito à escala, fiabilidade e utilização de recursos. Esta independência melhora a agilidade e flexibilidade no desenvolvimento e na gestão do ciclo de vida.

Os microserviços imponentes simplificam os desenhos de aplicações porque eliminam a necessidade de filas e caches adicionais que tradicionalmente têm sido obrigados a responder aos requisitos de disponibilidade e latência de aplicações puramente apátridas. Como os serviços estatais têm alta disponibilidade e baixa latência, há menos detalhes para gerir na sua aplicação.

Os seguintes diagramas ilustram as diferenças entre desenhar uma aplicação que é apátrida e uma que é imponente. Aproveitando os modelos de programação [reliable Services](service-fabric-reliable-services-introduction.md) e [Reliable Actors,](service-fabric-reliable-actors-introduction.md) os serviços estatais reduzem a complexidade da aplicação, ao mesmo tempo que alcançam uma elevada produção e baixa latência.

Aqui está uma aplicação exemplo que usa serviços apátridas: ![ Aplicação que usa serviços apátridas][Image1]

Aqui está uma aplicação exemplo que usa serviços estatais: ![ Aplicação que usa serviços estatais][Image2]

## <a name="next-steps"></a>Passos seguintes

* Começar a construir serviços apátridas e estatais com os [modelos](service-fabric-reliable-services-quick-start.md) de programação de Service Fabric Reliable Services e [Reliable Actors.](service-fabric-reliable-actors-get-started.md)
* Visite o Centro de Arquitetura Azure para obter orientações sobre [a construção de microserviços em Azure.](/azure/architecture/microservices/)
* Vá à [aplicação Azure Service Fabric e às melhores práticas do cluster](./service-fabric-best-practices-security.md) para orientação de design de aplicações.

* Veja também:
  * [Compreender os microsserviços](service-fabric-overview-microservices.md)
  * [Definir e gerir o estado de serviço](service-fabric-concepts-state.md)
  * [Disponibilidade dos serviços](service-fabric-availability-services.md)
  * [Serviços de escala](service-fabric-concepts-scalability.md)
  * [Serviços de partilha](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png