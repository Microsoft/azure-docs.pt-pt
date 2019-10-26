---
title: Exibir resultados de roteamento de mensagens do Hub IoT do Azure (.NET) | Microsoft Docs
description: Exibir resultados de roteamento de mensagens do Hub IoT do Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 6d40f7d81faa77a48a32aadefdf44ed89e184efb
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900788"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Tutorial: parte 2-exibir as mensagens roteadas

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Regras para rotear as mensagens

Essas são as regras para o roteamento de mensagens; Eles foram configurados na parte 1 deste tutorial e você os verá na segunda parte.

|Valor |Resultado|
|------|------|
|level="storage" |Escrever no Armazenamento do Azure.|
|level="critical" |Escrever numa fila do Service Bus. Uma Aplicação Lógica obtém a mensagem da fila e utiliza o Office 365 para enviar o e-mail.|
|predefinição |Apresentar estes dados com o Power BI.|

Agora você cria os recursos para os quais as mensagens serão roteadas, executa um aplicativo para enviar mensagens para o Hub e vê o roteamento em ação.

## <a name="create-a-logic-app"></a>Criar uma Aplicação Lógica  

A fila do Service Bus serve para receber mensagens designadas como críticas. Configure uma Aplicação lógica para monitorizar a fila do Service Bus e enviar um e-mail quando é adicionada uma mensagem à fila.

1. Na [portal do Azure](https://portal.azure.com), selecione **+ criar um recurso**. Introduza **aplicação lógica** na caixa de pesquisa e clique em Enter. Nos resultados da pesquisa exibidos, selecione aplicativo lógico e, em seguida, selecione **criar** para continuar no painel **criar aplicativo lógico** . Preencha os campos.

   **Nome**: este campo é o nome da aplicação lógica. Este tutorial utiliza **ContosoLogicApp**.

   **Subscrição**: selecione a sua subscrição do Azure.

   **Grupo de recursos**: selecione **usar existente** e selecione o grupo de recursos. Este tutorial utiliza **ContosoResources**.

   **Localização**: utilize a sua localização. Este tutorial utiliza **West US**.

   **Log Analytics**: este botão de alternar deve estar desativado.

   ![A tela criar aplicativo lógico](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Selecione **Criar**. Pode levar alguns minutos para que o aplicativo seja implantado.

2. Agora, vá para a Aplicação Lógica. A maneira mais fácil de acessar o aplicativo lógico é selecionar grupos de **recursos**, selecionar seu grupo de recursos (este tutorial usa **ContosoResources**) e, em seguida, selecionar o aplicativo lógico na lista de recursos. 

    É apresentada a página Estruturador de Aplicações Lógicas (poderá ter de deslocar o ecrã para a direita para ver a página completa). Na página designer de aplicativos lógicos, role para baixo até ver o bloco que diz **aplicativo lógico em branco +** e selecione-o. A guia padrão é "para você". Se esse painel estiver em branco, selecione **tudo** para ver todos os conectores e gatilhos disponíveis.

3. Selecione **barramento de serviço** na lista de conectores.

   ![A lista de conectores](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. É apresentada uma lista de acionadores. Selecione **quando uma mensagem é recebida em uma fila (preenchimento automático)/barramento de serviço**.

   ![A lista de gatilhos para o barramento de serviço](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. No ecrã seguinte, preencha o Nome da Ligação. Este tutorial utiliza **ContosoConnection**.

   ![Configurando a conexão para a fila do barramento de serviço](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Selecione o namespace do barramento de serviço. Este tutorial utiliza **ContosoSBNamespace**. Quando seleciona o espaço de nomes, o portal consulta o espaço de nomes do Service Bus para obter as chaves. Selecione **RootManageSharedAccessKey** e selecione **criar**.

   ![Concluindo a configuração da conexão](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. No ecrã seguinte, selecione o nome da fila na lista pendente (este tutorial utiliza **contososbqueue**). Pode utilizar as predefinições no resto dos campos.

   ![As opções de fila](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Agora, configure a ação para enviar um e-mail quando é recebida uma mensagem na fila. No designer de aplicativos lógicos, selecione **+ nova etapa** para adicionar uma etapa e, em seguida, selecione **tudo** para ver todas as opções disponíveis. No painel **escolher uma ação** , localize e selecione **Outlook do Office 365**. Na tela ações, selecione **enviar um email/Outlook do Office 365**.  

   ![As opções do Office365](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Entre na sua conta do Office 365 para configurar a conexão. Se isso expirar, basta tentar novamente. Especifique os endereços de e-mail dos destinatários. Especifique também o assunto e escreva a mensagem para apresentar no corpo ao destinatário. Para fins de teste, introduza o seu próprio endereço de e-mail para o destinatário.

   Selecione **adicionar conteúdo dinâmico** para mostrar o conteúdo da mensagem que você pode incluir. Selecione **Conteúdo** – incluirá a mensagem no e-mail.

   ![As opções de email para o aplicativo lógico](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Selecione **Guardar**. Em seguida, feche o Estruturador de Aplicações Lógicas.

## <a name="set-up-azure-stream-analytics"></a>Configurar o Azure Stream Analytics

Para ver os dados numa visualização do Power BI, configure primeiro uma tarefa do Stream Analytics para obter os dados. Lembre-se de que apenas as mensagens com o **nível** definido como **normal** são enviadas para o ponto final predefinido e serão obtidas pela tarefa do Stream Analytics para a visualização do Power BI.

### <a name="create-the-stream-analytics-job"></a>Criar a tarefa do Stream Analytics

1. Na [portal do Azure](https://portal.azure.com), selecione **criar um recurso** > **Internet das coisas** > **trabalho Stream Analytics**.

2. Introduza as seguintes informações para a tarefa.

   **Nome da tarefa**: o nome da tarefa. O nome tem de ser globalmente exclusivo. Este tutorial utiliza **contosoJob**.

   **Assinatura**: a assinatura do Azure que você está usando para o tutorial.

   **Grupo de recursos**: utilize o mesmo grupo de recursos utilizado pelo hub IoT. Este tutorial utiliza **ContosoResources**.

   **Localização**: utilize a mesma localização que utilizou no script de configuração. Este tutorial utiliza **West US**.

   ![Criar o trabalho do Stream Analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. Selecione **criar** para criar o trabalho. Pode levar alguns minutos para implantar.

    Para voltar ao trabalho, selecione grupos de **recursos**. Este tutorial utiliza **ContosoResources**. Selecione o grupo de recursos e, em seguida, selecione o Stream Analytics trabalho na lista de recursos.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada à tarefa do Stream Analytics

1. Em **topologia do trabalho**, selecione **entradas**.

2. No painel **entradas** , selecione **Adicionar entrada de fluxo** e selecione Hub IOT. No ecrã apresentado, preencha os campos seguintes:

   **Alias de entrada**: este tutorial utiliza **contosoinputs**.

   **Selecione Hub IOT em sua assinatura**: Selecione esta opção de botão de opção.

   **Assinatura**: selecione a assinatura do Azure que você está usando para este tutorial.

   **Hub IOT**: selecione o Hub IOT. Este tutorial utiliza **ContosoTestHub**.

   **Ponto final**: selecione **Mensagens**. (Se selecionar Monitorização de Operações, poderá obter os dados de telemetria sobre o hub IoT, em vez dos dados que está a enviar.) 

   **Nome da política de acesso compartilhado**: selecione **serviço**. O portal preenche a Chave da Política de Acesso Partilhado por si.

   **Grupo de consumidores**: selecione o grupo de consumidores configurado na parte 1 deste tutorial. Este tutorial utiliza **contosoconsumers**.
   
   Para o resto dos campos, aceite as predefinições. 

   ![Configurar as entradas para o trabalho do Stream Analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

3. Selecione **Guardar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída à tarefa do Stream Analytics

1. Em **topologia do trabalho**, selecione **saídas**.

2. No painel **saídas** , selecione **Adicionar**e, em seguida, selecione **Power bi**. No ecrã apresentado, preencha os campos seguintes:

   **Alias de saída**: o alias exclusivo da saída. Este tutorial utiliza **contosooutputs**. 

   **Nome do conjunto de dados**: nome do conjunto de dados a ser utilizado no Power BI. Este tutorial utiliza **contosodataset**. 

   **Nome da tabela**: nome da tabela a ser utilizada no Power BI. Este tutorial utiliza **contosotable**.

   Aceite as predefinições no resto dos campos.

3. Selecione **autorizar**e entre em sua conta do Power bi. (Isso pode levar mais de uma tentativa).

   ![Configurar as saídas para o trabalho do Stream Analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Selecione **Guardar**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta da tarefa do Stream Analytics

1. Em **Topologia do Trabalho**, selecione **Consulta**.

2. Substitua `[YourInputAlias]` pelo alias de entrada da tarefa. Este tutorial utiliza **contosoinputs**.

3. Substitua `[YourOutputAlias]` pelo alias de saída da tarefa. Este tutorial utiliza **contosooutputs**.

   ![Configurar a consulta para o trabalho do Stream Analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Selecione **Guardar**.

5. Feche o painel Consulta. Você retorna para a exibição dos recursos no grupo de recursos. Selecione o trabalho de Stream Analytics. Este tutorial denomina-o **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

No trabalho Stream Analytics, selecione **iniciar** > **agora** > **Iniciar**. Assim que a tarefa for iniciada com êxito, o estado da tarefa é alterado de **Parado** para **Em execução**.

Para configurar o relatório do Power BI, vai precisar de dados. Para isso, vai configurar o Power BI, depois de criar o dispositivo e executar a aplicação de simulação do dispositivo.

## <a name="run-simulated-device-app"></a>Executar aplicativo de dispositivo simulado

Na parte 1 deste tutorial, você configura um dispositivo para simular usando um dispositivo IoT. Nesta seção, você baixa o aplicativo de console .NET que simula esse dispositivo enviando mensagens do dispositivo para a nuvem para um hub IoT (supondo que você já não baixou o aplicativo e os recursos na parte 1).

Esse aplicativo envia mensagens para cada um dos diferentes métodos de roteamento de mensagens. Também há uma pasta no download que contém o modelo de Azure Resource Manager completo e o arquivo de parâmetros, bem como os scripts do CLI do Azure e do PowerShell.

Se você não tiver baixado os arquivos do repositório na parte 1 deste tutorial, vá em frente e baixe-os agora da [simulação de dispositivo IOT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). A seleção deste link baixa um repositório com vários aplicativos nele; a solução que você está procurando é IOT-Hub/tutoriais/Routing/IoT_SimulatedDevice. sln. 

Clique duas vezes no arquivo de solução (IoT_SimulatedDevice. sln) para abrir o código no Visual Studio e, em seguida, abra Program.cs. Substitua `{your hub name}` pelo nome do anfitrião do hub IoT. O formato do nome do anfitrião do hub IoT é **{iot-hub-name}.azure-devices.net**. Para este tutorial, o nome do anfitrião do hub é **ContosoTestHub.azure-devices.net**. Em seguida, substitua `{your device key}` pela chave de dispositivo que guardou anteriormente quando configurou o dispositivo simulado. 

   ```csharp
        static string s_myDeviceId = "Contoso-Test-Device";
        static string s_iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string s_deviceKey = "{your device key}";
   ```

## <a name="run-and-test"></a>Executar e testar

Execute a aplicação de consola. Aguarde alguns minutos. Pode ver as mensagens a serem enviadas no ecrã da consola da aplicação.

A aplicação envia uma nova mensagem de dispositivo para cloud a cada segundo. A mensagem contém um objeto JSON serializado com o ID do dispositivo, a temperatura, a humidade e o nível da mensagem, que está predefinido como `normal`. Atribui aleatoriamente um nível de `critical` ou `storage`, o que faz com que a mensagem seja encaminhada para a conta de armazenamento ou para a fila do Service Bus (que aciona a Aplicação Lógica para enviar um e-mail). As leituras (`normal`) predefinidas serão apresentadas no relatório do BI que vai configurar a seguir.

Se tudo estiver configurado corretamente, neste momento, deverá ver os seguintes resultados:

1. Começa a receber e-mails sobre mensagens críticas.

   ![Os emails resultantes](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   Esse resultado significa que as instruções a seguir são verdadeiras. 

   * O encaminhamento para a fila do Service Bus está a funcionar corretamente.
   * A Aplicação Lógica que obtém a mensagem da fila do Service Bus está a funcionar corretamente.
   * O conector da Aplicação Lógica para o Outlook está a funcionar corretamente. 

2. No [portal do Azure](https://portal.azure.com), selecione **grupos de recursos** e selecione seu grupo de recursos. Este tutorial utiliza **ContosoResources**. 

    Selecione a conta de armazenamento, selecione **contêineres**e, em seguida, selecione o contêiner. Este tutorial utiliza **contosoresults**. Deverá ver uma pasta e poderá pesquisar através dos diretórios até ver um ou mais ficheiros. Abra um desses ficheiros; contêm as entradas encaminhadas para a conta de armazenamento. 

   ![Os arquivos de resultado no armazenamento](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Esse resultado significa que a instrução a seguir é verdadeira.

   * O encaminhamento para a conta de armazenamento está a funcionar corretamente.

Agora, com o aplicativo ainda em execução, configure a visualização Power BI para ver as mensagens provenientes do roteamento padrão.

## <a name="set-up-the-power-bi-visualizations"></a>Configurar as visualizações do Power BI

1. Inicie sessão na sua conta do [Power BI](https://powerbi.microsoft.com/).

2. Aceda a **Áreas de Trabalho** e selecione a área de trabalho que definiu quando criou a saída da tarefa do Stream Analytics. Este tutorial utiliza **A Minha Área de Trabalho**. 

3. Selecione **conjuntos**de os. Se você não tiver conjuntos de itens, aguarde alguns minutos e verifique novamente.

   Deverá ver o conjunto de dados que especificou quando criou a saída da tarefa do Stream Analytics. Este tutorial utiliza **contosodataset**. (Pode demorar 5 a 10 minutos para que o conjunto de dados seja apresentado na primeira vez.)

4. Em **ações**, selecione o primeiro ícone para criar um relatório.

   ![Espaço de trabalho Power BI com ações e ícone de relatório realçado](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Crie um gráfico de linhas para mostrar a temperatura em tempo real ao longo do tempo.

   * Na página criação de relatório, adicione um gráfico de linhas selecionando o ícone de gráfico de linhas.

     ![As visualizações e os campos](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * No painel **Campos**, expanda a tabela que especificou quando criou a saída da tarefa do Stream Analytics. Este tutorial utiliza **contosotable**.

   * Arraste **EventEnqueuedUtcTime** para **Eixo** no painel **Visualizações**.

   * Arraste **temperatura** para **Valores**.

   É criado um gráfico de linhas. O eixo X apresenta a data e hora no fuso horário UTC. O eixo Y apresenta a temperatura do sensor.

6. Crie outro gráfico de linhas para mostrar a humidade em tempo real ao longo do tempo. Para configurar o segundo gráfico, siga o mesmo processo para o primeiro gráfico, colocando **EventEnqueuedUtcTime** no eixo x (**eixo**) e **umidade** no eixo y (**valores**).

   ![O relatório final de Power BI com os dois gráficos](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Selecione **salvar** para salvar o relatório, inserindo um nome para o relatório, se solicitado.

Poderá ver os dados em ambos os gráficos. Esse resultado significa que as seguintes instruções são verdadeiras:

   * O encaminhamento para o ponto final predefinido está a funcionar corretamente.
   * A tarefa do Azure Stream Analytics está a transmitir corretamente.
   * A Visualização do Power BI está configurada corretamente.

Você pode atualizar os gráficos para ver os dados mais recentes selecionando o botão atualizar na parte superior da janela de Power BI. 

## <a name="clean-up-resources"></a>Limpar recursos 

Se você quiser remover todos os recursos do Azure criados por meio de ambas as partes deste tutorial, exclua o grupo de recursos. Esta ação também elimina todos os recursos contidos no grupo. Neste caso, remove o hub IoT, o espaço de nomes e a fila do Service Bus, a Aplicação Lógica, a conta de armazenamento e o próprio grupo de recursos. Você também pode remover os recursos de Power BI e limpar os emails enviados durante o tutorial.

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Limpar os recursos na visualização do Power BI

Inicie sessão na sua conta do [Power BI](https://powerbi.microsoft.com/). Vá para a área de trabalho. Este tutorial utiliza **A Minha Área de Trabalho**. Para remover a visualização Power BI, vá para conjuntos de valores e selecione o ícone de lixeira para excluir o conjunto de um. Este tutorial utiliza **contosodataset**. Quando remover o conjunto de dados, o relatório é também removido.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Usar o CLI do Azure para limpar os recursos

Para remover o grupo de recursos, utilize o comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` foi definido como **ContosoResources** de volta no início deste tutorial.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Usar o PowerShell para limpar os recursos

Para remover o grupo de recursos, use o comando [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) . `$resourceGroup` foi definido como **ContosoResources** de volta no início deste tutorial.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

### <a name="clean-up-test-emails"></a>Limpar emails de teste

Talvez você também queira excluir a quantidade de emails em sua caixa de entrada que foram gerados por meio do aplicativo lógico enquanto o aplicativo do dispositivo estava em execução.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial de duas partes, você aprendeu a usar o roteamento de mensagens para rotear mensagens do Hub IoT para destinos diferentes executando as seguintes tarefas.  

**Parte I: criar recursos, configurar o roteamento de mensagens**
> [!div class="checklist"]
> * Crie os recursos – um hub IoT, uma conta de armazenamento, uma fila do barramento de serviço e um dispositivo simulado.
> * Configure os pontos de extremidade e as rotas de mensagem no Hub IoT para a conta de armazenamento e a fila do barramento de serviço.

**Parte II: enviar mensagens para o Hub, exibir resultados roteados**
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
> [Configurar e usar métricas e diagnósticos com um hub IoT](tutorial-use-metrics-and-diags.md)
