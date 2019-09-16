---
title: Configurar o roteamento de mensagens para o Hub IoT do Azure usando o CLI do Azure | Microsoft Docs
description: Configurar o roteamento de mensagens para o Hub IoT do Azure usando o CLI do Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 103a18389a2b956f20b61ce45d045fb9a11c4356
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984717"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Tutorial: Usar o CLI do Azure para configurar o roteamento de mensagens do Hub IoT

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Baixar o script (opcional)

Para a segunda parte deste tutorial, você baixa e executa um aplicativo do Visual Studio para enviar mensagens para o Hub IoT. Há uma pasta no download que contém o modelo de Azure Resource Manager e o arquivo de parâmetros, bem como os scripts do CLI do Azure e do PowerShell.

Se você quiser exibir o script concluído, baixe os [exemplos de IOT C# do Azure](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Descompacte o arquivo. zip mestre. O script de CLI do Azure está em/iot-hub/Tutorials/Routing/SimulatedDevice/resources/como **iothub_routing_cli. azcli**.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Use o CLI do Azure para criar seus recursos

Há vários nomes de recursos que devem ser globalmente exclusivos, como o nome do Hub IoT e o nome da conta de armazenamento. Para tornar isso mais fácil, esses nomes de recursos são anexados com um valor alfanumérico aleatório chamado *randomValue*. O randomValue é gerado uma vez na parte superior do script e acrescentado aos nomes de recursos conforme necessário em todo o script. Se você não quiser que ele seja aleatório, você pode defini-lo como uma cadeia de caracteres vazia ou como um valor específico. 

> [!IMPORTANT]
> As variáveis definidas no script inicial também são usadas pelo script de roteamento, portanto, execute todo o script na mesma sessão de Cloud Shell. Se você abrir uma nova sessão para executar o script para configurar o roteamento, várias das variáveis terão valores ausentes.
>

Copie e cole o script abaixo em Cloud Shell e pressione Enter. Ele executa o script uma linha por vez. Esta primeira seção do script criará os recursos base para este tutorial, incluindo a conta de armazenamento, o Hub IoT, o namespace do barramento de serviço e a fila do barramento de serviço. À medida que você passar pelo restante do tutorial, copie cada bloco de script e cole-o em Cloud Shell para executá-lo.

> [!TIP]
> Uma dica sobre a depuração: esse script usa o símbolo de continuação (a `\`barra invertida) para tornar o script mais legível. Se você tiver um problema ao executar o script, certifique-se de que não haja espaços após qualquer uma das barras invertidas.
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

Agora que os recursos base estão configurados, você pode configurar o roteamento de mensagens.

## <a name="set-up-message-routing"></a>Configurar o encaminhamento de mensagens

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Para criar um ponto de extremidade de roteamento, use [AZ IOT Hub Routing-Endpoint Create](/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest#az-iot-hub-routing-endpoint-create). Para criar a rota de mensagem para o ponto de extremidade, use [AZ IOT Hub Route Create](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create).

### <a name="route-to-a-storage-account"></a>Rotear para uma conta de armazenamento

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Primeiro, configure o ponto de extremidade para a conta de armazenamento e, em seguida, configure a rota. 

Essas variáveis são definidas:

**storageConnectionString**: Esse valor é recuperado da conta de armazenamento configurada no script anterior. Ele é usado pelo roteamento de mensagens para acessar a conta de armazenamento.

  **resourceGroup**: Há duas ocorrências do grupo de recursos – defina-as para seu grupo de recursos.

**SubscriptionId do ponto de extremidade**: Este campo é definido como a SubscriptionId do Azure para o ponto de extremidade. 

**endpointType**: Esse campo é o tipo de ponto de extremidade. Esse valor deve ser definido como `azurestoragecontainer`, `eventhub`, `servicebusqueue`ou `servicebustopic`. Para suas finalidades aqui, defina- `azurestoragecontainer`o como.

**iotHubName**: Esse campo é o nome do Hub que fará o roteamento.

**containerName**: Esse campo é o nome do contêiner na conta de armazenamento para o qual os dados serão gravados.

**codificação**: Esse campo será `avro` ou `json`. Isso denota o formato dos dados armazenados.

**routeName**: Esse campo é o nome da rota que você está configurando. 

**endpointName**: Este campo é o nome que identifica o ponto de extremidade. 

**habilitado**: Esse campo usa como `true`padrão, indicando que a rota da mensagem deve ser habilitada após a criação.

**condição**: Esse campo é a consulta usada para filtrar as mensagens enviadas para esse ponto de extremidade. A condição de consulta para as mensagens que estão sendo roteadas para o armazenamento é `level="storage"`.

Copie esse script e cole-o na janela Cloud Shell e execute-o.

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

A próxima etapa é criar o ponto de extremidade de roteamento para a conta de armazenamento. Você também especifica o contêiner no qual os resultados serão armazenados. O contêiner foi criado anteriormente quando a conta de armazenamento foi criada.

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

Em seguida, crie a rota para o ponto de extremidade de armazenamento. A rota de mensagem designa para onde enviar as mensagens que atendem à especificação de consulta. 

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

### <a name="route-to-a-service-bus-queue"></a>Rotear para uma fila do barramento de serviço

Agora configure o encaminhamento para a fila do Service Bus. Para recuperar a cadeia de conexão da fila do barramento de serviço, você deve criar uma regra de autorização que tenha os direitos corretos definidos. O script a seguir cria uma regra de autorização para a fila do `sbauthrule`barramento de serviço chamada e define `Listen Manage Send`os direitos para. Depois que essa regra de autorização for definida, você poderá usá-la para recuperar a cadeia de conexão para a fila.

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

Agora, use a regra de autorização para recuperar a cadeia de conexão para a fila do barramento de serviço.

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

Agora, configure o ponto de extremidade de roteamento e a rota de mensagem para a fila do barramento de serviço. Essas variáveis são definidas:

**endpointName**: Este campo é o nome que identifica o ponto de extremidade. 

**endpointType**: Esse campo é o tipo de ponto de extremidade. Esse valor deve ser definido como `azurestoragecontainer`, `eventhub`, `servicebusqueue`ou `servicebustopic`. Para suas finalidades aqui, defina- `servicebusqueue`o como.

**routeName**: Esse campo é o nome da rota que você está configurando. 

**condição**: Esse campo é a consulta usada para filtrar as mensagens enviadas para esse ponto de extremidade. A condição de consulta para as mensagens que estão sendo roteadas para a `level="critical"`fila do barramento de serviço é.

Aqui está a CLI do Azure para o ponto de extremidade de roteamento e a rota de mensagem para a fila do barramento de serviço.

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

### <a name="view-message-routing-in-the-portal"></a>Exibir o roteamento de mensagens no portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Agora que você tem os recursos configurados e as rotas de mensagens definidas, avance para o próximo tutorial para saber como enviar mensagens para o Hub IoT e vê-las roteadas para os diferentes destinos. 

> [!div class="nextstepaction"]
> [Parte 2-exibir os resultados de roteamento de mensagens](tutorial-routing-view-message-routing-results.md)
