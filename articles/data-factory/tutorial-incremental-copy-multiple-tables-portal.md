---
title: Copie incrementalmente várias tabelas usando o portal Azure
description: Neste tutorial, cria-se uma fábrica de dados Azure com um pipeline que carrega dados delta de várias tabelas numa base de dados do SQL Server para uma base de dados na Base de Dados Azure SQL.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 1fad6274b1dbbc4bf255caabd79352b3c836e352
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606708"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-a-database-in-azure-sql-database-using-the-azure-portal"></a>Carregue gradualmente os dados de várias tabelas no SQL Server para uma base de dados na Base de Dados Azure SQL utilizando o portal Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Neste tutorial, cria-se uma fábrica de dados Azure com um pipeline que carrega dados delta de várias tabelas numa base de dados do SQL Server para uma base de dados na Base de Dados Azure SQL.    

Vai executar os seguintes passos neste tutorial:

> [!div class="checklist"]
> * Prepare os arquivos de dados de origem e de destino.
> * Criar uma fábrica de dados.
> * Criar um integration runtime autoalojado.
> * Instalar o integration runtime. 
> * Criar serviços ligados. 
> * Crie conjuntos de dados de origem, de sink e de marca d'água.
> * Criar, executar e monitorizar um pipeline.
> * Reveja os resultados.
> * Adicionou ou atualizou os dados nas tabelas de origem.
> * Voltou a executar e a monitorizar o pipeline.
> * Reviu os resultados finais.

## <a name="overview"></a>Descrição Geral
Eis os passos importantes para criar esta solução: 

1. **Selecionar a coluna de limite de tamanho**.
    
    Selecione uma coluna por cada tabela no arquivo de dados de origem, que pode ser utilizada para identificar os registos novos ou atualizados para cada execução. Normalmente, os dados nesta coluna selecionada (por exemplo, last_modify_time ou ID) continuam a aumentar quando as linhas são criadas ou atualizadas. O valor máximo nesta coluna é utilizado como limite de tamanho.

1. **Preparar um arquivo de dados para armazenar o valor de limite de tamanho**.   
    
    Neste tutorial, vai armazenar o valor de marca d'água numa base de dados SQL.

1. **Criar um pipeline com as seguintes atividades:** 
    
    a. Criar uma atividade ForEach que itera através de uma lista de nomes de tabelas de origem que é transmitida como um parâmetro para o pipeline. Para cada tabela de origem, este invoca as seguintes atividades para efetuar o carregamento de diferenças para essa tabela.

    b. Criar duas atividades de pesquisa. Utilize a primeira atividade Lookup para obter o último valor de limite de tamanho. Utilize a segunda para obter o valor de limite de tamanho novo. Estes valores de limite de tamanho são transmitidos para a atividade Copy.

    c. Criar uma atividade Cópia que copia linhas do arquivo de dados de origem com o valor da coluna de limite de tamanho superior ao valor de limite de tamanho antigo e inferior ao valor novo. Em seguida, copia os dados delta do arquivo de dados de origem para o armazenamento de Blobs do Azure como um ficheiro novo.

    d. Crie uma atividade StoredProcedure, que atualiza o valor de marca d'água do pipeline que vai ser executado da próxima vez. 

    Eis o diagrama de nível elevado da solução: 

    ![Carregar dados de forma incremental](media/tutorial-incremental-copy-multiple-tables-portal/high-level-solution-diagram.png)


Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* **Sql Server**. Utiliza uma base de dados SQL Server como base de dados de origem neste tutorial. 
* **Base de Dados Azure SQL**. Utiliza uma base de dados na Base de Dados Azure SQL como loja de dados da pia. Se não tiver uma base de dados na Base de Dados SQL, consulte [criar uma base de dados na Base de Dados Azure SQL](../azure-sql/database/single-database-create-quickstart.md) para obter etapas para criar uma. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Criar tabelas de origem na base de dados do SQL Server

1. Abra o SQL Server Management Studio e ligue-se à base de dados do SQL Server.

1. No **Explorador de Servidores**, clique com botão direito do rato na base de dados e escolha **Nova Consulta**.

1. Execute o seguinte comando SQL na base de dados para criar tabelas com o nome `customer_table` e `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-database"></a>Crie tabelas de destino na sua base de dados

1. Abra o SQL Server Management Studio e ligue-se à sua base de dados na Base de Dados Azure SQL.

1. No **Explorador de Servidores**, clique com botão direito do rato na base de dados e escolha **Nova Consulta**.

1. Execute o seguinte comando SQL na base de dados para criar tabelas com o nome `customer_table` e `project_table`:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-your-database-to-store-the-high-watermark-value"></a>Crie outra tabela na sua base de dados para armazenar o elevado valor da marca de água

1. Executar o seguinte comando SQL contra a sua base de dados para criar uma tabela com o nome `watermarktable` para armazenar o valor da marca de água: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. Inserir valores de marca d'água iniciais para ambas as tabelas de origem na tabela de marca d'água.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-your-database"></a>Crie um procedimento armazenado na sua base de dados

Execute o seguinte comando para criar um procedimento armazenado na sua base de dados. Este procedimento armazenado atualiza o valor de limite de tamanho após cada execução de pipeline. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-your-database"></a>Crie tipos de dados e procedimentos adicionais armazenados na sua base de dados

Execute a seguinte consulta para criar dois procedimentos armazenados e dois tipos de dados na sua base de dados. São utilizados para intercalar os dados das tabelas de origem nas tabelas de destino.

Para facilitar a viagem, utilizamos diretamente estes Procedimentos Armazenados passando os dados delta através de uma variável de tabela e, em seguida, fundimo-los em loja de destino. Tenha cuidado por não esperar que um número "grande" de linhas delta (mais de 100) seja armazenado na variável de tabela.  

Se precisa de fundir um grande número de linhas delta na loja de destino, sugerimos que utilize a atividade de cópia para copiar todos os dados delta numa tabela temporária de "staging" na loja de destino, e depois construiu o seu próprio procedimento armazenado sem usar a variável de mesa para os fundir da mesa de "encenação" para a tabela "final". 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
2. No menu esquerdo, **selecione Criar uma** Fábrica de  >  Dados de **Integração de** Recursos  >  : 
   
   ![Seleção do Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Na página **Nova fábrica de dados**, introduza **ADFMultiIncCopyTutorialDF** em **nome**. 
 
   O nome da fábrica de dados Azure deve ser **globalmente único.** Se vir um ponto de exclamação vermelho com o seguinte erro, altere o nome da fábrica de dados (por exemplo, oseunomeADFIncCopyTutorialDF) e tente criá-la novamente. Veja o artigo [Data Factory – Naming Rules](naming-rules.md) (Data Factory – Regras de Nomenclatura) para obter as regras de nomenclatura dos artefactos do Data Factory.
  
   `Data factory name "ADFIncCopyTutorialDF" is not available`

4. Selecione a sua subscrição do **Azure** na qual pretende criar a fábrica de dados. 
5. No **Grupo de Recursos**, siga um destes passos:
     
    - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente. 
    - Selecione **Criar novo** e introduza o nome de um grupo de recursos.   
    Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).  
6. Selecione **V2** para a **versão**.
7. Selecione a **localização** da fábrica de dados. Só aparecem na lista pendente as localizações que são suportadas. Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.
8. Clique em **Criar**.      
9. Depois de concluída a criação, vai ver a página **Data Factory**, conforme mostrado na imagem.
   
    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Página inicial da Fábrica de Dados Azure, com o azulejo do Monitor de autores &.":::
10. Clique no mosaico **Criar e Monitorizar** para iniciar a interface de utilizador (IU) do Azure Data Factory num separador à parte.

## <a name="create-self-hosted-integration-runtime"></a>Criar o runtime de integração autoalojado
Como está a mover dados de um arquivo de dados numa rede privada (no local) para um arquivo de dados do Azure, instale um runtime de integração autoalojado (IR) no seu ambiente no local. O IR autoalojado move os dados entre a sua rede privada e o Azure. 

1. Na página **Let's get start** of Azure Data Factory UI, selecione o [separador Gerir](./author-management-hub.md) a partir do painel mais à esquerda.

   ![O botão de gerir a página inicial](media/doc-common-process/get-started-page-manage-button.png)

1. Selecione **os tempos de integração** no painel esquerdo e, em seguida, selecione **+New**.

   ![Criar um integration runtime](media/doc-common-process/manage-new-integration-runtime.png)

1. Na janela de configuração do tempo de execução de **integração,** selecione **Executar atividades de movimento de dados e de despacho para computaçãos externas,** e clique em **Continuar**. 

1. Selecione **Self-Hosted** e clique **em Continuar**. 
1. Insira **MySelfHostedIR** para **nome**, e clique em **Criar**. 

1. Clique em **Clique aqui para iniciar a configuração rápida neste computador**, na secção **Opção 1: Configuração Rápida**. 

   ![Clicar na ligação de Configuração Rápida](./media/tutorial-incremental-copy-multiple-tables-portal/click-express-setup.png)
1. Na janela **Configuração Rápida do Integration Runtime (Autoalojado)**, clique em **Fechar**. 

   ![Configuração do runtime de integração - êxito](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
1. No browser, na janela **Configuração do Runtime de Integração**, clique em **Concluir**. 

 
1. Confirme que vê **MySelfHostedIR** na lista de runtimes de integração.

## <a name="create-linked-services"></a>Criar serviços ligados
Os serviços ligados são criados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Nesta secção, crie serviços ligados à base de dados do SQL Server e à sua base de dados na Base de Dados Azure SQL. 

### <a name="create-the-sql-server-linked-service"></a>Criar o serviço ligado do SQL Server
Neste passo, ligue a sua base de dados SQL Server à fábrica de dados.

1. Na janela **Ligações**, mude do separador **Runtimes de Integração** para o separador **Serviços Ligados** e clique em **+ Novo**.

    :::image type="content" source="./media/doc-common-process/new-linked-service.png" alt-text="Novo serviço ligado.":::
1. Na janela **Novo Serviço Ligado**, selecione **SQL Server** e clique em **Continuar**. 

1. Na janela **Novo Serviço Ligado**, siga os passos abaixo:

    1. Introduza **SqlServerLinkedService** em **Nome**. 
    1. Selecione **MySelfHostedIR** em **Ligar através do runtime de integração**. Este é um passo **importante**. O runtime de integração predefinido não consegue ligar a um arquivo de dados no local. Utilize o runtime de integração autoalojado que criou anteriormente. 
    1. Em **Nome do servidor**, introduza o nome do computador que tem a base de dados do SQL Server.
    1. Em **Nome da base de dados**, introduza o nome da base de dados no SQL Server que tem a origem de dados. Criou uma tabela e inseriu dados nesta base de dados como parte dos pré-requisitos. 
    1. Em **Tipo de autenticação**, selecione o **tipo de autenticação** que pretende utilizar para ligar à base de dados. 
    1. Em **Nome do utilizador**, introduza o nome do utilizador que tem acesso à base de dados do SQL Server. Se precisar de utilizar um caráter de barra invertida (`\`) no nome da sua conta de utilizador ou no nome do seu servidor, utilize o caráter de escape (`\`). Um exemplo é `mydomain\\myuser`.
    1. Em **Palavra-passe**,introduza a **palavra-passe** do utilizador. 
    1. Para testar se o Data Factory consegue ligar à base de dados do SQL Server, clique em **Testar ligação**. Corrija os erros até que a ligação seja bem-sucedida. 
    1. Para guardar o serviço ligado, clique em **Terminar**.

### <a name="create-the-azure-sql-database-linked-service"></a>Criar o serviço ligado da Base de Dados SQL do Azure
Neste último passo, vai criar um serviço ligado para ligar a sua base de dados do SQL Server à fábrica de dados. Neste passo, ligue a sua base de dados de destino/pia à fábrica de dados. 

1. Na janela **Ligações**, mude do separador **Runtimes de Integração** para o separador **Serviços Ligados** e clique em **+ Novo**.
1. Na janela **Novo Serviço Ligado**, selecione **Base de Dados SQL do Azure** e clique em **Continuar**. 
1. Na janela **Novo Serviço Ligado**, siga os passos abaixo:

    1. Introduza **AzureSqlDatabaseLinkedService** em **Nome**. 
    1. Para **o nome do Servidor,** selecione o nome do seu servidor na lista de drop-down. 
    1. Para **o nome da Base de Dados**, selecione a base de dados na qual criou customer_table e project_table como parte dos pré-requisitos. 
    1. Para **o nome de utilizador,** insira o nome do utilizador que tenha acesso à base de dados. 
    1. Em **Palavra-passe**,introduza a **palavra-passe** do utilizador. 
    1. Para testar se o Data Factory consegue ligar à base de dados do SQL Server, clique em **Testar ligação**. Corrija os erros até que a ligação seja bem-sucedida. 
    1. Para guardar o serviço ligado, clique em **Terminar**.

1. Verifique se vê dois serviços ligados na lista. 
   
    ![Dois serviços ligados](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, vai criar conjuntos de dados para representar a origem de dados, o destino de dados e o local para armazenar o limite de tamanho.

### <a name="create-a-source-dataset"></a>Criar um conjunto de dados de origem

1. No painel esquerdo, clique em **+ (mais)** e clique em **Conjunto de Dados**.

1. Na janela **New Dataset,** selecione **SQL Server,** clique **em Continuar**. 

1. Verá um novo separador aberto no browser para configurar o conjunto de dados. Também se vê um conjunto de dados na vista da árvore. No separador **Geral** da janela Propriedades ao fundo, introduza **SourceDataset** em **Nome**. 

1. Mude para o separador **Ligação**, na janela Propriedades e clique em **SqlServerLinkedService** em **Serviço ligado**. Não selecione uma tabela aqui. A atividade Cópia no pipeline utiliza uma consulta SQL para carregar os dados em vez de carregar a tabela inteira.

   ![Conjunto de dados de origem - ligação](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>Criar um conjunto de dados de sink
1. No painel esquerdo, clique em **+ (mais)** e clique em **Conjunto de Dados**.

1. Na janela **New Dataset,** selecione **Azure SQL Database** e clique em **Continuar**. 

1. Verá um novo separador aberto no browser para configurar o conjunto de dados. Também se vê um conjunto de dados na vista da árvore. No separador **Geral** da janela Propriedades ao fundo, introduza **SinkDataset** em **Nome**.

1. Mude para o separador **Parâmetros** da janela Propriedades, e siga os seguintes abaixo: 

    1. Clique em **Novo** na secção **Criar/atualizar parâmetros**. 
    1. Introduza **SinkTableName** em **nome** e **Cadeia** em **tipo**. Este conjunto de dados assume **SinkTableName** como um parâmetro. O parâmetro SinkTableName é definido pelo pipeline dinamicamente durante o runtime. A atividade ForEach no pipeline itera através de uma lista de nomes de tabelas e transmite o nome da tabela para este conjunto de dados em cada iteração.
   
        ![Conjunto de Dados de Sink - propriedades](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)
1. Mude para o **separador 'Ligação'** na janela Propriedades e selecione **AzureSqlDatabaseLinkedService** para **o serviço Linked**. Na propriedade **Tabela**, clique em **Adicionar conteúdo dinâmico**.   
    
1. Na janela **Add Dynamic Content,** selecione **SinkTableName** na secção **Parâmetros.** 
 
1. Depois de clicar em **Terminar**, você vê " @dataset (). SinkTableName " como o nome da mesa.

   ![Conjunto de Dados de Sink - ligação](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-completion.png)

### <a name="create-a-dataset-for-a-watermark"></a>Criar um conjunto de dados para uma marca d'água
Neste passo, vai criar um conjunto de dados para armazenar um valor de limite superior de tamanho. 

1. No painel esquerdo, clique em **+ (mais)** e clique em **Conjunto de Dados**.

1. Na janela **New Dataset,** selecione **Azure SQL Database** e clique em **Continuar**. 

1. No separador **Geral** da janela Propriedades ao fundo, introduza **WatermarkDataset** em **Nome**.
1. Mude para o separador **Ligação** e faça os seguintes passos: 

    1. Selecione **AzureSqlDatabaseLinkedService** em **Serviço ligado**.
    1. Selecione **[dbo].[watermarktable]** em **Tabela**.

        ![Conjunto de Dados de Marca d'água - ligação](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>Criar um pipeline
O pipeline aceita uma lista de nomes de tabela como parâmetro. A atividade ForEach itera através da lista de nomes de tabelas e realiza as seguintes operações: 

1. Utilize a atividade Pesquisa para obter o valor do limite de tamanho antigo (o valor inicial ou o que foi utilizado na última iteração).

1. Utilize a atividade Pesquisa para obter o valor do limite de tamanho novo (o valor máximo da coluna de limites de tamanho na tabela de origem).

1. Utilize a atividade Cópia para copiar dados entre estes dois valores de limite de tamanho da base de dados de origem para a base de dados de destino.

1. Utilize a atividade StoredProcedure para atualizar o valor de limite de tamanho antigo a ser utilizado no primeiro passo da iteração seguinte. 

### <a name="create-the-pipeline"></a>Criar o pipeline

1. No painel do lado esquerdo, clique em **+ (mais)** e clique em **Pipeline**.

1. No painel geral em **Propriedades**, especifique **incrementalCopyPipeline** para **nome**. Em seguida, desabar o painel clicando no ícone Propriedades no canto superior direito.  

1. No **separador Parâmetros,** faça os seguintes passos: 

    1. Clique em **+ Novo**. 
    1. Introduza **tableList** no parâmetro **Nome**. 
    1. Selecione **Matriz** para o **tipo** de parâmetro .

1. Na caixa de ferramentas **Atividades**, expanda **Iteração e Condições** e arraste e largue a atividade **ForEach** na superfície de estruturador do pipeline. No separador **Geral** da janela **Propriedades**, introduza **IterateSQLTables**. 

1. Mude para o separador **Definições** e introduza `@pipeline().parameters.tableList` em **itens**. A atividade ForEach itera através da lista de tabelas e realiza a operação de cópia incremental. 

    ![Atividade ForEach - definições](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)

1. Selecione a atividade **ForEach** no pipeline, se ainda não estiver selecionada. Clique no botão **Editar (ícone de lápis).**

1. Na caixa de ferramentas **Atividades**, expanda **Geral**, arraste e largue a atividade **Lookup** na superfície de estruturador do pipeline e introduza **LookupOldWaterMarkActivity** em **Nome**.

1. Mude para o separador **Definições** da janela **Propriedades** e siga os passos seguintes: 

    1. Selecione **WatermarkDataset** em **Conjunto de Dados de Origem**.
    1. Selecione **Consulta** em **Utilize Consulta**. 
    1. Introduza a seguinte consulta SQL em **consulta**. 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![Primeira Atividade Pesquisar - definições](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
1. Arraste e largue a atividade **Pesquisar** da caixa de ferramentas **Atividades** e introduza **LookupNewWaterMarkActivity** em **Nome**.
        
1. Mudar para o separador **Definições**.

    1. Selecione **SourceDataset** em **Conjunto de Dados de Origem**. 
    1. Selecione **Consulta** em **Utilize Consulta**.
    1. Introduza a seguinte consulta SQL em **consulta**.

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![Segunda Atividade Pesquisar - definições](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
1. Arraste e largue a atividade **Copiar** da caixa de ferramentas **Atividades** e introduza **IncrementalCopyActivity** em **Nome**. 

1. Ligue as atividades **Pesquisar** à atividade **Copiar**, uma a uma. Para ligar, comece a arrastar a caixa **verde** anexada à atividade **Pesquisar** e largue-a na atividade **Copiar**. Largue o botão do rato quando a cor do limite da atividade Copiar mudar para **azul**.

    ![Ligar atividades Pesquisar à atividade Copiar](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
1. Selecione a atividade **Copiar** no pipeline. Mude para o separador **Origem** na janela **Propriedades**. 

    1. Selecione **SourceDataset** em **Conjunto de Dados de Origem**. 
    1. Selecione **Consulta** em **Utilize Consulta**. 
    1. Introduza a seguinte consulta SQL em **consulta**.

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![Atividade Copy - definições da origem](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
1. Mude para o separador **Sink** e selecione **SinkDataset** em **Conjunto de Dados de Sink**. 
        
1. Faça os seguintes passos:

    1. Nas propriedades do **Dataset**, para o parâmetro **SinkTableName,** insira `@{item().TABLE_NAME}` .
    1. Para **propriedade nome de procedimento armazenado,** insira `@{item().StoredProcedureNameForMergeOperation}` .
    1. Para a propriedade **tipo tabela,** insira `@{item().TableType}` .
    1. Para **o nome do parâmetro do tipo de** tabela, insira `@{item().TABLE_NAME}` .

        ![Atividade Copiar - parâmetros](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
1. Arraste e largue a atividade **Stored Procedure** da caixa de ferramentas **Atividades** para a superfície de desenho do pipeline. Ligue a atividade **copy** à atividade **do Procedimento Armazenado.** 

1. Selecione a atividade **Procedimento Armazenado** no pipeline e introduza **StoredProceduretoWriteWatermarkActivity** em **Nome**, no separador **Geral** da janela **Propriedades**. 

1. Mude para o separador **conta SQL** e selecione **AzureSqlDatabaseLinkedService** para **Serviço Ligado**.

    ![Atividade Stored Procedure - Conta do SQL](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
1. Mude para o separador **Procedimento Armazenado** e siga os passos abaixo:

    1. Para **Nome do procedimento armazenado**, selecione `[dbo].[usp_write_watermark]`. 
    1. Selecione **Parâmetro de importação**. 
    1. Especifique os seguintes valores para os parâmetros: 

        | Nome | Tipo | Valor | 
        | ---- | ---- | ----- |
        | LastModifiedtime | DateTime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | String | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![Atividade de procedimento armazenado - definições do procedimento armazenado](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
1. **Selecione Publicar Tudo** para publicar as entidades que criou para o serviço Data Factory. 

1. Aguarde até ver a mensagem **Publicação com êxito**. Para ver as notificações, clique na ligação **Mostrar Notificações**. Clique em **X** para fechar a janela de notificações.

 
## <a name="run-the-pipeline"></a>Executar o pipeline

1. Na barra de ferramentas para o pipeline, clique **em Adicionar gatilho** e clique em Trigger **Now**.     

1. Na janela **Execução de Pipeline**, introduza o seguinte valor no parâmetro **tableList** e clique em **Concluir**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

    ![Argumentos de Execução de Pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

1. Mude para o separador **Monitorizar**, no lado esquerdo. Irá ver o estado da execução do pipeline acionada pelo **acionador manual**. Pode utilizar links sob a coluna **PIPELINE NAME** para visualizar detalhes da atividade e para refazer o pipeline.

1. Para ver as atividades ligadas à execução do gasoduto, selecione a ligação sob a coluna **PIPELINE NAME.** Para mais detalhes sobre a atividade, selecione o link **Details** (ícone de óculos) sob a coluna **ACTIVITY NAME.** 

1. Selecione **Todos os gasodutos correm** na parte superior para voltar à vista Pipeline Runs. Para atualizar a vista, selecione **Atualizar**.


## <a name="review-the-results"></a>Rever os resultados
No SQL Server Management Studio, execute as seguintes consultas na base de dados SQL de destino para verificar que os dados foram copiados das tabelas de origem para as tabelas de destino. 

**Query** 
```sql
select * from customer_table
```

**Saída**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Query**

```sql
select * from project_table
```

**Saída**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Query**

```sql
select * from watermarktable
```

**Saída**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Tenha em atenção que os valores de limite de tamanho de ambas as tabelas foram atualizados. 

## <a name="add-more-data-to-the-source-tables"></a>Adicione mais dados às tabelas de origem

Execute a seguinte consulta na base de dados do SQL Server de origem para atualizar uma linha existente em customer_table. Insira uma linha nova em project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Volte a executar o pipeline
1. Na janela do browser, mude para o separador **Editar** no lado esquerdo. 
1. Na barra de ferramentas para o pipeline, clique **em Adicionar gatilho** e clique em Trigger **Now**.   
1. Na janela **Execução de Pipeline**, introduza o seguinte valor no parâmetro **tableList** e clique em **Concluir**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline-again"></a>Monitorizar o pipeline novamente

1. Mude para o separador **Monitorizar**, no lado esquerdo. Irá ver o estado da execução do pipeline acionada pelo **acionador manual**. Pode utilizar links sob a coluna **PIPELINE NAME** para visualizar detalhes da atividade e para refazer o pipeline.

1. Para ver as atividades ligadas à execução do gasoduto, selecione a ligação sob a coluna **PIPELINE NAME.** Para mais detalhes sobre a atividade, selecione o link **Details** (ícone de óculos) sob a coluna **ACTIVITY NAME.** 

1. Selecione **Todos os gasodutos correm** na parte superior para voltar à vista Pipeline Runs. Para atualizar a vista, selecione **Atualizar**.

## <a name="review-the-final-results"></a>Rever os resultados finais
No SQL Server Management Studio, execute as seguintes consultas com a base de dados SQL alvo para verificar se os dados atualizados/novos foram copiados das tabelas de origem para as tabelas de destino. 

**Query** 
```sql
select * from customer_table
```

**Saída**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Repare nos valores novos de **Name** e **LastModifytime** para **PersonID** relativamente ao número 3. 

**Query**

```sql
select * from project_table
```

**Saída**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Repare que a entrada **NewProject** foi adicionada a project_table. 

**Query**

```sql
select * from watermarktable
```

**Saída**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Tenha em atenção que os valores de limite de tamanho de ambas as tabelas foram atualizados.
     
## <a name="next-steps"></a>Passos seguintes
Neste tutorial, executou os passos seguintes: 

> [!div class="checklist"]
> * Prepare os arquivos de dados de origem e de destino.
> * Criar uma fábrica de dados.
> * Criou um integration runtime autoalojado (IR).
> * Instalar o integration runtime.
> * Criar serviços ligados. 
> * Crie conjuntos de dados de origem, de sink e de marca d'água.
> * Criar, executar e monitorizar um pipeline.
> * Reveja os resultados.
> * Adicionou ou atualizou os dados nas tabelas de origem.
> * Voltou a executar e a monitorizar o pipeline.
> * Reviu os resultados finais.

Avance para o tutorial seguinte para saber como transformar dados através de um cluster do Spark no Azure:

> [!div class="nextstepaction"]
>[Carregar dados de forma incremental da Base de Dados SQL do Azure para o armazenamento de Blobs do Azure com a tecnologia de Controlo de Alterações](tutorial-incremental-copy-change-tracking-feature-portal.md)