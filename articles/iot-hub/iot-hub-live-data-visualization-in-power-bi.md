---
title: Visualização de dados em tempo real de dados frm Azure IoT Hub – Power BI
description: Utilize o Power BI para visualizar os dados de temperatura e humidade que são recolhidos do sensor e enviados para o seu hub Azure IoT.
author: robinsh
keywords: visualização de dados em tempo real, visualização de dados ao vivo, visualização de dados de sensores
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/08/2020
ms.author: robinsh
ms.openlocfilehash: ed429d2f584da20439b0cb0eedcf4742b9ae4599
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84634514"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualizar dados de sensores em tempo real do Azure IoT Hub utilizando o Power BI

![Diagrama de ponta a ponta](./media/iot-hub-live-data-visualization-in-power-bi/end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que irá aprender

Aprende a visualizar dados de sensores em tempo real que o seu hub Azure IoT recebe utilizando o Power BI. Se quiser tentar visualizar os dados no seu hub IoT com uma aplicação web, consulte [utilize uma aplicação web para visualizar dados de sensores em tempo real a partir do Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>O que faz

* Prepare o seu hub IoT para acesso aos dados adicionando um grupo de consumidores.

* Crie, configuure e execute um trabalho stream Analytics para transferência de dados do seu hub IoT para a sua conta Power BI.

* Crie e publique um relatório Power BI para visualizar os dados.

## <a name="what-you-need"></a>O que precisa

* Complete o tutorial [de simulador on-line Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo; por exemplo, [Raspberry Pi com node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Estes artigos abrangem os seguintes requisitos:
  
  * Uma subscrição ativa do Azure.
  * Um hub Azure IoT sob a sua assinatura.
  * Uma aplicação de cliente que envia mensagens para o seu hub Azure IoT.

* Uma conta do Power BI. (Experimente[o Power BI gratuitamente)](https://powerbi.microsoft.com/)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Criar, configurar e executar um trabalho stream Analytics

Vamos começar por criar um trabalho de Stream Analytics. Depois de criar o trabalho, define as entradas, saídas e a consulta utilizada para recuperar os dados.

### <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

1. No [portal Azure,](https://portal.azure.com) **selecione Criar um recurso**de internet de  >  **coisas**stream  >  **analytics .**

2. Introduza as seguintes informações para a tarefa.

   **Nome da tarefa**: o nome da tarefa. O nome tem de ser globalmente exclusivo.

   **Grupo de recursos**: Utilize o mesmo grupo de recursos que o seu hub IoT utiliza.

   **Localização**: Utilize a mesma localização que o seu grupo de recursos.

   ![Criar um trabalho stream analytics em Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job.png)

3. Selecione **Criar**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada à tarefa do Stream Analytics

1. Abra o trabalho stream analytics.

2. Em **Topologia da tarefa**, selecione **Entradas**.

3. No painel **de entradas,** selecione **Adicione a entrada de fluxo**e, em seguida, selecione **IoT Hub** da lista de drop-down. No novo painel de entrada, insira as seguintes informações:

   **Inserir pseudónimo :** Introduza um pseudónimo único para a entrada.

   **Selecione IoT Hub a partir da sua subscrição**: Selecione este botão de rádio.

   **Subscrição**: Selecione a subscrição Azure que está a usar para este tutorial.

   **IoT Hub**: Selecione o Hub IoT que está a usar para este tutorial.

   **Ponto final**: selecione **Mensagens**.

   **Nome da política de acesso partilhado**: Selecione o nome da política de acesso partilhado que pretende que o trabalho stream Analytics seja utilizado para o seu hub IoT. Para este tutorial, pode selecionar *o serviço.* A política de *serviços* é criada por padrão em novos hubs IoT e concede permissão para enviar e receber em pontos finais do lado da nuvem expostos pelo hub IoT. Para saber mais, consulte [o controlo de acesso e permissões.](iot-hub-devguide-security.md#access-control-and-permissions)

   **Chave da política**de acesso partilhado : Este campo é preenchido automaticamente com base na sua seleção para o nome da política de acesso partilhado.

   **Grupo de consumidores**: Selecione o grupo de consumidores que criou anteriormente.

   Deixe todos os outros campos à sua falta.

   ![Adicione uma entrada a um trabalho de Stream Analytics em Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job.png)

4. Selecione **Guardar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída à tarefa do Stream Analytics

1. Em **Topologia da tarefa**, selecione **Saídas**.

2. No painel **outputs,** **selecione Add** and **Power BI**.

3. No **Power BI - Novo** painel de saída, selecione **Authorize** e siga as instruções para iniciar scontabilidade de POWER BI.

4. Depois de ter assinado no Power BI, insira as seguintes informações:

   **Pseudónimo de saída**: Um pseudónimo único para a saída.

   **Espaço de trabalho em grupo**: Selecione o seu espaço de trabalho de grupo alvo.

   **Nome do conjunto de dados**: Introduza um nome de conjunto de dados.

   **Nome da tabela**: Introduza um nome de mesa.

   **Modo de autenticação**: Deixe à padrão.

   ![Adicione uma saída a um trabalho de Stream Analytics em Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job.png)

5. Selecione **Guardar**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta da tarefa do Stream Analytics

1. Em **Topologia da tarefa**, selecione **Consulta**.

2. Substitua `[YourInputAlias]` pelo alias de entrada da tarefa.

3. Substitua `[YourOutputAlias]` pelo alias de saída da tarefa.

   ![Adicione uma consulta a um trabalho de Stream Analytics em Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-to-stream-analytics-job.png)

4. **Selecione Guardar consulta**.

### <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

No trabalho Stream Analytics, selecione **Overview**e, em seguida, **selecione Start**  >  **Now**  >  **Start**. Assim que a tarefa for iniciada com êxito, o estado da tarefa é alterado de **Parado** para **Em execução**.

![Executar um trabalho stream analytics em Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Criar e publicar um relatório Power BI para visualizar os dados

Os passos a seguir mostram-lhe como criar e publicar um relatório utilizando o serviço Power BI. Pode seguir estes passos, com alguma modificação, se quiser utilizar o "novo visual" no Power BI. Para compreender as diferenças e como navegar no "novo visual", veja [o 'novo visual' do serviço Power BI](https://docs.microsoft.com/power-bi/consumer/service-new-look).

1. Certifique-se de que a aplicação da amostra está a funcionar no seu dispositivo. Caso contrário, pode consultar os tutoriais sob [configuração do seu dispositivo](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Inicie sessão na sua conta do [Power BI](https://powerbi.microsoft.com/en-us/).

3. Selecione o espaço de trabalho que usou, **My Workspace**.

4. Selecione **Conjuntos de dados**.

   Deverá ver o conjunto de dados especificado quando criou a saída para o trabalho de Stream Analytics.

5. Para o conjunto de dados criado, selecione **Add Report** (o primeiro ícone à direita do nome do conjunto de dados).

   ![Criar um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-create-report.png)

6. Crie um gráfico de linhas para mostrar a temperatura em tempo real ao longo do tempo.

   1. No painel **visualizações** da página de criação do relatório, selecione o ícone do gráfico de linha para adicionar um gráfico de linha.

   2. No painel **Campos**, expanda a tabela que especificou quando criou a saída da tarefa do Stream Analytics.

   3. Arraste **EventEnqueuedUtcTime** para **Eixo** no painel **Visualizações**.

   4. Arraste **temperatura** para **Valores**.

      É criado um gráfico de linhas. O eixo X apresenta a data e hora no fuso horário UTC. O eixo Y apresenta a temperatura do sensor.

      ![Adicione um gráfico de linha para a temperatura a um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temperature.png)

7. Crie outro gráfico de linhas para mostrar a humidade em tempo real ao longo do tempo. Para isso, clique numa parte em branco da tela e siga os mesmos passos acima para colocar o **EventEnqueuedUtcTime** no eixo x e **humidade** no eixo y.

   ![Adicione um gráfico de linha para a humidade a um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. **Selecione Guardar** para guardar o relatório.

9. Selecione **Relatórios** sobre o painel esquerdo e, em seguida, selecione o relatório que acabou de criar.

10. Selecione **File**  >  **Publish para web**.

    ![Selecione publicar na web para o relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-select-publish-to-web.png)

    > [!NOTE]
    > Se receber uma notificação para contactar o seu administrador para permitir a criação de código incorporado, poderá ter de entrar em contacto com eles. A criação de código incorporado deve ser ativada antes de poder completar este passo.
    >
    > ![Contacte a notificação do seu administrador](./media/iot-hub-live-data-visualization-in-power-bi/contact-admin.png)

11. Selecione **Criar código incorporado**e, em seguida, selecione **Publicar**.

É-lhe fornecido o link de relatório que pode partilhar com qualquer pessoa para reportar acesso e um código que pode usar para integrar o relatório no seu blog ou website.

![Publique um relatório do Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-web-output.png)

A Microsoft também oferece as [aplicações móveis Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) para visualização e interação com os seus dashboards Power BI e relatórios no seu dispositivo móvel.

## <a name="next-steps"></a>Passos seguintes

Você usou com sucesso o Power BI para visualizar dados de sensores em tempo real a partir do seu hub Azure IoT.

Para obter outra forma de visualizar os dados do Azure IoT Hub, consulte [utilizar uma aplicação web para visualizar dados de sensores em tempo real a partir do Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
