---
title: Visualização de dados em tempo real de data frm Azure IoT Hub – Power BI
description: Utilize o Power BI para visualizar os dados de temperatura e humidade recolhidos do sensor e enviados para o seu hub Azure IoT.
author: robinsh
keywords: visualização de dados em tempo real, visualização de dados ao vivo, visualização de dados de sensores
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: f0b909d10790511408e090546fd3359889ea5aca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73954634"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualizar dados de sensores em tempo real do Hub Azure IoT usando O Power BI

![Diagrama de ponta a ponta](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que irá aprender

Aprende a visualizar dados de sensores em tempo real que o seu hub Azure IoT recebe utilizando o Power BI. Se quiser visualizar os dados no seu hub IoT com uma aplicação web, consulte [Utilize uma aplicação web para visualizar dados de sensores em tempo real do Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>O que faz

* Prepare o seu hub IoT para acesso a dados adicionando um grupo de consumidores.

* Crie, configure e execute um trabalho de Stream Analytics para transferência de dados do seu hub IoT para a sua conta Power BI.

* Crie e publique um relatório power BI para visualizar os dados.

## <a name="what-you-need"></a>Do que precisa

* Complete o tutorial de [simulador online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo; por exemplo, [Raspberry Pi com nó.js](iot-hub-raspberry-pi-kit-node-get-started.md). Estes artigos abrangem os seguintes requisitos:
  
  * Uma subscrição ativa do Azure.
  * Um hub Azure IoT sob a sua assinatura.
  * Uma aplicação de cliente que envia mensagens para o seu hub Azure IoT.

* Uma conta do Power BI. (Experimente[o Power BI gratuitamente)](https://powerbi.microsoft.com/)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Criar, configurar e executar um trabalho de Stream Analytics

Vamos começar por criar um trabalho de Stream Analytics. Depois de criar o trabalho, define as inputs, saídas e a consulta usada para recuperar os dados.

### <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

1. No [portal Azure,](https://portal.azure.com)selecione **Criar um recurso** > **Internet of Things** > **Stream Analytics.**

2. Introduza as seguintes informações para a tarefa.

   **Nome da tarefa**: o nome da tarefa. O nome tem de ser globalmente exclusivo.

   **Grupo de recursos:** Utilize o mesmo grupo de recursos que o seu hub IoT utiliza.

   **Localização**: Utilize a mesma localização que o seu grupo de recursos.

   ![Criar um trabalho de Stream Analytics em Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. Selecione **Criar**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada à tarefa do Stream Analytics

1. Abra o trabalho de Stream Analytics.

2. Em **Topologia de Trabalho,** selecione **Inputs**.

3. No painel de **entradas,** selecione **Adicionar entrada**de fluxo e, em seguida, selecione **IoT Hub** da lista de drop-down. No novo painel de entrada, introduza as seguintes informações:

   **Pseudónimo de entrada**: Introduza um pseudónimo único para a entrada.

   **Forneça ioT Hub a partir da sua subscrição**: Selecione este botão de rádio.

   **Subscrição**: Selecione a subscrição Azure que está a utilizar para este tutorial.

   **IoT Hub**: Selecione o IoT Hub que está a usar para este tutorial.

   **Ponto final**: selecione **Mensagens**.

   **Nome**da política de acesso partilhado : Selecione o nome da política de acesso partilhado que pretende utilizar para o seu hub IoT. Para este tutorial, pode selecionar *o serviço.* A política de *serviços* é criada por padrão em novos hubs IoT e concede permissão para enviar e receber em pontos finais do lado da nuvem expostos pelo hub IoT. Para saber mais, consulte [o controlo de acesso e as permissões.](iot-hub-devguide-security.md#access-control-and-permissions)

   **Chave da política**de acesso partilhado : Este campo é preenchido automaticamente com base na sua seleção para o nome da política de acesso partilhado.

   **Grupo de consumidores**: Selecione o grupo de consumidores que criou anteriormente.

   Deixe todos os outros campos à sua falta.

   ![Adicione uma entrada a um trabalho de Stream Analytics em Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. Selecione **Guardar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída à tarefa do Stream Analytics

1. Em **Topologia de Trabalho,** selecione **Saídas**.

2. No painel **de saídas,** selecione **Adicionar** e **Power BI**.

3. No **Power BI - Novo** painel de saída, selecione **Autorizar** e siga as instruções para iniciar sessão na sua conta Power BI.

4. Depois de ter assinado o Power BI, insira as seguintes informações:

   **Pseudónimo de saída**: Um pseudónimo único para a saída.

   **Espaço de trabalho em grupo**: Selecione o espaço de trabalho do grupo alvo.

   **Nome do conjunto de dados**: Introduza um nome de conjunto de dados.

   **Nome da mesa**: Introduza um nome de mesa.

   ![Adicione uma saída a um trabalho de Stream Analytics em Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. Selecione **Guardar**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta da tarefa do Stream Analytics

1. Em **Topologia do Trabalho**, selecione **Consulta**.

2. Substitua `[YourInputAlias]` pelo alias de entrada da tarefa.

3. Substitua `[YourOutputAlias]` pelo alias de saída da tarefa.

   ![Adicione uma consulta a um trabalho de Stream Analytics em Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. Selecione **Guardar**.

### <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

No trabalho stream analytics, selecione **Visão Geral**e, em seguida, selecione **Start** > **Now** > **Start**. Assim que a tarefa for iniciada com êxito, o estado da tarefa é alterado de **Parado** para **Em execução**.

![Executar um trabalho de Stream Analytics em Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Criar e publicar um relatório power bi para visualizar os dados

1. Certifique-se de que a aplicação da amostra está a funcionar no seu dispositivo. Caso contrário, pode consultar os tutoriais em [configuração do seu dispositivo](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Inicie sessão na sua conta do [Power BI](https://powerbi.microsoft.com/en-us/).

3. Selecione o espaço de trabalho que utilizou, **My Workspace**.

4. Selecione **Conjuntos**de Dados .

   Deve ver o conjunto de dados que especificou quando criou a saída para o trabalho do Stream Analytics.

5. Para o conjunto de dados que criou, selecione **Adicionar Relatório** (o primeiro ícone à direita do nome dataset).

   ![Criar um relatório microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. Crie um gráfico de linhas para mostrar a temperatura em tempo real ao longo do tempo.

   1. No painel de **Visualizações** da página de criação do relatório, selecione o ícone do gráfico de linha para adicionar um gráfico de linha.

   2. No painel **Campos**, expanda a tabela que especificou quando criou a saída da tarefa do Stream Analytics.

   3. Arraste **EventEnqueuedUtcTime** para **Eixo** no painel **Visualizações**.

   4. Arraste **temperatura** para **Valores**.

      É criado um gráfico de linhas. O eixo X apresenta a data e hora no fuso horário UTC. O eixo Y apresenta a temperatura do sensor.

      ![Adicione um gráfico de linha para a temperatura a um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. Crie outro gráfico de linhas para mostrar a humidade em tempo real ao longo do tempo. Para isso, siga os mesmos passos acima e coloque **eventEnqueuedUtcTime** no eixo x e **humidade** no eixo y.

   ![Adicione um gráfico de linha para a humidade a um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Selecione **Guardar** para guardar o relatório.

9. Selecione **Relatórios** sobre o painel esquerdo e, em seguida, selecione o relatório que acabou de criar.

10. Selecione **File** > **Publish para web**.

11. Selecione **Criar código incorporado**e, em seguida, selecione **Publicar**.

É-lhe fornecido o link do relatório que pode partilhar com qualquer pessoa para o acesso ao relatório e um código que pode usar para integrar o relatório no seu blog ou website.

![Publique um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

A Microsoft também oferece as [aplicações móveis Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) para visualização e interação com os seus dashboards Power BI e relatórios no seu dispositivo móvel.

## <a name="next-steps"></a>Passos seguintes

Usaste com sucesso o Power BI para visualizar dados de sensores em tempo real do teu hub Azure IoT.

Para mais uma forma de visualizar os dados do Azure IoT Hub, consulte [Use uma aplicação web para visualizar dados de sensores em tempo real do Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
