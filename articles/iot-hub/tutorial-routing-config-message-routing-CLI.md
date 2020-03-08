---
title: Configure o encaminhamento de mensagens para o Hub Azure IoT utilizando o Azure CLI
description: Configure o encaminhamento de mensagens para o Hub Azure IoT utilizando o Azure CLI. Dependendo das propriedades na mensagem, arota para uma conta de armazenamento ou uma fila de ônibus de serviço.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 056dac7977115f97892d8dbfde0710e00237804e
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674350"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Tutorial: Use o CLI Azure para configurar o encaminhamento de mensagens IoT Hub

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Descarregue o script (opcional)

Para a segunda parte deste tutorial, você descarrega e executa uma aplicação do Estúdio Visual para enviar mensagens para o IoT Hub. Há uma pasta no download que contém o modelo de gestor de recursos azure e o ficheiro de parâmetros, bem como os scripts Azure CLI e PowerShell.

Se quiser ver o script acabado, descarregue as [amostras Azure C# IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Desaperte o ficheiro master.zip. O script Azure CLI está em /iot-hub/Tutoriais/Routing/SimuladoDispositivo/recursos/ como **iothub_routing_cli.azcli**.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Use o Azure CLI para criar os seus recursos

Copie e cole o script abaixo na Cloud Shell e prima Enter. Corre o guião uma linha de cada vez. Esta primeira secção do script criará os recursos base para este tutorial, incluindo a conta de armazenamento, IoT Hub, Service Bus Namespace e service bus queue. Ao percorrer o resto do tutorial, copie cada bloco de script e cole-o na Cloud Shell para executá-lo.

> [!TIP]
> Uma dica sobre depuração: este script usa o símbolo de continuação (o backslash `\`) para tornar o guião mais legível. Se tiver algum problema em executar o script, certifique-se de que a sua sessão cloud Shell está a decorrer `bash` e que não há espaços depois de qualquer uma das pestanas.
> 

Existem vários nomes de recursos que devem ser globalmente únicos, como o nome IoT Hub e o nome da conta de armazenamento. Para facilitar isto, esses nomes de recursos são anexados com um valor alfanumérico aleatório chamado *RandomValue*. O RandomValue é gerado uma vez no topo do script e anexado aos nomes de recursos conforme necessário ao longo do script. Se não quiser que seja aleatório, pode defini-lo numa corda vazia ou num valor específico. 

> [!IMPORTANT]
> As variáveis definidas no script inicial também são usadas pelo script de encaminhamento, por isso executa todo o script na mesma sessão cloud Shell. Se abrir uma nova sessão para executar o guião para a configuração do encaminhamento, várias das variáveis estarão faltando valores.
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

Agora que os recursos base estão configurados, pode configurar o encaminhamento da mensagem.

## <a name="set-up-message-routing"></a>Configurar o encaminhamento de mensagens

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Para criar um ponto final de encaminhamento, utilize a criação de ponto final de [encaminhamento do hub az iot](/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest#az-iot-hub-routing-endpoint-create). Para criar a rota da mensagem para o ponto final, utilize a criação da [rota do hub az iot](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create).

### <a name="route-to-a-storage-account"></a>Rota para uma conta de armazenamento

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Primeiro, configurar o ponto final para a conta de armazenamento, em seguida, configurar a rota. 

Estas são as variáveis utilizadas pelo script que deve ser definida sessão cloud Shell:

**armazenamentoConnectionString**: Este valor é recuperado da conta de armazenamento criada no script anterior. É utilizado pelo encaminhamento de mensagens para aceder à conta de armazenamento.

  **recursosGroup**: Existem duas ocorrências de grupo de recursos -- detetetá-las para o seu grupo de recursos.

id de **subscrição**de ponto final : Este campo está definido para o ID de subscrição Azure para o ponto final. 

**ponto finalType**: Este campo é o tipo de ponto final. Este valor deve ser fixado para `azurestoragecontainer`, `eventhub`, `servicebusqueue`ou `servicebustopic`. Para os seus propósitos aqui, coloque-o para `azurestoragecontainer`.

**iotHubName**: Este campo é o nome do hub que fará o encaminhamento.

**nome**do recipiente : Este campo é o nome do recipiente na conta de armazenamento para onde serão escritos os dados.

**codificação**: Este campo será `avro` ou `json`. Isto denota o formato dos dados armazenados.

**nome**de rota : Este campo é o nome da rota que está a configurar. 

**nome final**: Este campo é o nome que identifica o ponto final. 

**ativado**: Este campo descorre para `true`, indicando que a rota da mensagem deve ser ativada após a criação.

**condição**: Este campo é a consulta utilizada para filtrar as mensagens enviadas para este ponto final. A condição de consulta para as mensagens que estão a ser encaminhadas para o armazenamento é `level="storage"`.

Copie este script e cole-o na janela da Cloud Shell e execute-o.

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

Em seguida, crie a rota para o ponto final de armazenamento. A rota da mensagem designa para onde enviar as mensagens que cumprem a especificação de consulta. 

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

Agora configure o encaminhamento para a fila do Service Bus. Para recuperar a cadeia de ligação para a fila do Ônibus de serviço, deve criar uma regra de autorização que tenha os direitos corretos definidos. O seguinte guião cria uma regra de autorização para a fila de ônibus de serviço chamada `sbauthrule`, e define os direitos de `Listen Manage Send`. Uma vez definida esta regra de autorização, pode usá-la para recuperar a corda de ligação para a fila.

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

Agora use a regra de autorização para recuperar a corda de ligação à fila do Ônibus de serviço.

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

Agora, instale o ponto final de encaminhamento e a rota de mensagem para a fila do Autocarro de Serviço. Estas são as variáveis utilizadas pelo script que deve ser definida sessão cloud Shell:

**nome final**: Este campo é o nome que identifica o ponto final. 

**ponto finalType**: Este campo é o tipo de ponto final. Este valor deve ser fixado para `azurestoragecontainer`, `eventhub`, `servicebusqueue`ou `servicebustopic`. Para os seus propósitos aqui, coloque-o para `servicebusqueue`.

**nome**de rota : Este campo é o nome da rota que está a configurar. 

**condição**: Este campo é a consulta utilizada para filtrar as mensagens enviadas para este ponto final. A condição de consulta para as mensagens que estão a ser encaminhadas para a fila do Autocarro de Serviço é `level="critical"`.

Aqui está o Azure CLI para o ponto final de encaminhamento e a rota de mensagem para a fila de ônibus de serviço.

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

### <a name="view-message-routing-in-the-portal"></a>Ver encaminhamento de mensagem no portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Agora que tem os recursos configurados e as rotas de mensagens configuradas, avance para o próximo tutorial para aprender a enviar mensagens para o centro ioT e vê-los ser encaminhados para os diferentes destinos. 

> [!div class="nextstepaction"]
> [Parte 2 - Veja os resultados do encaminhamento da mensagem](tutorial-routing-view-message-routing-results.md)
