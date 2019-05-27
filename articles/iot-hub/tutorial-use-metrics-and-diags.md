---
title: Configurar e utilizar métricas e registos de diagnóstico com um hub IoT do Azure | Documentos da Microsoft
description: Configurar e utilizar métricas e registos de diagnóstico com um hub IoT do Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 3/13/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 40e54daa60efedd84b32c72f29d1e2a8858c27da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66162347"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>Tutorial: Configurar e utilizar métricas e registos de diagnóstico com um hub IoT

Se tiver uma solução de IoT Hub em execução na produção, que pretende configurar algumas métricas e ativar os registos de diagnóstico. Em seguida, se ocorrer um problema, tem dados para ver o que irão ajudá-lo a diagnosticar o problema e corrigi-lo o mais rapidamente. Neste artigo, verá como ativar os registos de diagnóstico e como verificá-los a existência de erros. Também definir algumas métricas de observação e alertas que são acionados quando as métricas de atingir um certo limite. Por exemplo, poderia ter um email seja enviado a quando o número de mensagens de telemetria enviada excede um limite específico ou quando o número de mensagens utilizada se aproxima a quota de mensagens permitidos por dia para o IoT Hub. 

Um caso de utilização de exemplo é uma bomba de gasolina, onde as bombas são dispositivos de IoT que enviam comunicam com um hub IoT. Cartões de crédito são validados e a transação final é escrita um arquivo de dados. Se os dispositivos de IoT parar a ligar ao hub e envio de mensagens, é muito mais difícil corrigir se não têm visibilidade o que está acontecendo.

Este tutorial utiliza o exemplo do Azure da [encaminhamento do IoT Hub](tutorial-routing.md) para enviar mensagens para o hub IoT.

Neste tutorial, vai realizar as seguintes tarefas:

> [!div class="checklist"]
> * Com a CLI do Azure, crie um hub IoT, um dispositivo simulado e uma conta de armazenamento.  
> * Ative registos de diagnóstico.
> * Ative as métricas.
> * Configure alertas para essas métricas. 
> * Transferir e executar uma aplicação que simula um dispositivo de IoT, envio de mensagens para o hub. 
> * Execute a aplicação até que os alertas de começar a ser disparado. 
> * Ver os resultados de métricas e verifique os registos de diagnóstico. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Instale o [Visual Studio](https://www.visualstudio.com/). 

- Uma conta de e-mail capaz de receber correio.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Configurar recursos

Para este tutorial, terá de um hub IoT, uma conta de armazenamento e um dispositivo de IoT simulado. Pode criar estes recursos com a CLI do Azure ou o Azure PowerShell. Utilize o mesmo grupo de recursos e a mesma localização para todos os recursos. Em seguida, no fim, pode remover tudo num único passo ao eliminar o grupo de recursos.

Estes são os passos necessários.

1. Crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md). 

2. Crie um hub IoT.

3. Crie uma conta de armazenamento standard V1 com replicação Standard_LRS.

4. Crie uma identidade para o dispositivo simulado que envia mensagens para o hub. Guarde a chave para a fase de teste.

### <a name="set-up-resources-using-azure-cli"></a>Configurar recursos de CLI do Azure

Copie e cole este script no Cloud Shell. Partindo do princípio de que já tem sessão iniciada, o script é executado uma linha de cada vez. Os novos recursos são criados no grupo de recursos ContosoResources.

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
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku F1 --location $location

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostoragemon$RANDOM
echo "Storage account name = " $storageAccountName

# Create the storage account.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName 

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>Ao criar a identidade de dispositivo, poderá receber o erro seguinte: *Não existem chaves encontrado para a política iothubowner do IoT Hub ContosoTestHub*. Para corrigir este erro, atualize a extensão de IoT de CLI do Azure e, em seguida, execute novamente os últimos dois comandos no script. 
>
>Eis o comando para atualizar a extensão. Executá-lo na sua instância do Cloud Shell.
>
>```cli
>az extension update --name azure-cli-iot-ext
>```

## <a name="enable-the-diagnostic-logs"></a>Ativar os registos de diagnóstico 

[Os registos de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md) estão desativadas por predefinição quando cria um novo hub IoT. Nesta secção, ative os registos de diagnóstico para o seu hub.

1. Em primeiro lugar, se não esteja no seu hub no portal, clique em **grupos de recursos** e clique no grupo de recursos, recursos da Contoso. Selecione o hub na lista de recursos apresentados. 

2. Procure o **monitorização** secção no painel do IoT Hub. Clique em **Definições de diagnóstico**. 

   ![Captura de ecrã que mostra a parte de definições de diagnóstico do painel do IoT Hub.](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. Certifique-se que a subscrição e grupo de recursos estão corretos. Sob **tipo de recurso**, desmarque a opção **Selecionar tudo**, em seguida, procure e verificar **IoT Hub**. (Ele coloca a marca de verificação junto a *Selecionar tudo* mais uma vez, apenas a ignorá-lo.) Sob **recursos**, selecione o nome do hub. O ecrã deverá ser semelhante a esta imagem: 

   ![Captura de ecrã que mostra a parte de definições de diagnóstico do painel do IoT Hub.](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. Agora, clique em **ativar os diagnósticos**. É apresentado o painel de definições de diagnóstico. Especifique o nome das suas definições de registos de diagnóstico como "diagnósticos-hub".

5. Verifique **arquivo para uma conta de armazenamento**. 

   ![Captura de ecrã que mostra a definição de diagnóstico para arquivar numa conta de armazenamento.](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    Clique em **configurar** para ver a **Selecione uma conta de armazenamento** ecrã, selecionar o correto (*contosostoragemon*) e clique em **OK** para Volte ao painel de definições de diagnóstico. 

   ![Captura de ecrã que mostra a definição dos registos de diagnóstico para arquivar numa conta de armazenamento.](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. Sob **LOG**, verifique **ligações** e **telemetria do dispositivo**e defina o **retenção (dias)** a 7 dias para cada um. O ecrã de definições de diagnóstico deverá agora ser semelhante a esta imagem:

   ![Captura de ecrã que mostra as definições de registo de diagnóstico final.](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. Clique em **Guardar** para guardar as definições. Feche o painel de definições de diagnóstico.

Mais tarde, quando examinar os registos de diagnóstico, poderá ver a ligar e desligar o registo para o dispositivo. 

## <a name="set-up-metrics"></a>Configurar métricas 

Configure agora algumas métricas para observar quando as mensagens são enviadas para o hub. 

1. No painel de definições para o IoT hub, clique nas **métricas** opção a **monitorização** secção.

2. Na parte superior do ecrã, clique em **últimas 24 horas (automático)**. Na lista pendente que é apresentada, selecione **últimas 4 horas** para **intervalo de tempo**e defina **granularidade de tempo** para **1 minuto**, hora local. Clique em **aplicar** para salvar essas configurações. 

   ![Captura de ecrã que mostra as métricas de definições de hora.](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. Existe uma entrada de métrica por predefinição. Deixe o grupo de recursos como a predefinição e o espaço de nomes de métrica. Na **métrica** lista pendente, selecione **mensagens de telemetria enviadas**. Definir **agregação** ao **soma**.

   ![Captura de ecrã que mostra a adição de uma métrica para mensagens de telemetria enviada.](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. Agora, clique em **adicionar métrica** para adicionar outra métrica ao gráfico. Selecione o grupo de recursos (**ContosoTestHub**). Sob **métrica**, selecione **número Total de mensagens utilizada**. Para **agregação**, selecione **média**. 

   Agora seu ecrã mostra a métrica minimizada para *mensagens de telemetria enviadas*, além da nova métrica para *número Total de mensagens utilizada*.

   ![Captura de ecrã que mostra a adição de uma métrica para mensagens de telemetria enviada.](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   Clique em **afixar ao dashboard**. Ele será afixá-la para o dashboard do portal do Azure para que pode acessá-lo novamente. Se não afixá-la ao dashboard, as definições não são mantidas.

## <a name="set-up-alerts"></a>Configurar alertas

Vá para o hub no portal. Clique em **grupos de recursos**, selecione *ContosoResources*, em seguida, selecione o IoT Hub *ContosoTestHub*. 

IoT Hub não tiver sido migrado para o [métricas no Azure Monitor](/azure/azure-monitor/platform/data-collection#metrics) ainda; precisa usar [alertas clássicos](/azure/azure-monitor/platform/alerts-classic.overview).

1. Sob **monitorização**, clique em **alertas** isso mostra a tela principal do alerta. 

   ![Captura de ecrã que mostra como encontrar alertas clássicos.](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. Para aceder aos alertas clássicos a partir daqui, clique em **ver alertas clássicos**. 

    ![Captura de ecrã que mostra o ecrã de alertas clássicos.](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    Preencha os campos: 

    **Subscrição**: Deixe este campo definido como a sua subscrição atual.

    **origem**: Defina este campo para *métricas*.

    **Grupo de recursos**: Defina este campo para o grupo de recursos atual *ContosoResources*. 

    **Tipo de recurso**: Defina este campo para o IoT Hub. 

    **Recurso**: Selecione o seu hub IoT, *ContosoTestHub*.

3. Clique em **Adicionar alerta de métrica (clássico)** para configurar um novo alerta.

    Preencha os campos:

    **Nome**: Forneça um nome para a regra de alerta, como *mensagens de telemetria*.

    **Descrição**: Forneça uma descrição do seu alerta, tal como *alertar quando há 1 000 mensagens de telemetria enviadas*. 

    **origem**: Defina esta opção como *métricas*.

    **Subscrição**, **grupo de recursos**, e **recursos** deve ser definido como os valores selecionados no **ver alertas clássicos** ecrã. 

    Definir **métrica** ao *mensagens de telemetria enviadas*.

    ![Captura de ecrã que mostra como configurar um alerta de clássico para mensagens de telemetria enviada.](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. Depois do gráfico, defina os seguintes campos:

   **Condição**: Defina como *superior a*.

   **Limiar**: Definido como 1000.

   **Período**: Defina como *durante os últimos 5 minutos*.

   **Destinatários de e-mail de notificação**: Coloque o seu endereço de email aqui. 

   ![Captura de ecrã que mostra parte inferior do ecrã de alertas.](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   Clique em **OK** para guardar o alerta. 

5. Agora configurar outro alerta para o *número Total de mensagens utilizada*. Esta métrica é útil se pretender enviar um alerta quando o número de mensagens utilizada é a atingir a quota para o hub IoT – para informá-lo o hub em breve iniciará recusar mensagens.

   No **ver alertas clássicos** ecrã, clique em **Adicionar alerta de métrica (clássico)**, em seguida, preencha estes campos no **Adicionar regra de** painel.

   **Nome**: Forneça um nome para a regra de alerta, como *número-de-mensagens-used*.

   **Descrição**: Forneça uma descrição do seu alerta, tal como *alertar quando se aproximar dos quota*.

   **origem**: Defina este campo para *métricas*.

    **Subscrição**, **grupo de recursos**, e **recursos** deve ser definido como os valores selecionados no **ver alertas clássicos** ecrã. 

    Definir **métrica** ao *número Total de mensagens utilizada*.

6. No gráfico, preencha os campos seguintes:

   **Condição**: Defina como *superior a*.

   **Limiar**: Definido como 1000.

   **Período**: Defina este campo para *durante os últimos 5 minutos*. 

   **Destinatários de e-mail de notificação**: Coloque o seu endereço de email aqui. 

   Clique em **OK** para guardar a regra. 

5. Agora, deverá ver dois alertas no painel de alertas clássicos: 

   ![Captura de ecrã que mostra ecrã de alertas clássicos com as novas regras de alerta.](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. Feche o painel de alertas. 
    
    Com estas definições, receberá um alerta quando o número de mensagens enviadas for superior a 400 e quando o número total de mensagens utilizada excede o número.

## <a name="run-simulated-device-app"></a>Executara aplicação de Dispositivo Simulada

Anteriormente na secção de configuração de scripts, configurou um dispositivo para simular a utilização de um dispositivo IoT. Nesta secção, vai transferir uma aplicação de consola do .NET para simular um dispositivo que envia mensagens de dispositivo para cloud a um Hub IoT.  

Transfira a solução para a [Simulação de Dispositivos IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Esta ligação transfere um repositório com vários aplicativos no mesmo; a solução que procura está em iot-hub/tutoriais/encaminhamento /.

Faça duplo clique no ficheiro da solução (SimulatedDevice.sln) para abrir o código no Visual Studio e, em seguida, abra Program.cs. Substitua `{iot hub hostname}` pelo nome do anfitrião do hub IoT. O formato do nome do anfitrião do hub IoT é **{iot-hub-name}.azure-devices.net**. Para este tutorial, o nome do anfitrião do hub é **ContosoTestHub.azure-devices.net**. Em seguida, substitua `{device key}` pela chave de dispositivo que guardou anteriormente quando configurou o dispositivo simulado. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Executar e testar 

Em Program.cs, alterar o `Task.Delay` de 1000 para 10, o que reduz a quantidade de tempo entre o envio de mensagens de 1 segundo para.01 segundos. Reduzindo este atraso aumenta o número de mensagens enviadas.

```csharp
await Task.Delay(10);
```

Execute a aplicação de consola. Aguarde alguns minutos (10 a 15). Pode ver as mensagens a ser enviadas pelo dispositivo simulado no hub no ecrã da consola da aplicação.

### <a name="see-the-metrics-in-the-portal"></a>Ver as métricas no portal

Abra as suas métricas a partir do Dashboard. Alterar os valores de tempo para *últimos 30 minutos* com uma granularidade de tempo de *1 minuto*. Ele mostra as mensagens de telemetria enviadas e o número total de mensagens utilizada no gráfico, com os números mais recentes na parte inferior do gráfico.

   ![Captura de ecrã que mostra as métricas.](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>Ver os alertas

Volte para alertas. Clique em **grupos de recursos**, selecione *ContosoResources*, em seguida, selecione o hub *ContosoTestHub*. Na página de propriedades apresentada para o hub, selecione **alertas**, em seguida, **ver alertas clássicos**. 

Quando o número de mensagens enviadas excede o limite, começa a obter alertas de email. Para ver se existem quaisquer alertas ativos, aceda ao seu hub e selecione **alertas**. Ele mostra os alertas que estão ativos, e se existirem quaisquer avisos. 

   ![Captura de ecrã que mostra os alertas foram acionados.](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

Clique no alerta para mensagens de telemetria. Mostra o resultado de métrica e um gráfico com os resultados. Além disso, o email enviado para o avisar do alerta disparando terá um aspeto semelhante a esta imagem:

   ![Captura de ecrã do email que mostra que os alertas foram acionados.](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>Consulte os registos de diagnóstico

Configurar os registos de diagnóstico para ser exportados para o armazenamento de Blobs. Aceda ao seu grupo de recursos e selecione a sua conta de armazenamento *contosostoragemon*. Selecione a Blobs, em seguida, abra o contentor *insights-logs-ligações*. Desagregar até chegar à data atual e selecione o ficheiro mais recente. 

   ![Captura de ecrã de desagregação para o contentor de armazenamento para ver os registos de diagnóstico.](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

Clique em **transferir** transferi-la e abri-lo. Consulte os registos do dispositivo se ligar e desligar como enviar mensagens para o hub. Eis um exemplo:

``` json
{ 
  "time": "2018-12-17T18:11:25Z", 
  "resourceId": 
    "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
  "operationName": "deviceConnect", 
  "category": "Connections", 
  "level": "Information", 
  "properties": 
      {"deviceId":"Contoso-Test-Device",
       "protocol":"Mqtt",
       "authType":null,
       "maskedIpAddress":"73.162.215.XXX",
       "statusCode":null
       }, 
  "location": "westus"
}
{ 
   "time": "2018-12-17T18:19:25Z", 
   "resourceId": 
     "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
    "operationName": "deviceDisconnect", 
    "category": "Connections", 
    "level": "Error", 
    "resultType": "404104", 
    "resultDescription": "DeviceConnectionClosedRemotely", 
    "properties": 
        {"deviceId":"Contoso-Test-Device",
         "protocol":"Mqtt",
         "authType":null,
         "maskedIpAddress":"73.162.215.XXX",
         "statusCode":"404"
         }, 
    "location": "westus"
}
```

## <a name="clean-up-resources"></a>Limpar recursos 

Para remover todos os recursos que criou neste tutorial, elimine o grupo de recursos. Esta ação também elimina todos os recursos contidos no grupo. Neste caso, remove o hub IoT, a conta de armazenamento e o grupo de recursos. Se o ter afixado métricas ao dashboard, terá de removê-los manualmente ao clicar nos três pontos no canto superior direito de cada e selecionar **remover**.

Para remover o grupo de recursos, utilize o comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a utilizar as métricas e registos de diagnóstico, efetuando as seguintes tarefas:

> [!div class="checklist"]
> * Com a CLI do Azure, crie um hub IoT, um dispositivo simulado e uma conta de armazenamento.  
> * Ative registos de diagnóstico. 
> * Ative as métricas.
> * Configure alertas para essas métricas. 
> * Transferir e executar uma aplicação que simula um dispositivo de IoT, envio de mensagens para o hub. 
> * Execute a aplicação até que os alertas de começar a ser disparado. 
> * Ver os resultados de métricas e verifique os registos de diagnóstico. 

Avance para o tutorial seguinte para aprender a gerir o estado de um dispositivo IoT. 

> [!div class="nextstepaction"]
> [Configure os seus dispositivos a partir de um serviço de back-end](tutorial-device-twins.md)