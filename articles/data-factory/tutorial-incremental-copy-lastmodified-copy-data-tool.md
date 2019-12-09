---
title: Ferramenta de dados para copiar arquivos novos e atualizados incrementalmente
description: Crie um data factory do Azure e, em seguida, use a ferramenta Copiar Dados para carregar incrementalmente novos arquivos com base em LastModifiedDate.
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
ms.date: 1/24/2019
ms.openlocfilehash: 5ff3ade800b2a3474a68a34dc77d0c9b009e8822
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923146"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Copiar arquivos novos e alterados incrementalmente com base em LastModifiedDate usando a ferramenta de Copiar Dados

Neste tutorial, você usará o portal do Azure para criar um data factory. Em seguida, você usará a ferramenta Copiar Dados para criar um pipeline que copia incrementalmente somente arquivos novos e alterados, com base em seus **LastModifiedDate** do armazenamento de BLOBs do Azure para o armazenamento de BLOBs do Azure.

Ao fazer isso, o ADF examinará todos os arquivos do repositório de origem, aplicará o filtro de arquivo por seus LastModifiedDate e copiará o arquivo novo e atualizado somente desde a última vez para o repositório de destino.  Observe que, se você deixar o ADF digitalizar grandes quantidades de arquivos, mas copiar apenas alguns arquivos para o destino, você ainda esperará que a longa duração devido à verificação de arquivos também seja demorada.   

> [!NOTE]
> Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, você executará as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Utilizar a ferramenta Copiar Dados para criar um pipeline.
> * Monitorizar o pipeline e execuções de atividades.

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento do Azure**: Use o armazenamento de BLOBs como o armazenamento de dados de _origem_ e de _coletor_ . Se não tem uma conta de armazenamento do Azure, veja as instruções apresentadas em [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Criar dois contêineres no armazenamento de BLOBs

Prepare o armazenamento de BLOBs para o tutorial executando estas etapas.

1. Crie um contêiner chamado **origem**. Você pode usar várias ferramentas para executar essa tarefa, como [Gerenciador de armazenamento do Azure](https://storageexplorer.com/).

2. Crie um contêiner chamado **destino**. 

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu à esquerda, selecione **criar um recurso** > **dados + análise** > **Data Factory**: 
   
   ![Seleção do Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**. 
 
   O nome da fábrica de dados tem de ser _globalmente exclusivo_. Poderá receber a seguinte mensagem de erro:
   
   ![Mensagem de erro de nova fábrica de dados](./media/doc-common-process/name-not-available-error.png)

   Se receber uma mensagem de erro relacionada com o valor do nome, introduza um nome diferente para a fábrica de dados. Por exemplo, utilize o nome _**oseunome**_ **ADFTutorialDataFactory**. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
3. Selecione a **assinatura** do Azure na qual você criará o novo data Factory. 
4. Em **Grupo de Recursos**, efetue um destes passos:
     
    * Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.

    * Selecione **Criar novo** e introduza o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).

5. Em **versão**, selecione **v2**.
6. Em **Localização**, selecione a localização da fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. Os armazenamentos de dados (por exemplo, o armazenamento do Azure e o SQL Database) e as computações (por exemplo, Azure HDInsight) que seu data factory usa podem estar em outros locais e regiões.
7. Selecione **Afixar ao dashboard**. 
8. Selecione **Criar**.
9. No painel, consulte o bloco **Implantando data Factory** para ver o status do processo.

    ![Implantando Data Factory bloco](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Depois de concluída a criação, é apresentada a home page **Fábrica de Dados**.
   
    ![Home page da fábrica de dados](./media/doc-common-process/data-factory-home-page.png)
11. Para abrir a interface do usuário do Azure Data Factory (IU) em uma guia separada, selecione o bloco **criar & monitor** . 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Utilizar a ferramenta Copiar Dados para criar um pipeline

1. Na página **introdução** , selecione o título de **copiar dados** para abrir a ferramenta de copiar dados. 

   ![Mosaico ferramenta Copiar Dados](./media/doc-common-process/get-started-page.png)
   
2. Na página **Propriedades** , execute as seguintes etapas:

    a. Em **nome da tarefa**, insira **DeltaCopyFromBlobPipeline**.

    b. Em **cadência da tarefa** ou no **agendamento da tarefa**, selecione **executar regularmente na agenda**.

    c. Em **tipo de gatilho**, selecione **janela em cascata**.
    
    d. Em **recorrência**, insira **15 minuto (s)** . 
    
    e. Selecione **Seguinte**. 
    
    A IU do Data Factory cria um pipeline com o nome de tarefa especificado. 

    ![Página Propriedades](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. Na página **Arquivo de dados de origem**, conclua os seguintes passos:

    a. Selecione **+ criar nova conexão**para adicionar uma conexão.
    
    ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Selecione **armazenamento de BLOBs do Azure** na galeria e selecione **continuar**.
    
    ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Na página **novo serviço vinculado** , selecione sua conta de armazenamento na lista **nome da conta de armazenamento** e, em seguida, selecione **concluir**.
    
    ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Selecione o serviço vinculado recém-criado e, em seguida, selecione **Avançar**. 
    
   ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na página **Escolher o ficheiro ou pasta de entrada**, complete os seguintes passos:
    
    a. Procure e selecione a pasta de **origem** e selecione **escolher**.
    
    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. Em **comportamento de carregamento de arquivo**, selecione **carga incremental: LastModifiedDate**.
    
    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Marque **cópia binária** e selecione **Avançar**.
    
     ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. Na página **armazenamento de dados de destino** , selecione **AzureBlobStorage**. Essa é a mesma conta de armazenamento que o armazenamento de dados de origem. Em seguida, selecione **Seguinte**.

    ![Página arquivo de dados de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. Na página **Escolher o ficheiro ou pasta de saída**, complete os seguintes passos:
    
    a. Procure e selecione a pasta de **destino** e selecione **escolher**.
    
    ![Escolher ficheiro ou pasta de saída](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Selecione **Seguinte**.
    
     ![Escolher ficheiro ou pasta de saída](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. Na página **Definições**, selecione **Seguinte**. 

    ![Página de definições](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. Na página **Resumo** , examine as configurações e, em seguida, selecione **Avançar**.

    ![Página de resumo](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. Na **Página de implementação**, selecione **Monitorizar** para monitorizar o pipeline (tarefa).

    ![Página de implementação](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. A coluna **Ações** inclui ligações para ver os detalhes de execução da atividade e voltar a executar o pipeline. Selecione **Atualizar** para atualizar a lista e selecione o link **Exibir execuções de atividade** na coluna **ações** . 

    ![Atualizar lista e selecionar Exibir execuções de atividade](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Há apenas uma atividade (a atividade de cópia) no pipeline, para que você veja apenas uma entrada. Para ver os detalhes da operação de cópia, selecione a ligação **Detalhes** (ícone de óculos), na coluna **Ações**. 

    ![A atividade de cópia está no pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Como não há nenhum arquivo no contêiner de **origem** em sua conta de armazenamento de BLOBs, você não verá nenhum arquivo copiado para o contêiner de **destino** em sua conta de armazenamento de BLOBs.
    
    ![Nenhum arquivo no contêiner de origem ou de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Crie um arquivo de texto vazio e nomeie-o como **arquivo1. txt**. Carregue esse arquivo de texto no contêiner de **origem** em sua conta de armazenamento. Pode utilizar várias ferramentas para executar estas tarefas, como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/).   

    ![Criar file1. txt e carregar no contêiner de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Para voltar à exibição de **execuções de pipeline** , selecione **todas as execuções de pipeline**e aguarde até que o mesmo pipeline seja disparado novamente automaticamente.  

    ![Selecionar todas as execuções de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Selecione **Exibir execução da atividade** para a segunda execução de pipeline quando você a vir. Em seguida, examine os detalhes da mesma maneira que você fez para a primeira execução do pipeline.  

    ![Selecione Exibir execução da atividade e revisar detalhes](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Você verá que um arquivo (file1. txt) foi copiado do contêiner de **origem** para o contêiner de **destino** da sua conta de armazenamento de BLOBs.
    
    ![File1. txt foi copiado do contêiner de origem para o contêiner de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Crie outro arquivo de texto vazio e nomeie-o como **file2. txt**. Carregue esse arquivo de texto no contêiner de **origem** em sua conta de armazenamento de BLOBs.   
    
16. Repita as etapas 13 e 14 para esse segundo arquivo de texto. Você verá que apenas o novo arquivo (file2. txt) foi copiado do contêiner de **origem** para o contêiner de **destino** da sua conta de armazenamento na próxima execução do pipeline.  
    
    ![Arquivo2. txt foi copiado do contêiner de origem para o contêiner de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    Você também pode verificar isso usando [Gerenciador de armazenamento do Azure](https://storageexplorer.com/) para verificar os arquivos.
    
    ![Verificar arquivos usando Gerenciador de Armazenamento do Azure](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Passos seguintes
Avance para o tutorial a seguir para saber mais sobre como transformar dados usando um cluster Apache Spark no Azure:

> [!div class="nextstepaction"]
>[Transformar dados na nuvem usando um cluster Apache Spark](tutorial-transform-data-spark-portal.md)