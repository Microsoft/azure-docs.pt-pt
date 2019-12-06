---
title: Sincronizar dados do Azure SQL Database Edge usando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre a sincronização de dados entre o Azure SQL Database Edge e o armazenamento de BLOBs do Azure
keywords: borda do banco de dados SQL, sincronização de data do SQL Database Edge, banco de dados SQL data factory
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: e6fd9e6431137708ba93328a8ed1359b93b4ee1f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851711"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-by-using-azure-data-factory"></a>Tutorial: sincronizar dados do SQL Database Edge para o armazenamento de BLOBs do Azure usando Azure Data Factory

Neste tutorial, você usará Azure Data Factory para sincronizar de forma incremental os dados para o armazenamento de BLOBs do Azure de uma tabela em uma instância do Edge do banco de dados SQL do Azure.

## <a name="before-you-begin"></a>Antes de começar

Se você ainda não criou um banco de dados ou uma tabela em sua implantação de borda do banco de dados SQL do Azure, use um destes métodos para criar um:

* Use [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) ou [Azure Data Studio](/sql/azure-data-studio/download/) para se conectar à borda do banco de dados SQL. Execute um script SQL para criar o banco de dados e a tabela.
* Crie um banco de dados SQL e uma tabela usando o [sqlcmd](/sql/tools/sqlcmd-utility/) conectando-se diretamente ao módulo de borda do banco de dados SQL. Para obter mais informações, consulte [conectar-se ao mecanismo de banco de dados usando sqlcmd](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* Use SqlPackage. exe para implantar um arquivo de pacote de DAC no contêiner de borda do banco de dados SQL. Você pode automatizar esse processo especificando o URI do arquivo SqlPackage como parte da configuração de propriedades desejadas do módulo. Você também pode usar diretamente a ferramenta de cliente SqlPackage. exe para implantar um pacote de DAC na borda do banco de dados SQL.

    Para obter informações sobre como baixar o SqlPackage. exe, consulte [baixar e instalar o SqlPackage](/sql/tools/sqlpackage-download/). A seguir estão alguns comandos de exemplo para SqlPackage. exe. Para obter mais informações, consulte a documentação do SqlPackage. exe.

    **Criar um pacote de DAC**

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name>
    ```

    **Aplicar um pacote de DAC**

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Criar uma tabela SQL e um procedimento para armazenar e atualizar os níveis de marca d' água

Uma tabela de marca d' água é usada para armazenar o último carimbo de data/hora até o qual os dados já foram sincronizados com o armazenamento do Azure. Um procedimento armazenado Transact-SQL (T-SQL) é usado para atualizar a tabela de marca d' água após cada sincronização.

Execute estes comandos na instância de borda do banco de dados SQL:

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

## <a name="create-a-data-factory-pipeline"></a>Criar um pipeline de Data Factory

Nesta seção, você criará um pipeline de Azure Data Factory para sincronizar dados com o armazenamento de BLOBs do Azure de uma tabela na borda do banco de dados SQL do Azure.

### <a name="create-a-data-factory-by-using-the-data-factory-ui"></a>Criar um data factory usando a interface do usuário do Data Factory

Crie um data factory seguindo as instruções neste [tutorial](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).

### <a name="create-a-data-factory-pipeline"></a>Criar um pipeline de Data Factory

1. Na página **de introdução da** interface do usuário do data Factory, selecione **criar pipeline**.

    ![Criar um pipeline de Data Factory](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. Na página **geral** da janela **Propriedades** do pipeline, digite **PeriodicSync** para o nome.

3. Adicione a atividade de pesquisa para obter o valor antigo da marca d' água. No painel **atividades** , expanda **geral** e arraste a atividade de **pesquisa** para a superfície do designer de pipeline. Altere o nome da atividade para **OldWatermark**.

    ![Adicionar a pesquisa de marca d' água antiga](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Alterne para a guia **configurações** e selecione **novo** para o **conjunto de fonte de origem**. Agora, você criará um conjunto de dados para representar a tabela de marcas d' água. Esta tabela contém o limite de tamanho antigo que foi utilizado na operação de cópia anterior.

5. Na janela **novo conjunto de novos DataSet** , selecione **Azure SQL Server**e, em seguida, selecione **continuar**.  

6. Na janela **definir propriedades** do conjunto de um, em **nome**, insira **WatermarkDataset**.

7. Para **serviço vinculado**, selecione **novo**e conclua estas etapas:

    1. Em **nome**, insira **SQLDBEdgeLinkedService**.

    2. Em **nome do servidor**, insira os detalhes do servidor de borda do banco de dados SQL.

    3. Selecione o **nome do banco de dados** na lista.

    4. Insira seu **nome de usuário** e **senha**.

    5. Para testar a conexão com a instância de borda do banco de dados SQL, selecione **testar conexão**.

    6. Selecione **Criar**.

    ![Criar um serviço ligado](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Selecione **OK**.

8. Na guia **configurações** , selecione **Editar**.

9. Na guia **conexão** , selecione **[dbo]. [ watermarktable]** para **tabela**. Se você quiser Visualizar dados na tabela, selecione **Visualizar dados**.

10. Alterne para o editor de pipeline selecionando a guia pipeline na parte superior ou selecionando o nome do pipeline no modo de exibição de árvore à esquerda. Na janela Propriedades da atividade de pesquisa, confirme se **WatermarkDataset** está selecionado na lista **conjunto de fontes de origem** .

11. No painel **atividades** , expanda **geral** e arraste outra atividade de **pesquisa** para a superfície do designer de pipeline. Defina o nome como **NewWatermark** na guia **geral** da janela Propriedades. Essa atividade de pesquisa Obtém o novo valor de marca d' água da tabela que contém os dados de origem para que ele possa ser copiado para o destino.

12. Na janela Propriedades da segunda atividade de pesquisa, alterne para a guia **configurações** e selecione **novo** para criar um conjunto de um para apontar para a tabela de origem que contém o novo valor de marca d' água.

13. Na janela **novo conjunto** de dados, selecione **instância de borda do banco de dados SQL**e, em seguida, selecione **continuar**.

    1. Na janela **definir propriedades** , em **nome**, insira **SourceDataset**. Em **serviço vinculado**, selecione **SQLDBEdgeLinkedService**.

    2. Em **tabela**, selecione a tabela que você deseja sincronizar. Você também pode especificar uma consulta para esse conjunto de um, conforme descrito posteriormente neste tutorial. A consulta tem precedência sobre a tabela que você especificar nesta etapa.

    3. Selecione **OK**.

14. Alterne para o editor de pipeline selecionando a guia pipeline na parte superior ou selecionando o nome do pipeline no modo de exibição de árvore à esquerda. Na janela Propriedades da atividade de pesquisa, confirme se **SourceDataset** está selecionado na lista **conjunto de fontes de origem** .

15. Selecione **consulta** em **usar consulta**. Atualize o nome da tabela na consulta a seguir e, em seguida, insira a consulta. Você está selecionando apenas o valor máximo de `timestamp` da tabela. Certifique-se de selecionar a **primeira linha apenas**.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![Selecionar consulta](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. No painel **atividades** , expanda **mover & transformar** e arraste a atividade de **cópia** do painel **atividades** para a superfície do designer. Defina o nome da atividade como **IncrementalCopy**.

17. Conecte ambas as atividades de pesquisa à atividade de cópia arrastando o botão verde anexado às atividades de pesquisa para a atividade de cópia. Solte o botão do mouse quando você vir a cor da borda da atividade de cópia mudar para azul.

18. Selecione a atividade de cópia e confirme que você vê as propriedades da atividade na janela **Propriedades** .

19. Alterne para a guia **origem** na janela **Propriedades** e conclua estas etapas:

    1. Na caixa **conjunto de fonte de origem** , selecione **SourceDataset**.

    2. Em **usar consulta**, selecione **consulta**.

    3. Insira a consulta SQL na caixa **consulta** . Veja um exemplo de consulta:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Na guia **coletor** , selecione **novo** em **conjunto de banco de coleta**.

21. Neste tutorial, o armazenamento de dados do coletor é um armazenamento de dados do Azure Blob Storage. Selecione **armazenamento de BLOBs do Azure**e, em seguida, selecione **continuar** na janela **novo conjunto de novas** .

22. Na janela **selecionar formato** , selecione o formato dos dados e, em seguida, selecione **continuar**.

23. Na janela **definir propriedades** , em **nome**, insira **SinkDataset**. Em **serviço vinculado**, selecione **novo**. Agora você criará uma conexão (um serviço vinculado) para o armazenamento de BLOBs do Azure.

24. Na janela **novo serviço vinculado (armazenamento de BLOBs do Azure)** , conclua estas etapas:

    1. Na caixa **nome** , digite **AzureStorageLinkedService**.

    2. Em **nome da conta de armazenamento**, selecione a conta de armazenamento do Azure para sua assinatura do Azure.

    3. Teste a conexão e selecione **concluir**.

25. Na janela **definir propriedades** , confirme se **AzureStorageLinkedService** está selecionado em **serviço vinculado**. Selecione **criar** e **OK**.

26. Na guia **coletor** , selecione **Editar**.

27. Vá para a guia **conexão** do SinkDataset e conclua estas etapas:

    1. Em **caminho do arquivo**, insira *asdedatasync/incrementalcopy*, em que *asdedatasync* é o nome do contêiner de BLOB e *incrementalcopy* é o nome da pasta. Crie o contêiner se ele não existir, ou use o nome de um existente. Azure Data Factory criará automaticamente a pasta de saída *incrementalcopy* se ela não existir. Também pode utilizar o botão **Procurar** do **Caminho do ficheiro** para navegar para uma pasta num contentor de blobs.

    2. Para a parte do **arquivo** do **caminho do arquivo**, selecione **adicionar conteúdo dinâmico [Alt + P]** e, em seguida, insira **@CONCAT(' incremental-', pipeline (). RunId, '. txt ')** na janela que é aberta. Selecione **Concluir**. O nome do arquivo é gerado dinamicamente pela expressão. Cada execução de pipeline tem um ID exclusivo. A atividade Copy utiliza o ID de execução para gerar o nome do ficheiro.

28. Alterne para o editor de pipeline selecionando a guia pipeline na parte superior ou selecionando o nome do pipeline no modo de exibição de árvore à esquerda.

29. No painel **atividades** , expanda **geral** e arraste a atividade **procedimento armazenado** do painel **atividades** para a superfície do designer de pipeline. Conecte a saída verde (com êxito) da atividade de cópia à atividade de procedimento armazenado.

30. Selecione **atividade de procedimento armazenado** no designer de pipeline e altere seu nome para **SPtoUpdateWatermarkActivity**.

31. Alterne para a guia **conta SQL** e selecione ***QLDBEdgeLinkedService** em **serviço vinculado**.

32. Alterne para a guia **procedimento armazenado** e conclua estas etapas:

    1. Em **nome do procedimento armazenado**, selecione **[dbo]. [ usp_write_watermark]** .

    2. Para especificar valores para os parâmetros de procedimento armazenado, selecione **importar parâmetro** e insira esses valores para os parâmetros:

    |Nome|Tipo|Valor|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@ {atividade (' NewWaterMark '). Output. firstRow. NewWatermarkvalue}|
    |TableName|String|@ {atividade (' OldWaterMark '). Output. firstRow. TableName}|

33. Para validar as configurações de pipeline, selecione **validar** na barra de ferramentas. Confirme que não há erros de validação. Para fechar a janela **relatório de validação de pipeline** , selecione **>>** .

34. Publique as entidades (serviços vinculados, conjuntos de valores e pipelines) no serviço Azure Data Factory selecionando o botão **publicar tudo** . Aguarde até que você veja uma mensagem confirmando que a operação de publicação foi bem-sucedida.

## <a name="trigger-a-pipeline-based-on-a-schedule"></a>Disparar um pipeline com base em uma agenda

1. Na barra de ferramentas do pipeline, selecione **Adicionar gatilho**, selecione **novo/editar**e, em seguida, selecione **novo**.

2. Nomeie o gatilho **HourlySync**. Em **tipo**, selecione **agenda**. Defina a **recorrência** para a cada 1 hora.

3. Selecione **OK**.

4. Selecione **Publicar Tudo**.

5. Selecione **disparar agora**.

6. Mude para o separador **Monitorizar**, no lado esquerdo. Pode ver o estado da execução do pipeline acionada pelo acionador manual. Selecione **Atualizar** para atualizar a lista.

## <a name="next-steps"></a>Passos seguintes

O pipeline de Azure Data Factory neste tutorial copia dados de uma tabela em uma instância de borda do banco do dados SQL para um local no armazenamento de BLOBs do Azure uma vez a cada hora. Para saber mais sobre como usar Data Factory em outros cenários, consulte estes [tutoriais](../data-factory/tutorial-copy-data-portal.md).
