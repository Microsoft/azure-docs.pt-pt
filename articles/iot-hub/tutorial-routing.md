---
title: Configurar o encaminhamento de mensagens com o Hub IoT do Azure (.NET) | Microsoft Docs
description: Configurar o encaminhamento de mensagens com o Hub IoT do Azure
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 09/11/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: df98be4dbb65088951968a16198b41d3d6d0bb67
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57410218"
---
# <a name="tutorial-configure-message-routing-with-iot-hub"></a>Tutorial: Configurar o encaminhamento de mensagens com o IoT Hub

O [encaminhamento de mensagens](iot-hub-devguide-messages-d2c.md) permite enviar dados de telemetria dos dispositivos IoT para pontos finais compatíveis com Hubs de Eventos incorporados ou pontos finais personalizados, tais como o armazenamento de blobs, a Fila do Service Bus, o Tópico do Service Bus e os Hubs de Eventos. Ao configurar o encaminhamento de mensagens, pode criar [consultas de encaminhamento](iot-hub-devguide-routing-query-syntax.md) para personalizar o encaminhamento que corresponde a uma determinada condição. Uma vez configurado, os dados de entrada são automaticamente encaminhados para os pontos finais pelo Hub IoT. 

Neste tutorial, vai aprender a configurar e a utilizar consultas de encaminhamento com o Hub IoT. Vai encaminhar mensagens de um dispositivo IoT para um dos vários serviços, incluindo para o armazenamento de blobs e para uma fila do Service Bus. As mensagens para a fila do Service Bus serão detetadas por uma Aplicação Lógica e enviadas por e-mail. As mensagens que não dispõem de configuração especificamente para encaminhamento são enviadas para o ponto final predefinido e apresentadas numa visualização do Power BI.

Neste tutorial, vai realizar as seguintes tarefas:

> [!div class="checklist"]
> * Com a CLI do Azure ou o PowerShell, configurar os recursos base: um hub IoT, uma conta de armazenamento, uma fila do Service Bus e um dispositivo simulado.
> * Configurar pontos finais e rotas no hub IoT para a conta de armazenamento e para a fila do Service Bus.
> * Criar uma Aplicação Lógica que é acionada para enviar e-mails quando é adicionada uma mensagem à fila do Service Bus.
> * Transferir e executar uma aplicação que simula um Dispositivo IoT a enviar mensagens para o hub para as diferentes opções de encaminhamento.
> * Criar uma visualização do Power BI de dados enviados para o ponto final predefinido.
> * Visualizar os resultados...
> * …na fila do Service Bus e nos e-mails.
> * …na conta de armazenamento.
> * ...na visualização do Power BI.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Instale o [Visual Studio](https://www.visualstudio.com/). 

- Uma conta do Power BI para examinar a análise de transmissão do ponto final predefinido. ([Experimente o Power BI gratuitamente](https://app.powerbi.com/signupredirect?pbi_source=web).)

- Uma conta do Office 365 para enviar e-mails de notificação. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Configurar recursos

Para este tutorial, precisa de um hub IoT, uma conta de armazenamento e uma fila do Service Bus. Pode criar estes recursos com a CLI do Azure ou o Azure PowerShell. Utilize o mesmo grupo de recursos e a mesma localização para todos os recursos. Em seguida, no final, pode remover tudo num único passo ao eliminar o grupo de recursos.

As secções seguintes descrevem como efetuar estes passos obrigatórios. Siga as instruções da CLI *ou* do PowerShell.

1. Crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md). 

2. Crie um hub IoT na camada S1. Adicione um grupo de consumidores ao hub IoT. O grupo de consumidores é utilizado pelo Azure Stream Analytics durante a obtenção dos dados.

   > [!NOTE]
   > Tem de utilizar um hub Iot num escalão pago para concluir este tutorial. O escalão gratuito permite-lhe apenas configurar um ponto final e requer vários pontos de extremidade.
   > 

3. Crie uma conta de armazenamento standard V1 com replicação Standard_LRS.

4. Crie um espaço de nomes e uma fila do Service Bus. 

5. Crie uma identidade para o dispositivo simulado que envia mensagens para o hub. Guarde a chave para a fase de teste.

### <a name="set-up-your-resources-using-azure-cli"></a>Configurar os recursos com a CLI do Azure

Copie e cole este script no Cloud Shell. Partindo do princípio de que já tem sessão iniciada, o script é executado uma linha de cada vez. 

As variáveis que têm de ser globalmente exclusivas têm `$RANDOM` concatenado às mesmas. Quando o script for executado e as variáveis forem definidas, uma cadeia numérica aleatória é gerada e concatenada ao final da cadeia de carateres fixa, tornando-a exclusiva.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostorage$RANDOM
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
echo "$storageAccountKey"

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off 

# The Service Bus namespace must be globally unique, so add a random number to the end.
sbNameSpace=ContosoSBNamespace$RANDOM
echo "Service Bus namespace = " $sbNameSpace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNameSpace \
    --location $location
    
# The Service Bus queue name must be globally unique, so add a random number to the end.
sbQueueName=ContosoSBQueue$RANDOM
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNameSpace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

### <a name="set-up-your-resources-using-azure-powershell"></a>Configurar os recursos com o Azure PowerShell

Copie e cole este script no Cloud Shell. Partindo do princípio de que já tem sessão iniciada, o script é executado uma linha de cada vez.

As variáveis que têm de ser globalmente exclusivas têm `$(Get-Random)` concatenado às mesmas. Quando o script for executado e as variáveis forem definidas, uma cadeia numérica aleatória é gerada e concatenada ao final da cadeia de carateres fixa, tornando-a exclusiva.

```azurepowershell-interactive
# Log into Azure account.
Login-AzAccount

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"
$iotDeviceName = "Contoso-Test-Device"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, so add a random number to the end.
$iotHubName = "ContosoTestHub$(Get-Random)"
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

# The storage account name must be globally unique, so add a random number to the end.
$storageAccountName = "contosostorage$(Get-Random)"
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
$storageContext = $storageAccount.Context 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageContext

# The Service Bus namespace must be globally unique,
#   so add a random number to the end.
$serviceBusNamespace = "ContosoSBNamespace$(Get-Random)"
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random number to the end.
$serviceBusQueueName  = "ContosoSBQueue$(Get-Random)"
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName 

```

Depois, crie uma identidade do dispositivo e guarde a chave para utilização posterior. Esta identidade do dispositivo é utilizada pela aplicação de simulação para enviar mensagens para o hub IoT. Esta capacidade não está disponível no PowerShell, mas pode criar o dispositivo no [portal do Azure](https://portal.azure.com).

1. Abra o [portal do Azure](https://portal.azure.com) e inicie sessão na sua conta do Azure.

2. Clique em **Grupos de recursos** e selecione o grupo de recursos. Este tutorial utiliza **ContosoResources**.

3. Na lista de recursos, clique no hub IoT. Este tutorial utiliza **ContosoTestHub**. Selecione **Dispositivos IoT** no painel Hub.

4. Clique em **+ Adicionar**. No painel Adicionar Dispositivo, preencha o ID do dispositivo. Este tutorial utiliza **Contoso-Test-Device**. Deixe as chaves em branco e marque **Chaves de Geração Automática**. Confirme que a opção **Ligar dispositivo ao hub IoT**  está ativada. Clique em **Guardar**.

   ![Captura de ecrã que mostra o ecrã Adicionar dispositivo.](./media/tutorial-routing/add-device.png)

5. Agora que foi criado, clique no dispositivo para ver as chaves geradas. Clique no ícone de Cópia na Chave primária e guarde-a na localização desejada, por exemplo, no Bloco de notas para a fase de teste deste tutorial.

   ![Captura de ecrã que mostra os detalhes do dispositivo, incluindo as chaves.](./media/tutorial-routing/device-details.png)

## <a name="set-up-message-routing"></a>Configurar o encaminhamento de mensagens

Vai encaminhar mensagens para diferentes recursos com base nas propriedades anexadas à mensagem através do dispositivo simulado. As mensagens sem encaminhamento personalizado são enviadas para o ponto final predefinido (mensagens/eventos). 

|valor |Resultado|
|------|------|
|level="storage" |Escrever no Armazenamento do Azure.|
|level="critical" |Escrever numa fila do Service Bus. Uma Aplicação Lógica obtém a mensagem da fila e utiliza o Office 365 para enviar o e-mail.|
|predefinição |Apresentar estes dados com o Power BI.|

### <a name="routing-to-a-storage-account"></a>Encaminhar para uma conta de armazenamento 

Agora configure o encaminhamento para a conta de armazenamento. Vá para o painel Encaminhamento de Mensagens e, em seguida, adicione uma rota. Ao adicionar a rota, defina um novo ponto final para a rota. Depois de o configurar, as mensagens com a propriedade **nível** definida como **armazenamento** são automaticamente escritas para uma conta de armazenamento. 

Por predefinição, os dados são escritos para o blob de armazenamento no formato Avro.

1. No [portal do Azure](https://portal.azure.com), clique em**Grupos de Recursos** e selecione o grupo de recursos. Este tutorial utiliza **ContosoResources**. 

2. Clique no hub IoT na lista de recursos. Este tutorial utiliza **ContosoTestHub**. 

3. Clique em **Encaminhamento de Mensagens**. No painel **Encaminhamento de Mensagens**, clique em +**Adicionar**. No painel **Adicionar uma Rota**, clique em +**Adicionar** junto ao campo Ponto final, tal como apresentado na imagem seguinte:

   ![Captura de ecrã que mostra como começar a adicionar um ponto final a uma rota.](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. Selecione **Armazenamento de blobs**. Verá o painel **Adicionar Ponto Final de Armazenamento**. 

   ![Captura de ecrã que mostra a adição de um ponto final.](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. Introduza um nome para o ponto final. Este tutorial utiliza **StorageContainer**.

6. Clique em **Escolher um contentor**, será apresentada a lista das suas contas de armazenamento. Selecione a que configurou nos passos de preparação. Este tutorial utiliza **contosostorage**. Mostra uma lista de contentores nessa conta de armazenamento. Selecione o contentor que configurou nos passos de preparação. Este tutorial utiliza **contosoresults**. Clique em **Selecionar**. Volta ao painel **Adicionar ponto final**. 

7. Para efeitos deste tutorial, utilize as predefinições no resto dos campos. 

   > [!NOTE]
   > Pode definir o formato do nome de blob com o **Formato do nome de ficheiro blob**. A predefinição é `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. O formato tem de conter {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH}, and {mm} por qualquer ordem. 
   > 
   > Por exemplo, ao utilizar o formato de nome de ficheiro blob predefinido, se o nome do hub for ContosoTestHub e a data/hora for 30 de outubro de 2018 à 10:56, o nome do blob ficará assim: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > Por predefinição, os blobs são escritos no formato Avro. Pode optar por gravar arquivos no formato JSON. A capacidade de codificar o formato JSON está em pré-visualização em todas as regiões que IOT Hub está disponível, exceto E.U.A. leste, E.U.A. oeste e Europa Ocidental. Veja [documentação de orientação sobre o encaminhamento para o armazenamento de BLOBs] (iot-hub-devguide-messages-d2c.md#azure-blob-storage).
   > 
   > Quando o encaminhamento para o armazenamento de BLOBs, recomendamos que inscrever os blobs e, em seguida, iterar sobre os mesmos, para garantir que todos os contentores são lidas sem fazer suposições de partição. O intervalo de partição pode potencialmente alterar durante um [iniciada pelo Microsoft ativação pós-falha](iot-hub-ha-dr.md#microsoft-initiated-failover) ou o IoT Hub [ativação pós-falha manual](iot-hub-ha-dr.md#manual-failover-preview). Para saber como enumere a lista de blobs consulte [encaminhamento para o armazenamento de BLOBs](iot-hub-devguide-messages-d2c.md#azure-blob-storage)
   >

8. Clique em **Criar** para criar o ponto final de armazenamento e adicioná-lo à rota. Volta ao painel **Adicionar uma rota**.

9. Agora, preencha o resto das informações da consulta de encaminhamento. Esta consulta especifica os critérios para enviar mensagens para o contentor de armazenamento que acabou de adicionar como ponto final. Preencha os campos no ecrã. 

   **Nome**: Introduza um nome para a consulta de encaminhamento. Este tutorial utiliza **StorageRoute**.

   **Ponto final**: Isso mostra o ponto final que acabou de configurar. 
   
   **Origem de dados**: Selecione **mensagens de telemetria do dispositivo** na lista pendente.

   **Ativar a rota**: Certifique-se de que esta opção estiver ativada.
   
   **Consulta de encaminhamento**: Introduza `level="storage"` como a cadeia de consulta. 

   ![Captura de ecrã que mostra a criação de uma consulta de encaminhamento para a conta de armazenamento.](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  
   
   Clique em **Guardar**. Quando terminar, volta ao painel Encaminhamento de Mensagens, onde pode ver a nova consulta de encaminhamento do armazenamento. Feche o painel Rotas para voltar para a página Grupo de recursos.

### <a name="routing-to-a-service-bus-queue"></a>Encaminhar para uma fila do Service Bus 

Agora configure o encaminhamento para a fila do Service Bus. Vá para o painel Encaminhamento de Mensagens e, em seguida, adicione uma rota. Ao adicionar a rota, defina um novo ponto final para a rota. Depois de o configurar, as mensagens com a propriedade **nível** definida como **crítica** são escritas para a fila do Service Bus, que aciona uma Aplicação Lógica, que envia um e-mail com as informações. 

1. Na página Grupo de recursos, clique no hub IoT e, em seguida, clique em **Encaminhamento de Mensagens**. 

2. No painel **Encaminhamento de Mensagens**, clique em +**Adicionar**. 

3. No painel **Adicionar uma Rota**, clique em +**Adicionar** junto ao campo Ponto final. Selecione **Fila do Service Bus**. Verá o painel **Adicionar Ponto Final do Service Bus**. 

   ![Captura de ecrã que mostra a adição de um ponto final do service bus](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Preencha os campos:

   **Nome do ponto final**: Introduza um nome para o ponto final. Este tutorial utiliza **CriticalQueue**.
   
   **Espaço de nomes do barramento de serviço**: Clique neste campo para revelar a lista suspensa; Selecione o espaço de nomes de barramento do serviço que configurar os passos de preparação. Este tutorial utiliza **ContosoSBNamespace**.

   **Fila do Service Bus**: Clique neste campo para revelar a lista suspensa; Selecione a fila do Service Bus na lista pendente. Este tutorial utiliza **contososbqueue**.

5. Clique em **Criar** para adicionar o ponto final da fila do Service Bus. Volta ao painel **Adicionar uma rota**. 

6.  Agora, preencha o resto das informações da consulta de encaminhamento. Esta consulta especifica os critérios para enviar mensagens para a fila do Service Bus que acabou de adicionar como ponto final. Preencha os campos no ecrã. 

   **Nome**: Introduza um nome para a consulta de encaminhamento. Este tutorial utiliza **SBQueueRoute**. 

   **Ponto final**: Isso mostra o ponto final que acabou de configurar.

   **Origem de dados**: Selecione **mensagens de telemetria do dispositivo** na lista pendente.

   **Consulta de encaminhamento**: Introduza `level="critical"` como a cadeia de consulta. 

   ![Captura de ecrã que mostra a criação de uma consulta de encaminhamento para a fila do Service Bus.](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Clique em **Guardar**. Quando regressar ao painel Rotas, verá as duas novas rotas, tal como apresentado aqui.

   ![Captura de ecrã que mostra as rotas que acabou de configurar.](./media/tutorial-routing/message-routing-show-both-routes.png)

8. Pode ver os pontos finais personalizados que configurou ao clicar no separador **Pontos Finais Personalizados**.

   ![Captura de ecrã que mostra os pontos finais personalizados que acabou de configurar.](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Feche o painel Encaminhamento de Mensagens para voltar ao painel Grupo de recursos.

## <a name="create-a-logic-app"></a>Criar uma Aplicação Lógica  

A fila do Service Bus serve para receber mensagens designadas como críticas. Configure uma Aplicação lógica para monitorizar a fila do Service Bus e enviar um e-mail quando é adicionada uma mensagem à fila. 

1. No [portal do Azure](https://portal.azure.com), clique em **+ Criar um recurso**. Introduza **aplicação lógica** na caixa de pesquisa e clique em Enter. Nos resultados da pesquisa apresentados, selecione a Aplicação Lógica e clique em **Criar** para avançar para o painel **Criar aplicação lógica**. Preencha os campos. 

   **Nome**: Este campo é o nome da aplicação lógica. Este tutorial utiliza **ContosoLogicApp**. 

   **Subscrição**: Selecione a sua subscrição do Azure.

   **Grupo de recursos**: Clique em **utilizar existente** e selecione o grupo de recursos. Este tutorial utiliza **ContosoResources**. 

   **Localização**: Utilize a sua localização. Este tutorial utiliza **West US**. 

   **Log Analytics**: Esta alternância deverá desativá-la. 

   ![Captura de ecrã que mostra o ecrã Criar Aplicação Lógica.](./media/tutorial-routing/create-logic-app.png)

   Clique em **Criar**.

2. Agora, vá para a Aplicação Lógica. A forma mais fácil de aceder à Aplicação Lógica é clicar em **Grupos de recursos**, selecionar o grupo de recursos (este tutorial utiliza **ContosoResources**) e, em seguida, selecionar a Aplicação Lógica na lista de recursos. É apresentada a página Estruturador de Aplicações Lógicas (poderá ter de deslocar o ecrã para a direita para ver a página completa). Na página Estruturador de Aplicações Lógicas, desloque o ecrã para baixo até ver o mosaico que indica **Aplicação Lógica em Branco +** e clique nele. 

3. É apresentada uma lista de conectores. Selecione **Service Bus**. 

   ![Captura de ecrã que mostra a lista de conectores.](./media/tutorial-routing/logic-app-connectors.png)

4. É apresentada uma lista de acionadores. Selecione **Service Bus – Quando uma mensagem é recebida numa fila (concluir automaticamente)**. 

   ![Captura de ecrã que mostra a lista de acionadores do Service Bus.](./media/tutorial-routing/logic-app-triggers.png)

5. No ecrã seguinte, preencha o Nome da Ligação. Este tutorial utiliza **ContosoConnection**. 

   ![Captura de ecrã que mostra como configurar a ligação para a fila do Service Bus.](./media/tutorial-routing/logic-app-define-connection.png)

   Clique no espaço de nomes do Service Bus. Este tutorial utiliza **ContosoSBNamespace**. Quando seleciona o espaço de nomes, o portal consulta o espaço de nomes do Service Bus para obter as chaves. Selecione **RootManageSharedAccessKey** e clique em **Criar**. 
   
   ![Captura de ecrã que mostra como configurar a ligação.](./media/tutorial-routing/logic-app-finish-connection.png)

6. No ecrã seguinte, selecione o nome da fila na lista pendente (este tutorial utiliza **contososbqueue**). Pode utilizar as predefinições no resto dos campos. 

   ![Captura de ecrã que mostra as opções da fila.](./media/tutorial-routing/logic-app-queue-options.png)

7. Agora, configure a ação para enviar um e-mail quando é recebida uma mensagem na fila. No Estruturador de Aplicações Lógicas, clique em **+Novo passo** para adicionar um passo e, em seguida, clique em**Adicionar uma ação**. No painel **Escolher uma ação**, localize e clique em **Outlook do Office 365**. No ecrã de acionadores, selecione **Outlook do Office 365 – Enviar um e-mail**.  

   ![Captura de ecrã que mostra as opções do Office 365.](./media/tutorial-routing/logic-app-select-outlook.png)

8. Em seguida, inicie sessão na sua conta Office 365 para configurar a ligação. Especifique os endereços de e-mail dos destinatários. Especifique também o assunto e escreva a mensagem para apresentar no corpo ao destinatário. Para fins de teste, introduza o seu próprio endereço de e-mail para o destinatário.

   Clique em **Adicionar conteúdo dinâmico** para mostrar o conteúdo da mensagem que pode incluir. Selecione **Conteúdo** – incluirá a mensagem no e-mail. 

   ![Captura de ecrã que mostra as opções de e-mail da aplicação lógica.](./media/tutorial-routing/logic-app-send-email.png)

9. Clique em **Guardar**. Em seguida, feche o Estruturador de Aplicações Lógicas.

## <a name="set-up-azure-stream-analytics"></a>Configurar o Azure Stream Analytics

Para ver os dados numa visualização do Power BI, configure primeiro uma tarefa do Stream Analytics para obter os dados. Lembre-se de que apenas as mensagens com o **nível** definido como **normal** são enviadas para o ponto final predefinido e serão obtidas pela tarefa do Stream Analytics para a visualização do Power BI.

### <a name="create-the-stream-analytics-job"></a>Criar a tarefa do Stream Analytics

1. No [portal do Azure](https://portal.azure.com), clique em **Criar um recurso** > **Internet das Coisas** > **Tarefa do Stream Analytics**.

2. Introduza as seguintes informações para a tarefa.

   **Nome da tarefa**: O nome da tarefa. O nome tem de ser globalmente exclusivo. Este tutorial utiliza **contosoJob**.

   **Grupo de recursos**: Utilize o mesmo grupo de recursos utilizado pelo IoT hub. Este tutorial utiliza **ContosoResources**. 

   **Localização**: Utilize a mesma localização que utilizou no script de configuração. Este tutorial utiliza **West US**. 

   ![Captura de ecrã que mostra como criar a tarefa do Stream Analytics.](./media/tutorial-routing/stream-analytics-create-job.png)

3. Clique em **Criar** para criar a tarefa. Para voltar ao trabalho, clique em **Grupos de recursos**. Este tutorial utiliza **ContosoResources**. Selecione o grupo de recursos e, em seguida, clique na tarefa de Stream Analytics na lista de recursos. 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada à tarefa do Stream Analytics

4. Em **Topologia da Tarefa**, clique em **Entradas**.

5. No painel **Entradas**, clique em **Adicionar entrada de fluxo** e selecione o Hub IoT. No ecrã apresentado, preencha os campos seguintes:

   **O alias de entrada**: Este tutorial utiliza **contosoinputs**.

   **Subscrição**: Selecione a sua subscrição.

   **IoT Hub**: Selecione o Hub IoT. Este tutorial utiliza **ContosoTestHub**.

   **Ponto final**: Selecione **mensagens**. (Se selecionar Monitorização de Operações, poderá obter os dados de telemetria sobre o hub IoT, em vez dos dados que está a enviar.) 

   **Nome da política de acesso partilhado**: Selecione **iothubowner**. O portal preenche a Chave da Política de Acesso Partilhado por si.

   **Grupo de consumidores**: Selecione o grupo de consumidores que criou anteriormente. Este tutorial utiliza **contosoconsumers**.
   
   Para o resto dos campos, aceite as predefinições. 

   ![Captura de ecrã que mostra como configurar a tarefa do Stream Analytics.](./media/tutorial-routing/stream-analytics-job-inputs.png)

6. Clique em **Guardar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída à tarefa do Stream Analytics

1. Em **Topologia da Tarefa**, clique em **Saídas**.

2. No painel **Saídas**, clique em **Adicionar** e selecione **Power BI**. No ecrã apresentado, preencha os campos seguintes:

   **Alias de saída**: O alias exclusivo para a saída. Este tutorial utiliza **contosooutputs**. 

   **Nome do conjunto de dados**: Nome do conjunto de dados a ser utilizado no Power BI. Este tutorial utiliza **contosodataset**. 

   **Nome da tabela**: Nome da tabela a ser utilizado no Power BI. Este tutorial utiliza **contosotable**.

   Aceite as predefinições no resto dos campos.

3. Clique em **Autorizar**e inicie sessão na sua conta do Power BI.

   ![Captura de ecrã que mostra como configurar as saídas para a tarefa do Stream Analytics.](./media/tutorial-routing/stream-analytics-job-outputs.png)

4. Clique em **Guardar**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta da tarefa do Stream Analytics

1. Em **Topologia de Tarefas**, clique em **Consulta**.

2. Substitua `[YourInputAlias]` pelo alias de entrada da tarefa. Este tutorial utiliza **contosoinputs**.

3. Substitua `[YourOutputAlias]` pelo alias de saída da tarefa. Este tutorial utiliza **contosooutputs**.

   ![Captura de ecrã que mostra como configurar a consulta para a tarefa do Stream Analytics.](./media/tutorial-routing/stream-analytics-job-query.png)

4. Clique em **Guardar**.

5. Feche o painel Consulta. Esta ação leva-o de volta à vista de recursos no grupo de recursos. Clique na tarefa do Stream Analytics. Este tutorial denomina-o **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

Na tarefa do Stream Analytics, clique em **Iniciar** > **Agora** > **Iniciar**. Assim que a tarefa for iniciada com êxito, o estado da tarefa é alterado de **Parado** para **Em execução**.

Para configurar o relatório do Power BI, vai precisar de dados. Para isso, vai configurar o Power BI, depois de criar o dispositivo e executar a aplicação de simulação do dispositivo.

## <a name="run-simulated-device-app"></a>Executara aplicação de Dispositivo Simulada

Anteriormente na secção de configuração de scripts, configurou um dispositivo para simular a utilização de um dispositivo IoT. Nesta secção, vai transferir uma aplicação de consola do .NET para simular um dispositivo que envia mensagens de dispositivo para cloud a um Hub IoT. Esta aplicação envia mensagens para cada um dos métodos de encaminhamento diferentes. 

Transfira a solução para a [Simulação de Dispositivos IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Esta ação transfere um repositório com várias aplicações; a solução que procura está em iot-hub/Tutorials/Routing/SimulatedDevice/.

Faça duplo clique no ficheiro da solução (SimulatedDevice.sln) para abrir o código no Visual Studio e, em seguida, abra Program.cs. Substitua `{iot hub hostname}` pelo nome do anfitrião do hub IoT. O formato do nome do anfitrião do hub IoT é **{iot-hub-name}.azure-devices.net**. Para este tutorial, o nome do anfitrião do hub é **ContosoTestHub.azure-devices.net**. Em seguida, substitua `{device key}` pela chave de dispositivo que guardou anteriormente quando configurou o dispositivo simulado. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Executar e testar 

Execute a aplicação de consola. Aguarde alguns minutos. Pode ver as mensagens a serem enviadas no ecrã da consola da aplicação.

A aplicação envia uma nova mensagem de dispositivo para cloud a cada segundo. A mensagem contém um objeto JSON serializado com o ID do dispositivo, a temperatura, a humidade e o nível da mensagem, que está predefinido como `normal`. Atribui aleatoriamente um nível de `critical` ou `storage`, o que faz com que a mensagem seja encaminhada para a conta de armazenamento ou para a fila do Service Bus (que aciona a Aplicação Lógica para enviar um e-mail). As leituras (`normal`) predefinidas serão apresentadas no relatório do BI que vai configurar a seguir.

Se tudo estiver configurado corretamente, neste momento, deverá ver os seguintes resultados:

1. Começa a receber e-mails sobre mensagens críticas. 

   ![Captura de ecrã que mostra os e-mails resultantes.](./media/tutorial-routing/results-in-email.png)

   Significado:

   * O encaminhamento para a fila do Service Bus está a funcionar corretamente.
   * A Aplicação Lógica que obtém a mensagem da fila do Service Bus está a funcionar corretamente.
   * O conector da Aplicação Lógica para o Outlook está a funcionar corretamente. 

2. No [portal do Azure](https://portal.azure.com), clique em**Grupos de recursos** e selecione o seu Grupo de Recursos. Este tutorial utiliza **ContosoResources**. Selecione a conta de armazenamento, clique em **Blobs** e selecione o Contentor. Este tutorial utiliza **contosoresults**. Deverá ver uma pasta e poderá pesquisar através dos diretórios até ver um ou mais ficheiros. Abra um desses ficheiros; contêm as entradas encaminhadas para a conta de armazenamento. 

   ![Captura de ecrã que mostra os ficheiros resultantes no armazenamento.](./media/tutorial-routing/results-in-storage.png)

Significado:

   * O encaminhamento para a conta de armazenamento está a funcionar corretamente.

Agora com a aplicação ainda em execução, configure a visualização do Power BI para ver as mensagens que chegam através do encaminhamento predefinido. 

## <a name="set-up-the-power-bi-visualizations"></a>Configurar as Visualizações do Power BI

1. Inicie sessão na sua conta do [Power BI](https://powerbi.microsoft.com/).

2. Aceda a **Áreas de Trabalho** e selecione a área de trabalho que definiu quando criou a saída da tarefa do Stream Analytics. Este tutorial utiliza **A Minha Área de Trabalho**. 

3. Clique em **Conjuntos de dados**.

   Deverá ver o conjunto de dados que especificou quando criou a saída da tarefa do Stream Analytics. Este tutorial utiliza **contosodataset**. (Pode demorar 5 a 10 minutos para que o conjunto de dados seja apresentado na primeira vez.)

4. Em **AÇÕES**, clique no primeiro ícone para criar um relatório.

   ![Captura de ecrã que mostra a área de trabalho do Power BI com Ações e o ícone de relatório realçados.](./media/tutorial-routing/power-bi-actions.png)

5. Crie um gráfico de linhas para mostrar a temperatura em tempo real ao longo do tempo.

   * Na página de criação do relatório, adicione um gráfico de linhas ao clicar no ícone de gráfico de linhas.

   ![Captura de ecrã que mostra as visualizações e os campos.](./media/tutorial-routing/power-bi-visualizations-and-fields.png)

   * No painel **Campos**, expanda a tabela que especificou quando criou a saída da tarefa do Stream Analytics. Este tutorial utiliza **contosotable**.

   * Arraste **EventEnqueuedUtcTime** para **Eixo** no painel **Visualizações**.

   * Arraste **temperatura** para **Valores**.

   É criado um gráfico de linhas. O eixo X apresenta a data e hora no fuso horário UTC. O eixo Y apresenta a temperatura do sensor.

6. Crie outro gráfico de linhas para mostrar a humidade em tempo real ao longo do tempo. Para configurar o segundo gráfico, siga os mesmos passos acima e coloque **EventEnqueuedUtcTime** no eixo X e **humidade** no eixo Y.

   ![Captura de ecrã que mostra o relatório do Power BI final com os dois gráficos.](./media/tutorial-routing/power-bi-report.png)

7. Clique em **Guardar** para guardar o relatório.

Poderá ver os dados em ambos os gráficos. Significado:

   * O encaminhamento para o ponto final predefinido está a funcionar corretamente.
   * A tarefa do Azure Stream Analytics está a transmitir corretamente.
   * A Visualização do Power BI está configurada corretamente.

Pode atualizar os gráficos para ver os dados mais recentes ao clicar no botão Atualizar na parte superior da janela do Power BI. 

## <a name="clean-up-resources"></a>Limpar recursos 

Se quiser remover todos os recursos que criou, elimine o grupo de recursos. Esta ação também elimina todos os recursos contidos no grupo. Neste caso, remove o hub IoT, o espaço de nomes e a fila do Service Bus, a Aplicação Lógica, a conta de armazenamento e o próprio grupo de recursos. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Limpar os recursos na visualização do Power BI

Inicie sessão na sua conta do [Power BI](https://powerbi.microsoft.com/). Vá para a área de trabalho. Este tutorial utiliza **A Minha Área de Trabalho**. Para remover a visualização do Power BI, aceda a Conjuntos de Dados e clique no ícone do lixo para eliminar o conjunto de dados. Este tutorial utiliza **contosodataset**. Quando remover o conjunto de dados, o relatório é também removido.

### <a name="clean-up-resources-using-azure-cli"></a>Limpar os recursos com a CLI do Azure

Para remover o grupo de recursos, utilize o comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```
### <a name="clean-up-resources-using-powershell"></a>Limpar os recursos com o PowerShell

Para remover o grupo de recursos, utilize o [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) comando. $resourceGroup foi definido como **ContosoIoTRG1** anteriormente no início deste tutorial.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a utilizar o encaminhamento de mensagens para encaminhar mensagens do Hub IoT para diferentes destinos ao realizar as seguintes tarefas.  

> [!div class="checklist"]
> * Com a CLI do Azure ou o PowerShell, configurar os recursos base: um hub IoT, uma conta de armazenamento, uma fila do Service Bus e um dispositivo simulado.
> * Configurar pontos finais e rotas no hub IoT para a conta de armazenamento e para a fila do Service Bus.
> * Criar uma Aplicação Lógica que é acionada para enviar e-mails quando é adicionada uma mensagem à fila do Service Bus.
> * Transferir e executar uma aplicação que simula um Dispositivo IoT a enviar mensagens para o hub para as diferentes opções de encaminhamento.
> * Criar uma visualização do Power BI de dados enviados para o ponto final predefinido.
> * Visualizar os resultados...
> * …na fila do Service Bus e nos e-mails.
> * …na conta de armazenamento.
> * ...na visualização do Power BI.

Avance para o tutorial seguinte para aprender a gerir o estado de um dispositivo IoT. 

> [!div class="nextstepaction"]
[Configurar e utilizar métricas e diagnósticos com um IoT Hub](tutorial-use-metrics-and-diags.md)
