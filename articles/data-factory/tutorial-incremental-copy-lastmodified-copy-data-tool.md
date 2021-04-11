---
title: Ferramenta de dados para copiar ficheiros novos e atualizados incrementalmente
description: Crie uma fábrica de dados Azure e, em seguida, utilize a ferramenta Dados de Cópia para carregar gradualmente novos ficheiros com base no LastModifiedDate.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 25e39d3fc9c56a282714bafb630fee65421fb5ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606672"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Copie gradualmente ficheiros novos e alterados com base no LastModifiedDate utilizando a ferramenta Dados de Cópia

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Neste tutorial, você usará o portal Azure para criar uma fábrica de dados. Em seguida, utilizará a ferramenta Dados de Cópia para criar um pipeline que copie gradualmente ficheiros novos e alterados apenas, desde o armazenamento do Azure Blob até ao armazenamento do Azure Blob. Usa `LastModifiedDate` para determinar que ficheiros copiar.

Depois de completar os passos aqui, a Azure Data Factory irá analisar todos os ficheiros da loja de origem, aplicar o filtro de ficheiros por `LastModifiedDate` , e copiar para a loja de destino apenas ficheiros novos ou atualizados desde a última vez. Note que se a Data Factory digitalizar um grande número de ficheiros, ainda deverá esperar longas durações. A digitalização dos ficheiros é morosa, mesmo quando a quantidade de dados copiados é reduzida.

> [!NOTE]
> Se não estiver familiarizado com o Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, completará estas tarefas:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Utilizar a ferramenta Copiar Dados para criar um pipeline.
> * Monitorizar o pipeline e execuções de atividades.

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento Azure**: Utilize o armazenamento blob para as lojas de dados de origem e afundamento. Se não tiver uma conta de Armazenamento Azure, siga as instruções na [Criar uma conta de armazenamento](../storage/common/storage-account-create.md).

## <a name="create-two-containers-in-blob-storage"></a>Criar dois recipientes no armazenamento blob

Prepare o seu armazenamento Blob para o tutorial completando estes passos:

1. Criar um recipiente chamado **fonte**. Pode utilizar várias ferramentas para executar esta tarefa, como [o Azure Storage Explorer.](https://storageexplorer.com/)

2. Crie um contentor chamado **destino.**

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No painel esquerdo, selecione **Criar um recurso**. **Selecione Fábrica** de  >  **Dados de** Integração:

   ![Selecione Fábrica de Dados](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**.

   O nome da fábrica de dados tem de ser globalmente exclusivo. Pode receber esta mensagem de erro:

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nova mensagem de erro da fábrica de dados para nome duplicado.":::

   Se receber uma mensagem de erro relacionada com o valor do nome, introduza um nome diferente para a fábrica de dados. Por exemplo, utilize o nome _**oseunome**_**ADFTutorialDataFactory**. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
3. Em **Subscrição**, selecione a subscrição Azure na qual irá criar a nova fábrica de dados.
4. No **Grupo de Recursos,** tome um destes passos:

    * Selecione **Utilizar a existência** e, em seguida, selecione um grupo de recursos existente na lista.

    * Selecione **Criar novo** e, em seguida, insira um nome para o grupo de recursos.
         
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).

5. Em **Versão**, selecione **V2**.
6. Em **Localização**, selecione a localização para a fábrica de dados. Apenas locais suportados aparecem na lista. As lojas de dados (por exemplo, Azure Storage e Azure SQL Database) e calcula (por exemplo, Azure HDInsight) que a sua fábrica de dados utiliza pode estar em outros locais e regiões.
8. Selecione **Criar**.
9. Após a criação da fábrica de dados, aparece a página inicial da fábrica de dados.
10. Para abrir a interface de utilizador da Azure Data Factory (UI) num separador, selecione o **azulejo do Monitor & autor:**

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Página inicial da Fábrica de Dados Azure, com o azulejo do Monitor de autores &.":::

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Utilizar a ferramenta Copiar Dados para criar um pipeline

1. Na página **'Iniciar's,** selecione o azulejo **de Dados** de Cópia para abrir a ferramenta Dados de Cópia:

   ![Copiar azulejos de dados](./media/doc-common-process/get-started-page.png)

2. Na página **Propriedades,** tome os seguintes passos:

    a. Sob **o nome de tarefa**, insira **DeltaCopyFromBlobPipeline**.

    b. No horário **de cadência de tarefas ou agenda de tarefas**, selecione **Executar regularmente no horário**.

    c. Sob **o tipo Gatilho**, selecione A janela **Tumbling**.

    d. Em **Recorrência**, insira **15 Minutos(s)**.

    e. Selecione **Seguinte**.

    A Data Factory cria um oleoduto com o nome de tarefa especificado.

    ![Copiar página de propriedades de dados](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Na página da **loja de dados Source,** complete estes passos:

    a. **Selecione Criar uma nova ligação** para adicionar uma ligação.

    b. Selecione **Azure Blob Storage** da galeria e, em seguida, selecione **Continue**:

    ![Selecione armazenamento de blogs Azure](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Na página **New Linked Service (Azure Blob Storage),** selecione a sua conta de armazenamento na lista de nomes da **conta de armazenamento.** Teste a ligação e, em seguida, **selecione Criar**.

    d. Selecione o novo serviço ligado e, em seguida, selecione **Seguinte**:

   ![Selecione o novo serviço ligado](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na página **Escolher o ficheiro ou pasta de entrada**, complete os seguintes passos:

    a. Navegue e selecione a pasta **de origem** e, em seguida, selecione **Escolha**.

    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. Sob **o comportamento de carregamento de ficheiros**, selecione carga **incremental: LastModifiedDate**.

    c. Selecione **a cópia Binária** e, em seguida, selecione **Seguinte**:

     ![Escolha o ficheiro de entrada ou a página de pasta](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Na página da **loja de dados destino,** selecione o serviço **AzureBlobStorage** que criou. Esta é a mesma conta de armazenamento que a loja de dados de origem. Em seguida, selecione **Seguinte**.

6. Na página **Escolher o ficheiro ou pasta de saída**, complete os seguintes passos:

    a. Navegue e selecione a pasta de **destino** e, em seguida, selecione **Escolha**:

    ![Escolha o ficheiro de saída ou a página de pasta](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Selecione **Seguinte**.

7. Na página **Definições**, selecione **Seguinte**.

8. Na página **Resumo,** reveja as definições e, em seguida, selecione **Seguinte**.

    ![Página de resumo](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Na **Página de implementação**, selecione **Monitorizar** para monitorizar o pipeline (tarefa).

    ![Página de implementação](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. A aplicação muda para o **separador Monitor.** Vê-se o estado do oleoduto. Selecione **Atualizar** para atualizar a lista. Selecione o link em **PIPELINE NAME** para ver detalhes de execução da atividade ou para executar o pipeline novamente.

    ![Atualizar a lista e ver detalhes da atividade](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs-1.png)

11. Há apenas uma atividade (a atividade da cópia) no oleoduto, por isso só se vê uma entrada. Para obter mais informações sobre a operação da cópia, selecione o link **Detalhes** (o ícone óculos) na coluna **ACTIVITY NAME.** Para mais detalhes sobre os imóveis, consulte [a visão geral da atividade do Copy](copy-activity-overview.md).

    ![Copiar atividade no oleoduto](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Como não existem ficheiros no contentor de origem na sua conta de armazenamento Blob, não verá nenhum ficheiro copiado para o contentor de destino na conta:

    ![Não há ficheiros no contentor de origem ou no contentor de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Crie um ficheiro de texto vazio e nomeie-o **file1.txt**. Faça o upload deste ficheiro de texto para o recipiente de origem na sua conta de armazenamento. Pode utilizar várias ferramentas para executar estas tarefas, como [o Azure Storage Explorer.](https://storageexplorer.com/)

    ![Crie file1.txt e carreca-o para o recipiente de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Para voltar à vista **de execuções do Pipeline,** selecione **Todas as tubagem de gasodutos**, e aguarde que o mesmo gasoduto seja automaticamente acionado novamente.  

14. Quando o segundo gasoduto terminar, siga os mesmos passos mencionados anteriormente para rever os detalhes da execução da atividade.  

    Verá que um ficheiro (file1.txt) foi copiado do contentor de origem para o contentor de destino da sua conta de armazenamento Blob:

    ![file1.txt foi copiado do contentor de origem para o contentor de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Crie outro ficheiro de texto vazio e nomeie-o **file2.txt**. Faça o upload deste ficheiro de texto para o recipiente de origem na sua conta de armazenamento Blob.

16. Repita os passos 13 e 14 para o segundo ficheiro de texto. Verá que apenas o novo ficheiro (file2.txt) foi copiado do contentor de origem para o contentor de destino da sua conta de armazenamento durante esta corrida ao gasoduto.  

    Também pode verificar se apenas um ficheiro foi copiado utilizando [o Azure Storage Explorer](https://storageexplorer.com/) para digitalizar os ficheiros:

    ![Digitalizar ficheiros utilizando o Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Passos seguintes
Vá ao seguinte tutorial para aprender a transformar dados usando um cluster Apache Spark em Azure:

> [!div class="nextstepaction"]
>[Transforme os dados na nuvem usando um cluster Apache Spark](tutorial-transform-data-spark-portal.md)
