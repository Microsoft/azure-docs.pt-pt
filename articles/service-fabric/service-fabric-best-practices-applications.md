---
title: As melhores práticas de design de aplicações da Azure Service Fabric
description: Boas práticas e considerações de design para o desenvolvimento de aplicações e serviços usando o Tecido de Serviço Azure.
author: markfussell
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: mfussell
ms.openlocfilehash: 876980bd6a59bace9ab4e490358964d19fa52c7e
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586092"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>As melhores práticas de design de aplicações da Azure Service Fabric

Este artigo fornece orientações de boas práticas para aplicações e serviços de construção em Tecido de Serviço Azure.
 
## <a name="get-familiar-with-service-fabric"></a>Conheça o Tecido de Serviço
* Ler o [Artigo do So quer aprender sobre o Tecido de Serviço?](service-fabric-content-roadmap.md)
* Ler sobre cenários de [aplicação de Tecido de Serviço](service-fabric-application-scenarios.md).
* Compreenda as escolhas do modelo de programação através da leitura do modelo de [programação do Tecido de Serviço.](service-fabric-choose-framework.md)



## <a name="application-design-guidance"></a>Orientação de design de aplicações
Familiarize-se com a [arquitetura geral](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) das aplicações de Tecido de Serviço e as suas [considerações de design.](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)

### <a name="choose-an-api-gateway"></a>Escolha um gateway DaPI
Utilize um serviço de gateway API que comunica com serviços de back-end que podem ser dimensionados. Os serviços de gateway da API mais comuns utilizados são:

- [Azure API Management,](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview)que está [integrado com o Tecido de Serviço.](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)
- [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) ou [Azure Event Hubs,](https://docs.microsoft.com/azure/event-hubs/)utilizando o [ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor) para ler a partir de divisórias do Event Hub.
- [Procuração inversa træfik,](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/)utilizando o [fornecedor Azure Service Fabric](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/).
- [Gateway de aplicação Azure](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > O Portal de Aplicações Azure não está integrado diretamente com o Tecido de Serviço. A Azure API Management é tipicamente a escolha preferida.
- O seu próprio portal de aplicações [web core ASP.NET](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) personalizado.

### <a name="stateless-services"></a>Serviços apátridas
Recomendamos que comece sempre por construir serviços apátridas utilizando [Serviços Fiáveis](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) e armazenando o estado numa base de dados Azure, Azure Cosmos DB ou Azure Storage. O estado externo é a abordagem mais familiar para a maioria dos desenvolvedores. Esta abordagem também lhe permite tirar partido das capacidades de consulta na loja.  

### <a name="when-to-use-stateful-services"></a>Quando utilizar serviços estatais
Considere serviços estatais quando tiver um cenário de baixa latência e precise manter os dados perto da computação. Alguns cenários de exemplo incluem dispositivos duplos digitais IoT, estado de jogo, estado de sessão, dados de cache de uma base de dados, e fluxos de trabalho de longo prazo para rastrear chamadas para outros serviços.

Decida sobre o prazo de retenção de dados:

- **Dados em cache.** Use o cache quando a latência para lojas externas é um problema. Utilize um serviço imponente como cache de dados próprio, ou considere utilizar a cache distribuída de tecido de [serviço SoCreate de código aberto .](https://github.com/SoCreate/service-fabric-distributed-cache) Neste cenário, não precisa de se preocupar se perder todos os dados na cache.
- **Dados com limite de tempo.** Neste cenário, é necessário manter os dados perto da computação por um período de tempo para a latência, mas pode dar-se ao luxo de perder os dados numa *catástrofe*. Por exemplo, em muitas soluções IoT, os dados precisam estar perto da computação, como quando a temperatura média dos últimos dias está a ser calculada, mas se estes dados forem perdidos, os pontos de dados específicos registados não são assim tão importantes. Além disso, neste cenário, normalmente não se importa em apoiar os pontos de dados individuais. Basta apoiar valores médios calculados que são periodicamente escritos para armazenamento externo.  
- **Dados a longo prazo.** Coleções fiáveis podem armazenar os seus dados permanentemente. Mas neste caso precisa de se preparar para a recuperação de [desastres](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery), incluindo [configurar políticas periódicas](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) de backup para os seus clusters. Com efeito, configura o que acontece se o seu cluster for destruído num desastre, onde precisaria de criar um novo cluster, e como implementar novas instâncias de aplicação e recuperar dos mais recentes backups.

Poupe custos e melhore a disponibilidade:
- Você pode reduzir custos usando serviços imponentes porque você não incorre em custos de acesso de dados e transações da loja remota, e porque você não precisa usar outro serviço, como Azure Cache para Redis.
- Usar serviços imponentes principalmente para armazenamento e não para computação é caro, e não o recomendamos. Pense em serviços imponentes como computação com armazenamento local barato.
- Ao remover dependências de outros serviços, pode melhorar a disponibilidade do seu serviço. Gerir o estado com HA no cluster isola-o de outros tempos de serviço ou problemas de latência.

## <a name="how-to-work-with-reliable-services"></a>Como trabalhar com Serviços Fiáveis
Serviço Fabric Reliable Services permite-lhe facilmente criar serviços apátridas e apátridas. Para mais informações, consulte a [introdução de Serviços Fiáveis.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)
- Honre sempre o símbolo de [cancelamento](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) no método `RunAsync()` para serviços apátridas e apátridas e o método `ChangeRole()` para serviços estatais. Se não o fizer, o Serviço Fabric não sabe se o seu serviço pode ser fechado. Por exemplo, se não honrar o símbolo de cancelamento, podem ocorrer tempos de atualização de aplicações muito mais longos.
-   Ouvintes de comunicação abertos e [próximos](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) em tempo oportuno, e honrem os símbolos de cancelamento.
-   Nunca misture código de sincronização com código assync. Por exemplo, não use `.GetAwaiter().GetResult()` nas suas chamadas de asincronização. Use assinásdurante *todo o caminho* através da pilha de chamadas.

## <a name="how-to-work-with-reliable-actors"></a>Como trabalhar com atores fiáveis
Service Fabric Reliable Actors permite-lhe criar facilmente atores virtuais e imponentes. Para mais informações, consulte a [introdução aos Atores Fiáveis.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)

- Considere seriamente usar mensagens de pub/sub entre os seus atores para escalar a sua aplicação. As ferramentas que fornecem este serviço incluem o [Open-Source Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/) e [o Azure Service Bus.](https://docs.microsoft.com/azure/service-bus/)
- Faça o ator afirmar o mais [granular possível.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)
- Gerencie o [ciclo de vida do ator.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices) Apague os atores se não os voltar a usar. Apagar atores desnecessários é especialmente importante quando se está a usar o [fornecedor estatal volátil](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication), porque todo o Estado está guardado na memória.
- Devido à sua [conmoeda baseada na viragem,](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency)os atores são melhor usados como objetos independentes. Não crie gráficos de chamadas de métodos sincronizados e sincronizados (cada uma das quais provavelmente torna-se uma chamada de rede separada) ou crie pedidos de ator circular. Estes afetarão significativamente o desempenho e a escala.
- Não misture código de sincronização com código assiníncrono. Utilize assinás de forma consistente para evitar problemas de desempenho.
- Não faças chamadas de longa data em atores. Chamadas de longa duração bloquearão outras chamadas para o mesmo ator, devido à conmoeda baseada na viragem.
- Se está a comunicar com outros serviços usando o [Service Fabric remoting](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) e está a criar um `ServiceProxyFactory`, crie a fábrica ao nível [do serviço de ator](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) e *não* ao nível do ator.


## <a name="application-diagnostics"></a>Diagnósticos de aplicações
Seja minuciosa sobre a adição de registo de [aplicações](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) em chamadas de serviço. Vai ajudá-lo a diagnosticar cenários em que os serviços se ligam uns aos outros. Por exemplo, quando a Chamada B chama C, a chamada pode falhar em qualquer lugar. Se não tiver registo sinuoso suficiente, falhas são difíceis de diagnosticar. Se os serviços estiverem a registar demasiado por causa dos volumes de chamadas, certifique-se de que regista, pelo menos, erros e avisos.

## <a name="iot-and-messaging-applications"></a>IoT e aplicações de mensagens
Quando estiver a ler mensagens do [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) ou [do Azure Event Hubs,](https://docs.microsoft.com/azure/event-hubs/)utilize o [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor). ServiceFabricProcessor integra-se com Serviços Fiáveis de Tecido de Serviço para manter o estado de leitura das divisórias do centro de eventos e empurra novas mensagens para os seus serviços através do método `IEventProcessor::ProcessEventsAsync()`.


## <a name="design-guidance-on-azure"></a>Orientação de design em Azure
* Visite o centro de [arquitetura Azure](https://docs.microsoft.com/azure/architecture/microservices/) para orientação de design sobre a construção de [microserviços no Azure.](https://docs.microsoft.com/azure/architecture/microservices/)

* Visite [O Get Started com o Azure para gaming](https://docs.microsoft.com/gaming/azure/) para orientação de design na [utilização de Tecido de Serviço nos serviços de jogos.](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)
