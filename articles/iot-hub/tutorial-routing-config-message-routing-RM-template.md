---
title: Configure o encaminhamento de mensagens para o Hub Azure IoT usando um modelo de Gestor de Recursos Azure
description: Configure o encaminhamento de mensagens para o Hub Azure IoT usando um modelo de Gestor de Recursos Azure
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 8f245653a8b84944e1e8a3f48a49992f0065be58
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74084398"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Tutorial: Use um modelo de Gestor de Recursos Azure para configurar o encaminhamento de mensagens IoT Hub

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Encaminhamento de mensagens

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>Descarregue o modelo e o arquivo de parâmetros

Para a segunda parte deste tutorial, você descarrega e executa uma aplicação do Estúdio Visual para enviar mensagens para o IoT Hub. Há uma pasta no download que contém o modelo de modelo e parâmetros do Gestor de Recursos Azure, bem como os scripts Azure CLI e PowerShell.

Vá em frente e baixe as [amostras Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) agora. Desaperte o ficheiro master.zip. O modelo do Gestor de Recursos e o ficheiro de parâmetros estão em /iot-hub/Tutoriais/Routing/SimuladoDispositivo/recursos/ como **template_iothub.json** e **template_iothub_parameters.json**.

## <a name="create-your-resources"></a>Crie os seus recursos

Você vai usar um modelo de Gestor de Recursos Azure (RM) para criar todos os seus recursos. Os scripts Azure CLI e PowerShell podem ser executados algumas linhas de cada vez. Um modelo RM é implantado num passo. Este artigo mostra-lhe as secções separadamente para ajudá-lo a entender cada uma delas. Em seguida, irá mostrar-lhe como implementar o modelo, e criar o dispositivo virtual para testar. Depois de o modelo ser implementado, pode ver a configuração de encaminhamento de mensagens no portal.

Existem vários nomes de recursos que devem ser globalmente únicos, como o nome IoT Hub e o nome da conta de armazenamento. Para facilitar a designação dos recursos, esses nomes de recursos são criados para anexar um valor alfanumérico aleatório gerado a partir da data/hora atual. 

Se olhar para o modelo, verá onde são criadas variáveis para estes recursos que levam o parâmetro passado e concatenam *randomValue* para o parâmetro. 

A secção seguinte explica os parâmetros utilizados.

### <a name="parameters"></a>Parâmetros

A maioria destes parâmetros têm valores padrão. Os que terminam com **_in** são concatenados com *randomValue* para torná-los globalmente únicos. 

**randomValue**: Este valor é gerado a partir da data/hora atual quando implementa o modelo. Este campo não está no ficheiro de parâmetros, pois é gerado no próprio modelo.

**subscriçãoId**: Este campo está definido para si para a subscrição em que está a implementar o modelo. Este campo não está no ficheiro de parâmetros, uma vez que está definido para si.

**IoTHubName_in**: Este campo é o nome base IoT Hub, que é concatenado com o valor aleatório Value para ser globalmente único.

**localização**: Este campo é a região de Azure na qual está implantado, como "westus".

**consumer_group**: Este campo é o grupo de consumidores definido para mensagens que vêm através do ponto final de encaminhamento. É usado para filtrar resultados no Azure Stream Analytics. Por exemplo, há todo o fluxo onde obtém tudo, ou se tem dados que chegam com consumer_group definidos para **Contoso,** então pode configurar um fluxo De Análise de Fluxo Azure (e relatório Power BI) para mostrar apenas essas entradas. Este campo é usado na parte 2 deste tutorial.

**sku_name**: Este campo é o escalonamento para o Hub IoT. Este valor deve ser S1 ou superior; um nível livre não funciona para este tutorial porque não permite vários pontos finais.

**sku_units**: Este campo acompanha o **sku_name**, e é o número de unidades IoT Hub que podem ser utilizadas.

**d2c_partitions**: Este campo é o número de divisórias utilizadas para o fluxo de eventos.

**storageAccountName_in**: Este campo é o nome da conta de armazenamento a criar. As mensagens são encaminhadas para um contentor na conta de armazenamento. Este campo é concatenado com o randomValue para torná-lo globalmente único.

**storageNome contentor**: Este campo é o nome do recipiente no qual as mensagens encaminhadas para a conta de armazenamento são armazenadas.

**storage_endpoint**: Este campo é o nome para o ponto final da conta de armazenamento utilizado pelo encaminhamento da mensagem.

**service_bus_namespace_in**: Este campo é o nome do espaço de nome do Ônibus de serviço a criar. Este valor é concatenado com o randomValue para torná-lo globalmente único.

**service_bus_queue_in**: Este campo é o nome da fila do Ônibus de serviço utilizada para mensagens de encaminhamento. Este valor é concatenado com o randomValue para torná-lo globalmente único.

**AuthRules_sb_queue**: Este campo são as regras de autorização para a fila do autocarro de serviço, utilizada para recuperar a corda de ligação para a fila.

### <a name="variables"></a>Variáveis

Estes valores são usados no modelo, e são principalmente derivados de parâmetros.

**filaAuthorizationRuleResourceId**: Este campo é o ResourceId para a regra de autorização para a fila do Autocarro de Serviço. O ResourceId é, por sua vez, utilizado para recuperar a corda de ligação para a fila.

**iotHubName**: Este campo é o nome do Hub IoT depois de ter concatenado aleatoriamente. 

**armazenamentoNome da conta**: Este campo é o nome da conta de armazenamento depois de ter concatenado por conta aleatória. 

**service_bus_namespace**: Este campo é o espaço de nome depois de ter concatenado aleatoriamente.

**service_bus_queue**: Este campo é o nome da fila do Autocarro de Serviço depois de ter concatenado aleatoriamente.

**sbVersão**: Versão THe da API do autocarro de serviço para usar. Neste caso, é "2017-04-01".

### <a name="resources-storage-account-and-container"></a>Recursos: Conta de armazenamento e contentor

O primeiro recurso criado é a conta de armazenamento, juntamente com o recipiente para o qual as mensagens são encaminhadas. O recipiente é um recurso sob a conta de armazenamento. Tem uma `dependsOn` cláusula para a conta de armazenamento, exigindo que a conta de armazenamento seja criada antes do contentor.

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

### <a name="resources-service-bus-namespace-and-queue"></a>Recursos: Espaço de nome de ônibus de serviço e fila

O segundo recurso criado é o espaço de nome service Bus, juntamente com a fila do Ônibus de serviço para o qual as mensagens são encaminhadas. O SKU está definido como padrão. A versão API é recuperada das variáveis. Também está programado para ativar o espaço de nome do Autocarro de Serviço quando implementar esta secção (status:Ative). 

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

Esta secção cria a fila service Bus. Esta parte do script `dependsOn` tem uma cláusula que garante que o espaço de nome é criado antes da fila.

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

### <a name="resources-iot-hub-and-message-routing"></a>Recursos: Hub de iot e encaminhamento de mensagens

Agora que a conta de armazenamento e a fila do Ônibus de Serviço foram criadas, cria-se o IoT Hub que lhes encaminha mensagens. O modelo RM utiliza `dependsOn` cláusulas para que não tente criar o hub antes de os recursos do Bus de Serviço e a conta de armazenamento terem sido criadas. 

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

A próxima secção é a secção para a configuração de encaminhamento de mensagens para o Hub Iot. Primeiro é a secção para os pontos finais. Esta parte do modelo configura os pontos finais de encaminhamento para a fila do Ônibus de serviço e a conta de armazenamento, incluindo as cordas de ligação.

Para criar a cadeia de ligação para a fila, necessita da filaAuthorizationRulesResourcedId, que é recuperada inline. Para criar a cadeia de ligação para a conta de armazenamento, recupere a chave de armazenamento primária e, em seguida, use-a no formato para a cadeia de ligação.

A configuração do ponto final também é `AVRO` `JSON`onde se define o formato blob para ou .

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

A próxima secção é para as rotas da mensagem para os pontos finais. Há um configurado para cada ponto final, por isso há um para a fila do Ônibus de serviço e outro para o contentor da conta de armazenamento.

Lembre-se que a condição de consulta para `level="storage"`as mensagens que estão a ser encaminhadas `level="critical"`para o armazenamento é , e a condição de consulta para as mensagens que estão a ser encaminhadas para a fila do Autocarro de Serviço é .

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

Este json mostra o resto da secção IoT Hub, que contém informações padrão e o SKU para o centro.

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

A regra de autorização de fila de ônibus de serviço é usada para recuperar a corda de ligação para a fila do ônibus de serviço. Utiliza uma `dependsOn` cláusula para garantir que não é criada antes do espaço de nome do Bus de Serviço e da fila do Ônibus de Serviço.

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

Nesta secção, cria-se um Grupo de Consumidores para que os dados do IoT Hub sejam utilizados pelo Azure Stream Analytics na segunda parte deste tutorial.

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

Se pretender enviar um valor de volta para o script de implementação a exibir, utilize uma secção de saída. Esta parte do modelo devolve a corda de ligação para a fila do Ônibus de serviço. Devolver um valor não é necessário, está incluído como um exemplo de como devolver os resultados ao script de chamada.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>Implementar o modelo RM

Para implantar o modelo para Azure, faça upload do modelo e do ficheiro de parâmetros para Azure Cloud Shell e, em seguida, execute um script para implementar o modelo. Abra a Nuvem Azure e inscreva-se. Este exemplo utiliza powerShell.

Para fazer o upload dos ficheiros, selecione o ícone de **ficheiros Upload/Download** na barra de menus e, em seguida, escolha o Upload.

![Barra de menu Cloud Shell com ficheiros upload/download destacados](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Utilize o File Explorer que aparece para encontrar os ficheiros no disco local e selecione-os e, em seguida, escolha **Open**.

Depois de os ficheiros serem carregados, um diálogo de resultados mostra algo como a seguinte imagem.

![Barra de menu Cloud Shell com ficheiros upload/download destacados](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

Os ficheiros são enviados para a parte utilizada pela sua instância Cloud Shell. 

Execute o guião para executar a implementação. A última linha deste guião recupera a variável que foi criada para ser devolvida.

O script define e utiliza estas variáveis:

**$RGName** é o nome do grupo de recursos para implantar o modelo. Este campo é criado antes de implementar o modelo.

**$location** é a localização Azure a ser usada para o modelo, como "westus".

o nome de **implantação** é um nome que atribui à implantação para recuperar o valor variável de retorno.

Aqui está o guião da PowerShell. Copie este script PowerShell e cole-o na janela Cloud Shell e, em seguida, bata no Enter para executá-lo.

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

Se tiver erros de script, pode editar o script localmente, carregá-lo novamente para a Cloud Shell e executar o script novamente. Depois de o guião terminar a correr com sucesso, continue até ao próximo passo.

## <a name="create-simulated-device"></a>Criar dispositivo simulado

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Ver encaminhamento de mensagem no portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Agora que tem todos os recursos configurados e as rotas de mensagens estão configuradas, avance para o próximo tutorial para aprender a processar e exibir as informações sobre as mensagens encaminhadas.

> [!div class="nextstepaction"]
> [Parte 2 - Veja os resultados do encaminhamento da mensagem](tutorial-routing-view-message-routing-results.md)
