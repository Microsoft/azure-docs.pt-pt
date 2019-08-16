---
title: Configurar o roteamento de mensagens para o Hub IoT do Azure usando o CLI do Azure e o portal do Azure | Microsoft Docs
description: Configurar o roteamento de mensagens para o Hub IoT do Azure usando o CLI do Azure e o portal do Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 5019951ca9628bc3beb849bdb2b148b575bc8618
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69535118"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Tutorial: Usar o CLI do Azure e portal do Azure para configurar o roteamento de mensagens do Hub IoT

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Usar o CLI do Azure para criar os recursos básicos

Este tutorial usa o CLI do Azure para criar os recursos básicos e, em seguida, usa o [portal do Azure](https://portal.azure.com) para mostrar como configurar o roteamento de mensagens e configurar o dispositivo virtual para teste.

Há vários nomes de recursos que devem ser globalmente exclusivos, como o nome do Hub IoT e o nome da conta de armazenamento. Para tornar isso mais fácil, esses nomes de recursos são anexados com um valor alfanuméricoaleatório chamado randomValue. O randomValue é gerado uma vez na parte superior do script e acrescentado aos nomes de recursos conforme necessário em todo o script. Se você não quiser que ele seja aleatório, você pode defini-lo como uma cadeia de caracteres vazia ou como um valor específico.

Copie e cole o script abaixo em Cloud Shell e pressione Enter. Ele executa o script uma linha por vez. Isso criará os recursos base para este tutorial, incluindo a conta de armazenamento, o Hub IoT, o namespace do barramento de serviço e a fila do barramento de serviço.

Uma observação sobre depuração: esse script usa o símbolo de continuação (a barra `\`invertida) para tornar o script mais legível. Se você tiver um problema ao executar o script, certifique-se de que não haja espaços após qualquer uma das barras invertidas.

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

Agora que os recursos base estão configurados, você pode configurar o roteamento de mensagens no [portal do Azure](https://portal.azure.com).

## <a name="set-up-message-routing"></a>Configurar o encaminhamento de mensagens

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Rotear para uma conta de armazenamento

Agora configure o encaminhamento para a conta de armazenamento. Vá para o painel Encaminhamento de Mensagens e, em seguida, adicione uma rota. Ao adicionar a rota, defina um novo ponto final para a rota. Depois que esse roteamento é configurado, as mensagens em que a propriedade **Level** é definida como **armazenamento** são gravadas automaticamente em uma conta de armazenamento. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. No [portal do Azure](https://portal.azure.com), selecione **grupos de recursos**e, em seguida, selecione o grupo de recursos. Este tutorial utiliza **ContosoResources**.

2. Selecione o Hub IoT na lista de recursos. Este tutorial utiliza **ContosoTestHub**.

3. Selecione **Roteamento de mensagens**. No painel **Roteamento de mensagens** , selecione +**Adicionar**. No painel **Adicionar uma rota** , selecione +**Adicionar** ao lado do campo ponto de extremidade para mostrar os pontos de extremidades com suporte, conforme exibido na figura a seguir:

   ![Iniciar a adição de um ponto de extremidade para uma rota](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. Selecione **Armazenamento de blobs**. Você verá o painel **Adicionar um ponto de extremidade de armazenamento** .

   ![Adicionando um ponto de extremidade](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. Introduza um nome para o ponto final. Este tutorial usa **ContosoStorageEndpoint**.

6. Selecione **escolher um contêiner**. será apresentada a lista das suas contas de armazenamento. Selecione a que configurou nos passos de preparação. Este tutorial utiliza **contosostorage**. Mostra uma lista de contentores nessa conta de armazenamento. **Selecione** o contêiner que você configurou nas etapas de preparação. Este tutorial utiliza **contosoresults**. Você retorna ao painel **Adicionar um ponto de extremidade de armazenamento** e vê as seleções feitas.

7. Defina a codificação como AVRO ou JSON. Para efeitos deste tutorial, utilize as predefinições no resto dos campos. Esse campo ficará esmaecido se a região selecionada não oferecer suporte à codificação JSON.

   > [!NOTE]
   > Pode definir o formato do nome de blob com o **Formato do nome de ficheiro blob**. A predefinição é `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. O formato tem de conter {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH}, and {mm} por qualquer ordem.
   >
   > Por exemplo, ao utilizar o formato de nome de ficheiro blob predefinido, se o nome do hub for ContosoTestHub e a data/hora for 30 de outubro de 2018 à 10:56, o nome do blob ficará assim: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > Os blobs são escritos no formato Avro.
   >

8. Selecione **criar** para criar o ponto de extremidade de armazenamento e adicioná-lo à rota. Volta ao painel **Adicionar uma rota**.

9. Agora, preencha o resto das informações da consulta de encaminhamento. Esta consulta especifica os critérios para enviar mensagens para o contentor de armazenamento que acabou de adicionar como ponto final. Preencha os campos no ecrã.

   **Nome**: Insira um nome para a consulta de roteamento. Este tutorial usa **ContosoStorageRoute**.

   **Ponto de extremidade**: Isso mostra o ponto de extremidade que você acabou de configurar.

   **Fonte de dados**: Selecione **mensagens** de telemetria do dispositivo na lista suspensa.

   **Habilitar rota**: Verifique se esse campo está definido como `enabled`.
   
   **Consulta de roteamento**: Insira `level="storage"` como a cadeia de caracteres de consulta.

   ![Criando uma consulta de roteamento para a conta de armazenamento](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Selecione **Guardar**. Quando terminar, volta ao painel Encaminhamento de Mensagens, onde pode ver a nova consulta de encaminhamento do armazenamento. Feche o painel Rotas para voltar para a página Grupo de recursos.

### <a name="route-to-a-service-bus-queue"></a>Rotear para uma fila do barramento de serviço

Agora configure o encaminhamento para a fila do Service Bus. Vá para o painel Encaminhamento de Mensagens e, em seguida, adicione uma rota. Ao adicionar a rota, defina um novo ponto final para a rota. Depois que essa rota é configurada, as mensagens em que a propriedade **Level** é definida como **Critical** são gravadas na fila do barramento de serviço, que dispara um aplicativo lógico, que envia um email com as informações.

1. Na página grupo de recursos, selecione o Hub IoT e, em seguida, selecione **Roteamento de mensagens**.

2. No painel **Roteamento de mensagens** , selecione +**Adicionar**.

3. No painel **Adicionar uma rota** , selecione +**Adicionar** ao lado do campo ponto de extremidade. Selecione **Fila do Service Bus**. Verá o painel **Adicionar Ponto Final do Service Bus**.

   ![Adicionando um ponto de extremidade do barramento de serviço](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Preencha os campos:

   **Nome do ponto de extremidade**: Introduza um nome para o ponto final. Este tutorial usa **ContosoSBQueueEndpoint**.
   
   **Namespace do barramento de serviço**: Use a lista suspensa para selecionar o namespace do barramento de serviço que você configurou nas etapas de preparação. Este tutorial utiliza **ContosoSBNamespace**.

   **Fila do barramento de serviço**: Use a lista suspensa para selecionar a fila do barramento de serviço. Este tutorial utiliza **contososbqueue**.

5. Selecione **criar** para adicionar o ponto de extremidade de fila do barramento de serviço. Volta ao painel **Adicionar uma rota**.

6. Agora, preencha o resto das informações da consulta de encaminhamento. Esta consulta especifica os critérios para enviar mensagens para a fila do Service Bus que acabou de adicionar como ponto final. Preencha os campos no ecrã. 

   **Nome**: Insira um nome para a consulta de roteamento. Este tutorial usa **ContosoSBQueueRoute**. 

   **Ponto de extremidade**: Isso mostra o ponto de extremidade que você acabou de configurar.

   **Fonte de dados**: Selecione **mensagens** de telemetria do dispositivo na lista suspensa.

   **Consulta de roteamento**: Insira `level="critical"` como a cadeia de caracteres de consulta. 

   ![Criar uma consulta de roteamento para a fila do barramento de serviço](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Selecione **Guardar**. Quando regressar ao painel Rotas, verá as duas novas rotas, tal como apresentado aqui.

   ![As rotas que você acabou de configurar](./media/tutorial-routing/message-routing-show-both-routes.png)

8. Você pode ver os pontos de extremidade personalizados que você configurou selecionando a guia **pontos de extremidade personalizados** .

   ![O ponto de extremidade personalizado que você acabou de configurar](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Feche o painel Encaminhamento de Mensagens para voltar ao painel Grupo de recursos.

## <a name="create-a-simulated-device"></a>Criar um dispositivo simulado

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>Passos Seguintes

Agora que você tem os recursos configurados e as rotas de mensagens definidas, avance para o próximo tutorial para saber como enviar mensagens para o Hub IoT e vê-las roteadas para os diferentes destinos. 

> [!div class="nextstepaction"]
> [Parte 2-exibir os resultados de roteamento de mensagens](tutorial-routing-view-message-routing-results.md)
