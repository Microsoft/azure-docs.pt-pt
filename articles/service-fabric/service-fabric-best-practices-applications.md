---
title: O Azure Service Fabric application melhores práticas para conceção | Documentos da Microsoft
description: Melhores práticas para o desenvolvimento de aplicações do Service Fabric.
services: service-fabric
documentationcenter: .net
author: markfussell
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/26/2019
ms.author: msfussell
ms.openlocfilehash: 55f043effc7cdb102acea856e89c58f660d0cde5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65237751"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Service Fabric application design melhores práticas do Azure

Este artigo fornece diretrizes de práticas recomendadas para a criação de aplicativos e serviços no Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Familiarize-se com o Service Fabric
* [Portanto, quiser saber mais sobre o Service Fabric?](service-fabric-content-roadmap.md)
* Leia sobre [cenários de aplicações do Service Fabric](service-fabric-application-scenarios.md)
* Em seguida, compreender as opções de modelo de programação com [visão geral do modelo de programação do Service Fabric](service-fabric-choose-framework.md)



## <a name="application-design-guidance"></a>Orientações de conceção da aplicação
Familiarizar-se com o [arquitetura geral](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) de uma aplicação do Service Fabric e a respetiva [considerações de design](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Escolha um gateway de API
Utilize um serviço de gateway de API que comunica com os serviços de back-end que, em seguida, podem ser ampliados. Os serviços de gateway de API mais comuns utilizados são:

- [Gestão de API do Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), que é [integrado com o Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)
- [IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub/) ou [Hubs de eventos](https://docs.microsoft.com/azure/event-hubs/) com o [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) para ler a partir de partições do Hub de eventos
- [Proxy inverso do Træfik](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/) usando o [fornecedor do Azure Service Fabric](https://docs.traefik.io/configuration/backends/servicefabric/)
- [O Gateway de aplicação do Azure](https://docs.microsoft.com/azure/application-gateway/) Nota: Isto não está diretamente integrado com o Service Fabric e a API Management do Azure é, normalmente, uma opção preferencial
- Crie a sua própria [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) gateway de aplicação web

### <a name="choose-stateless-services"></a>Escolha serviços sem estado
Recomendamos que comece sempre pelos serviços sem estado criar usando [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) armazenando o estado de uma base de dados do Azure, Azure cosmos DB ou o armazenamento do Azure. Ter estado externalized é a abordagem mais familiar para a maioria dos desenvolvedores e permite-lhe também tirar partido das capacidades de consulta na loja.  

### <a name="when-to-choose-stateful-services"></a>Quando escolher a serviços com estado
Quando tiver um cenário de baixa latência e precisar de manter os dados perto a computação, considere serviços com estado. Alguns exemplos incluem dispositivos de gémeo digital do IoT, o estado do jogo, o estado da sessão, colocação em cache dados a partir de uma base de dados e fluxos de trabalho para controlar as chamadas para outros serviços de longa execução.

Decida o intervalo de tempo de retenção de dados:

- Dados armazenados em cache - utilizar a colocação em cache em que a latência em arquivos externos é um problema. Utilizar um serviço com estado, à medida que os seus dados em cache ou considere a utilização a [open source SoCreate service-fabric-cache distribuída do-](https://github.com/SoCreate/service-fabric-distributed-cache). Neste cenário, pode perder todos os dados na cache e não importa.
- Dados de limite de tempo - tem de manter os dados perto de si para computação de latência durante um período de tempo, mas esses dados podem se dar ao luxo perdidos num *desastre* cenário. Por exemplo, em muitas soluções de IoT dados tem de ser fechar para computação, por exemplo calcular a temperatura média ao longo dos últimos dias, no entanto, se estes dados são perdidos, em seguida, os dados específicos de pontos de registadas não é tão importante. Também neste cenário, não é normalmente importantes sobre a cópia de segurança dos pontos de dados individuais, apenas dos valores média calculados que são escritos no armazenamento externo periodicamente.  
- Dados de longa duração - coleções fiáveis armazenar os seus dados permanentemente. No entanto, neste caso terá [preparar a recuperação após desastre](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery) incluindo [configurar políticas de cópia de segurança periódicas](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) para os seus clusters. Na verdade, configure o que acontece se o cluster for destruído num cenário de desastres, em que terá de criar um novo cluster e como implementar novas instâncias da aplicação e recuperar a partir da cópia de segurança mais recente.

Reduzir os custos e melhorar a disponibilidade:
- Pode reduzir os custos com os serviços com estado, uma vez que não a incorrer em custos de acesso e transações de dados do arquivo remoto e não é necessário para utilizar outro serviço como de Redis do Azure.
- Utilizar serviços com estado, principalmente para armazenamento e não para a computação é Caro e não se recomenda. Considere a serviços com estado como computação com armazenamento local barato.
- Ao remover dependências noutros serviços, pode melhorar a disponibilidade do serviço. Ter o estado gerido com HA no cluster, isola de outros tempos de inatividade do serviço ou problemas de latência.

## <a name="how-to-properly-work-with-reliable-services"></a>Como trabalhar corretamente com o Reliable Services
Reliable Services permitem-lhe criar facilmente serviços com e sem estado. Leia o [introdução ao Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)
- Sempre honrar os [token de cancelamento](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) no `RunAsync()` método para serviços com e sem estado e o `ChangeRole()` método para serviços com estado. Sem isso, o Service Fabric não sabe se o seu serviço pode ser fechado. Por exemplo, não respeitar o token de cancelamento pode resultar em tempos muito mais tempo de atualização da aplicação.
-   Abrir e fechar [serviços de escuta de comunicação](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) atempadamente e irá cumprir os tokens de cancelamento.
-   Nunca misture o código de sincronização com o código assíncrono. Por exemplo, não utilize `.GetAwaiter().GetResult()` em suas chamadas de async; ele precisa ser async *totalmente* através da pilha de chamada.

## <a name="how-to-properly-work-with-reliable-actors"></a>Como trabalhar corretamente com o Reliable Actors
Reliable Actors permite-lhe criar facilmente atores com monitoração de estado, virtual. Leia o [introdução ao Reliable Actors](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)

- Considere vivamente a utilização de pub/sub de mensagens entre seus atores para dimensionar o aplicativo. Por exemplo, o [open source SoCreate pub/sub](https://service-fabric-pub-sub.socreate.it/) ou [do Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).
- Verifique o estado de ator como [granular possível](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Gerir o [ciclo de vida do ator](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Elimine atores se não vai utilizá-los novamente. Isto é especialmente verdadeiro quando utilizar o [VolatileState fornecedor](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication) como todo o estado é armazenado na memória.
- Devido a seus [vez com base em simultaneidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency) atores são mais úteis como objetos independentes. Não crie gráficos de chamadas de métodos de ator multi, síncrona (cada um deles provavelmente se torna uma chamada de rede separada) ou têm pedidos de ator circular; Estes afetará significativamente desempenho e dimensionamento.
- Não misturar código de sincronização com o código de async; ele precisa ser async todo o caminho, para evitar problemas de desempenho.
- Não faça chamadas de execução demorada no actors, ele irá bloquear outras chamadas para o ator mesmo, devido à simultaneidade turnos.
- Se comunicar com outros serviços que utilizam [comunicação remota do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) e está a criar um `ServiceProxyFactory`, em seguida, criar a fábrica no [serviço de ator](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) nível e *não*no nível de ator.


## <a name="application-diagnostics"></a>Diagnóstico de aplicação
- Ser completa no adicionando [registo de aplicações](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) em chamadas de serviço. Ele ajuda no diagnóstico de cenários em que serviços chamam uns aos outros. Por exemplo, quando A -> B -> C -> D a chamada pode falhar em qualquer lugar; Se não for suficiente. o Registro em log, é difícil de diagnosticar. Se os serviços estão a iniciar muito devido a chamada de volumes, pelo menos não se esqueça de registar erros e avisos.

## <a name="iot-and-messaging-applications"></a>IoT e aplicações de mensagens
Ao ler as mensagens de [IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub/) ou [Event Hubs do Azure](https://docs.microsoft.com/azure/event-hubs/) utilizar [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) que se integra com o Service Fabric Reliable Services, para manter o Estado de leitura do Hub de eventos cria partições e envia por push novas mensagens aos seus serviços através de `IEventProcessor::ProcessEventsAsync()` método.


## <a name="design-guidance-on-azure"></a>Orientações de conceção no Azure
* Visite o [Centro de arquitetura do Azure](https://docs.microsoft.com/azure/architecture/microservices/) para obter orientações de design sobre [a criação de microsserviços no Azure](https://docs.microsoft.com/azure/architecture/microservices/)

* Visite [introdução ao Azure para jogos](https://docs.microsoft.com/gaming/azure/) para obter orientações de design sobre [utilizar o Service Fabric em serviços de jogos](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)
