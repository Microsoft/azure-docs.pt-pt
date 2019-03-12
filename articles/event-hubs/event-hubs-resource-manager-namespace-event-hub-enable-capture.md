---
title: Criar um hub de eventos com habilitar - a captura dos Hubs de eventos do Azure | Documentos da Microsoft
description: Criar um espaço de nomes de Hubs de Eventos do Azure com o hub de um evento e ativar a Captura através de um modelo do Azure Resource Manager
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 8bdda6a2-5ff1-45e3-b696-c553768f1090
ms.service: event-hubs
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 02/06/2019
ms.author: shvija
ms.openlocfilehash: 804ef04f0066a8b8dd9df82aef03fcfc32d7cb43
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57776500"
---
# <a name="create-a-namespace-with-event-hub-and-enable-capture-using-a-template"></a>Criar um espaço de nomes com o hub de eventos e ativar a captura utilizando um modelo

Este artigo mostra como utiliza um modelo do Azure Resource Manager que cria um espaço de nomes de [Hubs de Eventos](event-hubs-what-is-event-hubs.md) com uma instância de hubs de eventos e também ativa a [funcionalidade Captura](event-hubs-capture-overview.md) no hub de eventos. O artigo descreve como definir quais os recursos que são implementados e como definir os parâmetros que são especificados quando a implementação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades.

Este artigo mostra também como especificar que eventos são capturados nos Azure Storage Blobs ou num Azure Data Lake Store, com base no destino que escolher.

Para obter mais informações sobre a criação de modelos, consulte [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Criar modelos do Azure Resource Manager). Para a sintaxe JSON e propriedades para utilizar num modelo, consulte [tipos de recursos do eventhub](/azure/templates/microsoft.eventhub/allversions).

Para obter mais informações sobre padrões e práticas das convenções de nomenclatura de Recursos do Azure, veja [Azure Resources naming conventions][Azure Resources naming conventions] (Convenções de nomenclatura de Recursos do Azure).

Para os modelos completos, clique nas seguintes ligações do GitHub:

- [Hub de eventos e ativar a Captura para o Modelo de armazenamento][Event Hub and enable Capture to Storage template] 
- [Hub de eventos e ativar a Captura para o Modelo do Azure Data Lake Store][Event Hub and enable Capture to Azure Data Lake Store template]

> [!NOTE]
> Para verificar os modelos mais recentes, visite a galeria [Modelos de Início Rápido do Azure][Azure Quickstart Templates] e procure Hubs de Eventos.
> 
> 

## <a name="what-will-you-deploy"></a>O que irá implementar?

Com esTe modelo, implementa um espaço de nomes de Hubs de Eventos com o hub de um evento e também ativa a [Captura de Hubs de Eventos](event-hubs-capture-overview.md). A Captura de Hubs de Eventos permite a transmissão automática de dados nos Hubs de Eventos para um armazenamento de Blobs ou do Azure do Azure Data Lake Store, dentro de um tempo ou intervalo de tamanho especificado. Clique no botão seguinte para ativar a Captura de Hubs de Eventos no Armazenamento do Azure:

[![Implementar no Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

Clique no botão seguinte para ativar a Captura de Hubs de Eventos no Azure Data Lake Store:

[![Implementar no Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture-for-adls%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Azure Resource Manager, define parâmetros para os valores que pretende especificar quando o modelo é implementado. O modelo inclui uma secção denominada `Parameters` que contém todos os valores de parâmetro. Deverá definir um parâmetro para esses valores que variam com base no projeto ou no ambiente que está a implementar. Não defina parâmetros para valores que permanecem sempre iguais. Cada valor de parâmetro é utilizado no modelo para definir os recursos que são implementados.

O modelo define os seguintes parâmetros.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

O nome do espaço de nomes dos Hubs de Eventos que serão criados.

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

O nome do hub de eventos no espaço de nomes dos Hubs de Eventos.

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

O número de dias para manter as mensagens no hub de eventos. 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

O número de partições a criar no hub de eventos.

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="captureenabled"></a>captureEnabled

Ative a Captura no hub de eventos.

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
### <a name="captureencodingformat"></a>captureEncodingFormat

O formato de codificação que especifica para serializar os dados do evento.

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

### <a name="capturetime"></a>captureTime

O intervalo de tempo no qual a Captura dos Hubs de Eventos inicia a captura de dados.

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"The time window in seconds for the capture"
    }
}
```

### <a name="capturesize"></a>captureSize
O intervalo de tamanho no qual a Captura inicia a captura de dados.

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

### <a name="capturenameformat"></a>captureNameFormat

O formato de nome utilizado pela Captura de Hubs de Eventos para escrever os ficheiros Avro. Tenha em atenção que formato de nome de Captura tem de conter os campos `{Namespace}`, `{EventHub}`, `{PartitionId}`, `{Year}`, `{Month}`, `{Day}`, `{Hour}`, `{Minute}` e `{Second}`. Estes podem ser dispostas por qualquer ordem, com ou sem delimitadores.
 
```json
"captureNameFormat": {
      "type": "string",
      "defaultValue": "{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}",
      "metadata": {
        "description": "A Capture Name Format must contain {Namespace}, {EventHub}, {PartitionId}, {Year}, {Month}, {Day}, {Hour}, {Minute} and {Second} fields. These can be arranged in any order with or without delimeters. E.g.  Prod_{EventHub}/{Namespace}\\{PartitionId}_{Year}_{Month}/{Day}/{Hour}/{Minute}/{Second}"
      }
    }
  
```

### <a name="apiversion"></a>apiVersion

A versão da API do modelo.

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2017-04-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

Utilize os seguintes parâmetros se escolher o Armazenamento do Azure como destino.

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

A Captura exige um ID de recurso da conta de Armazenamento do Azure para ativar a captura na conta de Armazenamento que pretender.

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource ID where you want the blobs be captured"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

O contentor de blobs onde pretende capturar os dados do seu evento.

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```

Utilize os seguintes parâmetros se escolher o Azure Data Lake Store como destino. Tem de definir as permissões no caminho do Data Lake Store, no qual pretenda Capturar o evento. Para definir permissões, veja [este artigo](event-hubs-capture-enable-through-portal.md#capture-data-to-an-azure-data-lake-store-account).

### <a name="subscriptionid"></a>subscriptionId

ID de subscrição para o espaço de nomes do Hub de Eventos e o Azure Data Lake Store. Ambos estes recursos tem de estar sob o mesmo ID de subscrição.

```json
"subscriptionId": {
    "type": "string",
    "metadata": {
        "description": "Subscription ID of both Azure Data Lake Store and Event Hubs namespace"
     }
 }
```

### <a name="datalakeaccountname"></a>dataLakeAccountName

O nome do Azure Data Lake Store para os eventos capturados.

```json
"dataLakeAccountName": {
    "type": "string",
    "metadata": {
        "description": "Azure Data Lake Store name"
    }
}
```

### <a name="datalakefolderpath"></a>dataLakeFolderPath

O caminho da pasta de destino para os eventos capturados. Esta é a pasta no Data Lake Store para os quais os eventos serão enviados durante a operação de captura. Para definir permissões nesta pasta, veja [Use Azure Data Lake Store to capture data from Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md) (Utilizar o Azure Data Lake Store para capturar dados dos Hubs de Eventos).

```json
"dataLakeFolderPath": {
    "type": "string",
    "metadata": {
        "description": "Destination capture folder path"
    }
}
```

## <a name="resources-to-deploy-for-azure-storage-as-destination-to-captured-events"></a>Recursos a implementar para o Armazenamento do Azure como destino para eventos capturados

Cria um espaço de nomes do tipo **EventHub** com um hub de eventos e também ativa a Captura para o Armazenamento de Blobs do Azure.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('eventHubNamespaceName')]",
      "type": "Microsoft.EventHub/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "isAutoInflateEnabled": "true",
        "maximumThroughputUnits": "7"
      },
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('eventHubName')]",
          "type": "EventHubs",
          "dependsOn": [
            "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
          ],
          "properties": {
            "messageRetentionInDays": "[parameters('messageRetentionInDays')]",
            "partitionCount": "[parameters('partitionCount')]",
            "captureDescription": {
              "enabled": "true",
              "skipEmptyArchives": false,
              "encoding": "[parameters('captureEncodingFormat')]",
              "intervalInSeconds": "[parameters('captureTime')]",
              "sizeLimitInBytes": "[parameters('captureSize')]",
              "destination": {
                "name": "EventHubArchive.AzureBlockBlob",
                "properties": {
                  "storageAccountResourceId": "[parameters('destinationStorageAccountResourceId')]",
                  "blobContainer": "[parameters('blobContainerName')]",
                  "archiveNameFormat": "[parameters('captureNameFormat')]"
                }
              }
            }
          }

        }
      ]
    }
  ]
```

## <a name="resources-to-deploy-for-azure-data-lake-store-as-destination"></a>Recursos a implementar para o Azure Data Lake Store como destino

Cria um espaço de nomes do tipo **EventHub** com um hub de eventos e também ativa a Captura para o Azure Data Lake Store.

```json
 "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('eventHubName')]",
                        "captureDescription": {
                            "enabled": "true",
                            "skipEmptyArchives": false,
                            "encoding": "[parameters('archiveEncodingFormat')]",
                            "intervalInSeconds": "[parameters('captureTime')]",
                            "sizeLimitInBytes": "[parameters('captureSize')]",
                            "destination": {
                                "name": "EventHubArchive.AzureDataLake",
                                "properties": {
                                    "DataLakeSubscriptionId": "[parameters('subscriptionId')]",
                                    "DataLakeAccountName": "[parameters('dataLakeAccountName')]",
                                    "DataLakeFolderPath": "[parameters('dataLakeFolderPath')]",
                                    "ArchiveNameFormat": "[parameters('captureNameFormat')]"
                                }
                            }
                        }
                    }
                }
            ]
        }
    ]
```

> [!NOTE]
> Pode ativar ou desativar emitindo ficheiros vazios quando não existem eventos ocorrem durante a janela de captura utilizando o **skipEmptyArchives** propriedade. 

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Implemente o modelo para ativar a Captura de Hubs de Eventos no Armazenamento do Azure:
 
```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

Implemente o modelo para ativar a Captura de Hubs de Eventos no Azure Data Lake Store:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json
```

## <a name="azure-cli"></a>CLI do Azure

Armazenamento de Blob do Azure como destino:

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```

Azure Data Lake Store como destino:

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json][]
```

## <a name="next-steps"></a>Passos Seguintes

Também pode configurar a Captura de Hubs de Eventos através do [portal do Azure](https://portal.azure.com). Para mais informações, consulte [Enable Event Hubs Capture using the Azure portal](event-hubs-capture-enable-through-portal.md) (Ativar Captura de Hubs de Eventos no portal do Azure).

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um hub de eventos](event-hubs-create.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Azure Resources naming conventions]: https://azure.microsoft.com/documentation/articles/guidance-naming-conventions/
[Event hub and enable Capture to Storage template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture
[Event hub and enable Capture to Azure Data Lake Store template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture-for-adls
