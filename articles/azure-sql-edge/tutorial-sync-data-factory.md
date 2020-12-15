---
title: Sincronizar dados do Azure SQL Edge utilizando a Azure Data Factory
description: Saiba mais sobre a sincronização de dados entre o armazenamento Azure SQL Edge e a Azure Blob
keywords: SQL Edge,sync dados da SQL Edge, sql edge data factory
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: b83201ae864d1f1eb9124af5268360bb1748f6c8
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507613"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-blob-storage-by-using-azure-data-factory"></a>Tutorial: Sincronizar dados do SQL Edge para o armazenamento do Azure Blob utilizando a Azure Data Factory

Neste tutorial, você usará Azure Data Factory para sincronizar gradualmente os dados para o armazenamento de Azure Blob a partir de uma tabela em um caso de Azure SQL Edge.

## <a name="before-you-begin"></a>Before you begin

Se ainda não criou uma base de dados ou tabela na sua implementação Azure SQL Edge, utilize um destes métodos para criar um:

* Utilize [o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) ou o [Azure Data Studio](/sql/azure-data-studio/download/) para ligar ao SQL Edge. Executar um script SQL para criar a base de dados e a tabela.
* Crie uma base de dados e uma tabela utilizando [o SQLCMD](/sql/tools/sqlcmd-utility/) ligando-se diretamente ao módulo SQL Edge. Para obter mais informações, consulte [Connect to the Database Engine utilizando sqlcmd](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* Utilize SQLPackage.exe para colocar um ficheiro de embalagem DAC no recipiente SQL Edge. Pode automatizar este processo especificando o ficheiro SqlPackage URI como parte da configuração de propriedades desejadas do módulo. Também pode utilizar diretamente a ferramenta SqlPackage.exe cliente para implantar um pacote DAC para SQL Edge.

    Para obter informações sobre como descarregar SqlPackage.exe, consulte [Download e instale sqlpackage.](/sql/tools/sqlpackage-download/) Seguem-se alguns comandos de amostra para SqlPackage.exe. Para mais informações, consulte a documentação SqlPackage.exe.

    **Criar um pacote DAC**

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name>
    ```

    **Aplicar um pacote DAC**

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Crie uma tabela e procedimento SQL para armazenar e atualizar os níveis de marca de água

Um quadro de marca de água é utilizado para armazenar o último carimbo de tempo até ao qual os dados já foram sincronizados com o Azure Storage. Um procedimento armazenado transact-SQL (T-SQL) é utilizado para atualizar o quadro de marca de água após cada sincronização.

Executar estes comandos na instância SQL Edge:

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp
    WHERE [TableName] = @TableName

    END
    Go
```

## <a name="create-a-data-factory-pipeline"></a>Criar um oleoduto de fábrica de dados

Nesta secção, irá criar um pipeline Azure Data Factory para sincronizar dados para o armazenamento da Azure Blob a partir de uma tabela em Azure SQL Edge.

### <a name="create-a-data-factory-by-using-the-data-factory-ui"></a>Criar uma fábrica de dados utilizando a UI da Fábrica de Dados

Crie uma fábrica de dados seguindo as instruções [deste tutorial.](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)

### <a name="create-a-data-factory-pipeline"></a>Criar um oleoduto de fábrica de dados

1. Na página **Let's get start** of the Data Factory UI, selecione **Create pipeline**.

    ![Criar um oleoduto de fábrica de dados](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. Na página **geral** da janela **Propriedades** para o pipeline, insira **periodicSync** para obter o nome.

3. Adicione a atividade de Lookup para obter o valor da marca de água antiga. No painel **de atividades,** expanda **a General** e arraste a atividade **de Lookup** para a superfície do designer de gasodutos. Mude o nome da atividade para **OldWatermark**.

    ![Adicione a velha procura de marca de água](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Mude para o **separador Definições** e selecione **Novo** para **conjunto de dados de origem**. Irá agora criar um conjunto de dados para representar dados na tabela de marca de água. Esta tabela contém o limite de tamanho antigo que foi utilizado na operação de cópia anterior.

5. Na janela **New Dataset,** selecione **O Servidor SQL Azure** e, em seguida, selecione **Continue**.  

6. Na janela **De propriedades definidas** para o conjunto de dados, em **Nome,** **introduza o WatermarkDataset**.

7. Para **Serviço Ligado**, selecione **New**, e, em seguida, complete estes passos:

    1. Under **Name**, insira **SQLDBEdgeLinkedService**.

    2. No **nome do Servidor,** insira os detalhes do servidor SQL Edge.

    3. Selecione o **nome da base de dados** na lista.

    4. Insira o **seu nome de utilizador** e **palavra-passe.**

    5. Para testar a ligação à instância SQL Edge, selecione **a ligação de teste**.

    6. Selecione **Criar**.

    ![Criar um serviço ligado](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Selecione **OK**.

8. No **separador Definições,** **selecione Editar**.

9. No separador **'Ligação',** selecione **[dbo].[ watermarktable]** para **a tabela**. Se pretender visualizar dados na tabela, selecione **dados de pré-visualização**.

10. Mude para o editor de gasoduto selecionando o separador de gasoduto na parte superior ou selecionando o nome do gasoduto na vista da árvore à esquerda. Na janela de propriedades para a atividade Lookup, confirme que o **WatermarkDataset** está selecionado na lista **de conjuntos de dados Source.**

11. No painel **de atividades,** expanda **a General** e arraste outra atividade **de Lookup** para a superfície do designer de gasodutos. Desapeie o nome no **NewWatermark** no separador **geral** da janela propriedades. Esta atividade de Lookup obtém o novo valor da marca de água a partir da tabela que contém os dados de origem para que possa ser copiado para o destino.

12. Na janela de propriedades para a segunda atividade de Lookup, mude para o **separador Definições** e selecione **Novo** para criar um conjunto de dados para apontar para a tabela de origem que contém o novo valor da marca de água.

13. Na janela **New Dataset,** selecione **sql edge e,** em seguida, selecione **Continue**.

    1. Na janela **'Definir propriedades',** em **Nome,** insira **o Conjunto de Dados de Origem**. No **serviço Linked**, selecione **SQLDBEdgeLinkedService**.

    2. EmBaixo **da tabela,** selecione a tabela que pretende sincronizar. Também pode especificar uma consulta para este conjunto de dados, como descrito mais tarde neste tutorial. A consulta tem precedência sobre a tabela que especifica neste passo.

    3. Selecione **OK**.

14. Mude para o editor de gasoduto selecionando o separador de gasoduto na parte superior ou selecionando o nome do gasoduto na vista da árvore à esquerda. Na janela de propriedades para a atividade Do Lookup, confirme que o **SourceDataset** está selecionado na lista **de conjuntos de dados Source.**

15. Selecione **Consulta** em **consulta de utilização**. Atualize o nome da tabela na seguinte consulta e, em seguida, insira a consulta. Está selecionando apenas o valor máximo `timestamp` da tabela. Certifique-se de selecionar **apenas** a primeira fila .

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![selecione consulta](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. No painel **de Atividades,** expanda **a move & Transforme** e arraste a atividade copy do painel de **Atividades** para a superfície do designer.  Desa ajuste o nome da atividade para **IncrementalCopy**.

17. Ligue ambas as atividades Lookup à atividade Copy ao arrastar o botão verde associado às atividades Lookup para a atividade Copy. Solte o botão do rato quando vir a cor da borda da atividade Copy mudar para azul.

18. Selecione a atividade Copy e confirme que vê as propriedades da atividade na janela **Propriedades**.

19. Mude para o **separador 'Fonte'** na janela **Propriedades** e complete estes passos:

    1. Na caixa **de conjunto de dados Source,** selecione **SourceDataset**.

    2. Em **consulta de utilização**, selecione **Consulta**.

    3. Introduza a consulta SQL na **caixa de consulta.** Aqui está uma consulta de amostra:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. No **separador Sink,** selecione **Novo** no **Conjunto de Dados de Sink**.

21. Neste tutorial, a loja de dados da pia é uma loja de dados de armazenamento Azure Blob. Selecione **o armazenamento de Azure Blob** e, em seguida, selecione **Continue** na janela Novo Conjunto **de Dados.**

22. Na janela **'Selecionar Formato',** selecione o formato dos seus dados e, em seguida, selecione **Continue**.

23. Na janela **'Definir propriedades',** em **Nome,** insira **o SinkDataset**. Ao **serviço Linked**, selecione **New**. Irá agora criar uma ligação (um serviço ligado) ao seu armazenamento Azure Blob.

24. Na janela **New Linked Service (Azure Blob storage),** complete estes passos:

    1. Na caixa **Nome,** **insira AzureStorageLinkedService**.

    2. No **nome da conta de armazenamento**, selecione a conta de armazenamento Azure para a sua subscrição Azure.

    3. Teste a ligação e, em seguida, **selecione Acabamento**.

25. Na janela **set Properties,** confirme que o **AzureStorageLinkedService** é selecionado sob **o serviço Linked**. Selecione **Criar** e **OK**.

26. No **separador Sink,** selecione **Editar**.

27. Vá ao separador **Ligação** do SinkDataset e complete estes passos:

    1. Na **trajetória do Ficheiro**, *introduza asdedatassync/incrementalcopia,* onde *asdedatasync* é o nome do recipiente blob e *incrementalcopia* é o nome da pasta. Crie o recipiente se não existir, ou use o nome de um existente. A Azure Data Factory cria automaticamente a pasta de saída *incrementalcopia* se não existir. Também pode utilizar o botão **Procurar** do **Caminho do ficheiro** para navegar para uma pasta num contentor de blobs.

    2. Para a parte **do Ficheiro** do **caminho 'Ficheiro',** selecione **Adicionar conteúdo dinâmico [Alt+P]** e, em seguida, introduza **@CONCAT ('Incremental-', pipeline(). RunId, '.txt')** na janela que se abre. Selecione **Concluir**. O nome do ficheiro é gerado dinamicamente pela expressão. Cada execução de pipeline tem um ID exclusivo. A atividade Copy utiliza o ID de execução para gerar o nome do ficheiro.

28. Mude para o editor de gasoduto selecionando o separador de gasoduto na parte superior ou selecionando o nome do gasoduto na vista da árvore à esquerda.

29. No painel **de Atividades,** expanda a **atividade Geral** e arraste a atividade do **Procedimento Armazenado** do painel **de Atividades** para a superfície do designer de gasodutos. Ligue a saída verde (sucesso) da atividade copy à atividade do Procedimento Armazenado.

30. Selecione **Atividade de Procedimento Armazenado** no designer de gasodutos e altere o seu nome para **SPtoUpdateWatermarkActivity**.

31. Mude para o separador **conta SQL** e selecione **_QLDBEdgeLinkedService_* sob **o serviço Linked**.

32. Mude para o **separador Procedimento Armazenado** e complete estes passos:

    1. Sob **o nome do procedimento armazenado,** selecione **[dbo].[ usp_write_watermark]**. .

    2. Para especificar os valores dos parâmetros de procedimento armazenados, selecione **o parâmetro de importação** e introduza estes valores para os parâmetros:

    |Nome|Tipo|Valor|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}|
    |TableName|String|@{activity('OldWaterMark').output.firstRow.TableName}|

33. Para validar as definições do pipeline, **selecione Validate** na barra de ferramentas. Confirme que não há erros de validação. Para fechar a janela **Pipeline Validation Report,** selecione **>>** .

34. Publique as entidades (serviços ligados, conjuntos de dados e oleodutos) ao serviço Azure Data Factory selecionando o botão **Publicar Tudo.** Aguarde até ver uma mensagem confirmando que a operação de publicação foi bem sucedida.

## <a name="trigger-a-pipeline-based-on-a-schedule"></a>Desencadear um oleoduto baseado num horário

1. Na barra de ferramentas do gasoduto, **selecione Add Trigger,** selecione **New/Edit**, e, em seguida, selecione **New**.

2. Diga o seu gatilho **HourlySync**. Sob **Tipo**, selecione **Agenda**. Desaprote **a Recorrência** a cada 1 hora.

3. Selecione **OK**.

4. Selecione **Publicar Tudo**.

5. Selecione **Trigger Now**.

6. Mude para o separador **Monitorizar**, no lado esquerdo. Pode ver o estado da execução do pipeline acionada pelo acionador manual. Selecione **Atualizar** para atualizar a lista.

## <a name="next-steps"></a>Passos seguintes

O oleoduto Azure Data Factory neste tutorial copia dados de uma tabela sobre uma instância SQL Edge para uma localização no armazenamento Azure Blob uma vez a cada hora. Para aprender a utilizar a Data Factory noutros cenários, consulte estes [tutoriais.](../data-factory/tutorial-copy-data-portal.md)
