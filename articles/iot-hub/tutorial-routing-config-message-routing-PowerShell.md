---
title: Configurar o encaminhamento de mensagens para o IoT Hub do Azure com o Azure PowerShell | Documentos da Microsoft
description: Configurar o encaminhamento de mensagens para o IoT Hub do Azure com o Azure PowerShell
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 51e9bc85c2ee843aa096674a25a1f634bd08b838
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660946"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Tutorial: Utilizar o Azure PowerShell para configurar o encaminhamento de mensagens do IoT Hub

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Transferir o script (opcional)

Para a segunda parte deste tutorial, transfira e execute um aplicativo do Visual Studio para enviar mensagens para o IoT Hub. Existe uma pasta no download que contém o modelo Azure Resource Manager e o ficheiro de parâmetros, bem como os scripts da CLI do Azure e PowerShell. 

Se quiser ver o script concluído, transfira o [IoT do Azure C# exemplos](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Deszipe o ficheiro de Master. O script da CLI do Azure é /iot-hub/Tutorials/Routing/SimulatedDevice/resources/como **iothub_routing_psh.ps1**.

## <a name="create-your-resources"></a>Criar os seus recursos

Comece por criar os recursos com o PowerShell.

### <a name="use-powershell-to-create-your-base-resources"></a>Utilize o PowerShell para criar os seus recursos de bases

Existem vários nomes de recurso que devem ser globalmente exclusivos, como o nome do IoT Hub e o nome da conta de armazenamento. Para facilitar este processo, esses nomes de recursos são acrescentados com um valor de alfanumérico aleatório chamado *randomValue*. RandomValue é gerado, uma vez, na parte superior do script e acrescentado para os nomes de recursos, conforme necessário em todo o script. Se não quiser que ela seja aleatório, pode configurá-lo como uma cadeia vazia ou para um valor específico. 

> [!IMPORTANT]
> As variáveis definidas no script inicial também são utilizadas pelo script de encaminhamento, assim, executar todos os scripts na mesma sessão do Cloud Shell. Se abrir uma nova sessão para executar o script para configurar o encaminhamento, várias das variáveis falta valores. 
>

Copie e cole o script abaixo no Cloud Shell e prima Enter. A linha de um script é executado cada vez. Nesta primeira secção do script irá criar os recursos de bases para este tutorial, incluindo a conta de armazenamento, o IoT Hub, o espaço de nomes do Service Bus e a fila do Service Bus. À medida que avança o tutorial, cada bloco de script de copiar e colá-lo no Cloud Shell para executá-lo.

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing rules.
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

Agora que os recursos de bases são configurados, pode configurar o encaminhamento de mensagens.

## <a name="set-up-message-routing"></a>Configurar o encaminhamento de mensagens

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Para criar um ponto de final de encaminhamento, utilize [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint). Para criar a rota de sistema de mensagens para o ponto final, utilize [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute).

### <a name="route-to-a-storage-account"></a>Encaminhar para uma conta de armazenamento 

Em primeiro lugar, configurar o ponto final para a conta de armazenamento, em seguida, crie a rota de mensagem.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Estas variáveis são definidas:

**resourceGroup**: Existem dois ocorrências deste campo--definir ambos como seu grupo de recursos.

**name**: Este campo é o nome do IoT Hub para que o encaminhamento será aplicada.

**endpointName**: Este campo é o nome identificando o ponto final. 

**endpointType**: Este campo é o tipo de ponto de extremidade. Este valor tem de ser definido como `azurestoragecontainer`, `eventhub`, `servicebusqueue`, ou `servicebustopic`. Para os fins pretendidos aqui, defina-o como `azurestoragecontainer`.

**subscriptionID**: Este campo está definido para o subscriptionID para a sua conta do Azure.

**storageConnectionString**: Este valor é obtido a partir da conta de armazenamento definida no script anterior. É utilizado pelo encaminhamento para aceder à conta de armazenamento.

**containerName**: Este campo é o nome do contentor na conta de armazenamento para o qual os dados serão escritos.

**Codificação**: Defina este campo para qualquer um `AVRO` ou `JSON`. Isto indica o formato dos dados armazenados. A predefinição é AVRO.

**routeName**: Este campo é o nome da rota que estiver a configurar. 

**Condição**: Este campo é a consulta usada para filtrar as mensagens enviadas para este ponto final. A condição de consulta para as mensagens que está a ser encaminhado para o armazenamento é `level="storage"`.

**enabled**: Este campo é predefinido para `true`, que indica que a rota de mensagem deve ser ativada depois de criado.

Este script de copiar e cole-a na janela do Cloud Shell.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

A próxima etapa é criar o ponto final de encaminhamento para a conta de armazenamento. Também especificar o contentor no qual serão armazenados os resultados. O contentor foi criado quando foi criada a conta de armazenamento.

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

Em seguida, crie a rota de mensagem para o ponto final de armazenamento. A rota de mensagem designa para onde enviar as mensagens que satisfazem a especificação de consulta.

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

### <a name="route-to-a-service-bus-queue"></a>Encaminhar para uma fila do Service Bus

Agora configure o encaminhamento para a fila do Service Bus. Para obter a cadeia de ligação para a fila do Service Bus, tem de criar uma regra de autorização que tem os direitos corretos definidos. O seguinte script cria uma regra de autorização para a fila do Service Bus chamada `sbauthrule`e define os direitos para `Listen Manage Send`. Quando esta regra de autorização estiver configurada, pode usá-lo a obter a cadeia de ligação para a fila.

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

Utilize a regra de autorização para obter a chave de fila do Service Bus. Esta regra de autorização será utilizada para obter a cadeia de ligação mais tarde no script.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Agora configure o ponto final de encaminhamento e a rota de mensagem de fila do Service Bus. Estas variáveis são definidas:

**endpointName**: Este campo é o nome identificando o ponto final. 

**endpointType**: Este campo é o tipo de ponto de extremidade. Este valor tem de ser definido como `azurestoragecontainer`, `eventhub`, `servicebusqueue`, ou `servicebustopic`. Para os fins pretendidos aqui, defina-o como `servicebusqueue`.

**routeName**: Este campo é o nome da rota que estiver a configurar. 

**Condição**: Este campo é a consulta usada para filtrar as mensagens enviadas para este ponto final. A condição de consulta para as mensagens que está a ser encaminhado para a fila do Service Bus é `level="critical"`.

Aqui está o Azure PowerShell para o encaminhamento de mensagens para a fila do Service Bus.

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

### <a name="view-message-routing-in-the-portal"></a>Ver o roteamento de mensagens no portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Passos Seguintes

Agora que tem os recursos de configuração e as rotas de mensagem configuradas, avance para o próximo tutorial para saber como enviar mensagens para o hub IoT e vê-los a ser encaminhados para os diferentes destinos. 

> [!div class="nextstepaction"]
> [Parte 2: ver os resultados de roteamento de mensagem](tutorial-routing-view-message-routing-results.md)