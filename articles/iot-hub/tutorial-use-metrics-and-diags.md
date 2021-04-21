---
title: Tutorial - Configurar e utilizar métricas e troncos com um hub Azure IoT
description: Tutorial - Aprenda a configurar e a utilizar métricas e troncos com um hub Azure IoT. Isto fornecerá dados para analisar para ajudar a diagnosticar problemas que o seu hub pode estar a ter.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: robinsh
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
- devx-track-csharp
ms.openlocfilehash: 099b7f4e812e92503c7ed8e3eb733f2e49ccd8b9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768076"
---
# <a name="tutorial-set-up-and-use-metrics-and-logs-with-an-iot-hub"></a>Tutorial: Configurar e usar métricas e troncos com um hub IoT

Pode utilizar o Azure Monitor para recolher métricas e registos para o seu hub IoT que o podem ajudar a monitorizar o funcionamento da sua solução e problemas de resolução de problemas quando ocorrem. Neste artigo, você verá como criar gráficos com base em métricas, como criar alertas que desencadeiem métricas, como enviar operações e erros do IoT Hub para registos do Monitor Azure, e como verificar os registos de erros.

Este tutorial utiliza a amostra Azure do [.NET Enviar o quickstart da telemetria](quickstart-send-telemetry-dotnet.md) para enviar mensagens para o hub IoT. Pode sempre utilizar um dispositivo ou outra amostra para enviar mensagens, mas poderá ter de modificar alguns passos em conformidade.

Alguma familiaridade com os conceitos do Azure Monitor pode ser útil antes de iniciar este tutorial. Para saber mais, consulte [o Monitor IoT Hub.](monitor-iot-hub.md) Para saber mais sobre as métricas e registos de recursos emitidos pelo IoT Hub, consulte [a referência de dados de monitorização](monitor-iot-hub-reference.md).

Neste tutorial, vai realizar as seguintes tarefas:

> [!div class="checklist"]
>
> * Utilize o CLI Azure para criar um hub IoT, registe um dispositivo simulado e crie um espaço de trabalho Log Analytics.  
> * Enviar ligações IoT Hub e registos de recursos de telemetria de dispositivo para registos de monitores Azure no espaço de trabalho Do Log Analytics.
> * Utilize o explorador métrico para criar um gráfico baseado em métricas selecionadas e fixá-lo no seu painel de instrumentos.
> * Crie alertas métricos para que possa ser notificado por e-mail quando ocorrem condições importantes.
> * Descarregue e execute uma aplicação que simula um dispositivo IoT enviando mensagens para o hub IoT.
> * Consulte os alertas quando as suas condições ocorrerem.
> * Veja o gráfico de métricas no seu painel de instrumentos.
> * Ver erros e operações do Hub IoT em Registos monitores Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Precisa do .NET Core SDK 2.1 ou superior na sua máquina de desenvolvimento. Pode transferir o SDK de .NET Core para múltiplas plataformas em [.NET](https://www.microsoft.com/net/download/all).

  Pode verificar qual a versão atual do C# no seu computador de desenvolvimento através do seguinte comando:

  ```cmd/sh
  dotnet --version
  ```

- Uma conta de e-mail capaz de receber correio.

- Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste tutorial utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="set-up-resources"></a>Configurar recursos

Para este tutorial, você precisa de um hub IoT, um espaço de trabalho Log Analytics e um dispositivo IoT simulado. Pode criar estes recursos com a CLI do Azure ou o Azure PowerShell. Utilize o mesmo grupo de recursos e a mesma localização para todos os recursos. Depois, quando terminar o tutorial, pode remover tudo num só passo, eliminando o grupo de recursos.

Aqui estão os passos necessários.

1. Criar um [grupo de recursos.](../azure-resource-manager/management/overview.md)

2. Criar um hub IoT.

3. Criar uma área de trabalho do Log Analytics.

4. Registe uma identidade do dispositivo para o dispositivo simulado que envia mensagens para o seu hub IoT. Guarde a cadeia de ligação do dispositivo para configurar o dispositivo simulado.

### <a name="set-up-resources-using-azure-cli"></a>Criar recursos utilizando o Azure CLI

Copie e cole este script no Cloud Shell. Partindo do princípio de que já tem sessão iniciada, o script é executado uma linha de cada vez. Alguns dos comandos podem levar algum tempo a executar. Os novos recursos são criados no grupo de recursos *ContosoResources.*

O nome de alguns recursos deve ser único em todo o Azure. O script gera um valor aleatório com a `$RANDOM` função e armazena-o numa variável. Para estes recursos, o script anexa este valor aleatório a um nome base para o recurso, tornando o nome do recurso único.

Apenas um hub IoT gratuito é permitido por subscrição. Se já tiver um hub IoT gratuito na sua subscrição, ou o elimine antes de executar o script ou modifique o script para utilizar o seu hub IoT gratuito ou um Hub IoT que utilize o nível padrão ou básico.

O script imprime o nome do hub IoT, o nome do espaço de trabalho Log Analytics e a cadeia de ligação para o dispositivo que regista. Certifique-se de anotar estes como você vai precisar deles mais tarde neste artigo.

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
randomValue=$RANDOM

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier. Partition count must be 2.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --partition-count 2 \
    --sku F1 --location $location

# The Log Analytics workspace name must be globally unique, so add a random number to the end.
workspaceName=contoso-la-workspace$randomValue
echo "Log Analytics workspace name = " $workspaceName


# Create the Log Analytics workspace
az monitor log-analytics workspace create --resource-group $resourceGroup \
    --workspace-name $workspaceName --location $location

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the primary connection string for the device identity, then copy it to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show-connection-string --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>Ao criar a identidade do dispositivo, poderá obter o seguinte erro: *Não são encontradas chaves para o iothubowner de ioT Hub ContosoTestHub*. Para corrigir este erro, atualize a Extensão IoT Azure CLI e, em seguida, volte a executar os dois últimos comandos no script. 
>
>Aqui está o comando para atualizar a extensão. Executar este comando na sua instância Cloud Shell.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="collect-logs-for-connections-and-device-telemetry"></a>Recolher registos para ligações e telemetria do dispositivo

O IoT Hub emite registos de recursos para várias categorias de funcionamento; no entanto, para que possa ver estes registos, tem de criar uma definição de diagnóstico para os enviar para um destino. Um desses destinos é o Azure Monitor Logs, que são recolhidos num espaço de trabalho log analytics. Os registos de recursos do IoT Hub são agrupados em diferentes categorias. Pode selecionar quais as categorias que deseja enviadas para os Registos do Monitor Azure na definição de diagnóstico. Neste artigo, recolheremos registos para operações e erros que ocorram tendo a ver com ligações e telemetria do dispositivo. Para obter uma lista completa das categorias suportadas para o IoT Hub, consulte [os registos de recursos do IoT Hub](monitor-iot-hub-reference.md#resource-logs).

Para criar uma definição de diagnóstico para enviar registos de recursos do IoT Hub para registos do Monitor Azure, siga estes passos:

1. Em primeiro lugar, se ainda não estiver no seu hub no portal, selecione **grupos de Recursos** e selecione o grupo de recursos ContosoResources. Selecione o seu hub IoT na lista de recursos apresentados.

1. Procure a secção **de monitorização** na lâmina do hub IoT. Selecione **definições de diagnóstico**. Em seguida, **selecione Adicionar a definição de diagnóstico**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/open-diagnostic-settings.png" alt-text="Screenshot que realça as definições de Diagnóstico na secção de Monitorização.":::

1. No painel **de definição** de Diagnóstico, dê à definição um nome descritivo, como "Enviar ligações e telemetria para registos".

1. Em **detalhes de categoria**, selecione **Connections** and **Device Telemetry**.

1. Em **Detalhes do Destino**, selecione Enviar para Registar **Analytics,** em seguida, use o picker do espaço de trabalho Log Analytics para selecionar o espaço de trabalho que anota anteriormente. Quando terminar, a definição de diagnóstico deve ser semelhante à seguinte imagem:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/add-diagnostic-setting.png" alt-text="Screenshot mostrando as definições finais de registo de diagnóstico.":::

1. Selecione **Guardar** para guardar as definições. Feche o painel **de definição** de diagnóstico. Pode ver a sua nova definição na lista de definições de diagnóstico.

## <a name="set-up-metrics"></a>Configurar métricas

Agora vamos usar o explorador de métricas para criar um gráfico que exibe métricas que quer rastrear. Irá fixar este gráfico no seu painel de instrumentos padrão no portal Azure.

1. No painel esquerdo do seu hub IoT, selecione **Métricas** na secção **de Monitorização.**

1. Na parte superior do ecrã, selecione **Últimas 24 horas (Automática)**. No dropdown que aparece, selecione **Last 4 horas** para **intervalo de tempo**, deslovide a **granularidade do tempo** para **1 minuto**, e selecione **Local** para show **time como**. **Selecione Aplicar** para guardar estas definições. A definição deve agora dizer **Hora Local: Últimas 4 horas (1 minuto)**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-select-time-range.png" alt-text="Screenshot mostrando as definições de tempo das métricas.":::

1. Na tabela, há uma definição métrica parcial exibida no seu hub IoT. Deixe os valores **de Espaço e** Nome **métrico** à sua padrão. Selecione a **definição métrica** e escreva "Telemetria", selecione as mensagens de **telemetria enviadas** a partir do dropdown. **A agregação** será automaticamente definida para **Sum**. Note que o título da sua carta também muda.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-telemetry-messages-sent.png" alt-text="Screenshot que mostra a adição de mensagens de telemetria enviadas métricas para gráfico.":::

1. Agora **selecione Adicione métrica** para adicionar outra métrica ao gráfico. Em **Métrica,** selecione **número total de mensagens utilizadas.** **A agregação** será automaticamente definida para **Avg**. Note mais uma vez que o título da tabela mudou para incluir esta métrica.

   Agora, o seu ecrã mostra a métrica minimizada das *mensagens de telemetria enviadas*, mais a nova métrica para o *número total de mensagens utilizadas*.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used.png" alt-text="Screenshot que mostra a adição de número total de mensagens usadas métricas para cartografar.":::

1. No canto superior direito da tabela, selecione Pin para o painel de **instrumentos**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used-pin.png" alt-text="Screenshot que realça o botão Pin para painel de instrumentos.":::

1. No **pino para painel de instrumentos,** selecione o **separador Existente.** Selecione **Private** e, em seguida, selecione **Dashboard** a partir do dropdown do dashboard. Por fim, selecione **Pin** para fixar o gráfico no seu painel de instrumentos padrão no portal Azure. Se não fixar a sua ficha num painel de instrumentos, as suas definições não são mantidas quando sai do explorador métrico.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/pin-to-dashboard.png" alt-text="Screenshot que mostra definições para Pin para painel de instrumentos.":::

## <a name="set-up-metric-alerts"></a>Configurar alertas métricos

Agora vamos configurar alertas para desencadear duas métricas *de telemetria enviadas* e *número total de mensagens usadas.*

*As mensagens de telemetria enviadas* são uma boa métrica para monitorizar para rastrear a produção de mensagens e evitar ser estrangulada. Para um Hub IoT no nível livre, o limite de estrangulamento é de 100 mensagens/seg. Com um único dispositivo, não conseguiremos alcançar este tipo de produção, então, em vez disso, vamos configurar o alerta para ativar se o número de mensagens exceder 1000 num período de 5 minutos. Na produção, pode definir o sinal para um valor mais significativo com base no nível, edição e número de unidades do seu hub IoT.

*O número total de mensagens utilizadas* rastreia o número diário de mensagens utilizadas. Esta métrica repõe todos os dias às 00:00 UTC. Se exceder a sua quota diária para além de um determinado limiar, o seu Hub IoT deixará de aceitar mensagens. Para um Hub IoT no nível livre, a quota diária de mensagem é de 8000. Vamos configurar o alerta para desencadear se o número total de mensagens exceder 4000, 50% da quota. Na prática, provavelmente definiria esta percentagem para um valor mais elevado. O valor de quota diária depende do nível, edição e número de unidades do seu hub IoT.

Para obter mais informações sobre os limites de quota e de estrangulamento com o IoT Hub, consulte [quotas e estrangulamentos.](iot-hub-devguide-quotas-throttling.md)

Para configurar alertas métricos:

1. Vá ao seu hub IoT no portal Azure.

1. Em **Monitorização**, **selecione Alertas**. Em seguida, selecione **Nova regra de alerta**.  O painel de regras de **alerta de criação** abre-se.

    :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-pane.png" alt-text="Screenshot mostrando o painel de regras de alerta Criar.":::

    No painel de regras de **alerta Criar,** existem quatro secções:

    * **O âmbito** já está definido para o seu hub IoT, por isso vamos deixar esta secção em paz.
    * **A condição** define o sinal e as condições que irão desencadear o alerta.
    * **As ações** configuram o que acontece quando o alerta dispara.
    * **Os detalhes da regra de alerta** permitem definir um nome e uma descrição para o alerta.

1. Primeiro, configure a condição de que o alerta dispare.

    1. Em **Condição**, selecione **Adicionar condição**. No painel de lógica de **sinal configurado,** escreva "telemetria" na caixa de pesquisa e selecione **mensagens de telemetria enviadas**.

       :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-telemetry-messages-sent.png" alt-text="Screenshot mostrando a seleção da métrica.":::

    1. No painel de lógica de **sinal de configuração,** desfaça ou confirme os seguintes campos na **lógica de Alerta** (pode ignorar o gráfico):

       **Limiar**:  *Estático*.

       **Operador**: *Maior que*.

       **Tipo de agregação**: *Total*.

       **Valor limiar:** 1000.

       **Granularidade agregada (Período)**: *5 minutos*.

       **Frequência de avaliação**: *A cada 1 minuto*

        :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-set-conditions.png" alt-text="Screenshot mostrando as definições de condições de alerta.":::

       Estas definições definem o sinal para o número total de mensagens durante um período de 5 minutos. Este total será avaliado a cada minuto e, se o total dos 5 minutos anteriores exceder 1000 mensagens, o alerta será acionado.

       Selecione **Feito** para salvar a lógica do sinal.

1. Agora,configem a ação para o alerta.

    1. Voltar ao painel **de regras de alerta Criar,** em **Ações**, selecione **Adicionar grupos de ação**. No **Selecione um grupo de ação para anexar a este** painel de regras de alerta, selecione **Criar grupo de ação**.

    1. No **separador Básicos** no painel de **grupo de ação Create,** dê ao seu grupo de ação um nome e um nome de exibição.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-basics.png" alt-text="Screenshot mostrando o separador Básico do painel de grupo de ação Create.":::

    1. Selecione o **separador Notificações.** Para **o tipo de notificação,** selecione **mensagem de e-mail/SMS/Push/Voice** a partir do dropdown. Abre-se a **mensagem de E-mail/SMS/Painel push/Voice.**

    1. No **painel de mensagens de e-mail/SMS/Push/Voice,** selecione e-mail e introduza o seu endereço de e-mail e, em seguida, selecione **OK**.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/set-email-address.png" alt-text="Screenshot mostrando a definição do endereço de e-mail.":::

    1. De volta ao painel **de notificações, insira** um nome para a notificação.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-notification-complete.png" alt-text="Screenshot mostrando painel de notificações completas.":::

    1. (Opcional) Se selecionar o separador **Ações** e, em seguida, selecionar o dropdown **do tipo Action,** pode ver os tipos de ações que pode desencadear com um alerta. Para este artigo, apenas usaremos notificações, para que possa ignorar as definições deste separador.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/action-types.png" alt-text="Screenshot mostrando tipos de ação disponíveis no painel de Ações.":::

    1. Selecione o separador **'Rever e Criar',** verifique as definições e selecione **Criar**.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-review-and-create.png" alt-text="Screenshot mostrando Rever e criar painel.":::

    1. De volta ao painel **de regras de alerta Create,** note que o seu novo grupo de ação foi adicionado às ações para o alerta.  

1. Por fim, configuure os detalhes da regra de alerta e guarde a regra de alerta.

    1. No painel **de regras de alerta Create,** em detalhes da regra de alerta, insira um nome e uma descrição para o seu alerta; por exemplo, "Alerta se mais de 1000 mensagens em 5 minutos". Certifique-se de que **a regra de alerta ativa após a criação** é verificada. A sua regra de alerta completa será semelhante a esta imagem.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-final.png" alt-text="Screenshot mostrando concluído Criar painel de regras de alerta.":::

    1. **Selecione Criar a regra de alerta** para salvar a sua nova regra.

1. Agora, crie outro alerta para o *número total de mensagens utilizadas*. Esta métrica é útil se quiser enviar um alerta quando o número de mensagens usadas se aproxima da quota diária para o hub IoT, altura em que o hub IoT começará a rejeitar mensagens. Siga os passos que já fez antes, com as seguintes diferenças.

    * Para o sinal no painel de lógica de **sinal de configuração,** selecione **Número total de mensagens utilizadas**.

    * No painel de lógica de **sinal de configuração,** desafie ou confirme os seguintes campos (pode ignorar o gráfico):

       **Limiar**:  *Estático*.

       **Operador**: *Maior que*.

       **Tipo de agregação**: *Máximo*.

       **Valor limiar:** 4000.

       **Granularidade agregada (Período)**: *1 minuto*.

       **Frequência de avaliação**: *A cada 1 minuto*

       Estas definições indicam que o sinal dispara quando o número de mensagens atinge 4000. A métrica é avaliada a cada minuto.

    * Quando especificar a ação para a sua regra de alerta, basta selecionar o grupo de ação que criou anteriormente.

    * Para os detalhes de alerta, escolha um nome e descrição diferentes do que fez anteriormente.

1. Selecione **Alertas**, em **Monitorização** no painel esquerdo do seu hub IoT. **Selecione Gerir as regras** de alerta no menu no topo do painel **de alertas.** O **painel do Regimento** abre. Devem agora ver os vossos dois alertas:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/rules-management.png" alt-text="Screenshot mostrando o painel de regras com as novas regras de alerta.":::

1. Feche o **painel de regras.**

Com estas definições, um alerta será desencadeado e receberá uma notificação por e-mail quando mais de 1000 mensagens forem enviadas dentro de um período de 5 minutos e também quando o número total de mensagens usadas exceder 4000 (50% da quota diária para um hub IoT no nível livre).

## <a name="run-the-simulated-device-app"></a>Executar a aplicação de dispositivo simulado

Na secção [De Recursos Configurar,](#set-up-resources) registou uma identidade do dispositivo para simular usando um dispositivo IoT. Nesta secção, descarrega uma aplicação de consola .NET que simula um dispositivo que envia mensagens dispositivo-a-nuvem para um Hub IoT, configura-as para enviar estas mensagens para o seu hub IoT e, em seguida, executá-las.

> [!IMPORTANT]
>
> Os alertas podem demorar até 10 minutos a ser totalmente configurados e ativados pelo IoT Hub. Aguarde pelo menos 10 minutos entre o momento em que configurar o seu último alerta e executar a aplicação do dispositivo simulado.

Transfira a solução para a [Simulação de Dispositivos IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Este link descarrega um repo com várias aplicações nele; o que procura está em iot-hub/Quickstarts/dispositivo simulado/.

1. Numa janela de terminal local, navegue para a pasta raiz da solução. Em seguida, navegue para a pasta **iot-hub\Quickstarts\simulated-device**.

1. Abra o ficheiro **SimulatedDevice.cs** num editor de texto à sua escolha.

    1. Substitua o valor da `s_connectionString` variável pela cadeia de ligação do dispositivo que notou quando executou o script para configurar recursos.

    1. No `SendDeviceToCloudMessagesAsync` método, altere o `Task.Delay` de 1000 para 1, o que reduz o tempo entre enviar mensagens de 1 segundo para 0,001 segundos. Encurtar este atraso aumenta o número de mensagens enviadas. (Provavelmente não receberá uma taxa de mensagem de 100 mensagens por segundo.)

        ```csharp
        await Task.Delay(1);
        ```

    1. Guarde as suas alterações para **SimulatedDevice.cs**.

1. Na janela do terminal local, executar o seguinte comando para instalar as embalagens necessárias para a aplicação do dispositivo simulado:

    ```cmd/sh
    dotnet restore
    ```

1. Na janela de terminal local, execute o seguinte comando para compilar e executar a aplicação de dispositivo simulado:

    ```cmd/sh
    dotnet run
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação de dispositivo simulado envia telemetria para o seu hub IoT:

    :::image type="content" source="media/tutorial-use-metrics-and-diags/simulated-device-output.png" alt-text="Screenshot mostrando saída simulada do dispositivo.":::

Deixe a inscrição funcionar durante pelo menos 10-15 minutos. Idealmente, deixe-o funcionar até parar de enviar mensagens (cerca de 20-30 minutos). Isto acontecerá quando exceder a quota de mensagem diária para o seu hub IoT, e deixou de aceitar mais mensagens.

> [!NOTE]
> Se deixar a aplicação do dispositivo em funcionamento por um período prolongado após deixar de enviar mensagens, poderá obter uma exceção. Pode ignorar com segurança esta exceção e fechar a janela da aplicação.

## <a name="view-metrics-chart-on-your-dashboard"></a>Veja o gráfico de métricas no seu painel de instrumentos

1. No canto superior esquerdo do portal Azure, abra o menu do portal e, em seguida, selecione **Dashboard**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/select-dashboard.png" alt-text="Screenshot como selecionar o seu painel de instrumentos.":::

1. Encontre o gráfico que fixou anteriormente e clique em qualquer lugar no azulejo fora dos dados do gráfico para expandi-lo. Mostra as mensagens de telemetria enviadas e o número total de mensagens utilizadas na tabela. Os números mais recentes aparecem na parte inferior da tabela. Pode mover o cursor na tabela para ver os valores métricos para tempos específicos. Também pode alterar o valor do tempo e a granularidade no topo da tabela para reduzir ou expandir os dados para um período de tempo de interesse.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-on-dashboard-last-hour.png" alt-text="Screenshot mostrando o gráfico de métricas.":::

   Neste cenário, a produção de mensagens simuladas não é suficientemente grande para fazer com que o IoT Hub atrase as suas mensagens. Num cenário que envolve realmente estrangulamento, pode ver mensagens de telemetria enviadas exceder o limite de aceleração para o seu hub IoT por um tempo limitado. Isto é para acomodar o tráfego rebentado. Para mais detalhes, consulte [a formação do tráfego.](iot-hub-devguide-quotas-throttling.md#traffic-shaping)

## <a name="view-the-alerts"></a>Ver os alertas

Quando o número de mensagens enviadas exceder os limites definidos nas suas regras de alerta, começa a receber alertas de e-mail.

Para ver se existem alertas ativos, selecione **Alertas** em **Monitorização** no painel esquerdo do seu hub IoT. O **painel de alertas** mostra o número de alertas que dispararam por gravidade para o intervalo de tempo especificado.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-alerts.png" alt-text="Screenshot mostrando o resumo dos alertas.":::

Selecione a linha para severidade Sev 3. O painel **All Alerts** abre e lista os alertas Sev 3 que dispararam.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-all-alerts.png" alt-text="Screenshot mostrando o painel all alerts.":::

Selecione um dos alertas para ver os detalhes do alerta.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-individual-alert.png" alt-text="Screenshot mostrando detalhes de alerta.":::

Verifique a sua caixa de entrada para obter e-mails da Microsoft Azure. A linha do assunto descreverá o alerta que foi desencadeado. Por exemplo, *Azure: Severidade Ativada: 3 Alerta se mais de 1000 mensagens em 5 minutos*. O corpo será semelhante à seguinte imagem:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/alert-mail.png" alt-text="A imagem do e-mail mostrando os alertas disparou.":::

## <a name="view-azure-monitor-logs"></a>Ver Registos do Monitor Azure

Na [secção Recolha de ligações e telemetria do dispositivo,](#collect-logs-for-connections-and-device-telemetry) criou uma definição de diagnóstico para enviar registos de recursos emitidos pelo seu hub IoT para operações de ligação e telemetria de dispositivos a Registos monitores Azure. Nesta secção, você executará uma consulta kusto contra registos do monitor Azure para observar quaisquer erros que ocorreram.

1. Em **Monitorização** no painel esquerdo do seu hub IoT no portal Azure, selecione **Logs**. Feche a janela **inicial de Consultas** se abrir.

1. No painel new's, selecione o separador **Consultas** e, em seguida, expanda o **IoT Hub** para ver a lista de consultas padrão.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/new-query-pane.png" alt-text="Screenshot de consultas padrão IoT Hub.":::

1. Selecione a consulta *de resumo de erro.* A consulta aparece no painel de editores de Consulta. Selecione **Executar** no painel de editores e observar os resultados da consulta. Expanda uma das linhas para ver detalhes.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/logs-errors.png" alt-text="Screenshot dos registos devolvidos pela consulta sumária de Erros.":::

   > [!NOTE]
   > Se não vir erros, tente executar a consulta *de dispositivos recentemente ligados.* Isto deve voltar a ser retornado para o dispositivo simulado.

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover todos os recursos que criou neste tutorial, elimine o grupo de recursos. Esta ação também elimina todos os recursos contidos no grupo. Neste caso, remove o hub IoT, o espaço de trabalho Log Analytics e o próprio grupo de recursos. Se tiver fixado gráficos de métricas no painel de instrumentos, terá de removê-los manualmente clicando nos três pontos no canto superior direito de cada gráfico e selecionando **Remover**. Certifique-se de que guarda as suas alterações depois de apagar as tabelas.

Para remover o grupo de recursos, utilize o comando [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name ContosoResources
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar métricas e registos IoT Hub realizando as seguintes tarefas:

> [!div class="checklist"]
>
> * Utilize o CLI Azure para criar um hub IoT, registe um dispositivo simulado e crie um espaço de trabalho Log Analytics.  
> * Enviar ligações IoT Hub e registos de recursos de telemetria de dispositivo para registos de monitores Azure no espaço de trabalho Do Log Analytics.
> * Utilize o explorador métrico para criar um gráfico baseado em métricas selecionadas e fixá-lo no seu painel de instrumentos.
> * Crie alertas métricos para que possa ser notificado por e-mail quando ocorrem condições importantes.
> * Descarregue e execute uma aplicação que simula um dispositivo IoT enviando mensagens para o hub IoT.
> * Consulte os alertas quando as suas condições ocorrerem.
> * Veja o gráfico de métricas no seu painel de instrumentos.
> * Ver erros e operações do Hub IoT em Registos monitores Azure.

Avance para o tutorial seguinte para aprender a gerir o estado de um dispositivo IoT. 

> [!div class="nextstepaction"]
> [Configure os seus dispositivos a partir de um serviço de back-end](tutorial-device-twins.md)