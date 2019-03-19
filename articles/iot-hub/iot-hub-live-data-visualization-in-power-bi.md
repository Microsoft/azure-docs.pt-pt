---
title: Visualização de dados em tempo real dos dados de sensor do IoT Hub do Azure – Power BI | Documentos da Microsoft
description: Utilize o Power BI para visualizar dados de temperatura e humidade recolhidos do sensor e enviados ao seu hub IoT do Azure.
author: rangv
keywords: visualização de dados em tempo real, visualização de dados em direto, visualização de dados do sensor
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 240895f4e8567426fec8e270b96b9e6528392a46
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103082"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualizar dados de sensores em tempo real do IoT Hub com o Power BI

![Diagrama de ponto a ponto](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)


[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que irá aprender

Saiba como visualizar dados de sensores em tempo real que recebe o seu hub IoT do Azure com o Power BI. Se quiser experimentar toe visualize os dados no seu hub IoT com aplicações Web, consulte [utilize as aplicações de Web do Azure para visualizar dados de sensores em tempo real do IoT Hub do Azure](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>O que fazer

* Prepare seu IoT hub para acesso a dados ao adicionar um grupo de consumidores.

* Criar, configurar e executar uma tarefa de Stream Analytics para transferência de dados do seu hub IoT para a sua conta do Power BI.

* Criar e publicar um relatório do Power BI para visualizar os dados.

## <a name="what-you-need"></a>Do que precisa

* Tutorial [configurar o seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) concluída que abrange os seguintes requisitos:
  
  * Uma subscrição ativa do Azure.
  * Um hub IoT do Azure com a sua subscrição.
  * Uma aplicação de cliente que envia mensagens para o seu hub IoT do Azure.

* Uma conta do Power BI. ([Experimente o Power BI gratuitamente](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Criar, configurar e executar uma tarefa do Stream Analytics

Vamos começar por criar uma tarefa do Stream Analytics. Depois de criar a tarefa, define as entradas, saídas e a consulta usada para recuperar os dados.

### <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

1. No [portal do Azure](https://portal.azure.com), clique em **Criar um recurso** > **Internet das Coisas** > **Tarefa do Stream Analytics**.

2. Introduza as seguintes informações para a tarefa.

   **Nome da tarefa**: O nome da tarefa. O nome tem de ser globalmente exclusivo.

   **Grupo de recursos**: Utilize o mesmo grupo de recursos que utiliza o seu hub IoT.

   **Localização**: Utilize a mesma localização que o grupo de recursos.

   **Afixar ao dashboard**: Marque esta opção para facilitar o acesso ao seu hub IoT a partir do dashboard.

   ![Criar uma tarefa do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/2_create-stream-analytics-job-azure.png)

3. Clique em **Criar**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada à tarefa do Stream Analytics

1. Abra a tarefa do Stream Analytics.

2. Em **Topologia da Tarefa**, clique em **Entradas**.

3. Na **entradas** painel, clique em **Adicionar entrada de fluxo**e, em seguida, introduza as seguintes informações:

   **O alias de entrada**: O alias exclusivo para a entrada e selecione **definições de fornecer o IoT Hub manualmente** abaixo.

   **origem**: Selecione **IoT hub**.
   
   **Ponto final**: Clique em **mensagens**.

   **Grupo de consumidores**: Selecione o grupo de consumidores que acabou de criar.

4. Clique em **Criar**.

   ![Adicione uma entrada para uma tarefa do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/3_add-input-to-stream-analytics-job-azure.png)

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída à tarefa do Stream Analytics

1. Em **Topologia da Tarefa**, clique em **Saídas**.

2. Na **saídas** painel, clique em **Add** e **Power BI**e, em seguida, introduza as seguintes informações:

   **Alias de saída**: O alias exclusivo para a saída.

   **Área de trabalho de grupo**: Selecione a área de trabalho de grupo de destino.

   **Nome do conjunto de dados**: Introduza um nome de conjunto de dados.

   **Nome da tabela**: Introduza um nome de tabela.

3. Clique em **autorizar**e, em seguida, inicie sessão na sua conta do Power BI.

4. Clique em **Criar**.

   ![Adicionar uma saída para uma tarefa do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/4_add-output-to-stream-analytics-job-azure.png)

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta da tarefa do Stream Analytics

1. Em **Topologia de Tarefas**, clique em **Consulta**.

2. Substitua `[YourInputAlias]` pelo alias de entrada da tarefa.

3. Substitua `[YourOutputAlias]` pelo alias de saída da tarefa.

4. Clique em **Guardar**.

   ![Adicionar uma consulta a uma tarefa do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/5_add-query-stream-analytics-job-azure.png)

### <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

Na tarefa do Stream Analytics, clique em **Iniciar** > **Agora** > **Iniciar**. Assim que a tarefa for iniciada com êxito, o estado da tarefa é alterado de **Parado** para **Em execução**.

![Executar uma tarefa de Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/6_run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Criar e publicar um relatório do Power BI para visualizar os dados

1. Certifique-se de que o aplicativo de exemplo está em execução no seu dispositivo. Se não, pode consultar os tutoriais sob [configurar o seu dispositivo](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Inicie sessão na sua conta do [Power BI](https://powerbi.microsoft.com/en-us/).

3. Clique em área de trabalho utilizou, **minha área de trabalho**.

4. Clique em **Conjuntos de dados**.

   Deverá ver o conjunto de dados que especificou quando criou a saída para a tarefa do Stream Analytics.

5. Para o conjunto de dados que criou, clique em **Adicionar relatório** (o primeiro ícone à direita do nome do conjunto de dados).

   ![Criar um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/7_create-power-bi-report-microsoft.png)

6. Crie um gráfico de linhas para mostrar a temperatura em tempo real ao longo do tempo.

   1. Na página de criação do relatório, adicione um gráfico de linhas.

   2. No painel **Campos**, expanda a tabela que especificou quando criou a saída da tarefa do Stream Analytics.
   
   3. Arraste **EventEnqueuedUtcTime** para **Eixo** no painel **Visualizações**.
   
   4. Arraste **temperatura** para **Valores**.

      É criado um gráfico de linhas. O eixo X apresenta a data e hora no fuso horário UTC. O eixo Y apresenta a temperatura do sensor.

      ![Adicionar um gráfico de linhas para temperatura a um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/8_add-line-chart-for-temperature-to-power-bi-report-microsoft.png)

7. Crie outro gráfico de linhas para mostrar a humidade em tempo real ao longo do tempo. Para tal, siga os passos acima e coloque **EventEnqueuedUtcTime** no eixo x e **humidade** no eixo y.

   ![Adicionar um gráfico de linhas para humidade a um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/9_add-line-chart-for-humidity-to-power-bi-report-microsoft.png)

8. Clique em **Guardar** para guardar o relatório.

9. Clique em **relatórios** no painel esquerdo e, em seguida, clique o relatório que acabou de criar.

10. Clique em **arquivo** > **publicar na web**.

11. Clique em **criar código de incorporação**e, em seguida, clique em **Publish**.

É apresentada a ligação do relatório que pode partilhar com qualquer pessoa para acesso de relatório e um trecho de código para integrar o relatório no seu blogue ou site.

![Publicar um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/10_publish-power-bi-report-microsoft.png)

A Microsoft também oferece a [aplicações móveis do Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) para ver e interagir com os seus relatórios e dashboards do Power BI no seu dispositivo móvel.

## <a name="next-steps"></a>Passos Seguintes

Utilizou com êxito Power BI para visualizar dados de sensores em tempo real do seu hub IoT do Azure.

Há uma maneira alternativa para visualizar dados do IoT Hub do Azure. Ver [utilize as aplicações de Web do Azure para visualizar dados de sensores em tempo real do IoT Hub do Azure](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
