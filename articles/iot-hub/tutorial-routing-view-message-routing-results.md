---
title: Ver resultados de encaminhamento de mensagens Azure IoT Hub (.NET) [ Microsoft Docs
description: Depois de configurar todos os recursos utilizando a Parte 1 do tutorial, adicione a capacidade de encaminhar mensagens para o Azure Stream Analytics e ver os resultados no PowerBI.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: bfee4e64070e5f37eaa3d63280409f00c0ed8672
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73890399"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Tutorial: Parte 2 - Ver as mensagens direcionadas

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Regras para encaminhamento das mensagens

Estas são as regras para o encaminhamento de mensagens; estes foram criados na Parte 1 deste tutorial, e você vê-los trabalhar nesta segunda parte.

|Valor |Resultado|
|------|------|
|level="storage" |Escrever no Armazenamento do Azure.|
|level="critical" |Escrever numa fila do Service Bus. Uma Aplicação Lógica obtém a mensagem da fila e utiliza o Office 365 para enviar o e-mail.|
|predefinição |Apresentar estes dados com o Power BI.|

Agora cria os recursos para os quais as mensagens serão encaminhadas, executa uma app para enviar mensagens para o centro e ver o encaminhamento em ação.

## <a name="create-a-logic-app"></a>Criar uma Aplicação Lógica  

A fila do Service Bus serve para receber mensagens designadas como críticas. Configure uma Aplicação lógica para monitorizar a fila do Service Bus e enviar um e-mail quando é adicionada uma mensagem à fila.

1. No [portal Azure, selecione](https://portal.azure.com) **+ Crie um recurso**. Introduza **aplicação lógica** na caixa de pesquisa e clique em Enter. A partir dos resultados de pesquisa apresentados, selecione Logic App e, em seguida, selecione **Create** para continuar para o painel de **aplicações lógica Criar.** Preencha os campos.

   **Nome**: este campo é o nome da aplicação lógica. Este tutorial utiliza **ContosoLogicApp**.

   **Subscrição**: selecione a sua subscrição do Azure.

   **Grupo de recursos**: Selecione **Use o existente** e selecione o seu grupo de recursos. Este tutorial utiliza **ContosoResources**.

   **Localização**: utilize a sua localização. Este tutorial utiliza **E.U.A. Oeste**.

   **Log Analytics**: este botão de alternar deve estar desativado.

   ![O ecrã da aplicação Create Logic](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Selecione **Criar**. Pode levar alguns minutos para a aplicação ser implementada.

2. Agora, vá para a Aplicação Lógica. A maneira mais fácil de chegar à Aplicação Lógica é selecionar **grupos de Recursos,** selecionar o seu grupo de recursos (este tutorial utiliza **ContosoResources),** e depois selecionar a App Lógica da lista de recursos. 

    É apresentada a página Estruturador de Aplicações Lógicas (poderá ter de deslocar o ecrã para a direita para ver a página completa). Na página logic Apps Designer, desça até ver o azulejo que diz **Blank Logic App +** e selecione-o. O separador predefinido é "For You". Se este painel estiver em branco, selecione **Tudo** para ver todos os conectores e gatilhos disponíveis.

3. Selecione **Service Bus** da lista de conectores.

   ![A lista de conectores](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. É apresentada uma lista de acionadores. Selecione **Quando uma mensagem for recebida numa fila (automática) / Autocarro de Serviço**.

   ![A lista de gatilhos para o Ônibus de Serviço](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. No ecrã seguinte, preencha o Nome da Ligação. Este tutorial utiliza **ContosoConnection**.

   ![Configuração da ligação para a fila do ônibus de serviço](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Selecione o espaço de nome do Autocarro de Serviço. Este tutorial utiliza **ContosoSBNamespace**. Quando seleciona o espaço de nomes, o portal consulta o espaço de nomes do Service Bus para obter as chaves. Selecione **RootManageSharedAccessKey** e selecione **Criar**.

   ![Acabamento de configuração da ligação](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. No ecrã seguinte, selecione o nome da fila na lista pendente (este tutorial utiliza **contososbqueue**). Pode utilizar as predefinições no resto dos campos.

   ![As opções de fila](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Agora, configure a ação para enviar um e-mail quando é recebida uma mensagem na fila. No Logic Apps Designer, selecione **+ Novo passo** para adicionar um passo e, em seguida, selecione **All** para ver todas as opções disponíveis. No painel **de ação Escolha,** encontre e selecione **Office 365 Outlook**. No ecrã Ações, selecione **Enviar um e-mail / Office 365 Outlook**.  

   ![As opções do Office365](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Inscreva-se na sua conta do Office 365 para configurar a ligação. Se isto passar do tempo, tente de novo. Especifique os endereços de e-mail dos destinatários. Especifique também o assunto e escreva a mensagem para apresentar no corpo ao destinatário. Para fins de teste, introduza o seu próprio endereço de e-mail para o destinatário.

   Selecione **Adicionar conteúdo dinâmico** para mostrar o conteúdo a partir da mensagem que pode incluir. Selecione **Conteúdo** – incluirá a mensagem no e-mail.

   ![As opções de e-mail para a aplicação lógica](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Selecione **Guardar**. Em seguida, feche o Estruturador de Aplicações Lógicas.

## <a name="set-up-azure-stream-analytics"></a>Configurar o Azure Stream Analytics

Para ver os dados numa visualização do Power BI, configure primeiro uma tarefa do Stream Analytics para obter os dados. Lembre-se de que apenas as mensagens com o **nível** definido como **normal** são enviadas para o ponto final predefinido e serão obtidas pela tarefa do Stream Analytics para a visualização do Power BI.

### <a name="create-the-stream-analytics-job"></a>Criar tarefa do Stream Analytics

1. No [portal Azure,](https://portal.azure.com)selecione **Criar um recurso** > **Internet of Things** > **Stream Analytics.**

2. Introduza as seguintes informações para a tarefa.

   **Nome da tarefa**: o nome da tarefa. O nome tem de ser globalmente exclusivo. Este tutorial utiliza **contosoJob**.

   **Subscrição**: A subscrição Azure que está a utilizar para o tutorial.

   **Grupo de recursos**: utilize o mesmo grupo de recursos utilizado pelo hub IoT. Este tutorial utiliza **ContosoResources**.

   **Localização**: utilize a mesma localização que utilizou no script de configuração. Este tutorial utiliza **E.U.A. Oeste**.

   ![Criar o trabalho de análise de fluxo](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. Selecione **Criar** para criar o trabalho. Pode levar alguns minutos para ser implantado.

    Para voltar ao trabalho, selecione **Grupos de Recursos**. Este tutorial utiliza **ContosoResources**. Selecione o grupo de recursos e, em seguida, selecione o trabalho stream analytics na lista de recursos.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada à tarefa do Stream Analytics

1. Em **Topologia de Trabalho,** selecione **Inputs**.

2. No painel de **entradas,** **selecione Adicionar entrada** de fluxo e selecione IoT Hub. No ecrã apresentado, preencha os campos seguintes:

   **Alias de entrada**: este tutorial utiliza **contosoinputs**.

   **Selecione IoT Hub a partir da sua subscrição**: Selecione esta opção de botão de rádio.

   **Subscrição**: Selecione a subscrição Azure que está a utilizar para este tutorial.

   **Hub IoT**: Selecione o hub IoT. Este tutorial utiliza **ContosoTestHub**.

   **Ponto final**: selecione **Mensagens**. (Se selecionar Monitorização de Operações, poderá obter os dados de telemetria sobre o hub IoT, em vez dos dados que está a enviar.) 

   **Nome da política**de acesso partilhado : Selecione **o serviço**. O portal preenche a Chave da Política de Acesso Partilhado por si.

   **Grupo de consumidores**: Selecione o grupo de consumidores criado na Parte 1 deste tutorial. Este tutorial utiliza **contosoconsumers**.
   
   Para o resto dos campos, aceite as predefinições. 

   ![Configurar as inputs para o trabalho de análise de fluxo](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

3. Selecione **Guardar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída à tarefa do Stream Analytics

1. Em **Topologia de Trabalho,** selecione **Saídas**.

2. No painel **de saídas,** selecione **Adicionar**, e, em seguida, selecione **Power BI**. No ecrã apresentado, preencha os campos seguintes:

   **Alias de saída**: o alias exclusivo da saída. Este tutorial utiliza **contosooutputs**. 

   **Nome do conjunto de dados**: nome do conjunto de dados a ser utilizado no Power BI. Este tutorial utiliza **contosodataset**. 

   **Nome da tabela**: nome da tabela a ser utilizada no Power BI. Este tutorial utiliza **contosotable**.

   Aceite as predefinições no resto dos campos.

3. Selecione **Autorizar**e iniciar sessão na sua conta Power BI. (Isto pode levar mais do que uma tentativa).

   ![Configurar as saídas para o trabalho de análise de fluxo](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Selecione **Guardar**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta da tarefa do Stream Analytics

1. Em **Topologia do Trabalho**, selecione **Consulta**.

2. Substitua `[YourInputAlias]` pelo alias de entrada da tarefa. Este tutorial utiliza **contosoinputs**.

3. Substitua `[YourOutputAlias]` pelo alias de saída da tarefa. Este tutorial utiliza **contosooutputs**.

   ![Configurar a consulta para o trabalho de análise de fluxo](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Selecione **Guardar**.

5. Feche o painel Consulta. Volta à vista dos recursos no Grupo de Recursos. Selecione o trabalho stream analytics. Este tutorial denomina-o **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

No trabalho stream analytics, selecione **Start** > **Now** > **Start**. Assim que a tarefa for iniciada com êxito, o estado da tarefa é alterado de **Parado** para **Em execução**.

Para configurar o relatório do Power BI, vai precisar de dados. Para isso, vai configurar o Power BI, depois de criar o dispositivo e executar a aplicação de simulação do dispositivo.

## <a name="run-simulated-device-app"></a>Executar aplicação de dispositivo simulado

Na parte 1 deste tutorial, configura um dispositivo para simular utilizando um dispositivo IoT. Nesta secção, descarrega a aplicação de consola .NET que simula esse dispositivo enviando mensagens de dispositivo-para-nuvem para um hub IoT (assumindo que ainda não descarregou a app e os recursos na Parte 1).

Esta aplicação envia mensagens para cada um dos diferentes métodos de encaminhamento de mensagens. Há também uma pasta no download que contém o modelo completo do Gestor de Recursos azure e o ficheiro de parâmetros, bem como os scripts Azure CLI e PowerShell.

Se não descarregou os ficheiros do repositório na Parte 1 deste tutorial, vá em frente e descarregue-os agora a partir da Simulação do [Dispositivo IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). A seleção deste link descarrega um repositório com várias aplicações no mesmo; a solução que procura é iot-hub/Tutorials/Routing/IoT_SimulatedDevice.sln. 

Clique duas vezes no ficheiro de solução (IoT_SimulatedDevice.sln) para abrir o código no Estúdio Visual e, em seguida, abra Program.cs. Substitua `{your hub name}` pelo nome do anfitrião do hub IoT. O formato do nome do anfitrião do hub IoT é **{iot-hub-name}.azure-devices.net**. Para este tutorial, o nome do anfitrião do hub é **ContosoTestHub.azure-devices.net**. Em seguida, substitua `{your device key}` pela chave de dispositivo que guardou anteriormente quando configurou o dispositivo simulado. 

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

   ![Os e-mails resultantes](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   Este resultado significa que as seguintes declarações são verdadeiras. 

   * O encaminhamento para a fila do Service Bus está a funcionar corretamente.
   * A Aplicação Lógica que obtém a mensagem da fila do Service Bus está a funcionar corretamente.
   * O conector da Aplicação Lógica para o Outlook está a funcionar corretamente. 

2. No [portal Azure,](https://portal.azure.com)selecione **grupos de Recursos** e selecione o seu Grupo de Recursos. Este tutorial utiliza **ContosoResources**. 

    Selecione a conta de armazenamento, selecione **Contentores**e, em seguida, selecione o Recipiente. Este tutorial utiliza **contosoresults**. Deverá ver uma pasta e poderá pesquisar através dos diretórios até ver um ou mais ficheiros. Abra um desses ficheiros; contêm as entradas encaminhadas para a conta de armazenamento. 

   ![Os ficheiros de resultados no armazenamento](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Este resultado significa que a seguinte declaração é verdadeira.

   * O encaminhamento para a conta de armazenamento está a funcionar corretamente.

Agora, com a aplicação ainda em funcionamento, configurar a visualização do Power BI para ver as mensagens a passar pelo encaminhamento padrão.

## <a name="set-up-the-power-bi-visualizations"></a>Configurar as visualizações do Power BI

1. Inicie sessão na sua conta do [Power BI](https://powerbi.microsoft.com/).

2. Aceda a **Áreas de Trabalho** e selecione a área de trabalho que definiu quando criou a saída da tarefa do Stream Analytics. Este tutorial utiliza **A Minha Área de Trabalho**. 

3. Selecione **Conjuntos**de Dados . Se não tiver conjuntos de dados, aguarde alguns minutos e verifique novamente.

   Deverá ver o conjunto de dados que especificou quando criou a saída da tarefa do Stream Analytics. Este tutorial utiliza **contosodataset**. (Pode demorar 5 a 10 minutos para que o conjunto de dados seja apresentado na primeira vez.)

4. Em **AÇÕES**, selecione o primeiro ícone para criar um relatório.

   ![Espaço de trabalho power BI com ações e ícone de relatório destacado](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Crie um gráfico de linhas para mostrar a temperatura em tempo real ao longo do tempo.

   * Na página de criação do relatório, adicione um gráfico de linha selecionando o ícone do gráfico de linha.

     ![As visualizações e campos](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * No painel **Campos**, expanda a tabela que especificou quando criou a saída da tarefa do Stream Analytics. Este tutorial utiliza **contosotable**.

   * Arraste **EventEnqueuedUtcTime** para **Eixo** no painel **Visualizações**.

   * Arraste **temperatura** para **Valores**.

   É criado um gráfico de linhas. O eixo X apresenta a data e hora no fuso horário UTC. O eixo Y apresenta a temperatura do sensor.

6. Crie outro gráfico de linhas para mostrar a humidade em tempo real ao longo do tempo. Para configurar o segundo gráfico, siga o mesmo processo para o primeiro gráfico, colocando **eventEnqueuedUtcTime** no eixo x (**Eixo**) e **humidade** no eixo y **(Valores).**

   ![O relatório final do Power BI com as duas tabelas](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Selecione **Guardar** para guardar o relatório, inserindo um nome para o relatório se solicitado.

Poderá ver os dados em ambos os gráficos. Este resultado significa que as seguintes declarações são verdadeiras:

   * O encaminhamento para o ponto final predefinido está a funcionar corretamente.
   * A tarefa do Azure Stream Analytics está a transmitir corretamente.
   * A Visualização do Power BI está configurada corretamente.

Pode refrescar as tabelas para ver os dados mais recentes selecionando o botão Refresh na parte superior da janela Power BI. 

## <a name="clean-up-resources"></a>Limpar recursos 

Se quiser remover todos os recursos Do Azure que criou através de ambas as partes deste tutorial, elimine o grupo de recursos. Esta ação também elimina todos os recursos contidos no grupo. Neste caso, remove o hub IoT, o espaço de nomes e a fila do Service Bus, a Aplicação Lógica, a conta de armazenamento e o próprio grupo de recursos. Também pode remover os recursos do Power BI e limpar os e-mails enviados durante o tutorial.

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Limpar os recursos na visualização do Power BI

Inicie sessão na sua conta do [Power BI](https://powerbi.microsoft.com/). Vá para a área de trabalho. Este tutorial utiliza **A Minha Área de Trabalho**. Para remover a visualização do Power BI, vá a DataSets e selecione o ícone do lixo para eliminar o conjunto de dados. Este tutorial utiliza **contosodataset**. Quando remover o conjunto de dados, o relatório é também removido.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Use o Azure CLI para limpar recursos

Para remover o grupo de recursos, utilize o comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup`foi definido para **ContosoRecursos** de volta no início deste tutorial.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Use a PowerShell para limpar recursos

Para remover o grupo de recursos, utilize o comando [Remove-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) `$resourceGroup`foi definido para **ContosoRecursos** de volta no início deste tutorial.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

### <a name="clean-up-test-emails"></a>Limpar e-mails de teste

Também pode querer eliminar a quantidade de e-mails na sua caixa de entrada que foram gerados através da Aplicação Lógica enquanto a aplicação do dispositivo estava em execução.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial de 2 partes, aprendeu a usar o encaminhamento de mensagens para direcionar as mensagens Do IoT Hub para diferentes destinos, executando as seguintes tarefas.  

**Parte I: Criar recursos, configurar o encaminhamento de mensagens**
> [!div class="checklist"]
> * Criar os recursos, um hub IoT, uma conta de armazenamento, uma fila de ônibus de serviço, e um dispositivo simulado.
> * Configure os pontos finais e as rotas de mensagem no IoT Hub para a conta de armazenamento e fila de ônibus de serviço.

**Parte II: Enviar mensagens para o centro, ver resultados direcionados**
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
> [Configurar e usar métricas e diagnósticos com um Hub IoT](tutorial-use-metrics-and-diags.md)
