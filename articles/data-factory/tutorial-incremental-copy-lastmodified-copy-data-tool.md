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
ms.openlocfilehash: 743f9dd8f7998178a75d716f4da22efee2b3bc79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131177"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Copie gradualmente ficheiros novos e alterados com base no LastModificadoDate utilizando a ferramenta Copy Data

Neste tutorial, você vai usar o portal Azure para criar uma fábrica de dados. Em seguida, utilizará a ferramenta Copy Data para criar um pipeline que copia gradualmente ficheiros novos e alterados apenas, com base no seu **LastModifieDDate** do armazenamento Azure Blob para o armazenamento Azure Blob.

Ao fazê-lo, a ADF digitalizará todos os ficheiros da loja de origem, aplicará o filtro de ficheiros pelo seu LastModifiedDate e copiará o ficheiro novo e atualizado apenas desde a última vez na loja de destino.  Por favor, note que se deixar a ADF digitalizar enormes quantidades de ficheiros mas apenas copiar alguns ficheiros para o destino, ainda esperaria que a longa duração devido à verificação de ficheiros também seja morosa.   

> [!NOTE]
> Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, realizará as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Utilizar a ferramenta Copiar Dados para criar um pipeline.
> * Monitorizar o pipeline e execuções de atividades.

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento Azure**: Utilize o armazenamento blob como _fonte_ e _sumidouro_ da loja de dados. Se não tem uma conta de armazenamento do Azure, veja as instruções apresentadas em [Criar uma conta de armazenamento](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Criar dois recipientes no armazenamento blob

Prepare o seu armazenamento Blob para o tutorial executando estes passos.

1. Criar um recipiente chamado **fonte.** Pode utilizar várias ferramentas para executar esta tarefa, como o [Azure Storage Explorer.](https://storageexplorer.com/)

2. Criar um recipiente chamado **destino.**

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu esquerdo, selecione **Criar um recurso** > **Data + Analytics** > **Data Factory**:

   ![Seleção do Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**.

   O nome da fábrica de dados tem de ser _globalmente exclusivo_. Poderá receber a seguinte mensagem de erro:

   ![Mensagem de erro de nova fábrica de dados](./media/doc-common-process/name-not-available-error.png)

   Se receber uma mensagem de erro relacionada com o valor do nome, introduza um nome diferente para a fábrica de dados. Por exemplo, utilize o nome _**oseunome**_**ADFTutorialDataFactory**. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
3. Selecione a **subscrição** Azure na qual irá criar a nova fábrica de dados.
4. Em **Grupo de Recursos**, efetue um destes passos:

    * Selecione Utilize o **uso existente** e selecione um grupo de recursos existente a partir da lista de drop-down.

    * Selecione **Criar novo** e insira o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).

5. Na **versão,** selecione **V2**.
6. Sob **localização,** selecione a localização para a fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. As lojas de dados (por exemplo, Armazenamento Azure e Base de Dados SQL) e cálculos (por exemplo, Azure HDInsight) que a sua fábrica de dados utiliza podem estar em outros locais e regiões.
8. Selecione **Criar**.
9. Depois de concluída a criação, é apresentada a home page **Fábrica de Dados**.
10. Para abrir a interface de utilizador da Azure Data Factory (UI) num separador, selecione o azulejo **do Autor & Monitor.**

    ![Home page da fábrica de dados](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Utilizar a ferramenta Copiar Dados para criar um pipeline

1. Na página **Let's start,** selecione o título **Copy Data** para abrir a ferramenta Copy Data.

   ![Mosaico ferramenta Copiar Dados](./media/doc-common-process/get-started-page.png)

2. Na página **Propriedades,** tome os seguintes passos:

    a. Sob **o nome de tarefa,** introduza **DeltaCopyFromBlobPipeline**.

    b. Sob **a cadência** de tarefaou no calendário de **tarefas,** selecione **Executar regularmente no horário**.

    c. No **tipo de gatilho,** selecione **Janela de tropeçar**.

    d. Em **Recurrence,** **insira 15 minutos( s)**.

    e. Selecione **Next**.

    A IU do Data Factory cria um pipeline com o nome de tarefa especificado.

    ![Página Propriedades](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Na página **Arquivo de dados de origem**, conclua os seguintes passos:

    a. Selecione **+ Criar uma nova ligação,** para adicionar uma ligação.

    b. Selecione **Armazenamento Azure Blob** na galeria e, em seguida, selecione **Continuar**.

    ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Na página **New Linked Service (Azure Blob Storage),** selecione a sua conta de armazenamento na lista de nomes da **conta de armazenamento.** Teste a ligação e, em seguida, **selecione Criar**.

    d. Selecione o serviço ligado recém-criado e, em seguida, selecione **Next**.

   ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na página **Escolher o ficheiro ou pasta de entrada**, complete os seguintes passos:

    a. Navegue e selecione a pasta **fonte** e, em seguida, selecione **Escolher**.

    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. No **comportamento de carregamento de ficheiros,** selecione carga **incremental: LastModifiedDate**.

    c. Verifique **a cópia Binária** e selecione **Next**.

     ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Na página da loja de **dados Destination,** selecione o **AzureBlobStorage** que criou. Esta é a mesma conta de armazenamento que a loja de dados de origem. Em seguida, selecione **Seguinte**.

6. Na página **Escolher o ficheiro ou pasta de saída**, complete os seguintes passos:

    a. Navegue e selecione a pasta de **destino** e, em seguida, selecione **Escolher**.

    ![Escolher ficheiro ou pasta de saída](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Selecione **Next**.

7. Na página **Definições**, selecione **Seguinte**.

8. Na página **Resumo,** reveja as definições e, em seguida, selecione **Next**.

    ![Página de resumo](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Na **Página de implementação**, selecione **Monitorizar** para monitorizar o pipeline (tarefa).

    ![Página de implementação](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. A aplicação muda para o separador **Monitor.** Vê o estado do oleoduto. Selecione **Atualizar** para atualizar a lista. Clique no link em **PIPELINE NAME** para ver os detalhes de execução da atividade ou reexecutar o gasoduto. 

    ![Atualizar lista e selecionar Ver Gestão de Atividades](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Há apenas uma atividade (a atividade de cópia) no oleoduto, por isso só se vê uma entrada. Para mais detalhes sobre o funcionamento da cópia, selecione o link **Detalhes** (ícone de óculos) sob a coluna **'NOME** DE ATIVIDADE'. Para obter os detalhes das propriedades, veja [Copy Activity overview](copy-activity-overview.md) (Descrição geral da Atividade Copy). 

    ![A atividade de cópia está em pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Como não há ficheiro no recipiente **de origem** na sua conta de armazenamento Blob, não verá nenhum ficheiro copiado para o contentor de **destino** na sua conta de armazenamento Blob.

    ![Nenhum ficheiro no recipiente de origem ou no recipiente de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Crie um ficheiro de texto vazio e nomeie-o **file1.txt**. Faça upload deste ficheiro de texto para o recipiente **de origem** na sua conta de armazenamento. Pode utilizar várias ferramentas para executar estas tarefas, como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/).

    ![Crie file1.txt e carregue para o recipiente de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Para voltar à vista **Pipeline Runs,** selecione **Todas as condutas**de gasodutos , e aguarde que o mesmo gasoduto seja acionado automaticamente.  

    ![Selecione todas as corridas de gasodutos](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Quando o segundo gasoduto estiver concluído, siga os mesmos passos acima mencionados para rever os detalhes da execução da atividade.  

    Verá que um ficheiro (file1.txt) foi copiado do recipiente de **origem** para o recipiente de **destino** da sua conta de armazenamento Blob.

    ![File1.txt foi copiado de recipiente de origem para recipiente de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Crie outro ficheiro de texto vazio e nomeie-o **file2.txt**. Faça upload deste ficheiro de texto para o recipiente **de origem** na sua conta de armazenamento Blob.

16. Repita os passos 13 e 14 para este segundo ficheiro de texto. Verá que apenas o novo ficheiro (file2.txt) foi copiado do recipiente de **origem** para o recipiente de **destino** da sua conta de armazenamento na próxima execução do gasoduto.  

    Também pode verificar isto utilizando o [Azure Storage Explorer](https://storageexplorer.com/) para digitalizar os ficheiros.

    ![Scan ficheiros usando o Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Passos seguintes
Avance para o seguinte tutorial para aprender sobre a transformação de dados utilizando um cluster Apache Spark em Azure:

> [!div class="nextstepaction"]
>[Transforme os dados na nuvem usando um cluster Apache Spark](tutorial-transform-data-spark-portal.md)
