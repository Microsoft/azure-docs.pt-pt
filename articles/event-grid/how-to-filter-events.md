---
title: Como filtrar eventos para a Grelha de Eventos Azure
description: Este artigo mostra como filtrar eventos (por tipo de evento, por sujeito, por operadores e dados, etc.) ao criar uma subscrição da Rede de Eventos.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 63a5cdbff79af52d9f96cf410a820c6cfc530066
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79454028"
---
# <a name="filter-events-for-event-grid"></a>Filtrar eventos para grelha de eventos

Este artigo mostra como filtrar eventos ao criar uma subscrição da Rede de Eventos. Para saber mais sobre as opções de filtragem de eventos, consulte a filtragem do [evento Understand para subscrições da Grelha de Eventos](event-filtering.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="filter-by-event-type"></a>Filtro por tipo de evento

Ao criar uma subscrição da Grelha de Eventos, pode especificar quais os tipos de [eventos](event-schema.md) a enviar para o ponto final. Os exemplos nesta secção criam subscrições de eventos para um `Microsoft.Resources.ResourceWriteFailure` `Microsoft.Resources.ResourceWriteSuccess`grupo de recursos, mas limitam os eventos que são enviados para e . Se precisar de mais flexibilidade ao filtrar eventos por tipos de eventos, consulte O Filtro por operadores avançados e campos de dados.

Para o PowerShell, utilize o `-IncludedEventType` parâmetro ao criar a subscrição.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

Para o Azure CLI, utilize o `--included-event-types` parâmetro. O exemplo seguinte utiliza o Azure CLI numa concha bash:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

Para um modelo de `includedEventTypes` Gestor de Recursos, use a propriedade.

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

## <a name="filter-by-subject"></a>Filtrar por sujeito

Pode filtrar eventos pelo sujeito nos dados do evento. Pode especificar um valor a combinar para o início ou o fim do sujeito. Se precisar de mais flexibilidade ao filtrar eventos por assunto, consulte Filter por operadores avançados e campos de dados.

No exemplo seguinte da PowerShell, cria-se uma subscrição de evento que filtra até ao início do assunto. Usa-se `-SubjectBeginsWith` o parâmetro para limitar os eventos a um recurso específico. Passas a identificação de recursos de um grupo de segurança da rede.

```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

O próximo exemplo da PowerShell cria uma subscrição para um armazenamento de bolhas. Limita os acontecimentos a pessoas com `.jpg`um tema que termina em .

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

No exemplo do ClI Azure seguinte, cria-se uma subscrição de evento que filtra até ao início do assunto. Usa-se `--subject-begins-with` o parâmetro para limitar os eventos a um recurso específico. Passas a identificação de recursos de um grupo de segurança da rede.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

O próximo exemplo do Azure CLI cria uma subscrição para um armazenamento de bolhas. Limita os acontecimentos a pessoas com `.jpg`um tema que termina em .

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

No exemplo do seguinte modelo de Gestor de Recursos, cria-se uma subscrição de evento que filtra até ao início do assunto. Você usa `subjectBeginsWith` a propriedade para limitar eventos a uns para um recurso específico. Passas a identificação de recursos de um grupo de segurança da rede.

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

O próximo exemplo do modelo do Gestor de Recursos cria uma subscrição para um armazenamento de bolhas. Limita os acontecimentos a pessoas com `.jpg`um tema que termina em .

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

Para obter mais flexibilidade na filtragem, pode utilizar operadores e propriedades de dados para filtrar eventos.

### <a name="subscribe-with-advanced-filters"></a>Inscreva-se com filtros avançados

Para saber sobre os operadores e teclas que pode utilizar para filtragem avançada, consulte [a filtragem avançada](event-filtering.md#advanced-filtering).

Estes exemplos criam um tópico personalizado. Subscrevem o tópico personalizado e filtram por um valor no objeto de dados. Eventos que têm a propriedade colorida definida para azul, vermelho ou verde são enviados para a subscrição.

Para a CLI do Azure, utilize:

```azurecli
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

```powershell
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

Para testar o filtro, envie um evento com o campo de cores definido para verde. Como o verde é um dos valores do filtro, o evento é entregue ao ponto final.

Para a CLI do Azure, utilize:

```azurecli
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Para o PowerShell, utilize:

```powershell
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

Para testar um cenário em que o evento não é enviado, envie um evento com o campo de cores definido para amarelo. O amarelo não é um dos valores especificados na subscrição, pelo que o evento não é entregue à sua subscrição.

Para a CLI do Azure, utilize:

```azurecli
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```
Para o PowerShell, utilize:

```powershell
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

* Para obter informações sobre a monitorização das entregas de eventos, consulte a entrega de [mensagens Monitor Event Grid](monitor-event-delivery.md).
* Para mais informações sobre a chave de autenticação, consulte [a segurança e a autenticação da Rede de Eventos.](security-authentication.md)
* Para mais informações sobre a criação de uma subscrição da Rede de Eventos Do Evento, consulte o esquema de subscrição da [Rede de Eventos](subscription-creation-schema.md).
