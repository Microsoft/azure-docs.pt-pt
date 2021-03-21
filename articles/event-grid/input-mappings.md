---
title: Mapear campo personalizado para esquema de grelha de evento azure
description: Este artigo descreve como converter o seu esquema personalizado para o esquema da Grelha de Eventos Azure quando os dados do evento não correspondem ao esquema da Grade de Eventos.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 34381782c9337631b0aa04e47eb5897a8071139a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97109203"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mapear campos personalizados no esquema do Event Grid

Se os dados do seu evento não corresponderem ao esquema esperado [da Grelha de Eventos,](event-schema.md)ainda pode utilizar a Grade de Eventos para encaminhar o evento para os assinantes. Este artigo descreve como mapear o seu esquema para o esquema da Grelha de Eventos.

## <a name="original-event-schema"></a>Esquema de evento original

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

Embora este formato não corresponda ao esquema exigido, o Event Grid permite-lhe mapear os seus campos para o esquema. Ou pode receber os valores no esquema original.

## <a name="create-custom-topic-with-mapped-fields"></a>Crie tópico personalizado com campos mapeados

Ao criar um tópico personalizado, especifique como mapear campos do seu evento original para o esquema da grelha do evento. Existem três valores que usa para personalizar o mapeamento:

* O valor **do esquema de entrada** especifica o tipo de esquema. As opções disponíveis são o esquema do CloudEvents, o esquema de eventos personalizado ou o esquema da Grade de Eventos. O valor predefinido é o esquema da Grelha de Eventos. Ao criar mapeamento personalizado entre o seu esquema de esquema e o esquema da grelha de eventos, utilize o esquema de eventos personalizado. Quando os eventos estiverem no formato CloudEvents, utilize o esquema do CloudEvents.

* A propriedade **de valores prefondo** de mapeamento especifica valores predefinidos para campos no esquema da Grelha de Evento. Pode definir valores predefinidos para `subject` `eventtype` , e `dataversion` . Normalmente, você usa este parâmetro quando o seu esquema personalizado não inclui um campo que corresponde a um desses três campos. Por exemplo, pode especificar que a versão de dados está sempre definida para **1.0**.

* Os **campos de mapeamento valorizam** os campos de mapas desde o seu esquema até ao esquema da grelha do evento. Especificar valores em pares de chave/valor separados pelo espaço. Para o nome chave, utilize o nome do campo de grelha de evento. Pelo valor, use o nome do seu campo. Pode usar nomes-chave para `id` , , , e `topic` `eventtime` `subject` `eventtype` `dataversion` .

Para criar um tópico personalizado com o Azure CLI, utilize:

```azurecli-interactive
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
New-AzEventGridTopic `
  -ResourceGroupName myResourceGroup `
  -Name demotopic `
  -Location eastus2 `
  -InputSchema CustomEventSchema `
  -InputMappingField @{eventType="myEventTypeField"} `
  -InputMappingDefaultValue @{subject="DefaultSubject"; dataVersion="1.0" }
```

## <a name="subscribe-to-event-grid-topic"></a>Subscreva o tópico da grelha de eventos

Ao subscrever o tópico personalizado, especifique o esquema que gostaria de usar para receber os eventos. Especifica o esquema cloudEvents, o esquema de eventos personalizados ou o esquema da Grelha de Eventos. O valor predefinido é o esquema da Grelha de Eventos.

O exemplo a seguir subscreve um tópico de grelha de eventos e utiliza o esquema da Grade de Eventos. Para a CLI do Azure, utilize:

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

O exemplo a seguir subscreve um tópico de grelha de eventos e utiliza o esquema da Grade de Eventos. Para o PowerShell, utilize:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

O próximo exemplo utiliza o esquema de entrada do evento:

```azurepowershell-interactive
New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Publicar evento para tema

Está agora pronto para enviar um evento para o tema personalizado, e ver o resultado do mapeamento. O seguinte script para publicar um evento no [esquema de exemplo:](#original-event-schema)

Para a CLI do Azure, utilize:

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

event='[ { "myEventTypeField":"Created", "resource":"Users/example/Messages/1000", "resourceData":{"someDataField1":"SomeDataFieldValue"} } ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

Para o PowerShell, utilize:

```azurepowershell-interactive
$endpoint = (Get-AzEventGridTopic -ResourceGroupName myResourceGroup -Name demotopic).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName myResourceGroup -Name demotopic

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

Agora, olhe para o seu ponto final WebHook. As duas subscrições entregaram eventos em esquemas diferentes.

A primeira subscrição usou o esquema da grelha de eventos. O formato do evento entregue é:

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

Estes campos contêm os mapeamentos do tópico personalizado. **myEventTypeField** está mapeado para **EventType**. São utilizados os **valores predefinidos para DataVersion** e **Subject.** O objeto **Data** contém os campos de esquema de evento original.

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

Note que os campos originais foram entregues.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre entrega de eventos e retrórias, [entrega e redação de mensagens de Event Grid.](delivery-and-retry.md)
* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente a utilizar a Grade de Eventos, consulte [Criar e encaminhar eventos personalizados com a Azure Event Grid](custom-event-quickstart.md).
