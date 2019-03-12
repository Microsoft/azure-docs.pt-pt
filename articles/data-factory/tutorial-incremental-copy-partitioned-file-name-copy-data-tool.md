---
title: Apenas com o Azure Data Factory para copiar novos ficheiros de forma incremental com base no nome de arquivo particionada de tempo | Documentos da Microsoft
description: Crie uma fábrica de dados do Azure e, em seguida, utilize a ferramenta copiar dados para carregar novos ficheiros apenas com base no nome de ficheiro particionada de tempo de forma incremental.
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
ms.openlocfilehash: df1542d6d20120a9b1e087fadf3743479ecebf07
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533838"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Copiar novos ficheiros com base no nome de ficheiro particionada de tempo com a ferramenta copiar dados de forma incremental

Neste tutorial, irá utilizar o portal do Azure para criar uma fábrica de dados. Utilize a ferramenta copiar dados para criar um pipeline que copia incrementalmente novos ficheiros com base no nome de ficheiro particionada de tempo do armazenamento de Blobs do Azure para o armazenamento de Blobs do Azure. 

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

1. Criar um contentor com o nome **origem**.  Criar um caminho de pasta como **2019/02/26/14** no seu contentor. Criar um arquivo de texto vazio e nomeie-o como **file1.txt**. Carregar o file1.txt para o caminho da pasta **origem/2019/02/26/14** na sua conta de armazenamento.  Pode utilizar várias ferramentas para executar estas tarefas, como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/).
    
    ![Carregar ficheiros](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)
    
    > [!NOTE]
    > Ajuste o nome da pasta com o seu fuso horário UTC.  Por exemplo, se a hora UTC atual é 2 às 18:03 26 de Fevereiro de 2019, pode criar o caminho da pasta como **origem/2019/02/26/14/** pela regra de **origem / {ano} / {Month} / {Day} / {Hour} /**.

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
    
    d. Sob **periodicidade**, introduza **1 hora (s)**. 
    
    e. Selecione **Seguinte**. 
    
    A IU do Data Factory cria um pipeline com o nome de tarefa especificado. 

    ![Página Propriedades](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Na página **Arquivo de dados de origem**, conclua os seguintes passos:

    a. Clique em **+ criar nova ligação**, para adicionar uma ligação.

    ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)
    
    b. Selecione **armazenamento de Blobs do Azure** da Galeria e clique em **continuar**.

    ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)
    
    c. Sobre o **novo serviço ligado** , selecione a sua conta de armazenamento da **nome da conta de armazenamento** lista e, em seguida, clique em **concluir**.
    
    ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Selecione o serviço ligado criado recentemente, em seguida, clique em **seguinte**. 
    
   ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. Na página **Escolher o ficheiro ou pasta de entrada**, execute os seguintes passos:
    
    a. Procure e selecione o **origem** contentor, em seguida, selecione **escolha**.
    
    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)
    
    b. Sob **comportamento de carregamento do ficheiro**, selecione **carga Incremental: nomes de pasta/ficheiro particionada tempo**.
    
    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)
    
    c. Escreva o caminho da pasta dinâmico como **origem / {ano} / {month} / {day} / {hour} /** e alterar o formato como o seguinte:
    
    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)
    
    d. Verifique **cópia binária** e clique em **próxima**.
    
    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. Sobre o **o arquivo de dados de destino** página, selecione a **AzureBlobStorage**, que é o mesmo armazenamento de conta como arquivo de origem de dados e, em seguida, clique em **seguinte**.

    ![Página arquivo de dados de destino](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png) 
6. Sobre o **escolher o ficheiro de saída ou a pasta** página, efetue os seguintes passos:
    
    a. Procure e selecione o **destino** pasta, em seguida, clique em **escolha**.
    
    ![Escolher ficheiro ou pasta de saída](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)   
    
    b. Escreva o caminho da pasta dinâmico como **origem / {ano} / {month} / {day} / {hour} /** e alterar o formato como o seguinte:
    
    ![Escolher ficheiro ou pasta de saída](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)    
    
    c. Clique em **Seguinte**.
    
    ![Escolher ficheiro ou pasta de saída](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)  
7. Na página **Definições**, selecione **Seguinte**. 

    ![Página de definições](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)  
8. Na página **Resumo**, reveja as definições e depois selecione **Seguinte**.

    ![Página de resumo](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)
    
9. Na **Página de implementação**, selecione **Monitorizar** para monitorizar o pipeline (tarefa).
    ![Página de implementação](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)
    
10. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente.  Terá de aguardar que o pipeline ser executado quando é acionada automaticamente (sobre depois de uma hora).  Quando é executada, o **ações** coluna inclui ligações para ver os detalhes da execução da atividade e voltar a executar o pipeline. Selecione **Atualize** para atualizar a lista e selecione o **ver execuções de atividades** ligação no **ações** coluna. 

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Há apenas uma atividade (atividade copiar) no pipeline, pelo que só vai ver uma entrada. Pode ver o ficheiro de origem (file1.txt) foram copiado do **origem/2019/02/26/14/** ao **destino/2019/02/26/14/** com o mesmo nome de ficheiro.  

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)
    
    Também pode verificar o mesmo ao utilizar o Explorador de armazenamento do Azure (https://storageexplorer.com/) para analisar os ficheiros.
    
    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Criar outro ficheiro de texto vazio com o nome do novo **file2.txt**. Carregar o ficheiro de file2.txt para o caminho da pasta **origem/2019/02/26/15** na sua conta de armazenamento.   Pode utilizar várias ferramentas para executar estas tarefas, como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/).   
    
    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)
    
    > [!NOTE]
    > Pode estar ciente de que um novo caminho de pasta é necessário a ser criada. Ajuste o nome da pasta com o seu fuso horário UTC.  Por exemplo, se a hora UTC atual é de 3:20 PM 26 de Fevereiro de 2019, pode criar o caminho da pasta como **origem/2019/02/26/15/** pela regra de **{ano} / {Month} / {Day} / {Hour} /**.
    
13. Voltar para o **execuções de Pipeline** visualizar, selecione **todas as execuções de Pipelines**e aguarde que o pipeline mesmo sendo acionado novamente automaticamente após a outra, uma hora.  

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Selecione **vista de execução da atividade** da segunda execução do pipeline quando ele é fornecido e fazer o mesmo para rever os detalhes.  

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)
    
    Pode ver o ficheiro de origem (file2.txt) foram copiado do **origem/2019/02/26/15/** ao **destino/2019/02/26/15/** com o mesmo nome de ficheiro.
    
    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png) 
    
    Também pode verificar o mesmo ao utilizar o Explorador de armazenamento do Azure (https://storageexplorer.com/) para analisar os ficheiros na **destino** contentor
    
    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Passos Seguintes
Avance para o tutorial seguinte para saber como transformar dados através de um cluster do Spark no Azure:

> [!div class="nextstepaction"]
>[Transformar dados com um cluster do Spark na cloud](tutorial-transform-data-spark-portal.md)