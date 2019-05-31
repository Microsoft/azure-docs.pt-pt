---
title: Tutorial - utilizar possível de mensagem do IoT Hub do Azure
description: Tutorial que mostra como utilizar o possível de mensagem para mensagens do IoT Hub do Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: e4906bf9f2aead69c315ddb7b2e3b10489378d87
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259078"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments-preview"></a>Tutorial: Usando o possível de mensagem do IoT Hub do Azure (pré-visualização)

*Mensagem possível* é a capacidade do IoT Hub para *carimbo de data /* mensagens com informações adicionais antes das mensagens são enviadas para o ponto de final designado. Uma das razões para utilizar possível de mensagem é incluir os dados que podem ser utilizados para simplificar o processamento a jusante. Por exemplo, Aprimorando as mensagens de telemetria do dispositivo com uma etiqueta do dispositivo duplo pode reduzir a carga de clientes para fazer chamadas à API para obter informações do dispositivo duplo. Para obter mais informações, consulte a [descrição geral da possível de mensagem](iot-hub-message-enrichments-overview.md).

Neste tutorial, vai utilizar a CLI do Azure para configurar os recursos, incluindo dois pontos de extremidade que apontam para dois contentores de armazenamento diferente-- **enriquecida** e **original**. Em seguida, utilizar o [portal do Azure](https://portal.azure.com) para configurar o possível da mensagem a ser aplicada apenas às mensagens enviadas para o ponto final com o **enriquecida** contentor de armazenamento. Enviar mensagens para o IoT Hub, que são encaminhados para ambos os contentores de armazenamento. Apenas as mensagens enviadas para o ponto final para o **enriquecida** irá ser enriquecido o contentor de armazenamento.

Seguem-se as tarefas que irá efetuar para concluir este tutorial:

**Usando o possível de mensagem do IoT Hub**
> [!div class="checklist"]
> * Com a CLI do Azure, crie os recursos de um hub IoT, uma conta de armazenamento com dois pontos de extremidade e a configuração de roteamento.
> * Utilize o portal do Azure para configurar possível de mensagem.
> * Execute uma aplicação que simula um dispositivo de IoT, envio de mensagens para o hub.
> * Ver os resultados e certifique-se de que o possível de mensagem está a funcionar conforme esperado.

## <a name="prerequisites"></a>Pré-requisitos

* Precisa de uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Instale o [Visual Studio](https://www.visualstudio.com/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>Obter o código de exemplo

Baixe [simulação do dispositivo IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) e Descompacte-o. Este repositório tem vários aplicativos, incluindo o que irá utilizar para enviar mensagens para o hub IoT.

Esse download também contém o script para criar os recursos utilizados para testar possível de mensagem. O script estiver no /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Por enquanto, pode observar o script e utilizá-lo. Também pode copiar o script diretamente a partir do artigo.

Quando estiver pronto para começar a testar, irá utilizar o aplicativo de simulação de dispositivo deste download para enviar mensagem ao seu hub IoT.

## <a name="set-up-and-configure-resources"></a>Definir e configurar recursos

Além de criar os recursos necessários, o script da CLI do Azure também configura as duas rotas para os pontos finais que são contentores de armazenamento separada. Para obter mais informações sobre como configurar o encaminhamento, consulte a [encaminhamento tutorial](tutorial-routing.md). Depois dos recursos são configurados, utilize o [portal do Azure](https://portal.azure.com) configurar possível de mensagem para cada ponto de extremidade e, em seguida, vá para a etapa de teste.

> [!NOTE]
> Todas as mensagens são encaminhadas para os dois pontos de extremidade, mas irão ser enriquecidas apenas as mensagens a ir para o ponto final com possível de mensagem configurado.
>

Pode utilizar o script abaixo, ou abra o script na pasta /resources do repositório transferido. Eis os passos que irá executar o script:

* Criar um Hub IoT.
* Criar uma conta de armazenamento.
* Crie dois contentores na conta de armazenamento – um para as mensagens plena e outro para as mensagens que não estão a ser enriquecidas.
* Configure o encaminhamento para as duas contas de armazenamento diferente.
    * Crie um ponto de extremidade para cada contentor da conta de armazenamento.
    * Crie uma rota a cada um dos pontos finais de contentor do conta de armazenamento.

Existem vários nomes de recurso que devem ser globalmente exclusivos, como o nome do IoT Hub e o nome da conta de armazenamento. Para executar o script mais facilidade, esses nomes de recursos são acrescentados com um valor de alfanumérico aleatório chamado *randomValue*. RandomValue é gerado, uma vez, na parte superior do script e acrescentado para os nomes de recursos, conforme necessário em todo o script. Se não quiser que ela seja aleatório, pode configurá-lo como uma cadeia vazia ou para um valor específico.

Se ainda não o tiver feito, abra um [janela do Cloud Shell de Bash.](https://shell.azure.com). Abra o script no repositório descompactado, use Ctrl-A para selecionar todos do mesmo, em seguida, Ctrl-C para copiá-lo. Em alternativa, pode copiar o seguinte script CLI ou abri-lo diretamente no cloud shell. Cole o script na janela do shell de cloud do Azure ao clicar com o botão direito na linha de comando e selecionar **colar**. O script é executado uma instrução por vez. Depois do script deixa de ser executada, selecione **Enter** para se certificar de que ele é executado o último comando. O bloco de código seguinte mostra o script que é utilizado, com comentários que explica o que está fazendo.

Aqui estão os recursos criados pelo script. **Enriquecida** significa que o recurso é para mensagens com o possível. **Original** significa que o recurso é para as mensagens que não estão a ser enriquecidas.

| Name | Value |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| Nome do contentor | original  |
| Nome do contentor | enriquecida  |
| Nome do dispositivo IoT | Contoso-Test-dispositivo |
| Nome do IoT Hub | ContosoTestHubMsgEn |
| Nome da conta de armazenamento | contosostorage |
| ponto final de nome 1 | ContosoStorageEndpointOriginal |
| ponto final de nome de 2 | ContosoStorageEndpointEnriched|
| 1 nome de rota | ContosoStorageRouteOriginal |
| 2 de nome de rota | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing rules.
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
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
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
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One points to container1 in the storage account
#   and includes all messages.
# The other points to container2 in the same storage account
#   and only includes enriched messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for container 1, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for container 2, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are not enriched.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

Neste momento, os recursos são tudo pronto, cópia de segurança e o roteamento está configurado. Pode ver a configuração de roteamento de mensagem no portal e configurar o possível de mensagem para mensagens a ir para o **enriquecida** contentor de armazenamento.

### <a name="view-routing-and-configure-the-message-enrichments"></a>Ver o encaminhamento e configurar o possível de mensagem

1. Aceda ao seu IoT Hub, selecionando **grupos de recursos**, em seguida, selecione o grupo de recursos definida para este tutorial (**ContosoResources_MsgEn**). Encontrar o IoT Hub na lista e selecione-o. Selecione *roteamento de mensagens** para o Hub Iot.

   ![Selecione o encaminhamento de mensagens](./media/tutorial-message-enrichments/select-iot-hub.png)

   A mensagem de painel de encaminhamento tem três separadores – **rotas**, **pontos finais personalizados**, e **enriquecer mensagens**. Pode procurar os dois primeiros separadores para ver a configuração configurar pelo script. Utilize a terceira guia para adicionar possível de mensagem. Vamos enriquecer mensagens a ir para o ponto final para o contentor de armazenamento denominado **enriquecida**. Preencha o nome e o valor e, em seguida, selecione o ponto final **ContosoStorageEndpointEnriched** na lista pendente. Eis um exemplo de como configurar um melhoramento que adiciona o nome do IoT Hub para a mensagem:

   ![Adicionar a melhoria do primeiro](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Adicione estes valores para a lista para o ponto final de ContosoStorageEndpointEnriched.

   | Name | Value | Ponto final (lista pendente) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | Localização do dispositivo | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |customerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Se o dispositivo não tiver um duplo, o valor que coloca aqui irá ser marcado como uma cadeia de caracteres para o valor na possível a mensagem. Para ver o dispositivo duplo informações, aceda ao seu hub no portal, em seguida, selecione **dispositivos IoT**, selecione o seu dispositivo e, em seguida, selecione **dispositivo duplo** na parte superior da página.
   >
   > Pode editar as informações de duplo para adicionar etiquetas (por exemplo, a localização) e defini-lo como um valor específico, se quiser. Para obter mais informações, veja [Understand and use device twins in IoT Hub](iot-hub-devguide-device-twins.md) (Compreender e utilizar dispositivos duplos no Hub IoT)

3. Quando tiver terminado, o painel deve ter um aspeto semelhante a esta imagem:

   ![Tabela com todos os possível adicionado](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Selecione **aplicar** para guardar as alterações.

## <a name="send-messages-to-the-iot-hub"></a>Enviar mensagens para o IoT Hub

Agora que o possível de mensagem está configurados para o ponto final, execute a aplicação de dispositivo simulada para enviar mensagens para o IoT Hub. O hub foi configurado com as regras que realizar o seguinte:

* As mensagens encaminhadas para o ponto final de armazenamento ContosoStorageEndpointOriginal não irá ser enriquecidas e serão armazenadas no contentor de armazenamento `original`.

* As mensagens encaminhadas para o ponto final de armazenamento ContosoStorageEndpointEnriched serão enriquecidas e armazenadas no contentor de armazenamento `enriched`.

A aplicação de dispositivo simulado é um dos aplicativos no download do descompactado. A aplicação envia mensagens para cada um dos métodos de encaminhamento de mensagem diferente no [encaminhamento Tutorial](tutorial-routing.md); isso inclui o armazenamento do Azure.

Clique duas vezes no arquivo de solução (IoT_SimulatedDevice.sln) para abrir o código no Visual Studio, em seguida, abra o ficheiro Program.cs. Substitute `{your hub name}` com o nome do hub IoT. O formato do nome de anfitrião do hub IoT é **{nome do seu hub}. Azure-Devices.NET**. Para este tutorial, é o nome de anfitrião do hub **ContosoTestHubMsgEn.azure Azure-Devices.NET**. Em seguida, substitua `{device key}` com a chave de dispositivo que guardou anteriormente ao executar o script para criar os recursos.

Se não tiver a chave de dispositivo, pode recuperá-la a partir do portal. Após iniciar sessão, vá para **grupos de recursos**, selecione o grupo de recursos, em seguida, selecione o seu IoT Hub. Procure em **dispositivos IoT** para o seu dispositivo de teste e selecione o seu dispositivo. Selecione o ícone de cópia junto a **chave primária** copiá-lo para a área de transferência.

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Executar e testar

Execute a aplicação de consola. Aguarde alguns minutos. As mensagens que estão a ser enviadas são apresentadas no ecrã da consola da aplicação.

A aplicação envia uma nova mensagem de dispositivo para cloud a cada segundo. A mensagem contém um objeto JSON serializado com o ID do dispositivo, a temperatura, a humidade e o nível da mensagem, que está predefinido como `normal`. Atribui um nível de aleatoriamente `critical` ou `storage`, fazendo com que a mensagem a ser encaminhado para a conta de armazenamento ou para o ponto final predefinido. As mensagens enviadas para o **enriquecida** irá ser enriquecido contentor na conta de armazenamento.

Depois de terem sido enviadas a várias mensagens de armazenamento, ver os dados.

1. Selecione **grupos de recursos**, em seguida, localize seu grupo de recursos (ContosoResourcesMsgEn) e selecioná-lo.

2. Selecione a sua conta de armazenamento (contosostorage). Em seguida, selecione **Explorador de armazenamento (pré-visualização)** partir do painel de seleção à esquerda.

   ![Selecione o Explorador de armazenamento](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Selecione **contentores de BLOBS** para ver os dois contentores que podem ser utilizados.

   ![Ver os contentores na conta de armazenamento](./media/tutorial-message-enrichments/show-blob-containers.png)

As mensagens no contentor chamado **enriquecida** têm a possível de mensagem incluído nas mensagens. As mensagens no contentor chamado **original** terão as mensagens não processadas com nenhuma possível. Desagregar para um dos contentores de até chegar ao fundo e abra o arquivo de mensagem mais recente, em seguida, fazer o mesmo para o outro contentor verificar que não não possível adicionado às mensagens nesse contentor.

Quando examinar as mensagens que tenham sido enriquecidas, deverá ver o "meu Hub IoT" com o nome do hub, bem como a localização e o ID de cliente, como este:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","device location":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

E aqui está uma mensagem de unenriched. "" Meu IoT Hub","Localização do dispositivo"e"customerID"não são mostradas aqui, porque este ponto final tem não possível.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender remover todos os recursos que criou neste tutorial, elimine o grupo de recursos. Esta ação também elimina todos os recursos contidos no grupo. Neste caso, remove o hub IoT, a conta de armazenamento e o grupo de recursos.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Utilizar a CLI do Azure para limpar os recursos

Para remover o grupo de recursos, utilize o comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` foi definido como **ContosoResources** início deste tutorial.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, configurados e testados adicionar possível de mensagem para mensagens do IoT Hub através dos seguintes passos:

**Usando o possível de mensagem do IoT Hub**
> [!div class="checklist"]
> * Com a CLI do Azure, crie os recursos de um hub IoT, uma conta de armazenamento com dois enendpoints e a configuração de roteamento.
> * Utilize o portal do Azure para configurar possível de mensagem.
> * Execute uma aplicação que simula uma dispositivo IoT enviar a mensagem para o hub.
> * Ver os resultados e certifique-se de que o possível de mensagem está a funcionar conforme esperado.

Para obter mais informações sobre a possível de mensagem, consulte a [descrição geral da possível de mensagem](iot-hub-message-enrichments-overview.md).

Para obter mais informações sobre o roteamento de mensagens, veja estes artigos:

* [Utilizar o encaminhamento de mensagens do IoT Hub para enviar mensagens do dispositivo para a cloud para diferentes pontos de extremidade](iot-hub-devguide-messages-d2c.md)

* [Tutorial: IoT Hub encaminhamento](tutorial-routing.md)