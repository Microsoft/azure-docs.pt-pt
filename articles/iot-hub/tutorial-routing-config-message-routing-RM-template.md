---
title: Tutorial - Configurar o encaminhamento de mensagens para Azure IoT Hub usando um modelo de Gestor de Recursos Azure
description: Tutorial - Configurar o encaminhamento de mensagens para Azure IoT Hub usando um modelo de Gestor de Recursos Azure
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 53217340b0d91f3de77e5e0d8c0a82e30599d6ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98621432"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Tutorial: Use um modelo de Gestor de Recursos Azure para configurar o encaminhamento de mensagens IoT Hub

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Encaminhamento de mensagens

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>Descarregue o modelo e o arquivo de parâmetros

Para a segunda parte deste tutorial, você descarrega e execute uma aplicação Visual Studio para enviar mensagens para o IoT Hub. Existe uma pasta nesse download que contém o modelo e o ficheiro de parâmetros do Azure Resource Manager, bem como os scripts Azure CLI e PowerShell.

Vá em frente e baixe já as [amostras Azure IoT C#.](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) Desaperte o ficheiro master.zip. O modelo do Gestor de Recursos e o ficheiro de parâmetros estão em /iot-hub/Tutorials/Encaminhamento/SimulatedDevice/resources/ à medida **quetemplate_iothub.jse** template_iothub_parameters.js **em**.

## <a name="create-your-resources"></a>Crie os seus recursos

Você vai usar um modelo Azure Resource Manager (RM) para criar todos os seus recursos. Os scripts Azure CLI e PowerShell podem ser executados algumas linhas de cada vez. Um modelo RM é implantado num só passo. Este artigo mostra-lhe as secções separadamente para ajudá-lo a entender cada um deles. Em seguida, irá mostrar-lhe como implantar o modelo e criar o dispositivo virtual para testes. Depois de o modelo ser implantado, pode visualizar a configuração de encaminhamento de mensagens no portal.

Existem vários nomes de recursos que devem ser globalmente únicos, como o nome IoT Hub e o nome da conta de armazenamento. Para facilitar a nomeação dos recursos, esses nomes de recursos são configurados para anexar um valor alfanumérico aleatório gerado a partir da data/hora atual. 

Se olhar para o modelo, verá onde são configuradas variáveis para estes recursos que tomam o parâmetro passado e concatenate *aleatoriamenteValue* para o parâmetro. 

A secção seguinte explica os parâmetros utilizados.

### <a name="parameters"></a>Parâmetros

A maioria destes parâmetros tem valores padrão. Os que terminam com **_in** são concatenados com *o RandomValue* para torná-los globalmente únicos. 

**RandomValue**: Este valor é gerado a partir da data/hora atual quando implementa o modelo. Este campo não está no ficheiro de parâmetros, uma vez que é gerado no próprio modelo.

**subscriçãoId**: Este campo está definido para si na subscrição em que está a implementar o modelo. Este campo não está no ficheiro de parâmetros, uma vez que está definido para si.

**IoTHubName_in**: Este campo é o nome base IoT Hub, que é concatenado com o RandomValue para ser globalmente único.

**localização**: Este campo é a região de Azure em que está a implantar, como "westus".

**consumer_group**: Este campo é o grupo de consumidores definido para mensagens que passam pelo ponto final de encaminhamento. É utilizado para filtrar os resultados no Azure Stream Analytics. Por exemplo, existe todo o fluxo onde obtém tudo, ou se tiver dados que venham com consumer_group definido para **Contoso,** então pode configurar um stream Azure Stream Analytics (e relatório Power BI) para mostrar apenas essas entradas. Este campo é usado na parte 2 deste tutorial.

**sku_name**: Este campo é o escalonamento para o IoT Hub. Este valor deve ser S1 ou superior; um nível livre não funciona para este tutorial porque não permite vários pontos finais.

**sku_units**: Este campo vai com o **sku_name,** e é o número de unidades IoT Hub que podem ser usadas.

**d2c_partitions**: Este campo é o número de divisórias utilizadas para o fluxo de eventos.

**storageAccountName_in**: Este campo é o nome da conta de armazenamento a criar. As mensagens são encaminhadas para um contentor na conta de armazenamento. Este campo é concatentado com o Valor Aleatório para torná-lo globalmente único.

**storageContainerName**: Este campo é o nome do recipiente no qual as mensagens encaminhadas para a conta de armazenamento são armazenadas.

**storage_endpoint**: Este campo é o nome do ponto final da conta de armazenamento utilizado pelo encaminhamento de mensagens.

**service_bus_namespace_in**: Este campo é o nome do espaço de nomes do Service Bus a ser criado. Este valor é concatentado com o Valor Aleatório para torná-lo globalmente único.

**service_bus_queue_in**: Este campo é o nome da fila do Service Bus utilizada para reencaminhamento de mensagens. Este valor é concatentado com o Valor Aleatório para torná-lo globalmente único.

**AuthRules_sb_queue**: Este campo é a regra de autorização para a fila do autocarro de serviço, utilizada para recuperar a cadeia de ligação para a fila.

### <a name="variables"></a>Variáveis

Estes valores são usados no modelo, e são derivados principalmente de parâmetros.

**queueAuthorizationRuleResourceId**: Este campo é o ResourceId para a regra de autorização para a fila do Autocarro de Serviço. O ResourceId é por sua vez utilizado para recuperar a cadeia de ligação para a fila.

**iotHubName**: Este campo é o nome do IoT Hub depois de ter concatenado aleatoriamente. 

**armazenamentoSAme :** Este campo é o nome da conta de armazenamento depois de ter concatenado aleatoriamente. 

**service_bus_namespace**: Este campo é o espaço de nome depois de ter concatenado aleatoriamente.

**service_bus_queue**: Este campo é o nome da fila do Service Bus depois de ter concatenado aleatoriamente.

**sbVersion**: A versão da API do Autocarro de Serviço para utilizar. Neste caso, é "2017-04-01".

### <a name="resources-storage-account-and-container"></a>Recursos: Conta de armazenamento e contentor

O primeiro recurso criado é a conta de armazenamento, juntamente com o recipiente para o qual as mensagens são encaminhadas. O contentor é um recurso sob a conta de armazenamento. Tem uma `dependsOn` cláusula para a conta de armazenamento, exigindo que a conta de armazenamento seja criada antes do contentor.

Aqui está o que esta secção parece:

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

### <a name="resources-service-bus-namespace-and-queue"></a>Recursos: Espaço de nome de autocarro de serviço e fila

O segundo recurso criado é o espaço de nomes do Service Bus, juntamente com a fila do Service Bus para a qual as mensagens são encaminhadas. O SKU está definido como padrão. A versão API é recuperada das variáveis. Está também definido para ativar o espaço de nomes do Service Bus quando implementar esta secção (status:Ative). 

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

Esta secção cria a fila do Service Bus. Esta parte do script tem uma `dependsOn` cláusula que garante que o espaço de nome é criado antes da fila.

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

### <a name="resources-iot-hub-and-message-routing"></a>Recursos: Iot Hub e encaminhamento de mensagens

Agora que a conta de armazenamento e a fila do Service Bus foram criadas, cria-se o Hub IoT que lhes encaminha mensagens. O modelo RM usa `dependsOn` cláusulas para que não tente criar o hub antes dos recursos do Service Bus e da conta de armazenamento terem sido criados. 

Aqui está a primeira parte da secção IoT Hub. Esta parte do modelo configura as dependências e começa com as propriedades.

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

A próxima secção é a secção para a configuração de encaminhamento de mensagens para o Hub Iot. Primeiro é a secção para os pontos finais. Esta parte do modelo configura os pontos finais de encaminhamento para a fila do Service Bus e a conta de armazenamento, incluindo as cordas de ligação.

Para criar a cadeia de ligação para a fila, precisa da filaAuthorizationRulesResourcedId, que é recuperada em linha. Para criar o fio de ligação para a conta de armazenamento, recupere a chave de armazenamento primária e, em seguida, use-a no formato para a cadeia de ligação.

A configuração do ponto final é também onde define o formato blob para `AVRO` ou `JSON` .

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

Esta próxima secção é para as rotas da mensagem para os pontos finais. Há um conjunto para cada ponto final, por isso há um para a fila do Service Bus e um para o recipiente da conta de armazenamento.

Lembre-se que a condição de consulta para as mensagens que estão a ser encaminhada para o armazenamento é `level="storage"` , e a condição de consulta para as mensagens que estão a ser encaminhada para a fila do Service Bus é `level="critical"` .

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

Este json mostra o resto da secção IoT Hub, que contém informações padrão e o SKU para o hub.

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

### <a name="resources-service-bus-queue-authorization-rules"></a>Recursos: Regras de autorização de fila de autocarros de serviço

A regra de autorização de fila do Service Bus é utilizada para recuperar a cadeia de ligação para a fila do Service Bus. Utiliza uma `dependsOn` cláusula para garantir que não é criada antes do espaço de nomes do Service Bus e da fila do Service Bus.

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

### <a name="resources-consumer-group"></a>Recursos: Grupo de consumidores

Nesta secção, você cria um Grupo de Consumidores para os dados do IoT Hub a serem usados pelo Azure Stream Analytics na segunda parte deste tutorial.

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

### <a name="resources-outputs"></a>Recursos: Saídas

Se pretender enviar um valor de volta para o script de implementação a ser apresentado, utilize uma secção de saída. Esta parte do modelo devolve a cadeia de ligação para a fila do Service Bus. Devolver um valor não é necessário, é incluído como um exemplo de como devolver os resultados ao script de chamada.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>Implementar o modelo RM

Para implementar o modelo para Azure, carreque o modelo e o ficheiro de parâmetros para Azure Cloud Shell e, em seguida, execute um script para implementar o modelo. Abra a Azure Cloud Shell e inscreva-se. Este exemplo utiliza o PowerShell.

Para fazer o upload dos ficheiros, selecione o ícone **de ficheiros upload/Download** na barra de menus e, em seguida, escolha upload.

![Screenshot que realça o ícone de ficheiros upload/download.](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Utilize o Explorador de Ficheiros que aparece para encontrar os ficheiros no disco local e selecione-os e, em seguida, escolha **Abrir**.

Depois de os ficheiros serem carregados, um diálogo de resultados mostra algo como a seguinte imagem.

![Barra de menu Cloud Shell com resultados de Upload/Download em destaque](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

Os ficheiros são enviados para a partilha utilizada pela sua instância Cloud Shell. 

Execute o script para executar a implementação. A última linha deste script recupera a variável que foi configurada para ser devolvida- a cadeia de ligação da fila do Service Bus.

O script define e utiliza estas variáveis:

**$RGName** é o nome do grupo de recursos para implantar o modelo. Este campo é criado antes de implementar o modelo.

**$location** é a localização Azure para ser usada para o modelo, como "westus".

**o nome de implantação** é um nome que atribui à implementação para recuperar o valor variável de retorno.

Aqui está o guião powerShell. Copie este script PowerShell e cole-o na janela Cloud Shell e, em seguida, bata em Enter para executá-lo.

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

Se tiver erros de script, pode editar o script localmente, carregá-lo novamente para a Cloud Shell e executar o script novamente. Depois de o guião terminar a correr com sucesso, continue para o próximo passo.

## <a name="create-simulated-device"></a>Criar dispositivo simulado

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Ver encaminhamento de mensagens no portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Agora que tem todos os recursos configurados e as rotas de mensagens configuradas, avance para o próximo tutorial para aprender a processar e mostrar a informação sobre as mensagens encaminhadas.

> [!div class="nextstepaction"]
> [Parte 2 - Ver os resultados do encaminhamento de mensagens](tutorial-routing-view-message-routing-results.md)
