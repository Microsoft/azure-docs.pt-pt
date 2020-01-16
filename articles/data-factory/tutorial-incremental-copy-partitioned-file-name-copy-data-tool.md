---
title: Copiar os novos arquivos incrementalmente com base no nome do arquivo particionado
description: Crie um data factory do Azure e, em seguida, use a ferramenta Copiar Dados para carregar incrementalmente novos arquivos com base no nome do arquivo particionado.
services: data-factory
documentationcenter: ''
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
ms.openlocfilehash: 38e0028efd7fbda8aa5bee497836ce1161109e03
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982613"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Copiar incrementalmente novos arquivos com base no nome do arquivo particionado usando a ferramenta de Copiar Dados

Neste tutorial, irá utilizar o portal do Azure para criar uma fábrica de dados. Em seguida, você usa a ferramenta Copiar Dados para criar um pipeline que copia incrementalmente novos arquivos com base no nome de arquivo particionado do armazenamento de BLOBs do Azure para o armazenamento de BLOBs do Azure.

> [!NOTE]
> Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, vai executar os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Utilizar a ferramenta Copiar Dados para criar um pipeline.
> * Monitorizar o pipeline e execuções de atividades.

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento do Azure**: Use o armazenamento de BLOBs como o armazenamento de dados de _origem_ e de _coletor_ . Se não tem uma conta de armazenamento do Azure, veja as instruções apresentadas em [Criar uma conta de armazenamento](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Criar dois contêineres no armazenamento de BLOBs

Prepare o armazenamento de BLOBs para o tutorial executando estas etapas.

1. Crie um contêiner chamado **origem**.  Crie um caminho de pasta como **2019/02/26/14** em seu contêiner. Crie um arquivo de texto vazio e nomeie-o como **arquivo1. txt**. Carregue o arquivo1. txt no caminho da pasta **Source/2019/02/26/14** em sua conta de armazenamento.  Pode utilizar várias ferramentas para executar estas tarefas, como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/).

    ![carregar arquivos](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > Ajuste o nome da pasta com a hora UTC.  Por exemplo, se a hora UTC atual for 2:03 PM em fevereiro de 26, 2019, você poderá criar o caminho da pasta como **Source/2019/02/26/14/** pela regra de **origem/{ano}/{month}/{Day}/{hora}/** .

2. Crie um contêiner chamado **destino**. Pode utilizar várias ferramentas para executar estas tarefas, como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu à esquerda, selecione **criar um recurso** > **dados + análise** > **Data Factory**:

   ![Seleção do Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**.

    O nome da fábrica de dados tem de ser _globalmente exclusivo_. Poderá receber a seguinte mensagem de erro:

   ![Mensagem de erro de nova fábrica de dados](./media/doc-common-process/name-not-available-error.png)

   Se receber uma mensagem de erro relacionada com o valor do nome, introduza um nome diferente para a fábrica de dados. Por exemplo, utilize o nome _**oseunome**_ **ADFTutorialDataFactory**. Para ter acesso às regras de nomenclatura para artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
3. Selecione a **subscrição** do Azure na qual quer criar a nova fábrica de dados.
4. Em **Grupo de Recursos**, efetue um destes passos:

    a. Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.

    b. Selecione **Criar novo** e introduza o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).

5. Em **Versão**, selecione **V2** para indicar a versão.
6. Em **Localização**, selecione a localização da fábrica de dados. Apenas são apresentadas as localizações suportadas na lista pendente. Os arquivos de dados (por exemplo, o Armazenamento do Azure e a Base de Dados SQL) e as computações (por exemplo, o Azure HDInsight) utilizados pela fábrica de dados podem estar noutras localizações e regiões.
7. Selecione **Afixar ao dashboard**.
8. Selecione **Criar**.
9. No dashboard, o mosaico **A Implementar a Fábrica de Dados** mostra o estado do processo.

    ![Mosaico de implementação da fábrica da dados](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Depois de concluída a criação, é apresentada a home page **Fábrica de Dados**.

    ![Home page da fábrica de dados](./media/doc-common-process/data-factory-home-page.png)
11. Para iniciar a interface de utilizador (IU) do Azure Data Factory num separador à parte, selecione o mosaico **Criar e Monitorizar**.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Utilizar a ferramenta Copiar Dados para criar um pipeline

1. Na página **introdução** , selecione o título de **copiar dados** para iniciar a ferramenta de copiar dados.

   ![Mosaico ferramenta Copiar Dados](./media/doc-common-process/get-started-page.png)

2. Na página **Propriedades** , execute as seguintes etapas:

    a. Em **nome da tarefa**, insira **DeltaCopyFromBlobPipeline**.

    b. Em **cadência da tarefa ou no agendamento da tarefa**, selecione **executar regularmente na agenda**.

    c. Em **tipo de gatilho**, selecione **janela em cascata**.

    d. Em **recorrência**, insira **1 hora (s)** .

    e. Selecione **Seguinte**.

    A IU do Data Factory cria um pipeline com o nome de tarefa especificado.

    ![Página Propriedades](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Na página **Arquivo de dados de origem**, conclua os seguintes passos:

    a. Clique em **+ criar nova conexão**para adicionar uma conexão.

    ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)

    b. Selecione **armazenamento de BLOBs do Azure** na galeria e clique em **continuar**.

    ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)

    c. Na página **novo serviço vinculado** , selecione sua conta de armazenamento na lista **nome da conta de armazenamento** e clique em **concluir**.

    ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. Selecione o serviço vinculado recém-criado e clique em **Avançar**.

   ![Página de arquivo de dados de origem](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. Na página **Escolher o ficheiro ou pasta de entrada**, execute os seguintes passos:

    a. Procure e selecione o contêiner de **origem** e selecione **escolher**.

    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. Em **comportamento de carregamento de arquivo**, selecione **carga incremental: nomes de pasta/arquivo particionados por tempo**.

    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)

    c. Grave o caminho da pasta dinâmica como **fonte/{ano}/{mês}/{dia}/{hora}/** e altere o formato da seguinte maneira:

    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)

    d. Marque **cópia binária** e clique em **Avançar**.

    ![Escolher o ficheiro ou pasta de entrada](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. Na página **armazenamento de dados de destino** , selecione o **AzureBlobStorage**, que é a mesma conta de armazenamento que o armazenamento de fonte de dados e clique em **Avançar**.

    ![Página arquivo de dados de destino](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. Na página **escolher o arquivo de saída ou a pasta** , execute as seguintes etapas:

    a. Procure e selecione a pasta de **destino** e clique em **escolher**.

    ![Escolher ficheiro ou pasta de saída](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)

    b. Grave o caminho da pasta dinâmica como **fonte/{ano}/{mês}/{dia}/{hora}/** e altere o formato da seguinte maneira:

    ![Escolher ficheiro ou pasta de saída](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)

    c. Clique em **Seguinte**.

    ![Escolher ficheiro ou pasta de saída](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. Na página **Definições**, selecione **Seguinte**.

    ![Página de definições](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)
8. Na página **Resumo**, reveja as definições e depois selecione **Seguinte**.

    ![Página de resumo](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. Na **Página de implementação**, selecione **Monitorizar** para monitorizar o pipeline (tarefa).
    página de implantação do ![](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. Tenha em atenção que o separador **Monitorização** à esquerda é selecionado automaticamente.  Você precisa aguardar a execução do pipeline quando ele é disparado automaticamente (aproximadamente uma hora).  Quando ele é executado, a coluna **Actions** inclui links para exibir detalhes da execução da atividade e executar novamente o pipeline. Selecione **Atualizar** para atualizar a lista e selecione o link **Exibir execuções de atividade** na coluna **ações** .

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Há apenas uma atividade (atividade copiar) no pipeline, pelo que só vai ver uma entrada. Você pode ver o arquivo de origem (file1. txt) foi copiado de **Source/2019/02/26/14/** para **Destination/2019/02/26/14/** com o mesmo nome de arquivo.  

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    Você também pode verificar o mesmo usando Gerenciador de Armazenamento do Azure (https://storageexplorer.com/) para verificar os arquivos.

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Crie outro arquivo de texto vazio com o novo nome como **file2. txt**. Carregue o arquivo File2. txt no caminho da pasta **Source/2019/02/26/15** em sua conta de armazenamento.   Pode utilizar várias ferramentas para executar estas tarefas, como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/).

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)

    > [!NOTE]
    > Você pode estar ciente de que um novo caminho de pasta deve ser criado. Ajuste o nome da pasta com a hora UTC.  Por exemplo, se a hora UTC atual for 3:20 PM em Fev 26, 2019, você poderá criar o caminho da pasta como **Source/2019/02/26/15/** pela regra de **{year}/{month}/{Day}/{Hour}/** .

13. Para voltar para a exibição **execuções de pipeline** , selecione **todos os pipelines são executados**e aguarde até que o mesmo pipeline seja disparado novamente automaticamente após outra hora.  

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Selecione **Exibir execução da atividade** para a segunda execução do pipeline quando ele vier e faça o mesmo para examinar os detalhes.  

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)

    Você pode ver o arquivo de origem (arquivo2. txt) foi copiado de **Source/2019/02/26/15/** para **Destination/2019/02/26/15/** com o mesmo nome de arquivo.

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png)

    Você também pode verificar o mesmo usando Gerenciador de Armazenamento do Azure (https://storageexplorer.com/) para examinar os arquivos no contêiner de **destino**

    ![Monitorizar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Passos seguintes
Avance para o tutorial seguinte para saber como transformar dados através de um cluster do Spark no Azure:

> [!div class="nextstepaction"]
>[Transformar dados com um cluster do Spark na cloud](tutorial-transform-data-spark-portal.md)
