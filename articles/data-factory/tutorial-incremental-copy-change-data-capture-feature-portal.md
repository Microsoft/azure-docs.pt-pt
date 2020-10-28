---
title: Copie gradualmente dados usando a Captura de Dados de Alteração
description: Neste tutorial, você cria um pipeline Azure Data Factory que copia dados delta incrementalmente de uma tabela na base de dados Azure SQL Managed Instance para Azure Storage.
services: data-factory
ms.author: nihurt
author: hurtn
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: ''
ms.date: 05/04/2020
ms.openlocfilehash: fd9e78b6bc3513f79b05c9522e891d346e3d31a0
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637518"
---
# <a name="incrementally-load-data-from-azure-sql-managed-instance-to-azure-storage-using-change-data-capture-cdc"></a>Carregue gradualmente os dados do Azure SQL Managed Instance para O Azure Storage utilizando a captura de dados de alteração (CDC)

Neste tutorial, você cria uma fábrica de dados Azure com um pipeline que carrega dados delta com base em informações de **captura de dados de alteração (CDC)** na base de dados de origem Azure SQL Managed Instance para um armazenamento de blob Azure.  

Vai executar os seguintes passos neste tutorial:

> [!div class="checklist"]
> * Preparar o arquivo de dados de origem
> * Criar uma fábrica de dados.
> * Criar serviços ligados.
> * Criar conjuntos de dados de origem e sink.
> * Criar, depurar e executar o oleoduto para verificar se há dados alterados
> * Modificar dados na tabela de origem
> * Complete, corra e monitorize todo o pipeline de cópia incremental

## <a name="overview"></a>Descrição geral
A tecnologia Change Data Capture suportada por lojas de dados como Azure SQL Managed Instances (MI) e SQL Server pode ser usada para identificar dados alterados.  Este tutorial descreve como usar a Azure Data Factory com a tecnologia SQL Change Data Capture para carregar gradualmente dados delta da Azure SQL Managed Instance em Azure Blob Storage.  Para obter informações mais concretas sobre a tecnologia SQL Change Data Capture, consulte [a captura de dados de alteração no SQL Server](/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

## <a name="end-to-end-workflow"></a>Fluxo de trabalho ponto a ponto
Aqui estão os típicos passos de fluxo de trabalho de ponta a ponta para carregar gradualmente dados usando a tecnologia Change Data Capture.

> [!NOTE]
> Tanto o Azure SQL MI como o SQL Server suportam a tecnologia Change Data Capture. Este tutorial utiliza a Azure SQL Managed Instance como a loja de dados de origem. Também pode utilizar um SQL Server local.

## <a name="high-level-solution"></a>Solução de alto nível
Neste tutorial, cria-se um oleoduto que realiza as seguintes operações:  

   1. Crie uma **atividade de pesquisa** para contar o número de registos alterados na tabela CDC da Base de Dados SQL e passá-la para uma atividade if Condition.
   2. Crie uma **Condição Se** verificar se existem registos alterados e, em caso afirmativo, invoque a atividade de cópia.
   3. Crie uma **atividade de cópia** para copiar os dados inseridos/atualizados/eliminados entre a tabela CDC e o Armazenamento Azure Blob.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* **Exemplo gerido pela base de dados Azure SQL** . A base de dados é utilizada como o arquivo de dados de **origem** . Se não tiver uma Instância Gerida pela Base de Dados Azure SQL, consulte o artigo Criar um artigo de Exemplo gerido por [uma base de dados Azure SQL](../azure-sql/managed-instance/instance-create-quickstart.md) para obter passos para criar um.
* **Conta de Armazenamento Azure** . O armazenamento de blobs é utilizado como arquivo de dados de **sink** . Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../storage/common/storage-account-create.md) para obter os passos para criar uma. Crie um recipiente chamado **cru.** 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>Criar uma tabela de fontes de dados na Base de Dados Azure SQL

1. Lance **o SQL Server Management Studio** e ligue-se ao seu servidor Azure SQL Managed Instances.
2. No **Explorador de Servidores** , clique com botão direito do rato em **base de dados** e escolha **Nova Consulta** .
3. Executar o seguinte comando SQL contra a sua base de dados Azure SQL Managed Instances para criar uma tabela chamada `customers` loja de fontes de dados.  

    ```sql
    create table customers 
    (
    customer_id int, 
    first_name varchar(50), 
    last_name varchar(50), 
    email varchar(100), 
    city varchar(50), CONSTRAINT "PK_Customers" PRIMARY KEY CLUSTERED ("customer_id") 
     );
    ```
4. Ativar o mecanismo **de captura de dados de alteração** na sua base de dados e na tabela de origem (clientes) executando a seguinte consulta SQL:

    > [!NOTE]
    > - Substitua &lt; o seu nome de esquema de origem pelo esquema do seu &gt; Azure SQL MI que tenha a tabela de clientes.
    > - A captura de dados de alteração não faz nada como parte das transações que mudam a tabela que está a ser rastreada. Em vez disso, as operações de inserção, atualização e eliminação são escritas no registo de transações. Os dados depositados em tabelas de mudança crescerão incontrolavelmente se não adira periodicamente e sistematicamente os dados. Para obter mais informações, consulte [Ativar a captura de dados de alteração para uma base de dados](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?enable-change-data-capture-for-a-database=&view=sql-server-ver15)

    ```sql
    EXEC sys.sp_cdc_enable_db 
    
    EXEC sys.sp_cdc_enable_table
    @source_schema = 'dbo',
    @source_name = 'customers', 
    @role_name = 'null',
    @supports_net_changes = 1
    ```
5. Insira os dados na tabela dos clientes executando o seguinte comando:

    ```sql
     insert into customers 
        (customer_id, first_name, last_name, email, city) 
     values 
        (1, 'Chevy', 'Leward', 'cleward0@mapy.cz', 'Reading'),
        (2, 'Sayre', 'Ateggart', 'sateggart1@nih.gov', 'Portsmouth'),
        (3, 'Nathalia', 'Seckom', 'nseckom2@blogger.com', 'Portsmouth');
    ```

    > [!NOTE]
    > Não são capturadas alterações históricas na tabela antes de alterar a captura de dados.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Abra o browser **Microsoft Edge** ou **Google Chrome** . Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
1. No menu esquerdo, **selecione Criar um recurso** Data +  >  **Analytics**  >  **Data Factory** :

   ![Seleção do Data Factory no painel "Novo"](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory-menu.png)

2. Na página **Nova fábrica de dados** , introduza **ADFTutorialDataFactory** no **nome** .

     ![Página Nova fábrica de dados](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory.png)

   O nome da fábrica de dados Azure deve ser **globalmente único.** Se receber o seguinte erro, altere o nome da fábrica de dados (por exemplo, oseunomeADFTutorialDataFactory) e tente criá-la novamente. Veja o artigo [Data Factory – Naming Rules](naming-rules.md) (Data Factory – Regras de Nomenclatura) para obter as regras de nomenclatura dos artefactos do Data Factory.

    *O nome da fábrica de dados "ADFTutorialDataFactory" não está disponível.*
3. Selecione **V2** para a **versão** .
4. Selecione a sua subscrição do **Azure** na qual pretende criar a fábrica de dados.
5. No **Grupo de Recursos** , siga um destes passos:

   1. Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.
   2. Selecione **Criar novo** e introduza o nome de um grupo de recursos.   
         
    Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).  
5. Selecione a **localização** da fábrica de dados. Só aparecem na lista pendente as localizações que são suportadas. Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.
6. Des-select **Enable GIT** .     
7. Clique em **Criar** .
8. Uma vez concluída a implementação, clique em **Ir ao recurso**

   ![A screenshot mostra uma mensagem de que a sua implementação está completa e uma opção para ir para o recurso.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-deploy-complete.png)
9. Depois de concluída a criação, vai ver a página **Data Factory** , conforme mostrado na imagem.

   ![A screenshot mostra a fábrica de dados que implementou.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-home-page.png)
10. Clique no mosaico **Criar e Monitorizar** para iniciar a interface de utilizador (IU) do Azure Data Factory num separador à parte.
11. Na página **introdução** , mude para o separador **Editar** , no painel do lado esquerdo, conforme mostrado na imagem abaixo:

    ![botão Criar pipeline](./media/tutorial-incremental-copy-change-data-capture-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>Criar serviços ligados
Os serviços ligados são criados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Nesta secção, cria serviços ligados à sua conta de Armazenamento Azure e ao Azure SQL MI.

### <a name="create-azure-storage-linked-service"></a>Criar o serviço ligado do Armazenamento do Azure.
Neste passo, vai ligar a sua Conta de Armazenamento do Azure à fábrica de dados.

1. Clique em **Ligações** e em **+ Nova** .

   ![Botão Nova ligação](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-connection-button-storage.png)
2. Na janela **Novo Serviço Ligado** , selecione **Armazenamento de Blobs do Azure** e clique em **Continuar** .

   ![Selecionar Armazenamento de Blobs do Azure](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-storage.png)
3. Na janela **Novo Serviço Ligado** , siga os passos abaixo:

   1. Introduza **AzureStorageLinkedService** em **Nome** .
   2. Selecione a sua conta de Armazenamento Azure para **o nome da conta de armazenamento** .
   3. Clique em **Guardar** .

   ![Definições da Conta de Armazenamento do Azure](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-mi-database-linked-service"></a>Criar serviço ligado à base de dados Azure SQL MI.
Neste passo, ligue a sua base de dados Azure SQL MI à fábrica de dados.

> [!NOTE]
> Para quem utiliza o SQL MI consulte [aqui](./connector-azure-sql-managed-instance.md#prerequisites) informações sobre o acesso através de ponto final público vs privado. Se utilizar o ponto final privado, seria necessário executar este gasoduto utilizando um tempo de integração auto-hospedado. O mesmo se aplicaria aos que executam o SQL Server on-prem, em cenários VM ou VNet.

1. Clique em **Ligações** e em **+ Nova** .
2. Na janela **New Linked Service,** selecione **Azure SQL Database Managed Instance** , e clique em **Continuar** .
3. Na janela **Novo Serviço Ligado** , siga os passos abaixo:

   1. Insira **AzureSqlMI1** para o campo **Nome.**
   2. Selecione o seu servidor SQL para o campo **de nomes do Servidor.**
   4. Selecione a sua base de dados SQL para o campo **de nomes da Base de Dados.**
   5. Introduza o nome do utilizador no campo **Nome de utilizador** .
   6. Introduza a palavra-passe do utilizador no campo **Palavra-passe** .
   7. Clique em **Testar ligação** para testar a ligação.
   8. Clique em **Guardar** para guardar o serviço ligado.

   ![Azure SQL MI Base de dados de serviços ligados](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-sql-managed-instance-database-linked-service-settings.png)

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, cria conjuntos de dados para representar a fonte de dados e o destino de dados.

### <a name="create-a-dataset-to-represent-source-data"></a>Criar um conjunto de dados para representar os dados de origem
Neste passo, vai criar um conjunto de dados para representar os dados de origem.

1. Na vista de árvore, clique em **+ (mais)** e em **Conjunto de Dados** .

   ![Menu Novo Conjunto de dados](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. Selecione **Azure SQL Database Managed Instance** , e clique em **Continuar** .

   ![Tipo de conjunto de dados de origem - Base de Dados SQL do Azure](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-sql-database.png)
   
3. No **separador Propriedades Definidas,** defina o nome do conjunto de dados e as informações de ligação:
 
   1. Selecione **AzureSqlMI1** para **o serviço Linked** .
   2. Selecione **[dbo].[ dbo_customers_CT]** para **o nome de mesa.**  Nota: esta tabela foi criada automaticamente quando o CDC foi ativado na tabela de clientes. Os dados alterados nunca são consultados diretamente a partir desta tabela, mas são extraídos através das [funções CDC](/sql/relational-databases/system-functions/change-data-capture-functions-transact-sql?view=sql-server-ver15).

   ![Ligação de origem](./media/tutorial-incremental-copy-change-data-capture-feature-portal/source-dataset-configuration.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>Crie um conjunto de dados para representar os dados copiados para o arquivo de dados de sink.
Neste passo, cria um conjunto de dados para representar os dados que são copiados do arquivo de dados de origem. Criou o contentor do lago de dados no seu Azure Blob Storage como parte dos pré-requisitos. Crie o contentor se ainda não existir ou defina-o com o nome de um contentor existente. Neste tutorial, o nome do ficheiro de saída é gerado dinamicamente utilizando o tempo de disparo, que será configurado mais tarde.

1. Na vista de árvore, clique em **+ (mais)** e em **Conjunto de Dados** .

   ![Menu Novo Conjunto de dados](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. Selecione **Azure Blob Storage** , e clique em **Continuar** .

   ![Tipo de conjunto de dados de sink - Blob de Armazenamento do Azure](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-type.png)
3. Selecione **DelimitedText** e clique em **Continuar** .

   ![Formato de conjunto de dados de sink - DelimitedText](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-format.png)
4. No **separador 'Propriedades definidas',** defina o nome do conjunto de dados e as informações de ligação:

   1. Selecione **AzureStorageLinkedService** em **Serviço ligado** .
   2. Introduza **cru** para parte do **recipiente** do **ficheiroPath** .
   3. Ativar **a primeira linha como cabeçalho**
   4. Clique **em Ok**

   ![Conjunto de dados de sink - ligação](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration.png)

## <a name="create-a-pipeline-to-copy-the-changed-data"></a>Criar um pipeline para copiar os dados alterados
Neste passo, cria-se um gasoduto que verifica primeiro o número de registos alterados presentes na tabela de mudanças utilizando uma **atividade de procuração** . Uma atividade de condição IF verifica se o número de registos alterados é superior a zero e executa uma **atividade de cópia** para copiar os dados inseridos/atualizados/eliminados da Base de Dados Azure SQL para o Armazenamento de Blob Azure. Por último, um gatilho da janela caindo é configurado e os tempos de início e fim serão passados para as atividades como parâmetros de janela de início e fim. 

1. Na UI da Fábrica de Dados, mude para o **separador Editar.** Clique **+ (mais)** no painel esquerdo e clique em **Pipeline** .

    ![Menu Novo pipeline](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-pipeline-menu.png)
2. Verá um separador novo para configurar o pipeline. Também verá o pipeline na vista de árvore. Na janela **Propriedades** , altere o nome do pipeline para **IncrementalCopyPipeline** .

    ![Nome do pipeline](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-name.png)
3. Expanda **Geral** na caixa de ferramentas **Atividades** e arraste e largue a atividade **Lookup** na superfície de desenho do pipeline. Desa estação o nome da atividade para **GetChangeCount** . Esta atividade obtém o número de registos na tabela de mudanças para uma determinada janela de tempo.

    ![Atividade Lookup - nome](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-name.png)
4. Mude para as **Definições** na janela **Propriedades:**
   1. Especifique o nome do conjunto de dados SQL MI para o campo **Conjunto de Dados de Origem.**
   2. Selecione a opção 'Consulta' e introduza o seguinte na caixa de consulta:
    ```sql
    DECLARE  @from_lsn binary(10), @to_lsn binary(10);  
    SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers');  
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal',  GETDATE());
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
    ```
   3. Ativar **apenas a primeira linha**

    ![Atividade Lookup -definições](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-settings.png)
5. Clique no botão **de dados de pré-visualização** para garantir que uma saída válida é obtida pela atividade de procura

    ![Atividade de lookup - pré-visualização](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-preview.png)
6. Expandir **a iteração & condicional na** caixa de ferramentas **Atividades** e arrastar a atividade **If Condition** para a superfície do designer do pipeline. Desacorra o nome da atividade a **HasChangedRows** . 

    ![Se Atividade da Condição - nome](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-name.png)
7. Mude para as **Atividades** na janela **Propriedades:**

   1. Insira a seguinte **Expressão**
   
    ```adf
    @greater(int(activity('GetChangeCount').output.firstRow.changecount),0)
    ```

   2. Clique no ícone do lápis para editar a condição Verdadeira.

   ![Se Atividade de Condição - definições](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-setting.png)

   3. Expanda **a General** na caixa de **ferramentas Das Atividades** e deixe cair uma atividade **de espera** para a superfície do designer de gasodutos. Trata-se de uma atividade temporária para depurar a condição De sese e será alterada mais tarde no tutorial. 

   ![If Condition True - espere](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-wait.png)

   4. Clique na migalha de pão IncrementalCopyPipeline para voltar ao oleoduto principal.

8. Execute o gasoduto no modo **Debug** para verificar se o gasoduto executa com sucesso. 

   ![Gasoduto - depurar](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug.png)
9. Em seguida, volte ao passo da condição Real e elimine a atividade **de Espera.** Na caixa de **ferramentas Atividades,** expanda o **Move & transformar,** e arrastar uma atividade **copy** para a superfície do designer de pipeline. Defina o nome da atividade como **IncrementalCopyActivity** . 

   ![Atividade Copy - nome](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-name.png)
10. Mude para o separador **Origem** , na janela **Propriedades** , e siga os passos abaixo:

   1. Especifique o nome do conjunto de dados SQL MI para o campo **Conjunto de Dados de Origem.** 
   2. Selecione **Consulta** em **Utilize Consulta** .
   3. Introduza o seguinte para **consulta.**

      ```sql
      DECLARE @from_lsn binary(10), @to_lsn binary(10); 
      SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers'); 
      SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal', GETDATE());
      SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
      ```

   ![Atividade Copy - definições da origem](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-settings.png)

11. Clique em pré-visualização para verificar se a consulta devolve corretamente as linhas alteradas.

    ![A screenshot mostra a pré-visualização para verificar a consulta.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-preview.png)
12. Mude para o **separador Sink** e especifique o conjunto de dados de armazenamento Azure para o campo **Conjunto de Dados de Sumidouro.**

    ![A imagem mostra a conta da Pia.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-sink-settings.png)
13. Clique de volta para a tela do gasoduto principal e ligue a atividade **de Lookup** à atividade **Se Condition,** uma a uma. Arraste o botão **verde** ligado à atividade **De procurar** para a atividade **'Se Estiver'.**

    ![Ligar as atividades Lookup e Copy](./media/tutorial-incremental-copy-change-data-capture-feature-portal/connect-lookup-if.png)
14. Clique em **Validar** , na barra de ferramentas. Confirme que não há erros de validação. Clique em **>>** para fechar a janela **Relatório de Validação do Pipeline** .

    ![Botão Validar](./media/tutorial-incremental-copy-change-data-capture-feature-portal/validate-button.png)
15. Clique em Debug para testar o pipeline e verificar se um ficheiro é gerado no local de armazenamento.

    ![Depuragem incremental do gasoduto depurar-2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-2.png)
16. Publique entidades (serviços ligados, conjuntos de dados e oleodutos) ao serviço Data Factory clicando no botão **Publicar todos.** Aguarde até ver a mensagem **Publicação com êxito** .

    ![Botão Publicar](./media/tutorial-incremental-copy-change-data-capture-feature-portal/publish-button-2.png)    

### <a name="configure-the-tumbling-window-trigger-and-cdc-window-parameters"></a>Configure o gatilho da janela caindo e os parâmetros da janela CDC 
Neste passo, cria-se um gatilho de janela para executar o trabalho num horário frequente. Utilizará as variáveis do sistema WindowStart e WindowEnd do gatilho da janela caindo e passará-as como parâmetros para o seu pipeline para ser utilizado na consulta CDC.

1. Navegue para o separador **Parâmetros** do pipeline **IncrementalCopyPipeline** e utilizando o botão **+ Novo** adicione dois parâmetros **(triggerStartTime** e **triggerEndTime** ) ao pipeline, que representará o início e o fim da janela. Para efeitos de depuração, adicione valores predefinidos no formato **YYYYY-MM-DD HH24:MI:SS.FFF,** mas certifique-se de que o triggerStartTime não está antes de o CDC ser ativado na tabela, caso contrário isso resultará num erro.

    ![Menu Acionar Agora](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-parameters.png)
2. Clique no separador definições da atividade **Desaconsecção** e configufique a consulta para utilizar os parâmetros de início e fim. Copie o seguinte na consulta:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```

3. Navegue para a atividade **Copiar** no caso Verdadeiro da atividade **Se Condicionar** e clique no separador **'Fonte'.** Copie o seguinte na consulta:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```
4. Clique no **separador 'Afundar'** da atividade **Copy** e clique em **Abrir** para editar as propriedades do conjunto de dados. Clique no separador **Parâmetros** e adicione um novo parâmetro chamado **triggerStart**    

    ![A screenshot mostra a adição de um novo parâmetro ao separador Parâmetros.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-2.png)
5. Em seguida, configurar as propriedades do conjunto de dados para armazenar os dados num **subdiretório/subdiretório incremental** com divisórias baseadas em datas.
   1. Clique no **separador 'Ligação'** das propriedades do conjunto de dados e adicione conteúdo dinâmico tanto para as secções **Do Diretório** como para o **Ficheiro.** 
   2. Introduza a seguinte expressão na secção **Diretório** clicando no link de conteúdo dinâmico na caixa de texto:
    
    ```sql
    @concat('customers/incremental/',formatDateTime(dataset().triggerStart,'yyyy/MM/dd'))
    ```
   3. Introduza a seguinte expressão na secção **Ficheiro.** Isto criará nomes de ficheiros baseados na data e hora de início do gatilho, sufixados com a extensão csv:
    
    ```sql
    @concat(formatDateTime(dataset().triggerStart,'yyyyMMddHHmmssfff'),'.csv')
    ```
    ![Configuração do conjunto de dados do afundanço-3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-3.png)

   4. Volte para as definições de **Pia** na atividade **Copy** clicando no **separador IncrementalCopyPipeline.** 
   5. Expandir as propriedades do conjunto de dados e introduzir conteúdo dinâmico no valor do parâmetro triggerStart com a seguinte expressão:
     ```sql
     @pipeline().parameters.triggerStartTime
     ```
    ![Configuração do conjunto de dados do afundanço-4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-4.png)

6. Clique em Debug para testar o pipeline e certifique-se de que a estrutura da pasta e o ficheiro de saída são gerados como esperado. Faça o download e abra o ficheiro para verificar o conteúdo. 

    ![Cópia Incremental Debug-3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-3.png)
7. Certifique-se de que os parâmetros estão a ser injetados na consulta, revendo os parâmetros de entrada do gasoduto.

    ![Cópia Incremental Debug-4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-4.png)
8. Publique entidades (serviços ligados, conjuntos de dados e oleodutos) ao serviço Data Factory clicando no botão **Publicar todos.** Aguarde até ver a mensagem **Publicação com êxito** .
9. Por fim, configurar um gatilho da janela caindo para executar o gasoduto num intervalo regular e definir os parâmetros de início e fim de tempo. 
   1. Clique no botão **Adicionar gatilho** e selecione **Novo/Editar**

   ![Adicionar novo gatilho](./media/tutorial-incremental-copy-change-data-capture-feature-portal/add-trigger.png)

   2. Introduza um nome de gatilho e especifique uma hora de início, que é igual à hora final da janela de depurar acima.

   ![Acionador de Janela em Cascata](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger.png)

   3. No ecrã seguinte, especifique os seguintes valores para os parâmetros de arranque e de fim, respectivamente.
    ```sql
    @formatDateTime(trigger().outputs.windowStartTime,'yyyy-MM-dd HH:mm:ss.fff')
    @formatDateTime(trigger().outputs.windowEndTime,'yyyy-MM-dd HH:mm:ss.fff')
    ```

   ![Gatilho da janela de tropeçar-2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger-2.png)

> [!NOTE]
> Note que o gatilho só será executado uma vez que tenha sido publicado. Adicionalmente, o comportamento esperado da janela de tumbling é executar todos os intervalos históricos desde a data de início até agora. Mais informações sobre os gatilhos das janelas de tumbling podem ser encontradas [aqui.](./how-to-create-tumbling-window-trigger.md) 
  
10. Utilizando **o SQL Server Management Studio** escora algumas alterações adicionais na tabela de clientes executando o seguinte SQL:
    ```sql
    insert into customers (customer_id, first_name, last_name, email, city) values (4, 'Farlie', 'Hadigate', 'fhadigate3@zdnet.com', 'Reading');
    insert into customers (customer_id, first_name, last_name, email, city) values (5, 'Anet', 'MacColm', 'amaccolm4@yellowbook.com', 'Portsmouth');
    insert into customers (customer_id, first_name, last_name, email, city) values (6, 'Elonore', 'Bearham', 'ebearham5@ebay.co.uk', 'Portsmouth');
    update customers set first_name='Elon' where customer_id=6;
    delete from customers where customer_id=5;
    ```
11. Clique no botão **Publicar todos.** Aguarde até ver a mensagem **Publicação com êxito** .  
12. Após alguns minutos o oleoduto terá desencadeado e um novo ficheiro terá sido carregado para o Azure Storage


### <a name="monitor-the-incremental-copy-pipeline"></a>Monitorizar o pipeline da cópia incremental
1. Clique no separador **Monitorizar** , no lado esquerdo. Verá a execução do pipeline na lista e o respetivo estado. Para atualizar a lista, clique em **Atualizar** . Paire perto do nome do oleoduto para aceder ao relatório de rerun e consumo.

    ![Execuções de pipeline](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-pipeline-runs.png)
2. Para visualizar as operações associadas à execução do gasoduto, clique no nome Pipeline. Se os dados alterados forem detetados, haverá três atividades, incluindo a atividade de cópia, caso contrário, haverá apenas duas entradas na lista. Para voltar à vista do gasoduto, clique na ligação **Todos os Pipelines** na parte superior.

    ![Execuções de atividade](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-activity-runs.png)


### <a name="review-the-results"></a>Rever os resultados
Vai ver um segundo ficheiro na pasta `customers/incremental/YYYY/MM/DD` do contentor `raw`.

![Ficheiro de saída a partir da cópia incremental](media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-run.png)
 

## <a name="next-steps"></a>Passos seguintes
Avance para o seguinte tutorial para aprender sobre a cópia de novos ficheiros e alterados apenas com base no seu Último Anomodified:

> [!div class="nextstepaction"]
>[Copiar novos ficheiros por último anúncio modificado](tutorial-incremental-copy-lastmodified-copy-data-tool.md)