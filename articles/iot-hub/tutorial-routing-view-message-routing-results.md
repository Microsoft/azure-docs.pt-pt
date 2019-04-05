---
title: Ver resultados de encaminhamento (.NET) de mensagens do IoT Hub do Azure | Documentos da Microsoft
description: Ver resultados de encaminhamento de mensagens do IoT Hub do Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 1417ecdaf6a85f491e1accfb9564e27d15e13445
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045838"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Tutorial: Parte 2: ver as mensagens encaminhadas

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Regras para encaminhar as mensagens

Estas são as regras de encaminhamento de mensagens; eles foram configurados na parte 1 deste tutorial, e vê-los funcionar nesta segunda parte.

|valor |Resultado|
|------|------|
|level="storage" |Escrever no Armazenamento do Azure.|
|level="critical" |Escrever numa fila do Service Bus. Uma Aplicação Lógica obtém a mensagem da fila e utiliza o Office 365 para enviar o e-mail.|
|predefinição |Apresentar estes dados com o Power BI.|

Agora, criar os recursos para o qual as mensagens serão encaminhadas, executar uma aplicação para enviar mensagens para o hub em veja o encaminhamento em ação.

## <a name="create-a-logic-app"></a>Criar uma Aplicação Lógica  

A fila do Service Bus serve para receber mensagens designadas como críticas. Configure uma Aplicação lógica para monitorizar a fila do Service Bus e enviar um e-mail quando é adicionada uma mensagem à fila.

1. Na [portal do Azure](https://portal.azure.com), selecione **+ criar um recurso**. Introduza **aplicação lógica** na caixa de pesquisa e clique em Enter. Resultados da pesquisa apresentado, selecione a aplicação lógica, em seguida, selecione **Create** para continuar para o **Criar aplicação lógica** painel. Preencha os campos.

   **Nome**: Este campo é o nome da aplicação lógica. Este tutorial utiliza **ContosoLogicApp**.

   **Subscrição**: Selecione a sua subscrição do Azure.

   **Grupo de recursos**: Selecione **utilizar existente** e selecione o grupo de recursos. Este tutorial utiliza **ContosoResources**.

   **Localização**: Utilize a sua localização. Este tutorial utiliza **West US**.

   **Log Analytics**: Esta alternância deverá desativá-la.

   ![O ecrã de criar aplicação de lógica](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Selecione **Criar**.

2. Agora, vá para a Aplicação Lógica. A maneira mais fácil para ir para a aplicação lógica consiste em selecionar **grupos de recursos**, selecione o grupo de recursos (Este tutorial utiliza **ContosoResources**), em seguida, selecione a aplicação lógica a partir da lista de recursos. É apresentada a página Estruturador de Aplicações Lógicas (poderá ter de deslocar o ecrã para a direita para ver a página completa). Na página de estruturador de aplicações lógicas, desloque para baixo até ver o mosaico que diz **aplicação lógica em branco +** e selecioná-lo. A guia padrão é "Para o utilizador". Se este painel está em branco, selecione **todos os** para ver todos os conectores e acionadores disponíveis.

3. Selecione **do Service Bus** na lista de conectores.

   ![A lista de conectores](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. É apresentada uma lista de acionadores. Selecione **quando uma mensagem é recebida numa fila (concluir automaticamente) / do Service Bus**.

   ![A lista de acionadores para o Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. No ecrã seguinte, preencha o Nome da Ligação. Este tutorial utiliza **ContosoConnection**.

   ![Configuração da ligação para a fila do Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Selecione o espaço de nomes do Service Bus. Este tutorial utiliza **ContosoSBNamespace**. Quando seleciona o espaço de nomes, o portal consulta o espaço de nomes do Service Bus para obter as chaves. Selecione **RootManageSharedAccessKey** e selecione **criar**.

   ![A concluir a configuração da ligação](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. No ecrã seguinte, selecione o nome da fila na lista pendente (este tutorial utiliza **contososbqueue**). Pode utilizar as predefinições no resto dos campos.

   ![As opções de fila](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Agora, configure a ação para enviar um e-mail quando é recebida uma mensagem na fila. No estruturador de aplicações lógicas, selecione **+ novo passo** para adicionar um passo, em seguida, selecione **todos os** para ver todas as opções disponíveis. Na **escolher uma ação** painel, localize e selecione **Outlook do Office 365**. No ecrã de disparadores, selecione **enviar um e-mail / Outlook do Office 365**.  

   ![As opções do Office 365](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Iniciar sessão sua conta Office 365 para configurar a ligação. Se isso exceder o tempo limite, apenas tente novamente. Especifique os endereços de e-mail dos destinatários. Especifique também o assunto e escreva a mensagem para apresentar no corpo ao destinatário. Para fins de teste, introduza o seu próprio endereço de e-mail para o destinatário.

   Selecione **adicionar conteúdo dinâmico** para mostrar o conteúdo da mensagem que podem ser incluídos. Selecione **Conteúdo** – incluirá a mensagem no e-mail.

   ![As opções de email para a aplicação lógica](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Selecione **Guardar**. Em seguida, feche o Estruturador de Aplicações Lógicas.

## <a name="set-up-azure-stream-analytics"></a>Configurar o Azure Stream Analytics

Para ver os dados numa visualização do Power BI, configure primeiro uma tarefa do Stream Analytics para obter os dados. Lembre-se de que apenas as mensagens com o **nível** definido como **normal** são enviadas para o ponto final predefinido e serão obtidas pela tarefa do Stream Analytics para a visualização do Power BI.

### <a name="create-the-stream-analytics-job"></a>Criar a tarefa do Stream Analytics

1. Na [portal do Azure](https://portal.azure.com), selecione **criar um recurso** > **Internet das coisas** > **tarefa do Stream Analytics**.

2. Introduza as seguintes informações para a tarefa.

   **Nome da tarefa**: O nome da tarefa. O nome tem de ser globalmente exclusivo. Este tutorial utiliza **contosoJob**.

   **Subscrição**: A subscrição do Azure que está a utilizar para o tutorial.

   **Grupo de recursos**: Utilize o mesmo grupo de recursos utilizado pelo IoT hub. Este tutorial utiliza **ContosoResources**.

   **Localização**: Utilize a mesma localização que utilizou no script de configuração. Este tutorial utiliza **West US**.

   ![Criar a tarefa do stream analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. Selecione **criar** para criar a tarefa. Para voltar ao trabalho, selecione **grupos de recursos**. Este tutorial utiliza **ContosoResources**. Selecione o grupo de recursos, em seguida, selecione a tarefa do Stream Analytics na lista de recursos.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada à tarefa do Stream Analytics

4. Sob **topologia da tarefa**, selecione **entradas**.

5. Na **entradas** painel, selecione **Adicionar entrada de fluxo** e selecione o IoT Hub. No ecrã apresentado, preencha os campos seguintes:

   **O alias de entrada**: Este tutorial utiliza **contosoinputs**.

   **Selecione o IoT Hub da sua subscrição**: Selecione esta opção.

   **Subscrição**: Selecione a subscrição do Azure que está a utilizar para este tutorial.

   **IoT Hub**: Selecione o Hub IoT. Este tutorial utiliza **ContosoTestHub**.

   **Ponto final**: Selecione **mensagens**. (Se selecionar Monitorização de Operações, poderá obter os dados de telemetria sobre o hub IoT, em vez dos dados que está a enviar.) 

   **Nome da política de acesso partilhado**: Selecione **iothubowner**. O portal preenche a Chave da Política de Acesso Partilhado por si.

   **Grupo de consumidores**: Selecione o grupo de consumidores definido no passo 1 deste tutorial. Este tutorial utiliza **contosoconsumers**.
   
   Para o resto dos campos, aceite as predefinições. 

   ![Configurar as entradas para a tarefa do stream analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

6. Selecione **Guardar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída à tarefa do Stream Analytics

1. Sob **topologia da tarefa**, selecione **saídas**.

2. Na **saídas** painel, selecione **Add**e, em seguida, selecione **Power BI**. No ecrã apresentado, preencha os campos seguintes:

   **Alias de saída**: O alias exclusivo para a saída. Este tutorial utiliza **contosooutputs**. 

   **Nome do conjunto de dados**: Nome do conjunto de dados a ser utilizado no Power BI. Este tutorial utiliza **contosodataset**. 

   **Nome da tabela**: Nome da tabela a ser utilizado no Power BI. Este tutorial utiliza **contosotable**.

   Aceite as predefinições no resto dos campos.

3. Selecione **autorizar**e inicie sessão na sua conta do Power BI. (Pode demorar mais do que uma tentativa).

   ![Configurar as saídas para a tarefa do stream analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Selecione **Guardar**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta da tarefa do Stream Analytics

1. Em **Topologia do Trabalho**, selecione **Consulta**.

2. Substitua `[YourInputAlias]` pelo alias de entrada da tarefa. Este tutorial utiliza **contosoinputs**.

3. Substitua `[YourOutputAlias]` pelo alias de saída da tarefa. Este tutorial utiliza **contosooutputs**.

   ![Configurar a consulta para a tarefa do stream analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Selecione **Guardar**.

5. Feche o painel Consulta. Regressar à vista dos recursos no grupo de recursos. Selecione a tarefa do Stream Analytics. Este tutorial denomina-o **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

A tarefa de Stream Analytics, selecione **começar** > **agora** > **iniciar**. Assim que a tarefa for iniciada com êxito, o estado da tarefa é alterado de **Parado** para **Em execução**.

Para configurar o relatório do Power BI, vai precisar de dados. Para isso, vai configurar o Power BI, depois de criar o dispositivo e executar a aplicação de simulação do dispositivo.

## <a name="run-simulated-device-app"></a>Executar a aplicação de dispositivo simulado

A parte 1 deste tutorial, vai configurar um dispositivo para simular a utilizar um dispositivo de IoT. Nesta secção, transfira a aplicação de consola .NET que simula esse dispositivo envio de mensagens do dispositivo para a cloud para um hub IoT, partindo do princípio de que já não a transferir a aplicação e os recursos na parte 1 deste tutorial.

Esta aplicação envia mensagens para cada um dos métodos de encaminhamento de mensagem diferente. Também existe uma pasta no download que contém o modelo Azure Resource Manager completo e o ficheiro de parâmetros, bem como a CLI do Azure e os scripts do PowerShell.

Se não transferir os ficheiros a partir do repositório no passo 1 deste tutorial, vá em frente e transferi-los agora a partir [simulação do dispositivo IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Selecionar esta ligação transfere um repositório com vários aplicativos no mesmo; a solução que está procurando é iot-hub/Tutorials/Routing/IoT_SimulatedDevice.sln. 

Clique duas vezes no arquivo de solução (IoT_SimulatedDevice.sln) para abrir o código no Visual Studio, em seguida, abra o ficheiro Program.cs. Substitua `{iot hub hostname}` pelo nome do anfitrião do hub IoT. O formato do nome do anfitrião do hub IoT é **{iot-hub-name}.azure-devices.net**. Para este tutorial, o nome do anfitrião do hub é **ContosoTestHub.azure-devices.net**. Em seguida, substitua `{device key}` pela chave de dispositivo que guardou anteriormente quando configurou o dispositivo simulado. 

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

   ![Os e-mails resultantes](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   Este resultado significa que declarações a seguir forem verdadeiras. 

   * O encaminhamento para a fila do Service Bus está a funcionar corretamente.
   * A Aplicação Lógica que obtém a mensagem da fila do Service Bus está a funcionar corretamente.
   * O conector da Aplicação Lógica para o Outlook está a funcionar corretamente. 

2. Na [portal do Azure](https://portal.azure.com), selecione **grupos de recursos** e selecione o grupo de recursos. Este tutorial utiliza **ContosoResources**. Selecione a conta de armazenamento, selecione **Blobs**, em seguida, selecione o contentor. Este tutorial utiliza **contosoresults**. Deverá ver uma pasta e poderá pesquisar através dos diretórios até ver um ou mais ficheiros. Abra um desses ficheiros; contêm as entradas encaminhadas para a conta de armazenamento. 

   ![Os arquivos de resultados no armazenamento](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Este resultado significa que a instrução a seguir for verdadeira.

   * O encaminhamento para a conta de armazenamento está a funcionar corretamente.

Agora com a aplicação ainda em execução, configure a visualização do Power BI para ver as mensagens que chegam através do encaminhamento predefinido.

## <a name="set-up-the-power-bi-visualizations"></a>Configurar as visualizações do Power BI

1. Inicie sessão na sua conta do [Power BI](https://powerbi.microsoft.com/).

2. Aceda a **Áreas de Trabalho** e selecione a área de trabalho que definiu quando criou a saída da tarefa do Stream Analytics. Este tutorial utiliza **A Minha Área de Trabalho**. 

3. Selecione **conjuntos de dados**. Se não tiver quaisquer conjuntos de dados, aguarde alguns minutos e verificar novamente.

   Deverá ver o conjunto de dados que especificou quando criou a saída da tarefa do Stream Analytics. Este tutorial utiliza **contosodataset**. (Pode demorar 5 a 10 minutos para que o conjunto de dados seja apresentado na primeira vez.)

4. Sob **AÇÕES**, selecione o ícone primeiro para criar um relatório.

   ![Área de trabalho do Power BI com o ícone de relatório e ações realçado](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Crie um gráfico de linhas para mostrar a temperatura em tempo real ao longo do tempo.

   * Na página de criação do relatório, adicione um gráfico de linhas selecionando o ícone de gráfico de linhas.

     ![As visualizações e os campos](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * No painel **Campos**, expanda a tabela que especificou quando criou a saída da tarefa do Stream Analytics. Este tutorial utiliza **contosotable**.

   * Arraste **EventEnqueuedUtcTime** para **Eixo** no painel **Visualizações**.

   * Arraste **temperatura** para **Valores**.

   É criado um gráfico de linhas. O eixo X apresenta a data e hora no fuso horário UTC. O eixo Y apresenta a temperatura do sensor.

6. Crie outro gráfico de linhas para mostrar a humidade em tempo real ao longo do tempo. Para configurar o segundo gráfico, siga os mesmos passos acima e coloque **EventEnqueuedUtcTime** no eixo X e **humidade** no eixo Y.

   ![O relatório final do Power BI com os dois gráficos](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Selecione **guardar** para guardar o relatório.

Poderá ver os dados em ambos os gráficos. Este resultado significa que declarações a seguir forem verdadeiras:

   * O encaminhamento para o ponto final predefinido está a funcionar corretamente.
   * A tarefa do Azure Stream Analytics está a transmitir corretamente.
   * A Visualização do Power BI está configurada corretamente.

Pode atualizar os gráficos para ver os dados mais recentes ao selecionar o botão de atualização na parte superior da janela do Power BI. 

## <a name="clean-up-resources"></a>Limpar recursos 

Se pretender remover todos os recursos que criou ambas as partes neste tutorial, elimine o grupo de recursos. Esta ação também elimina todos os recursos contidos no grupo. Neste caso, remove o hub IoT, o espaço de nomes e a fila do Service Bus, a Aplicação Lógica, a conta de armazenamento e o próprio grupo de recursos. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Limpar os recursos na visualização do Power BI

Inicie sessão na sua conta do [Power BI](https://powerbi.microsoft.com/). Vá para a área de trabalho. Este tutorial utiliza **A Minha Área de Trabalho**. Para remover a visualização do Power BI, aceda a conjuntos de dados e selecione o caixote do lixo pode ícone para eliminar o conjunto de dados. Este tutorial utiliza **contosodataset**. Quando remover o conjunto de dados, o relatório é também removido.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Utilizar a CLI do Azure para limpar os recursos

Para remover o grupo de recursos, utilize o comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` foi definido como **ContosoResources** início deste tutorial.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Utilize o PowerShell para limpar os recursos

Para remover o grupo de recursos, utilize o [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) comando. `$resourceGroup` foi definido como **ContosoResources** início deste tutorial.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Este tutorial parte 2, aprendeu a usar o roteamento de mensagens para encaminhar mensagens do IoT Hub para diversos destinos, efetuando as seguintes tarefas.  

**Parte i: Criar recursos, configurar o encaminhamento de mensagens**
> [!div class="checklist"]
> * Crie os recursos de um hub IoT, uma conta de armazenamento, uma fila do Service Bus e um dispositivo simulado.
> * Configure os pontos de extremidade e rotas de mensagens no IoT Hub para a conta de armazenamento e a fila do Service Bus.

**Parte II: Enviar mensagens para o hub, ver resultados roteados**
> [!div class="checklist"]
> * Criar uma Aplicação Lógica que é acionada para enviar e-mails quando é adicionada uma mensagem à fila do Service Bus.
> * Transferir e executar uma aplicação que simula um Dispositivo IoT a enviar mensagens para o hub para as diferentes opções de encaminhamento.
> * Criar uma visualização do Power BI de dados enviados para o ponto final predefinido.
> * Visualizar os resultados...
> * …na fila do Service Bus e nos e-mails.
> * …na conta de armazenamento.
> * ...na visualização do Power BI.

Avance para o tutorial seguinte para aprender a gerir o estado de um dispositivo IoT. 

> [!div class="nextstepaction"]
> [Configurar e utilizar métricas e diagnósticos com um IoT Hub](tutorial-use-metrics-and-diags.md)
