---
title: Mapa campo personalizado para esquema de rede de eventos azure
description: Este artigo descreve como converter o seu esquema personalizado para o esquema da Rede de Eventos Azure quando os dados do seu evento não correspondem ao esquema da Rede de Eventos.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: e8077068a265d659cf6009eb7762188637c373d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721664"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mapear campos personalizados no esquema do Event Grid

Se os dados do seu evento não corresponderem ao esquema esperado da Grelha de [Eventos,](event-schema.md)ainda pode utilizar a Rede de Eventos para encaminhar o evento para os assinantes. Este artigo descreve como mapear o seu esquema para o esquema da Grelha de Eventos.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalar função de pré-visualização

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Esquema original do evento

Suponhamos que tem uma aplicação que envia eventos no seguinte formato:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Embora esse formato não corresponda ao esquema necessário, a Rede de Eventos permite-lhe mapear os seus campos para o esquema. Ou, pode receber os valores no esquema original.

## <a name="create-custom-topic-with-mapped-fields"></a>Criar tópico personalizado com campos mapeados

Ao criar um tópico personalizado, especifique como mapear campos do seu evento original para o esquema da grelha de eventos. Existem três valores que usa para personalizar o mapeamento:

* O valor **do esquema** de entrada especifica o tipo de esquema. As opções disponíveis são CloudEvents schema, schema de evento personalizado ou schema de Event Grid. O valor padrão é event grid schema. Ao criar mapeamento personalizado entre o seu esquema e o esquema da grelha de eventos, use esquema de evento personalizado. Quando os eventos estiverem no esquema CloudEvents, use o esquema Cloudevents.

* O padrão de **mapeamento valoriza** a propriedade especifica valores predefinidos para campos no esquema da Grelha de Eventos. Pode definir valores `subject` `eventtype`predefinidos para, e `dataversion`. Normalmente, você usa este parâmetro quando o seu esquema personalizado não inclui um campo que corresponde a um desses três campos. Por exemplo, pode especificar que a versão de dados está sempre definida para **1.0**.

* Os **campos de mapeamento** valorizam os campos de mapas do seu esquema até ao esquema da grelha de eventos. Especifica valores em pares chave/valor separados pelo espaço. Para o nome-chave, utilize o nome do campo da grelha de eventos. Para o valor, use o nome do seu campo. Pode usar nomes-chave `eventtime` `subject`para, `id` `topic` `dataversion`, , `eventtype`e .

Para criar um tópico personalizado com o Azure CLI, utilize:

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

## <a name="subscribe-to-event-grid-topic"></a>Subscreva o tópico da grelha de eventos

Ao subscrever o tópico personalizado, especifice o esquema que gostaria de usar para receber os eventos. Especifica o esquema CloudEvents, o esquema de eventos personalizados ou o esquema da Grelha de Eventos. O valor padrão é event grid schema.

O exemplo seguinte subscreve um tópico de grelha de eventos e utiliza o esquema da Grelha de Eventos. Para a CLI do Azure, utilize:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

O próximo exemplo utiliza o esquema de entrada do evento:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

O exemplo seguinte subscreve um tópico de grelha de eventos e utiliza o esquema da Grelha de Eventos. Para o PowerShell, utilize:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

O próximo exemplo utiliza o esquema de entrada do evento:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Publicar evento para tópico

Está agora pronto para enviar um evento para o tópico personalizado, e ver o resultado do mapeamento. O seguinte guião para publicar um evento no [esquema de exemplo:](#original-event-schema)

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

Agora, olhe para o seu ponto final WebHook. As duas subscrições entregaram eventos em diferentes schemas.

A primeira subscrição usou esquema de grelha de eventos. O formato do evento entregue é:

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

Estes campos contêm os mapeamentos do tópico personalizado. **myEventTypeField** é mapeado para **EventType**. Os valores predefinidos para **DataVersion** e **Assunto** são utilizados. O objeto **Data** contém os campos de esquema sinuoso do evento original.

A segunda subscrição usou o esquema do evento de entrada. O formato do evento entregue é:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

Reparem que os campos originais foram entregues.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre entrega e repetições de eventos, entrega e retentativa de mensagens da Grelha de [Eventos.](delivery-and-retry.md)
* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente a usar a Grelha de Eventos, consulte [create e encaminhe eventos personalizados com a Grelha de Eventos Azure](custom-event-quickstart.md).
