---
title: Tutorial - Configurar o encaminhamento de mensagens para Azure IoT Hub utilizando o Azure CLI
description: Tutorial - Configurar o encaminhamento de mensagens para Azure IoT Hub utilizando o Azure CLI. Dependendo das propriedades da mensagem, encaminhe para uma conta de armazenamento ou uma fila de ônibus de serviço.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 25c3ff7582ed408776c0ae6904e4bacddd89e40b
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624175"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Tutorial: Use o CLI Azure para configurar o encaminhamento de mensagens IoT Hub

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Faça o download do script (opcional)

Para a segunda parte deste tutorial, você descarrega e execute uma aplicação Visual Studio para enviar mensagens para o IoT Hub. Existe uma pasta no download que contém o modelo e o ficheiro de parâmetros do Azure Resource Manager, bem como os scripts Azure CLI e PowerShell.

Se quiser ver o script acabado, descarregue as [amostras Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Desaperte o ficheiro master.zip. O script Azure CLI está em /iot-hub/Tutorials/Encaminhing/SimulatedDevice/resources/ as **iothub_routing_cli.azcli**.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Use o CLI Azure para criar os seus recursos

Copie e cole o script abaixo na Cloud Shell e prima Enter. Executa o guião uma linha de cada vez. Esta primeira secção do script irá criar os recursos base para este tutorial, incluindo a conta de armazenamento, IoT Hub, Service Bus Namespace e fila service bus. Ao passar pelo resto do tutorial, copie cada bloco de script e cole-o na Cloud Shell para executá-lo.

> [!TIP]
> Uma dica sobre depuração: este script usa o símbolo de continuação (o backslash `\` ) para tornar o script mais legível. Se tiver algum problema em executar o script, certifique-se de que a sessão cloud Shell está em execução `bash` e que não há espaços depois de qualquer uma das costas.
> 

Existem vários nomes de recursos que devem ser globalmente únicos, como o nome IoT Hub e o nome da conta de armazenamento. Para facilitar isto, esses nomes de recursos são anexados a um valor alfanumérico aleatório chamado *RandomValue*. O RandomValue é gerado uma vez no topo do script e anexado aos nomes de recursos necessários ao longo do script. Se não quiser que seja aleatório, pode defini-lo para uma corda vazia ou para um valor específico. 

> [!IMPORTANT]
> As variáveis definidas no script inicial também são usadas pelo script de encaminhamento, por isso executar todo o script na mesma sessão Cloud Shell. Se abrir uma nova sessão para executar o script para configurar o encaminhamento, várias das variáveis estarão em falta de valores.
>

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-iot

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, 
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName
```

Agora que os recursos base estão configurado, pode configurar o encaminhamento de mensagens.

## <a name="set-up-message-routing"></a>Configurar o encaminhamento de mensagens

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Para criar um ponto final de encaminhamento, utilize [o ponto final de encaminhamento do hub az iot](/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest#az-iot-hub-routing-endpoint-create&preserve-view=true). Para criar a rota de mensagem para o ponto final, utilize [a rota az iot hub create](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create&preserve-view=true).

### <a name="route-to-a-storage-account"></a>Rota para uma conta de armazenamento

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Primeiro, crie o ponto final para a conta de armazenamento e, em seguida, instale a rota. 

Estas são as variáveis utilizadas pelo script que devem ser definidas dentro da sua sessão Cloud Shell:

**armazenamentoConnectionString**: Este valor é recuperado a partir da conta de armazenamento criada no script anterior. É utilizado pelo encaminhamento de mensagens para aceder à conta de armazenamento.

  **resourceGroup**: Existem duas ocorrências de grupo de recursos - desapedaça-as para o seu grupo de recursos.

**subscrição de ponto finalID**: Este campo está definido para a subscrição AzureID para o ponto final. 

**ponto finalType**: Este campo é o tipo de ponto final. Este valor deve ser definido para `azurestoragecontainer` `eventhub` , , ou `servicebusqueue` `servicebustopic` . Para os seus propósitos aqui, desemaça-o para `azurestoragecontainer` .

**iotHubName**: Este campo é o nome do hub que fará o encaminhamento.

**containerName**: Este campo é o nome do recipiente na conta de armazenamento para o qual os dados serão escritos.

**codificação**: Este campo será ou `avro` `json` . . Isto denota o formato dos dados armazenados.

**nome de rota**: Este campo é o nome da rota que está a configurar. 

**endpointName**: Este campo é o nome que identifica o ponto final. 

**ativado**: Este campo desresuse, `true` indicando que a rota da mensagem deve ser ativada após a criação.

**condição**: Este campo é a consulta utilizada para filtrar as mensagens enviadas para este ponto final. A condição de consulta para as mensagens que estão a ser encaminhada para o armazenamento é `level="storage"` .

Copie este script e cole-o na janela cloud Shell e execute-o.

```azurecli
##### ROUTING FOR STORAGE ##### 

endpointName="ContosoStorageEndpoint"
endpointType="azurestoragecontainer"
routeName="ContosoStorageRoute"
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)
```

O próximo passo é criar o ponto final de encaminhamento para a conta de armazenamento. Especifica também o recipiente no qual os resultados serão armazenados. O contentor foi criado anteriormente quando a conta de armazenamento foi criada.

```azurecli
# Create the routing endpoint for storage.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName \
  --resource-group $resourceGroup \
  --encoding avro
```

Em seguida, crie a rota para o ponto final de armazenamento. A rota da mensagem designa para onde enviar as mensagens que satisfaçam a especificação de consulta. 

```azurecli
# Create the route for the storage endpoint.
az iot hub route create \
  --name $routeName \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
```

### <a name="route-to-a-service-bus-queue"></a>Rota para uma fila de ônibus de serviço

Agora configure o encaminhamento para a fila do Service Bus. Para recuperar o fio de ligação para a fila do Service Bus, tem de criar uma regra de autorização que tenha os direitos corretos definidos. O seguinte script cria uma regra de autorização para a fila do Service Bus chamada `sbauthrule` , e define os direitos de `Listen Manage Send` . Uma vez definida esta regra de autorização, pode usá-la para recuperar a cadeia de ligação para a fila.

```azurecli
# Create the authorization rule for the Service Bus queue.
az servicebus queue authorization-rule create \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --rights Listen Manage Send \
  --subscription $subscriptionID
```

Agora utilize a regra de autorização para recuperar a cadeia de ligação à fila do Service Bus.

```azurecli
# Get the Service Bus queue connection string.
# The "-o tsv" ensures it is returned without the default double-quotes.
sbqConnectionString=$(az servicebus queue authorization-rule keys list \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --subscription $subscriptionID \
  --query primaryConnectionString -o tsv)

# Show the Service Bus queue connection string.
echo "service bus queue connection string = " $sbqConnectionString
```

Agora, estabeleça o ponto final de encaminhamento e a rota de mensagem para a fila do Service Bus. Estas são as variáveis utilizadas pelo script que devem ser definidas dentro da sua sessão Cloud Shell:

**endpointName**: Este campo é o nome que identifica o ponto final. 

**ponto finalType**: Este campo é o tipo de ponto final. Este valor deve ser definido para `azurestoragecontainer` `eventhub` , , ou `servicebusqueue` `servicebustopic` . Para os seus propósitos aqui, desemaça-o para `servicebusqueue` .

**nome de rota**: Este campo é o nome da rota que está a configurar. 

**condição**: Este campo é a consulta utilizada para filtrar as mensagens enviadas para este ponto final. A condição de consulta para as mensagens que estão a ser encaminhada para a fila do Service Bus é `level="critical"` .

Aqui está o Azure CLI para o ponto final de encaminhamento e a rota de mensagem para a fila do Service Bus.

```azurecli
endpointName="ContosoSBQueueEndpoint"
endpointType="ServiceBusQueue"
routeName="ContosoSBQueueRoute"
condition='level="critical"'

# Set up the routing endpoint for the Service Bus queue.
# This uses the Service Bus queue connection string.
az iot hub routing-endpoint create \
  --connection-string $sbqConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --resource-group $resourceGroup 

# Set up the message route for the Service Bus queue endpoint.
az iot hub route create --name $routeName \
  --hub-name $iotHubName \
  --source-type devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
  ```

### <a name="view-message-routing-in-the-portal"></a>Ver encaminhamento de mensagens no portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Agora que tem os recursos configurados e as rotas de mensagem configuradas, avance para o próximo tutorial para aprender a enviar mensagens para o hub IoT e vê-las encaminhadas para os diferentes destinos. 

> [!div class="nextstepaction"]
> [Parte 2 - Ver os resultados do encaminhamento de mensagens](tutorial-routing-view-message-routing-results.md)
