---
title: Integração do Azure Stream Analytics com o Azure Machine Learning
description: Este artigo descreve como configurar rapidamente um trabalho simples de Azure Stream Analytics que integra Azure Machine Learning, usando uma função definida pelo usuário.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.custom: seodec18
ms.openlocfilehash: b078c92d02c55080cb84c386b7bbdabf3e1f85bf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467891"
---
# <a name="perform-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic-preview"></a>Executar análise de sentimentos com Azure Stream Analytics e Azure Machine Learning Studio (clássico) (visualização)
Este artigo descreve como configurar rapidamente um trabalho simples de Azure Stream Analytics que integra Azure Machine Learning Studio (clássico). Você usa um modelo de análise de sentimentos Machine Learning da Cortana Intelligence Gallery para analisar dados de texto de streaming e determinar a pontuação de sentimentos em tempo real. Usar o Cortana Intelligence Suite permite realizar essa tarefa sem se preocupar com os pormenores de criar um modelo de análise de sentimentos.

Você pode aplicar o que aprende neste artigo a cenários como estes:

* Análise de sentimentos em tempo real sobre streaming de dados do Twitter.
* Análise de registros de chats de clientes com a equipe de suporte.
* Avaliar comentários em fóruns, Blogs e vídeos. 
* Muitos outros cenários de Pontuação preditiva em tempo real.

Em um cenário do mundo real, você obteria os dados diretamente de um fluxo de dados do Twitter. Para simplificar o tutorial, ele é escrito para que o trabalho do Stream Analytics obtenha tweets de um arquivo CSV no armazenamento de BLOBs do Azure. Você pode criar seu próprio arquivo CSV ou pode usar um arquivo CSV de exemplo, conforme mostrado na imagem a seguir:

![Tweets de exemplo mostrados em um arquivo CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

O trabalho do Stream Analytics que você cria aplica o modelo de análise de sentimentos como uma UDF (função definida pelo usuário) nos dados de texto de exemplo do repositório de BLOB. A saída (o resultado da análise de sentimentos) é gravada no mesmo repositório de BLOB em um arquivo CSV diferente. 

A figura a seguir demonstra essa configuração. Conforme observado, para um cenário mais realista, você pode substituir o armazenamento de BLOBs pelo fluxo de dados do Twitter de uma entrada dos hubs de eventos do Azure. Além disso, você pode criar uma visualização em tempo real do [Microsoft Power bi](https://powerbi.microsoft.com/) da opinião agregada.    

![Visão geral da integração do Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que tem o seguinte:

* Uma subscrição ativa do Azure.
* Um arquivo CSV com alguns dados. Você pode baixar o arquivo mostrado anteriormente no [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)ou pode criar seu próprio arquivo. Neste artigo, supõe-se que você esteja usando o arquivo do GitHub.

Em um alto nível, para concluir as tarefas demonstradas neste artigo, faça o seguinte:

1. Crie uma conta de armazenamento do Azure e um contêiner de armazenamento de BLOBs e carregue um arquivo de entrada formatado em CSV para o contêiner.
3. Adicione um modelo de análise de sentimentos da Cortana Intelligence Gallery ao seu espaço de trabalho Azure Machine Learning Studio (clássico) e implante esse modelo como um serviço Web no espaço de trabalho Machine Learning.
5. Crie um trabalho de Stream Analytics que chama esse serviço Web como uma função para determinar o sentimentos para a entrada de texto.
6. Inicie o trabalho de Stream Analytics e verifique a saída.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Criar um contêiner de armazenamento e carregar o arquivo de entrada CSV
Para esta etapa, você pode usar qualquer arquivo CSV, como aquele disponível no GitHub.

1. No portal do Azure, clique em **criar um recurso** > **armazenamento** > **conta de armazenamento**.

2. Forneça um nome (`samldemo` no exemplo). O nome pode usar apenas letras minúsculas e números e deve ser exclusivo no Azure. 

3. Especifique um grupo de recursos existente e especifique um local. Para o local, recomendamos que todos os recursos criados neste tutorial usem o mesmo local.

    ![fornecer detalhes da conta de armazenamento](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. Na portal do Azure, selecione a conta de armazenamento. Na folha da conta de armazenamento, clique em **contêineres** e, em seguida, clique em **+&nbsp;contêiner** para criar o armazenamento de BLOBs.

    ![Criar contêiner de armazenamento de BLOBs para entrada](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Forneça um nome para o contêiner (`azuresamldemoblob` no exemplo) e verifique se o **tipo de acesso** está definido como **blob**. Quando tiver terminado, clique em **OK**.

    ![especificar detalhes do contêiner de BLOBs](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. Na folha **contêineres** , selecione o novo contêiner, que abre a folha desse contêiner.

7. Clique em **Carregar**.

    ![Botão ' carregar ' para um contêiner](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. Na folha **carregar blob** , carregue o arquivo **sampleinput. csv** que você baixou anteriormente. Para **tipo de blob**, selecione **blob de blocos** e defina o tamanho do bloco como 4 MB, o que é suficiente para este tutorial.

9. Clique no botão **carregar** na parte inferior da folha.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Adicionar o modelo de análise de sentimentos da Cortana Intelligence Gallery

Agora que os dados de exemplo estão em um blob, você pode habilitar o modelo de análise de sentimentos em Cortana Intelligence Gallery.

1. Vá para a página [modelo de análise de sentimentos preditiva](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) na Cortana Intelligence Gallery.  

2. Clique em **abrir no Studio**.  
   
   ![Stream Analytics Machine Learning, abra Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Entre para ir para o espaço de trabalho. Selecione uma localização.

4. Clique em **executar** na parte inferior da página. O processo é executado, o que leva cerca de um minuto.

   ![executar experimento no Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Depois que o processo for executado com êxito, selecione **implantar serviço Web** na parte inferior da página.

   ![implantar experimento no Machine Learning Studio como um serviço Web](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Para validar que o modelo de análise de sentimentos está pronto para uso, clique no botão **testar** . Fornecer entrada de texto como "Eu adoro a Microsoft". 

   ![teste de experimento em Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Se o teste funcionar, você verá um resultado semelhante ao exemplo a seguir:

   ![resultados de teste em Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Na coluna **aplicativos** , clique no link do **Excel 2010 ou da pasta de trabalho anterior** para baixar uma pasta de trabalho do Excel. A pasta de trabalho contém a chave de API e a URL que você precisa mais tarde para configurar o trabalho de Stream Analytics.

    ![Stream Analytics Machine Learning, visão rápida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Criar um trabalho de Stream Analytics que usa o modelo de Machine Learning

Agora você pode criar um trabalho de Stream Analytics que lê os tweets de exemplo do arquivo CSV no armazenamento de BLOBs. 

### <a name="create-the-job"></a>Criar o trabalho

1. Aceda ao [Portal do Azure](https://portal.azure.com).  

2. Clique em **criar um recurso** > **Internet das coisas** > **Stream Analytics trabalho**. 

3. Nomeie o trabalho `azure-sa-ml-demo`, especifique uma assinatura, especifique um grupo de recursos existente ou crie um novo e selecione o local para o trabalho.

   ![especificar as configurações para o novo trabalho de Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>Configurar a entrada do trabalho
O trabalho obtém sua entrada do arquivo CSV que você carregou anteriormente para o armazenamento de BLOBs.

1. Depois que o trabalho tiver sido criado, em **topologia do trabalho** na folha do trabalho, clique na opção **entradas** .    

2. Na folha **entradas** , clique em **Adicionar entrada de fluxo** >**armazenamento de BLOBs**

3. Preencha a folha de **armazenamento de BLOBs** com estes valores:

   
   |Campo  |Valor  |
   |---------|---------|
   |**Alias de entrada** | Use o nome `datainput` e selecione **selecionar armazenamento de blobs de sua assinatura**       |
   |**Conta de armazenamento**  |  Selecione a conta de armazenamento que você criou anteriormente.  |
   |**Container**  | Selecione o contêiner que você criou anteriormente (`azuresamldemoblob`)        |
   |**Formato de serialização de eventos**  |  Selecionar **CSV**       |

   ![Configurações para Nova entrada de Stream Analytics trabalho](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Clique em **Guardar**.

### <a name="configure-the-job-output"></a>Configurar a saída do trabalho
O trabalho envia resultados para o mesmo armazenamento de BLOBs onde ele obtém a entrada. 

1. Em **topologia do trabalho** na folha do trabalho, clique na opção **saídas** .  

2. Na folha **saídas** , clique em **Adicionar** >**armazenamento de BLOBs**e, em seguida, adicione uma saída com o alias `datamloutput`. 

3. Preencha a folha de **armazenamento de BLOBs** com estes valores:

   |Campo  |Valor  |
   |---------|---------|
   |**Alias de saída** | Use o nome `datamloutput` e selecione **selecionar armazenamento de blobs de sua assinatura**       |
   |**Conta de armazenamento**  |  Selecione a conta de armazenamento que você criou anteriormente.  |
   |**Container**  | Selecione o contêiner que você criou anteriormente (`azuresamldemoblob`)        |
   |**Formato de serialização de eventos**  |  Selecionar **CSV**       |

   ![Configurações para nova saída de trabalho de Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Clique em **Guardar**.   


### <a name="add-the-machine-learning-function"></a>Adicionar a função Machine Learning 
Anteriormente, você publicou um modelo de Machine Learning para um serviço Web. Nesse cenário, quando o trabalho de análise de fluxo é executado, ele envia cada tweet de exemplo da entrada para o serviço Web para análise de sentimentos. O serviço Web Machine Learning retorna um sentimentos (`positive`, `neutral`ou `negative`) e uma probabilidade de o tweet ser positivo. 

Nesta seção do tutorial, você define uma função no trabalho de análise de fluxo. A função pode ser chamada para enviar um tweet para o serviço Web e obter a resposta de volta. 

1. Verifique se você tem a URL do serviço Web e a chave de API que você baixou anteriormente na pasta de trabalho do Excel.

2. Navegue até sua folha de trabalho > **funções** >  **+ Adicionar** > **AzureML**

3. Preencha a folha da **função Azure Machine Learning** com estes valores:

   |Campo  |Valor  |
   |---------|---------|
   | **Alias da função** | Use o nome `sentiment` e selecione **fornecer Azure Machine Learning configurações de função manualmente** , o que lhe dá uma opção para inserir a URL e a chave.      |
   | **URL**| Cole a URL do serviço Web.|
   |**Chave** | Cole a chave de API. |
  
   ![Configurações para adicionar Machine Learning função ao trabalho Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Clique em **Guardar**.

### <a name="create-a-query-to-transform-the-data"></a>Criar uma consulta para transformar os dados

Stream Analytics usa uma consulta declarativa baseada em SQL para examinar a entrada e processá-la. Nesta seção, você cria uma consulta que lê cada tweet da entrada e, em seguida, invoca a função Machine Learning para executar a análise de sentimentos. Em seguida, a consulta envia o resultado para a saída que você definiu (armazenamento de BLOBs).

1. Retorne à folha de visão geral do trabalho.

2.  Em **topologia do trabalho**, clique na caixa **consulta** .

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

    A consulta invoca a função que você criou anteriormente (`sentiment`) para executar a análise de sentimentos em cada tweet na entrada. 

4. Clique em **salvar** para salvar a consulta.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

Agora você pode iniciar o trabalho de Stream Analytics.

### <a name="start-the-job"></a>Iniciar a tarefa
1. Retorne à folha de visão geral do trabalho.

2. Clique em **Iniciar** na parte superior da folha.

3. No **trabalho inicial**, selecione **personalizado**e, em seguida, selecione um dia antes de quando você carregou o arquivo CSV no armazenamento de BLOBs. Quando terminar, clique em **Iniciar**.  


### <a name="check-the-output"></a>Verificar a saída
1. Deixe o trabalho ser executado por alguns minutos até que você veja a atividade na caixa **monitoramento** . 

2. Se você tiver uma ferramenta que normalmente usa para examinar o conteúdo do armazenamento de BLOBs, use essa ferramenta para examinar o contêiner de `azuresamldemoblob`. Como alternativa, execute as seguintes etapas no portal do Azure:

    1. No portal, localize a conta de armazenamento `samldemo` e, dentro da conta, localize o contêiner `azuresamldemoblob`. Você vê dois arquivos no contêiner: o arquivo que contém os tweets de exemplo e um arquivo CSV gerado pelo trabalho de Stream Analytics.
    2. Clique com o botão direito do mouse no arquivo gerado e selecione **baixar**. 

   ![Baixar a saída do trabalho CSV do armazenamento de BLOBs](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Abra o arquivo CSV gerado. Você verá algo semelhante ao exemplo a seguir:  
   
   ![Stream Analytics Machine Learning, exibição de CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Ver métricas
Você também pode exibir Azure Machine Learning métricas relacionadas à função. As seguintes métricas relacionadas à função são exibidas na caixa **monitoramento** na folha de trabalho:

* **Solicitações de função** indica o número de solicitações enviadas a um serviço Web Machine Learning.  
* **Eventos de função** indica o número de eventos na solicitação. Por padrão, cada solicitação para um serviço Web Machine Learning contém até 1.000 eventos.  


## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Integrar API REST e Machine Learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)



