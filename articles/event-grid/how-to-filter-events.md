---
title: Como filtrar eventos para a Azure Event Grid
description: Este artigo mostra como filtrar eventos (por tipo de evento, por sujeito, por operadores e dados, etc.) ao criar uma subscrição de Grade de Eventos.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 99fb00f99a055033ccfcd99e32a52d423878fb44
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86105579"
---
# <a name="filter-events-for-event-grid"></a>Eventos de filtragem para grelha de eventos

Este artigo mostra como filtrar eventos ao criar uma subscrição de Grade de Eventos. Para saber mais sobre as opções de filtragem de [eventos, consulte a filtragem do evento para as subscrições da Grade de Eventos.](event-filtering.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="filter-by-event-type"></a>Filtro por tipo de evento

Ao criar uma subscrição de Grade de Eventos, pode especificar quais [os tipos de eventos](event-schema.md) a enviar para o ponto final. Os exemplos nesta secção criam subscrições de eventos para um grupo de recursos, mas limitam os eventos que são enviados `Microsoft.Resources.ResourceWriteFailure` para e `Microsoft.Resources.ResourceWriteSuccess` . Se necessitar de mais flexibilidade na filtragem de eventos por tipos de eventos, consulte Filter por operadores avançados e campos de dados.

Para PowerShell, utilize o `-IncludedEventType` parâmetro ao criar a subscrição.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

Para Azure CLI, utilize o `--included-event-types` parâmetro. O exemplo a seguir utiliza o Azure CLI numa concha bash:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

Para um modelo de Gestor de Recursos, utilize a `includedEventTypes` propriedade.

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

## <a name="filter-by-subject"></a>Filtro por sujeito

Pode filtrar eventos pelo sujeito nos dados do evento. Pode especificar um valor a condizer com o início ou o fim do assunto. Se necessitar de mais flexibilidade na filtragem de eventos por sujeito, consulte Filter por operadores avançados e campos de dados.

No exemplo seguinte do PowerShell, cria-se uma subscrição de eventos que filtra até ao início do assunto. Usa o parâmetro para limitar os `-SubjectBeginsWith` eventos a outros para um recurso específico. Passa-se a identificação de recursos de um grupo de segurança de rede.

```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

O próximo exemplo do PowerShell cria uma subscrição para um armazenamento de bolhas. Limita os acontecimentos a aqueles com um assunto que termina em `.jpg` .

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

No exemplo Azure CLI, cria-se uma subscrição de eventos que filtra até ao início do assunto. Usa o parâmetro para limitar os `--subject-begins-with` eventos a outros para um recurso específico. Passa-se a identificação de recursos de um grupo de segurança de rede.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

O próximo exemplo do Azure CLI cria uma subscrição para um armazenamento de bolhas. Limita os acontecimentos a aqueles com um assunto que termina em `.jpg` .

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

No exemplo do modelo seguinte do Gestor de Recursos, cria-se uma subscrição de eventos que filtra até ao início do assunto. Você usa a `subjectBeginsWith` propriedade para limitar eventos a outros para um recurso específico. Passa-se a identificação de recursos de um grupo de segurança de rede.

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

O exemplo do modelo do gestor de recursos seguinte cria uma subscrição para um armazenamento de bolhas. Limita os acontecimentos a aqueles com um assunto que termina em `.jpg` .

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

Para uma maior flexibilidade na filtragem, pode utilizar operadores e propriedades de dados para filtrar eventos.

### <a name="subscribe-with-advanced-filters"></a>Inscreva-se com filtros avançados

Para saber mais sobre os operadores e chaves que pode utilizar para filtragem avançada, consulte [a filtragem avançada](event-filtering.md#advanced-filtering).

Estes exemplos criam um tópico personalizado. Subscrevem o tópico personalizado e filtram por um valor no objeto de dados. Os eventos que têm a propriedade de cor definida para azul, vermelho ou verde são enviados para a subscrição.

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

Para testar um cenário onde o evento não é enviado, envie um evento com o campo de cores definido para amarelo. Amarelo não é um dos valores especificados na subscrição, por isso o evento não é entregue na sua subscrição.

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

* Para obter informações sobre a monitorização das entregas de eventos, consulte [a entrega de mensagens monitor a Grelha de Eventos](monitor-event-delivery.md).
* Para obter mais informações sobre a chave de autenticação, consulte [a segurança e a autenticação da Grade de Eventos.](security-authentication.md)
* Para obter mais informações sobre a criação de uma subscrição da Azure Event Grid, consulte [o esquema de subscrição da Event Grid](subscription-creation-schema.md).
