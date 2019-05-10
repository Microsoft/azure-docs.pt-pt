---
title: Cenários da aplicação e design | Documentos da Microsoft
description: Descrição geral das categorias de aplicações na cloud no Service Fabric. Aborda o design do aplicativo que utiliza os serviços com e sem estado.
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
ms.openlocfilehash: f7ad295eb30d257cd195ae02d5a5b1d85de76bda
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228492"
---
# <a name="service-fabric-application-scenarios"></a>Cenários de aplicações do Service Fabric
O Azure Service Fabric oferece uma plataforma fiável e flexível que lhe permite escrever e executar muitos tipos de aplicações e serviços empresariais. Estas aplicações e os microsserviços podem ser com ou sem estado, e são com balanceamento de recurso em máquinas virtuais para maximizar a eficiência. A arquitetura única do Service Fabric permite-lhe efetuar perto de análise de dados em tempo real, computação dentro da memória, transações paralelas e em seus aplicativos de processamento de eventos. Pode facilmente dimensionar as suas aplicações ou reduzir verticalmente (realmente a entrada ou saída), dependendo dos requisitos de recursos de alteração.

Para obter orientações de design sobre a criação de aplicativos, leia [arquitetura de microsserviços no Azure Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) e [melhores práticas para o design do aplicativo com o Service Fabric](service-fabric-best-practices-applications.md)

A plataforma do Service Fabric é ideal para os seguintes tipos de aplicativos:

* **Recolha de dados, processamento e IoT**: Uma vez que processa o dimensionamento de grande escala e tem de baixa latência através de seus serviços com monitoração de estado do Service Fabric, é ideal para o processamento em milhões de dispositivos onde os dados para o dispositivo e a computação estão colocalizados.

    Os clientes que criaram serviços de IoT com o Service Fabric incluem [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [ Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric), e [sistemas de malha](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Com base em sessão de jogos e aplicativos interativos**: O Service Fabric é ideal se a sua aplicação necessitar de leituras de latência baixa e escritas, tal como em jogos online ou o sistema de mensagens instantâneas. Service Fabric permite-lhe criar esses aplicativos interativos, com monitoração de estado sem ter de criar um armazenamento separado ou uma cache. Visite [soluções de jogos do Azure](https://azure.microsoft.com/solutions/gaming/) para obter orientações de design sobre [utilizar o Service Fabric em serviços de jogos](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

    Os clientes que criaram serviços de jogos incluem [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) e [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Os clientes que criaram sessões interativas incluem [Honeywell com Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)

* **Análise de dados e processamento de fluxo de trabalho**: Aplicações que devem processar de forma fiável eventos ou fluxos de dados também se beneficiam as otimizado de leituras e gravações no Service Fabric. Além disso, o Service Fabric suporta pipelines de processamento do aplicativo, onde os resultados tem de ser fiável e com êxito para o próximo estágio de processamento sem qualquer perda. Estes incluem sistemas transacionais e financeiros, onde as garantias de consistência e a computação de dados são essenciais.

    Os clientes que criaram serviços de fluxo de trabalho empresariais incluem [grupo Zeiss](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform) e [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)

* **Cálculo em dados**: Service Fabric permite-lhe criar aplicações com monitorização de estado de dados computação intensivas. Service Fabric permite que a colocalização de processamento (computação) e dados em aplicativos. Normalmente, quando seu aplicativo exigir acesso a dados, a latência de rede associada a uma camada de cache ou o armazenamento de dados externas limita o tempo de computação. Com os serviços do Service Fabric com monitorização de estado, que a latência é eliminada, permitindo que mais otimizada leituras e escritas. Por exemplo, considere um aplicativo que realiza perto seleções de recomendação em tempo real para os clientes, com um requisito de tempo de ida e volta de menos de 100 milissegundos. As características de desempenho e latência de serviços do Service Fabric (em que o cálculo da seleção de recomendação é colocalizado com os dados e regras) fornece uma experiência responsiva ao usuário em comparação com o modelo de implementação padrão de ter de obter os dados necessários a partir do armazenamento remoto.

    Os clientes que criaram serviços de computação incluem [resposta Solidsoft](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) e [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)

* **Serviços de elevada disponibilidade**: O Service Fabric fornece ativação pós-falha rápida através da criação de várias réplicas de serviço secundário. Se um nó, o processo ou o serviço individual ficar inativa devido a hardware ou outra falha, uma das réplicas secundárias é promovida para uma réplica primária com mínima perda de serviço.

* **Serviços escalonáveis**: Serviços individuais podem ser particionados, permitindo o estado para ser aumentadas horizontalmente em cluster. Além disso, os serviços individuais podem ser criados e removidos dinamicamente. Os serviços podem rápida e facilmente ser dimensionado de algumas instâncias em alguns nós para milhares de instâncias em vários nós e, em seguida, dimensionar novamente, consoante as suas necessidades de recursos. Pode utilizar o Service Fabric para criar estes serviços e gerir os seus ciclos de vida completos.

## <a name="application-design-case-studies"></a>Estudos de caso de design do aplicativo
Um número de estudos de caso que mostra como o Service Fabric é utilizado para conceber aplicações que é publicado no [histórias de clientes](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/) e o [site de soluções de microsserviços](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Conceber aplicações compostas por microsserviços com e sem monitoração de estado
Criar aplicações com funções de trabalho do serviço Cloud do Azure é um exemplo de um serviço sem estado. Por outro lado, os microsserviços com estado mantêm seu estado autoritativo para além do pedido e da respetiva resposta. Esta funcionalidade fornece elevada disponibilidade e consistência do Estado através de APIs simples que fornecem garantias transacionais apoiadas por replicação. Serviços com estado do Service Fabric democratizar a elevada disponibilidade, colocá-los a todos os tipos de aplicativos, não apenas as bases de dados e os outros arquivos de dados. Esta é uma progressão natural. Aplicações já se passaram a utilização de bases de dados totalmente relacionais para elevada disponibilidade para bases de dados NoSQL. Agora, os próprios aplicativos podem ter seus dados geridos dentro dos mesmos para ganhos de desempenho adicionais, sem sacrificar a confiabilidade, consistência ou da disponibilidade e o estado "hot".

Ao criar aplicações compostas por microsserviços, normalmente possuem uma combinação de aplicações web sem monitorização de estado (ASP.NET, node. js, etc.) chamar para serviços corporativos de com e sem monitoração de estado de camada intermediária, implementadas para o mesmo através de cluster de Service Fabric os comandos de implementação do Service Fabric. Cada um desses serviços é independente em relação ao dimensionamento, fiabilidade e uso de recursos, melhorando em grande medida agilidade e flexibilidade no desenvolvimento e gerenciamento do ciclo de vida.

Os microsserviços com estado simplificam designs de aplicativos, pois podem remover a necessidade do adicionais filas e caches que tradicionalmente exigidas para abordar os requisitos de disponibilidade e a latência das aplicações puramente sem monitoração de estado. Como os serviços com estado naturalmente têm elevada disponibilidade e baixa latência, há menos partes móveis para gerir em seu aplicativo como um todo. Os diagramas abaixo mostram as diferenças entre criar uma aplicação que é sem monitoração de estado e um que seja com monitoração de estado. Aproveitando os [Reliable Services](service-fabric-reliable-services-introduction.md) e [Reliable Actors](service-fabric-reliable-actors-introduction.md) modelos de programação, serviços com estado reduzem a complexidade do aplicativo mantendo um débito elevado e baixa latência.

## <a name="an-application-built-using-stateless-services"></a>Um aplicativo criado usando serviços sem estado
![Aplicação com o serviço sem estado][Image1]

## <a name="an-application-built-using-stateful-services"></a>Um aplicativo criado com os serviços com estado
![Aplicação com o serviço sem estado][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos Seguintes

* Leia sobre [casos práticos de clientes](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc)
* Saiba mais sobre [padrões e cenários](service-fabric-patterns-and-scenarios.md)

* Introdução à criação de serviços com e sem estado do Service Fabric [serviços fiáveis](service-fabric-reliable-services-quick-start.md) e [dos reliable actors](service-fabric-reliable-actors-get-started.md) modelos de programação.
* Visite o Centro de arquitetura do Azure para obter orientações sobre [a criação de microsserviços no Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* Aceda a [melhores práticas de aplicação do Azure Service Fabric e o cluster](service-fabric-best-practices-overview.md) para obter orientações de design do aplicativo.

* Consulte também os tópicos seguintes:
  * [Mais informações sobre os microsserviços](service-fabric-overview-microservices.md)
  * [Definir e gerir o estado do serviço](service-fabric-concepts-state.md)
  * [Disponibilidade dos serviços do Service Fabric](service-fabric-availability-services.md)
  * [Serviços do Service Fabric de dimensionamento](service-fabric-concepts-scalability.md)
  * [Serviços do Service Fabric de partição](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
