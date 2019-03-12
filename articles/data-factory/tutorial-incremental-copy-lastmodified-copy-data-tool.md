---
title: Apenas com o Azure Data Factory para copiar ficheiros de novos e alterados de forma incremental com base no LastModifiedDate | Documentos da Microsoft
description: Crie uma fábrica de dados do Azure e, em seguida, utilize a ferramenta copiar dados para carregar apenas com base em LastModifiedDate de novos ficheiros de forma incremental.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/24/2019
ms.openlocfilehash: e5e184e6dadfa993e0b758ad32b54ea6ccfcb272
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57546539"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Copiar ficheiros novos e alterados com base no LastModifiedDate com a ferramenta copiar dados de forma incremental

Neste tutorial, irá utilizar o portal do Azure para criar uma fábrica de dados. Utilize a ferramenta copiar dados para criar um pipeline de forma incremental copia arquivos novos e alterados apenas com base na respetiva "LastModifiedDate" do armazenamento de Blobs do Azure para o armazenamento de Blobs do Azure. 

> [!NOTE]
> Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, vai executar os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Utilizar a ferramenta Copiar Dados para criar um pipeline.
> * Monitorizar o pipeline e execuções de atividades.

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento do Azure**: Utilizar o armazenamento de BLOBs como o _origem_ e _sink_ arquivo de dados. Se não tem uma conta de armazenamento do Azure, veja as instruções apresentadas em [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Criar dois contentores no armazenamento de BLOBs

Prepare seu armazenamento de BLOBs para o tutorial ao efetuar estes passos.

1. Criar um contentor com o nome **origem**. Pode utilizar várias ferramentas para executar estas tarefas, como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/).

2. Criar um contentor com o nome **destino**. Pode utilizar várias ferramentas para executar estas tarefas, como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu da esquerda, selecione **+ Novo** > **Dados + Análise** > **Fábrica de Dados**: 
   
   ![Criação de nova fábrica de dados](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**. 
      
     ![Nova fábrica de dados](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   O nome da fábrica de dados tem de ser _globalmente exclusivo_. Poderá receber a seguinte mensagem de erro:
   
   ![Mensagem de erro de nova fábrica de dados](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Se receber uma mensagem de erro relacionada com o valor do nome, introduza um nome diferente para a fábrica de dados. Por exemplo, utilize o nome _**oseunome**_**ADFTutorialDataFactory**. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
3. Selecione a **subscrição** do Azure na qual quer criar a nova fábrica de dados. 
4. Em **Grupo de Recursos**, efetue um destes passos:
     
    a. Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.

    b. Selecione **Criar novo** e introduza o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).

5. Em **Versão**, selecione **V2** para indicar a versão.
6. Em **Localização**, selecione a localização da fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. Os arquivos de dados (por exemplo, o Armazenamento do Azure e a Base de Dados SQL) e as computações (por exemplo, o Azure HDInsight) utilizados pela fábrica de dados podem estar noutras localizações e regiões.
7. Selecione **Afixar ao dashboard**. 
8. Selecione **Criar**.
9. No dashboard, o mosaico **A Implementar a Fábrica de Dados** mostra o estado do processo.

    ![Mosaico A Implementar o Data Factory](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Depois de concluída a criação, é apresentada a home page **Fábrica de Dados**.
   
    ![Home page da fábrica de dados](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Para iniciar a interface de utilizador (IU) do Azure Data Factory num separador à parte, selecione o mosaico **Criar e Monitorizar**. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Utilizar a ferramenta Copiar Dados para criar um pipeline

1. Sobre o **Vamos começar** página, selecione a **copiar dados** title para iniciar a ferramenta copiar dados. 

   ![Mosaico ferramenta Copiar Dados](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. Sobre o **propriedades** página, siga os passos seguintes:

    a. Sob **nome da tarefa**, introduza **DeltaCopyFromBlobPipeline**.

    b. Sob **cadência de tarefas ou agenda de tarefas**, selecione **regularmente executada numa agenda**.

    c. Sob **Acione tipo**, selecione **janela em cascata**.
    
    d. Sob **periodicidade**, introduza **minuto (s 15)**. 
    
    e. Selecione **Seguinte**. 
    
    A IU do Data Factory cria um pipeline com o nome de tarefa especificado. 

    ![Página Propriedades](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. Na página **Arquivo de dados de origem**, conclua os seguintes passos:

    a. Clique em **+ criar nova ligação**, para adicionar uma ligação.
    
    ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Selecione **armazenamento de Blobs do Azure** da Galeria e clique em **continuar**.
    
    ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Sobre o **novo serviço ligado** , selecione a sua conta de armazenamento da **nome da conta de armazenamento** lista e, em seguida, clique em **concluir**.
    
    ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Selecione o serviço ligado criado recentemente, em seguida, clique em **seguinte**. 
    
   ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na página **Escolher o ficheiro ou pasta de entrada**, execute os seguintes passos:
    
    a. Procure e selecione o **origem** pasta, em seguida, clique em **escolha**.
    
    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. Sob **comportamento de carregamento do ficheiro**, selecione **carga Incremental: LastModifiedDate**.
    
    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Verifique **cópia binária** e clique em **próxima**.
    
     ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. Sobre o **o arquivo de dados de destino** página, selecione a **AzureBlobStorage** que é o mesmo armazenamento de conta como arquivo de origem de dados e, em seguida, clique em **seguinte**.

    ![Página arquivo de dados de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. Sobre o **escolher o ficheiro de saída ou a pasta** página, efetue os seguintes passos:
    
    a. Procure e selecione o **destino** pasta, em seguida, clique em **escolha**.
    
    ![Escolher ficheiro ou pasta de saída](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Clique em **Seguinte**.
    
     ![Escolher ficheiro ou pasta de saída](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. Na página **Definições**, selecione **Seguinte**. 

    ![Página de definições](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. Na página **Resumo**, reveja as definições e depois selecione **Seguinte**.

    ![Página de resumo](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. Na **Página de implementação**, selecione **Monitorizar** para monitorizar o pipeline (tarefa).

    ![Página de implementação](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. A coluna **Ações** inclui ligações para ver os detalhes de execução da atividade e voltar a executar o pipeline. Selecione **Atualize** para atualizar a lista e selecione o **ver execuções de atividades** ligação no **ações** coluna. 

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Há apenas uma atividade (atividade copiar) no pipeline, pelo que só vai ver uma entrada. Para ver os detalhes da operação de cópia, selecione a ligação **Detalhes** (ícone de óculos) na coluna **Ações**. 

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Uma vez que não existe nenhum ficheiro na **origem** contentor na sua conta de armazenamento de BLOBs, portanto, não verá qualquer ficheiro que foi copiado para **destino** contentor na sua conta de armazenamento de Blobs.
    
    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Crie um ficheiro de texto vazio e nomeie-o como file1.txt. Carregue o ficheiro de file1.txt para o **origem** contentor na sua conta de armazenamento. Pode utilizar várias ferramentas para executar estas tarefas, como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/).   

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Voltar para o **execuções de Pipeline** visualizar, selecione **todas as execuções de Pipelines**e aguarde que o pipeline mesmo sendo acionado novamente automaticamente.  

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Selecione **vista de execução da atividade** para o segundo se execução de pipeline vir vem e fazer o mesmo para rever os detalhes.  

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Verá um arquivo (file1.txt) foram copiado dos **origem** contentor para o **destino** contentor da conta de armazenamento.
    
    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Criar outro ficheiro de texto vazio e nomeie-o como file2.txt. Carregue o ficheiro de file2.txt para o **origem** contentor na sua conta de armazenamento. Pode utilizar várias ferramentas para executar estas tarefas, como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/).  
    
16. Fazer o mesmo passo 13 e 14 e verá apenas o ficheiro novo (file2.txt) foram copiado dos **origem** contentor para o **destino** contentor da conta de armazenamento na próxima execução de pipeline.  
    
    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    Também pode verificar o mesmo ao utilizar o Explorador de armazenamento do Azure (https://storageexplorer.com/) para analisar os ficheiros.
    
    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Passos Seguintes
Avance para o tutorial seguinte para saber como transformar dados através de um cluster do Spark no Azure:

> [!div class="nextstepaction"]
>[Transformar dados com um cluster do Spark na cloud](tutorial-transform-data-spark-portal.md)