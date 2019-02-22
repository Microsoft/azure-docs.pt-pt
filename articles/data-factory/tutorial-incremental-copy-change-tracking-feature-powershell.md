---
title: Copiar dados de forma incremental com o Controlo de Alterações e o Azure Data Factory | Microsoft Docs
description: 'Neste tutorial, vai criar um pipeline do Azure Data Factory, que copia dados delta de forma incremental de várias tabelas numa base de dados do SQL Server local para uma base de dados SQL do Azure. '
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: a7dd8cd349703fc9009695e570b66c3a3e626d15
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593187"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information"></a>Carregar dados de forma incremental da Base de Dados SQL do Azure para o Armazenamento de Blobs do Azure com informações de controlo de alterações 
Neste tutorial, cria uma fábrica de dados do Azure com um pipeline que carrega dados delta com base em informações de **controlo de alterações** na base de dados SQL do Azure de origem para um armazenamento de blobs do Azure.  

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
> Ambos a Base de Dados SQL do Azure e o SQL Server suportam a tecnologia de Controlo de Alterações. Este tutorial utiliza a Base de Dados SQL do Azure como o arquivo de dados de origem. Também pode utilizar um SQL Server local. 

1. **Carregamento de dados históricos inicial** (executar uma vez):
    1. Ative a tecnologia de Controlo de Alterações na base de dados SQL de Azure de origem.
    2. Obtenha o valor inicial da SYS_CHANGE_VERSION na base de dados SQL do Azure como a linha de base para capturar os dados alterados.
    3. Carregar os dados completos da base de dados SQL do Azure para um armazenamento de blobs do Azure. 
2. **Carregamento incremental de dados delta com base numa agenda** (executar periodicamente após o carregamento de dados inicial):
    1. Obter os valores SYS_CHANGE_VERSION novos e antigos.
    3. Carregar os dados delta ao associar as chaves primárias das linhas alteradas (entre dois valores SYS_CHANGE_VERSION) de **change_tracking_tables** com dados da **tabela de origem**, e em seguida, mover os dados delta para o destino.
    4. Atualize o SYS_CHANGE_VERSION para o carregamento delta da próxima vez.

## <a name="high-level-solution"></a>Solução de alto nível
Neste tutorial, vai criar dois pipelines que realizam as seguintes duas operações:  

1. **Carga inicial:** cria um pipeline com uma atividade de cópia que copia os dados inteiros do arquivo de dados de origem (Base de Dados SQL do Azure) para o arquivo de dados de destino (Armazenamento de Blobs do Azure).

    ![Carregamento completo de dados](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-load-flow-diagram.png)
1.  **Carga incremental:** cria um pipeline com as seguintes atividades e execute-o periodicamente. 
    1. Crie **duas atividades de pesquisa** para obter os SYS_CHANGE_VERSION antigo e novo da Base de Dados SQL do Azure e passe-o para a atividade de cópia.
    2. Crie **uma atividade de cópia** para copiar os dados inseridos/atualizados/eliminados entre os dois valores de SYS_CHANGE_VERSION da Base de Dados SQL do Azure para o Armazenamento de Blobs do Azure.
    3. Crie **uma atividade de procedimentos armazenados** para atualizar o valor de SYS_CHANGE_VERSION para a próxima execução de pipeline.

    ![Diagrama de fluxo de carga incremental](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-load-flow-diagram.png)


Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* Azure PowerShell. Instale os módulos do Azure PowerShell mais recentes ao seguir as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/azurerm/install-azurerm-ps).
* **Base de Dados SQL do Azure**. A base de dados é utilizada como o arquivo de dados de **origem**. Se não tiver uma Base de Dados SQL do Azure, veja o artigo [Criar uma base de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md) para obter os passos para criar uma.
* **Conta de Armazenamento do Azure**. O armazenamento de blobs é utilizado como arquivo de dados de **sink**. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md) para obter os passos para criar uma. Crie um contentor com o nome **adftutorial**. 

### <a name="create-a-data-source-table-in-your-azure-sql-database"></a>Criar uma tabela de origem de dados na base de dados SQL do Azure
1. Iniciar **SQL Server Management Studio** e ligar ao seu SQL Server do Azure. 
2. No **Explorador de Servidores**, clique com botão direito do rato em **base de dados** e escolha **Nova Consulta**.
3. Execute o seguinte comando SQL na base de dados SQL do Azure para criar uma tabela com o nome `data_source_table` como arquivo da origem de dados.  
    
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
    > - Substitua &lt;o nome da sua base de dados&gt; com o nome da base de dados SQL do Azure que tenha o data_source_table. 
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
6. Execute a seguinte consulta para criar um procedimento armazenado na base de dados SQL do Azure. O pipeline invoca este procedimento armazenado para atualizar a versão de controlo de alterações na tabela que criou no passo anterior. 

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
Instale os módulos do Azure PowerShell mais recentes ao seguir as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/azurerm/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
1. Defina uma variável para o nome do grupo de recursos que vai utilizar nos comandos do PowerShell mais tarde. Copie o texto do comando seguinte para o PowerShell, especifique um nome para o [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) com aspas duplas e execute o comando. Por exemplo: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Se o grupo de recursos já existir, pode não substituí-lo. Atribua outro valor à variável `$resourceGroupName` e execute novamente o comando
2. Defina uma variável para a localização da fábrica de dados: 

    ```powershell
    $location = "East US"
    ```
3. Para criar o grupo de recursos do Azure, execute o comando abaixo: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Se o grupo de recursos já existir, pode não substituí-lo. Atribua outro valor à variável `$resourceGroupName` e execute novamente o comando. 
3. Defina uma variável para o nome da fábrica de dados. 

    > [!IMPORTANT]
    >  Atualize o nome da fábrica de dados para que seja globalmente exclusivo.  

    ```powershell
    $dataFactoryName = "IncCopyChgTrackingDF";
    ```
5. Para criar a fábrica de dados, execute o cmdlet **Set-AzureRmDataFactoryV2**: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Tenha em atenção os seguintes pontos:

* O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro seguinte, altere o nome e tente novamente.

    ```
    The specified Data Factory name 'ADFIncCopyChangeTrackingTestFactory' is already in use. Data Factory names must be globally unique.
    ```
* Para criar instâncias do Data Factory, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ser um membro das funções **contribuidor** ou **proprietário**, ou um **administrador** da subscrição do Azure.
* Para obter uma lista de regiões do Azure em que a fábrica de dados está atualmente disponível, selecione as regiões que lhe interessam, na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.


## <a name="create-linked-services"></a>Criar serviços ligados
Os serviços ligados são criados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Nesta secção, vai criar serviços ligados para a sua conta de Armazenamento do Azure e a base de dados SQL do Azure. 

### <a name="create-azure-storage-linked-service"></a>Criar o serviço ligado do Armazenamento do Azure.
Neste passo, vai ligar a sua Conta de Armazenamento do Azure à fábrica de dados.

1. Crie um ficheiro JSON com o nome **azurestoragelinkedservice. JSON** na **C:\ADFTutorials\IncCopyChangeTrackingTutorial** pasta com o seguinte conteúdo: (Crie a pasta se ainda não exista.). Substitua `<accountName>`,  `<accountKey>` pelo nome e chave da sua conta de armazenamento do Azure antes de guardar o ficheiro.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. No **Azure PowerShell**, mude para a pasta **C:\ADFTutorials\IncCopyChgTrackingTutorial**.
3. Executar o **Set-AzureRmDataFactoryV2LinkedService** cmdlet para criar o serviço ligado: **AzureStorageLinkedService**. No exemplo seguinte, vai transmitir os valores para os parâmetros **ResourceGroupName** e **DataFactoryName**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Segue-se o resultado do exemplo:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-azure-sql-database-linked-service"></a>Criar o serviço ligado da Base de Dados SQL do Azure.
Neste passo, vai ligar a sua base de dados SQL do Azure à fábrica de dados.

1. Crie um ficheiro JSON com o nome **Azuresqldatabaselinkedservice** na **C:\ADFTutorials\IncCopyChangeTrackingTutorial** pasta com o seguinte conteúdo: Substitua server, database name **, &lt;user id&gt; e &lt;password&gt;** pelo nome do seu Azure SQL server, base de dados, ID de utilizador e palavra-passe antes de guardar o ficheiro. 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name>; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. Na **do Azure PowerShell**, execute o **Set-AzureRmDataFactoryV2LinkedService** cmdlet para criar o serviço ligado: **AzureSQLDatabaseLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Segue-se o resultado do exemplo:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, vai criar conjuntos de dados para representar a origem de dados e o destino dos dados. e o local para armazenar o SYS_CHANGE_VERSION.

### <a name="create-a-source-dataset"></a>Criar um conjunto de dados de origem
Neste passo, vai criar um conjunto de dados para representar os dados de origem. 

1. Crie um ficheiro JSON com o nome SourceDataset.json na mesma pasta com o seguinte conteúdo: 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

2.  Execute o cmdlet Set-AzureRmDataFactoryV2Dataset para criar o conjunto de dados: SourceDataset
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Eis a saída de exemplo do cmdlet:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Criar um conjunto de dados de sink
Neste passo, cria um conjunto de dados para representar os dados que são copiados do arquivo de dados de origem. 

1. Crie um ficheiro JSON com o nome SinkDataset.json na mesma pasta com o seguinte conteúdo: 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incchgtracking",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
    ```

    Crie o contentor adftutorial no seu Armazenamento de Blobs do Azure como parte dos pré-requisitos. Crie o contentor se ainda não existir ou defina-o com o nome de um contentor existente. Neste tutorial, o nome de ficheiro de saída é gerado dinamicamente através da expressão: @CONCAT('Incremental-', pipeline().RunId, '.txt').
2.  Execute o cmdlet Set-AzureRmDataFactoryV2Dataset para criar o conjunto de dados: SinkDataset
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Eis a saída de exemplo do cmdlet:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

### <a name="create-a-change-tracking-dataset"></a>Crie um conjunto de dados de controlo de alterações
Neste passo, vai criar um conjunto de dados para armazenar a versão de controlo de alterações.  

1. Crie um ficheiro JSON com o nome ChangeTrackingDataset.json na mesma pasta com o seguinte conteúdo: 

    ```json
    {
        "name": " ChangeTrackingDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "table_store_ChangeTracking_version"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
    ```

    Crie a tabela table_store_ChangeTracking_version como parte dos pré-requisitos.
2.  Execute o cmdlet Set-AzureRmDataFactoryV2Dataset para criar o conjunto de dados: WatermarkDataset
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "ChangeTrackingDataset" -File ".\ChangeTrackingDataset.json"
    ```

    Eis a saída de exemplo do cmdlet:
    
    ```json
    DatasetName       : ChangeTrackingDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

## <a name="create-a-pipeline-for-the-full-copy"></a>Criar um pipeline para a cópia completa
Neste passo, cria um pipeline com uma atividade de cópia que copia os dados inteiros do arquivo de dados de origem (Base de Dados SQL do Azure) para o arquivo de dados de destino (Armazenamento de Blobs do Azure).

1. Crie um ficheiro JSON: Fullcopypipeline. JSON na mesma pasta com o seguinte conteúdo: 

    ```json
    {
        "name": "FullCopyPipeline",
        "properties": {
            "activities": [{
                "name": "FullCopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
    
                "inputs": [{
                    "referenceName": "SourceDataset",
                    "type": "DatasetReference"
                }],
                "outputs": [{
                    "referenceName": "SinkDataset",
                    "type": "DatasetReference"
                }]
            }]
        }
    }
    ```
2. Execute o cmdlet Set-AzureRmDataFactoryV2Pipeline para criar o pipeline: FullCopyPipeline.
    
   ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "FullCopyPipeline" -File ".\FullCopyPipeline.json"
   ``` 

   Segue-se o resultado do exemplo: 

   ```json
    PipelineName      : FullCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {FullCopyActivity}
    Parameters        :
   ```
 
### <a name="run-the-full-copy-pipeline"></a>Execute o pipeline da cópia completa
Execute o pipeline: **FullCopyPipeline** , utilizando **Invoke-AzureRmDataFactoryV2Pipeline** cmdlet. 

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "FullCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName        
``` 

### <a name="monitor-the-full-copy-pipeline"></a>Monitorize o pipeline da cópia completa

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **Todos os serviços**, pesquise com a palavra-chave `data factories` e selecione **Fábricas de dados**. 

    ![Menu de fábricas de dados](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-data-factories-menu-1.png)
3. Pesquise pela **sua fábrica de dados** na lista de fábricas de dados e selecione-a para iniciar a página da Fábrica de dados. 

    ![Pesquisar pela sua fábrica de dados](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-search-data-factory-2.png)
4. Na página Fábrica de dados, clique no mosaico **Monitorizar e Gerir**. 

    ![Mosaico Monitorizar e Gerir](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-monitor-manage-tile-3.png)    
5. A **Aplicação de Integração de Dados**  abre-se numa aba separada. Pode ver todas as **execuções de pipelines** e os respetivos estados. Note que no seguinte exemplo, o estado da execução do pipeline é **Com Êxito**. Pode verificar os parâmetros transmitidos para o pipeline ao clicar na ligação na coluna **Parâmetros**. Se tiver ocorrido um erro, pode ver uma ligação na coluna **Erro**. Clique na ligação na coluna **Ações**. 

    ![Execuções de pipeline](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-pipeline-runs-4.png)    
6. Ao clicar na ligação na coluna **Ações**, verá a seguinte página que mostra todas as **execuções de atividade** no pipeline. 

    ![Execuções de atividade](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-activity-runs-5.png)
7. Para mudar de volta para a visualização **Execuções do pipeline**, clique em **Pipelines** conforme mostrado na imagem. 


### <a name="review-the-results"></a>Rever os resultados
Vai ver um ficheiro chamado `incremental-<GUID>.txt` na pasta `incchgtracking` do contentor `adftutorial`. 

![Ficheiro de saída a partir da cópia completa](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-copy-output-file.png)

O ficheiro deve ter os dados da base de dados SQL do Azure:

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>Adicione mais dados à tabela de origem

Execute a seguinte consulta na base de dados SQL do Azure para adicionar uma linha e atualizar uma linha. 

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

1. Crie um ficheiro JSON: Incrementalcopypipeline. JSON na mesma pasta com o seguinte conteúdo: 

    ```json
    {
            "name": "IncrementalCopyPipeline",
            "properties": {
                "activities": [
                {
                        "name": "LookupLastChangeTrackingVersionActivity",
                        "type": "Lookup",
                        "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from table_store_ChangeTracking_version"
                            },
        
                            "dataset": {
                            "referenceName": "ChangeTrackingDataset",
                            "type": "DatasetReference"
                            }
                        }
                    },
                    {
                        "name": "LookupCurrentChangeTrackingVersionActivity",
                        "type": "Lookup",
                        "typeProperties": {
                            "source": {
                                "type": "SqlSource",
                                "sqlReaderQuery": "SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion"
                        },
    
                            "dataset": {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                            }
                        }
                    },
    
                    {
                        "name": "IncrementalCopyActivity",
                        "type": "Copy",
                        "typeProperties": {
                            "source": {
                                "type": "SqlSource",
                                "sqlReaderQuery": "select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}"
                            },
                            "sink": {
                                "type": "BlobSink"
                            }
                        },
                        "dependsOn": [
                            {
                                "activity": "LookupLastChangeTrackingVersionActivity",
                                "dependencyConditions": [
                                    "Succeeded"
                                ]
                            },
                            {
                                "activity": "LookupCurrentChangeTrackingVersionActivity",
                                "dependencyConditions": [
                                    "Succeeded"
                                ]
                        }
                        ],
        
                        "inputs": [
                            {
                            "referenceName": "SourceDataset",
                                "type": "DatasetReference"
                        }
                        ],
                        "outputs": [
                            {
                                "referenceName": "SinkDataset",
                                "type": "DatasetReference"
                            }
                        ]
                    },
        
                {
                        "name": "StoredProceduretoUpdateChangeTrackingActivity",
                        "type": "SqlServerStoredProcedure",
                        "typeProperties": {
    
                            "storedProcedureName": "Update_ChangeTracking_Version",
                            "storedProcedureParameters": {
                            "CurrentTrackingVersion": {"value": "@{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}", "type": "INT64" },
                                "TableName":  { "value":"@{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName}", "type":"String"}
                            }
                    },
        
                        "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                            "type": "LinkedServiceReference"
                        },
        
                        "dependsOn": [
                        {
                                "activity": "IncrementalCopyActivity",
                            "dependencyConditions": [
                                    "Succeeded"
                                ]
                            }
                        ]
                    }
                ]
        
            }
    }
    
    ```
2. Execute o cmdlet Set-AzureRmDataFactoryV2Pipeline para criar o pipeline: FullCopyPipeline.
    
   ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Segue-se o resultado do exemplo: 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {LookupLastChangeTrackingVersionActivity, LookupCurrentChangeTrackingVersionActivity, IncrementalCopyActivity, StoredProceduretoUpdateChangeTrackingActivity}
    Parameters        :
   ```

### <a name="run-the-incremental-copy-pipeline"></a>Executar o pipeline da cópia incremental
Execute o pipeline: **IncrementalCopyPipeline** , utilizando **Invoke-AzureRmDataFactoryV2Pipeline** cmdlet. 

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName     
``` 


### <a name="monitor-the-incremental-copy-pipeline"></a>Monitorizar o pipeline da cópia incremental
1. Na **Aplicação de Integração de Dados**, atualize a vista de **execuções de pipeline**. Confirme que vê o IncrementalCopyPipeline na lista. Clique na ligação na coluna **Ações**.  

    ![Execuções de pipeline](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-pipeline-runs-6.png)    
2. Ao clicar na ligação na coluna **Ações**, verá a seguinte página que mostra todas as **execuções de atividade** no pipeline. 

    ![Execuções de atividade](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-activity-runs-7.png)
3. Para mudar de volta para a visualização **Execuções do pipeline**, clique em **Pipelines** conforme mostrado na imagem. 

### <a name="review-the-results"></a>Rever os resultados
Vai ver um segundo ficheiro na pasta `incchgtracking` do contentor `adftutorial`. 

![Ficheiro de saída a partir da cópia incremental](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-copy-output-file.png)

O ficheiro deve ter apenas os dados delta da base de dados SQL do Azure. O registo com `U` é a linha atualizada na base de dados e `I` é a linha adicionada. 

```
1,update,10,2,U
6,new,50,1,I
```
As primeiros três colunas são dados alterados de data_source_table. As últimas duas colunas são os metadados da tabela do sistema de controlo de alterações. A quarta coluna é o SYS_CHANGE_VERSION para cada linha alterada. A quinta coluna é a operação:  U = atualizar, eu = inserir.  Para obter detalhes sobre as informações do registo de alterações, consulte [CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql). 

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10     2                     U
6        new     50     1                     I
```

    
## <a name="next-steps"></a>Passos Seguintes
Avance para o tutorial seguinte para saber como copiar ficheiros de novos e alterados apenas com base na respetiva LastModifiedDate:

> [!div class="nextstepaction"]
>[Copiar novos ficheiros ao lastmodifieddate](tutorial-incremental-copy-lastmodified-copy-data-tool.md)



