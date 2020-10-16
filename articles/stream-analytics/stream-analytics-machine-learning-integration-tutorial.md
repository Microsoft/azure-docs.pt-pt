---
title: Integração Azure Stream Analytics com Azure Machine Learning Studio (clássico)
description: Este artigo descreve como configurar rapidamente um simples trabalho Azure Stream Analytics que integra o Azure Machine Learning Studio (clássico), utilizando uma função definida pelo utilizador.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: 529b1ce8026d9880bbc8caf87ab59148baf92df3
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019465"
---
# <a name="do-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Faça análise de sentimento com Azure Stream Analytics e Azure Machine Learning Studio (clássico)

Este artigo mostra-lhe como configurar um simples trabalho Azure Stream Analytics que usa o Azure Machine Learning Studio (clássico) para análise de sentimentos. Você usa um modelo de análise de sentimento (clássico) da Cortana Intelligence Gallery para analisar dados de streaming de texto e determinar a pontuação do sentimento.

> [!TIP]
> É altamente recomendado usar [UDFs de aprendizagem de máquinas Azure](machine-learning-udf.md) em vez de Azure Machine Learning Studio (clássico) UDF para melhorar o desempenho e a fiabilidade.

Pode aplicar o que aprende com este artigo a cenários como estes:

* Analisar o sentimento em tempo real no streaming de dados do Twitter.
* Analisar registos de conversas de clientes com pessoal de apoio.
* Avaliação de comentários em fóruns, blogs e vídeos.
* Muitos outros cenários de pontuação preditiva em tempo real.

O trabalho de Streaming Analytics que cria aplica o modelo de análise de sentimento como uma função definida pelo utilizador (UDF) nos dados de texto da amostra da loja blob. A saída (o resultado da análise do sentimento) é escrita para a mesma loja blob em um ficheiro CSV diferente. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem o seguinte:

* Uma subscrição ativa do Azure.

* Um ficheiro CSV com alguns dados do Twitter. Pode descarregar um ficheiro de amostras do [GitHub,](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)ou pode criar o seu próprio ficheiro. Num cenário real, obteria os dados diretamente de um fluxo de dados do Twitter.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Crie um recipiente de armazenamento e carrequiva o ficheiro de entrada CSV

Neste passo, faça o upload de um ficheiro CSV para o seu recipiente de armazenamento.

1. No portal Azure, selecione **Criar uma**conta de  >  **armazenamento**  >  **de**recursos .

2. Preencha o separador Básicos com os *seguintes* detalhes e deixe os valores predefinidos para os restantes campos:

   |Campo  |Valor  |
   |---------|---------|
   |Subscrição|Escolha a sua subscrição.|
   |Grupo de recursos|Escolha o seu grupo de recursos.|
   |Nome da conta de armazenamento|Introduza um nome para a conta de armazenamento. O nome deve ser único em Azure.|
   |Localização|Escolher uma localização. Todos os recursos devem usar o mesmo local.|
   |Tipo de conta|BlobStorage|

   ![fornecer detalhes da conta de armazenamento](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

3. Selecione **Review + Criar**. Em seguida, **selecione Criar** para implementar a sua conta de armazenamento.

4. Quando a implementação estiver concluída, navegue para a sua conta de armazenamento. Em **Serviço Blob**, selecione **Contentores**. Em seguida, selecione **+ Recipiente** para criar um novo recipiente.

   ![Criar recipiente de armazenamento de bolhas para entrada](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Forneça um nome para o recipiente e verifique se o **nível de acesso público** está definido para **Privado**. Quando concluir, selecione **Criar**.

   ![especificar detalhes do recipiente blob](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. Navegue para o recipiente recém-criado e selecione **Upload**. Faça o upload do ficheiro **sampleinput.csv** que descarregou anteriormente.

   ![Botão 'Upload' para um recipiente](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Adicione o modelo de análise de sentimento da Galeria de Inteligência cortana

Agora que os dados da amostra estão numa bolha, pode ativar o modelo de análise de sentimento na Cortana Intelligence Gallery.

1. Vá à página [do modelo de análise de sentimentos preditivos](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) na Galeria de Inteligência cortana.  

2. Selecione **Open in Studio (clássico)**.  
   
   ![Stream Analytics Azure Machine Learning Studio (clássico), open Studio (clássico)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Inscreva-se para ir ao espaço de trabalho. Selecione uma localização.

4. Selecione **Executar** na parte inferior da página. O processo decorre, o que leva cerca de um minuto.

   ![Experimente em Estúdio (clássico)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Depois de o processo ter sido executado com sucesso, selecione Implementar o **Serviço Web** na parte inferior da página.

   ![Implementar experiência em Studio (clássico) como um serviço web](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Para validar que o modelo de análise de sentimento está pronto a ser utilizado, selecione o botão **Teste.** Fornecer entrada de texto como "I love Microsoft".

   ![Experiência de teste em Estúdio (clássico)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

   Se o teste funcionar, vê um resultado semelhante ao seguinte exemplo:

   ![Resultados dos testes em Studio (clássico)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Na coluna **Apps,** selecione o link **excel 2010 ou** anterior para descarregar um livro do Excel. O livro contém a chave API e o URL que precisa mais tarde para configurar o trabalho stream Analytics.

    ![Stream Analytics Azure Machine Learning Studio (clássico), olhar rápido](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-studio-classic-model"></a>Crie um trabalho stream Analytics que use o modelo Studio (clássico)

Agora pode criar um trabalho stream Analytics que lê os tweets de amostra do ficheiro CSV no armazenamento de bolhas.

### <a name="create-the-job"></a>Criar o trabalho

Vá ao [portal Azure](https://portal.azure.com) e crie um trabalho stream Analytics. Se não estiver familiarizado com este processo, consulte [Criar um trabalho de Stream Analytics utilizando o portal Azure](stream-analytics-quick-create-portal.md).

### <a name="configure-the-job-input"></a>Configure a entrada de trabalho

O trabalho obtém a entrada do ficheiro CSV que carregou anteriormente para o armazenamento de bolhas.

1. Navegue para a tarefa do Stream Analytics. Em **Job Topology,** selecione a opção **Inputs.** **Selecione Adicionar Armazenamento de Entrada de Fluxo**  > **Blob**.

2. Preencha os detalhes **do Blob Storage** com os seguintes valores:

   |Campo  |Valor  |
   |---------|---------|
   |Alias de entrada|Dê um nome à sua entrada. Lembre-se deste pseudónimo para quando escrever a sua consulta.|
   |Subscrição|Selecione a sua subscrição.|
   |Conta de armazenamento|Selecione a conta de armazenamento que esou no passo anterior.|
   |Contentor|Selecione o recipiente que criou no passo anterior.|
   |Formato de serialização de eventos|CSV|

3. Selecione **Guardar**.

### <a name="configure-the-job-output"></a>Configure a produção de trabalho

O trabalho envia resultados para o mesmo armazenamento de bolhas onde obtém entrada.

1. Navegue para a tarefa do Stream Analytics. Em **Job Topology,** selecione a opção **Saídas.** **Selecione**  >  **Adicionar armazenamento Blob**.

2. Preencha o **formulário de Armazenamento Blob** com estes valores:

   |Campo  |Valor  |
   |---------|---------|
   |Alias de entrada|Dê um nome à sua entrada. Lembre-se deste pseudónimo para quando escrever a sua consulta.|
   |Subscrição|Selecione a sua subscrição.|
   |Conta de armazenamento|Selecione a conta de armazenamento que esou no passo anterior.|
   |Contentor|Selecione o recipiente que criou no passo anterior.|
   |Formato de serialização de eventos|CSV|

3. Selecione **Guardar**.

### <a name="add-the-studio-classic-function"></a>Adicione a função Studio (clássico)

Anteriormente publicou um modelo studio (clássico) para um serviço web. Neste cenário, quando o trabalho de Análise de Fluxo funciona, envia cada tweet da amostra da entrada para o serviço web para análise de sentimento. O serviço web Studio (clássico) devolve um sentimento `positive` `neutral` (, `negative` ou) e uma probabilidade de o tweet ser positivo.

Nesta secção, define-se uma função no trabalho de Análise de Fluxo. A função pode ser invocada para enviar um tweet para o serviço web e obter a resposta de volta.

1. Certifique-se de que tem a chave URL e API do serviço web que descarregou anteriormente no livro do Excel.

2. Navegue para a tarefa do Stream Analytics. Em seguida, **selecione Funções**  >  **+ Adicionar**  >  **Azure ML Studio**

3. Preencha o formulário **de função Azure Machine Learning** com os seguintes valores:

   |Campo  |Valor  |
   |---------|---------|
   | Pseudónimo de função | Utilize o nome `sentiment` e **selecione As definições de função de Aprendizagem automática Azure manualmente**, o que lhe dá a opção de introduzir o URL e a tecla.      |
   | URL| Cole o URL do serviço web.|
   |Chave | Cole a chave API. |

4. selecionar **Guardar**.

### <a name="create-a-query-to-transform-the-data"></a>Criar uma consulta para transformar os dados

Stream Analytics usa uma consulta declarativa baseada em SQL para examinar a entrada e processá-la. Nesta secção, você cria uma consulta que lê cada tweet a partir da entrada e, em seguida, invoca a função Studio (clássico) para executar a análise de sentimento. A consulta envia então o resultado para a saída que definiu (armazenamento de bolhas).

1. Voltar à visão geral do trabalho stream Analytics.

2. Em **Job Topology**, selecione **Consulta**.

3. Introduza a seguinte consulta:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM <input>  
    )  

    SELECT text, result.[Score]  
    INTO <output>
    FROM sentiment  
    ```    

   A consulta invoca a função que criou anteriormente `sentiment` () para realizar a análise de sentimento em cada tweet na entrada.

4. **selecione Guardar** para guardar a consulta.

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

Agora pode iniciar o trabalho stream Analytics.

### <a name="start-the-job"></a>Iniciar a tarefa

1. Voltar à visão geral do trabalho stream Analytics.

2. **selecione Iniciar** no topo da página.

3. No **trabalho Iniciar**, selecione **Custom**e, em seguida, selecione um dia antes de quando carregou o ficheiro CSV para o armazenamento de bolhas. Quando terminar, selecione **Iniciar**.  

### <a name="check-the-output"></a>Verifique a saída

1. Deixe o trabalho funcionar durante alguns minutos até ver atividade na caixa **de monitorização.**

2. Se tiver uma ferramenta que normalmente utiliza para examinar o conteúdo do armazenamento de bolhas, utilize essa ferramenta para examinar o recipiente. Em alternativa, faça os seguintes passos no portal Azure:

      1. No portal Azure, encontre a sua conta de armazenamento e, dentro da conta, encontre o recipiente. Vê dois ficheiros no recipiente: o ficheiro que contém os tweets de amostra e um ficheiro CSV gerado pela função Stream Analytics.
      2. Clique com o botão direito no ficheiro gerado e, em seguida, **selecione Download**.

3. Abra o ficheiro CSV gerado. Vê-se algo como o seguinte exemplo:  

   ![Stream Analytics Azure Machine Learning Studio (clássico), vista CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

### <a name="view-metrics"></a>Ver métricas

Também pode ver métricas relacionadas com a função studio (clássica). As seguintes métricas relacionadas com a função são apresentadas na caixa de **monitorização** da visão geral do trabalho:

* **Pedidos de função** indicam o número de pedidos enviados para um serviço web Studio (clássico).  
* **Eventos de função** indicam o número de eventos no pedido. Por padrão, cada pedido a um serviço web studio (clássico) contém até 1.000 eventos.

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Integrar o REST API e o Machine Learning Studio (clássico)](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
