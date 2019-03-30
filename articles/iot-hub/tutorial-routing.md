---
title: Configurar o encaminhamento de mensagens para o Hub de IoT do Azure com a CLI do Azure e o portal do Azure | Documentos da Microsoft
description: Configurar o encaminhamento de mensagens para o Hub de IoT do Azure com a CLI do Azure e o portal do Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 2f382c31c6bfb6ab71afd495c4c3f702715633c0
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661893"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Tutorial: Utilizar a CLI do Azure e o portal do Azure para configurar o encaminhamento de mensagens do IoT Hub

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Utilizar a CLI do Azure para criar os recursos de bases

Este tutorial utiliza a CLI do Azure para criar os recursos de bases, em seguida, utiliza a [portal do Azure](https://portal.azure.com) para mostrar como configurar o encaminhamento de mensagens e configurar o dispositivo virtual para fins de teste.

Existem vários nomes de recurso que devem ser globalmente exclusivos, como o nome do IoT Hub e o nome da conta de armazenamento. Para facilitar este processo, esses nomes de recursos são acrescentados com um valor de alfanumérico aleatório chamado *randomValue*. RandomValue é gerado, uma vez, na parte superior do script e acrescentado para os nomes de recursos, conforme necessário em todo o script. Se não quiser que ela seja aleatório, pode configurá-lo como uma cadeia vazia ou para um valor específico.

Copie e cole o script abaixo no Cloud Shell e prima Enter. A linha de um script é executado cada vez. Isto irá criar os recursos de bases para este tutorial, incluindo a conta de armazenamento, o IoT Hub, o espaço de nomes do Service Bus e a fila do Service Bus.

Uma observação sobre a depuração: este script utiliza o símbolo de continuação (a barra invertida `\`) para que o script mais legível. Se tiver um problema ao executar o script, certifique-se de que existem sem espaços após qualquer uma das barras invertidas.

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing rules.
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

Agora que os recursos de bases são configurados, pode configurar o encaminhamento de mensagens no [portal do Azure](https://portal.azure.com).

## <a name="set-up-message-routing"></a>Configurar o encaminhamento de mensagens

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Encaminhar para uma conta de armazenamento

Agora configure o encaminhamento para a conta de armazenamento. Vá para o painel Encaminhamento de Mensagens e, em seguida, adicione uma rota. Ao adicionar a rota, defina um novo ponto final para a rota. Depois de configurar esse roteamento, mensagens de onde o **nível** estiver definida como **armazenamento** são escritos para uma conta de armazenamento automaticamente. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. Na [portal do Azure](https://portal.azure.com), selecione **grupos de recursos**, em seguida, selecione o grupo de recursos. Este tutorial utiliza **ContosoResources**.

2. Selecione o hub IoT na lista de recursos. Este tutorial utiliza **ContosoTestHub**.

3. Selecione **roteamento de mensagens**. Na **roteamento de mensagens** painel, selecione +**Add**. Sobre o **adicionar uma rota** painel, selecione +**Add** junto ao campo de ponto final para mostrar os pontos finais suportados, tal como apresentado na imagem seguinte:

   ![Começar a adicionar um ponto final para uma rota](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. Selecione **Armazenamento de blobs**. Verá o **adicionar um ponto de final de armazenamento** painel.

   ![Adicionar um ponto final](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. Introduza um nome para o ponto final. Este tutorial utiliza **ContosoStorageEndpoint**.

6. Selecione **escolher um contentor**. será apresentada a lista das suas contas de armazenamento. Selecione a que configurou nos passos de preparação. Este tutorial utiliza **contosostorage**. Mostra uma lista de contentores nessa conta de armazenamento. **Selecione** o contentor configurou nos passos de preparação. Este tutorial utiliza **contosoresults**. Voltar para o **adicionar um ponto de final de armazenamento** painel e ver as seleções que fez.

7. Defina a codificação a ser AVRO ou JSON. Para efeitos deste tutorial, utilize as predefinições no resto dos campos. Este campo estarão esbatido se a região selecionada não suporta a codificação de JSON.,

   > [!NOTE]
   > Pode definir o formato do nome de blob com o **Formato do nome de ficheiro blob**. A predefinição é `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. O formato tem de conter {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH}, and {mm} por qualquer ordem.
   >
   > Por exemplo, ao utilizar o formato de nome de ficheiro blob predefinido, se o nome do hub for ContosoTestHub e a data/hora for 30 de outubro de 2018 à 10:56, o nome do blob ficará assim: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > Os blobs são escritos no formato Avro.
   >

8. Selecione **criar** para criar o ponto final de armazenamento e adicioná-lo para a rota. Volta ao painel **Adicionar uma rota**.

9. Agora, preencha o resto das informações da consulta de encaminhamento. Esta consulta especifica os critérios para enviar mensagens para o contentor de armazenamento que acabou de adicionar como ponto final. Preencha os campos no ecrã.

   **Nome**: Introduza um nome para a consulta de encaminhamento. Este tutorial utiliza **ContosoStorageRoute**.

   **Ponto final**: Isso mostra o ponto final que acabou de configurar.

   **Origem de dados**: Selecione **mensagens de telemetria do dispositivo** na lista pendente.

   **Ativar a rota**: Certifique-se de que este campo é definido como `enabled`.
   
   **Consulta de encaminhamento**: Introduza `level="storage"` como a cadeia de consulta.

   ![Criar uma consulta de encaminhamento para a conta de armazenamento](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Selecione **Guardar**. Quando terminar, volta ao painel Encaminhamento de Mensagens, onde pode ver a nova consulta de encaminhamento do armazenamento. Feche o painel Rotas para voltar para a página Grupo de recursos.

### <a name="route-to-a-service-bus-queue"></a>Encaminhar para uma fila do Service Bus

Agora configure o encaminhamento para a fila do Service Bus. Vá para o painel Encaminhamento de Mensagens e, em seguida, adicione uma rota. Ao adicionar a rota, defina um novo ponto final para a rota. Depois de configurar esta rota, mensagens de onde o **nível** estiver definida como **críticos** são escritos para a fila do Service Bus, que aciona uma aplicação lógica, que, em seguida, envia um e-mail com as informações.

1. Na página do grupo de recursos, selecione o seu hub IoT, em seguida, selecione **roteamento de mensagens**.

2. Na **roteamento de mensagens** painel, selecione +**Add**.

3. Sobre o **adicionar uma rota** painel, selecione +**adicionar** junto ao campo de ponto final. Selecione **Fila do Service Bus**. Verá o painel **Adicionar Ponto Final do Service Bus**.

   ![Adicionar um ponto de final do service bus](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Preencha os campos:

   **Nome do ponto final**: Introduza um nome para o ponto final. Este tutorial utiliza **ContosoSBQueueEndpoint**.
   
   **Espaço de nomes do barramento de serviço**: Utilize a lista pendente para selecionar o espaço de nomes de barramento do serviço que configurar os passos de preparação. Este tutorial utiliza **ContosoSBNamespace**.

   **Fila do Service Bus**: Utilize a lista pendente para selecionar a fila do Service Bus. Este tutorial utiliza **contososbqueue**.

5. Selecione **criar** para adicionar o ponto de final de fila do Service Bus. Volta ao painel **Adicionar uma rota**.

6. Agora, preencha o resto das informações da consulta de encaminhamento. Esta consulta especifica os critérios para enviar mensagens para a fila do Service Bus que acabou de adicionar como ponto final. Preencha os campos no ecrã. 

   **Nome**: Introduza um nome para a consulta de encaminhamento. Este tutorial utiliza **ContosoSBQueueRoute**. 

   **Ponto final**: Isso mostra o ponto final que acabou de configurar.

   **Origem de dados**: Selecione **mensagens de telemetria do dispositivo** na lista pendente.

   **Consulta de encaminhamento**: Introduza `level="critical"` como a cadeia de consulta. 

   ![Criar uma consulta de encaminhamento para a fila do Service Bus](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Selecione **Guardar**. Quando regressar ao painel Rotas, verá as duas novas rotas, tal como apresentado aqui.

   ![As rotas que acabou de configurar](./media/tutorial-routing/message-routing-show-both-routes.png)

8. Pode ver os pontos de extremidade personalizados que configurou ao selecionar o **os pontos finais personalizados** separador.

   ![O ponto de extremidade personalizado que acabou de configurar](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Feche o painel Encaminhamento de Mensagens para voltar ao painel Grupo de recursos.

## <a name="create-a-simulated-device"></a>Criar um dispositivo simulado

[!INCLUDE [iot-hub-include-create- imulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>Passos Seguintes

Agora que tem os recursos de configuração e as rotas de mensagem configuradas, avance para o próximo tutorial para saber como enviar mensagens para o hub IoT e vê-los a ser encaminhados para os diferentes destinos. 

> [!div class="nextstepaction"]
> [Parte 2: ver os resultados de roteamento de mensagem](tutorial-routing-view-message-routing-results.md)
