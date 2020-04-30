---
title: Configurar e usar métricas e registos de diagnóstico com um hub Azure IoT
description: Aprenda a configurar e utilizar métricas e registos de diagnóstico com um hub Azure IoT. Isto fornecerá dados para analisar para ajudar a diagnosticar problemas que o seu hub pode estar a ter.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 3/13/2019
ms.author: robinsh
ms.custom:
- mvc
- mqtt
ms.openlocfilehash: 3eda4cd8dc10bd9128186b2ff4f8d6ac0254fe5d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81770604"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>Tutorial: Configurar e usar métricas e registos de diagnóstico com um hub IoT

Se tiver uma solução IoT Hub em produção, pretende configurar algumas métricas e ativar registos de diagnóstico. Depois, se ocorrer um problema, tem dados para analisar que o ajudarão a diagnosticar o problema e corrigi-lo mais rapidamente. Neste artigo, você verá como ativar os registos de diagnóstico, e como verificar os erros. Também vais criar algumas métricas para ver, e alertas para o fogo quando as métricas atingirem um certo limite. Por exemplo, pode enviar-lhe um e-mail quando o número de mensagens de telemetria enviadas exceder um limite específico, ou quando o número de mensagens utilizadas se aproxima da quota de mensagens permitida si por dia para o IoT Hub. 

Um caso de utilização exemplo é um posto de gasolina onde as bombas são dispositivos IoT que enviam comunicação com um hub IoT. Os cartões de crédito são validados e a transação final é escrita numa loja de dados. Se os dispositivos IoT pararem de se ligar ao hub e enviarem mensagens, é muito mais difícil de corrigir se não tiver visibilidade no que se passa.

Este tutorial utiliza a amostra Azure do [IoT Hub Routing](tutorial-routing.md) para enviar mensagens para o centro IoT.

Neste tutorial, vai realizar as seguintes tarefas:

> [!div class="checklist"]
> * Utilizando o Azure CLI, crie um hub IoT, um dispositivo simulado e uma conta de armazenamento.  
> * Ativar os registos de diagnóstico.
> * Ativar métricas.
> * Estabeleça alertas para as métricas. 
> * Descarregue e execute uma aplicação que simula um dispositivo IoT enviando mensagens para o centro. 
> * Executar a aplicação até que os alertas comecem a disparar. 
> * Veja os resultados das métricas e verifique os registos de diagnóstico. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Instalar [o Estúdio Visual.](https://www.visualstudio.com/) 

- Uma conta de e-mail capaz de receber correio.

- Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste tutorial utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Configurar recursos

Para este tutorial, você precisa de um hub IoT, uma conta de armazenamento, e um dispositivo IoT simulado. Pode criar estes recursos com a CLI do Azure ou o Azure PowerShell. Utilize o mesmo grupo de recursos e a mesma localização para todos os recursos. Em seguida, no final, pode remover tudo num único passo ao eliminar o grupo de recursos.

Estes são os passos necessários.

1. Criar um [grupo de recursos](../azure-resource-manager/management/overview.md). 

2. Criar um centro de ioT.

3. Crie uma conta de armazenamento standard V1 com replicação Standard_LRS.

4. Crie uma identidade para o dispositivo simulado que envia mensagens para o hub. Guarde a chave para a fase de teste.

### <a name="set-up-resources-using-azure-cli"></a>Configurar recursos utilizando o Azure CLI

Copie e cole este script no Cloud Shell. Partindo do princípio de que já tem sessão iniciada, o script é executado uma linha de cada vez. Os novos recursos são criados no grupo de recursos ContosoResources.

As variáveis que têm de ser globalmente exclusivas têm `$RANDOM` concatenado às mesmas. Quando o script for executado e as variáveis forem definidas, uma cadeia numérica aleatória é gerada e concatenada ao final da cadeia de carateres fixa, tornando-a exclusiva.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-iot

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
>Ao criar a identidade do dispositivo, pode obter o seguinte erro: Não são encontradas chaves para o *iothubowner da IoT Hub ContosoTestHub*. Para corrigir este erro, atualize a extensão Azure CLI IoT e, em seguida, executar os dois últimos comandos no script novamente. 
>
>Aqui está o comando para atualizar a extensão. Executa isto no seu caso Cloud Shell.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="enable-the-diagnostic-logs"></a>Ativar os registos de diagnóstico 

[Os registos de diagnóstico](../azure-monitor/platform/platform-logs-overview.md) são desativados por padrão quando cria um novo hub IoT. Nesta secção, ative os registos de diagnóstico do seu hub.

1. Primeiro, se ainda não estiver no seu hub no portal, clique em **grupos de Recursos** e clique no grupo de recursos Contoso-Recursos. Selecione o centro da lista de recursos apresentados. 

2. Procure a secção **de monitorização** na lâmina do Hub IoT. Clique em **Definições de diagnóstico**. 

   ![Screenshot mostrando as definições de diagnóstico parte da lâmina Do Hub IoT.](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. Certifique-se de que o grupo de subscrição e recursos está correto. Em **Tipo de Recurso,** **desmarque tudo,** procure e verifique **o Hub IoT**. (Coloca a marca de verificação ao lado de *Select All* novamente, basta ignorá-la.) Em **Recurso,** selecione o nome do hub. O seu ecrã deve parecer esta imagem: 

   ![Screenshot mostrando as definições de diagnóstico parte da lâmina Do Hub IoT.](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. Agora clique **em Ligar diagnósticos**. O painel de definições de diagnóstico é apresentado. Especifique o nome das definições de registos de diagnóstico como "diags-hub".

5. Consulte o Arquivo numa conta de **armazenamento.** 

   ![Screenshot mostrando definir os diagnósticos para arquivar para uma conta de armazenamento.](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    Clique em **Configurar** para ver o ecrã da conta de **armazenamento,** selecione o certo *(contosostoragemon)* e clique em **OK** para voltar ao painel de definições de Diagnóstico. 

   ![Screenshot mostrando a definição dos registos de diagnóstico para arquivar para uma conta de armazenamento.](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. Em **LOG,** verifique **as ligações** e **a telemetria**do dispositivo e desloque a **Retenção (dias)** a 7 dias para cada um. O ecrã de definições de Diagnóstico deve agora parecer-se com esta imagem:

   ![Screenshot mostrando as definições finais de registo de diagnóstico.](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. Clique em **Guardar** para guardar as definições. Feche o painel de definições de Diagnóstico.

Mais tarde, quando olhar para os registos de diagnóstico, poderá ver a ligação e desligar o registo do dispositivo. 

## <a name="set-up-metrics"></a>Configurar métricas 

Agora, estabeleça algumas métricas para ver quando as mensagens são enviadas para o centro. 

1. No painel de definições para o hub IoT, clique na opção **Métricas** na secção **de Monitorização.**

2. Na parte superior do ecrã, clique nas **últimas 24 horas (Automática)**. No dropdown que aparece, selecione **Last 4 horas** para intervalo de **tempo**, e coloque a **granularidade** do tempo para **1 minuto,** hora local. Clique **Em Aplicar** para guardar estas definições. 

   ![Screenshot mostrando as definições de tempo das métricas.](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. Há uma entrada métrica por defeito. Deixe o grupo de recursos como padrão e o espaço de nomemétrico. Na lista de dropdown **métrica,** selecione **mensagens de Telemetria enviadas**. Definir **agregação** à **soma**.

   ![Screenshot mostrando adicionar uma métrica para mensagens de telemetria enviadas.](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. Agora clique **em Adicionar métrica** para adicionar outra métrica ao gráfico. Selecione o seu grupo de recursos **(ContosoTestHub).** Em **Métrica,** selecione **o número total de mensagens utilizadas**. Para **agregação, selecione** **Avg**. 

   Agora o seu ecrã mostra a métrica minimizada para *as mensagens de Telemetria enviadas,* mais a nova métrica para o *número total de mensagens utilizadas*.

   ![Screenshot mostrando adicionar uma métrica para mensagens de telemetria enviadas.](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   Clique **em Pin para painel de instrumentos**. Irá fixá-lo no painel do seu portal Azure para que possa aceder novamente. Se não o fixar no painel de instrumentos, as suas definições não são mantidas.

## <a name="set-up-alerts"></a>Configurar alertas

Vai para o centro do portal. Clique **em Grupos de Recursos,** selecione *ContosoResources,* em seguida, selecione IoT Hub *ContosoTestHub*. 

O IoT Hub ainda não foi migrado para as [métricas do Monitor Azure;](/azure/azure-monitor/platform/data-collection#metrics) tem que usar [alertas clássicos.](/azure/azure-monitor/platform/alerts-classic.overview)

1. Em **Monitorização,** clique em **Alertas** Isto mostra o ecrã de alerta principal. 

   ![Screenshot mostrando como encontrar alertas clássicos.](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. Para chegar aos alertas clássicos daqui, clique em **Ver alertas clássicos**. 

    ![Screenshot mostrando o ecrã de alertas clássicos.](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    Preencha os campos: 

    **Subscrição**: Deixe este campo definido para a sua subscrição atual.

    **Fonte**: Desloque este campo para *métricas*.

    **Grupo de recursos**: Desloque este campo para o seu grupo de recursos atuais, *ContosoResources.* 

    **Tipo de recurso**: Desloque este campo para o Hub IoT. 

    **Recurso**: Selecione o seu hub IoT, *ContosoTestHub*.

3. Clique em **Adicionar alerta métrico (clássico)** para configurar um novo alerta.

    Preencha os campos:

    **Nome**: Forneça um nome para a sua regra de alerta, como *mensagens de telemetria*.

    **Descrição**: Forneça uma descrição do seu alerta, como alerta *quando há 1000 mensagens de telemetria enviadas*. 

    **Fonte**: Desloque isto para *métricas*.

    **Subscrição,** **Grupo de Recursos**e **Recursos** devem ser definidos para os valores selecionados no ecrã de **alertas clássicos View.** 

    Definir As mensagens **Métricas** para *a Telemetria enviadas.*

    ![Screenshot mostrando configurar um alerta clássico para mensagens de telemetria enviadas.](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. Após o gráfico, desloque os seguintes campos:

   **Condição**: Definir para *Maior do que*.

   **Limiar**: Definido para 1000.

   **Período**: Definir para *Os últimos 5 minutos*.

   **Destinatários do email**de notificação : Coloque aqui o seu endereço de e-mail. 

   ![Screenshot mostrando metade inferior do ecrã de alertas.](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   Clique **em OK** para guardar o alerta. 

5. Agora, configurar outro alerta para o *número total de mensagens utilizadas*. Esta métrica é útil se quiser enviar um alerta quando o número de mensagens usadas se aproxima da quota para o hub IoT -- para que saiba que o hub em breve começará a rejeitar mensagens.

   No ecrã de **alertas clássicos Ver,** clique em **Adicionar alerta métrico (clássico)** e, em seguida, preencha estes campos no painel de **regra Adicionar.**

   **Nome**: Forneça um nome para a sua regra de alerta, como o *número de mensagens utilizados*.

   **Descrição**: Forneça uma descrição do seu alerta, como *alerta quando se aproximar da quota*.

   **Fonte**: Desloque este campo para *métricas*.

    **Subscrição,** **Grupo de Recursos**e **Recursos** devem ser definidos para os valores selecionados no ecrã de **alertas clássicos View.** 

    Definir **Métrica** para *o número total de mensagens utilizadas*.

6. Sob a tabela, preencha os seguintes campos:

   **Condição**: Definir para *Maior do que*.

   **Limiar**: Definido para 1000.

   **Período**: Desloque este campo para *os últimos 5 minutos*. 

   **Destinatários do email**de notificação : Coloque aqui o seu endereço de e-mail. 

   Clique em **OK** para guardar a regra. 

5. Deve agora ver dois alertas no painel de alertas clássicos: 

   ![Screenshot mostrando ecrã de alertas clássicos com as novas regras de alerta.](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. Feche o painel de alertas. 
    
    Com estas definições, receberá um alerta quando o número de mensagens enviadas for superior a 400 e quando o número total de mensagens utilizadas exceder o NÚMERO.

## <a name="run-simulated-device-app"></a>Executara aplicação de Dispositivo Simulada

Anteriormente na secção de configuração de scripts, configurou um dispositivo para simular a utilização de um dispositivo IoT. Nesta secção, vai transferir uma aplicação de consola do .NET para simular um dispositivo que envia mensagens de dispositivo para cloud a um Hub IoT.  

Transfira a solução para a [Simulação de Dispositivos IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Este link descarrega um repo com várias aplicações no mesmo; a solução que procura está no iot-hub/Tutorials/Routing/.

Faça duplo clique no ficheiro da solução (SimulatedDevice.sln) para abrir o código no Visual Studio e, em seguida, abra Program.cs. Substitua `{iot hub hostname}` pelo nome do anfitrião do hub IoT. O formato do nome do anfitrião do hub IoT é **{iot-hub-name}.azure-devices.net**. Para este tutorial, o nome do anfitrião do hub é **ContosoTestHub.azure-devices.net**. Em seguida, substitua `{device key}` pela chave de dispositivo que guardou anteriormente quando configurou o dispositivo simulado. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Executar e testar 

Em Program.cs, `Task.Delay` mude o de 1000 para 10, o que reduz o tempo entre o envio de mensagens de 1 segundo para 0,01 segundos. Encurtar este atraso aumenta o número de mensagens enviadas.

```csharp
await Task.Delay(10);
```

Execute a aplicação de consola. Aguarde alguns minutos (10-15). Pode ver as mensagens enviadas do dispositivo simulado para o centro no ecrã da consola da aplicação.

### <a name="see-the-metrics-in-the-portal"></a>Veja as métricas no portal

Abra as métricas do Painel de Instrumentos. Altere os valores de tempo para *30 minutos com* uma granularidade de *1 minuto*. Mostra as mensagens de telemetria enviadas e o número total de mensagens usadas na tabela, com os números mais recentes na parte inferior do gráfico.

   ![Screenshot mostrando as métricas.](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>Veja os alertas

Volte para os alertas. Clique em **grupos de recursos,** selecione *ContosoResources,* em seguida, selecione o hub *ContosoTestHub*. Na página de propriedades apresentada para o hub, selecione **Alertas,** em **seguida, ver alertas clássicos**. 

Quando o número de mensagens enviadas excede o limite, começa a receber alertas de e-mail. Para ver se existem alertas ativos, vá ao seu centro e selecione **Alertas**. Mostrar-lhe-á os alertas que estão ativos, e se houver algum aviso. 

   ![Imagens que mostram os alertas dispararam.](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

Clique no alerta para mensagens de telemetria. Mostra o resultado métrico e um gráfico com os resultados. Além disso, o e-mail enviado para avisá-lo do alerta de disparo parece com esta imagem:

   ![A imagem do e-mail mostrando os alertas disparou.](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>Consulte os registos de diagnóstico

Configuraos os seus registos de diagnóstico para serem exportados para o armazenamento de bolhas. Vá ao seu grupo de recursos e selecione a sua conta de armazenamento *contosostoragemon*. Selecione Blobs e, em seguida, abra as *ligações de insights-logs do*recipiente . Faça a broca até chegar à data atual e selecione o ficheiro mais recente. 

   ![Screenshot de perfuração no recipiente de armazenamento para ver os registos de diagnóstico.](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

Clique em **Baixar** para descarregá-lo e abra-o. Vê os registos do dispositivo a ligar e a desligar-se à medida que envia mensagens para o centro. Aqui uma amostra:

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

Para remover todos os recursos que criou neste tutorial, elimine o grupo de recursos. Esta ação também elimina todos os recursos contidos no grupo. Neste caso, remove o centro ioT, a conta de armazenamento e o próprio grupo de recursos. Se tiver as métricas fixadas no painel de instrumentos, terá de as remover manualmente clicando nos três pontos no canto superior direito de cada um e selecionando **Remover**.

Para remover o grupo de recursos, utilize o comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar métricas e registos de diagnóstico executando as seguintes tarefas:

> [!div class="checklist"]
> * Utilizando o Azure CLI, crie um hub IoT, um dispositivo simulado e uma conta de armazenamento.  
> * Ativar os registos de diagnóstico. 
> * Ativar métricas.
> * Estabeleça alertas para as métricas. 
> * Descarregue e execute uma aplicação que simula um dispositivo IoT enviando mensagens para o centro. 
> * Executar a aplicação até que os alertas comecem a disparar. 
> * Veja os resultados das métricas e verifique os registos de diagnóstico. 

Avance para o tutorial seguinte para aprender a gerir o estado de um dispositivo IoT. 

> [!div class="nextstepaction"]
> [Configure os seus dispositivos a partir de um serviço de back-end](tutorial-device-twins.md)
