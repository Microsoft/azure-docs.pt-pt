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
ms.date: 06/18/2019
ms.author: msfussell
ms.openlocfilehash: 30d696337061ade6b79c7ec0e4c4de67651f0dad
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203445"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Service Fabric application design melhores práticas do Azure

Este artigo fornece diretrizes de práticas recomendadas para a criação de aplicativos e serviços no Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Familiarize-se com o Service Fabric
* Leitura a [pelo que deve saber sobre o Service Fabric?](service-fabric-content-roadmap.md) artigo.
* Leia sobre [cenários de aplicações do Service Fabric](service-fabric-application-scenarios.md).
* Compreender as opções de modelo de programação lendo [descrição geral do modelo de programação do Service Fabric](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Orientações de conceção da aplicação
Familiarizar-se com o [arquitetura geral](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) de aplicações do Service Fabric e os respetivos [considerações de design](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Escolha um gateway de API
Utilize um serviço de gateway de API que comunica com os serviços de back-end que, em seguida, podem ser ampliados. Os serviços de gateway de API mais comuns utilizados são:

- [Gestão de API do Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), que é [integrado com o Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management).
- [O IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub/) ou [Hubs de eventos](https://docs.microsoft.com/azure/event-hubs/), com o [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) para ler a partir de partições do Hub de eventos.
- [Proxy inverso do Træfik](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/), utilizando o [fornecedor de Azure Service Fabric](https://docs.traefik.io/configuration/backends/servicefabric/).
- [Gateway de aplicação do Azure](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > O Gateway de aplicação do Azure não está diretamente integrado com o Service Fabric. Gestão de API do Azure é, normalmente, a opção preferencial.
- Sua própria personalizados internos [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) gateway de aplicação web.

### <a name="stateless-services"></a>Serviços sem estado
Recomendamos que comece sempre com a criação de serviços sem estado, utilizando [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) e armazenar o estado numa base de dados do Azure, o Azure Cosmos DB ou o armazenamento do Azure. Estado externalized é a abordagem mais familiar para a maioria dos desenvolvedores. Essa abordagem também permite-lhe tirar partido das capacidades de consulta na loja.  

### <a name="when-to-use-stateful-services"></a>Quando utilizar os serviços com estado
Quando tiver um cenário de baixa latência e precisar de manter os dados perto a computação, considere serviços com estado. Alguns cenários de exemplo incluem dispositivos de gémeo digital do IoT, o estado do jogo, o estado da sessão, colocação em cache dados a partir de uma base de dados e fluxos de trabalho de execução longa para controlar as chamadas para outros serviços.

Decidir qual o intervalo de tempo de retenção de dados:

- **Dados em cache**. Utilize a colocação em cache quando a latência em arquivos externos é um problema. Utilize um serviço com estado como sua própria cache de dados ou considere a utilização a [aberto SoCreate Service Fabric Distributed Cache](https://github.com/SoCreate/service-fabric-distributed-cache). Neste cenário, não precisa se preocupar se perder a todos os dados na cache.
- **Dados de limite de tempo**. Neste cenário, precisa de manter dados perto de si para computação durante um período de tempo para latência, mas pode pagar perder os dados num *desastre*. Por exemplo, em muitas soluções de IoT, dados tem de ser fechar para computação, tais como quando a temperatura média ao longo dos últimos dias alguns está sendo calculada, mas se esses dados são perdidos, os pontos de dados específico registados não são tão importantes. Além disso, este cenário não normalmente se preocupa backup os pontos de dados individuais. Apenas a criar cópias de segurança valores de média calculadas que periodicamente são escritos no armazenamento externo.  
- **Dados de longo prazo**. As coleções fiáveis armazenar os seus dados permanentemente. Mas nesse caso precisa [preparar a recuperação após desastre](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery), incluindo [configurar políticas de cópia de segurança periódicas](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) para os seus clusters. Na verdade, configure o que acontece se o cluster for destruído num desastre, em que terá de criar um novo cluster e como implementar novas instâncias da aplicação e recuperar a partir da cópia de segurança mais recente.

Reduzir os custos e melhorar a disponibilidade:
- Pode reduzir os custos com os serviços com monitoração de estado porque que não incorra acesso a dados e os custos de transações a partir da loja remota e, uma vez que não precisa de utilizar outro serviço, como o Cache do Azure para Redis.
- Utilizar serviços com estado, principalmente para armazenamento e não para a computação é cara, e não o recomendamos. Considere serviços com estado como computação com armazenamento local barato.
- Ao remover dependências noutros serviços, pode melhorar a disponibilidade do serviço. Gerenciamento de estado com HA no cluster, isola de outros tempos de inatividade do serviço ou problemas de latência.

## <a name="how-to-work-with-reliable-services"></a>Como trabalhar com o Reliable Services
Service Fabric Reliable Services permite-lhe criar facilmente serviços com e sem estado. Para obter mais informações, consulte a [introdução ao Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction).
- Sempre honrar os [token de cancelamento](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) no `RunAsync()` método para serviços com e sem estado e o `ChangeRole()` método para serviços com estado. Caso contrário, o Service Fabric não sabe se o seu serviço pode ser fechado. Por exemplo, se não a honrar o token de cancelamento, podem ocorrer muito mais tempo tempos de atualização da aplicação.
-   Abrir e fechar [serviços de escuta de comunicação](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) de forma atempada e irá cumprir os tokens de cancelamento.
-   Nunca misture o código de sincronização com o código assíncrono. Por exemplo, não utilize `.GetAwaiter().GetResult()` em suas chamadas de async. Utilizar o async *totalmente* através da pilha de chamada.

## <a name="how-to-work-with-reliable-actors"></a>Como trabalhar com Reliable Actors
Reliable Actors do Service Fabric permite-lhe criar facilmente atores com monitoração de estado, virtual. Para obter mais informações, consulte a [introdução ao Reliable Actors](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction).

- Considere seriamente a utilização de pub/sub de mensagens entre seus atores para dimensionar seu aplicativo. As ferramentas que fornecem este serviço incluem o [aberto SoCreate Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/) e [do Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).
- Verifique o estado de ator como [granular possível](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Gerir o [ciclo de vida do ator](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Elimine atores se não vai utilizá-los novamente. A eliminar atores desnecessários é especialmente importante quando estiver a utilizar o [fornecedor de estado volátil](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication), porque todo o estado é armazenado na memória.
- Devido seus [turnos simultaneidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency), atores são mais úteis como objetos independentes. Não crie gráficos de chamadas de métodos de ator multi, síncrona (cada um deles provavelmente se torna uma chamada de rede separada) ou criar pedidos de ator circular. Estes afetará significativamente desempenho e dimensionamento.
- Não misture o código de sincronização com o código assíncrono. Utilize o async consistentemente para evitar problemas de desempenho.
- Não faça chamadas de longa execução em atores. Chamadas de longa execução irão bloquear outras chamadas para o ator mesmo, devido à simultaneidade turnos.
- Se estiver a comunicar com outros serviços utilizando [comunicação remota do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) e que está a criar um `ServiceProxyFactory`, criar a fábrica no [serviço de ator](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) nível e *não* ao nível do ator.


## <a name="application-diagnostics"></a>Diagnóstico de aplicação
Ser criterioso sobre como adicionar [registo de aplicações](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) em chamadas de serviço. Ele irá ajudar a diagnosticar cenários em que os serviços chamam uns aos outros. Por exemplo, quando um chamadas de chamadas B C chama D, a chamada pode falhar em qualquer lugar. Se não tiver suficiente registo, falhas são difíceis de diagnosticar. Se os serviços estão a iniciar muito devido a chamada de volumes, certifique-se de que, pelo menos, registar erros e avisos.

## <a name="iot-and-messaging-applications"></a>IoT e aplicações de mensagens
Quando estiver lendo as mensagens de [IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub/) ou [Event Hubs do Azure](https://docs.microsoft.com/azure/event-hubs/), utilize [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor). ServiceFabricProcessor se integra com o Service Fabric Reliable Services, para manter o estado de leitura do hub de eventos cria partições e envia por push novas mensagens aos seus serviços através de `IEventProcessor::ProcessEventsAsync()` método.


## <a name="design-guidance-on-azure"></a>Orientações de conceção no Azure
* Visite o [Centro de arquitetura do Azure](https://docs.microsoft.com/azure/architecture/microservices/) para obter orientações de design sobre [criação de microsserviços no Azure](https://docs.microsoft.com/azure/architecture/microservices/).

* Visite [introdução ao Azure para jogos](https://docs.microsoft.com/gaming/azure/) para obter orientações de design sobre [com o Service Fabric em serviços de jogos](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).
