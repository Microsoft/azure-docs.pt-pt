---
title: 'Tutorial: Criar um pipeline com atividade de cópia com o Visual Studio | Documentos da Microsoft'
description: Neste tutorial, irá criar um pipeline do Azure Data Factory com uma Atividade de Cópia com o Visual Studio.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 1751185b-ce0a-4ab2-a9c3-e37b4d149ca3
ms.service: data-factory
ms.workload: data-services
ms.custom: vs-azure
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8ac17c00f635c4c14c0e4752847aff941f81804a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111621"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>Tutorial: Criar um pipeline com atividade de cópia com o Visual Studio
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
> 

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [tutorial de atividade de cópia](../quickstart-create-data-factory-dot-net.md). 

Neste artigo, vai aprender a utilizar o Microsoft Visual Studio para criar uma fábrica de dados com um pipeline que copia dados de um armazenamento de blobs do Azure para uma base de dados SQL do Azure. Se não estiver familiarizado com o Azure Data Factory, leia o artigo [Introduction to Azure Data Factory](data-factory-introduction.md) (Introdução ao Azure Data Factory) antes de fazer este tutorial.   

Neste tutorial, vai criar um pipeline com uma atividade no mesmo: Atividade de cópia. A Atividade de Cópia copia dados de um arquivo de dados suportado para um arquivo de dados sink suportado. Para obter uma lista dos arquivos de dados suportados como origens e sinks, veja [Supported data stores](data-factory-data-movement-activities.md#supported-data-stores-and-formats) (Arquivos de dados suportados). A atividade utiliza a tecnologia de um serviço globalmente disponível que pode copiar dados entre vários arquivos de dados de uma forma segura, fiável e dimensionável. Para obter mais informações sobre a Atividade de Cópia, veja [Data Movement Activities](data-factory-data-movement-activities.md) (Atividades de Movimento de Dados).

Um pipeline pode ter mais de uma atividade. Além disso, pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra. Para obter mais informações, veja [Multiple activities in a pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) (Várias atividades num pipeline).

> [!NOTE] 
> O pipeline de dados neste tutorial copia dados a partir de um arquivo de dados de origem para um arquivo de dados de destino. Para obter um tutorial sobre como transformar dados com o Azure Data Factory, veja [Tutorial: Criar um pipeline para transformar dados com o cluster do Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Leia o artigo [Descrição Geral do Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e conclua os passos de **pré-requisitos**.       
2. Para criar instâncias do Data Factory, tem de ser um membro da função [Contribuinte do Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) ao nível do grupo de recursos/subscrição.
3. Tem de ter o seguinte instalado no computador: 
   * Visual Studio 2013 ou Visual Studio 2015
   * Transfira o Azure SDK para o Visual Studio 2013 ou Visual Studio 2015. Navegue até à [Página de Transferências do Azure](https://azure.microsoft.com/downloads/) e clique em **VS 2013** ou **VS 2015** na secção **.NET**.
   * Transferir o plug-in mais recente do Azure Data Factory para Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Também pode atualizar o plug-in, efetuando os seguintes passos: No menu, clique em **ferramentas** -> **extensões e atualizações** -> **Online** -> **galeria do Visual Studio**  ->  **Ferramentas do Microsoft Azure Data Factory para Visual Studio** -> **atualização**.

## <a name="steps"></a>Passos
Veja a seguir os passos que deve executar como parte deste tutorial:

1. Crie **serviços ligados** na fábrica de dados. Neste passo, vai criar dois serviços ligados dos tipos: Armazenamento do Azure e Azure base de dados SQL. 
    
    O AzureStorageLinkedService liga a sua conta do Armazenamento do Azure à fábrica de dados. Como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), criou um contentor e carregou dados para esta conta de armazenamento.   

    O AzureSqlLinkedService liga a sua base de dados SQL do Azure à fábrica de dados. Os dados copiados do armazenamento de blobs são armazenados nesta base de dados. Como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), criou uma tabela SQL nesta base de dados.     
2. Crie **conjuntos de dados** de entrada e saída na fábrica de dados.  
    
    O serviço ligado do armazenamento do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua conta de armazenamento do Azure. Além disso, o conjunto de dados de blobs de entrada especifica o contentor e a pasta que contém os dados de entrada.  

    Do mesmo modo, o serviço ligado Base de Dados SQL do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua base de dados SQL do Azure. E o conjunto de dados da tabela SQL de saída especifica a tabela na base de dados para a qual os dados do armazenamento de blobs vão ser copiados.
3. Crie um **pipeline** na fábrica de dados. Neste passo, vai criar um pipeline com uma atividade de cópia.   
    
    A atividade de cópia copia dados de um blob no armazenamento de blobs do Azure para uma tabela na base de dados SQL do Azure. Pode utilizar uma atividade de cópia num pipeline para copiar dados de qualquer origem suportada para qualquer destino suportado. Para ver uma lista dos arquivos de dados suportados, veja o artigo [Data movement activities](data-factory-data-movement-activities.md#supported-data-stores-and-formats) (Atividades de movimento de dados). 
4. Crie uma **fábrica de dados** do Azure quando implementar entidades do Data Factory (serviços ligados, conjuntos de dados/tabelas e pipelines). 

## <a name="create-visual-studio-project"></a>Criar projeto do Visual Studio
1. Inicie o **Visual Studio 2015**. Clique em **Ficheiro**, aponte para **Novo** e, em seguida, clique em **Projeto**. A caixa de diálogo **Novo Projeto** deve aparecer.  
2. Na caixa de diálogo **Novo Projeto**, selecione o modelo **DataFactory** e clique em **Projeto do Data Factory Vazio**.  
   
    ![Caixa de diálogo Novo Projeto](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)
3. Especifique o nome do projeto e a localização e o nome da solução e, em seguida, prima **OK**.
   
    ![Explorador de Soluções](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)    

## <a name="create-linked-services"></a>Criar serviços ligados
Os serviços ligados são criados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Neste tutorial, não vai utilizar serviços de computação, como o Azure HDInsight ou o Azure Data Lake Analytics. Vai utilizar dois arquivos de dados do tipo Armazenamento do Azure (origem) e Base de Dados SQL do Azure (destino). 

Portanto, cria dois serviços ligados dos tipos: AzureStorage e AzureSqlDatabase.  

O serviço ligado Armazenamento do Azure liga a sua conta do Armazenamento do Azure à fábrica de dados. Esta conta de armazenamento é aquela em que criou um contentor e para a qual carregou os dados como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

O serviço ligado SQL do Azure liga a sua base de dados SQL do Azure à fábrica de dados. Os dados copiados do armazenamento de blobs são armazenados nesta base de dados. Como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), criou a tabela emp nesta base de dados.

Os serviços ligados ligam os arquivos de dados ou serviços de computação a um Azure Data Factory. Veja [Arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) relativamente a todas as origens e sinks que a Atividade de Cópia suporta. Veja [Compute linked services (Serviços ligados de computação)](data-factory-compute-linked-services.md) para obter a lista dos serviços de computação que o Data Factory suporta. Neste tutorial, não é utilizado nenhum serviço de computação. 

### <a name="create-the-azure-storage-linked-service"></a>Criar o serviço ligado do Storage do Azure
1. No **Explorador de Soluções**, clique com o botão direito do rato em **Serviços Ligados**, aponte para **Adicionar** e clique em **Novo Item**.      
2. Na caixa de diálogo **Adicionar Novo Item**, selecione **Serviço Ligado do Storage do Azure** na lista e clique em **Adicionar**. 
   
    ![Novo Serviço Ligado](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
3. Substitua `<accountname>` e `<accountkey>`* pelo nome da sua conta de armazenamento do Azure e pela respetiva chave. 
   
    ![Serviço Ligado do Storage do Azure](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)
4. Guarde o ficheiro **AzureStorageLinkedService1.json**.

    Para obter mais informações sobre as propriedades JSON na definição do serviço ligado, veja o artigo [Azure Blob Storage connector](data-factory-azure-blob-connector.md#linked-service-properties) (Conector do Armazenamento de Blobs do Azure).

### <a name="create-the-azure-sql-linked-service"></a>Criar o serviço ligado SQL do Azure
1. Clique com o botão direito do rato no nó **Serviços Ligados** no **Explorador de Soluções**, aponte para **Adicionar** e, em seguida, clique em **Novo Item**. 
2. Desta vez, selecione o **Serviço Ligado SQL do Azure** e clique em **Adicionar**. 
3. No **ficheiro AzureSqlLinkedService1.json**, substitua `<servername>`, `<databasename>`, `<username@servername>` e `<password>` pelos nomes do servidor, da base de dados, da conta de utilizador e a palavra-passe do SQL do Azure.    
4. Guarde o ficheiro **AzureSqlLinkedService1.json**. 
    
    Para obter mais informações sobre estas propriedades JSON, veja [Azure SQL Database connector](data-factory-azure-sql-connector.md#linked-service-properties) (Conector da Base de Dados SQL do Azure).


## <a name="create-datasets"></a>Criar conjuntos de dados
No passo anterior, criou serviços ligados para ligar a sua conta de Armazenamento do Azure e uma base de dados SQL do Azure à fábrica de dados. Neste passo, vai definir dois conjuntos de dados, com os nomes InputDataset e OutputDataset, que representam os dados de entrada e saída que estão armazenados nos arquivos de dados referenciados por AzureStorageLinkedService1 e AzureSqlLinkedService1, respetivamente.

O serviço ligado do armazenamento do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua conta de armazenamento do Azure. Além disso, o conjunto de dados de blobs de entrada (InputDataset) especifica o contentor e a pasta que contém os dados de entrada.  

Do mesmo modo, o serviço ligado Base de Dados SQL do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua base de dados SQL do Azure. E o conjunto de dados da tabela SQL de saída (OututDataset) especifica a tabela na base de dados para a qual os dados do armazenamento de blobs vão ser copiados. 

### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
Neste passo, vai criar um conjunto de dados com o nome InputDataset que aponta para um ficheiro de blobs (emp.text) na pasta raiz de um contentor de blobs (adftutorial) no Armazenamento do Azure, representado pelo serviço ligado AzureStorageLinkedService1. Se não especificar um valor para fileName (ou se o ignorar), os dados de todos os blobs da pasta de entrada são copiados para o destino. Neste tutorial, vai especificar um valor para fileName. 

Aqui, é utilizado o termo “tabelas” em vez de “conjuntos de dados”. Uma tabela é um conjunto de dados retangular e é o único tipo de conjunto de dados suportado atualmente. 

1. Clique com o botão direito do rato em **Tabelas** no **Explorador de Soluções**, aponte para **Adicionar** e, em seguida, clique em **Novo Item**.
2. Na caixa de diálogo **Adicionar Novo Item**, selecione **Blob do Azure** e clique em **Adicionar**.   
3. Substitua o texto JSON com o seguinte texto e guarde o ficheiro **AzureBlobLocation1.json**. 

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
      "linkedServiceName": "AzureStorageLinkedService1",
      "typeProperties": {
        "folderPath": "adftutorial/",
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

### <a name="create-output-dataset"></a>Criar conjunto de dados de saída
Neste passo, vai criar um conjunto de dados de saída com o nome **OutputDataset**. Este conjunto de dados aponta para uma tabela SQL na Base de Dados SQL do Azure representada por **AzureSqlLinkedService1**. 

1. Clique novamente com o botão direito em **Tabelas** no **Explorador de Soluções**, aponte para **Adicionar** e, em seguida, clique em **Novo Item**.
2. Na caixa de diálogo **Adicionar Novo Item**, selecione **SQL do Azure** e clique em **Adicionar**. 
3. Substitua o texto JSON com o seguinte JSON e guarde o ficheiro **AzureSqlTableLocation1.json**.

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
       "linkedServiceName": "AzureSqlLinkedService1",
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

## <a name="create-pipeline"></a>Criar pipeline
Neste passo, vai criar um pipeline com uma **atividade de cópia** que utiliza **InputDataset** como entrada e **OutputDataset** como saída.

Atualmente, é o conjunto de dados de saída que controla a agenda. Neste tutorial, o conjunto de dados de saída está configurado para produzir um setor uma vez por hora. As horas de início e de fim do pipeline têm um dia de diferença, ou seja, 24 horas. Desta forma, o pipeline produz 24 setores de conjuntos de dados de saída. 

1. Clique com o botão direito do rato em **Pipelines** no **Explorador de Soluções**, aponte para **Adicionar** e, em seguida, clique em **Novo Item**.  
2. Selecione **Copiar Pipeline de Dados** na caixa de diálogo **Adicionar Novo Item** e clique em **Adicionar**. 
3. Substitua o JSON pelo seguinte JSON e guarde o ficheiro **CopyActivity1.json**.

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
             "style": "StartOfInterval",
             "retry": 0,
             "timeout": "01:00:00"
           }
         }
       ],
       "start": "2017-05-11T00:00:00Z",
       "end": "2017-05-12T00:00:00Z",
       "isPaused": false
     }
    }
    ```   
   - Na secção atividades, existe apenas uma atividade cujo **type** está definido como **Copy**. Para obter mais informações sobre a atividade de cópia, veja [Data movement activities](data-factory-data-movement-activities.md) (Atividades de movimento de dados). Nas soluções do Data Factory, também pode utilizar [Data transformation activities](data-factory-data-transformation-activities.md) (Atividades de transformação de dados).
   - A entrada da atividade está definida como **InputDataset** e a saída como **OutputDataset**. 
   - Na secção **typeProperties**, **BlobSource** está especificado como o tipo de origem e **SqlSink** como o tipo de sink. Para obter uma lista completa dos arquivos de dados que a atividade de cópia suporta como origens e sinks, veja [supported data stores](data-factory-data-movement-activities.md#supported-data-stores-and-formats) (arquivos de dados suportados). Para saber como utilizar um arquivo de dados suportado específico como origem/sink, clique na ligação na tabela.  
     
     Substitua o valor da propriedade **start** com o dia atual e o valor **end** com o dia seguinte. Pode especificar apenas a parte da data e ignorar a parte da hora em data e hora. Por exemplo, "2016-02-03", que é equivalente a "2016-02-03T00:00:00Z"
     
     Tanto o datetime de início como de fim têm de estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2016-10-14T16:32:41Z. O tempo **end** é opcional, mas iremos utilizá-lo neste tutorial. 
     
     Se não especificar valor da propriedade **end**, esta é calculada como “**início + 48 horas**”. Para executar o pipeline de forma indefinida, especifique **9999-09-09** como o valor da propriedade **end**.
     
     No exemplo anterior existem 24 setores de dados, uma vez que estes são produzidos de hora a hora.

     Para obter descrições das propriedades JSON na definição de um pipeline, veja o artigo [create pipelines](data-factory-create-pipelines.md) (criar pipelines). Para obter descrições das propriedades JSON na definição de uma atividade de cópia, veja [data movement activities](data-factory-data-movement-activities.md) (atividades de movimento de dados). Para obter descrições das propriedades JSON que BlobSource suporta, veja o artigo [Azure Blob connector](data-factory-azure-blob-connector.md) (Conector de Blobs do Azure). Para obter descrições das propriedades JSON que SqlSink suporta, veja o artigo [Azure SQL Database connector](data-factory-azure-sql-connector.md) (Conector da Base de Dados SQL do Azure).

## <a name="publishdeploy-data-factory-entities"></a>Publicar/implementar entidades do Data Factory
Neste passo, vai publicar as entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) que criou anteriormente. Também vai especificar o nome da nova fábrica de dados a ser criada para conter estas entidades.  

1. Clique com o botão direito do rato no Explorador de Soluções e, em seguida, clique em **Publicar**. 
2. Se vir a caixa de diálogo **Iniciar sessão na sua conta Microsoft**, introduza as credenciais da conta com a subscrição do Azure e clique em **iniciar sessão**.
3. Deverá ver a seguinte caixa de diálogo:
   
   ![Caixa de diálogo Publicar](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
4. Na página Configurar fábrica de dados, realize os seguintes passos: 
   
   1. Selecione a opção **Criar Nova Fábrica de Dados**.
   2. Introduza **VSTutorialFactory** para **Nome**.  
      
      > [!IMPORTANT]
      > O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber um erro relativo ao nome da fábrica de dados durante a publicação, altere o nome (por exemplo, seunomeVSTutorialFactory) e tente publicar novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.        
      > 
      > 
   3. Selecione a sua subscrição do Azure no campo **Subscrição**.
      
      > [!IMPORTANT]
      > Se não vir nenhuma subscrição, verifique se iniciou sessão com uma conta de administrador ou coadministrador da subscrição.  
      > 
      > 
   4. Selecione o **grupo de recursos** para criar a fábrica de dados. 
   5. Selecione a **região** da fábrica de dados. A lista pendente só mostra as regiões que o serviço Data Factory suporta.
   6. Clique em **Seguinte** para mudar para a página **Publicar Itens**.
      
       ![Página Configurar fábrica de dados](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
5. Na página **Publicar Itens**, confirme que todas as entidades dos Data Factories estão selecionadas e clique em **Seguinte** para mudar para a página **Resumo**.
   
   ![Página Publicar itens](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
6. Reveja o resumo e clique em **Seguinte** para iniciar o processo de implementação e ver o **Estado da Implementação**.
   
   ![Página Resumo da publicação](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
7. Na página **Estado da Implementação**, deve ver o estado do processo de implementação. Clique em Concluir após a conclusão da implementação.
 
   ![Página de estado de implementação](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png)

Tenha em atenção os seguintes pontos: 

* Se receber o erro: "Esta subscrição não está registada para utilizar o espaço de nomes DataFactory", efetue um dos seguintes procedimentos e tente publicar novamente: 
  
  * No Azure PowerShell, execute o seguinte comando para registar o fornecedor do Data Factory. 

    ```PowerShell    
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    Pode executar o seguinte comando para confirmar que o fornecedor do Data Factory está registado. 
    
    ```PowerShell
    Get-AzResourceProvider
    ```
  * Inicie sessão com a subscrição do Azure no [Portal do Azure](https://portal.azure.com) e navegue até um painel do Data Factory (ou) crie uma fábrica de dados no Portal do Azure. Esta ação regista automaticamente o fornecedor por si.
* O nome da fábrica de dados pode ser registado como um nome DNS no futuro e, por conseguinte, ficar publicamente visível.

> [!IMPORTANT]
> Para criar instâncias do Data Factory, tem de ser administrador/coadministrador da subscrição do Azure

## <a name="monitor-pipeline"></a>Monitorizar o pipeline
Navegue para a home page da sua fábrica de dados:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **Mais serviços**, no menu da esquerda, e em **Fábricas de dados**.

    ![Procurar fábricas de dados](media/data-factory-copy-activity-tutorial-using-visual-studio/browse-data-factories.png)
3. Comece a escrever o nome da sua fábrica de dados.

    ![Nome da fábrica de dados](media/data-factory-copy-activity-tutorial-using-visual-studio/enter-data-factory-name.png) 
4. Na lista de resultados, clique na sua fábrica de dados para ver a home page da mesma.

    ![Home page da fábrica de dados](media/data-factory-copy-activity-tutorial-using-visual-studio/data-factory-home-page.png)
5. Siga as instruções em [Monitorizar conjuntos de dados e pipeline](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline) para monitorizar o pipeline e os conjuntos de dados que criou neste tutorial. Atualmente, o Visual Studio não suporta a monitorização de pipelines do Data Factory. 

## <a name="summary"></a>Resumo
Neste tutorial, irá criar um Azure Data Factory para copiar dados de um blob do Azure para uma base de dados SQL do Azure. Utilizou o Visual Studio para criar a fábrica de dados, serviços ligados, conjuntos de dados e um pipeline. Eis os passos de alto nível que realizou neste tutorial:  

1. Criou uma **fábrica de dados** do Azure.
2. Criou **serviços ligados**:
   1. Um serviço ligado do **Storagedo Azure** para ligar a sua Conta de armazenamento do Azure que contém dados de entrada.     
   2. Um serviço ligado do **SQL do Azure** para ligar a sua base de dados SQL do Azure que contém dados de saída. 
3. Criou **conjuntos de dados** que descrevem dados de entrada e saída para pipelines.
4. Criou um **pipeline** com uma **Atividade de Cópia** com **BlobSource** como origem e **SqlSink** como sink. 

Para ver como utilizar uma atividade do Hive do HDInsight para transformar dados com o cluster Azure HDInsight, consulte [ Tutorial: Crie seu primeiro pipeline para transformar dados com o cluster do Hadoop](data-factory-build-your-first-pipeline.md).

Pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Consulte [Scheduling and execution in Data Factory (Agendamento e execução no Data Factory)](data-factory-scheduling-and-execution.md) para obter informações detalhadas. 

## <a name="view-all-data-factories-in-server-explorer"></a>Ver todas as fábricas de dados no Explorador de Servidores
Esta secção descreve como utilizar o Explorador de Servidores no Visual Studio para ver todas as fábricas de dados na sua subscrição do Azure e criar um projeto do Visual Studio com base numa fábrica já existente. 

1. No **Visual Studio**, clique em **Ver** no menu e, em seguida, clique em **Explorador de Servidores**.
2. Na janela do Explorador de Servidores, expanda **Azure** e **Data Factory**. Se vir **Iniciar Sessão no Visual Studio**, introduza a **conta** associada à sua subscrição do Azure e clique em **Continuar**. Introduza a **palavra-passe** e clique em **Iniciar sessão**. O Visual Studio tenta obter informações sobre todas as fábricas de dados do Azure na sua subscrição. Verá o estado desta operação na janela **Lista de Tarefas do Data Factory**.

    ![Explorador de Servidores](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)

## <a name="create-a-visual-studio-project-for-an-existing-data-factory"></a>Criar um projeto do Visual Studio para uma fábrica de dados existente

- Clique com o botão direito do rato no Explorador de Servidores e selecione **Exportar Fábrica de Dados para Novo Projeto** para criar um projeto do Visual Studio com base numa fábrica de dados existente.

    ![Exportar a fábrica de dados para um projeto VS](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## <a name="update-data-factory-tools-for-visual-studio"></a>Atualizar as ferramentas do Data Factory para Visual Studio
Para atualizar as ferramentas do Azure Data Factory para Visual Studio, realize os seguintes passos:

1. Clique em **Ferramentas** no menu e selecione **Extensões e Atualizações**. 
2. Selecione **Atualizações** no painel esquerdo e, em seguida, selecione **Galeria do Visual Studio**.
3. Selecione **Ferramentas do Azure Data Factory para Visual Studio** e clique em **Atualizar**. Se não vir esta entrada, significa que já tem a versão mais recente das ferramentas. 

## <a name="use-configuration-files"></a>Utilizar os ficheiros de configuração
Pode utilizar os ficheiros de configuração no Visual Studio para configurar as propriedades diferentes para os serviços ligados/tabelas/pipelines para cada ambiente.

Considere a seguinte definição JSON para um serviço ligado do Storage do Azure. Para especificar **connectionString** com outros valores para accountname e accountkey com base no ambiente (Programação/Teste/Produção) para o qual está a implementar entidades do Data Factory. Pode conseguir este comportamento, utilizando um ficheiro de configuração diferente para cada ambiente.

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="add-a-configuration-file"></a>Adicionar um ficheiro de configuração
Adicione um ficheiro de configuração para cada ambiente realizando os seguintes passos:   

1. Com o botão direito do rato, clique no projeto do Data Factory na solução do Visual Studio, aponte para **Adicionar** e clique em **Novo item**.
2. Selecione **Configuração** na lista de modelos instalados à esquerda, selecione **Ficheiro de Configuração**, introduza um **nome** para o ficheiro de configuração e clique em **Adicionar**.

    ![Adicionar o ficheiro de configuração](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Adicione os parâmetros de configuração e os respetivos valores no seguinte formato:

    ```json
    {
        "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
        "AzureStorageLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        ],
        "AzureSqlLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value":  "Server=tcp:<Azure SQL server name>.database.windows.net,1433;Database=<Azure SQL datbase>;User ID=<Username>;Password=<Password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        ]
    }
    ```

    Este exemplo configura a propriedade connectionString de um serviço ligado do Storage do Azure e de um serviço ligado do Azure SQL. Tenha em atenção que a sintaxe para especificar o nome é [JsonPath](https://goessner.net/articles/JsonPath/).   

    Se JSON tiver uma propriedade com uma matriz de valores, conforme apresentado no seguinte código:  

    ```json
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
    ```

    Configure as propriedades conforme apresentado no seguinte ficheiro de configuração (utilize indexação baseada em zero):

    ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>Nomes de propriedade com espaços
Se um nome de propriedade tiver espaços, utilize parênteses retos, conforme ilustrado no exemplo seguinte (nome do servidor da Base de dados):

```json
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>Implementar a solução com uma configuração
Quando publicar entidades do Azure Data Factory no VS, pode especificar a configuração que pretende utilizar para essa operação de publicação.

Para publicar entidades num projeto do Azure Data Factory com o ficheiro de configuração:   

1. Clique com o botão direito do rato no projeto do Data Factory e clique em **Publicar** para ver a caixa de diálogo **Publicar Itens**.
2. Selecione uma fábrica de dados existente ou especifique valores para criar uma fábrica de dados na página **Configurar fábrica de dados** e clique em **Seguinte**.   
3. Na página **Publicar Itens**: verá uma lista pendente com configurações disponíveis para o campo **Selecionar Configuração da Implementação**.

    ![Selecionar o ficheiro de configuração](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. Selecione o **ficheiro de configuração** que pretende utilizar e clique em **Seguinte**.
5. Confirme se vê o nome do ficheiro JSON na página **Resumo** e clique em **Seguinte**.
6. Clique em **Concluir** depois de ter concluído a operação de implementação.

Aquando da implementação, os valores do ficheiro de configuração são utilizados para definir valores para as propriedades dos ficheiros JSON antes de as entidades serem implementadas no serviço do Azure Data Factory.   

## <a name="use-azure-key-vault"></a>Utilizar o Azure Key Vault
Não é aconselhável e frequentemente contra a política de segurança entregar dados confidenciais, como cadeias de ligação para o repositório de código. Consulte a amostra [Publicação Segura do ADF](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFSecurePublish) no GitHub para saber mais sobre o armazenamento e utilização de informações confidenciais no Azure Key Vault ao publicar entidades do Data Factory. A extensão Publicação Segura para o Visual Studio permite que os segredos sejam armazenados no Key Vault e sejam especificadas apenas referências aos mesmos nas configurações de serviços/implementações ligados. Estas referências são convertidas ao publicar entidades do Data Factory no Azure. Estes ficheiros podem então ser consolidados para o repositório de origem, sem expor quaisquer segredos.


## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, utilizou o armazenamento de blobs do Azure como arquivo de dados de origem e uma base de dados SQL do Azure como arquivo de dados de destino numa operação de cópia. A tabela seguinte disponibiliza uma lista dos arquivos de dados que a atividade de cópia suporta como origens e destinos: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Para saber como copiar dados de/para um arquivo de dados, clique na ligação relativa a esse arquivo na tabela.
