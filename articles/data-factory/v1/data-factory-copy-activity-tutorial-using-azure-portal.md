---
title: 'Tutorial: Criar um pipeline do Azure Data Factory para copiar dados (portal do Azure) | Documentos da Microsoft'
description: Neste tutorial, vai utilizar o portal do Azure para criar um pipeline do Azure Data Factory com uma Atividade de Cópia, para copiar dados de um armazenamento de blobs do Azure para uma base de dados SQL do Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: d9317652-0170-4fd3-b9b2-37711272162b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 168e5eea2004a31b108a8a00d883298f8e2198ba
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090275"
---
# <a name="tutorial-use-azure-portal-to-create-a-data-factory-pipeline-to-copy-data"></a>Tutorial: Utilizar o portal do Azure para criar um pipeline do Data Factory para copiar dados 
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [tutorial de atividade de cópia](../quickstart-create-data-factory-dot-net.md). 

Neste artigo, vai aprender a utilizar o [portal do Azure](https://portal.azure.com) para criar uma fábrica de dados com um pipeline que copia dados de um armazenamento de BLOBs do Azure para uma base de dados do SQL Azure. Se não estiver familiarizado com o Azure Data Factory, leia o artigo [Introduction to Azure Data Factory](data-factory-introduction.md) (Introdução ao Azure Data Factory) antes de fazer este tutorial.   

Neste tutorial, vai criar um pipeline com uma atividade no mesmo: Atividade de cópia. A Atividade de Cópia copia dados de um arquivo de dados suportado para um arquivo de dados sink suportado. Para obter uma lista dos arquivos de dados suportados como origens e sinks, veja [Supported data stores](data-factory-data-movement-activities.md#supported-data-stores-and-formats) (Arquivos de dados suportados). A atividade utiliza a tecnologia de um serviço globalmente disponível que pode copiar dados entre vários arquivos de dados de uma forma segura, fiável e dimensionável. Para obter mais informações sobre a Atividade de Cópia, veja [Data Movement Activities](data-factory-data-movement-activities.md) (Atividades de Movimento de Dados).

Um pipeline pode ter mais de uma atividade. Além disso, pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra. Para obter mais informações, veja [Multiple activities in a pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) (Várias atividades num pipeline). 

> [!NOTE] 
> O pipeline de dados neste tutorial copia dados a partir de um arquivo de dados de origem para um arquivo de dados de destino. Para obter um tutorial sobre como transformar dados com o Azure Data Factory, veja [Tutorial: Criar um pipeline para transformar dados com o cluster do Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de seguir este tutorial, conclua os [pré-requisitos do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="steps"></a>Passos
Veja a seguir os passos que deve executar como parte deste tutorial:

1. Crie uma **fábrica de dados** do Azure. Neste passo, vai criar uma fábrica de dados com o nome ADFTutorialDataFactory. 
2. Crie **serviços ligados** na fábrica de dados. Neste passo, vai criar dois serviços ligados dos tipos: Armazenamento do Azure e Azure base de dados SQL. 
    
    O AzureStorageLinkedService liga a sua conta do Armazenamento do Azure à fábrica de dados. Como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), criou um contentor e carregou dados para esta conta de armazenamento.   

    O AzureSqlLinkedService liga a sua base de dados SQL do Azure à fábrica de dados. Os dados copiados do armazenamento de blobs são armazenados nesta base de dados. Como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), criou uma tabela SQL nesta base de dados.   
3. Crie **conjuntos de dados** de entrada e saída na fábrica de dados.  
    
    O serviço ligado do armazenamento do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua conta de armazenamento do Azure. Além disso, o conjunto de dados de blobs de entrada especifica o contentor e a pasta que contém os dados de entrada.  

    Do mesmo modo, o serviço ligado Base de Dados SQL do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua base de dados SQL do Azure. E o conjunto de dados da tabela SQL de saída especifica a tabela na base de dados para a qual os dados do armazenamento de blobs vão ser copiados.
4. Crie um **pipeline** na fábrica de dados. Neste passo, vai criar um pipeline com uma atividade de cópia.   
    
    A atividade de cópia copia dados de um blob no armazenamento de blobs do Azure para uma tabela na base de dados SQL do Azure. Pode utilizar uma atividade de cópia num pipeline para copiar dados de qualquer origem suportada para qualquer destino suportado. Para ver uma lista dos arquivos de dados suportados, veja o artigo [Data movement activities](data-factory-data-movement-activities.md#supported-data-stores-and-formats) (Atividades de movimento de dados). 
5. Monitorize o pipeline. Neste passo, vai **monitorizar** os setores dos conjuntos de dados de entrada e saída com o portal do Azure. 

## <a name="create-data-factory"></a>Criar fábrica de dados
> [!IMPORTANT]
> Conclua os [pré-requisitos do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), se ainda não o tiver feito.   

Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline pode conter uma atividade ou mais. Por exemplo, uma Atividade de Cópia para copiar dados de uma origem para um arquivo de dados de destino e uma Atividade do Ramo de Registo do HDInsight para executar um Script de Ramo de Registo para transformar dados de entrada em dados de saída do produto. Comecemos este passo com a criação da fábrica de dados.

1. Depois de iniciar sessão no [portal do Azure](https://portal.azure.com/), clique em **Criar um recurso**, no menu da esquerda, clique em **Dados + Análises** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png)    
2. No painel **Nova fábrica de dados**:
   
   1. Introduza **ADFTutorialDataFactory** como **nome**. 
      
         ![Painel Nova fábrica de dados](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png)
      
       O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se receber o seguinte erro, altere o nome da fábrica de dados (por exemplo, oseunomeADFTutorialDataFactory) e tente criá-la novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.
      
           Data factory name “ADFTutorialDataFactory” is not available  
      
       ![Nome do Data Factory não disponível](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png)
   2. Selecione a sua subscrição do **Azure** na qual pretende criar a fábrica de dados. 
   3. No **Grupo de Recursos**, siga um destes passos:
      
      - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente. 
      - Selecione **Criar novo** e introduza o nome de um grupo de recursos.   
         
          Alguns dos passos neste tutorial partem do princípio que utiliza o nome: **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../../azure-resource-manager/resource-group-overview.md).  
   4. Selecione a **localização** da fábrica de dados. A lista pendente só mostra as regiões que o serviço Data Factory suporta.
   5. Selecione **Afixar ao dashboard**.     
   6. Clique em **Criar**.
      
      > [!IMPORTANT]
      > Para criar instâncias do Data Factory, tem de ser um membro da função [Contribuinte do Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) ao nível do grupo de recursos/subscrição.
      > 
      > O nome da fábrica de dados pode ser registado como um nome DNS no futuro e, por conseguinte, ficar publicamente visível.                
      > 
      > 
3. No dashboard, verá o mosaico seguinte com o estado: **Implementar fábrica de dados**. 

    ![Mosaico “implementar a fábrica de dados”](media/data-factory-copy-activity-tutorial-using-azure-portal/deploying-data-factory.png)
4. Depois de concluída a criação, vai ver o painel **Data Factory**, conforme mostrado na imagem.
   
   ![Home page da fábrica de dados](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png)

## <a name="create-linked-services"></a>Criar serviços ligados
Os serviços ligados são criados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Neste tutorial, não vai utilizar serviços de computação, como o Azure HDInsight ou o Azure Data Lake Analytics. Vai utilizar dois arquivos de dados do tipo Armazenamento do Azure (origem) e Base de Dados SQL do Azure (destino). 

Portanto, cria dois serviços ligados com o nome AzureStorageLinkedService e AzureSqlLinkedService dos tipos: AzureStorage e AzureSqlDatabase.  

O AzureStorageLinkedService liga a sua conta do Armazenamento do Azure à fábrica de dados. Esta conta de armazenamento é aquela em que criou um contentor e para a qual carregou os dados como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

O AzureSqlLinkedService liga a sua base de dados SQL do Azure à fábrica de dados. Os dados copiados do armazenamento de blobs são armazenados nesta base de dados. Como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), criou a tabela emp nesta base de dados.  

### <a name="create-azure-storage-linked-service"></a>Criar o serviço ligado do Storage do Azure
Neste passo, vai ligar a sua conta de Armazenamento do Azure à fábrica de dados. Especifique o nome e a chave da sua conta de armazenamento do Azure nesta secção.  

1. No painel **Data Factory**, clique no mosaico **Criar e implementar**.
   
   ![Mosaico Criar e Implementar](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png) 
2. Vai ver o **Editor do Data Factory**, conforme mostrado na imagem abaixo: 

    ![Editor do Data Factory](./media/data-factory-copy-activity-tutorial-using-azure-portal/data-factory-editor.png)
3. No editor, clique no botão **Novo arquivo de dados**, na barra de ferramentas, e selecione  **armazenamento do Azure**, no menu pendente. Deverá ver o modelo JSON para criar um serviço ligado de armazenamento do Azure no painel à direita. 
   
    ![Botão Arquivar dados novos do Editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png)    
3. Substitua `<accountname>` e `<accountkey>` pelo nome da conta e pelos valores da chave de conta da sua conta de armazenamento do Azure. 
   
    ![JSON do Blob Storage do Editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)    
4. Clique em **Implementar**, na barra de ferramentas. Deverá ver agora o **AzureStorageLinkedService** implementado na vista de árvore. 
   
    ![Implementar o Blob Storage do Editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png)

    Para obter mais informações sobre as propriedades JSON na definição do serviço ligado, veja o artigo [Azure Blob Storage connector](data-factory-azure-blob-connector.md#linked-service-properties) (Conector do Armazenamento de Blobs do Azure).

### <a name="create-a-linked-service-for-the-azure-sql-database"></a>Criar um serviço ligado para a SQL Database do Azure
Neste passo, vai ligar a sua base de dados SQL do Azure à fábrica de dados. Especifique o nome do servidor SQL do Azure, o nome da base de dados, o nome de utilizador e a palavra-passe do utilizador nesta secção. 

1. No **Editor do Data Factory**, clique no botão **Novo arquivo de dados**, na barra de ferramentas, e selecione **Base de Dados SQL do Azure** no menu pendente. Deverá ver o modelo JSON para criar o serviço ligado SQL do Azure no painel à direita.
2. Substitua `<servername>`, `<databasename>`, `<username>@<servername>` e `<password>` pelos nomes do servidor, da base de dados, da conta de utilizador e a palavra-passe do SQL do Azure. 
3. Clique em **Implementar**, na barra de ferramentas, para criar e implementar o **AzureSqlLinkedService**.
4. Confirme que consegue ver **AzureSqlLinkedService** na vista de árvore, em **Serviços ligados**.  

    Para obter mais informações sobre estas propriedades JSON, veja [Azure SQL Database connector](data-factory-azure-sql-connector.md#linked-service-properties) (Conector da Base de Dados SQL do Azure).

## <a name="create-datasets"></a>Criar conjuntos de dados
No passo anterior, criou serviços ligados para ligar a sua conta de Armazenamento do Azure e uma base de dados SQL do Azure à fábrica de dados. Neste passo, vai definir dois conjuntos de dados, com os nomes InputDataset e OutputDataset, que representam os dados de entrada e saída que estão armazenados nos arquivos de dados referenciados por AzureStorageLinkedService e AzureSqlLinkedService, respetivamente.

O serviço ligado do armazenamento do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua conta de armazenamento do Azure. Além disso, o conjunto de dados de blobs de entrada (InputDataset) especifica o contentor e a pasta que contém os dados de entrada.  

Do mesmo modo, o serviço ligado Base de Dados SQL do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua base de dados SQL do Azure. E o conjunto de dados da tabela SQL de saída (OututDataset) especifica a tabela na base de dados para a qual os dados do armazenamento de blobs vão ser copiados. 

### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
Neste passo, vai criar um conjunto de dados com o nome InputDataset que aponta para um ficheiro de blobs (emp.text) na pasta raiz de um contentor de blobs (adftutorial) no Armazenamento do Azure, representado pelo serviço ligado AzureStorageLinkedService. Se não especificar um valor para fileName (ou se o ignorar), os dados de todos os blobs da pasta de entrada são copiados para o destino. Neste tutorial, vai especificar um valor para fileName. 

1. No **Editor** do Data Factory, clique em **... Mais**, clique em **Novo conjunto de dados** e clique em **Armazenamento de Blobs do Azure**, no menu pendente. 
   
    ![Menu Novo conjunto de dados](./media/data-factory-copy-activity-tutorial-using-azure-portal/new-dataset-menu.png)
2. Substitua JSON no painel à direita com o fragmento JSON seguinte: 
   
    ```json
    {
      "name": "InputDataset",
      "properties": {
        "structure": [
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adftutorial/",
          "fileName": "emp.txt",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    ```   

    A tabela que se segue fornece descrições para as propriedades JSON utilizadas no fragmento:

    | Propriedade | Descrição |
    |:--- |:--- |
    | tipo | O tipo de propriedade é definido como **AzureBlob**, uma vez que os dados residem num armazenamento de blobs do Azure. |
    | linkedServiceName | Refere-se ao **AzureStorageLinkedService** que criou anteriormente. |
    | folderPath | Especifica o **contentor** de blobs e a **pasta** que contém os blobs de entrada. Neste tutorial, adftutorial é o contentor de blobs e a pasta é a pasta raiz. | 
    | fileName | Esta propriedade é opcional. Se omitir esta propriedade, são escolhidos todos os ficheiros em folderPath. Neste tutorial, **emp.txt** é especificado para fileName, pelo que apenas esse ficheiro é escolhido para processamento. |
    | formato -> tipo |O ficheiro de entrada está em formato de texto, pelo que utilizamos **TextFormat**. |
    | columnDelimiter | As colunas do ficheiro de entrada são delimitadas pelo caráter de **vírgula (`,`)**. |
    | frequência/intervalo | A frequência está definida como **Hora** e o intervalo como **1**, o que significa que os setores de entrada estão disponíveis **à hora**. Por outras palavras, o serviço do Data Factory procura dados de entrada a cada hora na pasta raiz do contentor de blobs (**adftutorial**) que especificou. Procura os dados entre as horas de início e fim do pipeline e não antes ou depois.  |
    | externo | Esta propriedade está definida como **verdadeira** se os dados de entrada não forem gerados por este pipeline. Os dados de entrada deste tutorial estão no ficheiro empt.txt, que não é gerado por este pipeline, pelo que vamos definir esta propriedade como verdadeira. |

    Para obter mais informações sobre estas propriedades JSON, veja [Azure Blob connector](data-factory-azure-blob-connector.md#dataset-properties) (Conector de Blobs do Azure).      
3. Clique em **Implementar**, na barra de ferramentas, para criar e implementar o conjunto de dados **InputDataset**. Confirme que consegue ver **InputDataset** na vista de árvore.

### <a name="create-output-dataset"></a>Criar conjunto de dados de saída
O serviço ligado Base de Dados SQL do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua base de dados SQL do Azure. O conjunto de dados da tabela SQL de saída (OututDataset) que vai criar neste passo especifica a tabela na base de dados para a qual os dados do armazenamento de blobs vão ser copiados.

1. No **Editor** do Data Factory, clique em **... Mais**, clique em **Novo conjunto de dados** e clique em **SQL Azure**, no menu pendente. 
2. Substitua JSON no painel à direita com o fragmento JSON seguinte:

    ```json   
    {
      "name": "OutputDataset",
      "properties": {
        "structure": [
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "emp"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    ```     

    A tabela que se segue fornece descrições para as propriedades JSON utilizadas no fragmento:

    | Propriedade | Descrição |
    |:--- |:--- |
    | tipo | A propriedade de tipo está definida como **AzureSqlTable**, porque os dados são copiados para uma tabela numa base de dados SQL do Azure. |
    | linkedServiceName | Refere-se ao **AzureSqlLinkedService** que criou anteriormente. |
    | tableName | Especificou a **tabela** para a qual os dados são copiados. | 
    | frequência/intervalo | A frequência está definida como **Hora** e o intervalo é **1**, o que significa que os setores de saída são produzidos **à hora** entre as horas de início e fim do pipeline, não antes ou depois.  |

    Existem três colunas – **ID**, **FirstName**, e **LastName** – na tabela emp da base de dados. O ID é uma coluna de identidade, por isso terá de especificar apenas **FirstName** e **LastName** aqui.

    Para obter mais informações sobre estas propriedades JSON, veja o artigo [Azure SQL connector](data-factory-azure-sql-connector.md#dataset-properties) (Conector do SQL do Azure).
3. Clique em **Implementar**, na barra de ferramentas, para criar e implementar o conjunto de dados **OutputDataset**. Confirme que consegue ver **OutputDataset** na vista de árvore, em **Conjuntos de Dados**. 

## <a name="create-pipeline"></a>Criar pipeline
Neste passo, vai criar um pipeline com uma **atividade de cópia** que utiliza **InputDataset** como entrada e **OutputDataset** como saída.

Atualmente, é o conjunto de dados de saída que controla a agenda. Neste tutorial, o conjunto de dados de saída está configurado para produzir um setor uma vez por hora. As horas de início e de fim do pipeline têm um dia de diferença, ou seja, 24 horas. Desta forma, o pipeline produz 24 setores de conjuntos de dados de saída. 

1. No **Editor** do Data Factory, clique em **... Mais** e clique em **Novo pipeline**. Alternativamente, pode clicar com o botão direito em **Pipelines** na vista de árvore e clicar em **Novo pipeline**.
2. Substitua JSON no painel à direita com o fragmento JSON seguinte: 

    ```json   
    {
      "name": "ADFTutorialPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputDataset"
              }
            ],
            "outputs": [
              {
                "name": "OutputDataset"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink",
                "writeBatchSize": 10000,
                "writeBatchTimeout": "60:00:00"
              }
            },
            "Policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2017-05-11T00:00:00Z",
        "end": "2017-05-12T00:00:00Z"
      }
    } 
    ```   
    
    Tenha em atenção os seguintes pontos:
   
   - Na secção atividades, existe apenas uma atividade cujo **type** está definido como **Copy**. Para obter mais informações sobre a atividade de cópia, veja [Data movement activities](data-factory-data-movement-activities.md) (Atividades de movimento de dados). Nas soluções do Data Factory, também pode utilizar [Data transformation activities](data-factory-data-transformation-activities.md) (Atividades de transformação de dados).
   - A entrada da atividade está definida como **InputDataset** e a saída como **OutputDataset**. 
   - Na secção **typeProperties**, **BlobSource** está especificado como o tipo de origem e **SqlSink** como o tipo de sink. Para obter uma lista completa dos arquivos de dados que a atividade de cópia suporta como origens e sinks, veja [supported data stores](data-factory-data-movement-activities.md#supported-data-stores-and-formats) (arquivos de dados suportados). Para saber como utilizar um arquivo de dados suportado específico como origem/sink, clique na ligação na tabela.
   - Tanto o datetime de início como de fim têm de estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2016-10-14T16:32:41Z. O tempo **end** é opcional, mas iremos utilizá-lo neste tutorial. Se não especificar valor da propriedade **end**, esta é calculada como “**início + 48 horas**”. Para executar o pipeline de forma indefinida, especifique **9999-09-09** como o valor da propriedade **end**.
     
     No exemplo anterior existem 24 setores de dados, uma vez que estes são produzidos de hora a hora.

     Para obter descrições das propriedades JSON na definição de um pipeline, veja o artigo [create pipelines](data-factory-create-pipelines.md) (criar pipelines). Para obter descrições das propriedades JSON na definição de uma atividade de cópia, veja [data movement activities](data-factory-data-movement-activities.md) (atividades de movimento de dados). Para obter descrições das propriedades JSON que BlobSource suporta, veja o artigo [Azure Blob connector](data-factory-azure-blob-connector.md) (Conector de Blobs do Azure). Para obter descrições das propriedades JSON que SqlSink suporta, veja o artigo [Azure SQL Database connector](data-factory-azure-sql-connector.md) (Conector da Base de Dados SQL do Azure).
3. Clique em **Implementar** na barra de ferramentas para criar e implementar o **ADFTutorialPipeline**. Verifique se vê o pipeline na vista de árvore. 
4. Agora, feche o painel **Editor** clicando em **X**. Clique em **X** novamente para ver a home page do **Data Factory** relativa a **ADFTutorialDataFactory**.

**Parabéns!** Criou com êxito uma fábrica de dados do Azure, com um pipeline para copiar dados de um armazenamento de blobs do Azure para uma base de dados SQL do Azure. 


## <a name="monitor-pipeline"></a>Monitorizar o pipeline
Neste passo, irá utilizar o portal do Azure para monitorizar os acontecimentos de uma fábrica de dados do Azure.    

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Monitorizar o pipeline com a Aplicação de Monitorização e Gestão
Os passos seguintes mostram-lhe como monitorizar pipelines na sua fábrica de dados com a aplicação Monitorizar e Gerir: 

1. Clique no mosaico **Monitorizar e Gerir** na home page do data factory.
   
    ![Mosaico Monitorizar e Gerir](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-manage-tile.png) 
2. Deverá ver a **Aplicação de Monitorização e Gestão** num separador à parte. 

    > [!NOTE]
    > Se vir que o browser bloqueia enquanto está a “A autorizar...”, siga um destes passos: limpe a caixa de verificação **Bloquear cookies de terceiros e dados do site** (ou) crie uma exceção para **login.microsoftonline.com** e repita para abrir a aplicação novamente.

    ![Aplicação Monitorizar e Gerir](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-and-manage-app.png)
3. Altere a **Hora de início** e a **Hora de fim** para incluir as horas de início (11-05-2017) e de fim (12-02-2017) do seu pipeline e clique em **Aplicar**.       
3. Vai ver as **janelas de atividades** associadas a cada hora no intervalo das horas de início e de fim do pipeline na lista do painel do meio. 
4. Par ver os detalhes de uma janela de atividade, selecione-a na lista **Janelas de Atividades**. 
    ![Detalhes da janela de atividade](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-window-details.png)

    No Explorador de Janelas de Atividades à direita, vai ver que todos os setores até à hora UTC atual (20:12) foram processados (a verde). Os setores 20:00 - 21:00, 21:00 - 22:00; 22:00 - 23:00 e 23:00 - 00:00 ainda não foram processados.

    A secção **Tentativas** no painel da direita disponibiliza informações sobre a execução da atividade relativa a esse setor de dados. Se tiver ocorrido um erro, fornece detalhes sobre o mesmo. Por exemplo, se a pasta ou o contentor de entrada não existir e o processamento do setor falhar, vê uma mensagem de erro que diz que o contentor ou a pasta não existe.

    ![Tentativas de execução de atividades](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-run-attempts.png) 
4. Inicie o **SQL Server Management Studio**, estabeleça ligação à Base de Dados SQL do Azure e verifique se as linhas estão inseridas na tabela **emp** na base de dados.
    
    ![resultados da consulta SQL](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)

Para obter detalhes sobre a utilização desta aplicação, veja [Monitor and manage Azure Data Factory pipelines using Monitoring and Management App (Monitorizar e gerir pipelines do Azure Data Factory com a Aplicação de Monitorização e Gestão)](data-factory-monitor-manage-app.md).

### <a name="monitor-pipeline-using-diagram-view"></a>Monitorizar o pipeline com a Vista de Diagrama
Também pode utilizar a vista de diagrama para monitorizar os pipelines de dados.  

1. No painel **Data Factory**, clique em **Diagrama**.
   
    ![Painel Data Factory – Mosaico do Diagrama](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png)
2. Deverá ver um diagrama semelhante à seguinte imagem: 
   
    ![Vista de diagrama](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png)  
5. Na vista de diagrama, faça duplo clique em **InputDataset** para ver os setores do conjunto de dados.  
   
    ![Conjuntos de dados com InputDataset selecionado](./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithInputDatasetFromBlobSelected.png)   
5. Clique na ligação **Ver mais** para ver todos os setores de dados. Vai ver 24 setores por hora entre as horas de início e de fim do pipeline. 
   
    ![Todos os setores de dados de entrada](./media/data-factory-copy-activity-tutorial-using-azure-portal/all-input-slices.png)  
   
    Repare que todos os setores de dados até à hora UTC atual estão no estado **Pronto** porque o ficheiro **emp.txt** está sempre presente no contentor de blobs **adftutorial\input**. Os setores relativos a horas futuras ainda não estão no estado “pronto”. Confirme que não existem setores na secção **Setores recentemente falhados** na parte inferior.
6. Feche os painéis até ver a vista de diagrama (ou) desloque-se para a esquerda para vê-la. Em seguida, faça duplo clique em **OutputDataset**. 
8. Clique na ligação **Ver mais**, no painel **Tabela** de **OutputDataset**, para ver todos os setores.

    ![painel de setores de dados](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png) 
9. Repare que todos os setores até à hora UTC atual passam do estado **execução pendente** => **Em curso** ==> **estado Pronto**. Os setores do passado (antes da hora atual) são processados do mais recente para o mais antigo, por predefinição. Por exemplo, se a hora atual for 20:12 UTC, o setor para 19:00 - 20:00 é processado antes do setor das 18:00 - 19:00. O setor das 20:00 - 21:00 é processado no final do intervalo das horas por predefinição, ou seja, após as 21:00.  
10. Clique em qualquer setor de dados da lista e deverá ver o painel **Setor de Dados**. Chama-se setor a uma parte dos dados associados a uma janela de atividade. Os setores podem ser um ou vários ficheiros.  
    
     ![painel de setor de dados](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png)
    
     Se o setor não estiver no estado **Pronto**, pode ver os setores a montante que não estão no estado Pronto e estão a impedir a execução do setor atual na lista **Setores a montante que não estão prontos**.
11. No painel **SETOR DE DADOS**, deverá ver toda a atividade executada na lista na parte inferior. Clique numa **execução de atividade** para ver o painel **Detalhes da execução de atividade**. 
    
    ![Detalhes da Execução da Atividade](./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png)

    Neste painel, vai ver quanto tempo demorou a operação de cópia, qual é o débito, quantos bytes de dados foram lidos e escritos, hora de início da execução, hora de fim da execução, etc.  
12. Clique em **X** para fechar todos os painéis até regressar ao painel principal para o **ADFTutorialDataFactory**.
13. (opcional) clique no mosaico **Conjuntos de Dados** ou no mosaico **Pipelines** para obter os painéis que viu nos passos anteriores. 
14. Inicie o **SQL Server Management Studio**, estabeleça ligação à Base de Dados SQL do Azure e verifique se as linhas estão inseridas na tabela **emp** na base de dados.
    
    ![resultados da consulta SQL](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)


## <a name="summary"></a>Resumo
Neste tutorial, vai criar um Azure Data Factory para copiar dados de um blob do Azure para uma base de dados SQL do Azure. Utilizou o portal do Azure para criar a fábrica de dados, serviços ligados, conjuntos de dados e um pipeline. Eis os passos de alto nível que realizou neste tutorial:  

1. Criou uma **fábrica de dados** do Azure.
2. Criou **serviços ligados**:
   1. Um serviço ligado do **Storagedo Azure** para ligar a sua Conta de armazenamento do Azure que contém dados de entrada.     
   2. Um serviço ligado do **SQL do Azure** para ligar a sua base de dados SQL do Azure que contém dados de saída. 
3. Criou **conjuntos de dados** que descrevem dados de entrada e saída para pipelines.
4. Criou um **pipeline** com uma **Atividade de Cópia** com **BlobSource** como origem e **SqlSink** como sink.  

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, utilizou o armazenamento de blobs do Azure como arquivo de dados de origem e uma base de dados SQL do Azure como arquivo de dados de destino numa operação de cópia. A tabela seguinte disponibiliza uma lista dos arquivos de dados que a atividade de cópia suporta como origens e destinos: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Para saber como copiar dados de/para um arquivo de dados, clique na ligação relativa a esse arquivo na tabela.