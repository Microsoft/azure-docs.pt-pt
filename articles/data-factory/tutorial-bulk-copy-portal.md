---
title: Copiar dados em massa com o Azure Data Factory | Microsoft Docs
description: Saiba como utilizar o Azure Data Factory e a Atividade de Cópia para copiar dados de um arquivo de dados de origem para um arquivo de dados de destino em massa.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: 16741461df2431cbf4433899dd375741e944ce0f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58112573"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Copiar várias tabelas em massa através do Azure Data Factory
Este tutorial demonstra como **copiar várias tabelas da Base de Dados SQL do Azure para o Azure SQL Data Warehouse**. Também pode aplicar o mesmo padrão noutros cenários de cópia. Por exemplo, copiar tabelas do SQL Server/Oracle para a Base de Dados SQL do Azure/Data Warehouse/Blob do Azure, copiar caminhos diferentes do Blob para tabelas de Base de Dados SQL do Azure.

> [!NOTE]
> - Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

A um nível elevado, este tutorial envolve os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar serviços ligados da Base de Dados SQL do Azure, do Azure SQL Data Warehouse e do Armazenamento do Azure.
> * Criar conjuntos de dados da Base de Dados SQL do Azure e do Azure SQL Data Warehouse.
> * Criar um pipeline para procurar as tabelas a copiar e outro pipeline para executar a operação de cópia real. 
> * Iniciar uma execução de pipeline.
> * Monitorizar o pipeline e execuções de atividades.

Este tutorial utiliza o portal do Azure. Para saber mais sobre como utilizar outras ferramentas/SDKs para criar uma fábrica de dados, veja [Inícios rápidos](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Fluxo de trabalho ponto a ponto
Neste cenário, tem várias tabelas na Base de Dados SQL do Azure que quer copiar para o SQL Data Warehouse. Segue-se a sequência lógica de passos no fluxo de trabalho que ocorre nos pipelines:

![Fluxo de trabalho](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* O primeiro pipeline procura a lista de tabelas que têm de ser copiadas para os arquivos de dados de sink.  Em alternativa, pode manter uma tabela de metadados que apresenta uma lista de todas as tabelas a copiar para o arquivo de dados de sink. Em seguida, o pipeline aciona outro pipeline, que itera cada tabela na base de dados e executa a operação de cópia de dados.
* O segundo pipeline executa a cópia real. Aceita a lista de tabelas como um parâmetro. Para cada tabela na lista, copie a tabela específica na Base de Dados SQL do Azure para a tabela correspondente no SQL Data Warehouse, utilizando [cópia faseada através do Armazenamento de blobs e do PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para um melhor desempenho. Neste exemplo, o primeiro pipeline passa a lista de tabelas como um valor para o parâmetro. 

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* **Conta de Armazenamento do Azure**. A conta de Armazenamento do Azure é utilizada como armazenamento de blobs de teste na operação de cópia em massa. 
* **Base de Dados SQL do Azure**. Esta base de dados contém os dados de origem. 
* **Azure SQL Data Warehouse**. Este armazém de dados contém os dados copiados da Base de Dados SQL. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>Preparar a Base de Dados SQL e o SQL Data Warehouse

**Preparar a Base de Dados SQL do Azure de origem**:

Crie uma Base de Dados SQL do Azure SQL com dados de exemplo do Adventure Works LT, seguindo o artigo [Criar uma base de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md). Este tutorial copia todas as tabelas desta base de dados de exemplo para um SQL Data Warehouse.

**Preparar o Azure SQL Data Warehouse de sink**:

1. Se não tiver um Azure SQL Data Warehouse, veja o artigo [Criar um SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) para obter os passos para criar um.

1. Crie esquemas de tabela correspondentes no SQL Data Warehouse. Pode utilizar o [Utilitário de Migração](https://www.microsoft.com/download/details.aspx?id=49100) para **migrar o esquema** da Base de Dados SQL do Azure para o Azure SQL Data Warehouse. Irá utilizar o Azure Data Factory para migrar/copiar dados num passo mais à frente.

## <a name="azure-services-to-access-sql-server"></a>Serviços do Azure para aceder ao SQL Server

Tanto para a Base de dados SQL como para o SQL Data Warehouse, permita que os serviços do Azure acedam ao SQL Server. Certifique-se de que a definição **Permitir acesso aos serviços do Azure** está **ativada** para o seu SQL Server do Azure. Esta definição permite ao serviço Data Factory ler dados da Base de Dados SQL do Azure e escrever dados no Azure SQL Data Warehouse. Para verificar e ativar desta definição, execute os passos seguintes:

1. Clique no hub **Mais serviços** à esquerda e clique em **Servidores SQL**.
1. Selecione o seu servidor e clique em **Firewall** em **DEFINIÇÕES**.
1. Na página **Definições de firewall**, clique em **ATIVAR** para **Permitir acesso aos serviços do Azure**.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
1. Clique em **Novo** no menu da esquerda, clique em **Dados + Análise** e, em seguida, em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-bulk-copy-portal/new-azure-data-factory-menu.png)
1. Na página **Nova fábrica de dados**, introduza **ADFTutorialBulkCopyDF** no **nome**. 
      
     ![Página Nova fábrica de dados](./media/tutorial-bulk-copy-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se vir o erro seguinte no campo de nome, altere o nome da fábrica de dados (por exemplo, oseunomeADFTutorialBulkCopyDF). Veja o artigo [Data Factory – Naming Rules](naming-rules.md) (Data Factory – Regras de Nomenclatura) para obter as regras de nomenclatura dos artefactos do Data Factory.
  
       `Data factory name “ADFTutorialBulkCopyDF” is not available`
1. Selecione a sua subscrição do **Azure** na qual pretende criar a fábrica de dados. 
1. No **Grupo de Recursos**, siga um destes passos:
     
   - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente. 
   - Selecione **Criar novo** e introduza o nome de um grupo de recursos.   
         
     Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
1. Selecione **V2** para a **versão**.
1. Selecione a **localização** da fábrica de dados. Para obter uma lista de regiões do Azure em que a fábrica de dados está atualmente disponível, selecione as regiões que lhe interessam, na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.
1. Selecione **Afixar ao dashboard**.     
1. Clique em **Criar**.
1. No dashboard, verá o mosaico seguinte com o estado: **Implementar fábrica de dados**. 

     ![Mosaico “implementar a fábrica de dados”](media//tutorial-bulk-copy-portal/deploying-data-factory.png)
1. Depois de concluída a criação, vai ver a página **Data Factory**, conforme mostrado na imagem.
   
     ![Home page da fábrica de dados](./media/tutorial-bulk-copy-portal/data-factory-home-page.png)
1. Clique no mosaico **Criar e Monitorizar** para iniciar a aplicação IU do Data Factory num separador à parte.
1. Na página **introdução**, mude para o separador **Editar**, no painel do lado esquerdo, conforme mostrado na imagem abaixo:  

     ![Página Introdução](./media/tutorial-bulk-copy-portal/get-started-page.png)

## <a name="create-linked-services"></a>Criar serviços ligados
Os serviços ligados são criados para ligar os seus arquivos de dados e computações a uma fábrica de dados. O serviço ligado tem as informações de ligação que o serviço Data Factory utiliza para se ligar ao arquivo de dados no runtime. 

Neste tutorial, vai ligar os arquivos de dados da Base de Dados SQL do Azure, do Azure SQL Data Warehouse e do Armazenamento de Blobs do Azure à fábrica de dados. A Base de Dados SQL do Azure é o arquivo de dados de origem. O Azure SQL Data Warehouse é o arquivo de dados de sink/destino. O Armazenamento de Blobs do Azure destina-se a testar os dados antes de serem isolados no SQL Data Warehouse mediante a utilização do PolyBase. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Criar o serviço ligado da Base de Dados do Azure de origem
Neste passo, vai criar um serviço ligado para ligar a sua base de dados SQL do Azure à fábrica de dados. 

1. Clique em **Ligações**, na parte inferior da janela, e clique em **+ Novo**, na barra de ferramentas. 

    ![Botão Novo serviço ligado](./media/tutorial-bulk-copy-portal/new-linked-service-button.png)
1. Na janela **Novo Serviço Ligado**, selecione **Base de Dados SQL do Azure** e clique em **Continuar**. 

    ![Selecionar a Base de Dados SQL do Azure](./media/tutorial-bulk-copy-portal/select-azure-sql-database.png)
1. Na janela **Novo Serviço Ligado**, siga os passos abaixo: 

    1. Introduza **AzureSqlDatabaseLinkedService** em **Nome**. 
    1. Em **Nome do servidor**, selecione o seu servidor do SQL do Azure.
    1. Em **Nome da base de dados**, selecione a sua base de dados SQL do Azure. 
    1. Introduza o **nome do utilizador** para ligar à base de dados SQL do Azure. 
    1. Introduza a **palavra-passe** do utilizador. 
    1. Para testar a ligação à base de dados SQL do Azure com as informações especificadas, clique em **Testar ligação**.
    1. Clique em **Guardar**.

        ![Definições da Base de Dados SQL do Azure](./media/tutorial-bulk-copy-portal/azure-sql-database-settings.png)

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>Criar o serviço ligado do Azure SQL Data Warehouse de sink

1. No separador **Ligações**, clique em **+ Novo**, novamente na barra de ferramentas. 
1. Na janela **Novo Serviço Ligado**, selecione **Azure SQL Data Warehouse** e clique em **Continuar**. 
1. Na janela **Novo Serviço Ligado**, siga os passos abaixo: 

    1. Introduza **AzureSqlDWLinkedService** em **Nome**. 
    1. Em **Nome do servidor**, selecione o seu servidor do SQL do Azure.
    1. Em **Nome da base de dados**, selecione a sua base de dados SQL do Azure. 
    1. Introduza o **nome do utilizador** para ligar à base de dados SQL do Azure. 
    1. Introduza a **palavra-passe** do utilizador. 
    1. Para testar a ligação à base de dados SQL do Azure com as informações especificadas, clique em **Testar ligação**.
    1. Clique em **Guardar**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Criar o serviço ligado de Armazenamento do Azure de teste
Neste tutorial, vai utilizar o armazenamento de Blobs do Azure como área de teste provisória para ativar o PolyBase para um melhor desempenho de cópia.

1. No separador **Ligações**, clique em **+ Novo**, novamente na barra de ferramentas. 
1. Na janela **Novo Serviço Ligado**, selecione **Armazenamento de Blobs do Azure** e clique em **Continuar**. 
1. Na janela **Novo Serviço Ligado**, siga os passos abaixo: 

    1. Introduza **AzureStorageLinkedService** em **Nome**. 
    1. Selecione a sua **conta de armazenamento do Azure** em **Nome da conta de armazenamento**.
    1. Clique em **Guardar**.


## <a name="create-datasets"></a>Criar conjuntos de dados
Neste tutorial, vai criar conjuntos de dados de origem e sink, que especificam a localização onde os dados são armazenados. 

O conjunto de dados de entrada **AzureSqlDatabaseDataset** refere-se a **AzureSqlDatabaseLinkedService**. O serviço ligado especifica a cadeia de ligação para ligar à base de dados. O conjunto de dados especifica o nome da base de dados e a tabela que contém os dados de origem. 

O conjunto de dados de saída **AzureSqlDWDataset** refere-se a **AzureSqlDWLinkedService**. O serviço ligado especifica a cadeia de ligação para ligar ao armazém de dados. O conjunto de dados especifica a base de dados e a tabela para a qual os dados são copiados. 

Neste tutorial, as tabelas SQL de origem e destino não estão hard-coded nas definições do conjunto de dados. Em vez disso, a atividade ForEach transmite o nome da tabela no runtime à atividade Cópia. 

### <a name="create-a-dataset-for-source-sql-database"></a>Criar um conjunto de dados para a Base de Dados SQL de origem

1. Clique em **+ (mais)**, no painel do lado esquerdo, e clique em **Conjunto de Dados**. 

    ![Menu Novo conjunto de dados](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. Na janela **Novo Conjunto de Dados**, selecione **Base de Dados SQL do Azure** e clique em **Concluir**. Deverá ver um novo separador com o nome **AzureSqlTable1**. 
    
    ![Selecionar a Base de Dados SQL do Azure](./media/tutorial-bulk-copy-portal/select-azure-sql-database-dataset.png)
1. Na janela de propriedades, na parte inferior, introduza **AzureSqlDatabaseDataset** em **Nome**.

1. Mude para o separador **Ligação** e siga os passos abaixo: 

   1. Selecione **AzureSqlDatabaseLinkedService** em **Serviço ligado**.
   1. Selecione qualquer tabela em **Tabela**. Esta é uma tabela fictícia. Vai especificar uma consulta no conjunto de dados de origem quando criar um pipeline. A consulta é utilizada para extrair dados da base de dados SQL do Azure. Em alternativa, pode clicar na caixa de verificação **Editar** e introduzir **dummyName** como o nome da tabela. 

      ![Página de ligação do conjunto de dados de origem](./media/tutorial-bulk-copy-portal/source-dataset-connection-page.png)
 

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Criar um conjunto de dados para o SQL Data Warehouse de sink

1. Clique em **+ (mais)**, no painel do lado esquerdo, e clique em **Conjunto de Dados**. 
1. Na janela **Novo Conjunto de Dados**, selecione **Azure SQL Data Warehouse** e clique em **Concluir**. Deverá ver um novo separador com o nome **AzureSqlDWTable1**. 
1. Na janela de propriedades, na parte inferior, introduza **AzureSqlDWDataset** em **Nome**.
1. Mude para o separador **Parâmetros**, clique em **+ Novo** e introduza **DWTableName** no nome do parâmetro. Se copiar/colar este nome da página, certifique-se de que não **há nenhuma um espaço** no final de **DWTableName**. 

    ![Página de ligação do conjunto de dados de origem](./media/tutorial-bulk-copy-portal/sink-dataset-new-parameter.png)

1. Mude para o separador **Ligação**, 

    a. Selecione **AzureSqlDatabaseLinkedService** em **Serviço ligado**.

    b. Na **Tabela**, assinale a opção **Editar**, clique na caixa de entrada do nome da tabela e, em seguida, clique na ligação **Adicionar conteúdo dinâmico** abaixo. 
    
    ![Nome do parâmetro](./media/tutorial-bulk-copy-portal/table-name-parameter.png)

    c. Na página **Adicionar Conteúdo Dinâmico**, clique em **DWTAbleName** em **Parâmetros**, o que irá preencher automaticamente a caixa de texto da expressão superior `@dataset().DWTableName` e, em seguida, clique em **Concluir**. A propriedade **tableName** do conjunto de dados está definida como o valor que é transmitido como argumento ao parâmetro **DWTableName**. A atividade ForEach itera através de uma lista de tabelas e transmite-as uma a uma à atividade Cópia. 

    ![Construtor de parâmetro do conjunto de dados](./media/tutorial-bulk-copy-portal/dataset-parameter-builder.png)

## <a name="create-pipelines"></a>Criar pipelines
Neste tutorial, vai criar dois pipelines: **IterateAndCopySQLTables** e **GetTableListAndTriggerCopyData**. 

O pipeline **GetTableListAndTriggerCopyData** executa dois passos:

* Procura a tabela do sistema da Base de Dados SQL do Azure para obter a lista de tabelas a copiar.
* Aciona o pipeline **IterateAndCopySQLTables** para executar a cópia de dados real.

**GetTableListAndTriggerCopyData** utiliza uma lista de tabelas como parâmetro. Para cada tabela na lista, copia dados da tabela na Base de Dados SQL do Azure para o Azure SQL Data Warehouse, utilizando a cópia faseada e o PolyBase.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Criar o pipeline IterateAndCopySQLTables

1. No painel do lado esquerdo, clique em **+ (mais)** e clique em **Pipeline**.

    ![Menu Novo pipeline](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. No separador **Geral**, especifique **IterateAndCopySQLTables** para o nome. 

1. Mude para o separador **Parâmetros** e execute as ações seguintes: 

    1. Clique em **+ Novo**. 
    1. Introduza **tableList** no parâmetro **Nome**.
    1. Selecione **Matriz** para **Tipo**.

        ![Parâmetro do pipeline](./media/tutorial-bulk-copy-portal/first-pipeline-parameter.png)
1. Na caixa de ferramentas **Atividades**, expanda **Iteração e Condições** e arraste e largue a atividade **ForEach** na superfície de desenho do pipeline. Também pode pesquisar por atividades na caixa de ferramentas **Atividades**. 

    a. No separador **Geral**, na parte inferior, introduza **IterateSQLTables** em **Nome**. 

    b. Mude para o separador **Definições**, clique na caixa de entrada de **Itens** e, em seguida, clique na ligação **Adicionar conteúdo dinâmico** abaixo. 

    ![Definições da atividade ForEach](./media/tutorial-bulk-copy-portal/for-each-activity-settings.png)

    c. Na página **Adicionar Conteúdo Dinâmico**, feche a secção Variáveis do Sistema e Funções, clique em **tableList** em **Parâmetros**, o que irá preencher automaticamente a caixa de texto da expressão superior como `@pipeline().parameter.tableList` e, em seguida, clique em **Concluir**. 

    ![Construtor de parâmetro do Foreach](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Mude para o separador **Atividades**, clique em **Adicionar atividade** para adicionar uma atividade subordinada à atividade do **ForEach**.

1. Na caixa de ferramentas **Atividades**, expanda **Fluxo de Dados** e arraste e largue a atividade **Copiar** na superfície de desenho do pipeline. Repare no menu de trilho na parte superior. IterateAndCopySQLTable é o nome do pipeline e IterateSQLTables é o nome da atividade ForEach. O estruturador está no âmbito da atividade. Para mudar do editor ForEach para o editor de pipelines, clique na ligação no menu de trilho. 

    ![Copy em ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)
1. Mude para o separador **Origem** e siga os passos abaixo:

    1. Selecione **AzureSqlDatabaseDataset** em **Conjunto de Dados de Origem**. 
    1. Selecione a opção **Consulta** em **Consulta do Utilizador**. 
    1. Clique na caixa de entrada **Consulta** -> selecione **Adicionar conteúdo dinâmico** abaixo -> introduza a seguinte expressão em **Consulta** -> selecione **Concluir**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 

        ![Definições de origem de Copy](./media/tutorial-bulk-copy-portal/copy-source-settings.png)
1. Mude para o separador **Sink** e siga os passos abaixo: 

    1. Selecione **AzureSqlDWDataset** em **Conjunto de Dados de Sink**.
    1. Clique na caixa de entrada para o VALOR do parâmetro DWTableName -> selecione **Adicionar conteúdo dinâmico** abaixo, introduza a expressão `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]` como script, -> selecione **Concluir**.
    1. Expanda **Definições de Polybase** e selecione **Permitir polybase**. 
    1. Limpe a opção **Utilizar Tipo predefinido**. 
    1. Clique na caixa de entrada **Cópia prévia do Script** -> selecione **Adicionar conteúdo dinâmico** abaixo -> introduza a seguinte expressão como um script -> selecione **Concluir**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Definições de sink de Copy](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)

1. Mude para o separador **Definições** e siga os passos abaixo: 

    1. Selecione **Verdadeiro** em **Ativar Teste**.
    1. Selecione **AzureStorageLinkedService** em **Nome do Serviço Ligado da Conta de Armazenamento**.

        ![Ativar o teste](./media/tutorial-bulk-copy-portal/copy-sink-staging-settings.png)

1. Para validar as definições do pipeline, clique em **Validar**, na barra de ferramentas do mesmo. Confirme que não há nenhum erro de validação. Para fechar o **Relatório de Validação do Pipeline**, clique em **>>**.

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Criar o pipeline GetTableListAndTriggerCopyData

Este pipeline executa dois passos:

* Procura a tabela do sistema da Base de Dados SQL do Azure para obter a lista de tabelas a copiar.
* Aciona o pipeline "IterateAndCopySQLTables" para executar a cópia de dados real.

1. No painel do lado esquerdo, clique em **+ (mais)** e clique em **Pipeline**.

    ![Menu Novo pipeline](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. Na janela Propriedades, altere o nome do pipeline para **GetTableListAndTriggerCopyData**. 

1. Na caixa de ferramentas **Atividades**, expanda **Geral** e arraste e largue a atividade **Lookup** na superfície de desenho do pipeline e siga os passos abaixo:

    1. Introduza **LookupTableList** em **Nome**. 
    1. Introduza **Obter a lista de tabelas da base de dados SQL do Azure** em **Descrição**.

        ![Atividade Lookup - página geral](./media/tutorial-bulk-copy-portal/lookup-general-page.png)
1. Mude para a página **Definições** e siga os passos abaixo:

    1. Selecione **AzureSqlDatabaseDataset** em **Conjunto de Dados de Origem**. 
    1. Selecione **Consulta** em **Utilize Consulta**. 
    1. Introduza a seguinte consulta SQL em **consulta**.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Desmarque a caixa de verificação do campo **Primeira linha apenas**.

        ![Atividade Lookup - página de definições](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Arraste e largue a atividade **Execute Pipeline** da caixa de ferramentas Atividades para a superfície de desenho do pipeline e defina o nome como **TriggerCopy**.

    ![Atividade Execute Pipeline - página geral](./media/tutorial-bulk-copy-portal/execute-pipeline-general-page.png)    
1. Mude para a página **Definições** e siga os passos abaixo: 

    1. Selecione **IterateAndCopySQLTables** em **Pipeline invocado**. 
    1. Expanda a secção **Avançadas**. 
    1. Clique em **+ Novo** na secção **Parâmetros**. 
    1. Introduza **tableList** no parâmetro **nome**.
    1. Clique na caixa de entrada VALOR -> selecione **Adicionar conteúdo dinâmico** abaixo -> introduza `@activity('LookupTableList').output.value` como o valor do nome de tabela -> selecione **Concluir**. Vai definir a lista de resultados da atividade Lookup como entrada para o segundo pipeline. A lista de resultados contém a lista de tabelas cujos dados têm de ser copiados para o destino. 

        ![Atividade Execute pipeline - página de definições](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)
1. Arraste a **caixa verde** que está ligada à atividade Lookup para o lado esquerdo da atividade Execute Pipeline para **ligar** a atividade **Lookup** à atividade **Execute Pipeline**.

    ![Ligar as atividades Lookup e Execute Pipeline](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)
1. Para validar o pipeline, clique em **Validar**, na barra de ferramentas. Confirme que não há erros de validação. Para fechar o **Relatório de Validação do Pipeline**, clique em **>>**.

1. Para publicar entidades (conjuntos de dados, pipelines, etc.) para o serviço Data Factory, clique em **Publicar Tudo** na parte superior da janela. Aguarde até que a publicação seja bem-sucedida. 

## <a name="trigger-a-pipeline-run"></a>Acionar uma execução de pipeline

Aceda ao pipeline **GetTableListAndTriggerCopyData**, clique em **Acionador** e clique em **Acionar Agora**. 

![Acionar agora](./media/tutorial-bulk-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.

1. Mude para o separador **Monitorizar**. Clique em **Atualizar** até ver execuções de ambos os pipelines na sua solução. Continue a atualizar a lista até ver o estado **Com Êxito**. 

    ![Execuções de pipeline](./media/tutorial-bulk-copy-portal/pipeline-runs.png)
1. Para ver as execuções de atividades associadas ao pipeline GetTableListAndTriggerCopyData pipeline, clique na primeira ligação da ligação Ações desse pipeline. Deverá ver duas execuções de atividade para esta execução de pipeline. 

    ![Execuções de atividade](./media/tutorial-bulk-copy-portal/activity-runs-1.png)    
1. Para ver o resultado da atividade **Lookup**, clique na ligação na coluna **Saída** da mesma. Pode maximizar e restaurar a janela **Saída**. Depois de rever, clique em **X** para fechar a janela **Saída**.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
1. Para regressar à vista **Execuções do Pipeline**, clique na ligação **Pipelines**, conforme mostrado na imagem. Clique na ligação **Ver Execuções de Atividade** (primeira ligação na coluna **Ações**) do pipeline **IterateAndCopySQLTables**. Deverá ver um resultado conforme mostrado na imagem seguinte: Repare que é um **cópia** execução da atividade para cada tabela na **pesquisa** saída da atividade. 

    ![Execuções de atividade](./media/tutorial-bulk-copy-portal/activity-runs-2.png)
1. Confirme que os dados foram copiados para o SQL Data Warehouse de destino que utilizou neste tutorial. 

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, executou os passos seguintes: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar serviços ligados da Base de Dados SQL do Azure, do Azure SQL Data Warehouse e do Armazenamento do Azure.
> * Criar conjuntos de dados da Base de Dados SQL do Azure e do Azure SQL Data Warehouse.
> * Criar um pipeline para procurar as tabelas a copiar e outro pipeline para executar a operação de cópia real. 
> * Iniciar uma execução de pipeline.
> * Monitorizar o pipeline e execuções de atividades.

Avance para o tutorial seguinte para saber como copiar dados de forma incremental de uma origem para um destino:
> [!div class="nextstepaction"]
>[Copiar dados de forma incremental](tutorial-incremental-copy-portal.md)
