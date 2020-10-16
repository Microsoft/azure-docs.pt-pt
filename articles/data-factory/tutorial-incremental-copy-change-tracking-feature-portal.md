---
title: Copie gradualmente dados usando o Change Tracking utilizando o portal Azure
description: Neste tutorial, você cria uma fábrica de dados Azure com um pipeline que carrega dados delta com base em informações de rastreamento de alterações na base de dados de origem na Base de Dados Azure SQL para um armazenamento de bolhas Azure.
services: data-factory
ms.author: yexu
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/12/2018
ms.openlocfilehash: 78b9d3f30ebc8f74433f04c4474121682c4a3f36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542024"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information-using-the-azure-portal"></a>Carregue gradualmente os dados da Base de Dados Azure SQL para o Armazenamento Azure Blob utilizando informações de rastreio de alterações utilizando o portal Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste tutorial, você cria uma fábrica de dados Azure com um pipeline que carrega dados delta com base em informações de rastreamento de **alterações** na base de dados de origem na Base de Dados Azure SQL para um armazenamento de bolhas Azure.  

Vai executar os seguintes passos neste tutorial:

> [!div class="checklist"]
> * Preparar o arquivo de dados de origem
> * Criar uma fábrica de dados.
> * Criar serviços ligados.
> * Crie uma origem, sink e conjuntos de dados de registo de alterações.
> * Criar, executar e monitorizar o pipeline da cópia completa
> * Adicionar ou atualizar os dados na tabela de origem
> * Criar, executar e monitorizar o pipeline da cópia incremental

## <a name="overview"></a>Descrição geral
Uma solução de integração de dados, que carrega dados incrementalmente após os carregamentos de dados iniciais é um cenário bastante utilizado. Em alguns casos, os dados alterados durante um período no seu arquivo de dados de origem podem ser facilmente segmentados (por exemplo, LastModifyTime, CreationTime). Em alguns casos, não há nenhuma forma explícita para identificar os dados delta da última vez que processou os dados. A tecnologia Controlo de Alterações suportada por arquivos de dados como a Base de Dados SQL do Azure e o SQL Server podem ser utilizados para identificar os dados delta.  Este tutorial descreve como utilizar o Azure Data Factory com a tecnologia de Controlo de Alterações do SQL Server para carregar dados delta incrementalmente da Base de Dados SQL do Azure para o Armazenamento de Blobs do Azure.  Para obter informações mais concretas sobre a tecnologia de Controlo de Alterações do SQL Server, consulte [Controlo de alterações no SQL Server](/sql/relational-databases/track-changes/about-change-tracking-sql-server).

## <a name="end-to-end-workflow"></a>Fluxo de trabalho ponto a ponto
Eis os passos de fluxo de trabalho ponto-a-ponto normais para carregar dados incrementalmente com recurso à tecnologia de Controlo de Alterações.

> [!NOTE]
> Ambos a Base de Dados SQL do Azure e o SQL Server suportam a tecnologia de Controlo de Alterações. Este tutorial utiliza a Base de Dados SQL do Azure como o arquivo de dados de origem. Também pode utilizar uma instância SQL Server.

1. **Carregamento de dados históricos inicial** (executar uma vez):
    1. Ativar a tecnologia change tracking na base de dados de origem na Base de Dados Azure SQL.
    2. Obtenha o valor inicial de SYS_CHANGE_VERSION na base de dados como base de base para capturar dados alterados.
    3. Carregue os dados completos da base de dados de origem num armazenamento de bolhas Azure.
2. **Carregamento incremental de dados delta com base numa agenda** (executar periodicamente após o carregamento de dados inicial):
    1. Obter os valores SYS_CHANGE_VERSION novos e antigos.
    3. Carregar os dados delta ao associar as chaves primárias das linhas alteradas (entre dois valores SYS_CHANGE_VERSION) de **change_tracking_tables** com dados da **tabela de origem**, e em seguida, mover os dados delta para o destino.
    4. Atualize o SYS_CHANGE_VERSION para o carregamento delta da próxima vez.

## <a name="high-level-solution"></a>Solução de alto nível
Neste tutorial, vai criar dois pipelines que realizam as seguintes duas operações:  

1. **Carga inicial:** cria um pipeline com uma atividade de cópia que copia os dados inteiros do arquivo de dados de origem (Base de Dados SQL do Azure) para o arquivo de dados de destino (Armazenamento de Blobs do Azure).

    ![Carregamento completo de dados](media/tutorial-incremental-copy-change-tracking-feature-portal/full-load-flow-diagram.png)
1.  **Carga incremental:** cria um pipeline com as seguintes atividades e execute-o periodicamente.
    1. Crie **duas atividades de pesquisa** para obter os SYS_CHANGE_VERSION antigo e novo da Base de Dados SQL do Azure e passe-o para a atividade de cópia.
    2. Crie **uma atividade de cópia** para copiar os dados inseridos/atualizados/eliminados entre os dois valores de SYS_CHANGE_VERSION da Base de Dados SQL do Azure para o Armazenamento de Blobs do Azure.
    3. Crie **uma atividade de procedimentos armazenados** para atualizar o valor de SYS_CHANGE_VERSION para a próxima execução de pipeline.

    ![Diagrama de fluxo de carga incremental](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-load-flow-diagram.png)


Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* **Base de Dados Azure SQL**. A base de dados é utilizada como o arquivo de dados de **origem**. Se não tiver uma base de dados na Base de Dados Azure SQL, consulte a [base de dados Criar uma base de dados no artigo base de dados Azure SQL](../azure-sql/database/single-database-create-quickstart.md) para obter etapas para criar uma.
* **Conta de Armazenamento Azure**. O armazenamento de blobs é utilizado como arquivo de dados de **sink**. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../storage/common/storage-account-create.md) para obter os passos para criar uma. Crie um contentor com o nome **adftutorial**. 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>Criar uma tabela de fontes de dados na Base de Dados Azure SQL

1. Lançar **SQL Server Management Studio,** e ligar-se à Base de Dados SQL.
2. No **Explorador de Servidores**, clique com botão direito do rato em **base de dados** e escolha **Nova Consulta**.
3. Executar o seguinte comando SQL contra a sua base de dados para criar uma tabela chamada `data_source_table` data source store.  

    ```sql
    create table data_source_table
    (
        PersonID int NOT NULL,
        Name varchar(255),
        Age int
        PRIMARY KEY (PersonID)
    );

    INSERT INTO data_source_table
        (PersonID, Name, Age)
    VALUES
        (1, 'aaaa', 21),
        (2, 'bbbb', 24),
        (3, 'cccc', 20),
        (4, 'dddd', 26),
        (5, 'eeee', 22);

    ```

4. Ative o mecanismo de **Controlo de Alterações** na sua base de dados e na tabela de origem (data_source_table) ao executar a seguinte consulta SQL:

    > [!NOTE]
    > - Substitua &lt; o nome da sua base de dados pelo nome da base de &gt; dados na Base de Dados Azure SQL que tenha a data_source_table.
    > - Os dados alterados são mantidos por dois dias no exemplo atual. Se carregar os dados alterados a cada três ou mais dias, alguns dados alterados não são incluídos.  Precisa ou de alterar o valor de CHANGE_RETENTION para um número maior. Em alternativa, certifique-se de que o período para carregar os dados alterados se encontra dentro dos dois dias. Para obter mais informações, consulte [Ativar o registo de alterações para uma base de dados](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server#enable-change-tracking-for-a-database)

    ```sql
    ALTER DATABASE <your database name>
    SET CHANGE_TRACKING = ON  
    (CHANGE_RETENTION = 2 DAYS, AUTO_CLEANUP = ON)  

    ALTER TABLE data_source_table
    ENABLE CHANGE_TRACKING  
    WITH (TRACK_COLUMNS_UPDATED = ON)
    ```
5. Criar uma nova tabela e armazenar ChangeTracking_version com um valor predefinido ao executar a seguinte consulta:

    ```sql
    create table table_store_ChangeTracking_version
    (
        TableName varchar(255),
        SYS_CHANGE_VERSION BIGINT,
    );

    DECLARE @ChangeTracking_version BIGINT
    SET @ChangeTracking_version = CHANGE_TRACKING_CURRENT_VERSION();  

    INSERT INTO table_store_ChangeTracking_version
    VALUES ('data_source_table', @ChangeTracking_version)
    ```

    > [!NOTE]
    > Se os dados não estiverem alterados após ativar o controlo de alterações da Base de Dados SQL, o valor da versão do controlo de alterações é 0.
6. Execute a seguinte consulta para criar um procedimento armazenado na sua base de dados. O pipeline invoca este procedimento armazenado para atualizar a versão de controlo de alterações na tabela que criou no passo anterior.

    ```sql
    CREATE PROCEDURE Update_ChangeTracking_Version @CurrentTrackingVersion BIGINT, @TableName varchar(50)
    AS

    BEGIN

        UPDATE table_store_ChangeTracking_version
        SET [SYS_CHANGE_VERSION] = @CurrentTrackingVersion
    WHERE [TableName] = @TableName

    END    
    ```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Instale os mais recentes módulos Azure PowerShell seguindo instruções sobre [como instalar e configurar a Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
1. No menu esquerdo, **selecione Criar um recurso**Data +  >  **Analytics**  >  **Data Factory**:

   ![Seleção do Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na página **Nova fábrica de dados**, introduza **ADFTutorialDataFactory** no **nome**.

     ![Página Nova fábrica de dados](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-azure-data-factory.png)

   O nome da fábrica de dados Azure deve ser **globalmente único.** Se receber o seguinte erro, altere o nome da fábrica de dados (por exemplo, oseunomeADFTutorialDataFactory) e tente criá-la novamente. Veja o artigo [Data Factory – Naming Rules](naming-rules.md) (Data Factory – Regras de Nomenclatura) para obter as regras de nomenclatura dos artefactos do Data Factory.

   *O nome da fábrica de dados "ADFTutorialDataFactory" não está disponível*
3. Selecione a sua subscrição do **Azure** na qual pretende criar a fábrica de dados.
4. No **Grupo de Recursos**, siga um destes passos:

      - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.
      - Selecione **Criar novo** e introduza o nome de um grupo de recursos.   
         
        Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).  
4. Selecione **V2 (Pré-visualização)** para a **versão**.
5. Selecione a **localização** da fábrica de dados. Só aparecem na lista pendente as localizações que são suportadas. Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.
6. Selecione **Afixar ao dashboard**.     
7. Clique em **Criar**.      
8. No painel de instrumentos, vê-se o seguinte azulejo com estado: **Implantação da fábrica de dados**.

    ![Mosaico “implementar a fábrica de dados”](media/tutorial-incremental-copy-change-tracking-feature-portal/deploying-data-factory.png)
9. Depois de concluída a criação, vai ver a página **Data Factory**, conforme mostrado na imagem.

   ![Home page da fábrica de dados](./media/tutorial-incremental-copy-change-tracking-feature-portal/data-factory-home-page.png)
10. Clique no mosaico **Criar e Monitorizar** para iniciar a interface de utilizador (IU) do Azure Data Factory num separador à parte.
11. Na página **introdução**, mude para o separador **Editar**, no painel do lado esquerdo, conforme mostrado na imagem abaixo:

    ![botão Criar pipeline](./media/tutorial-incremental-copy-change-tracking-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>Criar serviços ligados
Os serviços ligados são criados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Nesta secção, crie serviços ligados à sua conta de Armazenamento Azure e à sua base de dados na Base de Dados Azure SQL.

### <a name="create-azure-storage-linked-service"></a>Criar o serviço ligado do Armazenamento do Azure.
Neste passo, vai ligar a sua Conta de Armazenamento do Azure à fábrica de dados.

1. Clique em **Ligações** e em **+ Nova**.

   ![Botão Nova ligação](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-connection-button-storage.png)
2. Na janela **Novo Serviço Ligado**, selecione **Armazenamento de Blobs do Azure** e clique em **Continuar**.

   ![Selecionar Armazenamento de Blobs do Azure](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-storage.png)
3. Na janela **Novo Serviço Ligado**, siga os passos abaixo:

    1. Introduza **AzureStorageLinkedService** em **Nome**.
    2. Selecione a sua conta de Armazenamento Azure para **o nome da conta de armazenamento**.
    3. Clique em **Guardar**.

   ![Definições da Conta de Armazenamento do Azure](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-database-linked-service"></a>Criar o serviço ligado da Base de Dados SQL do Azure.
Neste passo, ligue a sua base de dados à fábrica de dados.

1. Clique em **Ligações** e em **+ Nova**.
2. Na janela **Novo Serviço Ligado**, selecione **Base de Dados SQL do Azure** e clique em **Continuar**.
3. Na janela **Novo Serviço Ligado**, siga os passos abaixo:

    1. Introduza **AzureSqlDatabaseLinkedService** no campo **Nome**.
    2. Selecione o seu servidor para o campo **de nomes do Servidor.**
    3. Selecione a sua base de dados para o campo **de nomes da Base de Dados.**
    4. Introduza o nome do utilizador no campo **Nome de utilizador**.
    5. Introduza a palavra-passe do utilizador no campo **Palavra-passe**.
    6. Clique em **Testar ligação** para testar a ligação.
    7. Clique em **Guardar** para guardar o serviço ligado.

       ![Definições do serviço ligado da Base de Dados SQL do Azure](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-sql-database-linked-service-settings.png)

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, vai criar conjuntos de dados para representar a origem de dados e o destino dos dados. e o local para armazenar o SYS_CHANGE_VERSION.

### <a name="create-a-dataset-to-represent-source-data"></a>Criar um conjunto de dados para representar os dados de origem
Neste passo, vai criar um conjunto de dados para representar os dados de origem.

1. Na vista de árvore, clique em **+ (mais)** e em **Conjunto de Dados**.

   ![Menu Novo Conjunto de dados](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. Selecione **Base de Dados SQL do Azure** e clique em **Concluir**.

   ![Tipo de conjunto de dados de origem - Base de Dados SQL do Azure](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-sql-database.png)
3. Verá um separador novo para configurar o conjunto de dados. Também verá o conjunto de dados na vista de árvore. Na janela **Propriedades**, altere o nome do conjunto de dados para **SourceDataset**.

   ![Nome do conjunto de dados de origem](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-name.png)    
4. Mude para o separador **Ligação** e faça os seguintes passos:

    1. Selecione **AzureSqlDatabaseLinkedService** em **Serviço ligado**.
    2. Selecione **[dbo].[data_source_table]** em **Table**.

   ![Ligação de origem](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-connection.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>Crie um conjunto de dados para representar os dados copiados para o arquivo de dados de sink.
Neste passo, cria um conjunto de dados para representar os dados que são copiados do arquivo de dados de origem. Criou o contentor adftutorial no seu Armazenamento de Blobs do Azure como parte dos pré-requisitos. Crie o contentor se ainda não existir ou defina-o com o nome de um contentor existente. Neste tutorial, o nome do ficheiro de saída é gerado dinamicamente através da expressão `@CONCAT('Incremental-', pipeline().RunId, '.txt')`.

1. Na vista de árvore, clique em **+ (mais)** e em **Conjunto de Dados**.

   ![Menu Novo Conjunto de dados](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. Selecione **Armazenamento de Blobs do Azure** e clique em **Concluir**.

   ![Tipo de conjunto de dados de sink - Blob de Armazenamento do Azure](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-type.png)
3. Verá um separador novo para configurar o conjunto de dados. Também verá o conjunto de dados na vista de árvore. Na janela **Propriedades**, altere o nome do conjunto de dados para **SinkDataset**.

   ![Conjunto de dados de sink - nome](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-name.png)
4. Mude para o separador **Ligação**, na janela Propriedades, e siga os passos abaixo:

    1. Selecione **AzureStorageLinkedService** em **Serviço ligado**.
    2. Introduza **adftutorial/incchgtracking** na parte **folder** de **filePath**.
    3. Introduza ** \@ o CONCAT («Incremental-', pipeline(). RunId, '.txt')** para parte do **ficheiro.** **filePath**  

       ![Conjunto de dados de sink - ligação](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-connection.png)

### <a name="create-a-dataset-to-represent-change-tracking-data"></a>Criar um conjunto de dados para representar os dados do controlo de alterações
Neste passo, vai criar um conjunto de dados para armazenar a versão de controlo de alterações.  Criou a tabela table_store_ChangeTracking_version como parte dos pré-requisitos.

1. Na vista de árvore, clique em **+ (mais)** e em **Conjunto de Dados**.
2. Selecione **Base de Dados SQL do Azure** e clique em **Concluir**.
3. Verá um separador novo para configurar o conjunto de dados. Também verá o conjunto de dados na vista de árvore. Na janela **Propriedades**, altere o nome do conjunto de dados para **ChangeTrackingDataset**.
4. Mude para o separador **Ligação** e faça os seguintes passos:

    1. Selecione **AzureSqlDatabaseLinkedService** em **Serviço ligado**.
    2. Selecione **[dbo].[table_store_ChangeTracking_version]** em **Tabela**.

## <a name="create-a-pipeline-for-the-full-copy"></a>Criar um pipeline para a cópia completa
Neste passo, cria um pipeline com uma atividade de cópia que copia os dados inteiros do arquivo de dados de origem (Base de Dados SQL do Azure) para o arquivo de dados de destino (Armazenamento de Blobs do Azure).

1. Clique em **+ (mais)**, no painel do lado esquerdo, e clique em **Pipeline**.

    ![A screenshot mostra a opção Pipeline para uma fábrica de dados.](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu.png)
2. Verá um separador novo para configurar o pipeline. Também verá o pipeline na vista de árvore. Na janela **Propriedades**, altere o nome do pipeline para **FullCopyPipeline**.

    ![A screenshot mostra um oleoduto com um nome introduzido.](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-name.png)
3. Na caixa de ferramentas **Atividades**, expanda **Fluxo de Dados** e arraste e largue a atividade **Copy** na superfície de desenho do pipeline e defina o nome ** FullCopyActivity**.

    ![Nome da atividade Full Copy](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-activity-name.png)
4. Mude para o separador **Origem** e selecione **SourceDataset** no campo **Conjunto de Dados de Origem**.

    ![Atividade Copy - origem](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-source.png)
5. Mude para o separador **Sink** e selecione **SinkDataset** no campo**Conjunto de Dados de Sink**.

    ![Atividade Copy - sink](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-sink.png)
6. Para validar a definição do pipeline, clique em **Validar**, na barra de ferramentas. Confirme que não há nenhum erro de validação. Clique em **>>** para fechar o **Relatório de Validação do Pipeline**.

    ![Validar o pipeline](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-validate.png)
7. Para publicar entidades (serviços ligados, conjuntos de dados e pipelines), clique em **Publicar**. Aguarde até que a publicação seja bem-sucedida.

    ![A screenshot mostra a fábrica de dados com o botão Publicar All chamado.](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button.png)
8. Aguarde até ver a mensagem **Publicação com êxito**.

    ![Publicação com êxito](./media/tutorial-incremental-copy-change-tracking-feature-portal/publishing-succeeded.png)
9. Também pode clicar no botão **Mostrar Notificações**, no lado esquerdo, para ver notificações. Para fechar a janela de notificações, clique em **X**.

    ![Mostrar notificações](./media/tutorial-incremental-copy-change-tracking-feature-portal/show-notifications.png)


### <a name="run-the-full-copy-pipeline"></a>Execute o pipeline da cópia completa
Clique em **Acionar**, na barra de ferramentas do pipeline, e clique em **Acionar Agora**.

![A screenshot mostra a opção Trigger Now selecionada no menu Trigger.](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu.png)

### <a name="monitor-the-full-copy-pipeline"></a>Monitorize o pipeline da cópia completa

1. Clique no separador **Monitorizar**, no lado esquerdo. Verá a execução do pipeline na lista e o respetivo estado. Para atualizar a lista, clique em **Atualizar**. As ligações na coluna Ações permitem-lhe ver as execuções de atividades associadas à execução do pipeline e voltar a executar o pipeline.

    ![A screenshot mostra que o oleoduto funciona para uma fábrica de dados.](./media/tutorial-incremental-copy-change-tracking-feature-portal/monitor-full-copy-pipeline-run.png)
2. Para ver as execuções de atividades associadas à execução do pipeline, clique na ligação **Ver Execuções de Atividades**, na coluna **Ações**. Há apenas uma atividade no pipeline, pelo que só vai ver uma entrada na lista. Para voltar à vista do gasoduto, clique na ligação **Pipelines** na parte superior.

    ![A screenshot mostra que a atividade funciona para uma fábrica de dados com a ligação Pipelines chamada.](./media/tutorial-incremental-copy-change-tracking-feature-portal/activity-runs-full-copy.png)

### <a name="review-the-results"></a>Rever os resultados
Vai ver um ficheiro chamado `incremental-<GUID>.txt` na pasta `incchgtracking` do contentor `adftutorial`.

![Ficheiro de saída a partir da cópia completa](media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-output-file.png)

O ficheiro deve ter os dados da sua base de dados:

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>Adicione mais dados à tabela de origem

Execute a seguinte consulta na base de dados para adicionar uma linha e atualizar uma linha.

```sql
INSERT INTO data_source_table
(PersonID, Name, Age)
VALUES
(6, 'new','50');


UPDATE data_source_table
SET [Age] = '10', [name]='update' where [PersonID] = 1

```

## <a name="create-a-pipeline-for-the-delta-copy"></a>Criar um pipeline para a cópia do delta
Neste passo, cria um pipeline com as seguintes atividades e execute-o periodicamente. As duas **atividades de pesquisa** obtêm os SYS_CHANGE_VERSION antigo e novo da Base de Dados SQL do Azure e passe-o para a atividade de cópia. A **atividade de cópia** copia os dados inseridos/atualizados/eliminados entre os dois valores de SYS_CHANGE_VERSION da Base de Dados SQL do Azure para o Armazenamento de Blobs do Azure. A **atividade de procedimentos armazenados** atualiza o valor de SYS_CHANGE_VERSION para a próxima execução de pipeline.

1. Na UI da Fábrica de Dados, mude para o **separador Editar.** Clique **+ (mais)** no painel esquerdo e clique em **Pipeline**.

    ![A screenshot mostra como criar um oleoduto numa fábrica de dados.](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu-2.png)
2. Verá um separador novo para configurar o pipeline. Também verá o pipeline na vista de árvore. Na janela **Propriedades**, altere o nome do pipeline para **IncrementalCopyPipeline**.

    ![Nome do pipeline](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-pipeline-name.png)
3. Expanda **Geral** na caixa de ferramentas **Atividades** e arraste e largue a atividade **Lookup** na superfície de desenho do pipeline. Defina o nome da atividade como **LookupLastChangeTrackingVersionActivity**. Esta atividade obtém a versão do controlo de alterações utilizada na última operação de cópia que está armazenada na tabela **table_store_ChangeTracking_version**.

    ![A screenshot mostra um oleoduto com uma atividade de procura.](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-name.png)
4. Mude para **Definições**, na janela **Propriedades** e selecione **ChangeTrackingDataset** no campo **Conjunto de Dados de Origem**.

    ![A screenshot mostra o separador Definições na janela Propriedades.](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-settings.png)
5. Arraste e largue a atividade **Lookup** da caixa de ferramentas **Atividades** para a superfície de desenho do pipeline. Defina o nome da atividade como **LookupCurrentChangeTrackingVersionActivity**. Esta atividade obtém a versão atual do controlo de alterações.

    ![A screenshot mostra um oleoduto com duas atividades de procura.](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-name.png)
6. Mude para **Definições**, na janela **Propriedades**, e siga os passos abaixo:

   1. Selecione **SourceDataset** no campo **Conjunto de Dados de Origem**.
   2. Selecione **Consulta** em **Utilize Consulta**.
   3. Introduza a seguinte consulta SQL em **consulta**.

       ```sql
       SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion
       ```

      ![A screenshot mostra uma consulta adicionada ao separador Definições na janela Propriedades.](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-settings.png)
7. Na caixa de ferramentas **Atividades**, expanda **Fluxo de Dados** e arraste e largue a atividade **Copy** na superfície de desenho do pipeline. Defina o nome da atividade como **IncrementalCopyActivity**. Esta atividade copia os dados entre a última versão do controlo de alterações e a atual para o arquivo de dados de destino.

    ![Atividade Copy - nome](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-activity-name.png)
8. Mude para o separador **Origem**, na janela **Propriedades**, e siga os passos abaixo:

   1. Selecione **SourceDataset** em **Conjunto de Dados de Origem**.
   2. Selecione **Consulta** em **Utilize Consulta**.
   3. Introduza a seguinte consulta SQL em **consulta**.

       ```sql
       select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}
       ```

      ![Atividade Copy - definições da origem](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-source-settings.png)
9. Mude para o separador **Sink** e selecione **SinkDataset** no campo**Conjunto de Dados de Sink**.

    ![Atividade Copy - definições do sink](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-sink-settings.png)
10. **Ligue ambas as atividades Lookup à atividade Copy**, uma a uma. Arraste o botão **verde** associado à atividade **Lookup** para a atividade **Copy**.

    ![Ligar as atividades Lookup e Copy](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-lookup-and-copy.png)
11. Arraste e largue a atividade **Stored Procedure** da caixa de ferramentas **Atividades** para a superfície de desenho do pipeline. Defina o nome da atividade como **StoredProceduretoUpdateChangeTrackingActivity**. Esta atividade atualiza a versão do controlo de alterações na tabela **table_store_ChangeTracking_version**.

    ![Atividade Stored Procedure - nome](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-activity-name.png)
12. Mude para o separador *Conta do SQL** e selecione **AzureSqlDatabaseLinkedService** em **Serviço ligado**.

    ![Atividade Stored Procedure - Conta do SQL](./media/tutorial-incremental-copy-change-tracking-feature-portal/sql-account-tab.png)
13. Mude para o separador **Procedimento Armazenado** e siga os passos abaixo:

    1. Para **Nome do Procedimento armazenado**, selecione **Update_ChangeTracking_Version**.  
    2. Selecione **Parâmetro de importação**.
    3. Na secção **Parâmetros de procedimentos armazenados**, especifique os seguintes valores para os parâmetros:

        | Nome | Tipo | Valor |
        | ---- | ---- | ----- |
        | CurrentTrackingVersion | Int64 | @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion} |
        | TableName | String | @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName} |

        ![Atividade Stored Procedure - Parâmetros](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-parameters.png)
14. **Ligue a atividade Copy à atividade Stored Procedure**. Arraste e largue o botão **verde** associado à atividade Copy na atividade Stored Procedure.

    ![Ligar as atividades Copy e Stored Procedure](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-copy-stored-procedure.png)
15. Clique em **Validar**, na barra de ferramentas. Confirme que não há erros de validação. Clique em **>>** para fechar a janela **Relatório de Validação do Pipeline**.

    ![Botão Validar](./media/tutorial-incremental-copy-change-tracking-feature-portal/validate-button.png)
16. Clique no botão **Publicar Tudo** para publicar entidades (serviços ligados, conjuntos de dados e pipelines) no serviço Data Factory. Aguarde até ver a mensagem **Publicação com êxito**.

       ![A screenshot mostra o botão Publicar Tudo para uma fábrica de dados.](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button-2.png)    

### <a name="run-the-incremental-copy-pipeline"></a>Executar o pipeline da cópia incremental
1. Clique em **Acionar**, na barra de ferramentas do pipeline, e clique em **Acionar Agora**.

    ![O Screenshot mostra um pipeline com atividades e a opção Trigger Now selecionada a partir do menu Trigger.](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu-2.png)
2. Na janela **Executar Pipeline**, selecione **Concluir**.

### <a name="monitor-the-incremental-copy-pipeline"></a>Monitorizar o pipeline da cópia incremental
1. Clique no separador **Monitorizar**, no lado esquerdo. Verá a execução do pipeline na lista e o respetivo estado. Para atualizar a lista, clique em **Atualizar**. As ligações na coluna **Ações** permitem-lhe ver as execuções de atividades associadas à execução do pipeline e voltar a executar o pipeline.

    ![A screenshot mostra que o gasoduto funciona para uma fábrica de dados, incluindo o seu oleoduto.](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-pipeline-runs.png)
2. Para ver as execuções de atividades associadas à execução do pipeline, clique na ligação **Ver Execuções de Atividades**, na coluna **Ações**. Há apenas uma atividade no pipeline, pelo que só vai ver uma entrada na lista. Para voltar à vista do gasoduto, clique na ligação **Pipelines** na parte superior.

    ![A screenshot mostra que o gasoduto corre para uma fábrica de dados com vários marcados como bem sucedidos.](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-activity-runs.png)


### <a name="review-the-results"></a>Rever os resultados
Vai ver um segundo ficheiro na pasta `incchgtracking` do contentor `adftutorial`.

![Ficheiro de saída a partir da cópia incremental](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-output-file.png)

O ficheiro deve ter apenas os dados delta da sua base de dados. O registo com `U` é a linha atualizada na base de dados e `I` é a linha adicionada.

```
1,update,10,2,U
6,new,50,1,I
```
As primeiros três colunas são dados alterados de data_source_table. As últimas duas colunas são os metadados da tabela do sistema de controlo de alterações. A quarta coluna é o SYS_CHANGE_VERSION para cada linha alterada. A quinta coluna é a operação: U = atualizar, I = inserir.  Para obter detalhes sobre as informações do registo de alterações, consulte [CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql).

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10     2                     U
6        new     50     1                     I
```


## <a name="next-steps"></a>Passos seguintes
Avance para o seguinte tutorial para aprender sobre a cópia de novos ficheiros e alterados apenas com base no seu Último Anomodified:

> [!div class="nextstepaction"]
>[Copiar novos ficheiros por último anúncio modificado](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
