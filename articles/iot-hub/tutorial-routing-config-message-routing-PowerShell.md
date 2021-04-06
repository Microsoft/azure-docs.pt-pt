---
title: Tutorial - Configurar o encaminhamento de mensagens para Azure IoT Hub com Azure PowerShell
description: Tutorial - Configurar o encaminhamento de mensagens para Azure IoT Hub utilizando a Azure PowerShell. Dependendo das propriedades da mensagem, encaminhe para uma conta de armazenamento ou uma fila de ônibus de serviço.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: be1560bcc03ec7a26f4bc374392c746243cd731a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624076"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Tutorial: Use a Azure PowerShell para configurar o encaminhamento de mensagens IoT Hub

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Faça o download do script (opcional)

Para a segunda parte deste tutorial, você descarrega e execute uma aplicação Visual Studio para enviar mensagens para o IoT Hub. Existe uma pasta no download que contém o modelo e o ficheiro de parâmetros do Azure Resource Manager, bem como os scripts Azure CLI e PowerShell. 

Se quiser ver o script acabado, descarregue as [amostras Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Desaperte o ficheiro master.zip. O script Azure CLI está em /iot-hub/Tutorials/Encaminhing/SimulatedDevice/resources/ como **iothub_routing_psh.ps1**.

## <a name="create-your-resources"></a>Crie os seus recursos

Comece por criar os recursos com o PowerShell.

### <a name="use-powershell-to-create-your-base-resources"></a>Use o PowerShell para criar os seus recursos base

Copie e cole o script abaixo na Cloud Shell e prima Enter. Executa o guião uma linha de cada vez. Esta primeira secção do script irá criar os recursos base para este tutorial, incluindo a conta de armazenamento, IoT Hub, Service Bus Namespace e fila service bus. Ao passar pelo tutorial, copie cada bloco de script e cole-o na Cloud Shell para executá-lo.

Existem vários nomes de recursos que devem ser globalmente únicos, como o nome IoT Hub e o nome da conta de armazenamento. Para facilitar isto, esses nomes de recursos são anexados a um valor alfanumérico aleatório chamado *RandomValue*. O RandomValue é gerado uma vez no topo do script e anexado aos nomes de recursos necessários ao longo do script. Se não quiser que seja aleatório, pode defini-lo para uma corda vazia ou para um valor específico. 

> [!IMPORTANT]
> As variáveis definidas no script inicial também são usadas pelo script de encaminhamento, por isso executar todo o script na mesma sessão Cloud Shell. Se abrir uma nova sessão para executar o script para configurar o encaminhamento, várias das variáveis estarão em falta de valores. 
>

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>Criar um dispositivo simulado

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

Agora que os recursos base estão configurado, pode configurar o encaminhamento de mensagens.

## <a name="set-up-message-routing"></a>Configurar o encaminhamento de mensagens

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Para criar um ponto final de encaminhamento, utilize [o Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint). Para criar a rota de mensagens para o ponto final, utilize [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute).

### <a name="route-to-a-storage-account"></a>Rota para uma conta de armazenamento 

Primeiro, crie o ponto final para a conta de armazenamento e, em seguida, crie a rota da mensagem.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Estas são as variáveis utilizadas pelo script que devem ser definidas dentro da sua sessão Cloud Shell:

**resourceGroup**: Existem duas ocorrências deste campo - definir ambas para o seu grupo de recursos.

**nome**: Este campo é o nome do Hub IoT ao qual o encaminhamento se aplicará.

**endpointName**: Este campo é o nome que identifica o ponto final. 

**ponto finalType**: Este campo é o tipo de ponto final. Este valor deve ser definido para `azurestoragecontainer` `eventhub` , , ou `servicebusqueue` `servicebustopic` . Para os seus propósitos aqui, desemaça-o para `azurestoragecontainer` .

**subscriçãoID**: Este campo está definido para a subscriçãoID para a sua conta Azure.

**armazenamentoConnectionString**: Este valor é recuperado a partir da conta de armazenamento criada no script anterior. É utilizado pelo encaminhamento para aceder à conta de armazenamento.

**containerName**: Este campo é o nome do recipiente na conta de armazenamento para o qual os dados serão escritos.

**Codificação**: Coloque este campo em qualquer um `AVRO` ou `JSON` . Isto designa o formato dos dados armazenados. O padrão é AVRO.

**nome de rota**: Este campo é o nome da rota que está a configurar. 

**condição**: Este campo é a consulta utilizada para filtrar as mensagens enviadas para este ponto final. A condição de consulta para as mensagens que estão a ser encaminhada para o armazenamento é `level="storage"` .

**ativado**: Este campo desresuse, `true` indicando que a rota da mensagem deve ser ativada após a criação.

Copie este script e cole-o na janela cloud Shell.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

O próximo passo é criar o ponto final de encaminhamento para a conta de armazenamento. Especifica também o recipiente no qual os resultados serão armazenados. O contentor foi criado quando a conta de armazenamento foi criada.

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

Em seguida, crie a rota de mensagem para o ponto final de armazenamento. A rota da mensagem designa para onde enviar as mensagens que satisfaçam a especificação de consulta.

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>Rota para uma fila de ônibus de serviço

Agora configure o encaminhamento para a fila do Service Bus. Para recuperar o fio de ligação para a fila do Service Bus, tem de criar uma regra de autorização que tenha os direitos corretos definidos. O seguinte script cria uma regra de autorização para a fila do Service Bus chamada `sbauthrule` , e define os direitos de `Listen Manage Send` . Uma vez configurada esta regra de autorização, pode usá-la para recuperar a cadeia de ligação para a fila.

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

Agora use a regra de autorização para recuperar a chave da fila do Service Bus. Esta regra de autorização será usada para recuperar a cadeia de ligação mais tarde no script.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Agora, estabeleça o ponto final de encaminhamento e a rota de mensagem para a fila do Service Bus. Estas são as variáveis utilizadas pelo script que devem ser definidas dentro da sua sessão Cloud Shell:

**endpointName**: Este campo é o nome que identifica o ponto final. 

**ponto finalType**: Este campo é o tipo de ponto final. Este valor deve ser definido para `azurestoragecontainer` `eventhub` , , ou `servicebusqueue` `servicebustopic` . Para os seus propósitos aqui, desemaça-o para `servicebusqueue` .

**nome de rota**: Este campo é o nome da rota que está a configurar. 

**condição**: Este campo é a consulta utilizada para filtrar as mensagens enviadas para este ponto final. A condição de consulta para as mensagens que estão a ser encaminhada para a fila do Service Bus é `level="critical"` .

Aqui está o Azure PowerShell para o encaminhamento de mensagens para a fila do ônibus de serviço.

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# Add the routing endpoint, using the connection string property from the key.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>Ver encaminhamento de mensagens no portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Agora que tem os recursos configurados e as rotas de mensagem configuradas, avance para o próximo tutorial para aprender a enviar mensagens para o hub IoT e vê-las encaminhadas para os diferentes destinos. 

> [!div class="nextstepaction"]
> [Parte 2 - Ver os resultados do encaminhamento de mensagens](tutorial-routing-view-message-routing-results.md)
