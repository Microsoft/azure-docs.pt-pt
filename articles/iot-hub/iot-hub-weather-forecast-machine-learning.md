---
title: Previsão do tempo usando Azure Machine Learning Studio (clássico) com dados do IoT Hub
description: Utilize o Azure Machine Learning Studio (clássico) para prever a possibilidade de chuva com base nos dados de temperatura e humidade que o seu hub IoT recolhe de um sensor.
author: robinsh
manager: philmea
keywords: previsão meteorológica aprendizagem de máquina
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 09/16/2020
ms.author: robinsh
ms.openlocfilehash: 455d78ed21403952046448dd4447b5ec54f77c00
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566984"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning-studio-classic"></a>Previsão do tempo usando os dados do sensor do seu hub IoT no Azure Machine Learning Studio (clássico)

![Diagrama de ponta a ponta](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Machine learning é uma técnica de ciência de dados que ajuda os computadores a aprender com os dados existentes para prever comportamentos futuros, resultados e tendências. O Azure Machine Learning Studio (clássico) é um serviço de análise preditiva em nuvem que permite criar e implementar rapidamente modelos preditivos como soluções de análise. Neste artigo, você aprende a usar o Azure Machine Learning Studio (clássico) para fazer previsão meteorológica (chance de chuva) usando os dados de temperatura e humidade do seu hub Azure IoT. A probabilidade de chuva é a saída de um modelo de previsão meteorológica preparado. O modelo baseia-se em dados históricos para prever a probabilidade de chuva com base na temperatura e humidade.

## <a name="prerequisites"></a>Pré-requisitos

- Complete o tutorial [de simulador on-line Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo. Por exemplo, pode ir a [Raspberry Pi com node.js](iot-hub-raspberry-pi-kit-node-get-started.md) ou a um dos quickstarts de [telemetria Enviar.](quickstart-send-telemetry-dotnet.md) Estes artigos abrangem os seguintes requisitos:
  - Uma subscrição ativa do Azure.
  - Um hub Azure IoT sob a sua assinatura.
  - Uma aplicação de cliente que envia mensagens para o seu hub Azure IoT.
- Uma conta [do Azure Machine Learning Studio (clássico).](https://studio.azureml.net/)
- Uma [conta de Armazenamento Azure](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts), uma conta **v2 para fins gerais** é preferível, mas qualquer conta de Armazenamento Azure que suporte o armazenamento Azure Blob também funcionará.

> [!Note]
> Este artigo utiliza a Azure Stream Analytics e vários outros serviços pagos. São incorridos encargos adicionais no Azure Stream Analytics quando os dados devem ser transferidos para as regiões de Azure. Por esta razão, seria bom garantir que a sua conta de Resource Group, IoT Hub e Azure Storage -- bem como o espaço de trabalho do Machine Learning Studio (clássico) e o Azure Stream Analytics Job adicionados mais tarde neste tutorial -- estão todos localizados na mesma região do Azure. Você pode verificar o suporte regional para Azure Machine Learning Studio (clássico) e outros serviços Azure na disponibilidade do [produto Azure por página região](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-studio&regions=all).

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Implemente o modelo de previsão meteorológica como um serviço web

Nesta secção obtém-se o modelo de previsão meteorológica da Biblioteca Azure AI. Em seguida, adicione um módulo de script R ao modelo para limpar os dados de temperatura e humidade. Por último, implementa o modelo como um serviço web preditivo.

### <a name="get-the-weather-prediction-model"></a>Obtenha o modelo de previsão do tempo

Nesta secção obtém-se o modelo de previsão meteorológica da Galeria Azure AI e abre-o no Azure Machine Learning Studio (clássico).

1. Vá para a página do [modelo de previsão do tempo.](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1)

   ![Abra a página do modelo de previsão do tempo na Galeria Azure AI](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Selecione **Open in Studio (clássico)** para abrir o modelo no Microsoft Azure Machine Learning Studio (clássico). Selecione uma região perto do seu hub IoT e o espaço de trabalho correto na **experiência Copy da Gallery** pop-up.

   ![Abra o modelo de previsão do tempo no Azure Machine Learning Studio (clássico)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>Adicione um módulo de script R para limpar dados de temperatura e humidade

Para que o modelo se comporte corretamente, os dados de temperatura e humidade devem ser convertíveis aos dados numéricos. Nesta secção, adicione um módulo de script R ao modelo de previsão meteorológica que remove quaisquer linhas que tenham valores de dados para temperatura ou humidade que não possam ser convertidos em valores numéricos.

1. No lado esquerdo da janela Azure Machine Learning Studio (clássico), selecione a seta para expandir o painel de ferramentas. Introduza "Executar" na caixa de pesquisa. Selecione o módulo **executar o script R.**

   ![Selecione executar o módulo de script R](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Arraste o módulo **de script Execut R** perto do módulo de **dados desaparecidos limpos** e do módulo **de script executante** existente no diagrama. Elimine a ligação entre os **dados em falta e** os módulos **Executo R Script** e, em seguida, conecte as entradas e saídas do novo módulo como mostrado.

   ![Adicionar o módulo de script R executo](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Selecione o novo módulo **executo R Script** para abrir a janela de propriedades. Copie e cole o seguinte código na caixa **do Script R.**

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   Quando terminar, a janela das propriedades deve ser semelhante à seguinte:

   ![Adicionar código para executar o módulo de script R](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Implementar serviço web preditivo

Nesta secção, você valida o modelo, configura um serviço web preditivo com base no modelo e, em seguida, implementar o serviço web.

1. Selecione **Executar** para validar os passos do modelo. Este passo pode levar alguns minutos para ser concluído.

   ![Executar a experiência para validar os passos](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Selecione **Configurar o**  >  **Serviço Web Preditivo do Serviço Web.** O diagrama de experiência preditiva abre.

   ![Implemente o modelo de previsão meteorológica no Azure Machine Learning Studio (clássico)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. No diagrama de experiência preditiva, elimine a ligação entre o módulo de entrada de **serviço Web** e as **Colunas Selecionadas no Conjunto de Dados** na parte superior. Em seguida, arraste o módulo **de entrada de serviço Web** em algum lugar perto do módulo **'Modelo de Pontuação'** e conecte-o como mostrado:

   ![Ligue dois módulos no Azure Machine Learning Studio (clássico)](media/iot-hub-weather-forecast-machine-learning/connect-modules-azure-machine-learning-studio.png)

1. Selecione **RUN** para validar os passos do modelo.

1. Selecione **DEPLOY WEB SERVICE** para implementar o modelo como um serviço web.

1. No painel de instrumentos do modelo, descarregue o **livro excel 2010 ou anterior** para **PEDIDO/RESPOSTA**.

   > [!Note]
   > Certifique-se de que descarrega o **livro do Excel 2010 ou anterior,** mesmo que esteja a executar uma versão posterior do Excel no seu computador.

   ![Descarregue o Excel para o ponto final de RESPOSTA DO PEDIDO](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Abra o livro excel, tome nota do URL do **serviço web** e da chave **de acesso.**

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Criar, configurar e executar um trabalho stream Analytics

### <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

1. No [portal Azure,](https://portal.azure.com/)selecione **Criar um recurso**. Digite "stream analytics job" na caixa Search e selecione **o trabalho stream Analytics** a partir do dropdown de resultados. Quando o painel de **trabalho stream Analytics** abrir, selecione **Criar**.
1. Introduza as seguintes informações para a tarefa.

   **Nome da tarefa**: o nome da tarefa. O nome tem de ser globalmente exclusivo.

   **Subscrição**: Selecione a sua subscrição se for diferente do padrão.

   **Grupo de recursos**: Utilize o mesmo grupo de recursos que o seu hub IoT utiliza.

   **Localização**: Utilize a mesma localização que o seu grupo de recursos.

   Deixe todos os outros campos no seu padrão.

   ![Criar um trabalho stream analytics em Azure](media/iot-hub-weather-forecast-machine-learning/create-stream-analytics-job.png)

1. Selecione **Criar**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada à tarefa do Stream Analytics

1. Abra o trabalho stream analytics.
1. Em **Topologia da tarefa**, selecione **Entradas**.
1. No painel **de entradas,** selecione **Adicione a entrada de fluxo** e, em seguida, selecione **IoT Hub** a partir do dropdown. No novo painel de **entrada,** escolha o **Select IoT Hub das suas subscrições** e introduza as seguintes informações:

   **Inserir pseudónimo :** O pseudónimo único para a entrada.

   **Subscrição**: Selecione a sua subscrição se for diferente do padrão.

   **IoT Hub**: Selecione o hub IoT da sua subscrição.

   **Nome da política de acesso partilhado**: Selecione o  **serviço**. (Também pode usar **iothubowner**.)

   **Grupo de consumidores**: Selecione o grupo de consumidores que criou.

   Deixe todos os outros campos no seu padrão.

   ![Adicione uma entrada ao trabalho stream Analytics em Azure](media/iot-hub-weather-forecast-machine-learning/add-input-stream-analytics-job.png)

1. Selecione **Guardar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída à tarefa do Stream Analytics

1. Em **Topologia da tarefa**, selecione **Saídas**.
1. No painel **outputs,** **selecione Add**, e, em seguida, selecione **Blob storage/Data Lake Storage** a partir do dropdown. No painel de **saída Novo,** escolha o **armazenamento Select das suas subscrições** e introduza as seguintes informações:

   **Alias de saída**: o alias exclusivo da saída.

   **Subscrição**: Selecione a sua subscrição se for diferente do padrão.

   **Conta de armazenamento**: A conta de armazenamento para o seu armazenamento de bolhas. Pode criar uma conta de armazenamento ou utilizar uma existente.

   **Recipiente**: O recipiente onde a bolha está guardada. Pode criar um recipiente ou utilizar um existente.

   **Formato de serialização do evento**: Selecione **CSV**.

   ![Adicione uma saída ao trabalho stream Analytics em Azure](media/iot-hub-weather-forecast-machine-learning/add-output-stream-analytics-job.png)

1. Selecione **Guardar**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Adicione uma função ao trabalho stream Analytics para ligar para o serviço web que implementou

1. Em **Job Topology**, selecione **Funções**.
1. No painel **'Funções',** **selecione Add**, e, em seguida, selecione **Azure ML Studio** a partir do dropdown. (Certifique-se de que seleciona **Azure ML Studio**, não **Azure ML Service**.) No painel de **funções Nova,** escolha manualmente as **definições de função De Aprendizagem automática De Azure** e introduza as seguintes informações:

   **Função Pseudónimo : Insira** `machinelearning` .

   **URL**: Introduza o URL de serviço web que anotado a partir do livro do Excel.

   **Tecla**: Introduza a chave de acesso que anotado a partir do livro do Excel.

   ![Adicione uma função ao trabalho stream Analytics em Azure](media/iot-hub-weather-forecast-machine-learning/add-function-stream-analytics-job.png)

1. Selecione **Guardar**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta da tarefa do Stream Analytics

1. Em **Topologia da tarefa**, selecione **Consulta**.
1. Substitua o código existente pelo código abaixo:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[scored probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Substitua `[YourInputAlias]` pelo alias de entrada da tarefa.

   Substitua `[YourOutputAlias]` pelo alias de saída da tarefa.

1. **Selecione Guardar consulta**.

> [!Note]
> Se selecionar **a consulta de teste,** será apresentada a seguinte mensagem: Os testes de consulta com as funções de Machine Learning não são suportados. Por favor, modifique a consulta e tente novamente. Pode ignorar esta mensagem com segurança e selecionar **OK** para fechar a caixa de mensagens. Certifique-se de que guarda a consulta antes de avançar para a secção seguinte.

### <a name="run-the-stream-analytics-job"></a>Executar a tarefa do Stream Analytics

No trabalho Stream Analytics, selecione **Overview** no painel esquerdo. Em seguida, **selecione Iniciar**  >    >  **Agora**. Assim que a tarefa for iniciada com êxito, o estado da tarefa é alterado de **Parado** para **Em execução**.

![Executar a tarefa do Stream Analytics](media/iot-hub-weather-forecast-machine-learning/run-stream-analytics-job.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Use o Microsoft Azure Storage Explorer para ver a previsão meteorológica

Execute a aplicação do cliente para começar a recolher e enviar dados de temperatura e humidade para o seu hub IoT. Para cada mensagem que o seu hub IoT recebe, o trabalho stream Analytics chama o serviço web de previsão meteorológica para produzir a chance de chuva. O resultado é então guardado para o seu armazenamento de bolhas Azure. O Azure Storage Explorer é uma ferramenta que pode utilizar para ver o resultado.

1. [Descarregue e instale o Microsoft Azure Storage Explorer](https://storageexplorer.com/).
1. Abra o Explorador de Armazenamento Azure.
1. Inicie sessão na sua conta do Azure.
1. Selecione a sua subscrição.
1. Selecione a sua subscrição > **Contas de Armazenamento** > a sua conta de armazenamento > **Blob Containers** > seu contentor.
1. Faça o download de um ficheiro .csv para ver o resultado. A última coluna regista a possibilidade de chuva.

   ![Obtenha o resultado da previsão do tempo com o Azure Machine Learning Studio (clássico)](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Resumo

Você usou com sucesso o Azure Machine Learning Studio (clássico) para produzir a chance de chuva com base nos dados de temperatura e humidade que o seu hub IoT recebe.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]