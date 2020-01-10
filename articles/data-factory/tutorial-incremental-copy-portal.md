---
title: Copiar uma tabela incrementalmente usando portal do Azure
description: Neste tutorial, vai criar um pipeline da fábrica de dados do Azure, que copia dados de forma incremental de uma base de dados SQL do Azure para o armazenamento de Blobs do Azure.
services: data-factory
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 01/11/2018
ms.openlocfilehash: 1c8df388bcd3956746edba9a721b0598025b827e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439178"
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage-using-the-azure-portal"></a>Carregar incrementalmente dados de um banco de dado SQL do Azure para o armazenamento de BLOBs do Azure usando o portal do Azure

Neste tutorial, vai criar uma fábrica de dados do Azure com um pipeline que carrega dados delta de uma tabela numa base de dados SQL do Azure para o armazenamento de Blobs do Azure. 

Vai executar os seguintes passos neste tutorial:

> [!div class="checklist"]
> * Preparar o arquivo de dados para armazenar o valor de limite de tamanho.
> * Criar uma fábrica de dados.
> * Criar serviços ligados. 
> * Crie conjuntos de dados de origem, de sink e de marca d'água.
> * Criar um pipeline.
> * Executar o pipeline.
> * Monitorizar a execução do pipeline. 
> * Resultados da revisão
> * Adicionar mais dados à origem.
> * Executar o pipeline novamente.
> * Monitorizar a segunda execução do pipeline
> * Rever os resultados da segunda execução


## <a name="overview"></a>Visão geral
Eis o diagrama de nível elevado da solução: 

![Carregar dados de forma incremental](media/tutorial-Incremental-copy-portal/incrementally-load.png)

Eis os passos importantes para criar esta solução: 

1. **Selecionar a coluna de limite de tamanho**.
    Selecione uma coluna no arquivo de dados de origem, que pode ser utilizada para dividir os registos novos ou atualizados para cada execução. Normalmente, os dados nesta coluna selecionada (por exemplo, last_modify_time ou ID) continuam a aumentar quando as linhas são criadas ou atualizadas. O valor máximo nesta coluna é utilizado como limite de tamanho.

2. **Preparar um arquivo de dados para armazenar o valor de limite de tamanho**. Neste tutorial, vai armazenar o valor de marca d'água numa base de dados SQL.
    
3. **Crie um pipeline com o seguinte fluxo de trabalho**: 
    
    O pipeline nesta solução tem as seguintes atividades:
  
    * Crie duas atividades de Pesquisa. Utilize a primeira atividade Lookup para obter o último valor de limite de tamanho. Utilize a segunda para obter o valor de limite de tamanho novo. Estes valores de limite de tamanho são transmitidos para a atividade Copy. 
    * Criar uma atividade Cópia que copia linhas do arquivo de dados de origem com o valor da coluna de limite de tamanho superior ao valor de limite de tamanho antigo e inferior ao valor novo. Em seguida, copia os dados delta do arquivo de dados de origem para um armazenamento de Blobs como um ficheiro novo. 
    * Crie uma atividade StoredProcedure, que atualiza o valor de marca d'água do pipeline que vai ser executado da próxima vez. 


Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* **Base de Dados SQL do Azure**. Vai utilizar a base de dados como o arquivo de dados de origem. Se não tiver uma base de dados SQL, veja[Criar uma base de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md) para obter os passos para criar uma.
* **Armazenamento do Azure**. Vai utilizar o armazenamento de blobs como arquivo de dados de sink. Se não tiver uma conta de armazenamento, veja [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md) para seguir os passos para criar uma. Crie um contentor com o nome adftutorial. 

### <a name="create-a-data-source-table-in-your-sql-database"></a>Criar uma tabela de origem de dados na base de dados SQL
1. Abra o SQL Server Management Studio. No **Explorador de Servidores**, clique com botão direito do rato na base de dados e escolha **Nova Consulta**.

2. Execute o seguinte comando SQL na base de dados SQL para criar uma tabela com o nome `data_source_table` e armazenar o valor de limite de tamaho: 
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    Neste tutorial, vai utilizar LastModifytime como a coluna de limite de tamanho. Os dados no arquivo da origem de dados são apresentados na tabela seguinte:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Criar outra tabela na base de dados SQL para armazenar o valor de limite superior de tamanho
1. Execute o seguinte comando SQL na base de dados SQL para criar uma tabela com o nome `watermarktable` e armazenar o valor de marca d'água:  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Defina o valor predefinido do limite superior de tamanho com o nome da tabela do arquivo de dados de origem. Neste tutorial, o nome da tabela é data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Reveja os dados na tabela `watermarktable`.
    
    ```sql
    Select * from watermarktable
    ```
    Saída: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Criar um procedimento armazenado na base de dados SQL 

Execute o comando seguinte para criar um procedimento armazenado na base de dados SQL:

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
2. No menu à esquerda, selecione **criar um recurso** > **Analytics** > **Data Factory**: 
   
   ![Seleção do Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Na página **Nova fábrica de dados**, introduza **ADFIncCopyTutorialDF** no **nome**. 
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se vir um ponto de exclamação vermelho com o seguinte erro, altere o nome da fábrica de dados (por exemplo, oseunomeADFIncCopyTutorialDF) e tente criá-la novamente. Veja o artigo [Data Factory – Naming Rules](naming-rules.md) (Data Factory – Regras de Nomenclatura) para obter as regras de nomenclatura dos artefactos do Data Factory.
  
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
   
   ![Home page da fábrica de dados](./media/doc-common-process/data-factory-home-page.png)
10. Clique no mosaico **Criar e Monitorizar** para iniciar a interface de utilizador (IU) do Azure Data Factory num separador à parte.

## <a name="create-a-pipeline"></a>Criar um pipeline
Neste tutorial, vai criar um pipeline com duas atividades de Pesquisa, uma atividade de Cópia e uma atividade StoredProcedure encadeadas num pipeline. 

1. Na página **introdução** da IU do Data Factory , clique no mosaico **Criar pipeline**. 

   ![Página de introdução da IU do Data Factory](./media/doc-common-process/get-started-page.png)    
3. Na página **Geral** da janela **Propriedades** do pipeline, introduza o nome **IncrementalCopyPipeline**. 

4. Vamos adicionar a primeira atividade Lookup para obter o valor de limite de tamanho antigo. Na caixa de ferramentas **Atividades**, expanda **Geral** e arraste e largue a atividade **Lookup** na superfície de desenho do pipeline. Altere o nome da atividade para **LookupOldWaterMarkActivity**.

   ![Primeira atividade Lookup - nome](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. Mude para o separador **Definições** e clique em **+ Novo** em **Conjunto de Dados de Origem**. Neste passo, vai criar um conjunto de dados para representar os dados em **watermarktable**. Esta tabela contém o limite de tamanho antigo que foi utilizado na operação de cópia anterior. 

6. Na janela **novo conjunto** de dados, selecione **Azure SQL Database**e clique em **continuar**. Você verá uma nova janela aberta para o conjunto de um. 

7. Na janela **definir propriedades** do conjunto de um, digite **WatermarkDataset** para o **nome**.

8. Para **serviço vinculado**, selecione **novo**e siga estas etapas:

    1. Introduza **AzureSqlDatabaseLinkedService** em **Nome**. 
    2. Em **Nome do servidor**, selecione o seu servidor do SQL do Azure.
    3. Selecione o **nome do banco de dados** na lista suspensa. 
    4. Insira seu **nome de usuário** & **senha**. 
    5. Para testar a ligação à base de dados SQL do Azure, clique em **Testar ligação**.
    6. Clique em **Concluir**.
    7. Confirme se **AzureSqlDatabaseLinkedService** está selecionado para o **serviço vinculado**.
       
        ![Janela Novo serviço ligado](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
    8. Selecione **Concluir**.
9. Na guia **conexão** , selecione **[dbo]. [ watermarktable]** para **tabela**. Se quiser pré-visualizar os dados na tabela, clique em **Pré-visualizar dados**.

    ![Conjunto de dados de limite de tamanho - definições de ligação](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
10. Clique no separador do pipeline, na parte superior, ou clique no nome do pipeline na vista de árvore, do lado esquerdo, para mudar para o editor do pipeline. Na janela de propriedades da atividade **Lookup**, confirme que **WatermarkDataset** está selecionado no campo **Conjunto de Dados de Origem**. 

11. Na caixa de ferramentas **Atividades**, expanda **Geral** e arraste e largue outra atividade **Lookup** na superfície de desenho do pipeline e defina o nome como **LookupNewWaterMarkActivity** no separador **Geral** da janela de propriedades. Esta atividade Lookup obtém o valor de limite de tamanho antigo da tabela com a origem de dados que vai ser copiada para o destino. 

12. Na janela de propriedades da segunda atividade **Lookup**, mude para o separador **Definições** e clique em **Novo**. Crie um conjunto de dados que aponte para a tabela de origem que contém o valor de limite de tamanho novo (valor máximo de LastModifyTime). 

13. Na janela **novo conjunto** de dados, selecione **Azure SQL Database**e clique em **continuar**. 
14. Na janela **definir propriedades** , digite **SourceDataset** para o **nome**. Selecione **AzureSqlDatabaseLinkedService** em **Serviço ligado**.
15. Selecione **[dbo].[data_source_table]** em Tabela. Vai especificar uma consulta neste conjunto de dados mais adiante no tutorial. A consulta tem precedência sobre a tabela a que especificar neste passo.
16. Selecione **Concluir**. 
17. Clique no separador do pipeline, na parte superior, ou clique no nome do pipeline na vista de árvore, do lado esquerdo, para mudar para o editor do pipeline. Na janela de propriedades da atividade **Lookup**, confirme que **SourceDataset** está selecionado no campo **Conjunto de Dados de Origem**. 
18. Selecione **Consulta** no campo **Utilizar Consulta** e introduza a consulta seguinte; só está a selecionar o valor máximo de **LastModifytime** de **data_ source_table**. Verifique se você também marcou a **primeira linha**.

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![Segunda atividade Lookup - consulta](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. Na caixa de ferramentas **atividades** , expanda **mover & transformar**e arraste e solte a atividade de **cópia** da caixa de ferramentas atividades e defina o nome como **IncrementalCopyActivity**. 

20. **Ligue ambas as atividades Lookup à atividade Copy** ao arrastar o **botão verde** associado às atividades Lookup para a atividade Copy. Largue o botão do rato quando vir que a cor do limite da atividade Copy muda para azul. 

    ![Ligar atividades Lookup à atividade Copy](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. Selecione a **atividade Copy** e confirme que vê as propriedades da atividade na janela **Propriedades**. 

22. Mude para o separador **Origem**, na janela **Propriedades**, e siga os passos abaixo:

    1. Selecione **SourceDataset** no campo **Conjunto de Dados de Origem**. 
    2. Selecione **Consulta** no campo **Utilizar Consulta**. 
    3. Introduza a seguinte consulta SQL no campo **Consulta**. 

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```
    
        ![Atividade Copy - origem](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. Mude para o separador **Sink** e clique em **+ Novo** no campo **Conjunto de Dados de Sink**. 

24. Neste tutorial, o arquivo de dados de sink é do tipo Armazenamento de Blobs do Azure. Portanto, selecione **armazenamento de BLOBs do Azure**e clique em **continuar** na janela **novo conjunto de novas** . 
25. Na janela **selecionar formato** , selecione o tipo de formato dos dados e clique em **continuar**.
25. Na janela **definir propriedades** , digite **SinkDataset** para o **nome**. Para **serviço vinculado**, selecione **+ novo**. Neste passo, vai criar uma ligação (serviço ligado) para o **Armazenamento de Blobs do Azure**.
26. Na janela **novo serviço vinculado (armazenamento de BLOBs do Azure)** , execute as seguintes etapas: 

    1. Introduza **AzureStorageLinkedService** em **Nome**. 
    2. Selecione a sua conta de Armazenamento do Azure em **Nome da conta de armazenamento**.
    3. Teste a conexão e clique em **concluir**. 

27. Na janela **definir propriedades** , confirme se **AzureStorageLinkedService** está selecionado para o **serviço vinculado**. Em seguida, selecione **Concluir**.
28. Vá para a guia **conexão** do SinkDataset e execute as seguintes etapas:
    1. Para o campo **caminho do arquivo** , digite **adftutorial/incrementalcopy**. **adftutorial** é o nome do contentor de blobs e **incrementalcopy** é o nome da pasta. Este fragmento parte do princípio de que tem um contentor de blobs denominado adftutorial no armazenamento de blobs. Crie o contentor se ainda não existir ou defina-o como o nome de um contentor existente. O Azure Data Factory cria automaticamente a pasta de saída **incrementalcopy**, se não existir. Também pode utilizar o botão **Procurar** do **Caminho do ficheiro** para navegar para uma pasta num contentor de blobs.
    2. Para a parte de **arquivo** do campo **caminho do arquivo** , selecione **adicionar conteúdo dinâmico [Alt + P]** e, em seguida, insira `@CONCAT('Incremental-', pipeline().RunId, '.txt')`na janela aberta. Em seguida, selecione **Concluir**. O nome do ficheiro é gerado dinamicamente através da expressão. Cada execução de pipeline tem um ID exclusivo. A atividade Copy utiliza o ID de execução para gerar o nome do ficheiro. 

28. Clique no separador do pipeline, na parte superior, ou clique no nome do pipeline na vista de árvore, do lado esquerdo, para mudar para o editor do **pipeline**. 
29. Na caixa de ferramentas **Atividades**, expanda **Geral** e arraste e largue a atividade **Stored Procedure** da caixa de ferramentas **Atividades** na superfície de desenho do pipeline. **Ligue** a saída verde (Êxito) da atividade **Copy** à atividade **Stored Procedure**. 

24. Selecione **Atividade de Procedimento Armazenado** no estruturador do pipeline e altere o nome para **StoredProceduretoWriteWatermarkActivity**. 

25. Alterne para a guia **conta SQL** e selecione **AzureSqlDatabaseLinkedService** para o **serviço vinculado**. 

26. Mude para o separador **Procedimento Armazenado** e siga os passos abaixo: 

    1. Para **nome do procedimento armazenado**, selecione **usp_write_watermark**. 
    2. Para especificar valores para os parâmetros do procedimento armazenado, clique em **Importar parâmetro** e introduza os seguintes valores para os parâmetros: 

        | Nome | Tipo | Valor | 
        | ---- | ---- | ----- | 
        | LastModifiedtime | DateTime | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue} |
        | TableName | String | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

    ![Atividade de procedimento armazenado - definições do procedimento armazenado](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. Para validar as definições do pipeline, clique em **Validar**, na barra de ferramentas. Confirme que não há erros de validação. Para fechar a janela **Relatório de Validação do Pipeline**, clique em >>.   

28. Selecione o botão **Publicar Tudo** para publicar entidades (serviços ligados, conjuntos de dados e pipelines) no serviço Azure Data Factory. Aguarde até ver uma mensagem a indicar que a publicação foi bem-sucedida. 


## <a name="trigger-a-pipeline-run"></a>Acionar uma execução de pipeline
1. Clique em **Adicionar gatilho** na barra de ferramentas e clique em **disparar agora**. 

2. Na janela **Executar Pipeline**, selecione **Concluir**. 

## <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.

1. Mude para o separador **Monitorizar**, no lado esquerdo. Pode ver o estado da execução do pipeline acionada pelo acionador manual. Clique em **Atualizar** para atualizar a lista. 
    
2. Para ver as execuções de atividades associadas a esta execução do pipeline, clique na primeira ligação (**Ver Execuções de Atividades**), na coluna **Ações**. Pode clicar em **Pipelines**, na parte superior, para regressar à vista anterior. Clique em **Atualizar** para atualizar a lista.


## <a name="review-the-results"></a>Rever os resultados
1. Utilize ferramentas como o [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para ligar à sua Conta de Armazenamento do Azure. Verifique se é criado um ficheiro de saída na pasta **incrementalcopy** do contentor **adftutorial**.

    ![Primeiro ficheiro de saída](./media/tutorial-incremental-copy-portal/first-output-file.png)
2. Abra o ficheiro de saída e repare que todos os dados são copiados de **data_source_table** para o ficheiro de blob. 

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. Verifique o valor mais recente do `watermarktable`. Verá que o valor de marca d’água foi atualizado.

    ```sql
    Select * from watermarktable
    ```
    
    A saída é:
 
    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 | 

## <a name="add-more-data-to-source"></a>Adicionar mais dados à origem

Insira novos dados na base de dados SQL (arquivo de dados de origem).

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
``` 

Os dados atualizados na base de dados SQL são:

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```


## <a name="trigger-another-pipeline-run"></a>Acionar outra execução de pipeline
1. Alterne para a guia **Editar** . Clique no pipeline no modo de exibição de árvore se ele não estiver aberto no designer. 

2. Clique em **Adicionar gatilho** na barra de ferramentas e clique em **disparar agora**. 


## <a name="monitor-the-second-pipeline-run"></a>Monitorizar a segunda execução do pipeline

1. Mude para o separador **Monitorizar**, no lado esquerdo. Pode ver o estado da execução do pipeline acionada pelo acionador manual. Clique em **Atualizar** para atualizar a lista. 
    
2. Para ver as execuções de atividades associadas a esta execução do pipeline, clique na primeira ligação (**Ver Execuções de Atividades**), na coluna **Ações**. Pode clicar em **Pipelines**, na parte superior, para regressar à vista anterior. Clique em **Atualizar** para atualizar a lista.


## <a name="verify-the-second-output"></a>Verificar a segunda saída

1. No armazenamento de blobs, verá que outro ficheiro foi criado. Neste tutorial, o novo nome de ficheiro é `Incremental-<GUID>.txt`. Abra esse ficheiro e verá duas linhas de registos no mesmo.

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. Verifique o valor mais recente do `watermarktable`. Verá que o valor de marca d’água foi atualizado.

    ```sql
    Select * from watermarktable
    ```
    saída de exemplo: 
    
    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |


     
## <a name="next-steps"></a>Passos seguintes
Neste tutorial, executou os passos seguintes: 

> [!div class="checklist"]
> * Preparar o arquivo de dados para armazenar o valor de limite de tamanho.
> * Criar uma fábrica de dados.
> * Criar serviços ligados. 
> * Crie conjuntos de dados de origem, de sink e de marca d'água.
> * Criar um pipeline.
> * Executar o pipeline.
> * Monitorizar a execução do pipeline. 
> * Resultados da revisão
> * Adicionar mais dados à origem.
> * Executar o pipeline novamente.
> * Monitorizar a segunda execução do pipeline
> * Rever os resultados da segunda execução

Neste tutorial, o pipeline copiou dados a partir de uma única tabela numa base de dados SQL para um armazenamento de Blobs. Avance para o tutorial seguinte para saber como copiar dados de várias tabelas para uma base de dados do SQL Server local para uma base de dados SQL. 

> [!div class="nextstepaction"]
>[Carregar dados de forma incremental a partir de várias tabelas no SQL Server para a Base de Dados SQL do Azure](tutorial-incremental-copy-multiple-tables-portal.md)



