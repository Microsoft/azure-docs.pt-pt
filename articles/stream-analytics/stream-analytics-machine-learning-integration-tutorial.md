---
title: Integração do Stream Analytics do Azure com o Azure Machine Learning
description: Este artigo descreve como configurar rapidamente a uma tarefa Azure Stream Analytics simples que integra o Azure Machine Learning, utilizando uma função definida pelo utilizador.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: b06fec8ab726f48e1937bae4cfbdbd9842788d0a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58109312"
---
# <a name="performing-sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Executar análise de sentimentos com o Azure Stream Analytics e o Azure Machine Learning
Este artigo descreve como configurar rapidamente a uma tarefa Azure Stream Analytics simples que integra o Azure Machine Learning. Utilize um modelo de análise de sentimentos de Machine Learning a partir da galeria do Cortana Intelligence para analisar dados de texto de transmissão em fluxo e determinar a classificação de sentimento em tempo real. Utilizar o Cortana Intelligence Suite permite-lhe realizar esta tarefa sem se preocupar sobre a complexidade da criação de um modelo de análise de sentimentos.

Pode aplicar o que Aprenda com este artigo, para estes cenários:

* Análise de sentimentos em tempo real na transmissão em fluxo de dados do Twitter.
* Analisar registos de cliente conversa com a equipa de suporte.
* Avaliar comentários nos fóruns, blogs e vídeos. 
* Muitos outros em tempo real, previsão classificação cenários.

Num cenário do mundo real, obteria os dados diretamente a partir de um fluxo de dados do Twitter. Para simplificar o tutorial, ele é escrito para que a tarefa do Stream Analytics obtém os tweets de um ficheiro CSV no armazenamento de Blobs do Azure. Pode criar seu próprio ficheiro CSV ou pode utilizar um ficheiro CSV de exemplo, conforme mostrado na imagem seguinte:

![Tweets de exemplo mostradas um ficheiro CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

A tarefa de análise de transmissão em fluxo que criar aplica-se o modelo de análise de sentimentos como uma função definida pelo utilizador (UDF) nos dados de texto de exemplo do armazenamento de Blobs. O resultado (o resultado da análise de sentimentos) é escrito para o mesmo arquivo de blob num arquivo CSV diferente. 

A figura a seguir demonstra esta configuração. Como observado, para um cenário mais realista, pode substituir o armazenamento de Blobs com transmissão em fluxo de dados do Twitter a partir de uma entrada de Event Hubs do Azure. Além disso, pode criar uma [Microsoft Power BI](https://powerbi.microsoft.com/) visualização em tempo real do sentimento de agregação.    

![Descrição geral de integração do Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que tem o seguinte:

* Uma subscrição ativa do Azure.
* Um ficheiro CSV com alguns dados nela. Pode baixar o arquivo mostrado anteriormente a partir [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv), ou pode criar seu próprio ficheiro. Neste artigo, é assumido que está a utilizar o ficheiro a partir do GitHub.

Num alto nível, para concluir as tarefas demonstradas neste artigo, faça o seguinte:

1. Criar uma conta de armazenamento do Azure e um contentor de armazenamento de BLOBs e carregar um ficheiro de entrada com formatação CSV para o contentor.
3. Adicionar um modelo de análise de sentimentos do Cortana Intelligence Gallery a sua área de trabalho do Azure Machine Learning e implementar este modelo como um serviço da web na área de trabalho do Machine Learning.
5. Crie uma tarefa de Stream Analytics que chama esse serviço da web como uma função para determinar os sentimentos do texto de entrada.
6. Iniciar a tarefa de Stream Analytics e verificar a saída.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Criar um contentor de armazenamento e carregue o ficheiro de entrada de CSV
Para este passo, pode utilizar ficheiros CSV, como o disponibilizado a partir do GitHub.

1. No portal do Azure, clique em **criar um recurso** > **armazenamento** > **conta de armazenamento**.

2. Forneça um nome (`samldemo` no exemplo). O nome pode utilizar apenas letras minúsculas e números e tem de ser exclusivo em todo o Azure. 

3. Especifique um grupo de recursos existente e especificar uma localização. Para a localização, recomendamos que todos os recursos criados neste tutorial, utilize a mesma localização.

    ![forneça detalhes da conta de armazenamento](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. No portal do Azure, selecione a conta de armazenamento. No painel da conta de armazenamento, clique em **contentores** e, em seguida, clique em  **+ &nbsp;contentor** para criar o armazenamento de Blobs.

    ![Criar contentor de armazenamento de BLOBs de entrada](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Forneça um nome para o contentor (`azuresamldemoblob` no exemplo) e certifique-se de que **acessar tipo** está definido como **Blob**. Quando tiver terminado, clique em **OK**.

    ![Especifique os detalhes do contentor de BLOBs](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. Na **contentores** painel, selecione o novo contentor, que abre o painel para esse contentor.

7. Clique em **Carregar**.

    ![Botão "Carregar" para um contentor](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. Na **carregar blob** painel, o carregamento a **sampleinput.csv** ficheiro que transferiu anteriormente. Para **tipo de Blob**, selecione **blob de blocos** e defina o tamanho do bloco de 4 MB, o que é suficiente para este tutorial.

9. Clique nas **carregar** na parte inferior do painel.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Adicionar o modelo de análise de sentimento a partir da galeria do Cortana Intelligence

Agora que os dados de exemplo estão num blob, pode ativar o modelo de análise de sentimentos na galeria do Cortana Intelligence.

1. Vá para o [modelo de análise de sentimentos preditiva](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) página na galeria da Cortana Intelligence.  

2. Clique em **abrir no Studio**.  
   
   ![Stream Analytics Machine Learning, abra o Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Inicie sessão para aceder à área de trabalho. Selecione uma localização.

4. Clique em **executar** na parte inferior da página. O processo é executado, que demora cerca de um minuto.

   ![Execute a experimentação no Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Depois do processo foi executada com êxito, selecione **implementar serviço Web** na parte inferior da página.

   ![implementação de experimentação no Machine Learning Studio como um serviço web](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Para validar que o modelo de análise de sentimentos está pronto a utilizar, clique nas **teste** botão. Forneça o texto de entrada, tais como "Eu adoro Microsoft". 

   ![teste de experimentação no Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Se o teste funcione, verá um resultado semelhante ao seguinte exemplo:

   ![resultados do teste no Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Na **aplicações** coluna, clique nas **Excel 2010 ou pasta de trabalho anterior** link para baixar um livro do Excel. O livro contém a chave de API e o URL que irá precisar posteriormente para configurar a tarefa do Stream Analytics.

    ![Stream Analytics Machine Learning, leitura rápida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Criar uma tarefa do Stream Analytics que utiliza o modelo de Machine Learning

Agora, pode criar uma tarefa de Stream Analytics que lê os tweets de exemplo do ficheiro CSV no armazenamento de Blobs. 

### <a name="create-the-job"></a>Criar o trabalho

1. Aceda ao [Portal do Azure](https://portal.azure.com).  

2. Clique em **criar um recurso** > **Internet das coisas** > **tarefa do Stream Analytics**. 

3. Nome da tarefa `azure-sa-ml-demo`, especifique uma subscrição, especifique um grupo de recursos existente ou crie um novo e selecione a localização para a tarefa.

   ![especificar definições para a nova tarefa de Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>Configurar a entrada da tarefa
A tarefa obtém entrada do ficheiro CSV que carregado anteriormente para o armazenamento de Blobs.

1. Depois da tarefa tiver sido criada, em **topologia da tarefa** no painel de tarefas, clique nas **entradas** opção.    

2. Na **entradas** painel, clique em **Adicionar entrada de Stream** >**armazenamento de BLOBs**

3. Preencha os **armazenamento de BLOBs** painel com estes valores:

   
   |Campo  |Valor  |
   |---------|---------|
   |**Alias de entrada** | Utilize o nome `datainput` e selecione **selecione blob storage, a sua subscrição**       |
   |**Conta de armazenamento**  |  Selecione a conta de armazenamento que criou anteriormente.  |
   |**Contentor**  | Selecione o contentor que criou anteriormente (`azuresamldemoblob`)        |
   |**Formato de serialização de eventos**  |  Selecione **CSV**       |

   ![Definições para a nova entrada de tarefa do Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Clique em **Guardar**.

### <a name="configure-the-job-output"></a>Configurar o resultado da tarefa
A tarefa envia resultados para o mesmo armazenamento de BLOBs onde obtém entrada. 

1. Sob **topologia da tarefa** no painel de tarefas, clique nas **saídas** opção.  

2. Na **saídas** painel, clique em **Add** >**armazenamento de BLOBs**e, em seguida, adicione uma saída com o alias `datamloutput`. 

3. Preencha os **armazenamento de BLOBs** painel com estes valores:

   |Campo  |Valor  |
   |---------|---------|
   |**Alias de saída** | Utilize o nome `datamloutput` e selecione **selecione blob storage, a sua subscrição**       |
   |**Conta de armazenamento**  |  Selecione a conta de armazenamento que criou anteriormente.  |
   |**Contentor**  | Selecione o contentor que criou anteriormente (`azuresamldemoblob`)        |
   |**Formato de serialização de eventos**  |  Selecione **CSV**       |

   ![Definições para a nova saída de tarefa do Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Clique em **Guardar**.   


### <a name="add-the-machine-learning-function"></a>Adicione a função de Machine Learning 
Anteriormente publicou um modelo de Machine Learning para um serviço web. Neste cenário, quando a tarefa de Stream análise é executada, envia cada tweet de exemplo da entrada para o web service para análise de sentimentos. O serviço web do Machine Learning devolve um sentimento (`positive`, `neutral`, ou `negative`) e uma probabilidade do tweet a ser positivo. 

Nesta secção do tutorial, vai definir uma função da tarefa de análise de Stream. A função pode ser invocada para enviar um tweet para o serviço web e obter a resposta. 

1. Certifique-se de que tem a chave de API e o URL de serviço de web que transferiu anteriormente no livro do Excel.

2. Navegue para o painel de trabalho > **funções** > **+ adicionar** > **AzureML**

3. Preencha os **função do Azure Machine Learning** painel com estes valores:

   |Campo  |Valor  |
   |---------|---------|
   | **Alias de função** | Utilize o nome `sentiment` e selecione **definições de função de fornecer o Azure Machine Learning manualmente** que lhe dá uma opção para introduzir o URL e a chave.      |
   | **URL**| Cole o URL do serviço web.|
   |**Chave** | Cole a chave de API. |
  
   ![Definições para adicionar a função de Machine Learning à tarefa do Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Clique em **Guardar**.

### <a name="create-a-query-to-transform-the-data"></a>Criar uma consulta para transformar os dados

Stream Analytics utiliza uma consulta declarativa, baseado em SQL para examinar a entrada e processá-lo. Nesta secção, vai criar uma consulta que lê cada tweet de entrada e, em seguida, invoca a função de Machine Learning para efetuar a análise de sentimentos. A consulta, em seguida, envia o resultado para a saída que definiu (armazenamento de BLOBs).

1. Volte ao painel de descrição geral de tarefa.

2.  Sob **topologia da tarefa**, clique nas **consulta** caixa.

3. Introduza a seguinte consulta:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM datainput  
    )  

    SELECT text, result.[Score]  
    INTO datamloutput
    FROM sentiment  
    ```    

    A consulta invoca a função que criou anteriormente (`sentiment`) para realizar a análise de sentimentos em cada tweet na entrada. 

4. Clique em **guardar** para guardar a consulta.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

Agora pode iniciar a tarefa do Stream Analytics.

### <a name="start-the-job"></a>Iniciar a tarefa
1. Volte ao painel de descrição geral de tarefa.

2. Clique em **iniciar** na parte superior do painel.

3. Na **tarefa de início**, selecione **personalizado**e, em seguida, selecione um dia antes quando carregou o ficheiro CSV para armazenamento de Blobs. Quando tiver terminado, clique em **iniciar**.  


### <a name="check-the-output"></a>Verificar a saída
1. Permitir que a tarefa seja executada durante alguns minutos até ver atividade no **monitorização** caixa. 

2. Se tiver uma ferramenta que normalmente usada para examinar o conteúdo do armazenamento de BLOBs, usar essa ferramenta para examinar o `azuresamldemoblob` contentor. Em alternativa, execute os seguintes passos no portal do Azure:

    1. No portal, localize a `samldemo` armazenamento de conta e, dentro da conta, encontre o `azuresamldemoblob` contentor. Verá dois ficheiros no contentor: o ficheiro que contém os tweets de exemplo e um ficheiro CSV gerado pela tarefa do Stream Analytics.
    2. O arquivo gerado com o botão direito e, em seguida, selecione **transferir**. 

   ![Transferir o resultado da tarefa CSV do armazenamento de BLOBs](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Abra o ficheiro CSV gerado. Verá algo semelhante ao seguinte exemplo:  
   
   ![Stream Analytics Machine Learning, a vista CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Ver métricas
Também pode ver métricas relacionadas com a função do Azure Machine Learning. As métricas seguintes relacionados com a função são apresentadas no **monitorização** caixa no painel da tarefa:

* **Pedidos de função** indica o número de pedidos enviados para um serviço web do Machine Learning.  
* **Eventos de função** indica o número de eventos no pedido. Por predefinição, cada solicitação a um serviço web do Machine Learning contém até 1000 eventos.  


## <a name="next-steps"></a>Passos Seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Integrar a REST API e Machine Learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)



