---
title: Tutorial - Use enriquecimentos de mensagens Azure IoT Hub
description: Tutorial mostrando como usar enriquecimentos de mensagens para mensagens Azure IoT Hub
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 7ae85fa2559b8313bd35a761945bff946563e935
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199786"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Tutorial: Use enriquecimentos de mensagens Azure IoT Hub

*Enriquecimentos de mensagens* descreve a capacidade do Azure IoT Hub de *carimbar* mensagens com informações adicionais antes de as mensagens serem enviadas para o ponto final designado. Uma das razões para usar o enriquecimento de mensagens é incluir dados que podem ser usados para simplificar o processamento a jusante. Por exemplo, enriquecer mensagens de telemetria do dispositivo com uma etiqueta dupla do dispositivo pode reduzir a carga sobre os clientes para fazer chamadas API gémeas do dispositivo para esta informação. Para mais informações, por favor leia [as posições gerais dos enriquecimentos de mensagens.](iot-hub-message-enrichments-overview.md)

Neste tutorial, você vê duas maneiras de criar e configurar os recursos necessários para testar os enriquecimentos de mensagens para um hub IoT. Os recursos incluem uma conta de armazenamento com dois contentores de armazenamento. Um recipiente contém as mensagens enriquecidas e outro contém as mensagens originais. Também está incluído um hub IoT para receber as mensagens e encaminhá-las para o recipiente de armazenamento apropriado com base no enriquecimento ou não.

* O primeiro método é utilizar o CLI Azure para criar os recursos e configurar o encaminhamento da mensagem. Em seguida, define os enriquecimentos manualmente utilizando o [portal Azure](https://portal.azure.com).

* O segundo método é utilizar um modelo de Gestor de Recursos Azure para criar tanto os *recursos* como as configurações para o encaminhamento de mensagens e enriquecimentos de mensagens.

Após as configurações para o encaminhamento de mensagens e enriquecimentos de mensagens, utiliza uma aplicação para enviar mensagens para o hub IoT. Em seguida, o centro encaminha-os para ambos os recipientes de armazenamento. Apenas as mensagens enviadas para o ponto final para o recipiente de armazenamento **enriquecido** são enriquecidas.

Aqui estão as tarefas que executa para completar este tutorial:

**Use enriquecimentos de mensagens IoT Hub**
> [!div class="checklist"]
> * Primeiro método: Criar recursos e configurar o encaminhamento de mensagens utilizando o Azure CLI. Configure manualmente os enriquecimentos de mensagens utilizando o [portal Azure](https://portal.azure.com).
> * Segundo método: Criar recursos e configurar o encaminhamento de mensagens e enriquecimentos de mensagens utilizando um modelo de Gestor de Recursos. 
> * Executar uma aplicação que simula um dispositivo IoT enviando mensagens para o centro.
> * Veja os resultados e verifique se os enriquecimentos de mensagens estão funcionando como esperado.

## <a name="prerequisites"></a>Pré-requisitos

- Precisa de uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Instalar [o Estúdio Visual](https://www.visualstudio.com/).

- Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste tutorial utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Recupere o repositório de amostras IoT C#

Descarregue as [amostras IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) do GitHub e desaperte-as. Este repositório tem várias aplicações, scripts e modelos de Gestor de Recursos nele. Os que devem ser usados para este tutorial são os seguintes:

* Para o método manual, há um script CLI que é usado para criar os recursos. Este script está em /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Este script cria os recursos e configura o encaminhamento de mensagens. Depois de executar este script, crie os enriquecimentos de mensagens manualmente utilizando o [portal Azure](https://portal.azure.com).
* Para o método automatizado, há um modelo de Gestor de Recursos Azure. O modelo está em /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.json. Este modelo cria os recursos, configura o encaminhamento da mensagem e, em seguida, configura os enriquecimentos de mensagens.
* A terceira aplicação que utiliza é a aplicação De Simulação de Dispositivos, que utiliza para enviar mensagens para o hub IoT e testar os enriquecimentos de mensagens.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Configurar e configurar manualmente utilizando o CLI Azure

Além de criar os recursos necessários, o script Azure CLI configura também as duas rotas para os pontos finais que são recipientes de armazenamento separados. Para obter mais informações sobre como configurar o encaminhamento de mensagens, consulte o [tutorial de encaminhamento](tutorial-routing.md). Após a criação dos recursos, utilize o [portal Azure](https://portal.azure.com) para configurar enriquecimentos de mensagens para cada ponto final. Então continue para o passo de teste.

> [!NOTE]
> Todas as mensagens são encaminhadas para ambos os pontos finais, mas apenas as mensagens que vão para o ponto final com enriquecimentos de mensagens configuradas serão enriquecidas.
>

Pode utilizar o script que se segue ou pode abrir o script na pasta /recursos do repositório descarregado. O script realiza os passos seguintes:

* Criar um hub IoT.
* Criar uma conta de armazenamento.
* Criar dois recipientes na conta de armazenamento. Um contentor é para as mensagens enriquecidas, e outro é para mensagens que não são enriquecidas.
* Configurar o encaminhamento para as duas contas de armazenamento diferentes:
    * Crie um ponto final para cada recipiente de conta de armazenamento.
    * Crie uma rota para cada um dos pontos finais do contentor de armazenamento.

Existem vários nomes de recursos que devem ser globalmente únicos, como o nome do hub IoT e o nome da conta de armazenamento. Para facilitar a execução do script, esses nomes de recursos são anexados a um valor alfanumérico aleatório chamado *RandomValue*. O valor aleatório é gerado uma vez no topo do script. Está anexado aos nomes de recursos necessários ao longo do script. Se não quiser que o valor seja aleatório, pode defini-lo para uma corda vazia ou para um valor específico.

Se ainda não o fez, abra uma janela Azure [Cloud Shell](https://shell.azure.com) e certifique-se de que está preparada para a Bash. Abra o script no repositório desapertado, selecione Ctrl+A para selecionar tudo e, em seguida, selecione Ctrl+C para copiá-lo. Em alternativa, pode copiar o seguinte script CLI ou abri-lo diretamente na Cloud Shell. Cole o script na janela Cloud Shell clicando à direita na linha de comando e selecionando **Pasta**. O guião faz uma declaração de cada vez. Depois de o script parar de correr, selecione **Enter** para se certificar de que executa o último comando. O bloco de código que se segue mostra o script que é usado, com comentários que explicam o que está a fazer.

Aqui estão os recursos criados pelo guião. *Enriquecido* significa que o recurso é para mensagens com enriquecimentos. *Original* significa que o recurso é para mensagens que não são enriquecidas.

| Name | Valor |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| nome do recipiente | Original  |
| nome do recipiente | enriquecido  |
| Nome do dispositivo IoT | Contoso-Test-Device |
| Nome do Hub IoT | ContosoTestHubMsgEn |
| Nome da conta de armazenamento | contosostorage |
| nome de ponto final 1 | ContosoStorageEndpointOriginal |
| nome de ponto final 2 | ContosoStorageEndpointEnriched|
| rota Nome 1 | ContosoStorageRouteOriginal |
| rota Nome 2 | ContosoStorageRouteEnriched |

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

Neste momento, os recursos estão todos preparados e o encaminhamento de mensagens está configurado. Pode visualizar a configuração de encaminhamento de mensagens no portal e configurar os enriquecimentos de mensagens para mensagens que vão para o recipiente de armazenamento **enriquecido.**

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Configurar manualmente os enriquecimentos de mensagens utilizando o portal Azure

1. Vá ao seu hub IoT selecionando **grupos de recursos.** Em seguida, selecione o grupo de recursos configurado para este tutorial **(ContosoResourcesMsgEn).** Encontre o hub IoT na lista e selecione-o. Selecione **o encaminhamento de mensagens** para o hub IoT.

   ![Selecione o encaminhamento de mensagens](./media/tutorial-message-enrichments/select-iot-hub.png)

   O painel de encaminhamento de mensagens tem três separadores marcados **Rotas,** **pontos finais personalizados** e **mensagens Enriquecer**. Navegue nos dois primeiros separadores para ver a configuração configurada pelo script. Utilize o terceiro separador para adicionar enriquecimentos de mensagens. Vamos enriquecer as mensagens que vão para o ponto final do contentor de armazenamento chamado **enriquecido.** Preencha o nome e o valor e, em seguida, selecione o ponto final **ContosoStorageEndpointEnriched** da lista de drop-down. Aqui está um exemplo de como configurar um enriquecimento que adiciona o nome do hub IoT à mensagem:

   ![Adicione o primeiro enriquecimento](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Adicione estes valores à lista para o ponto final ContosoStorageEndpointEnriched.

   | Chave | Valor | Ponto final (lista de abandono) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | Locação de dispositivos | $twin.tags.localização | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |clienteID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Se o seu dispositivo não tiver um gémeo, o valor que colocou aqui será carimbado como uma corda pelo valor nos enriquecimentos de mensagens. Para ver as informações gémeas do dispositivo, vá ao seu hub no portal e selecione **dispositivos IoT**. Selecione o seu dispositivo e, em seguida, selecione **Device twin** no topo da página.
   >
   > Pode editar as informações gémeas para adicionar tags, como a localização, e defini-la para um valor específico. Para obter mais informações, consulte [Compreender e utilizar gémeos do dispositivo no IoT Hub.](iot-hub-devguide-device-twins.md)

3. Quando terminar, o seu painel deve parecer-se com esta imagem:

   ![Tabela com todos os enriquecimentos adicionados](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. **Selecione Aplicar** para guardar as alterações. Salte para a secção [de enriquecimentos de mensagens de teste.](#test-message-enrichments)

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Criar e configurar usando um modelo de Gestor de Recursos
Pode utilizar um modelo de Gestor de Recursos para criar e configurar os recursos, encaminhamento de mensagens e enriquecimentos de mensagens.

1. Inicie sessão no Portal do Azure. Selecione **+ Criar um recurso** para criar uma caixa de pesquisa. Introduza *a implementação do modelo* e procure-o. No painel de resultados, selecione a **implementação do modelo (implementar utilizando o modelo personalizado)**.

   ![Implantação do modelo no portal Azure](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Selecione **Criar** no **painel de implementação** do modelo.

1. No painel **de implementação personalizado,** selecione **Construa o seu próprio modelo no editor**.

1. No painel **de modelo de edição,** selecione **Ficheiro de carga**. O Explorador do Windows aparece. Localize a **template_messageenrichments.jsno** ficheiro no ficheiro de repo desapertado em **/iot-hub/Tutorials/Encaminhamento/SimulaçãoDevice/recursos**. 

   ![Selecione o modelo da máquina local](./media/tutorial-message-enrichments/template-select.png)

1. Selecione **Abrir** para carregar o ficheiro do modelo a partir da máquina local. Carrega e aparece no painel de edição.

   Este modelo está configurado para usar um nome de conta ioT globalmente único e nome de conta de armazenamento, adicionando um valor aleatório ao final dos nomes padrão, para que possa usar o modelo sem fazer quaisquer alterações ao mesmo.

   Aqui estão os recursos criados carregando o modelo. **Enriquecido** significa que o recurso é para mensagens com enriquecimentos. **Original** significa que o recurso é para mensagens que não são enriquecidas. Estes são os mesmos valores utilizados no script Azure CLI.

   | Name | Valor |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | nome do recipiente | Original  |
   | nome do recipiente | enriquecido  |
   | Nome do dispositivo IoT | Contoso-Test-Device |
   | Nome do Hub IoT | ContosoTestHubMsgEn |
   | Nome da conta de armazenamento | contosostorage |
   | nome de ponto final 1 | ContosoStorageEndpointOriginal |
   | nome de ponto final 2 | ContosoStorageEndpointEnriched|
   | rota Nome 1 | ContosoStorageRouteOriginal |
   | rota Nome 2 | ContosoStorageRouteEnriched |

1. Selecione **Guardar**. O **painel de implantação personalizado** aparece e mostra todos os parâmetros utilizados pelo modelo. O único campo que precisa de definir é **o grupo De recursos.** Ou cria um novo ou seleciona um da lista de espera.

   Aqui está a metade superior do painel de **implantação personalizado.** Pode ver onde preenche o grupo de recursos.

   ![Metade superior do painel de implantação personalizado](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Aqui está a metade inferior do painel de **implantação personalizado.** Pode ver o resto dos parâmetros e os termos e condições. 

   ![Metade inferior do painel de implantação personalizado](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Selecione a caixa de verificação para concordar com os termos e condições. Em seguida, **selecione Comprar** para continuar com a implementação do modelo.

1. Aguarde que o modelo esteja totalmente implantado. Selecione o ícone da campainha na parte superior do ecrã para verificar o progresso. Quando terminar, continue para a secção [de enriquecimentos de mensagens de teste.](#test-message-enrichments)

## <a name="test-message-enrichments"></a>Enriquecimentos de mensagens de teste

Para visualizar os enriquecimentos de mensagens, selecione **Grupos de Recursos**. Em seguida, selecione o grupo de recursos que está a usar para este tutorial. Selecione o hub IoT da lista de recursos e vá para **mensagens.** A configuração de encaminhamento de mensagens e os enriquecimentos configurados aparecem.

Agora que os enriquecimentos de mensagens estão configurados para o ponto final, execute a aplicação Dispositivo Simulado para enviar mensagens para o hub IoT. O hub foi criado com configurações que realizam as seguintes tarefas:

* As mensagens encaminhadas para o ponto final de armazenamento ContosoStorageEndpointOriginal não serão enriquecidas e serão armazenadas no recipiente de armazenamento `original` .

* As mensagens encaminhadas para o ponto final de armazenamento ContosoStorageEndpointEnriched serão enriquecidas e armazenadas no recipiente de `enriched` armazenamento.

A aplicação Dispositivo Simulado é uma das aplicações no download não abre-se. A aplicação envia mensagens para cada um dos diferentes métodos de encaminhamento de mensagens no tutorial de [encaminhamento](tutorial-routing.md), que inclui o Azure Storage.

Clique duas vezes no ficheiro de solução **IoT_SimulatedDevice.sln** para abrir o código no Estúdio Visual e, em seguida, abrir **Program.cs**. Substitua o nome do hub IoT para o marcador `{your hub name}` . O formato do nome do anfitrião do hub IoT é **{your hub name}.azure-devices.net**. Para este tutorial, o nome de anfitrião do hub é ContosoTestHubMsgEn.azure-devices.net. Em seguida, substitua a chave do dispositivo que guardou anteriormente quando executou o script para criar os recursos para o marcador `{your device key}` .

Se não tiver a chave do dispositivo, pode recuperá-la do portal. Depois de iniciar sação, vá aos **grupos de Recursos,** selecione o seu grupo de recursos e, em seguida, selecione o seu hub IoT. Procure em **dispositivos IoT** para o seu dispositivo de teste e selecione o seu dispositivo. Selecione o ícone de cópia ao lado **da chave primária** para copiá-lo para a área de transferência.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Executar e testar

Executar a aplicação da consola por alguns minutos. As mensagens que estão a ser enviadas são apresentadas no ecrã da consola da aplicação.

A aplicação envia uma nova mensagem de dispositivo para cloud a cada segundo. A mensagem contém um objeto JSON serializado com o ID do dispositivo, a temperatura, a humidade e o nível da mensagem, que está predefinido como `normal`. Atribui aleatoriamente um nível de `critical` ou , o que faz com que a `storage` mensagem seja encaminhada para a conta de armazenamento ou para o ponto final padrão. As mensagens enviadas para o recipiente **enriquecido** na conta de armazenamento serão enriquecidas.

Depois de várias mensagens de armazenamento serem enviadas, consulte os dados.

1. Selecione **Grupos de recursos**. Encontre o seu grupo de recursos, **ContosoResourcesMsgEn,** e selecione-o.

2. Selecione a sua conta de armazenamento, que é **contosostorage**. Em seguida, selecione **Storage Explorer (pré-visualização)** no painel esquerdo.

   ![Selecione Explorador de Armazenamento](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Selecione **OS RECIPIENTES BLOB** para ver os dois recipientes que podem ser utilizados.

   ![Veja os recipientes na conta de armazenamento](./media/tutorial-message-enrichments/show-blob-containers.png)

As mensagens no recipiente chamado **enriquecido** têm os enriquecimentos de mensagens incluídos nas mensagens. As mensagens no recipiente chamado **original** têm as mensagens cruas sem enriquecimentos. Deite-se num dos recipientes até chegar ao fundo e abra o ficheiro de mensagem mais recente. Em seguida, faça o mesmo para que o outro recipiente verifique se não há enriquecimentos adicionados a mensagens nesse recipiente.

Quando olhas para as mensagens que foram enriquecidas, deves ver "o meu IoT Hub" com o nome do hub e a localização e a identificação do cliente, assim:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Aqui está uma mensagem não enfeitada. Note que "o meu IoT Hub", "devicelocation" e "customerID" não aparecem aqui porque estes campos são adicionados pelos enriquecimentos. Este ponto final não tem enriquecimentos.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover todos os recursos que criou neste tutorial, elimine o grupo de recursos. Esta ação também elimina todos os recursos contidos no grupo. Neste caso, remove o hub IoT, a conta de armazenamento e o próprio grupo de recursos.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Use o CLI Azure para limpar recursos

Para remover o grupo de recursos, utilize o comando [az group delete](/cli/azure/group#az-group-delete). Lembre-se que `$resourceGroup` foi definido para **ContosoResourcesMsgEn** no início deste tutorial.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configura e testou a adição de enriquecimentos de mensagens às mensagens IoT Hub utilizando os seguintes passos:

**Use enriquecimentos de mensagens IoT Hub**

> [!div class="checklist"]
> * Primeiro método: Criar recursos e configurar o encaminhamento de mensagens utilizando o Azure CLI. Configure manualmente os enriquecimentos de mensagens utilizando o [portal Azure](https://portal.azure.com).
> * Segundo método: Criar recursos e configurar o encaminhamento de mensagens e enriquecimentos de mensagens utilizando um modelo de Gestor de Recursos Azure.
> * Executar uma aplicação que simula um dispositivo IoT enviando mensagens para o centro.
> * Veja os resultados e verifique se os enriquecimentos de mensagens estão funcionando como esperado.

Para mais informações sobre enriquecimento de mensagens, consulte [a visão geral dos enriquecimentos de mensagens.](iot-hub-message-enrichments-overview.md)

Para obter mais informações sobre o encaminhamento de mensagens, consulte estes artigos:

> [!div class="nextstepaction"]
> [Utilize o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-a-nuvem para diferentes pontos finais](iot-hub-devguide-messages-d2c.md)

> [!div class="nextstepaction"]
> [Tutorial: Encaminhamento IoT Hub](tutorial-routing.md)