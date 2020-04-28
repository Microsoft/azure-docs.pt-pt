---
title: Cenários de aplicação e design
description: Visão geral das categorias de aplicações em nuvem em Tecido de Serviço. Discute o design de aplicações que utiliza serviços apátridas e apátridas.
ms.topic: conceptual
ms.date: 01/08/2020
ms.custom: sfrev
ms.openlocfilehash: 0aeb8ab2923915befdd11f96025687be3b3c4ff9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76024738"
---
# <a name="service-fabric-application-scenarios"></a>Cenários de aplicação de tecido de serviço

O Azure Service Fabric oferece uma plataforma fiável e flexível onde pode escrever e executar muitos tipos de aplicações e serviços de negócios. Estas aplicações e microserviços podem ser apátridas ou apátridas, e são equilibradas em recursos através de máquinas virtuais para maximizar a eficiência.

A arquitetura única do Tecido de Serviço permite-lhe realizar análises de dados em tempo real, computação em memória, transações paralelas e processamento de eventos nas suas aplicações. Pode facilmente escalar as suas aplicações dentro ou fora, dependendo dos seus requisitos de recursos em mudança.

Para orientação de design sobre aplicações de construção, leia [arquitetura microservices em Tecido de Serviço Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) e boas práticas para design de [aplicações usando tecido de serviço.](service-fabric-best-practices-applications.md)

Considere utilizar a plataforma Service Fabric para os seguintes tipos de aplicações:

* **Recolha de dados, processamento e IoT**: O Tecido de Serviço trata de grande escala e tem baixa latência através dos seus serviços imponentes. Pode ajudar a processar dados de milhões de dispositivos onde os dados do dispositivo e da computação estão colocalizados.

    Os clientes que construíram serviços IoT utilizando o Service Fabric incluem [Honeywell,](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure) [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron,](https://customers.microsoft.com/story/crestron-partner-professional-services-azure) [BMW,](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/) [Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)e [Mesh Systems.](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions)

* **Aplicações interativas baseadas**em jogos e sessões : O Tecido de Serviço é útil se a sua aplicação necessitar de leituras e escritos de baixa latência, como em jogos online ou mensagens instantâneas. O Service Fabric permite-lhe construir estas aplicações interativas e imponentes sem ter de criar uma loja ou cache separadas. Visite [soluções de jogo Azure](https://azure.microsoft.com/solutions/gaming/) para orientação de design sobre [a utilização de Tecido de Serviço nos serviços de jogos.](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

    Os clientes que construíram serviços de jogo incluem [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) e [Digamore.](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/) Os clientes que construíram sessões interativas incluem [Honeywell com Hololens.](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)

* **Análise de dados e processamento de fluxos**de trabalho : Aplicações que devem processar de forma fiável eventos ou fluxos de dados beneficiam das leituras e escritos otimizados no Tecido de Serviço. O Service Fabric também suporta os gasodutos de processamento de aplicações, onde os resultados devem ser fiáveis e transmitidos para a fase de processamento seguinte sem qualquer perda. Estes oleodutos incluem sistemas transacionais e financeiros, onde a consistência dos dados e as garantias de cálculo são essenciais.

    Os clientes que construíram serviços de fluxo de trabalho empresarial incluem o [Grupo Zeiss,](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform) [quorum Business Solutions](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)e [Société General.](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric)

* **Cálculo de dados**: O Tecido de Serviço permite-lhe construir aplicações imponentes que fazem a putação intensiva de dados. O Tecido de Serviço permite a colocalização do processamento (cálculo) e os dados nas aplicações. 

   Normalmente, quando a sua aplicação requer acesso a dados, a latência da rede associada a um cache de dados externo ou ao nível de armazenamento limita o tempo de cálculo. Os serviços de Tecido de Serviço Audais eliminam essa latência, permitindo leituras e escritos mais otimizados.

   Por exemplo, considere uma aplicação que execute seleções de recomendação em tempo real para os clientes, com um requisito de tempo de ida e volta inferior a 100 milissegundos. As características de latência e desempenho dos serviços de Tecido de Serviço proporcionam uma experiência responsiva ao utilizador, em comparação com o modelo de implementação padrão de ter de recolher os dados necessários a partir do armazenamento remoto. O sistema é mais reativo porque a computação da seleção de recomendações está colocalizada com os dados e regras.

    Os clientes que construíram serviços de computação incluem [a Solidsoft Answer](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) e [infosupport.](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)

* **Serviços altamente disponíveis**: O Service Fabric fornece uma falha rápida criando múltiplas réplicas de serviço secundário. Se um nó, processo ou serviço individual descer devido a hardware ou outra falha, uma das réplicas secundárias é promovida a uma réplica primária com perda mínima de serviço.

* **Serviços escaláveis**: Os serviços individuais podem ser divididos, permitindo que o Estado seja escalado em todo o cluster. Os serviços individuais também podem ser criados e removidos no voo. Você pode escalar os serviços de alguns casos em alguns nós para milhares de casos em muitos nós, e depois escaloná-los novamente como necessário. Pode utilizar o Service Fabric para construir estes serviços e gerir os seus ciclos de vida completos.

## <a name="application-design-case-studies"></a>Estudos de caso de conceção de aplicações

Estudos de caso que mostram como o Service Fabric é usado para projetar aplicações são publicados nas [histórias](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc) do Cliente e Microservices em sites [Azure.](https://azure.microsoft.com/solutions/microservice-applications/)

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Conceber aplicações compostas por microserviços apátridas e apátridas

A construção de aplicações com funções de trabalhador da Azure Cloud Services é um exemplo de um serviço apátrida. Em contrapartida, os microserviços estatais mantêm o seu estado autoritário para além do pedido e da sua resposta. Esta funcionalidade proporciona alta disponibilidade e consistência do Estado através de apis simples que fornecem garantias transacionais apoiadas pela replicação.

Os serviços estatais em Tecido de Serviço trazem alta disponibilidade a todos os tipos de aplicações, não apenas bases de dados e outras lojas de dados. Esta é uma progressão natural. As aplicações já passaram da utilização de bases de dados puramente relacionais para alta disponibilidade para bases de dados NoSQL. Agora, as próprias aplicações podem ter o seu estado "quente" e os dados geridos dentro delas para ganhos adicionais de desempenho sem sacrificar a fiabilidade, consistência ou disponibilidade.

Quando você está construindo aplicações que consistem em microserviços, você normalmente tem uma combinação de aplicações web apátridas (como ASP.NET e Node.js) chamando para serviços apátridas e apátridas de negócios de nível médio. As aplicações e serviços são todos implantados no mesmo cluster service Fabric através dos comandos de implementação de Tecido de Serviço. Cada um destes serviços é independente no que diz respeito à escala, fiabilidade e utilização de recursos. Esta independência melhora a agilidade e flexibilidade no desenvolvimento e na gestão do ciclo de vida.

Os microserviços estatais simplificam os desenhos de aplicações porque eliminam a necessidade de filas e caches adicionais que tradicionalmente têm sido necessários para responder aos requisitos de disponibilidade e latência de aplicações puramente apátridas. Como os serviços estatais têm elevada disponibilidade e baixa latência, há menos detalhes para gerir na sua aplicação.

Os seguintes diagramas ilustram as diferenças entre desenhar uma aplicação que é apátrida e uma que é imponente. Aproveitando os modelos de programação [de Serviços Fiáveis](service-fabric-reliable-services-introduction.md) e [Atores Fiáveis,](service-fabric-reliable-actors-introduction.md) os serviços estatais reduzem a complexidade da aplicação, ao mesmo tempo que alcançam alta supressão e baixa latência.

Aqui está uma aplicação de exemplo ![que usa serviços apátridas: Aplicação que usa serviços apátridas][Image1]

Aqui está uma aplicação de exemplo ![que usa serviços estatais: Aplicação que usa serviços estatais][Image2]

## <a name="next-steps"></a>Passos seguintes

* Começar a construir serviços apátridas e apátridas com os modelos de programação de [Serviços Fiáveis e](service-fabric-reliable-services-quick-start.md) [Atores Fiáveis.](service-fabric-reliable-actors-get-started.md)
* Visite o Centro de Arquitetura Azure para obter orientações sobre a construção de [microserviços no Azure.](https://docs.microsoft.com/azure/architecture/microservices/)
* Vá à [aplicação Azure Service Fabric e ao cluster de boas práticas](service-fabric-best-practices-overview.md) para orientação de design de aplicações.

* Veja também:
  * [Compreender os microsserviços](service-fabric-overview-microservices.md)
  * [Definir e gerir o estado de serviço](service-fabric-concepts-state.md)
  * [Disponibilidade dos serviços](service-fabric-availability-services.md)
  * [Serviços à escala](service-fabric-concepts-scalability.md)
  * [Serviços de partição](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png
