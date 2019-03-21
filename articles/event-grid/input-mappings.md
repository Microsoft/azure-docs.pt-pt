---
title: Campo personalizado do mapa ao esquema do Azure Event Grid
description: Descreve como converter seu esquema personalizado para o esquema do Azure Event Grid.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: spelluru
ms.openlocfilehash: a0e054be3ab7d4818ac323eb5fb93968f57eca4f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181321"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mapear campos personalizados ao esquema do Event Grid

Se os dados de evento não corresponderem a esperada [esquema de grelha de eventos](event-schema.md), ainda pode utilizar o Event Grid para eventos de rota para assinantes. Este artigo descreve como mapear o seu esquema para o esquema do Event Grid.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instale a funcionalidade de pré-visualização

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Esquema de eventos do original

Vamos supor que tenha um aplicativo para enviar eventos no seguinte formato:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Embora esse formato não corresponde ao esquema necessário, o Event Grid permite-lhe mapear seus campos para o esquema. Em alternativa, pode receber os valores no esquema original.

## <a name="create-custom-topic-with-mapped-fields"></a>Criar um tópico personalizado com campos mapeados

Ao criar um tópico personalizado, especifique como mapear campos do seu evento original para o esquema de grelha de eventos. Existem três valores a que utilizar para personalizar o mapeamento:

* O **esquema de entrada** valor Especifica o tipo de esquema. As opções disponíveis são o esquema do CloudEvents, o esquema de evento personalizado ou o esquema do Event Grid. O valor predefinido é o esquema do Event Grid. Quando criar personalizado mapeamento entre o esquema e o esquema de grelha de eventos, utilize o esquema de eventos personalizados. Quando eventos forem no esquema do CloudEvents, use o esquema do Cloudevents.

* O **valores predefinidos de mapeamento** propriedade especifica os valores predefinidos para a campos no esquema do Event Grid. Pode definir os valores predefinidos para `subject`, `eventtype`, e `dataversion`. Normalmente, utilize este parâmetro quando o seu esquema personalizado não inclui um campo que corresponde a um desses três campos. Por exemplo, pode especificar essa versão de dados está sempre definido como **1.0**.

* O **mapeamento de campos** valor mapeia os campos do seu esquema para o esquema de grelha de eventos. Especifique os valores em pares chave/valor, separadas por espaços. O nome da chave, utilize o nome do campo de grelha de eventos. O valor, utilize o nome do campo. Pode utilizar nomes de chaves para `id`, `topic`, `eventtime`, `subject`, `eventtype`, e `dataversion`.

Para criar um tópico personalizado com a CLI do Azure, utilize:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema \
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

Para o PowerShell, utilize:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName myResourceGroup `
  -Name demotopic `
  -Location eastus2 `
  -InputSchema CustomEventSchema `
  -InputMappingField @{eventType="myEventTypeField"} `
  -InputMappingDefaultValue @{subject="DefaultSubject"; dataVersion="1.0" }
```

## <a name="subscribe-to-event-grid-topic"></a>Subscrever o tópico do event grid

Ao subscrever o tópico personalizado, especifique o esquema que pretende utilizar para receber os eventos. Especifique o esquema do CloudEvents, o esquema de evento personalizado ou o esquema do Event Grid. O valor predefinido é o esquema do Event Grid.

O exemplo seguinte subscreve um tópico do event grid e usa o esquema do Event Grid. Para a CLI do Azure, utilize:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

O exemplo seguinte utiliza o esquema de entrada do evento:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

O exemplo seguinte subscreve um tópico do event grid e usa o esquema do Event Grid. Para o PowerShell, utilize:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

O exemplo seguinte utiliza o esquema de entrada do evento:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Publicar o evento para o tópico

Agora, está pronto para enviar um evento para o tópico personalizado e ver o resultado do mapeamento. O seguinte script para publicar um evento no [esquema de exemplo](#original-event-schema):

Para a CLI do Azure, utilize:

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

event='[ { "myEventTypeField":"Created", "resource":"Users/example/Messages/1000", "resourceData":{"someDataField1":"SomeDataFieldValue"} } ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

Para o PowerShell, utilize:

```azurepowershell-interactive
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demotopic).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName myResourceGroup -Name demotopic

$htbody = @{
    myEventTypeField="Created"
    resource="Users/example/Messages/1000"
    resourceData= @{
        someDataField1="SomeDataFieldValue"
    }
}

$body = "["+(ConvertTo-Json $htbody)+"]"
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Agora, observe o ponto de final do WebHook. As duas subscrições entregar eventos em esquemas diferentes.

A primeira assinatura utilizado o esquema de grelha de eventos. O formato do evento entregue é:

```json
{
  "id": "aa5b8e2a-1235-4032-be8f-5223395b9eae",
  "eventTime": "2018-11-07T23:59:14.7997564Z",
  "eventType": "Created",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "subject": "DefaultSubject",
  "data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": {
      "someDataField1": "SomeDataFieldValue"
    }
  }
}
```

Estes campos contêm os mapeamentos do tópico personalizado. **myEventTypeField** é mapeado para **EventType**. Os valores predefinidos para **DataVersion** e **assunto** são utilizados. O **dados** objeto contém os campos de esquema original do evento.

A segunda assinatura utilizado o esquema de eventos de entrada. O formato do evento entregue é:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

Tenha em atenção que os campos originais foram entregues.

## <a name="next-steps"></a>Passos Seguintes

* Para obter informações sobre a entrega de eventos e repetições, [entrega de mensagens do Event Grid e volte a tentar](delivery-and-retry.md).
* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente com o Event Grid, veja [criar e encaminhar eventos personalizados com o Azure Event Grid](custom-event-quickstart.md).
