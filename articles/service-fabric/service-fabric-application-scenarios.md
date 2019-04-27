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
ms.date: 7/02/2017
ms.author: atsenthi
ms.openlocfilehash: c9b2f9ac131e71b7c6b37ed85568adc0c3978dc2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621229"
---
# <a name="service-fabric-application-scenarios"></a>Cenários de aplicações do Service Fabric
O Azure Service Fabric oferece uma plataforma fiável e flexível que lhe permite escrever e executar muitos tipos de aplicações e serviços empresariais. Estas aplicações e os microsserviços podem ser com ou sem estado, e são com balanceamento de recurso em máquinas virtuais para maximizar a eficiência. A arquitetura única do Service Fabric permite-lhe efetuar perto de análise de dados em tempo real, computação dentro da memória, transações paralelas e em seus aplicativos de processamento de eventos. Pode facilmente dimensionar as suas aplicações ou reduzir verticalmente (realmente a entrada ou saída), dependendo dos requisitos de recursos de alteração.

A plataforma do Service Fabric no Azure é ideal para as seguintes categorias de aplicações:

* **Serviços de elevada disponibilidade**: Serviços do Service Fabric fornecem ativação pós-falha rápida através da criação de várias réplicas de serviço secundário. Se um nó, o processo ou o serviço individual ficar inativa devido a hardware ou outra falha, uma das réplicas secundárias é promovida para uma réplica primária com mínima perda de serviço.
* **Serviços escalonáveis**: Serviços individuais podem ser particionados, permitindo o estado para ser aumentadas horizontalmente em cluster. Além disso, os serviços individuais podem ser criados e removidos dinamicamente. Serviços podem ser rápida e facilmente aumentados horizontalmente de algumas instâncias em alguns nós a milhares de instâncias em vários nós e, em seguida, dimensionar novamente, consoante as suas necessidades de recursos. Pode utilizar o Service Fabric para criar estes serviços e gerir os ciclos de vida completos.
* **Computação em colocam dados**: Service Fabric permite-lhe dados de compilação, entrada/saída e aplicações com monitorização de estado intensivas em termos de computação. Service Fabric permite que a colocalização de processamento (computação) e dados em aplicativos. Normalmente, quando seu aplicativo exigir acesso a dados, existe latência de rede associada a uma camada de cache ou o armazenamento de dados externa. Com os serviços do Service Fabric com monitorização de estado, que a latência é eliminada, permitir um melhor desempenho leituras e escritas. Digamos que tenha um aplicativo que realiza perto seleções de recomendação em tempo real para os clientes, com um requisito de tempo de ida e volta de menos de 100 milissegundos. As características de desempenho e latência de serviços do Service Fabric (em que a computação de seleção de recomendação estiver colocalizada com os dados e regras) fornece uma experiência responsiva ao usuário em comparação com o modelo de implementação padrão de ter de obter os dados necessários a partir do armazenamento remoto.  
* **Aplicativos interativos baseados em sessão**: O Service Fabric é útil se as suas aplicações, como jogos online ou o serviço de mensagens instantâneas, requerem baixa latência leituras e gravações. Service Fabric permite-lhe criar esses aplicativos interativos, com monitoração de estado sem ter de criar um armazenamento separado ou cache, conforme necessário para as aplicações sem monitorização de estado. (Isso aumenta a latência e potencialmente apresenta problemas de consistência.).
* **Análise de dados e fluxos de trabalho**: As leituras rápidas e gravações do Service Fabric permitem aplicações que devem processar de forma fiável eventos ou fluxos de dados. Service Fabric também permite que os aplicativos que descrevem os pipelines de processamento, onde os resultados tem de ser fiável e com êxito para o próximo estágio de processamento sem perdas. Estes incluem sistemas transacionais e financeiros, onde as garantias de consistência e a computação de dados são essenciais.
* **Recolha de dados, processamento e de IoT**: Uma vez que processa o dimensionamento de grande escala e tem de baixa latência através de seus serviços com monitoração de estado do Service Fabric, é ideal para o processamento em milhões de dispositivos onde os dados para o dispositivo e a computação estão localizados conjuntamente.
Vimos vários clientes que criaram os sistemas de IoT com o Service Fabric incluindo [BMW](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/24/service-fabric-customer-profile-bmw-technology-corporation/), [Schneider Electric](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/05/service-fabric-customer-profile-schneider-electric/) e [Mesh sistemas](https://blogs.msdn.microsoft.com/azureservicefabric/2016/06/20/service-fabric-customer-profile-mesh-systems/).

## <a name="application-design-case-studies"></a>Estudos de caso de design do aplicativo
Um número de estudos de caso que mostra como o Service Fabric é utilizado para conceber aplicações é publicado com o [blogue da equipa do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/) e o [site de soluções de microsserviços](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Conceber aplicações compostas por microsserviços com e sem monitoração de estado
Criar aplicações com funções de trabalho do serviço Cloud do Azure é um exemplo de um serviço sem estado. Por outro lado, os microsserviços com estado mantêm seu estado autoritativo para além do pedido e da respetiva resposta. Esta opção fornece elevada disponibilidade e consistência do Estado através de APIs simples que fornecem garantias transacionais apoiadas por replicação. Serviços com estado do Service Fabric democratizar a elevada disponibilidade, colocá-los a todos os tipos de aplicativos, não apenas as bases de dados e os outros arquivos de dados. Esta é uma progressão natural. Aplicações já se passaram a utilização de bases de dados totalmente relacionais para elevada disponibilidade para bases de dados NoSQL. Agora, os próprios aplicativos podem ter seus dados geridos dentro dos mesmos para ganhos de desempenho adicionais, sem sacrificar a confiabilidade, consistência ou da disponibilidade e o estado "hot".

Ao criar aplicações compostas por microsserviços, normalmente possuem uma combinação de aplicações web sem monitorização de estado (ASP.NET, node. js, etc.) chamar para serviços corporativos de com e sem monitoração de estado de camada intermediária, implementadas para o mesmo através de cluster de Service Fabric os comandos de implementação do Service Fabric. Cada um desses serviços é independente em relação à utilização de dimensionamento, fiabilidade e recursos, melhorando em grande medida agilidade no desenvolvimento e o ciclo de vida de gestão.

Os microsserviços com estado simplificam designs de aplicativos, pois podem remover a necessidade do adicionais filas e caches que tradicionalmente exigidas para abordar os requisitos de disponibilidade e a latência das aplicações puramente sem monitoração de estado. Uma vez que os serviços com estado naturalmente são de elevada disponibilidade e baixa latência, isso significa que existem menos partes móveis para gerir em seu aplicativo como um todo. Os diagramas abaixo mostram as diferenças entre criar uma aplicação que é sem monitoração de estado e um que seja com monitoração de estado. Aproveitando os [Reliable Services](service-fabric-reliable-services-introduction.md) e [Reliable Actors](service-fabric-reliable-actors-introduction.md) modelos de programação, serviços com estado reduzem a complexidade do aplicativo mantendo um débito elevado e baixa latência.

## <a name="an-application-built-using-stateless-services"></a>Um aplicativo criado usando serviços sem estado
![Aplicação com o serviço sem estado][Image1]

## <a name="an-application-built-using-stateful-services"></a>Um aplicativo criado com os serviços com estado
![Aplicação com o serviço sem estado][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos Seguintes

* Leia sobre [casos práticos de clientes](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/)
* Saiba mais sobre [padrões e cenários](service-fabric-patterns-and-scenarios.md)

* Introdução à criação de serviços com e sem estado do Service Fabric [serviços fiáveis](service-fabric-reliable-services-quick-start.md) e [dos reliable actors](service-fabric-reliable-actors-get-started.md) modelos de programação.
* Consulte também os tópicos seguintes:
  * [Mais informações sobre os microsserviços](service-fabric-overview-microservices.md)
  * [Definir e gerir o estado do serviço](service-fabric-concepts-state.md)
  * [Disponibilidade dos serviços do Service Fabric](service-fabric-availability-services.md)
  * [Serviços do Service Fabric de dimensionamento](service-fabric-concepts-scalability.md)
  * [Serviços do Service Fabric de partição](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
