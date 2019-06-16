---
title: Visualização de dados em tempo real dos dados de sensor do IoT Hub do Azure – Power BI | Documentos da Microsoft
description: Utilize o Power BI para visualizar dados de temperatura e humidade recolhidos do sensor e enviados ao seu hub IoT do Azure.
author: robinsh
keywords: visualização de dados em tempo real, visualização de dados em direto, visualização de dados do sensor
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: 7deb1b501d30c8af0cb190f4722d46435afa9b8e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065825"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualizar dados de sensores em tempo real do IoT Hub com o Power BI

![Diagrama de ponto a ponto](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que irá aprender

Saiba como visualizar dados de sensores em tempo real que recebe o seu hub IoT do Azure com o Power BI. Se quiser tentar visualizar os dados do seu hub IoT com uma aplicação web, veja [utilizar uma aplicação web para visualizar dados de sensores em tempo real do IoT Hub do Azure](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>O que fazer

* Prepare seu IoT hub para acesso a dados ao adicionar um grupo de consumidores.

* Criar, configurar e executar uma tarefa de Stream Analytics para transferência de dados do seu hub IoT para a sua conta do Power BI.

* Criar e publicar um relatório do Power BI para visualizar os dados.

## <a name="what-you-need"></a>Do que precisa

* Concluir o [simulador online de Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) tutorial ou um dos tutoriais dispositivo; por exemplo, [Raspberry Pi com node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Estes artigos abrangem os seguintes requisitos:
  
  * Uma subscrição ativa do Azure.
  * Um hub IoT do Azure com a sua subscrição.
  * Uma aplicação de cliente que envia mensagens para o seu hub IoT do Azure.

* Uma conta do Power BI. ([Experimente o Power BI gratuitamente](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Criar, configurar e executar uma tarefa do Stream Analytics

Vamos começar por criar uma tarefa do Stream Analytics. Depois de criar a tarefa, define as entradas, saídas e a consulta usada para recuperar os dados.

### <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

1. Na [portal do Azure](https://portal.azure.com), selecione **criar um recurso** > **Internet das coisas** > **tarefa do Stream Analytics**.

2. Introduza as seguintes informações para a tarefa.

   **Nome da tarefa**: O nome da tarefa. O nome tem de ser globalmente exclusivo.

   **Grupo de recursos**: Utilize o mesmo grupo de recursos que utiliza o seu hub IoT.

   **Localização**: Utilize a mesma localização que o grupo de recursos.

   ![Criar uma tarefa do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. Selecione **Criar**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada à tarefa do Stream Analytics

1. Abra a tarefa do Stream Analytics.

2. Sob **topologia da tarefa**, selecione **entradas**.

3. Na **entradas** painel, selecione **Adicionar entrada de fluxo**, em seguida, selecione **IoT Hub** na lista pendente. No painel de entrada, introduza as seguintes informações:

   **O alias de entrada**: Introduza um alias de exclusivo para a entrada.

   **Fornecer o IoT Hub da sua subscrição**: Selecione o botão de opção.

   **Subscrição**: Selecione a subscrição do Azure que está a utilizar para este tutorial.

   **IoT Hub**: Selecione o IoT Hub está a utilizar para este tutorial.

   **Ponto final**: Selecione **mensagens**.

   **Nome da política de acesso partilhado**: Selecione o nome da política de acesso partilhado que pretender que a tarefa de Stream Analytics a utilizar para o seu hub IoT. Para este tutorial, pode selecionar *serviço*. O *serviço* política é criada por predefinição em novos IoT hubs e concede permissão para enviar e receber em pontos de extremidade do lado do cloud expostos pelo IoT hub. Para obter mais informações, consulte [controlo de acesso e permissões](iot-hub-devguide-security.md#access-control-and-permissions).

   **Chave de política de acesso partilhado**: Este campo é preenchido automaticamente com base na sua seleção para o nome de política de acesso partilhado.

   **Grupo de consumidores**: Selecione o grupo de consumidores que criou anteriormente.

   Deixe todos os outros campos nas predefinições.

   ![Adicione uma entrada para uma tarefa do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. Selecione **Guardar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída à tarefa do Stream Analytics

1. Sob **topologia da tarefa**, selecione **saídas**.

2. Na **saídas** painel, selecione **Add** e **Power BI**.

3. Sobre o **Power BI - nova saída** painel, selecione **autorizar** e siga as instruções para iniciar sessão na sua conta do Power BI.

4. Depois de ter iniciado sessão Power BI, introduza as seguintes informações:

   **Alias de saída**: Um alias exclusivo para a saída.

   **Área de trabalho de grupo**: Selecione a área de trabalho de grupo de destino.

   **Nome do conjunto de dados**: Introduza um nome de conjunto de dados.

   **Nome da tabela**: Introduza um nome de tabela.

   ![Adicionar uma saída para uma tarefa do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. Selecione **Guardar**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta da tarefa do Stream Analytics

1. Em **Topologia do Trabalho**, selecione **Consulta**.

2. Substitua `[YourInputAlias]` pelo alias de entrada da tarefa.

3. Substitua `[YourOutputAlias]` pelo alias de saída da tarefa.

   ![Adicionar uma consulta a uma tarefa do Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. Selecione **Guardar**.

### <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

A tarefa de Stream Analytics, selecione **descrição geral**, em seguida, selecione **iniciar** > **agora** > **iniciar**. Assim que a tarefa for iniciada com êxito, o estado da tarefa é alterado de **Parado** para **Em execução**.

![Executar uma tarefa de Stream Analytics no Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Criar e publicar um relatório do Power BI para visualizar os dados

1. Certifique-se de que o aplicativo de exemplo está em execução no seu dispositivo. Se não, pode consultar os tutoriais sob [configurar o seu dispositivo](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Inicie sessão na sua conta do [Power BI](https://powerbi.microsoft.com/en-us/).

3. Selecione a área de trabalho utilizou, **minha área de trabalho**.

4. Selecione **conjuntos de dados**.

   Deverá ver o conjunto de dados que especificou quando criou a saída para a tarefa do Stream Analytics.

5. Para o conjunto de dados que criou, selecione **Adicionar relatório** (o primeiro ícone à direita do nome do conjunto de dados).

   ![Criar um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. Crie um gráfico de linhas para mostrar a temperatura em tempo real ao longo do tempo.

   1. Sobre o **visualizações** painel da página de criação do relatório, selecione o ícone de gráfico de linha para adicionar um gráfico de linhas.

   2. No painel **Campos**, expanda a tabela que especificou quando criou a saída da tarefa do Stream Analytics.

   3. Arraste **EventEnqueuedUtcTime** para **Eixo** no painel **Visualizações**.

   4. Arraste **temperatura** para **Valores**.

      É criado um gráfico de linhas. O eixo X apresenta a data e hora no fuso horário UTC. O eixo Y apresenta a temperatura do sensor.

      ![Adicionar um gráfico de linhas para temperatura a um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. Crie outro gráfico de linhas para mostrar a humidade em tempo real ao longo do tempo. Para tal, siga os passos acima e coloque **EventEnqueuedUtcTime** no eixo x e **humidade** no eixo y.

   ![Adicionar um gráfico de linhas para humidade a um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Selecione **guardar** para guardar o relatório.

9. Selecione **relatórios** no painel esquerdo e, em seguida, selecione o relatório que acabou de criar.

10. Selecione **arquivo** > **publicar na web**.

11. Selecione **criar código de incorporação**e, em seguida, selecione **Publish**.

É apresentada a ligação do relatório que pode partilhar com qualquer pessoa para acesso de relatório e um trecho de código que pode usar para integrar o relatório no seu blogue ou site.

![Publicar um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

A Microsoft também oferece a [aplicações móveis do Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) para ver e interagir com os seus relatórios e dashboards do Power BI no seu dispositivo móvel.

## <a name="next-steps"></a>Passos Seguintes

Utilizou com êxito Power BI para visualizar dados de sensores em tempo real do seu hub IoT do Azure.

Para outra forma de visualizar dados do IoT Hub do Azure, consulte [utilizar uma aplicação web para visualizar dados de sensores em tempo real do IoT Hub do Azure](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
