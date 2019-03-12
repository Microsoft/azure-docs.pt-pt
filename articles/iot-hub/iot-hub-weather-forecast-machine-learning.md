---
title: Previsão meteorológica com o Azure Machine Learning com dados do IoT Hub | Documentos da Microsoft
description: Utilização do Azure Machine Learning para prever a probabilidade de chuva com base nos dados de temperatura e humidade que seu hub IoT recolhe a partir de um sensor.
author: rangv
manager: ''
keywords: aprendizagem de previsão meteorológica
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 7fdd2a96044acdae223243d751bfcffb7a99da78
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534264"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Previsão meteorológica com os dados de sensor do seu hub IoT no Azure Machine Learning

![Diagrama de ponto a ponto](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Machine learning é uma técnica da ciência de dados que o ajuda a aprender com dados existentes para prever futuros comportamentos, resultados e tendências de computadores. O Azure Machine Learning é um serviço de análise preditiva baseado na cloud e que torna possível a criação e implementação rápidas de modelos preditivos como soluções de análise.

## <a name="what-you-learn"></a>O que irá aprender

Saiba como utilizar o Azure Machine Learning resistir a previsão (probabilidade de chuva) usando os dados de temperatura e humidade do seu hub IoT do Azure. A probabilidade de chuva é a saída de um modelo de previsão meteorológica preparado. O modelo foi criado a partir do histórico de dados para prever a probabilidade de chuva com base em temperatura e humidade.

## <a name="what-you-do"></a>O que fazer

- Implemente o modelo de previsão de Meteorologia como um serviço web.
- Prepare seu IoT hub para acesso a dados ao adicionar um grupo de consumidores.
- Criar uma tarefa do Stream Analytics e configurar a tarefa para:
  - Ler dados de temperatura e humidade do seu hub IoT.
  - Chame o web service para obter a probabilidade de chuva.
  - Guarde o resultado para um armazenamento de Blobs do Azure.
- Utilize o Explorador de armazenamento do Microsoft Azure para ver a previsão meteorológica.

## <a name="what-you-need"></a>Do que precisa

- Tutorial [configurar o seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) concluída que abrange os seguintes requisitos:
  - Uma subscrição ativa do Azure.
  - Um hub IoT do Azure com a sua subscrição.
  - Uma aplicação de cliente que envia mensagens para o seu hub IoT do Azure.
- Uma conta do Azure Machine Learning Studio. ([Experimente o Machine Learning Studio gratuitamente](https://studio.azureml.net/)).

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Implementar o modelo de previsão meteorológica como um serviço web

1. Vá para o [página do modelo de previsão de Meteorologia](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).
1. Clique em **abrir no Studio** no Microsoft Azure Machine Learning Studio.
   ![Abra a página de modelo de previsão meteorológica na galeria do Cortana Intelligence](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Clique em **executar** para validar os passos no modelo. Este passo pode demorar a 2 minutos a concluir.
   ![Abra o modelo de previsão meteorológica no Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Clique em **configurar o serviço WEB** > **serviço Web preditivo**.
   ![Implementar o modelo de previsão meteorológica no Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. No diagrama, arraste o **entrada do serviço da Web** módulo em algum lugar junto a **Score Model** módulo.
1. Ligar o **entrada de serviço da Web** módulo para o **Score Model** módulo.
   ![Ligue-se dois módulos no Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Clique em **executar** para validar os passos no modelo.
1. Clique em **implementar serviço WEB** para implementar o modelo como um serviço web.
1. No dashboard do modelo, transfira o **Excel 2010 ou pasta de trabalho anterior** para **SOLICITAÇÃO/resposta**.

   > [!Note]
   > Certifique-se de que baixe o **Excel 2010 ou pasta de trabalho anterior** mesmo que esteja executando uma versão posterior do Excel no seu computador.

   ![Transferir o Excel para o ponto final de resposta a um pedido](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Abra o livro do Excel, anote o **URL do serviço WEB** e **chave de acesso**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Criar, configurar e executar uma tarefa do Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

1. No [portal do Azure](https://portal.azure.com/), clique em **Criar um recurso** > **Internet das Coisas** > **Tarefa do Stream Analytics**.
1. Introduza as seguintes informações para a tarefa.

   **Nome da tarefa**: O nome da tarefa. O nome tem de ser globalmente exclusivo.

   **Grupo de recursos**: Utilize o mesmo grupo de recursos que utiliza o seu hub IoT.

   **Localização**: Utilize a mesma localização que o grupo de recursos.

   **Afixar ao dashboard**: Marque esta opção para facilitar o acesso ao seu hub IoT a partir do dashboard.

   ![Criar uma tarefa do Stream Analytics no Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Clique em **Criar**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada à tarefa do Stream Analytics

1. Abra a tarefa do Stream Analytics.
1. Em **Topologia da Tarefa**, clique em **Entradas**.
1. Na **entradas** painel, clique em **Add**e, em seguida, introduza as seguintes informações:

   **O alias de entrada**: O alias exclusivo para a entrada.

   **origem**: Selecione **IoT hub**.

   **Grupo de consumidores**: Selecione o grupo de consumidores que criou.

   ![Adicione uma entrada à tarefa do Stream Analytics no Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Clique em **Criar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída à tarefa do Stream Analytics

1. Em **Topologia da Tarefa**, clique em **Saídas**.
1. Na **saídas** painel, clique em **Add**e, em seguida, introduza as seguintes informações:

   **Alias de saída**: O alias exclusivo para a saída.

   **Sink**: Selecione **armazenamento de BLOBs**.

   **Conta de armazenamento**: A conta de armazenamento do seu armazenamento de Blobs. Pode criar uma conta de armazenamento ou utilize um já existente.

   **contentor**: O contentor onde o blob é guardado. Pode criar um contentor ou utilize um já existente.

   **Formato de serialização de eventos**: Selecione **CSV**.

   ![Adicionar uma saída da tarefa do Stream Analytics no Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Clique em **Criar**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Adicionar uma função para a tarefa de Stream Analytics para chamar o serviço web que implementou

1. Sob **topologia da tarefa**, clique em **funções** > **adicionar**.
1. Introduza as seguintes informações:

   **Alias de função**: Introduza `machinelearning`.

   **Tipo de função**: Selecione **do Azure ML**.

   **Opção de importar**: Selecione **importação de uma subscrição diferente**.

   **URL**: Introduza o URL do serviço WEB que anotou para baixo do livro do Excel.

   **chave**: Introduza a chave de acesso que anotou para baixo do livro do Excel.

   ![Adicionar uma função para a tarefa de Stream Analytics no Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Clique em **Criar**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta da tarefa do Stream Analytics

1. Em **Topologia da Tarefa**, clique em **Consulta**.
1. Substitua o código existente pelo código seguinte:

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

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Utilize o Explorador de armazenamento do Microsoft Azure para ver a previsão meteorológica

Execute a aplicação de cliente para começar a recolher e enviar dados de temperatura e humidade ao seu hub IoT. Para cada mensagem que recebe o seu hub IoT, a tarefa do Stream Analytics chama o serviço da web de previsão meteorológica para produzir a probabilidade de chuva. O resultado, em seguida, é guardado no armazenamento de Blobs do Azure. Explorador de armazenamento do Azure é uma ferramenta que pode utilizar para ver o resultado.

1. [Baixe e instale o Explorador de armazenamento do Microsoft Azure](https://storageexplorer.com/).
1. Abra o Explorador de armazenamento do Azure.
1. Inicie sessão na sua conta do Azure.
1. Selecione a sua subscrição.
1. Clique na sua subscrição > **contas de armazenamento** > sua conta de armazenamento > **contentores de BLOBs** > seu contentor.
1. Abra um ficheiro. csv para ver o resultado. A última coluna regista a probabilidade de chuva.

   ![Obter Resultado de previsão meteorológica com o Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Resumo

Utilizou com êxito do Azure Machine Learning para produzir a probabilidade de chuva com base nos dados de temperatura e humidade que recebe o seu hub IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]