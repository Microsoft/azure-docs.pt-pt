---
title: Integração Azure Stream Analytics com Azure Machine Learning
description: Este artigo descreve como configurar rapidamente um simples trabalho Azure Stream Analytics que integra o Azure Machine Learning, utilizando uma função definida pelo utilizador.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/19/2020
ms.custom: seodec18
ms.openlocfilehash: 3c7a9a4c31ad7282782f45a8e2a4457cd159ee77
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044452"
---
# <a name="perform-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Realize análise de sentimento com Azure Stream Analytics e Azure Machine Learning Studio (clássico)

Este artigo descreve como configurar rapidamente um simples trabalho Azure Stream Analytics que integra o Azure Machine Learning Studio (clássico). Você usa um modelo de análise de sentimento de Machine Learning da Cortana Intelligence Gallery para analisar dados de streaming de texto e determinar a pontuação do sentimento em tempo real. A utilização da Suíte de Inteligência Cortana permite-lhe realizar esta tarefa sem se preocupar com os meandros da construção de um modelo de análise de sentimentos.

> [!TIP]
> É altamente recomendado usar [UDFs de aprendizagem de máquinas Azure](machine-learning-udf.md) em vez de Azure Machine Learning Studio (clássico) UDF para melhorar o desempenho e a fiabilidade.

Pode aplicar o que aprende com este artigo a cenários como estes:

* Analisar o sentimento em tempo real no streaming de dados do Twitter.
* Analisar registos de conversas de clientes com pessoal de apoio.
* Avaliação de comentários em fóruns, blogs e vídeos. 
* Muitos outros cenários de pontuação preditiva em tempo real.

Num cenário real, obteria os dados diretamente de um fluxo de dados do Twitter. Para simplificar o tutorial, está escrito para que o trabalho de Streaming Analytics obtenha tweets de um ficheiro CSV no armazenamento de Azure Blob. Pode criar o seu próprio ficheiro CSV, ou pode utilizar um ficheiro CSV de amostra, como mostra a seguinte imagem:

![Tweets de amostra mostrados num ficheiro CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

O trabalho de Streaming Analytics que cria aplica o modelo de análise de sentimento como uma função definida pelo utilizador (UDF) nos dados de texto da amostra da loja blob. A saída (o resultado da análise do sentimento) é escrita para a mesma loja blob em um ficheiro CSV diferente. 

A seguinte figura demonstra esta configuração. Como notado, para um cenário mais realista, pode substituir o armazenamento de blob por streaming de dados do Twitter a partir de uma entrada do Azure Event Hubs. Além disso, pode construir uma visualização em tempo real do [Microsoft Power BI](https://powerbi.microsoft.com/) do sentimento agregado.    

![Visão geral da integração da máquina de aprendizagem da máquina de streaming Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que tem o seguinte:

* Uma subscrição ativa do Azure.
* Um ficheiro CSV com alguns dados. Pode descarregar o ficheiro apresentado anteriormente no [GitHub,](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)ou pode criar o seu próprio ficheiro. Para este artigo, presume-se que está a usar o ficheiro do GitHub.

A um nível elevado, para completar as tarefas demonstradas neste artigo, faz o seguinte:

1. Crie uma conta de armazenamento Azure e um recipiente de armazenamento de bolhas e carrequiva um ficheiro de entrada com formato CSV para o recipiente.
3. Adicione um modelo de análise de sentimento da Cortana Intelligence Gallery ao seu espaço de trabalho Azure Machine Learning Studio (clássico) e implemente este modelo como um serviço web no espaço de trabalho machine learning.
5. Crie um trabalho stream Analytics que chame este serviço web como uma função para determinar o sentimento para a entrada de texto.
6. Inicie o trabalho stream Analytics e verifique a saída.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Crie um recipiente de armazenamento e carrequiva o ficheiro de entrada CSV
Para este passo, pode utilizar qualquer ficheiro CSV, como o disponível no GitHub.

1. No portal Azure, clique em **Criar uma**conta de armazenamento  >  **Storage**  >  **de**recursos.

2. Fornecer um nome `samldemo` (no exemplo). O nome só pode usar letras e números minúsculos, e deve ser único em todo o Azure. 

3. Especifique um grupo de recursos existente e especifique uma localização. Para a localização, recomendamos que todos os recursos criados neste tutorial utilizem o mesmo local.

    ![fornecer detalhes da conta de armazenamento](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. No portal Azure, selecione a conta de armazenamento. Na lâmina da conta de armazenamento, clique em **Recipientes** e, em seguida, clique em ** + &nbsp; Recipiente** para criar armazenamento de bolhas.

    ![Criar recipiente de armazenamento de bolhas para entrada](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Forneça um nome para o recipiente `azuresamldemoblob` (no exemplo) e verifique se o **tipo de acesso** está definido para **Blob**. Quando tiver terminado, clique em **OK**.

    ![especificar detalhes do recipiente blob](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. Na lâmina **recipientes,** selecione o novo recipiente, que abre a lâmina para o recipiente.

7. Clique em **Carregar**.

    ![Botão 'Upload' para um recipiente](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. Na lâmina do **blob do Upload,** carrete o ficheiro **sampleinput.csv** que descarregou anteriormente. Para **o tipo Blob**, selecione **Block blob** e ajuste o tamanho do bloco para 4 MB, o que é suficiente para este tutorial.

9. Clique no botão **Upload** na parte inferior da lâmina.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Adicione o modelo de análise de sentimento da Galeria de Inteligência cortana

Agora que os dados da amostra estão numa bolha, pode ativar o modelo de análise de sentimento na Cortana Intelligence Gallery.

1. Vá à página [do modelo de análise de sentimentos preditivos](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) na Galeria de Inteligência cortana.  

2. Clique **em Abrir no Estúdio.**  
   
   ![Stream Analytics Machine Learning, estúdio aberto de aprendizagem automática](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Inscreva-se para ir ao espaço de trabalho. Selecione uma localização.

4. Clique em **Executar** na parte inferior da página. O processo decorre, o que leva cerca de um minuto.

   ![executar experiência no Estúdio de Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Depois de o processo ter sido executado com sucesso, selecione Implementar o **Serviço Web** na parte inferior da página.

   ![implementar experiência no Machine Learning Studio como um serviço web](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Para validar que o modelo de análise de sentimento está pronto a ser utilizado, clique no botão **Teste.** Fornecer entrada de texto como "I love Microsoft". 

   ![experiência de teste no Estúdio de Aprendizagem automática](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Se o teste funcionar, vê um resultado semelhante ao seguinte exemplo:

   ![resultados do teste no Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Na coluna **Apps,** clique no link **excel 2010 ou anterior** para descarregar um livro do Excel. O livro contém a chave API e o URL que precisa mais tarde para configurar o trabalho stream Analytics.

    ![Stream Analytics Machine Learning, olhar rápido](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Crie um trabalho stream analytics que use o modelo de Machine Learning

Agora pode criar um trabalho stream Analytics que lê os tweets de amostra do ficheiro CSV no armazenamento de bolhas. 

### <a name="create-the-job"></a>Criar o trabalho

1. Vá ao [portal Azure.](https://portal.azure.com)  

2. Clique **em Criar um recurso**de internet de  >  **coisas**stream  >  **Analytics.** 

3. Nomear o trabalho `azure-sa-ml-demo` , especificar uma subscrição, especificar um grupo de recursos existente ou criar um novo, e selecionar a localização para o trabalho.

   ![especificar definições para novo trabalho stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>Configure a entrada de trabalho
O trabalho obtém a entrada do ficheiro CSV que carregou anteriormente para o armazenamento de bolhas.

1. Após a criação do trabalho, sob **Job Topology** na lâmina de trabalho, clique na opção **Inputs.**    

2. Na lâmina **de entrada,** clique em **Adicionar Armazenamento de**  > **Entrada** de Fluxo

3. Preencha a lâmina **blob de armazenamento** com estes valores:

   
   |Campo  |Valor  |
   |---------|---------|
   |**Inserir pseudónimo** | Use o nome `datainput` e **selecione Selecione o armazenamento de blob da sua subscrição**       |
   |**Conta de armazenamento**  |  Selecione a conta de armazenamento que criou anteriormente.  |
   |**Contentor**  | Selecione o recipiente que criou anteriormente `azuresamldemoblob` ()        |
   |**Formato de serialização de eventos**  |  Selecione **CSV**       |

   ![Definições para nova entrada de trabalho stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Clique em **Guardar**.

### <a name="configure-the-job-output"></a>Configure a produção de trabalho
O trabalho envia resultados para o mesmo armazenamento de bolhas onde obtém entrada. 

1. Em **Job Topology** na lâmina de trabalho, clique na opção **Saídas.**  

2. Na lâmina **Outputs,** clique em **Adicionar**  > **o armazenamento Blob**e, em seguida, adicione uma saída com o pseudónimo `datamloutput` . 

3. Preencha a lâmina **blob de armazenamento** com estes valores:

   |Campo  |Valor  |
   |---------|---------|
   |**Pseudónimo de saída** | Use o nome `datamloutput` e **selecione Selecione o armazenamento de blob da sua subscrição**       |
   |**Conta de armazenamento**  |  Selecione a conta de armazenamento que criou anteriormente.  |
   |**Contentor**  | Selecione o recipiente que criou anteriormente `azuresamldemoblob` ()        |
   |**Formato de serialização de eventos**  |  Selecione **CSV**       |

   ![Definições para nova saída de trabalho do Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Clique em **Guardar**.   


### <a name="add-the-machine-learning-function"></a>Adicione a função de Aprendizagem automática 
Anteriormente publicou um modelo de Machine Learning para um serviço web. Neste cenário, quando o trabalho de Análise de Fluxo funciona, envia cada tweet da amostra da entrada para o serviço web para análise de sentimento. O serviço web Machine Learning devolve um sentimento `positive` `neutral` (, `negative` ou) e uma probabilidade de o tweet ser positivo. 

Nesta secção do tutorial, define-se uma função no trabalho de Análise de Fluxo. A função pode ser invocada para enviar um tweet para o serviço web e obter a resposta de volta. 

1. Certifique-se de que tem a chave URL e API do serviço web que descarregou anteriormente no livro do Excel.

2. Navegue para a sua lâmina de trabalho > **Funções**  >  **+ Adicionar**  >  **AzureML**

3. Preencha a lâmina de aprendizagem automática **Azure** com estes valores:

   |Campo  |Valor  |
   |---------|---------|
   | **Pseudónimo de função** | Utilize o nome `sentiment` e **selecione As definições de função de Aprendizagem automática Azure manualmente,** o que lhe dá a opção de introduzir o URL e a tecla.      |
   | **URL**| Cole o URL do serviço web.|
   |**Chave** | Cole a chave API. |
  
   ![Configurações para adicionar a função de machine learning para streaming de analíticos](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Clique em **Guardar**.

### <a name="create-a-query-to-transform-the-data"></a>Criar uma consulta para transformar os dados

Stream Analytics usa uma consulta declarativa baseada em SQL para examinar a entrada e processá-la. Nesta secção, cria-se uma consulta que lê cada tweet a partir da entrada e, em seguida, invoca a função machine learning para realizar a análise de sentimento. A consulta envia então o resultado para a saída que definiu (armazenamento de bolhas).

1. Volte para a lâmina geral do trabalho.

2.  Em **Job Topology,** clique na **caixa de consulta.**

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

    A consulta invoca a função que criou anteriormente ( `sentiment` ) para realizar a análise de sentimento em cada tweet na entrada. 

4. Clique em **Guardar** para guardar a consulta.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

Agora pode iniciar o trabalho stream Analytics.

### <a name="start-the-job"></a>Iniciar a tarefa
1. Volte para a lâmina geral do trabalho.

2. Clique em **Iniciar** na parte superior da lâmina.

3. No **trabalho Iniciar**, selecione **Custom**e, em seguida, selecione um dia antes de quando carregou o ficheiro CSV para o armazenamento de bolhas. Quando terminar, clique em **Iniciar**.  


### <a name="check-the-output"></a>Verifique a saída
1. Deixe o trabalho funcionar durante alguns minutos até ver atividade na caixa **de monitorização.** 

2. Se tiver uma ferramenta que normalmente utiliza para examinar o conteúdo do armazenamento de bolhas, utilize essa ferramenta para examinar o `azuresamldemoblob` recipiente. Em alternativa, faça os seguintes passos no portal Azure:

    1. No portal, encontre a `samldemo` conta de armazenamento, e dentro da conta, encontre o `azuresamldemoblob` recipiente. Vê dois ficheiros no recipiente: o ficheiro que contém os tweets de amostra e um ficheiro CSV gerado pela função Stream Analytics.
    2. Clique com o botão direito no ficheiro gerado e, em seguida, **selecione Download**. 

   ![Descarregue a saída de trabalho do CSV a partir do armazenamento blob](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Abra o ficheiro CSV gerado. Vê-se algo como o seguinte exemplo:  
   
   ![Stream Analytics Machine Learning, vista CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Ver métricas
Também pode ver métricas relacionadas com a função Azure Machine Learning. As seguintes métricas relacionadas com a função são apresentadas na caixa **de monitorização** na lâmina de trabalho:

* **Pedidos de função** indicam o número de pedidos enviados para um serviço web machine learning.  
* **Eventos de função** indicam o número de eventos no pedido. Por predefinição, cada pedido a um serviço web machine learning contém até 1.000 eventos.  


## <a name="next-steps"></a>Próximos passos

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Integrar API rest e machine learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)



