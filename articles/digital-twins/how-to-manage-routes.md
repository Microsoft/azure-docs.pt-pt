---
title: Gerir pontos finais e rotas
titleSuffix: Azure Digital Twins
description: Veja como configurar e gerir pontos finais e rotas de eventos para os dados da Azure Digital Twins.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8c9fbf7bc45ed2070570faf0d1dfdb15b5fd98ee
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373271"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>Gerir pontos finais e rotas em Azure Digital Twins

Em Azure Digital Twins, você pode encaminhar notificações de eventos para [serviços](how-to-interpret-event-data.md) a jusante, ou para conectar recursos de computação. Isto é feito primeiro através da criação de **pontos finais** que podem receber os eventos, seguidos das rotas do [**evento**](concepts-route-events.md) que especificam quais os eventos gerados pela Azure Digital Twins são entregues a que pontos finais.

Os tipos de pontos finais suportados incluem:
* [Hub de Eventos](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Para obter mais informações sobre os diferentes pontos finais, consulte [*Escolha entre os serviços de mensagens Azure.*](https://docs.microsoft.com/azure/event-grid/compare-messaging-services)

Os pontos finais e as rotas são geridos com os [**APIs do EventRoutes,**](how-to-use-apis-sdks.md)o [.NET (C#) SDK,](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)ou o [Azure Digital Twins CLI](how-to-use-cli.md). Também podem ser geridos através do [portal Azure.](https://portal.azure.com)

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Criar um ponto final para a Azure Digital Twins

Para ligar um ponto final à Azure Digital Twins, o Event Hub, o tema da grelha de eventos ou o Service Bus que está a usar para o ponto final já precisa de existir. 

O exemplo a seguir mostra como criar um tópico de grelha de eventos utilizando o Azure CLI:

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

Isto torna o tópico da grelha de evento disponível dentro de Azure Digital Twins, sob o nome especificado com o `--endpoint-name` argumento. Normalmente, você usará esse nome como alvo de uma rota de **eventos**, que irá criar na secção seguinte usando o serviço Azure Digital Twins API.

Existem comandos equivalentes para pontos finais do Event Hub e do Service Bus:

* Adicionar ponto final de tópico de ônibus de serviço (requer um recurso de service bus pré-criado)
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Adicionar ponto final do Event Hub (requer recurso de Centro de Eventos pré-criado)
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="manage-event-routes-with-apis"></a>Gerir rotas de eventos com APIs

Para enviar dados da Azure Digital Twins para um ponto final, precisa definir uma rota de eventos. Azure Digital Twins **EventRoutes APIs** permitem aos desenvolvedores ligar o fluxo de eventos, em todo o sistema e para serviços a jusante. Leia mais sobre as rotas de eventos em [*Conceitos: Eventos de Roteamento Azure Digital Twins*](concepts-route-events.md).

Pode proceder à criação de uma rota de eventos assim que os seus pontos finais terminarem a sua configuração.

>[!NOTE]
>Se implementou recentemente os seus pontos finais, valide que terminaram de ser implementados **antes** de tentar usá-los para uma nova rota de eventos. Se a implementação da rota falhar porque os pontos finais não estão prontos, aguarde alguns minutos e tente novamente.
>
> Se estiver a escrever este fluxo, é melhor prestar contas por isso construindo em 2-3 minutos de tempo de espera para o serviço de ponto final terminar a implantação antes de seguir para a configuração da rota.

As amostras deste artigo utilizam o C# SDK.

As rotas do evento são definidas usando APIs de plano de dados. Uma definição de rota pode conter estes elementos:
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

Pode restringir os eventos que estão a ser enviados adicionando um filtro a um ponto final.

Para adicionar um filtro, pode utilizar um pedido PUT para *https://{YourHost}/EventRoutes/myNewRoute?api-version=2020-05-31-pré-visualização* com o seguinte corpo:

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

Aqui estão os filtros de rota suportados.

| Nome do filtro | Descrição | Esquema de filtro | Valores suportados | 
| --- | --- | --- | --- |
| Tipo | O [tipo de evento](./concepts-route-events.md#types-of-event-messages) que flui através da sua instância digital gémea | `"filter" : "type = '<eventType>'"` | `Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Origem | Nome da instância Azure Digital Twins | `"filter" : "source = '<hostname>'"`|  **Para notificações:**`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **Para telemetria:**`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Assunto | Uma descrição do evento no contexto da fonte do evento acima | `"filter": " subject = '<subject>'"` | **Para notificações**: O assunto é`<twinid>` <br> ou um formato URI para sujeitos, que são identificados exclusivamente por múltiplas partes ou IDs:<br>`<twinid>/relationships/<relationshipid>`<br> **Para a telemetria**: O sujeito é o caminho do componente (se a telemetria for emitida a partir de um componente gémeo), tais como `comp1.comp2` . Se a telemetria não for emitida a partir de um componente, então o seu campo de objetos está vazio. |
| Esquema de dados | ID modelo DTDL | `"filter": "dataschema = 'dtmi:example:com:floor4;2'"` | **Para telemetria**: O esquema de dados é o ID do modelo do gémeo ou o componente que emite a telemetria <br>**Para notificações**: O esquema de dados não é suportado|
| Tipo do conteúdo | Tipo de conteúdo do valor dos dados | `"filter": "datacontenttype = '<contentType>'"` | `application/json` |
| Versão spec | A versão do esquema de evento que está a usar | `"filter": "specversion = '<version>'"` | Deve `1.0` ser. Isto indica que o esquema do CloudEvents versão 1.0 |
| Verdadeiro / Falso | Permite criar uma rota sem filtragem, ou desativar uma rota | `"filter" : "<true/false>"` | `true`= rota está ativada sem filtragem <br> `false`= rota é desativada |
<!--
| ID | *Not implemented for public preview* | "filter": "id = '…'" | |
| Schema | *Not implemented for public preview*  | "filter": "dataschema = '…'" | |
-->

Também é possível combinar filtros utilizando as seguintes operações:

| Nome do filtro | Esquema de filtro | Exemplo | 
| --- | --- | --- |
| E / OU | `"filter": "<filter1> AND <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'"` |
| E / OU | `"filter": "<filter1> OR <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'"` |
| Operações aninhadas | `"filter": "(<Comparison1>) AND (<Comparison2>)"` | `"filter": "(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')"` |

> [!NOTE]
> Durante a pré-visualização, apenas a igualdade de cordas é suportada (=, !=).

Quando implementar ou atualizar um filtro, a alteração pode demorar alguns minutos a ser refletida no pipeline de dados.

## <a name="manage-endpoints-and-routes-with-cli"></a>Gerir pontos finais e rotas com CLI

Os pontos finais e as rotas também podem ser geridos usando o CLI das Gémeas Digitais Azure. Os comandos podem ser encontrados em [*Como-a-: Use o CLI das Gémeas Digitais Azure*](how-to-use-cli.md).

## <a name="monitor-event-routes"></a>Monitorizar rotas de eventos

Métricas de encaminhamento como contagem, latência e taxa de insucesso podem ser vistas no [portal Azure](https://portal.azure.com/). 

A partir da página inicial do portal, procure a sua instância Azure Digital Twins para obter os seus detalhes. Selecione a opção **Métricas** no menu da instância Azure Digital Twins para apresentar a página *Métricas.*

:::image type="content" source="media/how-to-view-metrics/azure-digital-twins-metrics.png" alt-text="Screenshot mostrando a página métrica para Azure Digital Twins":::

A partir daqui, você pode ver as métricas para o seu exemplo e criar vistas personalizadas.

## <a name="next-steps"></a>Passos seguintes

Leia sobre os diferentes tipos de mensagens de evento que pode receber:
* [*Como fazer: Interpretar dados do evento*](how-to-interpret-event-data.md)
