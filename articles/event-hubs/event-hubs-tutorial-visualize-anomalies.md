---
title: Azure Event Hubs - Visualizar anomalias de dados em eventos em tempo real
description: 'Tutorial: Visualizar anomalias de dados em eventos em tempo real enviados para os Hubs de Eventos da Microsoft Azure'
ms.topic: tutorial
ms.date: 06/23/2020
ms.openlocfilehash: bb7d8da2498005b8b2e1183a836d9385f3d31e5c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783760"
---
# <a name="tutorial-visualize-data-anomalies-in-real-time-events-sent-to-azure-event-hubs"></a>Tutorial: Visualizar anomalias de dados em eventos em tempo real enviados para os Hubs de Eventos do Azure

Com os Hubs de Eventos do Azure, pode utilizar o Azure Stream Analytics para verificar os dados recebidos e extrair as anomalias, que, em seguida, pode visualizar no Power BI. Imaginemos que tem milhares de dispositivos constantemente a enviar dados em tempo real para um hub de eventos, adicionando até milhões de eventos por segundo. De que forma verifica a existência de anomalias ou erros nessa quantidade de dados? Por exemplo, e se os dispositivos estiverem a enviar transações de cartões de crédito e precisar de capturar em qualquer lugar que tenha várias transações em vários países/regiões dentro de um intervalo de 5 segundos? Isto poderia acontecer se alguém roubar cartões de crédito e, em seguida, utilizá-los para comprar artigos em todo o mundo ao mesmo tempo. 

Neste tutorial, vai simular este exemplo. Vai executar uma aplicação que cria e envia transações de cartão de crédito para um hub de eventos. Em seguida, você leu o fluxo de dados em tempo real com a Azure Stream Analytics, que separa as transações válidas das transações inválidas, e depois usa o Power BI para identificar visualmente as transações que são marcadas como inválidas.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Criar um hub de eventos
> * Executar a aplicação que envia as transações de cartão de crédito
> * Configurar uma tarefa do Stream Analytics para processar essas transações
> * Configurar uma visualização do Power BI para mostrar os resultados

Para concluir este tutorial, precisa de uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita][] antes de começar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Instalar [o Estúdio Visual](https://www.visualstudio.com/). 
- Precisa de uma conta do Power BI para analisar a saída de uma tarefa do Stream Analytics. Pode [experimentar o Power BI gratuitamente](https://app.powerbi.com/signupredirect?pbi_source=web).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-resources"></a>Configurar recursos

Para este tutorial, precisa de um espaço de nomes de Hubs de Eventos e de um hub de eventos. Pode criar estes recursos com a CLI do Azure ou com o Azure PowerShell. Utilize o mesmo grupo de recursos e a mesma localização para todos os recursos. Em seguida, no final, pode remover tudo num único passo ao eliminar o grupo de recursos.

As secções seguintes descrevem como executar estes passos obrigatórios. Siga as instruções da CLI *ou* do PowerShell para executar os seguintes passos:

1. Criar um [grupo de recursos.](../azure-resource-manager/management/overview.md) 

2. Crie um espaço de nomes dos Hubs de Eventos. 

3. Crie um hub de eventos.

> [!NOTE]
> Existem variáveis definidas em cada script de que precisará mais à frente no tutorial. Estas variáveis incluem o nome do grupo de recursos ($resourceGroup), o espaço de nomes do hub de eventos (**$eventHubNamespace**) e o nome do hub de eventos (**$eventHubName**). Estas variáveis são referidas com os respetivos prefixos de cifrão ($) neste artigo, para que saiba que foram definidas no script.

<!-- some day they will approve the tab control; 
  When that happens, put CLI and PSH in tabs. -->

### <a name="set-up-your-resources-using-azure-cli"></a>Configurar os recursos com a CLI do Azure

Copie e cole este script no Cloud Shell. Partindo do princípio de que já tem sessão iniciada, o script é executado uma linha de cada vez.

As variáveis que têm de ser globalmente exclusivas têm `$RANDOM` concatenado às mesmas. Quando o script for executado e as variáveis forem definidas, uma cadeia numérica aleatória é gerada e concatenada ao final da cadeia de carateres fixa, tornando-a exclusiva.

```azurecli-interactive
# Set the values for location and resource group name.
location=westus
resourceGroup=ContosoResourcesEH

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
eventHubNamespace=ContosoEHNamespace$RANDOM
echo "Event Hub Namespace = " $eventHubNamespace

# Create the Event Hubs namespace.
az eventhubs namespace create --resource-group $resourceGroup \
   --name $eventHubNamespace \
   --location $location \
   --sku Standard

# The event hub name must be globally unique, so add a random number to the end.
eventHubName=ContosoEHhub$RANDOM
echo "event hub name = " $eventHubName

# Create the event hub.
az eventhubs eventhub create --resource-group $resourceGroup \
    --namespace-name $eventHubNamespace \
    --name $eventHubName \
    --message-retention 3 \
    --partition-count 2

# Get the connection string that authenticates the app with the Event Hubs service.
connectionString=$(az eventhubs namespace authorization-rule keys list \
   --resource-group $resourceGroup \
   --namespace-name $eventHubNamespace \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString \
   --output tsv)
echo "Connection string = " $connectionString 
```

### <a name="set-up-your-resources-using-azure-powershell"></a>Configurar os recursos com o Azure PowerShell

Copie e cole este script no Cloud Shell. Partindo do princípio de que já tem sessão iniciada, o script é executado uma linha de cada vez.

As variáveis que têm de ser globalmente exclusivas têm `$(Get-Random)` concatenado às mesmas. Quando o script for executado e as variáveis forem definidas, uma cadeia numérica aleatória é gerada e concatenada ao final da cadeia de carateres fixa, tornando-a exclusiva.

```azurepowershell-interactive
# Log in to Azure account.
Login-AzAccount

# Set the values for the location and resource group.
$location = "West US"
$resourceGroup = "ContosoResourcesEH"

# Create the resource group to be used  
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
$eventHubNamespace = "contosoEHNamespace$(Get-Random)"
Write-Host "Event Hub Namespace is " $eventHubNamespace

# The event hub name must be globally unique, so add a random number to the end.
$eventHubName = "contosoEHhub$(Get-Random)"
Write-Host "Event hub Name is " $eventHubName

# Create the Event Hubs namespace.
New-AzEventHubNamespace -ResourceGroupName $resourceGroup `
     -NamespaceName $eventHubNamespace `
     -Location $location

# Create the event hub.
$yourEventHub = New-AzEventHub -ResourceGroupName $resourceGroup `
    -NamespaceName $eventHubNamespace `
    -Name $eventHubName `
    -MessageRetentionInDays 3 `
    -PartitionCount 2

# Get the event hub key, and retrieve the connection string from that object.
# You need this to run the app that sends test messages to the event hub.
$eventHubKey = Get-AzEventHubKey -ResourceGroupName $resourceGroup `
    -Namespace $eventHubNamespace `
    -AuthorizationRuleName RootManageSharedAccessKey

# Save this value somewhere local for later use.
Write-Host "Connection string is " $eventHubKey.PrimaryConnectionString
```

## <a name="run-app-to-produce-test-event-data"></a>Executar a aplicação para produzir dados de evento de teste

As amostras do Event Hubs [no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet) incluem uma aplicação de Detetor de Anomalias que produz dados de teste para si. A aplicação simula a utilização de cartões de crédito ao escrever transações de cartão de crédito no hub de eventos, incluindo a escrita ocasional de várias transações para o mesmo cartão de crédito em várias localizações, de modo a que sejam identificadas como anomalias. Para executar esta aplicação, siga estes passos: 

1. Transfira os [exemplos de Hubs de Eventos do Azure](https://github.com/Azure/azure-event-hubs/archive/master.zip) do GitHub e deszipe o ficheiro localmente.
2. Navegue para a pasta **\azure-event-hubs-master\samples\dotNet. \\** 
3. Mude para a pasta **Azure.Messaging.EventHubs\AnomalyDetector \\** e clique duas vezes no **AnomalyDetector.sln** para abrir a solução no Estúdio Visual. 

    Para utilizar a versão antiga da amostra que utiliza o antigo pacote Microsoft.Azure.EventHubs, abra a solução a partir da pasta **Microsoft.Azure.EventHubs\AnomalyDetector.** 
3. Abra o ficheiro Program.cs e substitua **Event Hubs connection string** pela cadeia de ligação que guardou quando executou o script. 
4. Substitua **Event Hub name** pelo nome do seu hub de eventos. Clique em F5 para executar a aplicação. A aplicação começa a enviar eventos para o hub de eventos e continua até ter enviado 1000 eventos. Existem alguns casos em que a aplicação precisa de estar a ser executada para que possa obter os dados. Estes casos são indicados nas instruções seguintes, quando necessário.

## <a name="set-up-azure-stream-analytics"></a>Configurar o Azure Stream Analytics

Agora, pode transmitir dados em fluxo para o seu hub de eventos. Para utilizar esses dados numa visualização do Power BI, comece por configurar uma tarefa do Stream Analytics para obter os dados que, em seguida, são fornecidos para a visualização do Power BI.

### <a name="create-the-stream-analytics-job"></a>Criar tarefa do Stream Analytics

1. No portal do Azure, clique em **Criar um recurso**. Escreva **stream analytics** na caixa de pesquisa e prima **Enter**. Selecione **Tarefa do Stream Analytics**. Clique em **Criar** no painel de tarefas do Stream Analytics. 

2. Introduza as seguintes informações para a tarefa:

   **Nome da tarefa**: utilize **contosoEHjob**. Este campo é o nome da tarefa e tem de ser globalmente exclusivo.

   **Subscrição**: selecione a sua subscrição.

   **Grupo de recursos**: utilize o mesmo grupo de recursos utilizado pelo seu hub de eventos (**ContosoResourcesEH**).

   **Localização**: utilize a mesma localização que utilizou no script de configuração (**E.U.A. Oeste**).

   ![Captura de ecrã que mostra como criar uma nova tarefa do Azure Stream Analytics.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-add-job.png)

    Aceite as predefinições no resto dos campos. Clique em **Criar**. 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada à tarefa do Stream Analytics

Se não estiver no portal no painel da **Tarefa do Stream Analytics**, pode voltar à sua tarefa do Stream Analytics ao clicar em **Grupos de Recursos** no portal e, em seguida, selecionar o grupo de recursos (**ContosoResourcesEH**). Esta ação mostra todos os recursos no grupo e, em seguida, pode selecionar a sua tarefa do Stream Analytics. 

As entradas para a tarefa do Stream Analytics são as transações de cartão de crédito do hub de eventos.

> [!NOTE]
> Os valores das variáveis que começam com o cifrão ($) são definidos nos scripts de arranque nas secções anteriores. Tem de utilizar os mesmos valores aqui quando especificar esses campos, que são o espaço de nomes dos Hubs de Eventos e o nome do hub de eventos.

1. Em **Topologia da Tarefa**, clique em **Entradas**.

2. No painel **Entradas**, clique em **Adicionar entrada de fluxo** e selecione Hubs de Eventos. No ecrã apresentado, preencha os campos seguintes:

   **Alias de entrada**: utilize **contosoinputs**. Este campo é o nome do fluxo de entrada, utilizado quando define a consulta para os dados.

   **Subscrição**: selecione a sua subscrição.

   **Espaço de nomes dos Hubs de Eventos**: selecione o seu espaço de nomes do Hub de Eventos ($**eventHubNamespace**). 

   **Nome do Hub de Eventos**: clique em **Utilizar existente** e selecione o seu hub de eventos ($**eventHubName**).

   **Nome da política dos Hubs de Eventos**: selecione **RootManageSharedAccessKey**.

   **Grupo de consumidores de Hubs de Eventos**: deixe este campo em branco para utilizar o grupo de consumidores predefinido.

   Aceite as predefinições no resto dos campos.

   ![Captura de ecrã que mostra como adicionar um fluxo de entrada à tarefa do Stream Analytics.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-inputs.png)

5. Clique em **Guardar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída à tarefa do Stream Analytics

1. Em **Topologia de Tarefas**, clique em **Saídas**. Este campo é o nome do fluxo de saída, utilizado quando define a consulta para os dados.

2. No painel **Saídas**, clique em **Adicionar** e selecione **Power BI**. No ecrã apresentado, preencha os campos seguintes:

   **Alias de saída**: utilize **contosooutputs**. Este campo é o alias exclusivo para a saída. 

   **Nome do conjunto de dados**: utilize **contosoehdataset**. Este campo é nome do conjunto de dados a utilizar no Power BI. 

   **Nome da tabela**: utilize **contosoehtable**. Este campo é o nome da tabela a utilizar no Power BI. 

   Aceite as predefinições no resto dos campos.

   ![Captura de ecrã que mostra como configurar a saída para uma tarefa do Stream Analytics.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-outputs.png)

3. Clique em **Autorizar** e inicie sessão na sua conta do Power BI.

4. Aceite as predefinições no resto dos campos.

5. Clique em **Guardar**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta da tarefa do Stream Analytics

Esta consulta serve para obter os dados que, por fim, são enviados para a visualização do Power BI. Utiliza **contosoinputs** e **contosooutputs**, que definiu anteriormente quando configurou a tarefa. Esta consulta obtém as transações de cartão de crédito que considera fraudulentas, que são transações nas quais o mesmo número de cartão de crédito tem várias transações em diferentes localizações no mesmo intervalo de cinco segundos.

1. Em **Topologia de Tarefas**, clique em **Consulta**.

2. Substitua a consulta pela seguinte: 

   ```SQL
   /* criteria for fraud:
      credit card purchases with the same card
      in different locations within 5 seconds
   */
   SELECT System.Timestamp AS WindowEnd, 
     COUNT(*) as FraudulentUses      
   INTO contosooutputs
   FROM contosoinputs CS1 TIMESTAMP BY [Timestamp]
       JOIN contosoinputs CS2 TIMESTAMP BY [Timestamp]
       /* where the credit card # is the same */
       ON CS1.CreditCardId = CS2.CreditCardId
       /* and time between the two is between 0 and 5 seconds */
       AND DATEDIFF(second, CS1, CS2) BETWEEN 0 AND 5
       /* where the location is different */
   WHERE CS1.Location != CS2.Location
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. Clique em **Guardar**.

### <a name="test-the-query-for-the-stream-analytics-job"></a>Testar a consulta da tarefa do Stream Analytics 

1. Execute a aplicação Deteção de Anomalias para enviar dados para o hub de eventos enquanto estiver a configurar e a executar o teste. 

2. No painel Consulta, clique nos pontos junto à entrada **contosoinputs** e selecione **Dados de exemplo da entrada**.

3. Especifique que pretende três minutos de dados e clique em **OK**. Aguarde até receber uma notificação a indicar que foi criada uma amostra dos dados.

4. Clique em **Testar** e certifique-se de que está a obter os resultados. Os resultados são apresentados na secção **Resultados** do painel inferior à direita, debaixo da consulta.

5. Feche o painel Consulta.

### <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

Na tarefa do Stream Analytics, clique em **Iniciar**, **Agora** e **Iniciar**. Assim que a tarefa for iniciada com êxito, o estado da tarefa é alterado de **Parado** para **Em execução**.

## <a name="set-up-the-power-bi-visualizations"></a>Configurar as visualizações do Power BI

1. Execute a aplicação Deteção de Anomalias para enviar dados para o hub de eventos enquanto estiver a configurar a visualização do Power BI. Pode ser necessário executá-la várias vezes, uma vez que gera apenas 1000 transações de cada vez que é executada.

2. Inicie sessão na sua conta do [Power BI](https://powerbi.microsoft.com/).

3. Aceda a **A minha área de trabalho**.

4. Clique em **Conjuntos de dados**.

   Deverá ver o conjunto de dados que especificou quando criou a saída para a tarefa do Stream Analytics (**contosoehdataset**). Pode demorar de 5 a 10 minutos até que o conjunto de dados seja apresentado pela primeira vez.

5. Clique em **Dashboards**, clique em **Criar** e selecione **Dashboard**.

   ![Captura de ecrã dos botões Dashboards e Criar.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-dashboard.png)

6. Especifique o nome do dashboard e clique em **Criar**. Utilize **Anomalias de Cartões de Crédito**.

   ![Captura de ecrã da especificação do nome do dashboard.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-name.png)

7. Na página dashboard, clique em **Adicionar azulejos,** selecione **Dados de Streaming personalizados** na secção **DADOS EM TEMPO REAL** e, em seguida, clique em **Seguinte**.

   ![Captura de ecrã da especificação da origem do mosaico.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-real-time-data.png)

8. Selecione o conjunto de dados (**contosoehdataset**) e clique em **Seguinte**.

   ![Captura de ecrã da especificação do conjunto de dados.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-select-dataset.png)

9. Selecione **Cartão** para o tipo de visualização. Em **Campos**, clique **em Adicionar valor** e, em seguida, selecione `fraudulentuses` .

   ![Captura de ecrã da especificação do tipo de visualização e dos campos.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-tile.png)

   Clique em **Seguinte**.

10. Defina o título como **Utilizações fraudulentas** e o subtítulo como **Soma nos últimos minutos**. Clique em **Aplicar**. Esta ação guarda o mosaico no dashboard.

    ![Captura de ecrã da especificação do título e subtítulo para o mosaico do dashboard.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-tile-details.png)

    > [!IMPORTANT]
    > Quando executam a aplicação da amostra e transmitem os dados para o centro do evento, o número deste azulejo muda rapidamente (a cada segundo). É porque a consulta Stream Analytics realmente atualiza o valor a **cada segundo**. Atualize a consulta para uma janela de 3 minutos para ver a soma nos últimos minutos. 
11. Adicione outra visualização. Repita os primeiros passos novamente:

    * Clique em **Adicionar Mosaico**.
    * Selecione **Dados de Transmissão em Fluxo Personalizados**. 
    * Clique em **Seguinte**.
    * Selecione o conjunto de dados e clique em **Seguinte**. 

12. Em **Tipo de Visualização**, selecione **Gráfico de linhas**.

13. No **eixo,** clique em **Adicionar Valor** e selecione `windowend` . 

14. Em **Valores**, clique **em Adicionar valor** e selecione `fraudulentuses` .

15. Em **Janela de tempo a apresentar**, selecione os últimos cinco minutos. Clique em **Seguinte**.

16. Especifique **Mostrar utilizações fraudulentas ao longo do tempo** para o título e deixe o subtítulo para o mosaico em branco e, em seguida, clique em **Aplicar**. Volta ao seu dashboard.

17. Execute a aplicação Deteção de Anomalias novamente para enviar alguns dados para o hub de eventos. Verá o mosaico **Utilizações fraudulentas** a mudar à medida que analisa os dados e o gráfico de linhas mostra os dados. 

    ![Captura de ecrã que mostra os resultados do Power BI](./media/event-hubs-tutorial-visualize-anomalies/power-bi-results.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser remover todos os recursos que criou, remova os dados de visualização do Power BI e, em seguida, elimine o grupo de recursos. Eliminar o grupo de recursos elimina todos os recursos incluídos no grupo. Neste caso, remove o hub de eventos, o espaço de nomes do Hub de Eventos, tarefa do Stream Analytics e o próprio grupo de recursos. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Limpar os recursos na visualização do Power BI

Inicie sessão na sua conta do Power BI. Aceda a **A minha área de trabalho**. Na linha com o nome do dashboard, clique no ícone de lixo. Em seguida, aceda a **Conjuntos de Dados** e clique no ícone de lixo para eliminar o conjunto de dados (**contosoehdataset**).

### <a name="clean-up-resources-using-azure-cli"></a>Limpar os recursos com a CLI do Azure

Para remover o grupo de recursos, utilize o comando [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="clean-up-resources-using-powershell"></a>Limpar os recursos com o PowerShell

Para remover o grupo de recursos, utilize o comando [Remove-AzResourceGroup.](/powershell/module/az.resources/remove-azresourcegroup)

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:
> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Criar um hub de eventos
> * Executar a aplicação que simula eventos e os envia para o hub de eventos
> * Configurar uma tarefa do Stream Analytics para processar eventos enviados para o hub
> * Configurar uma visualização do Power BI para mostrar os resultados

Avance para o artigo seguinte para saber mais sobre os Hubs de Eventos do Azure.

> [!div class="nextstepaction"]
> [Introdução ao envio de mensagens para os Hubs de Eventos do Azure no .NET Standard](event-hubs-dotnet-standard-getstarted-send.md)

[criar uma conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
