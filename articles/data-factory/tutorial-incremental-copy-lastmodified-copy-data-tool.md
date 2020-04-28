---
title: Ferramenta de dados para copiar ficheiros novos e atualizados de forma incremental
description: Crie uma fábrica de dados Azure e, em seguida, use a ferramenta Copy Data para carregar gradualmente novos ficheiros com base no LastModifiedDate.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/18/2020
ms.openlocfilehash: 3098ca0d3d5e41c298d3058ffa84fcf129648281
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81399491"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Copie gradualmente ficheiros novos e alterados com base no LastModificadoDate utilizando a ferramenta Copy Data

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste tutorial, você vai usar o portal Azure para criar uma fábrica de dados. Em seguida, utilizará a ferramenta Copy Data para criar um pipeline que copia gradualmente novos e alterados ficheiros apenas, desde o armazenamento do Azure Blob até ao armazenamento azure Blob. Usa `LastModifiedDate` para determinar que ficheiros copiar.

Depois de completar os passos aqui, a Azure Data Factory digitalizará todos `LastModifiedDate`os ficheiros na loja de origem, aplicará o filtro de ficheiros por , e copiará para a loja de destino apenas ficheiros novos ou que tenham sido atualizados desde a última vez. Note que se a Data Factory digitalizar um grande número de ficheiros, ainda deverá esperar longas durações. A verificação de ficheiros é morosa, mesmo quando a quantidade de dados copiados é reduzida.

> [!NOTE]
> Se não estiver familiarizado com o Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, completará estas tarefas:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Utilizar a ferramenta Copiar Dados para criar um pipeline.
> * Monitorizar o pipeline e execuções de atividades.

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento Azure**: Utilize o armazenamento blob para as lojas de dados de origem e pia. Se não tiver uma conta de Armazenamento Azure, siga as instruções em [Criar uma conta](../storage/common/storage-account-create.md)de armazenamento .

## <a name="create-two-containers-in-blob-storage"></a>Criar dois recipientes no armazenamento blob

Prepare o seu armazenamento Blob para o tutorial completando estes passos:

1. Criar um recipiente chamado **fonte.** Pode utilizar várias ferramentas para executar esta tarefa, como o [Azure Storage Explorer.](https://storageexplorer.com/)

2. Criar um recipiente chamado **destino.**

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No painel esquerdo, selecione **Criar um recurso**. Selecione **Analytics** > **Data Factory**:

   ![Selecione Fábrica de Dados](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**.

   O nome da fábrica de dados tem de ser globalmente exclusivo. Pode receber esta mensagem de erro:

   ![Nome não disponível mensagem de erro](./media/doc-common-process/name-not-available-error.png)

   Se receber uma mensagem de erro relacionada com o valor do nome, introduza um nome diferente para a fábrica de dados. Por exemplo, utilize o nome _**oseunome**_**ADFTutorialDataFactory**. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
3. Em **Subscrição,** selecione a subscrição Azure na qual irá criar a nova fábrica de dados.
4. No âmbito do **Grupo de Recursos,** dê um destes passos:

    * Selecione **Use existente** e, em seguida, selecione um grupo de recursos existente na lista.

    * Selecione **Criar novo** e, em seguida, insira um nome para o grupo de recursos.
         
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).

5. Em **Versão**, selecione **V2**.
6. Em **Localização**, selecione a localização para a fábrica de dados. Apenas locais apoiados aparecem na lista. As lojas de dados (por exemplo, Azure Storage e Azure SQL Database) e calcula (por exemplo, Azure HDInsight) que a sua fábrica de dados utiliza pode estar em outros locais e regiões.
8. Selecione **Criar**.
9. Após a criação da fábrica de dados, aparece a página inicial da fábrica de dados.
10. Para abrir a interface de utilizador da Azure Data Factory (UI) num separador, selecione o azulejo **do Autor & Monitor:**

    ![Home page da fábrica de dados](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Utilizar a ferramenta Copiar Dados para criar um pipeline

1. Na página **Let's start,** selecione o azulejo **Copy Data** para abrir a ferramenta Copy Data:

   ![Cópia De Dados](./media/doc-common-process/get-started-page.png)

2. Na página **Propriedades,** tome os seguintes passos:

    a. Sob **o nome de tarefa,** introduza **DeltaCopyFromBlobPipeline**.

    b. Sob **a cadência de tarefaou no calendário**de tarefas, selecione Executar **regularmente no horário**.

    c. No **tipo de gatilho,** selecione **a janela Tumbling**.

    d. Em **Recurrence,** **insira 15 minutos( s)**.

    e. Selecione **Seguinte**.

    Data Factory cria um pipeline com o nome de tarefa especificado.

    ![Copiar página de propriedades de dados](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Na página da loja de **dados Source,** preencha estes passos:

    a. Selecione **Criar uma nova ligação** para adicionar uma ligação.

    b. Selecione **Armazenamento Azure Blob** na galeria e, em seguida, selecione **Continue:**

    ![Selecione Armazenamento de Blog Seleta Azure](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Na página **New Linked Service (Azure Blob Storage),** selecione a sua conta de armazenamento na lista de nomes da **conta de armazenamento.** Teste a ligação e, em seguida, **selecione Criar**.

    d. Selecione o novo serviço ligado e, em seguida, selecione **Seguinte:**

   ![Selecione o novo serviço ligado](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na página **Escolher o ficheiro ou pasta de entrada**, complete os seguintes passos:

    a. Procure e selecione **a** pasta fonte e, em seguida, selecione **Escolher**.

    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. No **comportamento de carregamento de ficheiros,** selecione carga **incremental: LastModifiedDate**.

    c. Selecione **cópia Binária** e, em seguida, selecione **Seguinte:**

     ![Escolha o ficheiro de entrada ou página de pasta](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Na página da loja de **dados Destination,** selecione o serviço **AzureBlobStorage** que criou. Esta é a mesma conta de armazenamento que a loja de dados de origem. Em seguida, selecione **Seguinte**.

6. Na página **Escolher o ficheiro ou pasta de saída**, complete os seguintes passos:

    a. Procure e selecione a pasta de **destino** e, em seguida, selecione **Escolher:**

    ![Escolha o ficheiro de saída ou página de pasta](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Selecione **Seguinte**.

7. Na página **Definições**, selecione **Seguinte**.

8. Na página **Resumo,** reveja as definições e, em seguida, selecione **Next**.

    ![Página de resumo](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Na **Página de implementação**, selecione **Monitorizar** para monitorizar o pipeline (tarefa).

    ![Página de implementação](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. A aplicação muda para o separador **Monitor.** Vê o estado do oleoduto. Selecione **Atualizar** para atualizar a lista. Selecione o link em **PIPELINE NAME** para ver os detalhes de execução da atividade ou para executar novamente o gasoduto.

    ![Atualizar a lista e ver detalhes de execução de atividade](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Há apenas uma atividade (a atividade de cópia) no oleoduto, por isso só se vê uma entrada. Para mais detalhes sobre o funcionamento da cópia, selecione o link **Detalhes** (o ícone dos óculos) na coluna **'NOME** DE ATIVIDADE'. Para mais detalhes sobre as propriedades, consulte a [visão geral da atividade da Cópia](copy-activity-overview.md).

    ![Copiar atividade no gasoduto](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Como não existem ficheiros no recipiente de origem na sua conta de armazenamento Blob, não verá ficheiros copiados para o contentor de destino na conta:

    ![Não há ficheiros no recipiente de origem ou no contentor de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Crie um ficheiro de texto vazio e nomeie-o **file1.txt**. Faça upload deste ficheiro de texto para o recipiente de origem na sua conta de armazenamento. Pode utilizar várias ferramentas para executar estas tarefas, como o [Azure Storage Explorer.](https://storageexplorer.com/)

    ![Crie file1.txt e carregue-o para o recipiente de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Para voltar à vista **de pipeline,** selecione **Todas as condutas**de gasodutos , e aguarde que o mesmo gasoduto seja automaticamente acionado novamente.  

    ![Selecione Todas as corridas de gasodutos](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Quando o segundo gasoduto estiver concluído, siga os mesmos passos mencionados anteriormente para rever os detalhes da execução da atividade.  

    Verá que um ficheiro (file1.txt) foi copiado do recipiente de origem para o recipiente de destino da sua conta de armazenamento Blob:

    ![file1.txt foi copiado do recipiente de origem para o recipiente de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Crie outro ficheiro de texto vazio e nomeie-o **file2.txt**. Faça upload deste ficheiro de texto para o recipiente de origem na sua conta de armazenamento Blob.

16. Repita os passos 13 e 14 para o segundo ficheiro de texto. Verá que apenas o novo ficheiro (file2.txt) foi copiado do recipiente de origem para o recipiente de destino da sua conta de armazenamento durante esta execução do gasoduto.  

    Também pode verificar se apenas um ficheiro foi copiado utilizando o [Azure Storage Explorer](https://storageexplorer.com/) para digitalizar os ficheiros:

    ![Scaneie ficheiros utilizando o Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Passos seguintes
Vá ao seguinte tutorial para aprender a transformar dados utilizando um cluster Apache Spark em Azure:

> [!div class="nextstepaction"]
>[Transforme os dados na nuvem usando um cluster Apache Spark](tutorial-transform-data-spark-portal.md)
