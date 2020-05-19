---
title: Sync dados do Azure SQL Edge (Pré-visualização) utilizando a Azure Data Factory
description: Saiba mais sobre sincronização de dados entre o Armazenamento Azure SQL Edge (Preview) e o Azure Blob
keywords: SQL Edge,sync dados da SQL Edge, fábrica de dados SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 1238505a10214c315bd5f2ceb428cf097b3ef5c6
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599662"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-blob-storage-by-using-azure-data-factory"></a>Tutorial: Sincronizar dados do SQL Edge para o armazenamento da Blob Azure utilizando a Azure Data Factory

Neste tutorial, utilizará a Azure Data Factory para sincronizar gradualmente os dados do armazenamento da Azure Blob a partir de uma tabela num caso de Azure SQL Edge.

## <a name="before-you-begin"></a>Antes de começar

Se ainda não criou uma base de dados ou uma tabela na sua implementação Azure SQL Edge, utilize um destes métodos para criar um:

* Utilize o [Estúdio de Gestão de Servidores SQL](/sql/ssms/download-sql-server-management-studio-ssms/) ou [o Azure Data Studio](/sql/azure-data-studio/download/) para se ligar ao SQL Edge. Executar um script SQL para criar a base de dados e a tabela.
* Crie uma base de dados e uma tabela SQL utilizando [o SQLCMD](/sql/tools/sqlcmd-utility/) ligando-se diretamente ao módulo SQL Edge. Para mais informações, consulte [A Ligação ao Motor de Base de Dados utilizando sqlcmd](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* Utilize o SQLPackage.exe para implantar um ficheiro de pacote SQL Edge no recipiente SQL Edge. Pode automatizar este processo especificando o ficheiro SqlPackage URI como parte da configuração de propriedades desejadas do módulo. Também pode utilizar diretamente a ferramenta cliente SqlPackage.exe para implementar um pacote DAC para o SQL Edge.

    Para obter informações sobre como baixar SqlPackage.exe, consulte [Download e instale sqlpackage](/sql/tools/sqlpackage-download/). Seguem-se alguns comandos de amostra para SqlPackage.exe. Para mais informações, consulte a documentação SqlPackage.exe.

    **Criar um pacote DAC**

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name>
    ```

    **Aplicar um pacote DAC**

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Crie uma tabela E procedimento SQL para armazenar e atualizar os níveis de marca de água

Um quadro de marca de água é usado para armazenar a última marca de tempo até que os dados já foram sincronizados com o Armazenamento Azure. Um procedimento armazenado Transact-SQL (T-SQL) é utilizado para atualizar a tabela de marca de água após cada sincronização.

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
    SET [WatermarkValue] = @timestamp WHERE [TableName] = @TableName
    END
    Go
```

## <a name="create-a-data-factory-pipeline"></a>Criar um oleoduto data factory

Nesta secção, você vai criar um oleoduto Azure Data Factory para sincronizar dados para armazenamento Azure Blob a partir de uma mesa em Azure SQL Edge.

### <a name="create-a-data-factory-by-using-the-data-factory-ui"></a>Criar uma fábrica de dados utilizando a UI da Fábrica de Dados

Crie uma fábrica de dados seguindo as instruções [deste tutorial.](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)

### <a name="create-a-data-factory-pipeline"></a>Criar um oleoduto data factory

1. Na página **Let's start** da Data Factory UI, selecione **Create pipeline**.

    ![Criar um oleoduto data factory](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. Na página **Geral** da janela **Propriedades** para o gasoduto, introduza **periodicSync** para o nome.

3. Adicione a atividade de lookup para obter o valor de marca de água antiga. No painel **de Atividades,** expanda o **General** e arraste a atividade **de Lookup** para a superfície do designer de gasodutos. Mude o nome da atividade para **OldWatermark**.

    ![Adicione o velho lookup watermark](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Mude para o separador **Definições** e selecione **New** for **Source Dataset**. Agora vai criar um conjunto de dados para representar dados na tabela de marca de água. Esta tabela contém o limite de tamanho antigo que foi utilizado na operação de cópia anterior.

5. Na janela **New Dataset,** selecione **Azure SQL Server**, e, em seguida, selecione **Continuar**.  

6. Na janela de **propriedades definidas** para o conjunto de dados, em **nome,** introduza **WatermarkDataset**.

7. Para **o Linked Service,** selecione **New**, e, em seguida, complete estes passos:

    1. Sob **nome,** introduza **SQLDBEdgeLinkedService**.

    2. Sob **o nome do Servidor,** introduza os detalhes do servidor SQL Edge.

    3. Selecione o nome base de **dados** da lista.

    4. Introduza o **seu nome de utilizador** e **palavra-passe.**

    5. Para testar a ligação à instância SQL Edge, selecione a **ligação**de teste .

    6. Selecione **Criar**.

    ![Criar um serviço ligado](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Selecione **OK**.

8. No separador **Definições,** **selecione Editar**.

9. No separador **Ligação,** **selecione [dbo].[ watermarktable]** para **Tabela**. Se pretender visualizar os dados na tabela, selecione **dados de Pré-visualização**.

10. Mude para o editor do gasoduto selecionando o separador de gasoduto na parte superior ou selecionando o nome do gasoduto na vista da árvore à esquerda. Na janela de propriedades para a atividade de Lookup, confirme que **watermarkDataset** é selecionado na lista de **dados Source.**

11. No painel **de Atividades,** expanda **o General** e arraste outra atividade de **Lookup** para a superfície do designer de gasodutos. Desloque o nome para **NewWatermark** no separador **geral** da janela de propriedades. Esta atividade de procuração obtém o novo valor da marca de água da tabela que contém os dados de origem para que possa ser copiado para o destino.

12. Na janela de propriedades para a segunda atividade de Lookup, mude para o separador **Definições** e selecione **Novo** para criar um conjunto de dados para apontar para a tabela de origem que contém o novo valor da marca de água.

13. Na janela **New Dataset,** selecione **sQL Edge ,** e, em seguida, selecione **Continuar**.

    1. Na janela **'Propriedades definidas',** em **nome**, introduza **SourceDataset**. No **serviço Linked,** selecione **SQLDBEdgeLinkedService**.

    2. Em **Tabela**, selecione a tabela que pretende sincronizar. Também pode especificar uma consulta para este conjunto de dados, como descrito mais tarde neste tutorial. A consulta tem precedência sobre a tabela que especifica neste passo.

    3. Selecione **OK**.

14. Mude para o editor do gasoduto selecionando o separador de gasoduto na parte superior ou selecionando o nome do gasoduto na vista da árvore à esquerda. Na janela de propriedades para a atividade de Lookup, confirme que **o SourceDataset** é selecionado na lista de **dados Source.**

15. **Selecione Consulta** sob **consulta de utilização**. Atualize o nome da tabela na seguinte consulta e, em seguida, introduza a consulta. Está selendo apenas o valor máximo `timestamp` da tabela. Certifique-se de selecionar **apenas**a primeira linha .

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![selecionar consulta](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. No painel **de Atividades,** expanda **o Move & Transform** e arraste a atividade **copy** do painel **de Atividades** para a superfície do designer. Delineie o nome da atividade para **IncrementalCopy**.

17. Ligue ambas as atividades Lookup à atividade Copy ao arrastar o botão verde associado às atividades Lookup para a atividade Copy. Desbloqueie o botão do rato quando vir a cor da fronteira da atividade copy mudar para azul.

18. Selecione a atividade Copy e confirme que vê as propriedades da atividade na janela **Propriedades**.

19. Mude para o separador **Fonte** na janela **Propriedades** e complete estes passos:

    1. Na caixa source **dataset,** selecione **SourceDataset**.

    2. Sob **consulta de utilização,** selecione **Consulta**.

    3. Introduza a consulta SQL na caixa **de consulta.** Aqui está uma consulta de amostra:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. No **separador Sink,** selecione **New** under **Sink Dataset**.

21. Neste tutorial, a loja de dados do lavatório é uma loja de dados de armazenamento Azure Blob. Selecione **o armazenamento Azure Blob**e, em seguida, selecione **Continue** na nova janela **Dataset.**

22. Na janela **Select Format,** selecione o formato dos seus dados e, em seguida, selecione **Continuar**.

23. Na janela **set Properties,** sob **nome,** **introduza SinkDataset**. No **serviço Linked,** selecione **New**. Agora vai criar uma ligação (um serviço ligado) ao seu armazenamento Azure Blob.

24. Na janela **New Linked Service (armazenamento Azure Blob),** complete estes passos:

    1. Na caixa **Nome,** **introduza o AzureStorageLinkedService**.

    2. Sob **o nome da conta de armazenamento,** selecione a conta de armazenamento Azure para a sua subscrição Azure.

    3. Teste a ligação e, em seguida, selecione **Terminar**.

25. Na janela **set Properties,** confirme que **o AzureStorageLinkedService** é selecionado sob **o serviço Linked**. Selecione **Criar** e **OK**.

26. No **separador Sink,** selecione **Editar**.

27. Vá ao separador **de ligação** do SinkDataset e complete estes passos:

    1. No **caminho do Ficheiro,** introduza *asdedatasync/incrementalcopy,* onde *asdedatasync* é o nome do recipiente blob e *incrementalcopy* é o nome da pasta. Crie o recipiente se não existir, ou use o nome de um existente. A Azure Data Factory cria automaticamente a *incrementalcopy* da pasta de saída se não existir. Também pode utilizar o botão **Procurar** do **Caminho do ficheiro** para navegar para uma pasta num contentor de blobs.

    2. Para a parte **do Ficheiro** do caminho do **Ficheiro,** selecione **Adicionar conteúdo dinâmico [Alt+P]** e, em seguida, introduzir ** @CONCAT ('Incremental-', pipeline(). RunId, '.txt')** na janela que se abre. Selecione **Concluir**. O nome do ficheiro é gerado dinamicamente pela expressão. Cada execução de pipeline tem um ID exclusivo. A atividade Copy utiliza o ID de execução para gerar o nome do ficheiro.

28. Mude para o editor do gasoduto selecionando o separador de gasoduto na parte superior ou selecionando o nome do gasoduto na vista da árvore à esquerda.

29. No painel **de Atividades,** expandir a **Atividade Geral** e arrastar a atividade do **Procedimento Armazenado** do painel **de Atividades** para a superfície do pipeline designer. Ligue a saída verde (sucesso) da atividade Copy à atividade do Procedimento Armazenado.

30. Selecione **Atividade de Procedimento Armazenada** no designer de gasodutos e mude o seu nome para **SPtoUpdateWatermarkActivity**.

31. Mude para o separador **Conta SQL** e selecione ***QLDBEdgeLinkedService** sob **o serviço Linked**.

32. Mude para o separador **Procedimento Armazenado** e complete estes passos:

    1. Sob **o nome do procedimento armazenado,** selecione **[dbo].[ usp_write_watermark]**.

    2. Para especificar os valores dos parâmetros do procedimento armazenado, selecione parâmetro de **importação** e introduza estes valores para os parâmetros:

    |Name|Tipo|Valor|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}|
    |TableName|String|@{activity('OldWaterMark').output.firstRow.TableName}|

33. Para validar as definições do gasoduto, selecione **Validar** na barra de ferramentas. Confirme que não há erros de validação. Para fechar a janela do Relatório de Validação do **Gasoduto,** selecione **>>** .

34. Publique as entidades (serviços ligados, conjuntos de dados e oleodutos) ao serviço Azure Data Factory selecionando o botão **Publicar Todos.** Espere até ver uma mensagem confirmando que a operação de publicação foi bem sucedida.

## <a name="trigger-a-pipeline-based-on-a-schedule"></a>Desencadear um oleoduto com base num horário

1. Na barra de ferramentas do gasoduto, selecione **Adicionar Gatilho,** selecione **New/Edit**, e, em seguida, selecione **New**.

2. Nomeie o gatilho **HoramenteSync**. Em **Tipo,** selecione **Agenda**. Desloque a **Recorrência** a cada 1 hora.

3. Selecione **OK**.

4. Selecione **Publicar Tudo**.

5. Selecione **Gatilho agora**.

6. Mude para o separador **Monitorizar**, no lado esquerdo. Pode ver o estado da execução do pipeline acionada pelo acionador manual. Selecione **Atualizar** para atualizar a lista.

## <a name="next-steps"></a>Passos seguintes

O oleoduto Azure Data Factory neste tutorial copia dados de uma tabela sobre uma instância SQL Edge para uma localização no armazenamento Azure Blob uma vez por hora. Para aprender sobre a utilização da Fábrica de Dados noutros cenários, consulte estes [tutoriais.](../data-factory/tutorial-copy-data-portal.md)
