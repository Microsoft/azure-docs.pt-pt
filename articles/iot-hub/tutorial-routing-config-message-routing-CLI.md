---
title: Configurar o encaminhamento de mensagens para o IoT Hub do Azure com a CLI do Azure | Documentos da Microsoft
description: Configurar o encaminhamento de mensagens para o IoT Hub do Azure com a CLI do Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 6faa585f1ad38eb981e0bbffffef603c4aab0bc8
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360282"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Tutorial: Utilizar a CLI do Azure para configurar o encaminhamento de mensagens do IoT Hub

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Transferir o script (opcional)

Para a segunda parte deste tutorial, transfira e execute um aplicativo do Visual Studio para enviar mensagens para o IoT Hub. Existe uma pasta no download que contém o modelo Azure Resource Manager e o ficheiro de parâmetros, bem como os scripts da CLI do Azure e PowerShell.

Se quiser ver o script concluído, transfira o [IoT do Azure C# exemplos](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Deszipe o ficheiro de Master. O script da CLI do Azure é /iot-hub/Tutorials/Routing/SimulatedDevice/resources/como **iothub_routing_cli.azcli**.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Utilizar a CLI do Azure para criar os seus recursos

Existem vários nomes de recurso que devem ser globalmente exclusivos, como o nome do IoT Hub e o nome da conta de armazenamento. Para facilitar este processo, esses nomes de recursos são acrescentados com um valor de alfanumérico aleatório chamado *randomValue*. RandomValue é gerado, uma vez, na parte superior do script e acrescentado para os nomes de recursos, conforme necessário em todo o script. Se não quiser que ela seja aleatório, pode configurá-lo como uma cadeia vazia ou para um valor específico. 

> [!IMPORTANT]
> As variáveis definidas no script inicial também são utilizadas pelo script de encaminhamento, assim, executar todos os scripts na mesma sessão do Cloud Shell. Se abrir uma nova sessão para executar o script para configurar o encaminhamento, várias das variáveis falta valores.
>

Copie e cole o script abaixo no Cloud Shell e prima Enter. A linha de um script é executado cada vez. Nesta primeira secção do script irá criar os recursos de bases para este tutorial, incluindo a conta de armazenamento, o IoT Hub, o espaço de nomes do Service Bus e a fila do Service Bus. À medida que avança o resto do tutorial, cada bloco de script de copiar e colá-lo no Cloud Shell para executá-lo.

> [!TIP]
> Uma dica sobre a depuração: este script utiliza o símbolo de continuação (a barra invertida `\`) para que o script mais legível. Se tiver um problema ao executar o script, certifique-se de que existem sem espaços após qualquer uma das barras invertidas.
> 

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing rules.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

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

Agora que os recursos de bases são configurados, pode configurar o encaminhamento de mensagens.

## <a name="set-up-message-routing"></a>Configurar o encaminhamento de mensagens

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Para criar um ponto de final de encaminhamento, utilize [az iot hub encaminhamento ponto final de criar](/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest#az-iot-hub-routing-endpoint-create). Para criar a rota de mensagem para o ponto final, utilize [rota do az iot hub criar](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create).

### <a name="route-to-a-storage-account"></a>Encaminhar para uma conta de armazenamento

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Em primeiro lugar, configurar o ponto final para a conta de armazenamento, em seguida, configurar a rota. 

Estas variáveis são definidas:

**storageConnectionString**: Este valor é obtido a partir da conta de armazenamento definida no script anterior. Ele é usado pela encaminhamento para aceder à conta de armazenamento de mensagens.

  **resourceGroup**: Existem dois ocorrências do grupo de recursos--defini-las para o grupo de recursos.

**ponto final subscriptionID**: Este campo está definido para o subscriptionID do Azure para o ponto final. 

**endpointType**: Este campo é o tipo de ponto de extremidade. Este valor tem de ser definido como `azurestoragecontainer`, `eventhub`, `servicebusqueue`, ou `servicebustopic`. Para os fins pretendidos aqui, defina-o como `azurestoragecontainer`.

**iotHubName**: Este campo é o nome do hub que fará o encaminhamento.

**containerName**: Este campo é o nome do contentor na conta de armazenamento para o qual os dados serão escritos.

**Codificação**: Este campo poderá ser `avro` ou `json`. Isso indica o formato dos dados armazenados.

**routeName**: Este campo é o nome da rota que estiver a configurar. 

**endpointName**: Este campo é o nome identificando o ponto final. 

**enabled**: Este campo é predefinido para `true`, que indica que a rota de mensagem deve ser ativada depois de criado.

**Condição**: Este campo é a consulta usada para filtrar as mensagens enviadas para este ponto final. A condição de consulta para as mensagens que está a ser encaminhado para o armazenamento é `level="storage"`.

Copie esse script e cole-a na janela do Cloud Shell e executá-lo.

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

A próxima etapa é criar o ponto final de encaminhamento para a conta de armazenamento. Também especificar o contentor no qual serão armazenados os resultados. O contentor foi criado anteriormente, quando foi criada a conta de armazenamento.

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

Em seguida, crie a rota para o ponto final de armazenamento. A rota de mensagem designa para onde enviar as mensagens que satisfazem a especificação de consulta. 

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

### <a name="route-to-a-service-bus-queue"></a>Encaminhar para uma fila do Service Bus

Agora configure o encaminhamento para a fila do Service Bus. Para obter a cadeia de ligação para a fila do Service Bus, tem de criar uma regra de autorização que tem os direitos corretos definidos. O seguinte script cria uma regra de autorização para a fila do Service Bus chamada `sbauthrule`e define os direitos para `Listen Manage Send`. Esta regra de autorização definida, pode usá-lo a obter a cadeia de ligação para a fila.

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

Utilize a regra de autorização para obter a cadeia de ligação para a fila do Service Bus.

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

Agora configure o ponto final de encaminhamento e a rota de mensagem de fila do Service Bus. Estas variáveis são definidas:

**endpointName**: Este campo é o nome identificando o ponto final. 

**endpointType**: Este campo é o tipo de ponto de extremidade. Este valor tem de ser definido como `azurestoragecontainer`, `eventhub`, `servicebusqueue`, ou `servicebustopic`. Para os fins pretendidos aqui, defina-o como `servicebusqueue`.

**routeName**: Este campo é o nome da rota que estiver a configurar. 

**Condição**: Este campo é a consulta usada para filtrar as mensagens enviadas para este ponto final. A condição de consulta para as mensagens que está a ser encaminhado para a fila do Service Bus é `level="critical"`.

Eis a CLI do Azure para o ponto final de encaminhamento e a rota de mensagem de fila do Service Bus.

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

### <a name="view-message-routing-in-the-portal"></a>Ver o roteamento de mensagens no portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Passos Seguintes

Agora que tem os recursos de configuração e as rotas de mensagem configuradas, avance para o próximo tutorial para saber como enviar mensagens para o hub IoT e vê-los a ser encaminhados para os diferentes destinos. 

> [!div class="nextstepaction"]
> [Parte 2: ver os resultados de roteamento de mensagem](tutorial-routing-view-message-routing-results.md)