---
title: Tutorial – usando aprimoramentos de mensagens do Hub IoT do Azure
description: Tutorial mostrando como usar os aprimoramentos de mensagens para mensagens do Hub IoT do Azure
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.openlocfilehash: 323730fff4659c87058669016b69808a880994cf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453834"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments"></a>Tutorial: usando aprimoramentos de mensagens do Hub IoT do Azure

Os *aprimoramentos de mensagens* são a capacidade do Hub IOT de *carimbar* mensagens com informações adicionais antes que as mensagens sejam enviadas para o ponto de extremidade designado. Um motivo para usar os aprimoramentos de mensagem é incluir dados que possam ser usados para simplificar o processamento de downstream. Por exemplo, enriquecer mensagens de telemetria do dispositivo com uma marca de dispositivo de Altova pode reduzir a carga em clientes para fazer chamadas à API do dispositivo. Para obter mais informações, consulte a [visão geral dos aprimoramentos de mensagem](iot-hub-message-enrichments-overview.md).

Neste tutorial, você verá duas maneiras de criar e configurar os recursos necessários para testar os aprimoramentos de mensagens para um hub IoT. Os recursos incluem uma conta de armazenamento com dois contêineres de armazenamento, um para manter as mensagens aprimoradas e outra para manter as mensagens originais. Também está incluído um hub IoT para receber as mensagens e roteá-las para o contêiner de armazenamento apropriado, dependendo se elas são aprimoradas ou não. 

* O primeiro método é usar o CLI do Azure para criar os recursos e configurar o roteamento de mensagens. Em seguida, você define os aprimoramentos manualmente usando o [portal do Azure](https://portal.azure.com). 

* O segundo método é usar um modelo de Azure Resource Manager para criar os recursos *e* as configurações para o roteamento de mensagens e os aprimoramentos de mensagens. 

Depois que as configurações para o roteamento de mensagens e os aprimoramentos de mensagens forem concluídas, você usará um aplicativo para enviar mensagens ao Hub IoT, que os roteia para ambos os contêineres de armazenamento. Somente as mensagens enviadas ao ponto de extremidade para o contêiner de armazenamento **aprimorado** são enriquecedas.

Estas são as tarefas que você executa para concluir este tutorial:

**Usando aprimoramentos de mensagens do Hub IoT**
> [!div class="checklist"]
> * Primeiro método: aprimoramentos de mensagens manuais
>   - Crie recursos e configure o roteamento de mensagens usando o CLI do Azure.
>   - Configure os aprimoramentos de mensagem manualmente usando o [portal do Azure](https://portal.azure.com).
> * Segundo método: usando um modelo RM
>   - Crie recursos, configure o roteamento de mensagens e os aprimoramentos de mensagens usando um modelo de Azure Resource Manager. 
> * Execute um aplicativo que simule um dispositivo IoT enviando mensagens para o Hub.
> * Exiba os resultados e verifique se os aprimoramentos de mensagem estão funcionando conforme o esperado.

## <a name="prerequisites"></a>Pré-requisitos

* Precisa de uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Instale o [Visual Studio](https://www.visualstudio.com/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Recuperar o repositório C# de amostras de IOT

Baixe os [exemplos C# de IOT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) do GitHub e descompacte-os. Esse repositório tem vários aplicativos, scripts e modelos do Resource Manager nele. Os itens a serem usados para este tutorial são os seguintes:

* Para o método manual, há um script de CLI para criar os recursos. Este script está em **/azure-iot-samples-csharp/iot-hub/tutorials/routing/simulateddevice/resources/iothub_msgenrichment_cli. azcli**. Esse script cria os recursos e configura o roteamento de mensagens. Depois de executar isso, você cria os aprimoramentos de mensagem manualmente usando o [portal do Azure](https://portal.azure.com) e, em seguida, executa o aplicativo DeviceSimulation para ver os aprimoramentos que funcionam.

* Para o método automatizado, há um modelo de Azure Resource Manager. O modelo está em **/azure-iot-samples-csharp/iot-hub/tutorials/routing/simulateddevice/resources/template_msgenrichments. JSON**. Este modelo cria os recursos, configura o roteamento de mensagens e, por fim, configura os aprimoramentos de mensagens. Depois de carregar esse modelo, você executa o aplicativo de simulação de dispositivo para ver os aprimoramentos que funcionam.

* O terceiro aplicativo usado é o aplicativo de simulação de dispositivo, que você usa para enviar mensagens ao Hub IoT e testar os aprimoramentos de mensagens.

## <a name="manual-set-up-and-configuration-using-azure-cli"></a>Instalação manual e configuração usando o CLI do Azure

Além de criar os recursos necessários, o script de CLI do Azure também configura as duas rotas para os pontos de extremidade que são contêineres de armazenamento separados. Para obter mais informações sobre como configurar o roteamento de mensagens, consulte o [tutorial de roteamento](tutorial-routing.md). Depois que os recursos são configurados, você usa o [portal do Azure](https://portal.azure.com) para configurar os aprimoramentos de mensagens para cada ponto de extremidade e, em seguida, continuar na etapa de teste.

> [!NOTE]
> Todas as mensagens são roteadas para os dois pontos de extremidade, mas somente as mensagens enviadas para o ponto de extremidade com os aprimoramentos de mensagens configuradas serão aprimoradas.
>

Você pode usar o script abaixo ou abrir o script na pasta/resources do repositório baixado. Aqui estão as etapas que o script executará:

* Criar um Hub IoT.
* Criar uma conta de armazenamento.
* Crie dois contêineres na conta de armazenamento-um para as mensagens aprimoradas e outra para mensagens que não são enriquecedas.
* Configure o roteamento para as duas contas de armazenamento diferentes.
    * Crie um ponto de extremidade para cada contêiner de conta de armazenamento.
    * Crie uma rota para cada um dos pontos de extremidade do contêiner da conta de armazenamento.

Há vários nomes de recursos que devem ser globalmente exclusivos, como o nome do Hub IoT e o nome da conta de armazenamento. Para facilitar a execução do script, esses nomes de recursos são anexados com um valor alfanumérico aleatório chamado *randomValue*. O randomValue é gerado uma vez na parte superior do script e acrescentado aos nomes de recursos conforme necessário em todo o script. Se você não quiser que ele seja aleatório, você pode defini-lo como uma cadeia de caracteres vazia ou como um valor específico.

Se você ainda não tiver feito isso, abra uma [janela de Cloud Shell](https://shell.azure.com) e verifique se ela está definida como bash. Abra o script no repositório descompactado, use Ctrl-A para selecionar tudo e, em seguida, CTRL-C para copiá-lo. Como alternativa, você pode copiar o script da CLI a seguir ou abri-lo diretamente no Cloud Shell. Cole o script na janela Cloud Shell clicando com o botão direito do mouse na linha de comando e selecionando **colar**. O script executa uma instrução de cada vez. Depois que o script parar de ser executado, selecione **Enter** para certificar-se de que ele executa o último comando. O bloco de código a seguir mostra o script que é usado, com comentários explicando o que está fazendo.

Aqui estão os recursos criados pelo script. **Aprimorado** significa que o recurso é para mensagens com aprimoramentos. **Original** significa que o recurso é para mensagens que não são aprimoradas.

| Nome | Valor |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| nome do contêiner | Original  |
| nome do contêiner | enriquecida  |
| Nome do dispositivo IoT | Contoso-Test-Device |
| Nome do Hub IoT | ContosoTestHubMsgEn |
| Nome da conta de armazenamento | contosostorage |
| Nome do ponto de extremidade 1 | ContosoStorageEndpointOriginal |
| Nome do ponto de extremidade 2 | ContosoStorageEndpointEnriched|
| Nome da rota 1 | ContosoStorageRouteOriginal |
| Nome da rota 2 | ContosoStorageRouteEnriched |

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
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

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

# This is the endpoint for the first container, for endpoint messages that are not enriched.
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

# This is the endpoint for the second container, for endpoint messages that are enriched.
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

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

Neste ponto, os recursos estão todos configurados e o roteamento de mensagens é configurado. Você pode exibir a configuração de roteamento de mensagens no portal e configurar os aprimoramentos de mensagem para as mensagens que vão para o contêiner de armazenamento **aprimorado** .

### <a name="manually-configure-the-message-enrichments-using-the-azure-portal"></a>Configurar manualmente os aprimoramentos de mensagem usando o portal do Azure

1. Vá para o Hub IoT selecionando **grupos de recursos**e selecione a configuração do grupo de recursos para este tutorial (**ContosoResourcesMsgEn**). Localize o Hub IoT na lista e selecione-o. Selecione **Roteamento de mensagens** para o Hub IOT.

   ![Selecionar roteamento de mensagens](./media/tutorial-message-enrichments/select-iot-hub.png)

   O painel roteamento de mensagens tem três guias – **rotas**, **pontos de extremidade personalizados**e **mensagens de enriquecimento**. Você pode procurar as duas primeiras guias para ver a configuração configurada pelo script. Use a terceira guia para adicionar aprimoramentos de mensagens. Vamos enriquecer as mensagens para o ponto de extremidade do contêiner de armazenamento chamado **enriquecedo**. Preencha o nome e o valor e, em seguida, selecione o ponto de extremidade **ContosoStorageEndpointEnriched** na lista suspensa. Aqui está um exemplo de como configurar um enriquecimento que adiciona o nome do Hub IoT à mensagem:

   ![Adicionar primeiro enriquecimento](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Adicione esses valores à lista para o ponto de extremidade ContosoStorageEndpointEnriched.

   | Chave | Valor | Ponto de extremidade (lista suspensa) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |customerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Se o dispositivo não tiver um "alto", o valor que você colocar aqui será marcado como uma cadeia de caracteres para o valor nos aprimoramentos da mensagem. Para ver as informações de 10 do dispositivo, vá para o Hub no portal, selecione **dispositivos IOT**, selecione o dispositivo e, em seguida, selecione **dispositivo** 10 na parte superior da página.
   >
   > Você pode editar as informações de entrelaçamento para adicionar marcas (como local) e defini-las como um valor específico, se desejar. Para obter mais informações, veja [Understand and use device twins in IoT Hub](iot-hub-devguide-device-twins.md) (Compreender e utilizar dispositivos duplos no Hub IoT)

3. Quando tiver terminado, seu painel deverá ser semelhante a esta imagem:

   ![Tabela com todos os aprimoramentos adicionados](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Selecione **aplicar** para salvar as alterações. Pule para a seção [aprimoramentos de mensagem de teste](#testing-message-enrichments) .

## <a name="use-an-rm-template-to-create-and-configure-the-resources-message-routing-and-message-enrichments"></a>Use um modelo do RM para criar e configurar os recursos, o roteamento de mensagens e os aprimoramentos de mensagens 

1. Inicie sessão no portal do Azure. Clique em **+ criar um recurso**. Isso abre a caixa de pesquisa. Procure **implantação de modelo**. No painel de resultados, selecione **implantação de modelo (implantar usando o modelo personalizado)** .

   ![Implantação de modelo no portal do Azure](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Selecione **criar** no painel implantação de modelo. 

1. No painel implantação personalizada. Selecione **criar seu próprio modelo no editor**.

1. No painel Editar modelo, selecione **carregar arquivo**. Você verá o Windows Explorer. Localize o arquivo **template_messageenrichments. JSON** no arquivo de repositório descompactado em **/IOT-Hub/Tutorials/Routing/SimulatedDevice/Resources**. 

   ![Selecionar modelo do computador local](./media/tutorial-message-enrichments/template-select.png)

1. Selecione **abrir** para carregar o arquivo de modelo do computador local. Ele o carrega no painel de edição e o mostra a você.

   Este modelo é configurado para usar um nome de Hub IoT globalmente exclusivo e um nome de conta de armazenamento adicionando um valor aleatório ao final dos nomes padrão, para que você possa usar o modelo sem fazer nenhuma alteração nele. 

   Aqui estão os recursos criados carregando o modelo. **Aprimorado** significa que o recurso é para mensagens com aprimoramentos. **Original** significa que o recurso é para mensagens que não são aprimoradas. Esses são os mesmos valores usados no script de CLI do Azure.

   | Nome | Valor |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | nome do contêiner | Original  |
   | nome do contêiner | enriquecida  |
   | Nome do dispositivo IoT | Contoso-Test-Device |
   | Nome do Hub IoT | ContosoTestHubMsgEn |
   | Nome da conta de armazenamento | contosostorage |
   | Nome do ponto de extremidade 1 | ContosoStorageEndpointOriginal |
   | Nome do ponto de extremidade 2 | ContosoStorageEndpointEnriched|
   | Nome da rota 1 | ContosoStorageRouteOriginal |
   | Nome da rota 2 | ContosoStorageRouteEnriched |

1. Selecione **salvar**e o painel implantação personalizada será exibido, mostrando todos os parâmetros usados pelo modelo. O único campo que você precisa definir é o **grupo de recursos**. Crie um novo ou selecione um na lista suspensa.

   Aqui está a metade superior do painel de implantação personalizado. Você pode ver onde você preenche o grupo de recursos.

   ![Metade superior do painel de implantação personalizado](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Aqui está a metade inferior do painel de implantação personalizado. Você pode ver o restante dos parâmetros e os termos e condições. 

   ![Metade inferior do painel de implantação personalizado](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Marque a caixa de seleção indicando que você concorda com os termos e condições e selecione **comprar** para continuar com a implantação do modelo.

1. Aguarde até que o modelo seja completamente implantado. Você pode selecionar o ícone de sino na parte superior da tela para verificar o andamento. Quando tiver terminado, você poderá continuar a [testar os aprimoramentos de mensagens](#testing-message-enrichments).

## <a name="testing-message-enrichments"></a>Testando aprimoramentos de mensagens

Você pode exibir os aprimoramentos de mensagem selecionando **grupos de recursos**e, em seguida, selecionando o grupo de recursos que você está usando para este tutorial. Em seguida, selecione o Hub IoT na lista de recursos e vá para **mensagens**. Isso mostra a configuração de roteamento de mensagens e os aprimoramentos configurados.

Agora que os aprimoramentos de mensagem estão configurados para o ponto de extremidade, execute o aplicativo de dispositivo simulado para enviar mensagens para o Hub IoT. O Hub foi configurado com configurações que realizam o seguinte:

* As mensagens roteadas para o ponto de extremidade de armazenamento ContosoStorageEndpointOriginal não serão enriquecedas e serão armazenadas no contêiner de armazenamento `original`.

* As mensagens roteadas para o ponto de extremidade de armazenamento ContosoStorageEndpointEnriched serão enriquecedas e armazenadas no contêiner de armazenamento `enriched`.

O aplicativo de dispositivo simulado é um dos aplicativos no download descompactado. O aplicativo envia mensagens para cada um dos diferentes métodos de roteamento de mensagens no [tutorial de roteamento](tutorial-routing.md); Isso inclui o armazenamento do Azure.

Clique duas vezes no arquivo de solução (IoT_SimulatedDevice. sln) para abrir o código no Visual Studio e, em seguida, abra Program.cs. Substitua o nome do Hub IoT pelo `{your hub name}`do marcador. O formato do nome de host do Hub IoT é **{Your Hub Name}. Azure-Devices.net**. Para este tutorial, o nome de host do hub é **ContosoTestHubMsgEn.Azure-Devices.net**. Em seguida, substitua a chave do dispositivo que você salvou anteriormente ao executar o script para criar os recursos para o marcador `{your device key}`.

Se você não tiver a chave do dispositivo, poderá recuperá-la no Portal. Depois de fazer logon, vá para **grupos de recursos**, selecione o grupo de recursos e, em seguida, selecione o Hub IOT. Procure em **dispositivos IOT** para seu dispositivo de teste e selecione seu dispositivo. Selecione o ícone de cópia ao lado de **chave primária** para copiá-lo para a área de transferência.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Executar e testar

Execute o aplicativo de console por alguns minutos. As mensagens que estão sendo enviadas são exibidas na tela do console do aplicativo.

A aplicação envia uma nova mensagem de dispositivo para cloud a cada segundo. A mensagem contém um objeto JSON serializado com o ID do dispositivo, a temperatura, a humidade e o nível da mensagem, que está predefinido como `normal`. Ele atribui aleatoriamente um nível de `critical` ou `storage`, fazendo com que a mensagem seja roteada para a conta de armazenamento ou para o ponto de extremidade padrão. As mensagens enviadas para o contêiner **aprimorado** na conta de armazenamento serão aprimoradas.

Depois que várias mensagens de armazenamento forem enviadas, exiba os dados.

1. Selecione **grupos de recursos**, localize o grupo de recursos (ContosoResourcesMsgEn) e selecione-o.

2. Selecione sua conta de armazenamento (contosostorage). Em seguida, selecione **Gerenciador de armazenamento (versão prévia)** no painel de seleção à esquerda.

   ![Selecionar Gerenciador de armazenamento](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Selecione **contêineres de blob** para ver os dois contêineres que podem ser usados.

   ![Ver os contêineres na conta de armazenamento](./media/tutorial-message-enrichments/show-blob-containers.png)

As mensagens no contêiner chamado **enriqueceted** têm os aprimoramentos de mensagem incluídos nas mensagens. As mensagens no contêiner chamado **original** terão as mensagens não processadas sem aprimoramentos. Faça uma busca detalhada em um dos contêineres até chegar à parte inferior e abra o arquivo de mensagem mais recente e, em seguida, execute o mesmo para o outro contêiner para verificar se não há aprimoramentos adicionados às mensagens nesse contêiner.

Ao examinar as mensagens que foram aprimoradas, você deverá ver o "meu Hub IoT" com o nome do Hub, bem como o local e a ID do cliente, desta forma:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Aqui está uma mensagem não enriqueceda. "meu Hub IoT", "devicelocation" e "customerID" não aparecem aqui, pois esses são os campos que seriam adicionados pelos aprimoramentos, e esse ponto de extremidade não tem aprimoramentos.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser remover todos os recursos que criou neste tutorial, exclua o grupo de recursos. Esta ação também elimina todos os recursos contidos no grupo. Nesse caso, ele remove o Hub IoT, a conta de armazenamento e o próprio grupo de recursos.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Usar o CLI do Azure para limpar os recursos

Para remover o grupo de recursos, utilize o comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` foi definido como **ContosoResourcesMsgEn** de volta no início deste tutorial.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configurou e testou a adição de aprimoramentos de mensagens às mensagens do Hub IoT usando as seguintes etapas:

**Usando aprimoramentos de mensagens do Hub IoT**
> [!div class="checklist"]
> * Primeiro método
>   * Crie recursos e configure o roteamento de mensagens usando o CLI do Azure.
>   * Configure os aprimoramentos de mensagem manualmente usando o [portal do Azure](https://portal.azure.com).
> * Segundo método
>   * Crie recursos, configure o roteamento de mensagens e os aprimoramentos de mensagens usando um modelo de Azure Resource Manager. 
> * Execute um aplicativo que simule um dispositivo IoT enviando mensagens para o Hub.
> * Exiba os resultados e verifique se os aprimoramentos de mensagem estão funcionando conforme o esperado.

Para obter mais informações sobre os aprimoramentos de mensagens, consulte a [visão geral dos aprimoramentos de mensagens](iot-hub-message-enrichments-overview.md).

Para obter mais informações sobre o roteamento de mensagens, consulte estes artigos:

* [Usar o roteamento de mensagens do Hub IoT para enviar mensagens do dispositivo para a nuvem para diferentes pontos de extremidade](iot-hub-devguide-messages-d2c.md)

* [Tutorial: roteamento de Hub IoT](tutorial-routing.md)