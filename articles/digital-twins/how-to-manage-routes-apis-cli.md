---
title: Gerir pontos finais e rotas (APIs e CLI)
titleSuffix: Azure Digital Twins
description: Veja como configurar e gerir pontos finais e rotas de eventos para os dados da Azure Digital Twins.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9274e147bcaec4b3e63a6720e369946d64e94628
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87809889"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Gerir pontos finais e rotas em Azure Digital Twins (APIs e CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Em Azure Digital Twins, você pode encaminhar notificações de eventos para serviços a [jusante](how-to-interpret-event-data.md) ou recursos de computação conectados. Isto é feito primeiro através da criação **de pontos finais** que podem receber os eventos. Em seguida, pode criar [**rotas de eventos**](concepts-route-events.md) que especifiquem quais os eventos gerados pela Azure Digital Twins que são entregues a que pontos finais.

Os pontos finais e as rotas podem ser geridos com os [ApIs do EventRoutes,](how-to-use-apis-sdks.md)o [.NET (C#) SDK,](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)ou o [Azure Digital Twins CLI](how-to-use-cli.md). Este artigo acompanha-o através do processo de criação de pontos finais e rotas através destes mecanismos.

Também podem ser geridos através do [portal Azure.](https://portal.azure.com) Para uma versão deste artigo que utiliza o portal, consulte [*Como fazer: Gerir pontos finais e rotas (portal)*](how-to-manage-routes-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

* Você vai precisar de uma **conta Azure** (você pode configurar uma [gratuitamente aqui)](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Você precisará de uma **instância Azure Digital Twins** na sua assinatura Azure. Se ainda não tiver um caso, pode criar um utilizando os passos em [*Como-a-: Configurar um caso e autenticação*](how-to-set-up-instance-scripted.md). Ter os seguintes valores da configuração útil para usar mais tarde neste artigo:
    - Nome da instância
    - Grupo de recursos
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Criar um ponto final para a Azure Digital Twins

Estes são os tipos suportados de pontos finais que pode criar, para o seu exemplo:
* [Event Grid](../event-grid/overview.md)
* [Hubs de Eventos](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Para obter mais informações sobre os diferentes tipos de pontos [*finais, consulte Escolha entre os serviços de mensagens Azure.*](../event-grid/compare-messaging-services.md)

Para ligar um ponto final à Azure Digital Twins, o tópico da grelha de eventos, o centro de eventos ou o Service Bus que está a usar para o ponto final já precisa de existir. 

### <a name="create-an-event-grid-endpoint"></a>Criar um ponto final de Grade de Eventos

O exemplo a seguir mostra como criar um ponto final do tipo de grelha de evento usando o Azure CLI. Pode utilizar [a Azure Cloud Shell,](https://shell.azure.com)ou [instalar o CLI localmente.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

Primeiro, criar um tópico de grelha de eventos. Pode utilizar o seguinte comando ou ver os passos mais detalhadamente visitando [a secção *de tópicos personalizados* ](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) dos eventos Event Grid *Custom.*

```azurecli
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Para obter uma lista de nomes da região de Azure que podem ser passados em comandos no CLI Azure, executar este comando:
> ```azurecli
> az account list-locations -o table
> ```

Uma vez criado o tópico, pode ligá-lo à Azure Digital Twins com o seguinte comando:

```azurecli
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Agora, o tópico da grelha de eventos está disponível como ponto final dentro da Azure Digital Twins, sob o nome especificado com o `--endpoint-name` argumento. Normalmente, você usará esse nome como alvo de uma rota de **eventos**, que irá criar [mais tarde neste artigo](#event-routes-with-apis-and-the-c-sdk) usando o serviço Azure Digital Twins API.

### <a name="create-an-event-hubs-or-service-bus-endpoint"></a>Crie um centro de eventos ou ponto final de ônibus de serviço

O processo de criação de Centros de Eventos ou pontos finais de autocarros de serviço é semelhante ao processo de Grelha de Eventos acima mostrado.

Primeiro, crie os seus recursos que usará como ponto final. Aqui está o que é necessário:
* Service Bus: _Service Bus namespace_, _Service Bus topic_, Regra de _autorização_
* Centros de eventos: _Espaço de nomes de Centros de Eventos,_ _centro de eventos,_ regra _de autorização_

Em seguida, utilize os seguintes comandos para criar os pontos finais em Azure Digital Twins: 

* Adicionar ponto final de tópico de ônibus de serviço (requer um recurso de service bus pré-criado)
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Adicionar ponto final do Event Hubs (requer recurso de Centros de Eventos pré-criado)
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="event-routes-with-apis-and-the-c-sdk"></a>Rotas de eventos (com APIs e o C# SDK)

Para enviar dados da Azure Digital Twins para um ponto final, terá de definir uma rota de **eventos.** Azure Digital Twins **EventRoutes APIs** permitem aos desenvolvedores ligar o fluxo de eventos, em todo o sistema e para serviços a jusante. Leia mais sobre as rotas de eventos em [*Conceitos: Eventos de Roteamento Azure Digital Twins*](concepts-route-events.md).

As amostras desta secção utilizam o C# SDK.

**Pré-requisito**: É necessário criar pontos finais, conforme descrito anteriormente neste artigo, antes de poder passar a criar uma rota. Pode proceder à criação de uma rota de eventos assim que os seus pontos finais terminarem a sua configuração.

>[!NOTE]
>Se implementou recentemente os seus pontos finais, valide que terminaram de ser implementados **antes** de tentar usá-los para uma nova rota de eventos. Se a implementação da rota falhar porque os pontos finais não estão prontos, aguarde alguns minutos e tente novamente.
>
> Se estiver a escrever este fluxo, é melhor prestar contas por isso construindo em 2-3 minutos de tempo de espera para o serviço de ponto final terminar a implantação antes de seguir para a configuração da rota.

### <a name="create-an-event-route"></a>Criar uma rota de eventos

As rotas do evento são definidas usando APIs de plano de dados. 

Uma definição de rota pode conter estes elementos:
* O ID da rota que você quer usar
* O nome do ponto final que quer usar
* Um filtro que define quais os eventos enviados para o ponto final 

Se não houver ID de rota, nenhuma mensagem é encaminhada para fora da Azure Digital Twins. Se houver um ID de rota e o filtro estiver `true` , todas as mensagens são encaminhadas para o ponto final. Se houver um ID de rota e um filtro diferente for adicionado, as mensagens serão filtradas com base no filtro.

Uma rota deve permitir a seleção de várias notificações e tipos de eventos. 

`CreateEventRoute`é a chamada SDK que é usada para adicionar uma rota de evento. Aqui está um exemplo da sua utilização:

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

> [!TIP]
> Todas as funções SDK vêm em versões sincronizadas e assíncronos.

### <a name="event-route-sample-code"></a>Código de amostra de rota de evento

A seguinte amostra de código mostra como criar, listar e eliminar uma rota de eventos:
```csharp
try
{
    Console.WriteLine("Create a route: testRoute1");
    EventRoute er = new EventRoute("< your - endpoint - name >");
    // Make a filter that passes everything
    er.Filter = "true";
    client.CreateEventRoute("< your - route - name >", er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable <EventRoute> result = client.GetEventRoutes();
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointId} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
}
```

### <a name="filter-events"></a>Filtrar eventos

Sem filtragem, os pontos finais recebem uma variedade de eventos da Azure Digital Twins:
* Telemetria disparada por [gémeos digitais](concepts-twins-graph.md) usando o serviço Azure Digital Twins API
* Notificações de mudança de propriedade gémea, disparadas sobre alterações de propriedade para qualquer gémeo no caso Azure Digital Twins
* Eventos de ciclo de vida, disparados quando gémeos ou relacionamentos são criados ou eliminados
* Eventos de mudança de modelo, disparados quando [os modelos](concepts-models.md) configurados numa instância Azure Digital Twins são adicionados ou eliminados

Pode restringir os eventos que estão a ser enviados adicionando um **filtro** para um ponto final na sua rota de eventos.

Para adicionar um filtro, pode utilizar um pedido PUT para *https://{YourHost}/EventRoutes/myNewRoute?api-version=2020-05-31-pré-visualização* com o seguinte corpo:

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

Aqui estão os filtros de rota suportados. Utilize o detalhe na coluna *de esquema de texto do filtro* para substituir o espaço reservado no corpo de pedido `<filter-text>` acima.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

## <a name="manage-endpoints-and-routes-with-cli"></a>Gerir pontos finais e rotas com CLI

Os pontos finais e as rotas também podem ser geridos usando o CLI das Gémeas Digitais Azure. Para obter mais informações sobre a utilização do CLI e quais os comandos disponíveis, consulte [*Como-a-fazer: Use o CLI das Gémeas Digitais Azure*](how-to-use-cli.md).

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Passos seguintes

Leia sobre os diferentes tipos de mensagens de evento que pode receber:
* [*Como fazer: Interpretar dados do evento*](how-to-interpret-event-data.md)
