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
ms.openlocfilehash: 0e75da9516303bb4250b6847a4b381d07b3d7dad
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501324"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-using-azure-data-factory"></a>Tutorial: sincronizar dados do SQL Database Edge para o armazenamento de BLOBs do Azure usando Azure Data Factory

Neste tutorial, você usa Azure Data Factory para sincronizar de forma incremental os dados de uma tabela em uma instância do Azure SQL Database Edge para o armazenamento de BLOBs do Azure.

## <a name="before-you-begin"></a>Antes de começar

Se você ainda não criou um banco de dados ou uma tabela em sua implantação de borda do banco de dados SQL do Azure, use um dos seguintes métodos para criar um:

* Use [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) ou [Azure Data Studio](/sql/azure-data-studio/download/) para se conectar à borda do banco de dados SQL e executar um script SQL para criar o banco de dados e a tabela.
* Crie um banco de dados SQL e uma tabela usando [sqlcmd](/sql/tools/sqlcmd-utility/) conectando-se diretamente ao módulo de borda do banco de dados SQL. Para obter mais informações, consulte [conectar-se ao mecanismo de banco de dados usando o sqlcmd](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* Use SqlPackage. exe para implantar um arquivo dacpac no contêiner de borda do banco de dados SQL. Isso pode ser automatizado especificando o URI do arquivo SqlPackage como parte da configuração de propriedades desejadas dos módulos ou usando diretamente a ferramenta de cliente SqlPackage. exe para implantar um dacpac na borda do banco de dados SQL.

    Para baixar o SqlPackage, consulte [baixar e instalar o SqlPackage](/sql/tools/sqlpackage-download/). Os comandos de exemplo a seguir para SqlPackage. exe são fornecidos, mas verifique a documentação do SqlPackage para obter mais informações.

    **Criar dacpac**:

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name> 
    ```

    **Aplicar dacpac**:

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Criar uma tabela SQL e um procedimento para armazenar e atualizar os níveis de marca d' água

A tabela de marca d' água é usada para armazenar o último carimbo de data/hora até o qual os dados já foram sincronizados com o armazenamento do Azure. O procedimento armazenado Transact-SQL (T-SQL) é usado para atualizar a tabela de marca d' água após cada sincronização. 

Execute os seguintes comandos na instância de borda do banco de dados SQL:

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

## <a name="create-a-data-factory-workflow"></a>Criar um Data Factory fluxo de trabalho

Nesta seção, você criará um pipeline de Azure Data Factory para sincronizar dados de uma tabela no Azure SQL Database Edge para o armazenamento de BLOBs do Azure.

### <a name="create-data-factory-using-the-data-factory-ui"></a>Criar Data Factory usando a interface do usuário do Data Factory

Crie um Data Factory usando as instruções neste [tutorial](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).

### <a name="create-a-data-factory-pipeline"></a>Criar um pipeline de Data Factory

1. Na página **introdução** da interface do usuário do data Factory, selecione o bloco **criar pipeline** .

    ![Pipeline de Data Factory de criação](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. Na página **geral** da janela **Propriedades** do pipeline, insira o nome do **PeriodicSync** .

3. Adicione a atividade de **pesquisa** para obter o valor antigo da marca d' água. Na **caixa de ferramentas atividades**, expanda **geral**, arraste & descartar a atividade de **pesquisa** para a superfície do designer de pipeline. Altere o nome da atividade para *OldWatermark*.

    ![pesquisa de marca d' água antiga](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Alterne para a guia **configurações** e selecione **+ novo** para o **conjunto de fonte de código**. Nesta etapa, você criará um conjunto de dados para representar a marca d' água. Esta tabela contém o limite de tamanho antigo que foi utilizado na operação de cópia anterior.

5. Na janela **novo conjunto de novos DataSet** , selecione **Azure SQL Server**e selecione **continuar**.  

6. Na janela **definir propriedades** do conjunto de um, digite *WatermarkDataset* para o nome.

7. Para **serviço vinculado**, selecione **novo**e, em seguida, execute as seguintes etapas:

    1. Digite *SQLDBEdgeLinkedService* para o **nome**.

    2. Insira os detalhes do servidor de borda do banco de dados SQL para o **nome do servidor**.

    3. Insira o **nome do banco de dados** na lista suspensa.

    4. Insira seu **nome de usuário** e **senha**.

    5. Para testar a conexão com a instância de borda do banco de dados SQL, selecione **testar conexão**.

    6. Selecione **Criar**.

    ![Criar serviço vinculado](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Selecione **OK**

8. Na guia **configurações** , selecione **Editar**.

9. Na guia **conexão** , selecione *[dbo]. [ watermarktable]* para **tabela**. Se você quiser Visualizar dados na tabela, selecione **Visualizar dados**.

10. Alterne para o editor de pipeline selecionando a guia pipeline na parte superior ou selecionando o nome do pipeline no modo de exibição de árvore à esquerda. Na janela Propriedades da atividade de **pesquisa**, confirme se **WatermarkDataset** está selecionado para o campo **conjunto de fonte de origem** .

11. Na caixa de ferramentas **atividades** , expanda **geral**, arraste e solte outra atividade de **pesquisa** para a superfície do designer de pipeline e defina o nome como **NewWatermark** na guia **geral** da janela Propriedades. Esta atividade Lookup obtém o valor de limite de tamanho antigo da tabela com a origem de dados que vai ser copiada para o destino.

12. Na janela Propriedades da segunda atividade de **pesquisa** , alterne para a guia **configurações** e selecione **novo** para criar um conjunto de um para apontar para a tabela de origem que contém o novo valor de marca d' água.

13. Na janela **novo conjunto** de dados, selecione instância de borda do banco de dados SQL e selecione **continuar**.

    1. Na janela **definir propriedades** , digite **SourceDataset** para o **nome**. Selecione *SQLDBEdgeLinkedService* para o serviço vinculado.

    2. Selecione ***a tabela que você deseja sincronizar para a*** tabela. Você também pode especificar uma consulta para esse conjunto de um, conforme mencionado posteriormente no tutorial. A consulta tem precedência sobre a tabela a que especificar neste passo.

    3. Selecione **OK**.

14. Alterne para o editor de pipeline selecionando a guia pipeline na parte superior ou selecionando o nome do pipeline no modo de exibição de árvore à esquerda. Na janela de propriedades da atividade **Lookup**, confirme que **SourceDataset** está selecionado no campo **Conjunto de Dados de Origem**.

15. Selecione **consulta** para o campo **usar consulta** e insira a seguinte consulta depois de atualizar o nome da tabela na consulta: você está selecionando apenas o valor máximo do carimbo de data/hora da tabela. Verifique se você também marcou a **primeira linha**.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![Selecionar consulta](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. Na caixa de ferramentas **atividades** , expanda **mover & transformar**, arraste e solte a atividade de **cópia** da caixa de ferramentas atividades e defina o nome como **IncrementalCopy**.

17. Conecte ambas as atividades de **pesquisa** à atividade de **cópia** arrastando o **botão verde** anexado às atividades de **pesquisa** para a atividade de **cópia** . Solte o botão do mouse quando você vir a cor da borda da atividade de cópia mudar para azul.

18. Selecione a atividade de **cópia** e confirme que você vê as propriedades da atividade na janela **Propriedades** .

19. Mude para o separador **Origem**, na janela **Propriedades**, e siga os passos abaixo:

    1. Selecione **SourceDataset** no campo **Conjunto de Dados de Origem**.

    2. Selecione **Consulta** no campo **Utilizar Consulta**.

    3. Insira a consulta SQL para o campo de **consulta** . Exemplo de consulta abaixo

    4. Consulta SQL:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Alterne para a guia **coletor** e selecione **+ novo** para o campo do conjunto de **coleta** .

21. Neste tutorial, o repositório de dados do coletor é do tipo **armazenamento de BLOBs do Azure**. Selecione **armazenamento de BLOBs do Azure**e selecione **continuar** na janela **novo conjunto de novas** .

22. Na janela **selecionar formato** , selecione o tipo de formato dos dados e selecione **continuar**.

23. Na janela **definir propriedades** , digite **SinkDataset** para o nome. Para serviço vinculado, selecione **+ novo**. Neste passo, vai criar uma ligação (serviço ligado) para o **Armazenamento de Blobs do Azure**.

24. Na janela **novo serviço vinculado (armazenamento de BLOBs do Azure)** , execute as seguintes etapas:

    1. Digite *AzureStorageLinkedService* para o nome.

    2. Selecione sua conta de armazenamento do Azure para o **nome da conta de armazenamento** com sua assinatura do Azure.

    3. Teste a **conexão** e selecione **concluir**.

25. Na janela **definir propriedades** , confirme se *AzureStorageLinkedService* está selecionado para o **serviço vinculado**. Em seguida, selecione **criar** e **OK**.

26. Na guia **coletor** , selecione **Editar**.

27. Vá para a guia **conexão** do *SinkDataset* e execute as seguintes etapas:

    1. Para o **campo caminho do arquivo** , insira *asdedatasync/incrementalcopy*, em que **adftutorial** é o nome do contêiner de BLOB e **incrementalcopy** é o nome da pasta. Crie o contentor se ainda não existir ou defina-o como o nome de um contentor existente. O Azure Data Factory cria automaticamente a pasta de saída *incrementalcopy*, se não existir. Também pode utilizar o botão **Procurar** do **Caminho do ficheiro** para navegar para uma pasta num contentor de blobs.

    2. Para a parte de **arquivo** do campo **caminho do arquivo** , selecione **adicionar conteúdo dinâmico [Alt + P]** e, em seguida, *insira @CONCAT(' incremental-', pipeline (). RunId, '. txt ')* na janela aberta. Em seguida, selecione **Concluir**. O nome do ficheiro é gerado dinamicamente através da expressão. Cada execução de pipeline tem um ID exclusivo. A atividade Copy utiliza o ID de execução para gerar o nome do ficheiro.

28. Alterne para o editor de **pipeline** selecionando a guia pipeline na parte superior ou selecionando o nome do pipeline no modo de exibição de árvore à esquerda.

29. Na caixa de ferramentas **Atividades**, expanda **Geral** e arraste e largue a atividade **Stored Procedure** da caixa de ferramentas **Atividades** na superfície de desenho do pipeline. **Ligue** a saída verde (Êxito) da atividade **Copy** à atividade **Stored Procedure**.

30. Selecione **atividade de procedimento armazenado** no designer de pipeline, altere seu nome para *SPtoUpdateWatermarkActivity*.

31. Alterne para a guia **conta SQL** e selecione *SQLDBEdgeLinkedService* para o **serviço vinculado**.

32. Mude para o separador **Procedimento Armazenado** e siga os passos abaixo:

    1. Para **nome do procedimento armazenado**, selecione *[dbo]. [ usp_write_watermark]* .

    2. Para especificar valores para os parâmetros de procedimento armazenado, selecione Importar parâmetro e insira os seguintes valores para os parâmetros:

    |Nome|Tipo|Valor|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@ {atividade (' NewWaterMark '). Output. firstRow. NewWatermarkvalue}|
    |TableName|String|@ {atividade (' OldWaterMark '). Output. firstRow. TableName}|

33. Para validar as configurações de pipeline, selecione **validar** na barra de ferramentas. Confirme que não há erros de validação. Para fechar **a janela relatório de validação de pipeline** , selecione **>>** .

34. Selecione o botão **Publicar Tudo** para publicar entidades (serviços ligados, conjuntos de dados e pipelines) no serviço Azure Data Factory. Aguarde até ver uma mensagem a indicar que a publicação foi bem-sucedida.

## <a name="trigger-a-pipeline-on-schedule"></a>Disparar um pipeline na agenda

1. Na barra de ferramentas do pipeline, selecione **Adicionar gatilho**e, em seguida, selecione **novo/editar**, selecione **+ novo**.

2. Nomeie seu gatilho com *HourlySync*, escolha **tipo** como agenda e defina **recorrência** como a cada 1 hora.

3. Selecione **OK**.

4. Selecione **Publicar Tudo**.

5. Selecione **disparar agora**.

6. Mude para o separador **Monitorizar**, no lado esquerdo. Pode ver o estado da execução do pipeline acionada pelo acionador manual. Selecione o botão **Atualizar** para atualizar a lista.

## <a name="next-steps"></a>Passos seguintes

O pipeline de Azure Data Factory neste tutorial copia dados de uma tabela na instância de borda do banco de dados SQL para um local no armazenamento de BLOBs do Azure em uma frequência de hora. Para saber mais sobre como usar Data Factory em mais cenários, consulte estes [tutoriais](../data-factory/tutorial-copy-data-portal.md).
