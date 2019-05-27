---
title: Configurar o encaminhamento de mensagens para o IoT Hub do Azure com um modelo Azure Resource Manager | Documentos da Microsoft
description: Configurar o encaminhamento de mensagens para o IoT Hub do Azure com um modelo Azure Resource Manager
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: d7b8c0685cf92341241575d3e67c09a759f5c190
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66163275"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Tutorial: Utilizar um modelo Azure Resource Manager para configurar o encaminhamento de mensagens do IoT Hub

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Encaminhamento de mensagens

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>Transfira o ficheiro de modelo e parâmetros

Para a segunda parte deste tutorial, transfira e execute um aplicativo do Visual Studio para enviar mensagens para o IoT Hub. Existe uma pasta nesse download que contém o modelo Azure Resource Manager e o ficheiro de parâmetros, bem como os scripts da CLI do Azure e PowerShell.

Vá em frente e transfira o [IoT do Azure C# exemplos](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) agora. Deszipe o ficheiro de Master. O modelo do Resource Manager e o ficheiro de parâmetros são /iot-hub/Tutorials/Routing/SimulatedDevice/resources/como **template_iothub.json** e **template_iothub_parameters.json**.

## <a name="create-your-resources"></a>Criar os seus recursos

Vamos utilizar um modelo do Azure Resource Manager (RM) para criar todos os seus recursos. Os scripts da CLI do Azure e o PowerShell podem ser executados algumas linhas de cada vez. Um modelo de RM é implementado num único passo. Este artigo mostra-lhe as secções em separado para ajudar a compreender a cada um deles. Em seguida, ela mostrará como implementar o modelo e criar o dispositivo virtual para fins de teste. Depois do modelo é implementado, pode ver a configuração de roteamento de mensagem no portal.

Existem vários nomes de recurso que devem ser globalmente exclusivos, como o nome do IoT Hub e o nome da conta de armazenamento. Para tornar os recursos mais fácil de nomenclatura, esses nomes de recursos são configuradas para acrescentar um valor de alfanumérico aleatório gerado a partir de data/hora atual. 

Se examinar o modelo, verá onde variáveis estão configuradas para estes recursos que assumem o parâmetro passado e concatenam *randomValue* para o parâmetro. 

A secção seguinte explica os parâmetros utilizados.

### <a name="parameters"></a>Parâmetros

A maioria desses parâmetros têm valores predefinidos. Aqueles terminando **_in** são concatenados com *randomValue* para torná-las globalmente exclusivo. 

**randomValue**: Este valor é gerado a partir de data/hora atual quando implementar o modelo. Este campo não está no ficheiro de parâmetros, que são gerados no próprio modelo.

**subscriptionId**: Este campo é definido por si para a subscrição na qual está a implementar o modelo. Este campo não está no ficheiro de parâmetros, uma vez que ele é definido por si.

**IoTHubName_in**: Este campo é o nome do IoT Hub base, que é concatenado com randomValue ser globalmente exclusivo.

**Localização**: Este campo é a região do Azure na qual estiver a implementar, por exemplo, "westus".

**consumer_group**: Este campo é o grupo de consumidores definido para mensagens recebidas através do ponto de final de encaminhamento. É utilizado para filtrar os resultados no Azure Stream Analytics. Por exemplo, há o fluxo todo em que obtenha tudo o que, ou se tiver dados que com consumer_group definido como **Contoso**, em seguida, pode configurar um fluxo do Azure Stream Analytics (e relatório do Power BI) para mostrar apenas essas entradas do utilizador. Este campo é utilizado na parte 2 deste tutorial.

**sku_name**: Este campo é o dimensionamento para o IoT Hub. Este valor tem de ser S1 ou superior; um escalão gratuito não funciona para este tutorial porque ele não permite que vários pontos de extremidade.

**sku_units**: Este campo fica com o **sku_name**, e é o número de unidades do IoT Hub que pode ser utilizado.

**d2c_partitions**: Este campo é o número de partições utilizado para o fluxo de eventos.

**storageAccountName_in**: Este campo é o nome da conta de armazenamento a ser criada. As mensagens são encaminhadas para um contentor na conta de armazenamento. Este campo é concatenado com randomValue para que seja globalmente exclusivo.

**storageContainerName**: Este campo é o nome do contentor no qual as mensagens encaminhadas para a conta de armazenamento são armazenadas.

**storage_endpoint**: Este campo é o nome para o ponto final conta de armazenamento utilizado pelo encaminhamento de mensagens.

**service_bus_namespace_in**: Este campo é o nome do espaço de nomes do Service Bus a ser criada. Este valor é concatenado com randomValue para que seja globalmente exclusivo.

**service_bus_queue_in**: Este campo é o nome da fila do Service Bus utilizado para encaminhamento de mensagens. Este valor é concatenado com randomValue para que seja globalmente exclusivo.

**AuthRules_sb_queue**: Este campo é as regras de autorização para a fila do service bus, usada para recuperar a cadeia de ligação para a fila.

### <a name="variables"></a>Variáveis

Estes valores são utilizados no modelo e, principalmente são derivados de parâmetros.

**queueAuthorizationRuleResourceId**: Este campo é o ResourceId para a regra de autorização para a fila do Service Bus. ResourceId por sua vez é utilizado para obter a cadeia de ligação para a fila.

**iotHubName**: Este campo é o nome do IoT Hub depois de ter randomValue concatenado. 

**storageAccountName**: Este campo é o nome da conta de armazenamento depois de ter randomValue concatenado. 

**service_bus_namespace**: Este campo é o espaço de nomes, depois de ter randomValue concatenado.

**service_bus_queue**: Este campo é o nome da fila do Service Bus depois de ter randomValue concatenado.

**sbVersion**: A versão de API do Service Bus para utilizar. Neste caso, é "2017-04-01".

### <a name="resources-storage-account-and-container"></a>Recursos: Conta de armazenamento e um contentor

O primeiro recurso criado é a conta de armazenamento, juntamente com o contentor para o qual as mensagens são roteadas. O contêiner é um recurso sob a conta de armazenamento. Ele tem um `dependsOn` cláusula para a conta de armazenamento, exigir que a conta de armazenamento ser criado antes do contentor.

Eis o que esta secção é semelhante a:

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

### <a name="resources-service-bus-namespace-and-queue"></a>Recursos: Espaço de nomes do Service Bus e fila

O segundo recurso criado é o espaço de nomes do Service Bus, juntamente com a fila do Service Bus para o qual as mensagens são roteadas. O SKU é definido como standard. A versão de API é obtida a partir de variáveis. Também é definida para ativar o espaço de nomes do Service Bus, quando implementa nesta secção (Estado: ativo). 

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

Esta secção cria uma fila do Service Bus. Esta parte do script tem um `dependsOn` cláusula que garante que o espaço de nomes é criada antes da fila.

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

### <a name="resources-iot-hub-and-message-routing"></a>Recursos: O IOT Hub e o roteamento de mensagens

Agora que a conta de armazenamento e a fila do Service Bus foram criados, crie o IoT Hub que redireciona mensagens aos mesmos. O modelo de RM utiliza `dependsOn` cláusulas, para que ela não tente criar o hub, antes dos recursos do Service Bus e a conta de armazenamento foram criados. 

Aqui é a primeira parte da secção do IoT Hub. Esta parte do modelo configura as dependências e começa com as propriedades.

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

A secção seguinte é a seção para a configuração de roteamento de mensagem para o Iot Hub. Em primeiro lugar, é a seção para os pontos finais. Esta parte do modelo define os pontos finais de encaminhamento para a fila do Service Bus e a conta de armazenamento, incluindo as cadeias de ligação.

Para criar a cadeia de ligação para a fila, precisa queueAuthorizationRulesResourcedId, que é obtido inline. Para criar a cadeia de ligação para a conta de armazenamento, obter a chave de armazenamento primário e, em seguida, utilizá-lo no formato da cadeia de ligação.

A configuração de ponto final é também onde define o formato de blob `AVRO` ou `JSON`.

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

Nesta secção seguinte é para as rotas de mensagens para os pontos finais. Existe um conjunto cópia de segurança para cada ponto de extremidade, portanto, há um para a fila do Service Bus e outro para o contentor da conta de armazenamento.

Lembre-se de que a condição de consulta para as mensagens que está a ser encaminhado para o armazenamento está `level="storage"`, e a condição de consulta para as mensagens que está a ser encaminhado para a fila do Service Bus é `level="critical"`.

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

Este json mostra o resto da seção do IoT Hub, que contém informações padrão e o SKU para o hub.

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

### <a name="resources-service-bus-queue-authorization-rules"></a>Recursos: Regras de autorização de fila do Service Bus

A regra de autorização de fila do Service Bus é utilizada para obter a cadeia de ligação para a fila do Service Bus. Ele usa um `dependsOn` cláusula para se certificar de que não é criada antes do espaço de nomes do Service Bus e a fila do Service Bus.

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

Nesta secção, vai criar um grupo de consumidores para os dados de IoT Hub para ser utilizado pelo Azure Stream Analytics na segunda parte deste tutorial.

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

Se pretender enviar um valor de volta para o script de implementação a apresentar, utiliza uma secção de saída. Esta parte do modelo devolve a cadeia de ligação para a fila do Service Bus. Retornando que um valor não é necessário, vem incluído como um exemplo de como devolver resultados para o script de chamada.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>Implementar o modelo do RM

Para implementar o modelo para o Azure, carregar o modelo e o ficheiro de parâmetros para o Azure Cloud Shell e, em seguida, executar um script para implementar o modelo. Abra o Azure Cloud Shell e inicie sessão. Este exemplo utiliza o PowerShell.

Para carregar os ficheiros, selecione o **carregar/transferir ficheiros** ícone na barra de menus, em seguida, escolha carregar.

![Barra de menus do cloud Shell com ficheiros de carregamento/transferência realçado](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Utilizar o Explorador de ficheiros que aparece para encontrar os arquivos no disco local e selecioná-los, em seguida, escolha **aberto**.

Depois dos ficheiros são carregados, uma caixa de diálogo de resultados mostra algo semelhante à imagem seguinte.

![Barra de menus do cloud Shell com ficheiros de carregamento/transferência realçado](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

Os ficheiros são carregados para a partilha utilizada pela sua instância do Cloud Shell. 

Execute o script para executar a implantação. A última linha deste script obtém a variável que foi configurada para ser retornado – a cadeia de ligação de fila do Service Bus.

Estas variáveis são definidas nesse script.

**$RGName** é o nome do grupo de recursos ao qual pretende implementar o modelo. Este campo é criado antes de implementar o modelo.

**$location** é a localização do Azure a ser utilizado para o modelo, como "westus".

**deploymentname** é um nome de atribuir à implementação para obter o valor da variável devolvido.

Aqui está o script do PowerShell. Copiar este script do PowerShell e cole-a na janela do Cloud Shell e, em seguida, pressionar Enter para executá-lo.

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

Se tiver erros de script, pode editar o script localmente, carregue-o novamente para o Cloud Shell e execute novamente o script. Após a conclusão do script em execução com êxito, continue para o passo seguinte.

## <a name="create-simulated-device"></a>Criar dispositivo simulado

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Ver o roteamento de mensagens no portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Passos Seguintes

Agora que tem todos os recursos de configurar e as rotas de mensagem estão configuradas, avance para o próximo tutorial para saber como processar e exibir as informações sobre as mensagens encaminhadas.

> [!div class="nextstepaction"]
> [Parte 2: ver os resultados de roteamento de mensagem](tutorial-routing-view-message-routing-results.md)
