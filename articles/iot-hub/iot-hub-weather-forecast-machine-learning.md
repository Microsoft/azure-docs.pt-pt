---
title: Previsão do tempo usando Azure Machine Learning com dados do Hub IoT
description: Use Azure Machine Learning para prever a possibilidade de Rain com base nos dados de temperatura e umidade que o Hub IoT coleta de um sensor.
author: robinsh
manager: philmea
keywords: aprendizado de máquina de previsão do tempo
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: e7114febe85af4acaf94ef7fe64fe47988fd6321
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74079268"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Previsão do tempo usando os dados do sensor do seu hub IoT no Azure Machine Learning

![Diagrama de ponta a ponta](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

O aprendizado de máquina é uma técnica de ciência de dados que ajuda os computadores a aprenderem com os dados existentes para prever comportamentos, resultados e tendências futuros. O Azure Machine Learning é um serviço de análise preditiva baseado na cloud e que torna possível a criação e implementação rápidas de modelos preditivos como soluções de análise.

## <a name="what-you-learn"></a>O que irá aprender

Você aprende a usar Azure Machine Learning para fazer a previsão do tempo (possibilidade de chuva) usando os dados de temperatura e umidade do Hub IoT do Azure. A possibilidade de chuva é a saída de um modelo de previsão do clima preparado. O modelo é criado sobre dados históricos para prever a chance de chuva com base na temperatura e na umidade.

## <a name="what-you-do"></a>O que você faz

- Implante o modelo previsão do tempo como um serviço Web.
- Prepare seu hub IoT para acesso a dados adicionando um grupo de consumidores.
- Crie um trabalho Stream Analytics e configure o trabalho para:
  - Leia dados de temperatura e umidade do Hub IoT.
  - Chame o serviço Web para obter a probabilidade de chuva.
  - Salve o resultado em um armazenamento de BLOBs do Azure.
- Use Gerenciador de Armazenamento do Microsoft Azure para exibir a previsão do tempo.

## <a name="what-you-need"></a>Do que precisa

- Conclua o tutorial do [simulador online do Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo; por exemplo, [Raspberry Pi com node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Eles abrangem os seguintes requisitos:
  - Uma subscrição ativa do Azure.
  - Um hub IoT do Azure em sua assinatura.
  - Um aplicativo cliente que envia mensagens para o Hub IoT do Azure.
- Uma conta de [Azure Machine Learning Studio](https://studio.azureml.net/) .

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Implantar o modelo de previsão do clima como um serviço Web

1. Vá para a [página modelo de previsão do clima](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).
1. Clique em **abrir no estúdio** em Microsoft Azure Machine Learning Studio.
   ![abrir a página modelo de previsão do tempo no Cortana Intelligence Gallery](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Clique em **executar** para validar as etapas no modelo. Esta etapa pode levar 2 minutos para ser concluída.
   ![abrir o modelo previsão do tempo no Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Clique em **configurar serviço web** > **serviço Web de previsão**.
   ![implantar o modelo previsão do tempo no Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. No diagrama, arraste o módulo **entrada do serviço Web** em algum lugar próximo do módulo **modelo de Pontuação** .
1. Conecte o módulo **entrada do serviço Web** ao módulo **modelo de Pontuação** .
   ![conectar dois módulos no Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Clique em **executar** para validar as etapas no modelo.
1. Clique em **implantar serviço Web** para implantar o modelo como um serviço Web.
1. No painel do modelo, baixe a pasta de **trabalho do Excel 2010 ou anterior** para **solicitação/resposta**.

   > [!Note]
   > Certifique-se de baixar a **pasta de trabalho do excel 2010 ou anterior** , mesmo que você esteja executando uma versão posterior do Excel em seu computador.

   ![Baixar o Excel para o ponto de extremidade de resposta de solicitação](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Abra a pasta de trabalho do Excel, anote a **URL do serviço Web** e a **chave de acesso**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Criar, configurar e executar um trabalho de Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

1. No [portal do Azure](https://portal.azure.com/), clique em **Criar um recurso** > **Internet das Coisas** > **Tarefa do Stream Analytics**.
1. Introduza as seguintes informações para a tarefa.

   **Nome da tarefa**: o nome da tarefa. O nome tem de ser globalmente exclusivo.

   **Grupo de recursos**: Use o mesmo grupo de recursos que o Hub IOT usa.

   **Local**: Use o mesmo local que o seu grupo de recursos.

   **Afixar ao dashboard**: marque esta opção para facilitar o acesso ao hub IoT a partir do dashboard.

   ![Criar um trabalho de Stream Analytics no Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Clique em **Criar**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada à tarefa do Stream Analytics

1. Abra o trabalho Stream Analytics.
1. Em **Topologia da Tarefa**, clique em **Entradas**.
1. No painel **entradas** , clique em **Adicionar**e insira as seguintes informações:

   **Alias de entrada**: o alias exclusivo para a entrada.

   **Origem**: selecione **Hub IOT**.

   **Grupo de consumidores**: selecione o grupo de consumidores que você criou.

   ![Adicionar uma entrada ao trabalho de Stream Analytics no Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Clique em **Criar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída à tarefa do Stream Analytics

1. Em **Topologia da Tarefa**, clique em **Saídas**.
1. No painel **saídas** , clique em **Adicionar**e insira as seguintes informações:

   **Alias de saída**: o alias exclusivo da saída.

   **Coletor**: selecione **armazenamento de BLOBs**.

   **Conta de armazenamento**: a conta de armazenamento para o armazenamento de BLOBs. Você pode criar uma conta de armazenamento ou usar uma existente.

   **Contêiner**: o contêiner em que o blob é salvo. Você pode criar um contêiner ou usar um existente.

   **Formato de serialização de evento**: selecione **CSV**.

   ![Adicionar uma saída ao trabalho de Stream Analytics no Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Clique em **Criar**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Adicionar uma função ao trabalho de Stream Analytics para chamar o serviço Web implantado

1. Em **topologia do trabalho**, clique em **funções** > **Adicionar**.
1. Introduza as seguintes informações:

   **Alias da função**: Insira `machinelearning`.

   **Tipo de função**: selecione **Azure ml**.

   **Opção de importação**: selecione **importar de uma assinatura diferente**.

   **URL**: Insira a URL do serviço Web que você anotou na pasta de trabalho do Excel.

   **Chave**: Insira a chave de acesso que você anotou na pasta de trabalho do Excel.

   ![Adicionar uma função ao trabalho de Stream Analytics no Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Clique em **Criar**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta da tarefa do Stream Analytics

1. Em **Topologia da Tarefa**, clique em **Consulta**.
1. Substitua o código existente pelo código a seguir:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Substitua `[YourInputAlias]` pelo alias de entrada da tarefa.

   Substitua `[YourOutputAlias]` pelo alias de saída da tarefa.

1. Clique em **Guardar**.

### <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

Na tarefa do Stream Analytics, clique em **Iniciar** > **Agora** > **Iniciar**. Assim que a tarefa for iniciada com êxito, o estado da tarefa é alterado de **Parado** para **Em execução**.

![Executar a tarefa do Stream Analytics](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Use Gerenciador de Armazenamento do Microsoft Azure para exibir a previsão do tempo

Execute o aplicativo cliente para começar a coletar e enviar dados de temperatura e umidade para o Hub IoT. Para cada mensagem que seu hub IoT recebe, o trabalho de Stream Analytics chama o serviço Web previsão do tempo para produzir a possibilidade de chuva. O resultado é então salvo em seu armazenamento de BLOBs do Azure. Gerenciador de Armazenamento do Azure é uma ferramenta que você pode usar para exibir o resultado.

1. [Baixe e instale o Gerenciador de armazenamento do Microsoft Azure](https://storageexplorer.com/).
1. Abra Gerenciador de Armazenamento do Azure.
1. Inicie sessão na sua conta do Azure.
1. Selecione a sua subscrição.
1. Clique em sua assinatura > **contas de armazenamento** > sua conta de armazenamento > **contêineres de blob** > seu contêiner.
1. Abra um arquivo. csv para ver o resultado. A última coluna registra a possibilidade de chuva.

   ![Obter resultado da previsão do clima com Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Resumo

Você usou com êxito Azure Machine Learning para produzir a possibilidade de Rain com base nos dados de temperatura e umidade que seu hub IoT recebe.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]