---
title: Azure Service Fabric design de boas práticas
description: Boas práticas e considerações de design para o desenvolvimento de aplicações e serviços utilizando o Azure Service Fabric.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: ddf846e9e3ac6add7cf3f584b702de5accfb22af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91538503"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric design de boas práticas

Este artigo fornece orientações de boas práticas para a construção de aplicações e serviços no Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Familiarize-se com o Tecido de Serviço
* Leia o [Artigo So you want to learn about Service Fabric?](service-fabric-content-roadmap.md)
* Leia sobre [os cenários de aplicação do Service Fabric](service-fabric-application-scenarios.md).
* Compreenda as escolhas do modelo de programação lendo a visão geral do [modelo de programação do Service Fabric](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Orientação de design de aplicativos
Familiarize-se com a [arquitetura geral](/azure/architecture/reference-architectures/microservices/service-fabric) das aplicações de Tecido de Serviço e as suas [considerações de design.](/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)

### <a name="choose-an-api-gateway"></a>Escolha um gateway API
Utilize um serviço de gateway API que comunica a serviços back-end que podem ser dimensionado. Os serviços de gateway da API mais comuns utilizados são:

- [AZure API Management,](./service-fabric-api-management-overview.md)que está [integrada com o Service Fabric.](./service-fabric-tutorial-deploy-api-management.md)
- [Azure IoT Hub](../iot-hub/index.yml) ou [Azure Event Hubs,](../event-hubs/index.yml)utilizando o [ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor) para ler as divisórias do Event Hub.
- [Procuração inversa Træfik,](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)utilizando o [fornecedor de tecido de serviço Azure](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/).
- [Gateway de aplicação Azure](../application-gateway/index.yml).

   > [!NOTE] 
   > O Azure Application Gateway não está diretamente integrado com o Service Fabric. A Azure API Management é tipicamente a escolha preferida.
- Seu próprio porta de aplicação [web core ASP.NET](./service-fabric-reliable-services-communication-aspnetcore.md) personalizado.

### <a name="stateless-services"></a>Serviços apátridas
Recomendamos que comece sempre por construir serviços apátridas utilizando [serviços fiáveis](./service-fabric-reliable-services-introduction.md) e armazenando o estado numa base de dados Azure Cosmos DB ou Azure Storage. Estado externalizado é a abordagem mais familiar para a maioria dos desenvolvedores. Esta abordagem também permite tirar partido das capacidades de consulta na loja.  

### <a name="when-to-use-stateful-services"></a>Quando utilizar serviços estatais
Considere os serviços estatais quando tem um cenário de baixa latência e precisa manter os dados perto do cálculo. Alguns cenários de exemplo incluem dispositivos gémeos digitais IoT, estado de jogo, estado de sessão, dados de caching de uma base de dados, e fluxos de trabalho de longa duração para rastrear chamadas para outros serviços.

Decidir sobre o prazo de retenção de dados:

- **Dados em cache.** A utilização de caching quando a latência em lojas externas é um problema. Utilize um serviço estatal como cache de dados próprio, ou considere utilizar a [cache distribuída do tecido de serviço socreate de código aberto](https://github.com/SoCreate/service-fabric-distributed-cache). Neste cenário, não precisa de se preocupar se perder todos os dados da cache.
- **Dados com limite de tempo.** Neste cenário, é necessário manter os dados perto do cálculo durante um período de tempo de latência, mas pode dar-se ao luxo de perder os dados num *desastre.* Por exemplo, em muitas soluções IoT, os dados precisam de ser próximos do cálculo, como quando a temperatura média dos últimos dias está a ser calculada, mas se estes dados forem perdidos, os dados específicos registados não são assim tão importantes. Além disso, neste cenário, normalmente não se preocupa em fazer backup dos pontos de dados individuais. Só faz o back up valores médios calculados que são periodicamente escritos para armazenamento externo.  
- **Dados a longo prazo.** As coleções fiáveis podem armazenar os seus dados permanentemente. Mas neste caso, você precisa se preparar para a recuperação de [desastres](./service-fabric-disaster-recovery.md), [incluindo configurar políticas de backup periódicas](./service-fabric-backuprestoreservice-configure-periodic-backup.md) para os seus clusters. Com efeito, configura o que acontece se o seu cluster for destruído numa catástrofe, onde precisaria de criar um novo cluster, e como implementar novas instâncias de aplicação e recuperar do mais recente backup.

Economize custos e melhore a disponibilidade:
- Pode reduzir custos usando serviços estatais porque não incorre em acesso a dados e custos de transações a partir da loja remota, e porque não precisa de usar outro serviço, como o Azure Cache para redis.
- Usar serviços estatais principalmente para armazenamento e não para computação é caro, e nós não recomendamos. Pense em serviços estatais como um cálculo com armazenamento local barato.
- Ao remover dependências de outros serviços, pode melhorar a disponibilidade do seu serviço. Gerir o estado com HA no cluster isola-o de outros problemas de inatividade de serviço ou de latência.

## <a name="how-to-work-with-reliable-services"></a>Como trabalhar com serviços fiáveis
Serviços fiáveis de tecidos de serviço permitem-lhe criar facilmente serviços apátridas e estatais. Para mais informações, consulte a [introdução aos Serviços Fiáveis.](./service-fabric-reliable-services-introduction.md)
- Honrar sempre o [sinal de cancelamento](./service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps) no método dos `RunAsync()` serviços apátridas e imponentes e o `ChangeRole()` método para serviços estatais. Caso não o faça, a Service Fabric não sabe se o seu serviço pode ser encerrado. Por exemplo, se não honrar o token de cancelamento, podem ocorrer tempos de upgrade de aplicações muito mais longos.
-    Abrir e fechar [os ouvintes](./service-fabric-reliable-services-communication.md) de comunicação em tempo oportuno, e honrar os tokens de cancelamento.
-    Nunca misture o código de sincronização com o código async. Por exemplo, não use `.GetAwaiter().GetResult()` nas suas chamadas de assíduos. Use async *todo o caminho* através da pilha de chamadas.

## <a name="how-to-work-with-reliable-actors"></a>Como trabalhar com atores fiáveis
Service Fabric Reliable Actors permite-lhe criar facilmente atores virtuais e imponentes. Para mais informações, consulte a [introdução a Reliable Actors](./service-fabric-reliable-actors-introduction.md).

- Considere seriamente usar mensagens de pub/sub entre os seus atores para escalonar a sua aplicação. As ferramentas que fornecem este serviço incluem o [Serviço SoCreate De código aberto Do Serviço De Tecido Pub/Sub](https://service-fabric-pub-sub.socreate.it/) e [o Autocarro de Serviço Azure.](/azure/service-bus/)
- Faça o estado do ator o mais [granular possível.](./service-fabric-reliable-actors-state-management.md#best-practices)
- Gerir o [ciclo de vida do ator.](./service-fabric-reliable-actors-state-management.md#best-practices) Apague os atores se não os voltares a usar. Excluir atores não precisos é especialmente importante quando se está a usar o [fornecedor estatal volátil](./service-fabric-reliable-actors-state-management.md#state-persistence-and-replication), porque todo o Estado está guardado na memória.
- Devido à sua [concordância por turnos,](./service-fabric-reliable-actors-introduction.md#concurrency)os atores são melhor usados como objetos independentes. Não crie gráficos de chamadas de métodos sincronizados e multi-actor (cada uma das quais provavelmente se torne uma chamada de rede separada) ou crie pedidos de ator circular. Estes afetarão significativamente o desempenho e a escala.
- Não misture o código de sincronização com o código async. Utilize async de forma consistente para evitar problemas de desempenho.
- Não faças chamadas de longa data em atores. Chamadas de longa data bloquearão outras chamadas para o mesmo ator, devido à concordância baseada na reviravolta.
- Se estiver a comunicar com outros serviços utilizando [o Service Fabric e](./service-fabric-reliable-services-communication-remoting.md) estiver a criar uma , crie a fábrica ao `ServiceProxyFactory` nível do [actor-serviço](./service-fabric-reliable-actors-using.md) e *não* ao nível do ator.


## <a name="application-diagnostics"></a>Diagnóstico de aplicações
Seja minucioso sobre a adição [de registo de aplicações](./service-fabric-diagnostics-event-generation-app.md) em chamadas de serviço. Vai ajudá-lo a diagnosticar cenários em que os serviços se chamam uns aos outros. Por exemplo, quando a chamada B chama C chama D, a chamada pode falhar em qualquer lugar. Se não tiver registos suficientes, falhas são difíceis de diagnosticar. Se os serviços estiverem a registar demasiado devido aos volumes de chamadas, certifique-se de que, pelo menos, regista erros e avisos.

## <a name="iot-and-messaging-applications"></a>IoT e aplicações de mensagens
Quando estiver a ler mensagens do [Azure IoT Hub](../iot-hub/index.yml) ou [dos Azure Event Hubs,](../event-hubs/index.yml)utilize  [o ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/ServiceFabricProcessor). O ServiceFabricProcessor integra-se com serviços de serviços fiáveis para manter o estado de leitura das divisórias do centro do evento e empurra novas mensagens para os seus serviços através do `IEventProcessor::ProcessEventsAsync()` método.


## <a name="design-guidance-on-azure"></a>Orientação de design em Azure
* Visite o [Centro de Arquitetura Azure](/azure/architecture/microservices/) para orientar o design sobre [a construção de microserviços em Azure.](/azure/architecture/microservices/)

* Visite [Get Start with Azure for Gaming](/gaming/azure/) para orientação de design sobre a [utilização de Service Fabric em serviços de jogos.](/gaming/azure/reference-architectures/multiplayer-synchronous-sf)
