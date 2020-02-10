---
title: Tutorial - Use os enriquecimentos de mensagens Azure IoT Hub
description: Tutorial mostrando como usar enriquecimentos de mensagens para mensagens Azure IoT Hub
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.openlocfilehash: d16954760d1f2bf11ec5575f912ee32810696590
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2020
ms.locfileid: "77108213"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Tutorial: Use os enriquecimentos de mensagens Azure IoT Hub

*Os enriquecimentos de mensagens* descrevem a capacidade do Azure IoT Hub *carimbar* mensagens com informações adicionais antes de as mensagens serem enviadas para o ponto final designado. Uma das razões para utilizar o enriquecimento de mensagens é incluir dados que possam ser usados para simplificar o processamento a jusante. Por exemplo, o enriquecimento de mensagens de telemetria do dispositivo com uma etiqueta dupla de dispositivos pode reduzir a carga nos clientes para fazer com que o dispositivo twin API admente apresente chamadas para esta informação. Para mais informações, consulte a [visão geral dos enriquecimentos de mensagens.](iot-hub-message-enrichments-overview.md)

Neste tutorial, você vê duas maneiras de criar e configurar os recursos necessários para testar os enriquecimentos de mensagens para um hub IoT. Os recursos incluem uma conta de armazenamento com dois contentores de armazenamento. Um contentor contém as mensagens enriquecidas e outro contentor contém as mensagens originais. Também está incluído um hub IoT para receber as mensagens e encaminhá-las para o recipiente de armazenamento apropriado com base em se são enriquecidas ou não.

* O primeiro método é utilizar o AZURE CLI para criar os recursos e configurar o encaminhamento de mensagens. Em seguida, define os enriquecimentos manualmente utilizando o [portal Azure](https://portal.azure.com).

* O segundo método é usar um modelo de Gestor de Recursos Azure para criar tanto os *recursos* como as configurações para o encaminhamento de mensagens e enriquecimento de mensagens.

Após as configurações para o encaminhamento de mensagens e enriquecimento de mensagens, utilize uma aplicação para enviar mensagens para o centro IoT. Em seguida, o centro os encaminha para ambos os recipientes de armazenamento. Apenas as mensagens enviadas para o ponto final do recipiente de armazenamento **enriquecido** são enriquecidas.

Aqui estão as tarefas que executa para completar este tutorial:

**Use enriquecimentos de mensagens IoT Hub**
> [!div class="checklist"]
> * Primeiro método: Criar recursos e configurar o encaminhamento de mensagens utilizando o CLI Azure. Configure os enriquecimentos de mensagens manualmente utilizando o [portal Azure](https://portal.azure.com).
> * Segundo método: Criar recursos e configurar o encaminhamento de mensagens e enriquecimento de mensagens utilizando um modelo de Gestor de Recursos. 
> * Executar uma aplicação que simula um dispositivo IoT enviando mensagens para o centro.
> * Veja os resultados e verifique se os enriquecimentos de mensagens estão a funcionar como esperado.

## <a name="prerequisites"></a>Pré-requisitos

* Precisa de uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Instalar o [Visual Studio](https://www.visualstudio.com/).

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste tutorial utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Recuperar o repositório de amostras ioT C#

Descarregue as [amostras ioT C# ](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) do GitHub e desaperte-as. Este repositório tem várias aplicações, scripts e modelos de Gestor de Recursos nele. Os que devem ser usados para este tutorial são os seguintes:

* Para o método manual, há um script CLI que é usado para criar os recursos. Este script está em /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimuladoDevice/resources/iothub_msgenrichment_cli.azcli. Este guião cria os recursos e configura o encaminhamento da mensagem. Depois de executar este script, crie os enriquecimentos de mensagens manualmente utilizando o [portal Azure](https://portal.azure.com).
* Para o método automatizado, há um modelo de Gestor de Recursos Azure. O modelo está em /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimuladoDispositivo/recursos/template_msgenrichments.json. Este modelo cria os recursos, configura o encaminhamento da mensagem e, em seguida, configura os enriquecimentos de mensagens.
* A terceira aplicação que utiliza é a aplicação De Simulação de Dispositivos, que utiliza para enviar mensagens para o centro IoT e testar os enriquecimentos de mensagens.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Configurar e configurar manualmente utilizando o Azure CLI

Além de criar os recursos necessários, o script Azure CLI também configura as duas rotas para os pontos finais que são recipientes de armazenamento separados. Para obter mais informações sobre como configurar o encaminhamento da mensagem, consulte o tutorial de [Encaminhamento](tutorial-routing.md). Depois de configurar os recursos, utilize o [portal Azure](https://portal.azure.com) para configurar os enriquecimentos de mensagens para cada ponto final. Em seguida, continue para o passo de teste.

> [!NOTE]
> Todas as mensagens são encaminhadas para ambos os pontos finais, mas apenas as mensagens que vão para o ponto final com enriquecimentos de mensagens configurados serão enriquecidas.
>

Pode utilizar o script que se segue, ou pode abrir o script na pasta /recursos do repositório descarregado. O guião executa os seguintes passos:

* Criar um centro de ioT.
* Criar uma conta de armazenamento.
* Crie dois recipientes na conta de armazenamento. Um recipiente é para as mensagens enriquecidas, e outro recipiente é para mensagens que não são enriquecidas.
* Configurar o encaminhamento para as duas contas de armazenamento diferentes:
    * Crie um ponto final para cada recipiente de conta de armazenamento.
    * Crie uma rota para cada um dos pontos finais do contentor da conta de armazenamento.

Existem vários nomes de recursos que devem ser globalmente únicos, como o nome do hub IoT e o nome da conta de armazenamento. Para facilitar a execução do script, esses nomes de recursos são anexados com um valor alfanumérico aleatório chamado *RandomValue*. O valor aleatório é gerado uma vez no topo do script. Está anexado aos nomes de recursos necessários ao longo do guião. Se não quiser que o valor seja aleatório, pode defini-lo numa corda vazia ou num valor específico.

Se ainda não o fez, abra uma janela Azure [Cloud Shell](https://shell.azure.com) e certifique-se de que está definida para Bash. Abra o script no repositório desapertado, selecione Ctrl+A para selecionar tudo e, em seguida, selecione Ctrl+C para copiá-lo. Em alternativa, pode copiar o seguinte script CLI ou abri-lo diretamente na Cloud Shell. Colhe o script na janela Cloud Shell clicando na linha de comando e selecionando **paste**. O guião faz uma declaração de cada vez. Depois de o script parar de funcionar, selecione **Enter** para se certificar de que executa o último comando. O seguinte bloco de código mostra o script que é usado, com comentários que explicam o que está a fazer.

Aqui estão os recursos criados pelo guião. *Enriquecido* significa que o recurso é para mensagens com enriquecimento. *Original* significa que o recurso é para mensagens que não são enriquecidas.

| Nome | Valor |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| nome do recipiente | Original  |
| nome do recipiente | enriquecido  |
| Nome do dispositivo IoT | Contoso-Dispositivo de Teste |
| Nome Do Hub IoT | ContosoTestHubMsgEn |
| nome da conta de armazenamento | contosostorage |
| nome do ponto final 1 | ContosoStorageEndpointOriginal |
| nome do ponto final 2 | ContosoStorageEndpointEnriched|
| nome da rota 1 | ContosoStorageRouteOriginal |
| rota Nome 2 | ContosoStorageRouteEnriqueed |

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

Neste momento, os recursos estão todos configurados e o encaminhamento de mensagens está configurado. Pode visualizar a configuração de encaminhamento de mensagens no portal e configurar os enriquecimentos de mensagens para mensagens que vão para o recipiente de armazenamento **enriquecido.**

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Configure manualmente os enriquecimentos de mensagens utilizando o portal Azure

1. Vá ao seu hub IoT selecionando **grupos de Recursos**. Em seguida, selecione o grupo de recursos criado para este tutorial **(ContosoResourcesMsgEn).** Encontre o centro ioT na lista e selecione-o. Selecione **o encaminhamento de mensagem** para o centro IoT.

   ![Selecione o encaminhamento de mensagens](./media/tutorial-message-enrichments/select-iot-hub.png)

   O painel de encaminhamento de mensagens tem três separadores rotulados **Rotas,** **pontos finais personalizados,** e **mensagens Enrich**. Navegue nos dois primeiros separadores para ver a configuração configurada pelo script. Utilize o terceiro separador para adicionar enriquecimentode mensagens. Vamos enriquecer as mensagens que vão ao ponto final do recipiente de armazenamento chamado **enriquecido.** Preencha o nome e o valor e, em seguida, selecione o ponto final **ContosoStorageEndpointEnriqueed** da lista de drop-down. Aqui está um exemplo de como configurar um enriquecimento que adiciona o nome do hub IoT à mensagem:

   ![Adicionar primeiro enriquecimento](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Adicione estes valores à lista para o ponto final Enriquecido contosoStorageEndpoint.

   | Chave | Valor | Ponto final (lista de entrega) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriqueed |
   | Localização de Dispositivos | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriqueed |
   |customerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriqueed |

   > [!NOTE]
   > Se o seu dispositivo não tiver um gémeo, o valor que coloca aqui será carimbado como uma corda pelo valor nos enriquecimentos de mensagens. Para ver a informação gémea do dispositivo, vá ao seu hub no portal e selecione **dispositivos IoT**. Selecione o seu dispositivo e, em seguida, selecione **Device twin** na parte superior da página.
   >
   > Pode editar as informações gémeas para adicionar tags, como localização, e defini-la para um valor específico. Para obter mais informações, veja [Understand and use device twins in IoT Hub](iot-hub-devguide-device-twins.md) (Compreender e utilizar dispositivos duplos no Hub IoT).

3. Quando terminar, o seu painel deve parecer semelhante a esta imagem:

   ![Tabela com todos os enriquecimentos adicionados](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Selecione **Aplicar** para guardar as alterações. Passe para a secção de [enriquecimento de mensagens de teste.](#test-message-enrichments)

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Criar e configurar usando um modelo de Gestor de Recursos
Você pode usar um modelo de Gestor de Recursos para criar e configurar os recursos, encaminhamento de mensagens e enriquecimento de mensagens.

1. Inicie sessão no Portal do Azure. Selecione **+ Criar um Recurso** para criar uma caixa de pesquisa. Introduza a *implementação*do modelo e procure-a. No painel de resultados, selecione a implementação do **modelo (desdobre utilizando o modelo personalizado)** .

   ![Implantação do modelo no portal Azure](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Selecione **Criar** no painel de implantação do **modelo.**

1. No painel de **implementação Personalizado,** selecione **Construa o seu próprio modelo no editor**.

1. No painel do **modelo Editar,** selecione **ficheiro load**. O Windows Explorer aparece. Localize o ficheiro **template_messageenrichments.json** no ficheiro repo desapertado em **/iot-hub/Tutoriais/Routing/SimuladoDispositivo/recursos**. 

   ![Selecione modelo da máquina local](./media/tutorial-message-enrichments/template-select.png)

1. **Selecione Abrir** para carregar o ficheiro do modelo a partir da máquina local. Carrega e aparece no painel de edição.

   Este modelo é configurado para usar um nome de hub ioT globalmente único e nome de conta de armazenamento, adicionando um valor aleatório ao final dos nomes padrão, para que você possa usar o modelo sem fazer quaisquer alterações no mesmo.

   Aqui estão os recursos criados carregando o modelo. **Enriquecido** significa que o recurso é para mensagens com enriquecimento. **Original** significa que o recurso é para mensagens que não são enriquecidas. Estes são os mesmos valores usados no script Azure CLI.

   | Nome | Valor |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | nome do recipiente | Original  |
   | nome do recipiente | enriquecido  |
   | Nome do dispositivo IoT | Contoso-Dispositivo de Teste |
   | Nome Do Hub IoT | ContosoTestHubMsgEn |
   | nome da conta de armazenamento | contosostorage |
   | nome do ponto final 1 | ContosoStorageEndpointOriginal |
   | nome do ponto final 2 | ContosoStorageEndpointEnriched|
   | nome da rota 1 | ContosoStorageRouteOriginal |
   | rota Nome 2 | ContosoStorageRouteEnriqueed |

1. Selecione **Guardar**. O painel de **implantação personalizado** aparece e mostra todos os parâmetros utilizados pelo modelo. O único campo que precisa de definir é o **grupo Derecursos.** Ou cria um novo ou seleciona um da lista de lançamentos.

   Aqui está a metade superior do painel de **implantação personalizado.** Pode ver onde preenche o grupo de recursos.

   ![Metade superior do painel de implantação personalizado](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Aqui está a metade inferior do painel de **implantação personalizado.** Pode ver o resto dos parâmetros e os termos e condições. 

   ![Metade inferior do painel de implantação personalizado](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Selecione a caixa de verificação para concordar com os termos e condições. Em seguida, **selecione Comprar** para continuar com a implementação do modelo.

1. Aguarde que o modelo esteja totalmente implantado. Selecione o ícone do sino na parte superior do ecrã para verificar o progresso. Quando terminar, continue para a secção de [enriquecimento de mensagens](#test-message-enrichments) de teste.

## <a name="test-message-enrichments"></a>Enriquecimento de mensagens de teste

Para ver os enriquecimentos de mensagens, selecione **Grupos de Recursos**. Em seguida, selecione o grupo de recursos que está a usar para este tutorial. Selecione o hub IoT da lista de recursos e vá para **mensagens.** A configuração de encaminhamento de mensagens e os enriquecimentos configurados aparecem.

Agora que os enriquecimentos de mensagens estão configurados para o ponto final, execute a aplicação Simulada dispositivo para enviar mensagens para o hub IoT. O centro foi criado com configurações que realizam as seguintes tarefas:

* As mensagens encaminhadas para o ponto final de armazenamento ContosoStorageEndpointOriginal não serão enriquecidas e serão armazenadas no recipiente de armazenamento `original`.

* As mensagens encaminhadas para o ponto final de armazenamento ContosoStorageEndpointEnriqueed serão enriquecidas e armazenadas no recipiente de armazenamento `enriched`.

A aplicação Dispositivo Simulado é uma das aplicações no download desapertado. A aplicação envia mensagens para cada um dos diferentes métodos de encaminhamento de mensagens no tutorial de [Encaminhamento](tutorial-routing.md), que inclui o Armazenamento Azure.

Clique duas vezes no ficheiro de solução **IoT_SimulatedDevice.sln** para abrir o código no Estúdio Visual e, em seguida, abra **Program.cs**. Substitua o nome do hub IoT pelo marcador `{your hub name}`. O formato do nome de anfitrião do hub IoT é **{o seu nome hub}.azure-devices.net**. Para este tutorial, o nome de anfitrião do centro é ContosoTestHubMsgEn.azure-devices.net. Em seguida, substitua a chave do dispositivo que guardou anteriormente quando executou o script para criar os recursos para o marcador `{your device key}`.

Se não tiver a chave do dispositivo, pode recuperá-la do portal. Depois de iniciar sessão, vá aos **grupos De recursos,** selecione o seu grupo de recursos e, em seguida, selecione o seu hub IoT. Procure em **Dispositivos IoT** para o seu dispositivo de teste e selecione o seu dispositivo. Selecione o ícone da cópia ao lado da **tecla Primary** para copiá-lo para a área de recortes.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Executar e testar

Executar a aplicação da consola por alguns minutos. As mensagens que estão a ser enviadas são exibidas no ecrã da consola da aplicação.

A aplicação envia uma nova mensagem de dispositivo para cloud a cada segundo. A mensagem contém um objeto JSON serializado com o ID do dispositivo, a temperatura, a humidade e o nível da mensagem, que está predefinido como `normal`. Atribui aleatoriamente um nível de `critical` ou `storage`, o que faz com que a mensagem seja encaminhada para a conta de armazenamento ou para o ponto final predefinido. As mensagens enviadas para o recipiente **enriquecido** na conta de armazenamento serão enriquecidas.

Depois de enviar várias mensagens de armazenamento, consulte os dados.

1. Selecione **Grupos de recursos**. Encontre o seu grupo de recursos, **ContosoResourcesMsgEn,** e selecione-o.

2. Selecione a sua conta de armazenamento, que é **um armazenamento .** Em seguida, selecione **Storage Explorer (pré-visualização)** no painel esquerdo.

   ![Selecione Explorador de Armazenamento](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Selecione **RECIPIENTES BLOB** para ver os dois recipientes que podem ser utilizados.

   ![Consulte os recipientes na conta de armazenamento](./media/tutorial-message-enrichments/show-blob-containers.png)

As mensagens no contentor chamado **enriquecido** têm os enriquecimentos de mensagens incluídos nas mensagens. As mensagens no contentor chamado **original** têm as mensagens cruas sem enriquecimento. Aperte num dos recipientes até chegar ao fundo e abra o ficheiro de mensagem mais recente. Em seguida, faça o mesmo para que o outro recipiente verifique se não há enriquecimentos adicionados às mensagens nesse recipiente.

Quando se olha para mensagens enriquecidas, deve ver "o meu Hub IoT" com o nome do hub e a localização e identificação do cliente, assim:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Aqui está uma mensagem não enriquecida. Note que "o meu Hub IoT", "localização de dispositivos" e "customerID" não aparecem aqui porque estes campos são adicionados pelos enriquecimentos. Este ponto final não tem enriquecimentos.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os recursos que criou neste tutorial, elimine o grupo de recursos. Esta ação também elimina todos os recursos contidos no grupo. Neste caso, remove o centro ioT, a conta de armazenamento e o próprio grupo de recursos.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Use o Azure CLI para limpar recursos

Para remover o grupo de recursos, utilize o comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). Recorde-se que `$resourceGroup` foi definido para **ContosoResourcesMsgEn** no início deste tutorial.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou e testou adicionando enriquecimentos de mensagens às mensagens IoT Hub utilizando os seguintes passos:

**Use enriquecimentos de mensagens IoT Hub**
> [!div class="checklist"]
> * Primeiro método: Criar recursos e configurar o encaminhamento de mensagens utilizando o CLI Azure. Configure os enriquecimentos de mensagens manualmente utilizando o [portal Azure](https://portal.azure.com).
> * Segundo método: Criar recursos e configurar o encaminhamento de mensagens e enriquecimento de mensagens utilizando um modelo de Gestor de Recursos Azure.
> * Executar uma aplicação que simula um dispositivo IoT enviando mensagens para o centro.
> * Veja os resultados e verifique se os enriquecimentos de mensagens estão a funcionar como esperado.

Para mais informações sobre enriquecimento de mensagens, consulte [a visão geral dos enriquecimentos de mensagens.](iot-hub-message-enrichments-overview.md)

Para obter mais informações sobre o encaminhamento de mensagens, consulte estes artigos:

* [Use o encaminhamento de mensagens IoT Hub para enviar mensagens dispositivo-cloud para diferentes pontos finais](iot-hub-devguide-messages-d2c.md)
* [Tutorial: Encaminhamento do Hub IoT](tutorial-routing.md)
