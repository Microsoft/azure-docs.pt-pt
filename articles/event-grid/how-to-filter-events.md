---
title: Como filtrar eventos para a grade de eventos do Azure
description: Este artigo mostra como filtrar eventos (por tipo de evento, por assunto, por operadores e dados, etc.) ao criar uma assinatura de grade de eventos.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 58da209c68449d3a28b08f52ec575f7db520f121
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514566"
---
# <a name="filter-events-for-event-grid"></a>Filtrar eventos para a grade de eventos

Este artigo mostra como filtrar eventos ao criar uma assinatura de grade de eventos. Para saber mais sobre as opções de filtragem de eventos, consulte [entender a filtragem de eventos para assinaturas de grade de eventos](event-filtering.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="filter-by-event-type"></a>Filtrar por tipo de evento

Ao criar uma assinatura de grade de eventos, você pode especificar quais [tipos de eventos](event-schema.md) enviar ao ponto de extremidade. Os exemplos nesta seção criam assinaturas de evento para um grupo de recursos, mas limitam os eventos que são enviados para `Microsoft.Resources.ResourceWriteFailure` e `Microsoft.Resources.ResourceWriteSuccess`. Se precisar de mais flexibilidade ao filtrar eventos por tipos de evento, consulte filtrar por operadores avançados e campos de dados.

Para o PowerShell, use o parâmetro `-IncludedEventType` ao criar a assinatura.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

Para CLI do Azure, use o parâmetro `--included-event-types`. O exemplo a seguir usa CLI do Azure em um shell bash:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

Para um modelo do Resource Manager, use a propriedade `includedEventTypes`.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

## <a name="filter-by-subject"></a>Filtrar por assunto

Você pode filtrar eventos pelo assunto nos dados do evento. Você pode especificar um valor para corresponder ao início ou ao fim do assunto. Se você precisar de mais flexibilidade ao filtrar eventos por assunto, consulte filtrar por operadores avançados e campos de dados.

No exemplo do PowerShell a seguir, você cria uma assinatura de evento que filtra pelo início do assunto. Use o parâmetro `-SubjectBeginsWith` para limitar eventos a um recurso específico. Você passa a ID de recurso de um grupo de segurança de rede.

```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

O próximo exemplo do PowerShell cria uma assinatura para um armazenamento de BLOBs. Ele limita os eventos a aqueles com um assunto que termina em `.jpg`.

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

No exemplo a seguir CLI do Azure, você cria uma assinatura de evento que filtra pelo início do assunto. Use o parâmetro `--subject-begins-with` para limitar eventos a um recurso específico. Você passa a ID de recurso de um grupo de segurança de rede.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

O exemplo a seguir CLI do Azure cria uma assinatura para um armazenamento de BLOBs. Ele limita os eventos a aqueles com um assunto que termina em `.jpg`.

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

No exemplo de modelo do Resource Manager a seguir, você cria uma assinatura de evento que filtra pelo início do assunto. Você usa a propriedade `subjectBeginsWith` para limitar eventos a um recurso específico. Você passa a ID de recurso de um grupo de segurança de rede.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

O próximo exemplo de modelo do Resource Manager cria uma assinatura para um armazenamento de BLOBs. Ele limita os eventos a aqueles com um assunto que termina em `.jpg`.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

## <a name="filter-by-operators-and-data"></a>Filtrar por operadores e dados

Para obter mais flexibilidade na filtragem, você pode usar operadores e propriedades de dados para filtrar eventos.

### <a name="subscribe-with-advanced-filters"></a>Assinar filtros avançados

Para saber mais sobre os operadores e as chaves que você pode usar para a filtragem avançada, consulte [filtragem avançada](event-filtering.md#advanced-filtering).

Esses exemplos criam um tópico personalizado. Eles assinam o tópico personalizado e filtram por um valor no objeto de dados. Os eventos que têm a Propriedade Color definida como Blue, Red ou Green são enviados para a assinatura.

Para a CLI do Azure, utilize:

```azurecli-interactive
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

Observe que está definida uma [data de expiração](concepts.md#event-subscription-expiration) para a subscrição.

Para o PowerShell, utilize:

```azurepowershell-interactive
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="test-filter"></a>Filtro de teste

Para testar o filtro, envie um evento com o campo cor definido como verde. Como Green é um dos valores no filtro, o evento é entregue ao ponto de extremidade.

Para a CLI do Azure, utilize:

```azurecli-interactive
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Para o PowerShell, utilize:

```azurepowershell-interactive
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Para testar um cenário em que o evento não é enviado, envie um evento com o campo cor definido como amarelo. Amarelo não é um dos valores especificados na assinatura, portanto, o evento não é entregue à sua assinatura.

Para a CLI do Azure, utilize:

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```
Para o PowerShell, utilize:

```azurepowershell-interactive
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre como monitorar entregas de eventos, consulte [monitorar a entrega de mensagens na grade de eventos](monitor-event-delivery.md).
* Para obter mais informações sobre a chave de autenticação, consulte [segurança e autenticação da grade de eventos](security-authentication.md).
* Para obter mais informações sobre como criar uma assinatura da grade de eventos do Azure, consulte [esquema de assinatura da grade de eventos](subscription-creation-schema.md).
