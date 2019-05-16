---
title: Copiar ficheiros novos e alterados com base no LastModifiedDate com a ferramenta copiar dados de forma incremental | Documentos da Microsoft
description: Crie uma fábrica de dados do Azure e, em seguida, utilize a ferramenta copiar dados para carregar novos ficheiros com base em LastModifiedDate de forma incremental.
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
ms.openlocfilehash: 3865bb10346c4a55adbf94a7df225eacf2c11252
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65519137"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Copiar ficheiros novos e alterados com base no LastModifiedDate com a ferramenta copiar dados de forma incremental

Neste tutorial, irá utilizar o portal do Azure para criar uma fábrica de dados. Em seguida, usará a ferramenta copiar dados para criar um pipeline de forma incremental copia arquivos novos e alterados apenas, com base na respetiva **LastModifiedDate** do armazenamento de Blobs do Azure para o armazenamento de Blobs do Azure.

Ao fazê-lo, ADF irá analisar todos os ficheiros de arquivo de origem, aplique o filtro de ficheiros pelo respetivo LastModifiedDate e copie o ficheiro de novo e atualizado apenas desde a última vez para o arquivo de destino.  Tenha em atenção que se permitir que enormes quantidades da ADF análise de ficheiros, mas apenas copiar alguns arquivos para o destino, ainda esperaria que a duração de tempo devido a análise do ficheiro é demorada também.   

> [!NOTE]
> Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, irá executar as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Utilizar a ferramenta Copiar Dados para criar um pipeline.
> * Monitorizar o pipeline e execuções de atividades.

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento do Azure**: Utilizar o armazenamento de BLOBs como o _origem_ e _sink_ arquivo de dados. Se não tem uma conta de armazenamento do Azure, veja as instruções apresentadas em [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Criar dois contentores no armazenamento de BLOBs

Prepare seu armazenamento de BLOBs para o tutorial ao efetuar estes passos.

1. Criar um contentor com o nome **origem**. Pode utilizar várias ferramentas para realizar esta tarefa, tal como [Explorador de armazenamento do Azure](https://storageexplorer.com/).

2. Criar um contentor com o nome **destino**. 

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu da esquerda, selecione **criar um recurso** > **dados + análise** > **Data Factory**: 
   
   ![Seleção do Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**. 
      
     ![Nova fábrica de dados](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   O nome da fábrica de dados tem de ser _globalmente exclusivo_. Poderá receber a seguinte mensagem de erro:
   
   ![Mensagem de erro de nova fábrica de dados](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Se receber uma mensagem de erro relacionada com o valor do nome, introduza um nome diferente para a fábrica de dados. Por exemplo, utilize o nome _**oseunome**_**ADFTutorialDataFactory**. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
3. Selecione o Azure **subscrição** no qual irá criar a nova fábrica de dados. 
4. Em **Grupo de Recursos**, efetue um destes passos:
     
    * Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.

    * Selecione **Criar novo** e introduza o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).

5. Sob **versão**, selecione **V2**.
6. Em **Localização**, selecione a localização da fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. Os arquivos de dados (por exemplo, armazenamento do Azure e base de dados SQL) e as computações (por exemplo, o Azure HDInsight) que a fábrica de dados utiliza podem estar noutras localizações e regiões.
7. Selecione **Afixar ao dashboard**. 
8. Selecione **Criar**.
9. No dashboard, consulte a **implementar o Data Factory** mosaico para ver o estado do processo.

    ![Implantando o mosaico da fábrica de dados](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Depois de concluída a criação, é apresentada a home page **Fábrica de Dados**.
   
    ![Home page da fábrica de dados](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Para abrir a interface de utilizador (IU) do Azure Data Factory num separador à parte, selecione o **criar e monitorizar** mosaico. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Utilizar a ferramenta Copiar Dados para criar um pipeline

1. Sobre o **Vamos começar** página, selecione a **copiar dados** title para abrir a ferramenta copiar dados. 

   ![Mosaico ferramenta Copiar Dados](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. Sobre o **propriedades** página, siga os passos seguintes:

    a. Sob **nome da tarefa**, introduza **DeltaCopyFromBlobPipeline**.

    b. Sob **cadência de tarefas** ou **agenda de tarefa**, selecione **regularmente executada numa agenda**.

    c. Sob **tipo de Acionador**, selecione **janela em cascata**.
    
    d. Sob **periodicidade**, introduza **minuto (s 15)**. 
    
    e. Selecione **Seguinte**. 
    
    A IU do Data Factory cria um pipeline com o nome de tarefa especificado. 

    ![Página Propriedades](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. Na página **Arquivo de dados de origem**, conclua os seguintes passos:

    a. Selecione **+ criar nova ligação**, para adicionar uma ligação.
    
    ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Selecione **armazenamento de Blobs do Azure** da Galeria e, em seguida, selecione **continuar**.
    
    ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Sobre o **novo serviço ligado** , selecione a sua conta de armazenamento da **nome da conta de armazenamento** lista e, em seguida, selecione **concluir**.
    
    ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Selecione o serviço ligado criado recentemente e, em seguida, selecione **seguinte**. 
    
   ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na página **Escolher o ficheiro ou pasta de entrada**, complete os seguintes passos:
    
    a. Procure e selecione o **origem** e, em seguida, selecione **escolha**.
    
    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. Sob **comportamento de carregamento do ficheiro**, selecione **carga Incremental: LastModifiedDate**.
    
    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Verifique **cópia binária** e selecione **próxima**.
    
     ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. Sobre o **o arquivo de dados de destino** página, selecione **AzureBlobStorage**. Esta é a mesma conta de armazenamento como o arquivo de dados de origem. Em seguida, selecione **Seguinte**.

    ![Página arquivo de dados de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. Na página **Escolher o ficheiro ou pasta de saída**, complete os seguintes passos:
    
    a. Procure e selecione o **destino** e, em seguida, selecione **escolha**.
    
    ![Escolher ficheiro ou pasta de saída](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Selecione **Seguinte**.
    
     ![Escolher ficheiro ou pasta de saída](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. Na página **Definições**, selecione **Seguinte**. 

    ![Página de definições](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. Sobre o **resumo** página, reveja as definições e, em seguida, selecione **próxima**.

    ![Página de resumo](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. Na **Página de implementação**, selecione **Monitorizar** para monitorizar o pipeline (tarefa).

    ![Página de implementação](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente. A coluna **Ações** inclui ligações para ver os detalhes de execução da atividade e voltar a executar o pipeline. Selecione **Atualize** para atualizar a lista e selecione o **ver execuções de atividades** ligação no **ações** coluna. 

    ![Atualizar lista e selecione ver execuções de atividades](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Há apenas uma atividade (atividade de cópia) no pipeline, pelo que vai ver apenas uma entrada. Para ver os detalhes da operação de cópia, selecione a ligação **Detalhes** (ícone de óculos) na coluna **Ações**. 

    ![Atividade de cópia está no pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Porque não existe nenhum ficheiro no **origem** contentor na sua conta de armazenamento de BLOBs, não verá qualquer ficheiro copiado para o **destino** contentor na sua conta de armazenamento de Blobs.
    
    ![Não existem ficheiros no contentor de origem ou o contentor de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Crie um ficheiro de texto vazio e nomeie- **file1.txt**. Carregar este ficheiro de texto para o **origem** contentor na sua conta de armazenamento. Pode utilizar várias ferramentas para executar estas tarefas, como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/).   

    ![Criar file1.txt e carregar para o contentor de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Voltar para o **execuções de Pipeline** visualizar, selecione **todas as execuções de Pipeline**e aguarde até mesmo pipeline sejam acionadas automaticamente novamente.  

    ![Selecione todas as execuções de Pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Selecione **vista de execução da atividade** para o segundo pipeline executado quando vê-lo. Em seguida, reveja os detalhes da mesma forma que fez para a primeira execução de pipeline.  

    ![Selecione a vista de execução da atividade e rever os detalhes](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Irá consulte o artigo um ficheiro (file1.txt) foram copiado dos **origem** contentor para o **destino** contentor da conta de armazenamento de Blobs.
    
    ![File1.txt foram copiados do contentor de origem para o contentor de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Crie outro ficheiro de texto vazio e nomeie- **file2.txt**. Carregar este ficheiro de texto para o **origem** contentor na sua conta de armazenamento de Blobs.   
    
16. Repita os passos 13 e 14 para este ficheiro de texto de segundo. Verá que apenas o ficheiro novo (file2.txt) foram copiado dos **origem** contentor para o **destino** contentor da conta de armazenamento na próxima execução de pipeline.  
    
    ![File2.txt foram copiados do contentor de origem para o contentor de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    Também pode verificar isto utilizando [Explorador de armazenamento do Azure](https://storageexplorer.com/) para analisar os ficheiros.
    
    ![Analisar ficheiros com o Explorador de armazenamento do Azure](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Passos Seguintes
Avance para o tutorial seguinte para saber como transformar dados através de um cluster do Apache Spark no Azure:

> [!div class="nextstepaction"]
>[Transformar dados na cloud através de um cluster do Apache Spark](tutorial-transform-data-spark-portal.md)