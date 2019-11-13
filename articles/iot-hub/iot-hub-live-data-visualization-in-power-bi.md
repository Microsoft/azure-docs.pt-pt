---
title: Visualização de dados em tempo real do Hub IoT do Azure de frm de dados – Power BI
description: Use Power BI para visualizar dados de temperatura e umidade que são coletados do sensor e enviados para o Hub IoT do Azure.
author: robinsh
keywords: visualização de dados em tempo real, visualização de dados dinâmicos, visualização de dados do sensor
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: f0b909d10790511408e090546fd3359889ea5aca
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954634"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualizar dados de sensor em tempo real do Hub IoT do Azure usando Power BI

![Diagrama de ponta a ponta](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que irá aprender

Você aprende a visualizar os dados de sensor em tempo real que o Hub IoT do Azure recebe usando Power BI. Se você quiser tentar visualizar os dados em seu hub IoT com um aplicativo Web, consulte [usar um aplicativo Web para visualizar dados de sensor em tempo real do Hub IOT do Azure](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>O que você faz

* Prepare seu hub IoT para acesso a dados adicionando um grupo de consumidores.

* Crie, configure e execute um trabalho de Stream Analytics para transferência de dados do Hub IoT para sua conta de Power BI.

* Crie e publique um relatório de Power BI para visualizar os dados.

## <a name="what-you-need"></a>Do que precisa

* Conclua o tutorial do [simulador online do Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo; por exemplo, [Raspberry Pi com node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Esses artigos abordam os seguintes requisitos:
  
  * Uma subscrição ativa do Azure.
  * Um hub IoT do Azure em sua assinatura.
  * Um aplicativo cliente que envia mensagens para o Hub IoT do Azure.

* Uma conta do Power BI. ([Tente Power bi gratuitamente](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Criar, configurar e executar um trabalho de Stream Analytics

Vamos começar criando um trabalho de Stream Analytics. Depois de criar o trabalho, você define as entradas, as saídas e a consulta usada para recuperar os dados.

### <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

1. Na [portal do Azure](https://portal.azure.com), selecione **criar um recurso** > **Internet das coisas** > **trabalho Stream Analytics**.

2. Introduza as seguintes informações para a tarefa.

   **Nome da tarefa**: o nome da tarefa. O nome tem de ser globalmente exclusivo.

   **Grupo de recursos**: Use o mesmo grupo de recursos que o Hub IOT usa.

   **Local**: Use o mesmo local que o seu grupo de recursos.

   ![Criar um trabalho de Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. Selecione **Criar**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada à tarefa do Stream Analytics

1. Abra o trabalho Stream Analytics.

2. Em **topologia do trabalho**, selecione **entradas**.

3. No painel **entradas** , selecione **Adicionar entrada de fluxo**e, em seguida, selecione **Hub IOT** na lista suspensa. No painel novo entrada, insira as seguintes informações:

   **Alias de entrada**: Insira um alias exclusivo para a entrada.

   **Fornecer o Hub IOT de sua assinatura**: Selecione este botão de opção.

   **Assinatura**: selecione a assinatura do Azure que você está usando para este tutorial.

   **Hub IOT**: selecione o Hub IOT que você está usando para este tutorial.

   **Ponto final**: selecione **Mensagens**.

   **Nome da política de acesso compartilhado**: selecione o nome da política de acesso compartilhado que você deseja que o trabalho de Stream Analytics use para o Hub IOT. Para este tutorial, você pode selecionar *serviço*. A política de *serviço* é criada por padrão em novos hubs IOT e concede permissão para enviar e receber em pontos de extremidade do lado da nuvem expostos pelo Hub IOT. Para saber mais, consulte [permissões e controle de acesso](iot-hub-devguide-security.md#access-control-and-permissions).

   **Chave de política de acesso compartilhado**: esse campo é preenchido automaticamente com base na sua seleção para o nome da política de acesso compartilhado.

   **Grupo de consumidores**: selecione o grupo de consumidores que você criou anteriormente.

   Deixe todos os outros campos em seus padrões.

   ![Adicionar uma entrada a um trabalho Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. Selecione **Guardar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída à tarefa do Stream Analytics

1. Em **topologia do trabalho**, selecione **saídas**.

2. No painel **saídas** , selecione **Adicionar** e **Power bi**.

3. No painel **Power bi-nova saída** , selecione **autorizar** e siga os prompts para entrar em sua conta do Power bi.

4. Depois de entrar no Power BI, insira as seguintes informações:

   **Alias de saída**: um alias exclusivo para a saída.

   **Espaço de trabalho de grupo**: Selecione seu espaço de trabalho do grupo de destino.

   **Nome do conjunto**de um: Insira um nome de conjunto de um.

   **Nome da tabela**: Insira um nome de tabela.

   ![Adicionar uma saída a um trabalho de Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. Selecione **Guardar**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta da tarefa do Stream Analytics

1. Em **Topologia do Trabalho**, selecione **Consulta**.

2. Substitua `[YourInputAlias]` pelo alias de entrada da tarefa.

3. Substitua `[YourOutputAlias]` pelo alias de saída da tarefa.

   ![Adicionar uma consulta a um trabalho Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. Selecione **Guardar**.

### <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

No trabalho Stream Analytics, selecione **visão geral**e, em seguida, selecione **iniciar** > **agora** > **Iniciar**. Assim que a tarefa for iniciada com êxito, o estado da tarefa é alterado de **Parado** para **Em execução**.

![Executar um trabalho de Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Criar e publicar um relatório de Power BI para visualizar os dados

1. Verifique se o aplicativo de exemplo está em execução no seu dispositivo. Caso contrário, você pode consultar os tutoriais em [configurar seu dispositivo](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Inicie sessão na sua conta do [Power BI](https://powerbi.microsoft.com/en-us/).

3. Selecione o espaço de trabalho usado, **meu espaço de trabalho**.

4. Selecione **conjuntos**de os.

   Você deve ver o conjunto de resultados que você especificou quando criou a saída para o trabalho de Stream Analytics.

5. Para o conjunto de um que você criou, selecione **Adicionar relatório** (o primeiro ícone à direita do nome do conjunto de um).

   ![Criar um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. Crie um gráfico de linhas para mostrar a temperatura em tempo real ao longo do tempo.

   1. No painel **visualizações** da página de criação de relatório, selecione o ícone de gráfico de linhas para adicionar um gráfico de linhas.

   2. No painel **Campos**, expanda a tabela que especificou quando criou a saída da tarefa do Stream Analytics.

   3. Arraste **EventEnqueuedUtcTime** para **Eixo** no painel **Visualizações**.

   4. Arraste **temperatura** para **Valores**.

      É criado um gráfico de linhas. O eixo X apresenta a data e hora no fuso horário UTC. O eixo Y apresenta a temperatura do sensor.

      ![Adicionar um gráfico de linhas para temperatura a um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. Crie outro gráfico de linhas para mostrar a humidade em tempo real ao longo do tempo. Para fazer isso, siga as mesmas etapas acima e coloque **EventEnqueuedUtcTime** no eixo x e **umidade** no eixo y.

   ![Adicionar um gráfico de linhas para umidade a um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Selecione **salvar** para salvar o relatório.

9. Selecione **relatórios** no painel esquerdo e, em seguida, selecione o relatório que você acabou de criar.

10. Selecione **arquivo** > **publicar na Web**.

11. Selecione **criar código de inserção**e, em seguida, selecione **publicar**.

Você forneceu o link de relatório que pode compartilhar com qualquer pessoa para acesso de relatório e um trecho de código que você pode usar para integrar o relatório em seu blog ou site.

![Publicar um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

A Microsoft também oferece os [Power bi aplicativos móveis](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) para exibir e interagir com seus painéis de Power bi e relatórios em seu dispositivo móvel.

## <a name="next-steps"></a>Passos seguintes

Você usou com êxito Power BI para visualizar dados de sensor em tempo real do Hub IoT do Azure.

Para outra maneira de Visualizar dados do Hub IoT do Azure, consulte [usar um aplicativo Web para visualizar dados de sensor em tempo real do Hub IOT do Azure](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
