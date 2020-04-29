---
title: Integração azure Stream Analytics com Aprendizagem automática Azure
description: Este artigo descreve como configurar rapidamente um simples trabalho de Azure Stream Analytics que integra o Azure Machine Learning, utilizando uma função definida pelo utilizador.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.custom: seodec18
ms.openlocfilehash: b33aeeee03fa57d87a60fd4c1904d5e4a86dd004
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80067080"
---
# <a name="perform-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Execute análise de sentimento com Azure Stream Analytics e Azure Machine Learning Studio (clássico)

Este artigo descreve como configurar rapidamente um simples trabalho de Azure Stream Analytics que integra o Azure Machine Learning Studio (clássico). Você usa um modelo de análise de sentimentos de Machine Learning da Galeria de Inteligência Cortana para analisar dados de texto de streaming e determinar a pontuação de sentimento em tempo real. A utilização da Suíte de Inteligência Cortana permite-lhe realizar esta tarefa sem se preocupar com os meandros de construir um modelo de análise de sentimentos.

> [!TIP]
> É altamente recomendado utilizar [UDFs de Aprendizagem Automática Azure](machine-learning-udf.md) em vez do Azure Machine Learning Studio (clássico) UDF para melhorar o desempenho e a fiabilidade.

Pode aplicar o que aprende com este artigo a cenários como estes:

* Analisando o sentimento em tempo real no streaming de dados do Twitter.
* Analisando registos de conversas com clientes com pessoal de apoio.
* Avaliando comentários sobre fóruns, blogs e vídeos. 
* Muitos outros cenários de pontuação em tempo real e preditivos.

Num cenário real, obteria os dados diretamente de um fluxo de dados do Twitter. Para simplificar o tutorial, está escrito para que o trabalho de Streaming Analytics receba tweets de um ficheiro CSV no armazenamento de Azure Blob. Pode criar o seu próprio ficheiro CSV, ou pode utilizar um ficheiro CSV de amostra, como mostra a seguinte imagem:

![Tweets de amostra mostrados num ficheiro CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

O trabalho de Streaming Analytics que cria aplica o modelo de análise de sentimentos como uma função definida pelo utilizador (UDF) nos dados de texto da amostra da loja blob. A saída (resultado da análise de sentimento) é escrita na mesma loja blob num ficheiro CSV diferente. 

A figura que se segue demonstra esta configuração. Como notado, para um cenário mais realista, pode substituir o armazenamento de blob por streaming de dados do Twitter a partir de uma entrada do Azure Event Hubs. Além disso, você poderia construir uma visualização do [Microsoft Power BI](https://powerbi.microsoft.com/) em tempo real do sentimento agregado.    

![Visão geral da integração da aprendizagem automática stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que tem o seguinte:

* Uma subscrição ativa do Azure.
* Um ficheiro CSV com alguns dados. Pode descarregar o ficheiro mostrado anteriormente a partir do [GitHub,](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)ou pode criar o seu próprio ficheiro. Para este artigo, presume-se que está a usar o ficheiro do GitHub.

A um nível elevado, para completar as tarefas demonstradas neste artigo, faz o seguinte:

1. Crie uma conta de armazenamento Azure e um recipiente de armazenamento blob e carregue um ficheiro de entrada formado em CSV para o recipiente.
3. Adicione um modelo de análise de sentimentos da Galeria de Inteligência Cortana ao seu espaço de trabalho Azure Machine Learning Studio (clássico) e implemente este modelo como um serviço web no espaço de trabalho machine learning.
5. Crie um trabalho de Stream Analytics que chame este serviço web como uma função para determinar o sentimento para a entrada de texto.
6. Inicie o trabalho de Stream Analytics e verifique a saída.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Criar um recipiente de armazenamento e carregar o ficheiro de entrada CSV
Para este passo, pode utilizar qualquer ficheiro CSV, como o disponível no GitHub.

1. No portal Azure, clique em **Criar uma** > conta de**Armazenamento** > **Storage account**de Recursos .

2. Fornecer um`samldemo` nome (no exemplo). O nome só pode usar letras minúsculas e números, e deve ser único em todo o Azure. 

3. Especifique um grupo de recursos existente e especifique uma localização. Para localização, recomendamos que todos os recursos criados neste tutorial utilizem a mesma localização.

    ![fornecer detalhes da conta de armazenamento](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. No portal Azure, selecione a conta de armazenamento. Na lâmina da conta de armazenamento, ** + &nbsp;** clique em **Recipientes** e, em seguida, clique em Container para criar armazenamento de bolhas.

    ![Criar recipiente de armazenamento blob para entrada](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Forneça um nome para`azuresamldemoblob` o recipiente (no exemplo) e verifique se o **tipo de acesso** está definido para **Blob**. Quando tiver terminado, clique em **OK**.

    ![especificar detalhes do recipiente blob](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. Na lâmina dos **recipientes,** selecione o novo recipiente, que abre a lâmina para o recipiente.

7. Clique em **Carregar**.

    ![Botão 'Upload' para um recipiente](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. Na lâmina **de upload blob,** faça upload do ficheiro **sampleinput.csv** que descarregou anteriormente. Para o **tipo Blob,** selecione **bloco blob** e coloque o tamanho do bloco para 4 MB, o que é suficiente para este tutorial.

9. Clique no botão **upload** na parte inferior da lâmina.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Adicione o modelo de análise de sentimentos da Galeria de Inteligência Cortana

Agora que os dados da amostra estão numa bolha, pode ativar o modelo de análise de sentimentos na Cortana Intelligence Gallery.

1. Vá à página de modelos de análise de [sentimentos preditivos](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) na Galeria de Inteligência cortana.  

2. Clique **em Open in Studio**.  
   
   ![Stream Analytics Machine Learning, estúdio de aprendizagem automática aberto](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Inscreva-se para ir ao espaço de trabalho. Selecione uma localização.

4. Clique em **Correr** na parte inferior da página. O processo decorre, o que leva cerca de um minuto.

   ![executar experiência em Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Depois de o processo ter sido executado com sucesso, selecione **Deploy Web Service** na parte inferior da página.

   ![implementar experiência no Machine Learning Studio como um serviço web](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Para validar que o modelo de análise de sentimentos está pronto a ser utilizado, clique no botão **Teste.** Forneça a entrada de texto como "Amo a Microsoft". 

   ![experiência de teste no Estúdio de Aprendizagem automática](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Se o teste funcionar, verá um resultado semelhante ao seguinte exemplo:

   ![resultados dos testes no Estúdio de Aprendizagem automática](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Na coluna **Apps,** clique no link **Excel 2010 ou anterior** livro para descarregar um livro do Excel. O livro contém a chave API e o URL que necessita mais tarde para configurar o trabalho stream analytics.

    ![Stream Analytics Machine Learning, olhar rápido](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Crie um trabalho de Stream Analytics que utilize o modelo machine learning

Agora pode criar um trabalho de Stream Analytics que leia os tweets da amostra do ficheiro CSV no armazenamento de blob. 

### <a name="create-the-job"></a>Criar o trabalho

1. Vá ao [portal Azure.](https://portal.azure.com)  

2. Clique **em Criar um recurso** > **Internet of Things** > **Stream Analytics trabalho**. 

3. Nomeie `azure-sa-ml-demo`a função, especifique uma subscrição, especifique um grupo de recursos existente ou crie um novo e selecione a localização para o trabalho.

   ![especificar definições para o novo trabalho de Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>Configure a entrada de trabalho
O trabalho obtém a sua entrada do ficheiro CSV que carregou anteriormente para o armazenamento de blob.

1. Depois de criado o trabalho, sob **a Topologia** de Emprego na lâmina de trabalho, clique na opção **Inputs.**    

2. Na lâmina **de entrada,** clique em Adicionar**armazenamento de entrada de** **fluxo** >

3. Preencha a lâmina **de armazenamento Blob** com estes valores:

   
   |Campo  |Valor  |
   |---------|---------|
   |**Pseudónimo de entrada** | Use o `datainput` nome e selecione **Selecione armazenamento blob a partir da sua subscrição**       |
   |**Conta de armazenamento**  |  Selecione a conta de armazenamento que criou anteriormente.  |
   |**Contentor**  | Selecione o recipiente`azuresamldemoblob`que criou anteriormente ( )        |
   |**Formato de serialização de eventos**  |  Selecione **CSV**       |

   ![Definições para nova entrada de trabalho stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Clique em **Guardar**.

### <a name="configure-the-job-output"></a>Configure a saída de trabalho
O trabalho envia resultados para o mesmo armazenamento de bolhas onde obtém entrada. 

1. Em **Job Topology** na lâmina de trabalho, clique na opção **Saídas.**  

2. Na lâmina **outputs,** clique em **Adicionar** >**armazenamento Blob**, `datamloutput`e, em seguida, adicione uma saída com o pseudónimo . 

3. Preencha a lâmina **de armazenamento Blob** com estes valores:

   |Campo  |Valor  |
   |---------|---------|
   |**Pseudónimo de saída** | Use o `datamloutput` nome e selecione **Selecione armazenamento blob a partir da sua subscrição**       |
   |**Conta de armazenamento**  |  Selecione a conta de armazenamento que criou anteriormente.  |
   |**Contentor**  | Selecione o recipiente`azuresamldemoblob`que criou anteriormente ( )        |
   |**Formato de serialização de eventos**  |  Selecione **CSV**       |

   ![Definições para a nova saída de trabalho do Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Clique em **Guardar**.   


### <a name="add-the-machine-learning-function"></a>Adicione a função de Aprendizagem automática 
Anteriormente publicou um modelo de Machine Learning para um serviço web. Neste cenário, quando o trabalho de Análise de Fluxo saem, envia cada tweet de amostra da entrada para o serviço web para análise de sentimentos. O serviço web Machine Learning`positive` `neutral`devolve `negative`um sentimento ( ou ) e uma probabilidade de o tweet ser positivo. 

Nesta secção do tutorial, define uma função no trabalho de Análise de Fluxo. A função pode ser invocada para enviar um tweet para o serviço web e obter a resposta de volta. 

1. Certifique-se de que tem o URL do serviço web e a chave API que descarregou anteriormente no livro excel.

2. Navegue para a sua lâmina de trabalho > **Funções** > **+ Adicione** > **AzureML**

3. Preencha a lâmina de **função De Aprendizagem automática Azure** com estes valores:

   |Campo  |Valor  |
   |---------|---------|
   | **Pseudónimo de função** | Utilize o `sentiment` nome e selecione **Fornecer manualmente as definições da função De aprendizagem automática Azure,** o que lhe dá a opção de introduzir o URL e a chave.      |
   | **URL**| Colhe o URL do serviço web.|
   |**Chave** | Cola a chave API. |
  
   ![Definições para adicionar função de Machine Learning ao trabalho de Streaming Analytics](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Clique em **Guardar**.

### <a name="create-a-query-to-transform-the-data"></a>Criar uma consulta para transformar os dados

O Stream Analytics usa uma consulta declarativa baseada em SQL para examinar a entrada e processá-la. Nesta secção, cria-se uma consulta que lê cada tweet a partir da entrada e, em seguida, invoca a função Machine Learning para realizar a análise de sentimentos. A consulta envia então o resultado para a saída que definiu (armazenamento de bolhas).

1. Volte para a lâmina de visão geral do trabalho.

2.  Em **Topologia de Trabalho,** clique na caixa **de consulta.**

3. Insira a seguinte consulta:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM datainput  
    )  

    SELECT text, result.[Score]  
    INTO datamloutput
    FROM sentiment  
    ```    

    A consulta invoca a função que`sentiment`criou anteriormente , a fim de realizar análises de sentimento em cada tweet na entrada. 

4. Clique em **Guardar** para guardar a consulta.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

Agora pode começar o trabalho de Stream Analytics.

### <a name="start-the-job"></a>Iniciar a tarefa
1. Volte para a lâmina de visão geral do trabalho.

2. Clique **em Começar** na parte superior da lâmina.

3. No **trabalho inicial**, selecione **Custom**, e, em seguida, selecione um dia antes de ter enviado o ficheiro CSV para o armazenamento blob. Quando terminar, clique **em Iniciar**.  


### <a name="check-the-output"></a>Verifique a saída
1. Deixe o trabalho funcionar durante alguns minutos até ver a atividade na caixa **de monitorização.** 

2. Se tiver uma ferramenta que normalmente utiliza para examinar o conteúdo do `azuresamldemoblob` armazenamento de bolhas, utilize essa ferramenta para examinar o recipiente. Em alternativa, faça os seguintes passos no portal Azure:

    1. No portal, encontre `samldemo` a conta de armazenamento, `azuresamldemoblob` e dentro da conta, encontre o recipiente. Vê dois ficheiros no recipiente: o ficheiro que contém os tweets da amostra e um ficheiro CSV gerado pelo trabalho do Stream Analytics.
    2. Clique no ficheiro gerado e, em seguida, selecione **Download**. 

   ![Baixe a saída de trabalho csv do armazenamento blob](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Abra o ficheiro CSV gerado. Vê algo como o seguinte exemplo:  
   
   ![Stream Analytics Machine Learning, vista CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Ver métricas
Também pode ver métricas relacionadas com a função Azure Machine Learning. As seguintes métricas relacionadas com a função são apresentadas na caixa **de monitorização** na lâmina de trabalho:

* Pedidos de **função** indica o número de pedidos enviados para um serviço web de Aprendizagem automática.  
* **Eventos de Função** indica o número de eventos no pedido. Por padrão, cada pedido a um serviço web machine learning contém até 1.000 eventos.  


## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Integrar a API rest e machine learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)



