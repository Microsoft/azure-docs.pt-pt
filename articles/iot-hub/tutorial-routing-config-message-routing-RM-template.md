---
title: Configurar o roteamento de mensagens para o Hub IoT do Azure usando um modelo de Azure Resource Manager
description: Configurar o roteamento de mensagens para o Hub IoT do Azure usando um modelo de Azure Resource Manager
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 8f245653a8b84944e1e8a3f48a49992f0065be58
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084398"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Tutorial: usar um modelo de Azure Resource Manager para configurar o roteamento de mensagens do Hub IoT

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Roteamento de mensagens

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>Baixar o modelo e o arquivo de parâmetros

Para a segunda parte deste tutorial, você baixa e executa um aplicativo do Visual Studio para enviar mensagens para o Hub IoT. Há uma pasta nesse download que contém o modelo de Azure Resource Manager e o arquivo de parâmetros, bem como os scripts do CLI do Azure e do PowerShell.

Vá em frente e baixe os [exemplos C# do Azure IOT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) agora. Descompacte o arquivo. zip mestre. O modelo do Resource Manager e o arquivo de parâmetros estão em/iot-hub/Tutorials/Routing/SimulatedDevice/resources/como **template_iothub. JSON** e **template_iothub_parameters. JSON**.

## <a name="create-your-resources"></a>Criar seus recursos

Você vai usar um modelo de Azure Resource Manager (RM) para criar todos os seus recursos. Os scripts do CLI do Azure e do PowerShell podem ser executados algumas linhas por vez. Um modelo RM é implantado em uma única etapa. Este artigo mostra as seções separadamente para ajudá-lo a entender cada uma delas. Em seguida, ele mostrará como implantar o modelo e criará o dispositivo virtual para teste. Depois que o modelo for implantado, você poderá exibir a configuração de roteamento de mensagens no Portal.

Há vários nomes de recursos que devem ser globalmente exclusivos, como o nome do Hub IoT e o nome da conta de armazenamento. Para facilitar o nome dos recursos, esses nomes de recursos são configurados para acrescentar um valor alfanumérico aleatório gerado a partir da data/hora atual. 

Se você examinar o modelo, verá onde as variáveis são configuradas para esses recursos que usam o parâmetro passado e concatenam *randomValue* com o parâmetro. 

A seção a seguir explica os parâmetros usados.

### <a name="parameters"></a>Parâmetros

A maioria desses parâmetros tem valores padrão. Aqueles que terminam com **_in** são concatenados com *randomValue* para torná-los globalmente exclusivos. 

**randomValue**: esse valor é gerado a partir da data/hora atual quando você implanta o modelo. Esse campo não está no arquivo de parâmetros, pois ele é gerado no próprio modelo.

**SubscriptionId**: esse campo é definido para você para a assinatura na qual você está implantando o modelo. Este campo não está no arquivo de parâmetros porque está definido para você.

**IoTHubName_in**: esse campo é o nome do Hub IOT base, que é concatenado com o randomValue para ser globalmente exclusivo.

**local**: esse campo é a região do Azure na qual você está implantando, como "westus".

**consumer_group**: esse campo é o conjunto de grupos de consumidores para mensagens provenientes do ponto de extremidade de roteamento. Ele é usado para filtrar resultados em Azure Stream Analytics. Por exemplo, há todo o fluxo em que você obtém tudo, ou se você tiver dados recebidos com consumer_group definido como **contoso**, poderá configurar um fluxo de Azure Stream Analytics (e Power bi relatório) para mostrar apenas essas entradas. Esse campo é usado na parte 2 deste tutorial.

**sku_name**: esse campo é o dimensionamento para o Hub IOT. Esse valor deve ser S1 ou superior; uma camada gratuita não funciona para este tutorial porque não permite vários pontos de extremidade.

**sku_units**: esse campo acompanha o **sku_name**e é o número de unidades do Hub IOT que podem ser usadas.

**d2c_partitions**: esse campo é o número de partições usadas para o fluxo de eventos.

**storageAccountName_in**: esse campo é o nome da conta de armazenamento a ser criada. As mensagens são roteadas para um contêiner na conta de armazenamento. Esse campo é concatenado com o randomValue para torná-lo globalmente exclusivo.

**storageContainerName**: esse campo é o nome do contêiner no qual as mensagens roteadas para a conta de armazenamento são armazenadas.

**storage_endpoint**: esse campo é o nome do ponto de extremidade da conta de armazenamento usado pelo roteamento de mensagens.

**service_bus_namespace_in**: esse campo é o nome do namespace do barramento de serviço a ser criado. Esse valor é concatenado com o randomValue para torná-lo globalmente exclusivo.

**service_bus_queue_in**: esse campo é o nome da fila do barramento de serviço usada para rotear mensagens. Esse valor é concatenado com o randomValue para torná-lo globalmente exclusivo.

**AuthRules_sb_queue**: Este campo é as regras de autorização para a fila do barramento de serviço, usada para recuperar a cadeia de conexão para a fila.

### <a name="variables"></a>Variáveis

Esses valores são usados no modelo e são basicamente derivados de parâmetros.

**queueAuthorizationRuleResourceId**: esse campo é o ResourceId para a regra de autorização para a fila do barramento de serviço. O ResourceId é, por sua vez, usado para recuperar a cadeia de conexão para a fila.

**iotHubName**: esse campo é o nome do Hub IOT depois de ter randomValue concatenado. 

**storageAccountName**: esse campo é o nome da conta de armazenamento depois de ter randomValue concatenado. 

**service_bus_namespace**: esse campo é o namespace depois de ter randomValue concatenado.

**service_bus_queue**: esse campo é o nome da fila do barramento de serviço depois de ter randomValue concatenado.

**sbVersion**: a versão da API do barramento de serviço a ser usada. Nesse caso, é "2017-04-01".

### <a name="resources-storage-account-and-container"></a>Recursos: conta de armazenamento e contêiner

O primeiro recurso criado é a conta de armazenamento, juntamente com o contêiner para o qual as mensagens são roteadas. O contêiner é um recurso na conta de armazenamento. Ele tem uma cláusula `dependsOn` para a conta de armazenamento, exigindo que a conta de armazenamento seja criada antes do contêiner.

Esta é a aparência desta seção:

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "properties": {},
    "resources": [
        {
        "type": "blobServices/containers",
        "apiVersion": "2018-07-01",
        "name": "[concat('default/', parameters('storageContainerName'))]",
        "properties": {
            "publicAccess": "None"
            } ,
        "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
            ]
        }
    ]
}
```

### <a name="resources-service-bus-namespace-and-queue"></a>Recursos: namespace e fila do barramento de serviço

O segundo recurso criado é o namespace do barramento de serviço, juntamente com a fila do barramento de serviço para a qual as mensagens são roteadas. A SKU é definida como standard. A versão da API é recuperada das variáveis. Ele também é definido para ativar o namespace do barramento de serviço quando ele implanta esta seção (status: Active). 

```json
{
    "type": "Microsoft.ServiceBus/namespaces",
    "comments": "The Sku should be 'Standard' for this tutorial.",
    "sku": {
        "name": "Standard",
        "tier": "Standard"
    },
    "name": "[variables('service_bus_namespace')]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "properties": {
        "provisioningState": "Succeeded",
        "metricId": "[concat('a4295411-5eff-4f81-b77e-276ab1ccda12:', variables('service_bus_namespace'))]",
        "serviceBusEndpoint": "[concat('https://', variables('service_bus_namespace'),'.servicebus.windows.net:443/')]",
        "status": "Active"
    },
    "dependsOn": []
}
```

Esta seção cria a fila do barramento de serviço. Essa parte do script tem uma cláusula `dependsOn` que garante que o namespace seja criado antes da fila.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {},
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]"
    ]
}
```

### <a name="resources-iot-hub-and-message-routing"></a>Recursos: Hub IOT e roteamento de mensagens

Agora que a conta de armazenamento e a fila do barramento de serviço foram criadas, você cria o Hub IoT que roteia as mensagens para elas. O modelo RM usa cláusulas `dependsOn` para que ele não tente criar o Hub antes que os recursos do barramento de serviço e a conta de armazenamento tenham sido criados. 

Aqui está a primeira parte da seção do Hub IoT. Essa parte do modelo configura as dependências e começa com as propriedades.

```json
{
    "apiVersion": "2018-04-01",
    "type": "Microsoft.Devices/IotHubs",
    "name": "[variables('IoTHubName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ],
    "properties": {
        "eventHubEndpoints": {}
            "events": {
                "retentionTimeInDays": 1,
                "partitionCount": "[parameters('d2c_partitions')]"
                }
            },
```

A próxima seção é a seção para a configuração de roteamento de mensagens para o Hub IOT. Primeiro é a seção para os pontos de extremidade. Essa parte do modelo configura os pontos de extremidade de roteamento para a fila do barramento de serviço e a conta de armazenamento, incluindo as cadeias de conexão.

Para criar a cadeia de conexão para a fila, você precisa do queueAuthorizationRulesResourcedId, que é recuperado embutido. Para criar a cadeia de conexão para a conta de armazenamento, recupere a chave de armazenamento primária e, em seguida, use-a no formato da cadeia de conexão.

A configuração do ponto de extremidade também é onde você define o formato de BLOB para `AVRO` ou `JSON`.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

 ```json
"routing": {
    "endpoints": {
        "serviceBusQueues": [
        {
            "connectionString": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]",
            "name": "[parameters('service_bus_queue_endpoint')]",
            "subscriptionId": "[parameters('subscriptionId')]", 
            "resourceGroup": "[resourceGroup().Name]"
        }
        ],
        "serviceBusTopics": [],
        "eventHubs": [],
        "storageContainers": [
            {
                "connectionString": 
                "[Concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]",
                "containerName": "[parameters('storageContainerName')]",
                "fileNameFormat": "{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}",
                "batchFrequencyInSeconds": 100,
                "maxChunkSizeInBytes": 104857600,
                "encoding": "avro",
                "name": "[parameters('storage_endpoint')]",
                "subscriptionId": "[parameters('subscriptionId')]",
                "resourceGroup": "[resourceGroup().Name]"
            }
        ]
    },
```

Esta próxima seção destina-se às rotas de mensagens para os pontos de extremidade. Há uma configuração para cada ponto de extremidade, portanto, há uma para a fila do barramento de serviço e outra para o contêiner da conta de armazenamento.

Lembre-se de que a condição de consulta para as mensagens que estão sendo roteadas para o armazenamento é `level="storage"`e a condição de consulta para as mensagens que estão sendo roteadas para a fila do barramento de serviço é `level="critical"`.

```json
"routes": [
    {
        "name": "contosoStorageRoute",
        "source": "DeviceMessages",
        "condition": "level=\"storage\"",
        "endpointNames": [
            "[parameters('storage_endpoint')]"
            ],
        "isEnabled": true
    },
    {
        "name": "contosoSBQueueRoute",
        "source": "DeviceMessages",
        "condition": "level=\"critical\"",
        "endpointNames": [
            "[parameters('service_bus_queue_endpoint')]"
            ],
        "isEnabled": true
    }
],
```

Esse JSON mostra o restante da seção do Hub IoT, que contém informações padrão e a SKU para o Hub.

```json
            "fallbackRoute": {
                "name": "$fallback",
                "source": "DeviceMessages",
                "condition": "true",
                "endpointNames": [
                    "events"
                ],
                "isEnabled": true
            }
        },
        "storageEndpoints": {
            "$default": {
                "sasTtlAsIso8601": "PT1H",
                "connectionString": "",
                "containerName": ""
            }
        },
        "messagingEndpoints": {
            "fileNotifications": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        },
        "enableFileUploadNotifications": false,
        "cloudToDevice": {
            "maxDeliveryCount": 10,
            "defaultTtlAsIso8601": "PT1H",
            "feedback": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        }
    },
    "sku": {
        "name": "[parameters('sku_name')]",
        "capacity": "[parameters('sku_units')]"
    }
}
```

### <a name="resources-service-bus-queue-authorization-rules"></a>Recursos: regras de autorização de fila do barramento de serviço

A regra de autorização de fila do barramento de serviço é usada para recuperar a cadeia de conexão para a fila do barramento de serviço. Ele usa uma cláusula `dependsOn` para garantir que ela não seja criada antes do namespace do barramento de serviço e da fila do barramento de serviço.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues/authorizationRules",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'), '/', parameters('AuthRules_sb_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {
        "rights": [
            "Send"
        ]
    },
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ]
},
```

### <a name="resources-consumer-group"></a>Recursos: grupo de consumidores

Nesta seção, você cria um grupo de consumidores para os dados do Hub IoT a serem usados pelo Azure Stream Analytics na segunda parte deste tutorial.

```json
{
    "type": "Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups",
    "name": "[concat(variables('iotHubName'), '/events/',parameters('consumer_group'))]",
    "apiVersion": "2018-04-01",
    "dependsOn": [
        "[concat('Microsoft.Devices/IotHubs/', variables('iotHubName'))]"
    ]
}
```

### <a name="resources-outputs"></a>Recursos: saídas

Se você quiser enviar um valor de volta para o script de implantação a ser exibido, use uma seção de saída. Essa parte do modelo retorna a cadeia de conexão para a fila do barramento de serviço. Retornar um valor não é necessário, ele é incluído como um exemplo de como retornar resultados para o script de chamada.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>Implantar o modelo RM

Para implantar o modelo no Azure, carregue o modelo e o arquivo de parâmetros para Azure Cloud Shell e, em seguida, execute um script para implantar o modelo. Abra Azure Cloud Shell e entre. Este exemplo usa o PowerShell.

Para carregar os arquivos, selecione o ícone **carregar/baixar arquivos** na barra de menus e escolha carregar.

![Barra de menus Cloud Shell com arquivos de upload/download realçados](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Use o explorador de arquivos que abre para localizar os arquivos no disco local e selecione-os e, em seguida, escolha **abrir**.

Depois que os arquivos são carregados, uma caixa de diálogo de resultados mostra algo semelhante à imagem a seguir.

![Barra de menus Cloud Shell com arquivos de upload/download realçados](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

Os arquivos são carregados no compartilhamento usado por sua instância de Cloud Shell. 

Execute o script para executar a implantação. A última linha desse script recupera a variável que foi configurada para ser retornada--a cadeia de conexão da fila do barramento de serviço.

O script define e usa essas variáveis:

**$RGName** é o nome do grupo de recursos no qual o modelo será implantado. Este campo é criado antes da implantação do modelo.

**$Location** é o local do Azure a ser usado para o modelo, como "westus".

**deploymentname** é um nome que você atribui à implantação para recuperar o valor de variável retornado.

Este é o script do PowerShell. Copie esse script do PowerShell e cole-o na janela Cloud Shell e pressione ENTER para executá-lo.

```powershell
$RGName="ContosoResources"
$location = "westus"
$deploymentname="contoso-routing"

# Remove the resource group if it already exists. 
#Remove-AzResourceGroup -name $RGName 
# Create the resource group.
New-AzResourceGroup -name $RGName -Location $location 

# Set a path to the parameter file. 
$parameterFile = "$HOME/template_iothub_parameters.json"
$templateFile = "$HOME/template_iothub.json"

# Deploy the template.
New-AzResourceGroupDeployment `
    -Name $deploymentname `
    -ResourceGroupName $RGName `
    -TemplateParameterFile $parameterFile `
    -TemplateFile $templateFile `
    -verbose

# Get the returning value of the connection string.
(Get-AzResourceGroupDeployment -ResourceGroupName $RGName -Name $deploymentname).Outputs.sbq_connectionString.value
```

Se você tiver erros de script, poderá editar o script localmente, carregá-lo novamente no Cloud Shell e executar o script novamente. Depois que o script for concluído com êxito, prossiga para a próxima etapa.

## <a name="create-simulated-device"></a>Criar dispositivo simulado

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Exibir o roteamento de mensagens no portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Agora que você tem todos os recursos configurados e as rotas de mensagens estão configuradas, avance para o próximo tutorial para saber como processar e exibir as informações sobre as mensagens roteadas.

> [!div class="nextstepaction"]
> [Parte 2-exibir os resultados de roteamento de mensagens](tutorial-routing-view-message-routing-results.md)
