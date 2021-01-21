---
title: Tutorial - Encaminhamento de mensagens configurantes para Azure IoT Hub usando Azure CLI
description: Tutorial - Encaminhamento de mensagens configurantes para Azure IoT Hub usando o Azure CLI e o portal Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: d7a10aac82286616273f7283bc3fba74f2c58f35
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625219"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Tutorial: Utilize o portal Azure CLI e Azure para configurar o encaminhamento de mensagens IoT Hub

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Use o CLI Azure para criar os recursos base

Este tutorial utiliza o CLI Azure para criar os recursos base, em seguida, usa o [portal Azure](https://portal.azure.com) para mostrar como configurar o encaminhamento de mensagens e configurar o dispositivo virtual para testes.

Copie e cole o script abaixo na Cloud Shell e prima Enter. Executa o guião uma linha de cada vez. Isto criará os recursos base para este tutorial, incluindo a conta de armazenamento, IoT Hub, Service Bus Namespace e a fila service bus.

Existem vários nomes de recursos que devem ser globalmente únicos, como o nome IoT Hub e o nome da conta de armazenamento. Para facilitar isto, esses nomes de recursos são anexados a um valor alfanumérico aleatório chamado *RandomValue*. O RandomValue é gerado uma vez no topo do script e anexado aos nomes de recursos necessários ao longo do script. Se não quiser que seja aleatório, pode defini-lo para uma corda vazia ou para um valor específico.

> [!TIP]
> Uma dica sobre depuração: este script usa o símbolo de continuação (o backslash `\` ) para tornar o script mais legível. Se tiver algum problema em executar o script, certifique-se de que a sessão cloud Shell está em execução `bash` e que não há espaços depois de qualquer uma das costas.
>

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing queries.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults

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

```

Agora que os recursos base estão configurado, pode configurar o encaminhamento de mensagens no [portal Azure](https://portal.azure.com).

## <a name="set-up-message-routing"></a>Configurar o encaminhamento de mensagens

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Rota para uma conta de armazenamento

Agora configure o encaminhamento para a conta de armazenamento. Vá para o painel Encaminhamento de Mensagens e, em seguida, adicione uma rota. Ao adicionar a rota, defina um novo ponto final para a rota. Após a configuração deste encaminhamento, as mensagens em que a propriedade **nível** é definida para **armazenamento** são escritas automaticamente para uma conta de armazenamento. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. No [portal Azure](https://portal.azure.com), selecione **Grupos de Recursos,** em seguida, selecione o seu grupo de recursos. Este tutorial utiliza **ContosoResources**.

2. Selecione o hub IoT na lista de recursos. Este tutorial utiliza **ContosoTestHub**.

3. **Selecione encaminhamento de mensagens**. No painel **de encaminhamento de mensagens,** selecione +**Adicionar**. No painel **Adicionar uma Rota,** selecione +**Adicione o ponto final** ao lado do campo Endpoint para mostrar os pontos finais suportados, como mostrado na seguinte imagem:

   ![Comece a adicionar um ponto final para uma rota](./media/tutorial-routing/message-routing-add-a-route-with-storage-endpoint-ver2.png)

4. Selecione **Armazenamento**. Veja o **Painel de Ponto final de armazenamento.**

   ![Adicionar um ponto final](./media/tutorial-routing/message-routing-add-storage-endpoint-ver2.png)

5. Introduza um nome para o ponto final. Este tutorial utiliza **ContosoStorageEndpoint**.

6. **Selecione Escolha um recipiente**. será apresentada a lista das suas contas de armazenamento. Selecione a que configurou nos passos de preparação. Este tutorial utiliza **contosostorage**. Mostra uma lista de contentores nessa conta de armazenamento. **Selecione** o recipiente que configurar nos passos de preparação. Este tutorial utiliza **contosoresults**. Volte ao **painel de ponto final de armazenamento Adicione um** ponto de armazenamento e veja as seleções que fez.

7. Desaperte a codificação para AVRO ou JSON. Para efeitos deste tutorial, utilize as predefinições no resto dos campos. Este campo será acinzentado se a região selecionada não apoiar a codificação JSON.,

   > [!NOTE]
   > Pode definir o formato do nome de blob com o **Formato do nome de ficheiro blob**. A predefinição é `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. O formato tem de conter {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH}, and {mm} por qualquer ordem.
   >
   > Por exemplo, ao utilizar o formato de nome de ficheiro blob predefinido, se o nome do hub for ContosoTestHub e a data/hora for 30 de outubro de 2018 à 10:56, o nome do blob ficará assim: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > Os blobs são escritos no formato Avro.
   >

8. Selecione **Criar** para criar o ponto final de armazenamento e adicioná-lo à rota. Volta ao painel **Adicionar uma rota**.

9. Agora, preencha o resto das informações da consulta de encaminhamento. Esta consulta especifica os critérios para enviar mensagens para o contentor de armazenamento que acabou de adicionar como ponto final. Preencha os campos no ecrã.

   **Nome**: introduza um nome para a consulta de encaminhamento. Este tutorial usa **ContosoStorageRoute.**

   **Ponto final**: mostra o ponto final que acabou de configurar.

   **Origem de dados**: selecione **Mensagens de Telemetria do Dispositivo** na lista pendente.

   **Ativar a rota**: Certifique-se de que este campo está definido para `enabled` .
   
   **Consulta de encaminhamento**: introduza `level="storage"` como a cadeia de consulta.

   ![Criação de uma consulta de encaminhamento para a conta de armazenamento](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Selecione **Guardar**. Quando terminar, volta ao painel Encaminhamento de Mensagens, onde pode ver a nova consulta de encaminhamento do armazenamento. Feche o painel Rotas para voltar para a página Grupo de recursos.

### <a name="route-to-a-service-bus-queue"></a>Rota para uma fila de ônibus de serviço

Agora configure o encaminhamento para a fila do Service Bus. Vá para o painel Encaminhamento de Mensagens e, em seguida, adicione uma rota. Ao adicionar a rota, defina um novo ponto final para a rota. Após a configuração desta rota, as mensagens em que a propriedade **nível** é definida para **crítica** são escritas para a fila do Service Bus, que desencadeia uma App Lógica, que depois envia um e-mail com a informação.

1. Na página do grupo Recursos, selecione o seu hub IoT e, em seguida, selecione **O Encaminhamento de Mensagens**.

2. No painel **de encaminhamento de mensagens,** selecione +**Adicionar**.

3. No painel **Adicionar uma rota,** Selecione +**Adicione** ao lado do campo Endpoint. Selecione **Fila do Service Bus**. Verá o painel **Adicionar Ponto Final do Service Bus**.

   ![Adicionar um ponto final de ônibus de serviço](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Preencha os campos:

   **Nome do Ponto Final**: introduza um nome para o ponto final. Este tutorial utiliza **ContosoSBQueueEndpoint**.
   
   **Service Bus Namespace**: Utilize a lista de dropdown para selecionar o espaço de nome do autocarro de serviço que configura nas etapas de preparação. Este tutorial utiliza **ContosoSBNamespace**.

   **Fila de ônibus de serviço**: Utilize a lista de dropdown para selecionar a fila do Service Bus. Este tutorial utiliza **contososbqueue**.

5. Selecione **Criar** para adicionar o ponto de final da fila do Serviço de Autocarro. Volta ao painel **Adicionar uma rota**.

6. Agora, preencha o resto das informações da consulta de encaminhamento. Esta consulta especifica os critérios para enviar mensagens para a fila do Service Bus que acabou de adicionar como ponto final. Preencha os campos no ecrã. 

   **Nome**: introduza um nome para a consulta de encaminhamento. Este tutorial utiliza **ContosoSBQueueRoute**. 

   **Ponto final**: mostra o ponto final que acabou de configurar.

   **Origem de dados**: selecione **Mensagens de Telemetria do Dispositivo** na lista pendente.

   **Consulta de encaminhamento**: introduza `level="critical"` como a cadeia de consulta. 

   ![Crie uma consulta de encaminhamento para a fila do Service Bus](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Selecione **Guardar**. Quando regressar ao painel Rotas, verá as duas novas rotas, tal como apresentado aqui.

   ![As rotas que acabou de configurar](./media/tutorial-routing/message-routing-show-both-routes.png)

8. Pode ver os pontos finais personalizados que definiu selecionando o separador **Pontos de Finalização personalizados.**

   ![O ponto final personalizado que acabou de configurar](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Feche o painel Encaminhamento de Mensagens para voltar ao painel Grupo de recursos.

## <a name="create-a-simulated-device"></a>Criar um dispositivo simulado

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Agora que tem os recursos configurados e as rotas de mensagem configuradas, avance para o próximo tutorial para aprender a enviar mensagens para o hub IoT e vê-las encaminhadas para os diferentes destinos. 

> [!div class="nextstepaction"]
> [Parte 2 - Ver os resultados do encaminhamento de mensagens](tutorial-routing-view-message-routing-results.md)