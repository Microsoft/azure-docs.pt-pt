---
title: Configurar e usar logs de diagnóstico e métricas com um hub IoT do Azure
description: Saiba como configurar e usar logs de diagnóstico e métricas com um hub IoT do Azure. Isso fornecerá dados a serem analisados para ajudar a diagnosticar problemas que o Hub pode ter.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 3/13/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 88101aacab8b4745ce6bc9180521e66500086edd
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084368"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>Tutorial: configurar e usar logs de diagnóstico e métricas com um hub IoT

Se você tiver uma solução de Hub IoT em execução na produção, você deseja configurar algumas métricas e habilitar os logs de diagnóstico. Em seguida, se ocorrer um problema, você terá dados a serem examinados para ajudá-lo a diagnosticar o problema e corrigi-lo mais rapidamente. Neste artigo, você verá como habilitar os logs de diagnóstico e como verificá-los quanto a erros. Você também configurará algumas métricas para observar e alertas que são acionados quando as métricas atingem um determinado limite. Por exemplo, você pode ter um email enviado a você quando o número de mensagens de telemetria enviadas exceder um limite específico ou quando o número de mensagens usadas chegar perto da cota de mensagens permitidas por dia para o Hub IoT. 

Um exemplo de caso de uso é uma estação de gás em que as bombas são dispositivos IoT que enviam comunicação com um hub IoT. Os cartões de crédito são validados e a transação final é gravada em um repositório de dados. Se os dispositivos IoT interromperem a conexão com o Hub e enviarem mensagens, será muito mais difícil de corrigir se você não tiver visibilidade do que está acontecendo.

Este tutorial usa o exemplo do Azure do [Roteamento do Hub IOT](tutorial-routing.md) para enviar mensagens ao Hub IOT.

Neste tutorial, vai realizar as seguintes tarefas:

> [!div class="checklist"]
> * Usando CLI do Azure, crie um hub IoT, um dispositivo simulado e uma conta de armazenamento.  
> * Habilite os logs de diagnóstico.
> * Habilitar métricas.
> * Configure alertas para essas métricas. 
> * Baixe e execute um aplicativo que simule um dispositivo IoT enviando mensagens para o Hub. 
> * Execute o aplicativo até que os alertas comecem a ser acionados. 
> * Exiba os resultados de métricas e verifique os logs de diagnóstico. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Instale o [Visual Studio](https://www.visualstudio.com/). 

- Uma conta de email capaz de receber emails.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Configurar os recursos

Para este tutorial, você precisa de um hub IoT, uma conta de armazenamento e um dispositivo IoT simulado. Pode criar estes recursos com a CLI do Azure ou o Azure PowerShell. Utilize o mesmo grupo de recursos e a mesma localização para todos os recursos. Em seguida, no final, pode remover tudo num único passo ao eliminar o grupo de recursos.

Essas são as etapas necessárias.

1. Crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md). 

2. Crie um hub IoT.

3. Crie uma conta de armazenamento standard V1 com replicação Standard_LRS.

4. Crie uma identidade para o dispositivo simulado que envia mensagens para o hub. Guarde a chave para a fase de teste.

### <a name="set-up-resources-using-azure-cli"></a>Configurar recursos usando CLI do Azure

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
>Ao criar a identidade do dispositivo, você pode receber o seguinte erro: *nenhuma chave encontrada para a política iothubowner do Hub IOT ContosoTestHub*. Para corrigir esse erro, atualize a extensão de IoT CLI do Azure e, em seguida, execute os dois últimos comandos no script novamente. 
>
>Aqui está o comando para atualizar a extensão. Execute-o em sua instância de Cloud Shell.
>
>```cli
>az extension update --name azure-cli-iot-ext
>```

## <a name="enable-the-diagnostic-logs"></a>Habilitar os logs de diagnóstico 

[Os logs de diagnóstico](../azure-monitor/platform/resource-logs-overview.md) são desabilitados por padrão quando você cria um novo hub IOT. Nesta seção, habilite os logs de diagnóstico do seu hub.

1. Primeiro, se você ainda não estiver em seu Hub no portal, clique em **grupos de recursos** e clique no grupo de recursos contoso-Resources. Selecione o Hub na lista de recursos exibida. 

2. Procure a seção **monitoramento** na folha do Hub IOT. Clique em **Definições de diagnóstico**. 

   ![Captura de tela mostrando a parte de configurações de diagnóstico da folha do Hub IoT.](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. Verifique se a assinatura e o grupo de recursos estão corretos. Em **tipo de recurso**, desmarque **selecionar tudo**, em seguida, procure e verifique o **Hub IOT**. (Ele coloca a marca de seleção ao lado de *selecionar tudo* novamente, apenas ignorá-la.) Em **recurso**, selecione o nome do Hub. Sua tela deve ser parecida com esta imagem: 

   ![Captura de tela mostrando a parte de configurações de diagnóstico da folha do Hub IoT.](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. Agora, clique em **Ativar diagnóstico**. O painel configurações de diagnóstico é exibido. Especifique o nome das configurações dos logs de diagnóstico como "Diags-Hub".

5. Verifique **o arquivo morto em uma conta de armazenamento**. 

   ![Captura de tela mostrando a configuração do diagnóstico para arquivar em uma conta de armazenamento.](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    Clique em **Configurar** para ver a tela **selecionar uma conta de armazenamento** , selecione a correta (*Contosostoragemon*) e clique em **OK** para retornar ao painel configurações de diagnóstico. 

   ![Captura de tela mostrando a configuração dos logs de diagnóstico para arquivar em uma conta de armazenamento.](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. Em **log**, verifique **conexões** e **telemetria do dispositivo**e defina a **retenção (dias)** como 7 dias para cada. A tela de configurações de diagnóstico agora deve ser parecida com esta imagem:

   ![Captura de tela mostrando as configurações de log de diagnóstico final.](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. Clique em **Guardar** para guardar as definições. Feche o painel configurações de diagnóstico.

Posteriormente, ao examinar os logs de diagnóstico, você poderá ver o log de conexão e desconexão do dispositivo. 

## <a name="set-up-metrics"></a>Configurar métricas 

Agora, configure algumas métricas para observar quando as mensagens são enviadas ao Hub. 

1. No painel configurações do Hub IoT, clique na opção **métricas** na seção **monitoramento** .

2. Na parte superior da tela, clique em **últimas 24 horas (automática)** . Na lista suspensa exibida, selecione **últimas 4 horas** para o **intervalo de tempo**e defina **granularidade de tempo** como **1 minuto**, hora local. Clique em **aplicar** para salvar essas configurações. 

   ![Captura de tela mostrando as configurações de tempo das métricas.](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. Há uma entrada de métrica por padrão. Deixe o grupo de recursos como o padrão e o namespace de métrica. Na lista suspensa **métrica** , selecione **mensagens de telemetria enviadas**. Defina **agregação** como **sum**.

   ![Captura de tela mostrando a adição de uma métrica para mensagens de telemetria enviadas.](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. Agora, clique em **Adicionar métrica** para adicionar outra métrica ao gráfico. Selecione seu grupo de recursos (**ContosoTestHub**). Em **métrica**, selecione o **número total de mensagens usadas**. Para **agregação**, selecione **Méd**. 

   Agora, a tela mostra a métrica minimizada para *mensagens de telemetria enviadas*, além da nova métrica para o *número total de mensagens usadas*.

   ![Captura de tela mostrando a adição de uma métrica para mensagens de telemetria enviadas.](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   Clique em **fixar no painel**. Ele o fixará no painel de seu portal do Azure para que você possa acessá-lo novamente. Se você não fixá-lo no painel, suas configurações não serão mantidas.

## <a name="set-up-alerts"></a>Configurar alertas

Vá para o Hub no Portal. Clique em **grupos de recursos**, selecione *ContosoResources*e, em seguida, selecione *ContosoTestHub*do Hub IOT. 

O Hub IoT ainda não foi migrado para as [métricas no Azure monitor](/azure/azure-monitor/platform/data-collection#metrics) ; Você precisa usar [alertas clássicos](/azure/azure-monitor/platform/alerts-classic.overview).

1. Em **monitoramento**, clique em **alertas** . isso mostra a tela principal de alerta. 

   ![Captura de tela mostrando como encontrar alertas clássicos.](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. Para obter os alertas clássicos aqui, clique em **exibir alertas clássicos**. 

    ![Captura de tela mostrando o alerta de alertas clássicos.](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    Preencha os campos: 

    **Assinatura**: Deixe esse campo definido como sua assinatura atual.

    **Origem**: defina esse campo como *métricas*.

    **Grupo de recursos**: defina esse campo para o grupo de recursos atual, *ContosoResources*. 

    **Tipo de recurso**: defina esse campo como hub IOT. 

    **Recurso**: selecione o Hub IOT, *ContosoTestHub*.

3. Clique em **adicionar alerta de métrica (clássico)** para configurar um novo alerta.

    Preencha os campos:

    **Nome**: forneça um nome para a regra de alerta, como *telemetria-mensagens*.

    **Descrição**: forneça uma descrição do seu alerta, como *alerta quando houver mensagens de telemetria de 1000 enviadas*. 

    **Origem**: Defina isso para *métricas*.

    A **assinatura**, o **grupo de recursos**e o **recurso** devem ser definidos para os valores selecionados na tela **exibir alertas clássicos** . 

    Defina a **métrica** para *as mensagens de telemetria enviadas*.

    ![Captura de tela mostrando a configuração de um alerta clássico para mensagens de telemetria enviadas.](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. Após o gráfico, defina os seguintes campos:

   **Condição**: definida como *maior que*.

   **Limite**: definido como 1000.

   **Período**: defina para *nos últimos 5 minutos*.

   **Destinatários do email de notificação**: Coloque seu endereço de email aqui. 

   ![Captura de tela mostrando a parte inferior do ecrã de alertas.](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   Clique em **OK** para salvar o alerta. 

5. Agora, configure outro alerta para o *número total de mensagens usadas*. Essa métrica será útil se você quiser enviar um alerta quando o número de mensagens usadas estiver se aproximando da cota do Hub IoT – para informar que o Hub começará a rejeitar mensagens em breve.

   Na tela **exibir alertas clássicos** , clique em **adicionar alerta de métrica (clássico)** e preencha Esses campos no painel **Adicionar regra** .

   **Nome**: forneça um nome para a regra de alerta, como *número de mensagens-usado*.

   **Descrição**: forneça uma descrição do seu alerta, como *alertar ao chegar perto da cota*.

   **Origem**: defina esse campo como *métricas*.

    A **assinatura**, o **grupo de recursos**e o **recurso** devem ser definidos para os valores selecionados na tela **exibir alertas clássicos** . 

    Defina a **métrica** para o *número total de mensagens usadas*.

6. No gráfico, preencha os seguintes campos:

   **Condição**: definida como *maior que*.

   **Limite**: definido como 1000.

   **Período**: defina esse campo para *nos últimos 5 minutos*. 

   **Destinatários do email de notificação**: Coloque seu endereço de email aqui. 

   Clique em **OK** para salvar a regra. 

5. Agora você deve ver dois alertas no painel alertas clássicos: 

   ![Captura de tela mostrando telas de alertas clássicos com as novas regras de alerta.](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. Feche o painel alertas. 
    
    Com essas configurações, você receberá um alerta quando o número de mensagens enviadas for maior que 400 e quando o número total de mensagens usadas exceder o número.

## <a name="run-simulated-device-app"></a>Executara aplicação de Dispositivo Simulada

Anteriormente na secção de configuração de scripts, configurou um dispositivo para simular a utilização de um dispositivo IoT. Nesta secção, vai transferir uma aplicação de consola do .NET para simular um dispositivo que envia mensagens de dispositivo para cloud a um Hub IoT.  

Transfira a solução para a [Simulação de Dispositivos IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Este link baixa um repositório com vários aplicativos nele; a solução que você está procurando está em IOT-Hub/tutoriais/roteamento/.

Faça duplo clique no ficheiro da solução (SimulatedDevice.sln) para abrir o código no Visual Studio e, em seguida, abra Program.cs. Substitua `{iot hub hostname}` pelo nome do anfitrião do hub IoT. O formato do nome do anfitrião do hub IoT é **{iot-hub-name}.azure-devices.net**. Para este tutorial, o nome do anfitrião do hub é **ContosoTestHub.azure-devices.net**. Em seguida, substitua `{device key}` pela chave de dispositivo que guardou anteriormente quando configurou o dispositivo simulado. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Executar e testar 

Em Program.cs, altere o `Task.Delay` de 1000 para 10, o que reduz a quantidade de tempo entre o envio de mensagens de 1 segundo a. 01 segundos. A redução desse atraso aumenta o número de mensagens enviadas.

```csharp
await Task.Delay(10);
```

Execute a aplicação de consola. Aguarde alguns minutos (10-15). Você pode ver as mensagens que estão sendo enviadas do dispositivo simulado para o Hub na tela do console do aplicativo.

### <a name="see-the-metrics-in-the-portal"></a>Ver as métricas no portal

Abra suas métricas no painel. Altere os valores de tempo para os *últimos 30 minutos* com uma granularidade de tempo de *1 minuto*. Ele mostra as mensagens de telemetria enviadas e o número total de mensagens usadas no gráfico, com os números mais recentes na parte inferior do gráfico.

   ![Captura de tela mostrando as métricas.](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>Consulte os alertas

Volte para alertas. Clique em **grupos de recursos**, selecione *ContosoResources*e, em seguida, selecione o Hub *ContosoTestHub*. Na página Propriedades exibida para o Hub, selecione **alertas**e, em seguida, **exibir alertas clássicos**. 

Quando o número de mensagens enviadas exceder o limite, você começará a receber alertas por email. Para ver se há alertas ativos, vá para o Hub e selecione **alertas**. Ele mostrará os alertas que estão ativos e, se houver algum aviso. 

   ![Captura de tela mostrando que os alertas foram acionados.](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

Clique no alerta para mensagens de telemetria. Ele mostra o resultado da métrica e um gráfico com os resultados. Além disso, o email enviado para avisá-lo sobre o acionamento do alerta é semelhante a esta imagem:

   ![Captura de tela do email mostrando que os alertas foram acionados.](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>Consulte os logs de diagnóstico

Você configura os logs de diagnóstico a serem exportados para o armazenamento de BLOBs. Vá para o grupo de recursos e selecione sua conta de armazenamento *contosostoragemon*. Selecione BLOBs e abra contêiner *insights-logs-conexões*. Faça uma busca detalhada até chegar à data atual e selecione o arquivo mais recente. 

   ![Captura de tela de busca detalhada no contêiner de armazenamento para ver os logs de diagnóstico.](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

Clique em **baixar** para baixá-lo e abri-lo. Você vê os logs do dispositivo conectando e desconectando à medida que ele envia mensagens para o Hub. Aqui está um exemplo:

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

Para remover todos os recursos que você criou neste tutorial, exclua o grupo de recursos. Esta ação também elimina todos os recursos contidos no grupo. Nesse caso, ele remove o Hub IoT, a conta de armazenamento e o próprio grupo de recursos. Se você tiver fixado as métricas no painel, precisará removê-las manualmente clicando nos três pontos no canto superior direito de cada um e selecionando **remover**.

Para remover o grupo de recursos, utilize o comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a usar as métricas e os logs de diagnóstico executando as seguintes tarefas:

> [!div class="checklist"]
> * Usando CLI do Azure, crie um hub IoT, um dispositivo simulado e uma conta de armazenamento.  
> * Habilite os logs de diagnóstico. 
> * Habilitar métricas.
> * Configure alertas para essas métricas. 
> * Baixe e execute um aplicativo que simule um dispositivo IoT enviando mensagens para o Hub. 
> * Execute o aplicativo até que os alertas comecem a ser acionados. 
> * Exiba os resultados de métricas e verifique os logs de diagnóstico. 

Avance para o tutorial seguinte para aprender a gerir o estado de um dispositivo IoT. 

> [!div class="nextstepaction"]
> [Configure os seus dispositivos a partir de um serviço de back-end](tutorial-device-twins.md)