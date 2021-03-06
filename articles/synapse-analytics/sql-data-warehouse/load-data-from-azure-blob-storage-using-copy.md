---
title: 'Tutorial: Carregue os dados do Táxi de Nova Iorque'
description: Tutorial usa o portal Azure e o SQL Server Management Studio para carregar os dados do Táxi de Nova Iorque a partir de uma bolha de Azure para Synapse SQL.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 7ede40aba8e2d36e4262b4bc89a35f5d67079e0e
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567514"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>Tutorial: Carregue o conjunto de dados do Táxi de Nova Iorque

Este tutorial utiliza a [declaração COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) para carregar o conjunto de dados do Táxi de Nova Iorque a partir de uma conta de Armazenamento Azure Blob. Este tutorial utiliza o [portal do Azure](https://portal.azure.com) e o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS) para:

> [!div class="checklist"]
>
> * Criar um utilizador designado para carregar dados
> * Criar as tabelas para o conjunto de dados da amostra 
> * Utilize a declaração COPY T-SQL para carregar dados no seu armazém de dados
> * Ver o progresso dos dados à medida que são carregados

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar este tutorial, transfira e instale a versão mais recente do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS).  

Este tutorial pressupõe que já criou uma piscina dedicada SQL a partir do seguinte [tutorial.](./create-data-warehouse-portal.md#connect-to-the-server-as-server-admin)

## <a name="create-a-user-for-loading-data"></a>Criar um utilizador para carregar dados

A conta de administrador do servidor destina-se a efetuar operações de gestão e não é adequada para executar consultas em dados do utilizador. O carregamento de dados é uma operação de memória intensiva. Os máximos de memória são definidos de acordo com as unidades de [armazém de dados](what-is-a-data-warehouse-unit-dwu-cdwu.md) e [a classe de recursos](resource-classes-for-workload-management.md) configurada.

É melhor criar um início de sessão e utilizador dedicado para carregar dados. Em seguida, adicione o utilizador de carregamento a uma [classe de recursos](resource-classes-for-workload-management.md) que permita uma alocação de memória máxima adequada.

Conecte-se como administrador do servidor para que possa criar logins e utilizadores. Utilize estes passos para criar um início de sessão e o utilizador com o nome **LoaderRC20**. Em seguida, atribua o utilizador à classe de recursos **staticrc20**.

1. Em SSMS, **o mestre** de seleção à direita para mostrar um menu suspenso e escolher **Nova Consulta**. É aberta uma nova janela de consulta.

    ![Nova consulta na base de dados mestra](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. Na janela de consulta, introduza estes comandos T-SQL para criar um início de sessão e o utilizador com o nome LoaderRC20, substituindo a sua palavra-passe por “a123STRONGpassword!”.

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Selecione **Execute** (Executar).

4. Clique com o botão direito do rato em **mySampleDataWarehouse** e escolha **Nova Consulta**. É aberta uma nova janela de consulta.  

    ![Nova consulta no armazém de dados de exemplo](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. Introduza os seguintes comandos T-SQL para criar um utilizador de base de dados com o nome LoaderRC20 para o início de sessão de LoaderRC20. A segunda linha concede ao novo utilizador permissões CONTROL no novo armazém de dados.  Estas permissões são semelhantes a tornar o utilizador o proprietário da base de dados. A terceira linha adiciona o novo utilizador como um membro da [classe de recursos](resource-classes-for-workload-management.md) staticrc20.

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Selecione **Execute** (Executar).

## <a name="connect-to-the-server-as-the-loading-user"></a>Ligar ao servidor como utilizador de carregamento

O primeiro passo para o carregamento de dados é iniciar sessão como LoaderRC20.  

1. No Object Explorer, selecione o menu **Desacompor** o menu Desajeição e selecione **o Motor de Base de Dados**. A caixa de diálogo **Ligar ao Servidor** é apresentada.

    ![Ligar com início de sessão novo](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Introduza o nome de servidor completamente qualificado e introduza **LoaderRC20** como o Início de Sessão.  Introduza a palavra-passe para LoaderRC20.

3. Selecione **Ligar**.

4. Quando a ligação estiver pronta, verá duas ligações de servidor no Object Explorer. Uma ligação como ServerAdmin e uma ligação como MedRCLogin.

    ![Ligação com êxito](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-tables-for-the-sample-data"></a>Criar tabelas para os dados da amostra

Está pronto para iniciar o processo de carregamento de dados para o novo armazém de dados. Esta parte do tutorial mostra-lhe como usar a declaração COPY para carregar o conjunto de dados do táxi de Nova Iorque a partir de uma bolha de armazenamento Azure. Para referência futura, para saber como obter os seus dados para o Azure Blob Storage ou para os carregar diretamente a partir da sua fonte, consulte a [visão geral](design-elt-data-loading.md)de carregamento .

Execute os seguintes scripts SQL e especifique informações sobre os dados que pretende carregar. Estas informações incluem a localização dos dados, o formato do conteúdo dos dados e a definição da tabela dos dados.

1. Na secção anterior, iniciou sessão no armazém de dados como LoaderRC20. No SSMS, clique com o botão direito do rato na sua ligação LoaderRC20 e selecione **Nova Consulta**.  É apresentada uma nova janela de consulta.

    ![Nova janela de consulta de carregamento](./media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Compare a janela de consulta com a imagem anterior.  Certifique-se de que a nova janela de consulta está em execução como LoaderRC20 e a efetuar consultas na base de dados MySampleDataWarehouse. Utilize esta janela de consulta para executar todos os passos de carregamento.

7. Executar as seguintes declarações T-SQL para criar as tabelas:

    ```sql
    CREATE TABLE [dbo].[Date]
    (
        [DateID] int NOT NULL,
        [Date] datetime NULL,
        [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FirstDayOfMonth] date NULL,
        [LastDayOfMonth] date NULL,
        [FirstDayOfQuarter] date NULL,
        [LastDayOfQuarter] date NULL,
        [FirstDayOfYear] date NULL,
        [LastDayOfYear] date NULL,
        [IsHolidayUSA] bit NULL,
        [IsWeekday] bit NULL,
        [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Geography]
    (
        [GeographyID] int NOT NULL,
        [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Time]
    (
        [TimeID] int NOT NULL,
        [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HourNumber] tinyint NOT NULL,
        [MinuteNumber] tinyint NOT NULL,
        [SecondNumber] tinyint NOT NULL,
        [TimeInSecond] int NOT NULL,
        [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [DayTimeBucketGroupKey] int NOT NULL,
        [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    ```
    

## <a name="load-the-data-into-your-data-warehouse"></a>Carregar os dados para o armazém de dados

Esta secção utiliza a [declaração COPY para carregar](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) os dados da amostra a partir da Bolha de Armazenamento Azure.  

> [!NOTE]
> Este tutorial carrega os dados diretamente para a tabela final. Normalmente, carrega-se numa mesa de preparação para as suas cargas de produção. Enquanto os dados estiverem na tabela de teste, pode efetuar quaisquer transformações necessárias. 

1. Executar as seguintes declarações para carregar os dados:

    ```sql
    COPY INTO [dbo].[Date]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Date'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Date] - Taxi dataset');
    
    
    COPY INTO [dbo].[Geography]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Geography'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Geography] - Taxi dataset');
    
    COPY INTO [dbo].[HackneyLicense]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/HackneyLicense'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset');
    
    COPY INTO [dbo].[Medallion]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Medallion'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Medallion] - Taxi dataset');
    
    COPY INTO [dbo].[Time]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Time'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Time] - Taxi dataset');
    
    COPY INTO [dbo].[Weather]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Weather'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Weather] - Taxi dataset');
    
    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```

2. Veja os dados à medida que são carregados. Está a carregar vários GBs de dados e a comprimi-lo em índices de colunas agrupados altamente performantes. Execute a seguinte consulta que utiliza vistas de gestão dinâmica (DMVs) para mostrar o estado do carregamento.

    ```sql
    SELECT  r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command
    ,       sum(bytes_processed) AS bytes_processed
    ,       sum(rows_processed) AS rows_processed
    FROM    sys.dm_pdw_exec_requests r
                  JOIN sys.dm_pdw_dms_workers w
                         ON r.[request_id] = w.request_id
    WHERE [label] = 'COPY : Load [dbo].[Date] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Geography] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Medallion] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Time] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Weather] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Trip] - Taxi dataset' 
    and session_id <> session_id() and type = 'WRITER'
    GROUP BY r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command;
    ```
    
3. Veja todas as consultas de sistema.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Veja os seus dados devidamente carregados para o armazém de dados e deleite-se.

    ![Ver tabelas carregadas](./media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Estão a ser-lhe cobrados os recursos de computação e os dados que carregou para o armazém de dados. São faturados em separado.

* Se quiser manter os dados no armazenamento, pode interromper a computação quando não estiver a utilizar o armazém de dados. Ao colocar a computação em pausa, só lhe será cobrado o armazenamento de dados e pode retomar a computação quando estiver pronto para trabalhar com os dados.
* Se quiser remover futuras cobranças, pode eliminar o armazém de dados.

Siga estes passos para limpar os recursos conforme quiser.

1. Faça login no [portal Azure,](https://portal.azure.com)selecione o seu armazém de dados.

    ![Limpar os recursos](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Para parar o cálculo, selecione o **botão Pausa.** Quando o armazém de dados estiver em pausa, verá um botão **Iniciar**.  Para retomar o cálculo, selecione **Start**.

3. Para remover o armazém de dados para que não seja cobrado para computação ou armazenamento, selecione **Delete**.

4. Para remover o servidor criado, selecione **mynewserver-20180430.database.windows.net** na imagem anterior e, em seguida, selecione **Delete**.  Tenha cuidado, uma vez que eliminar o servidor também eliminará todas as bases de dados atribuídas ao mesmo.

5. Para remover o grupo de recursos, selecione **myResourceGroup** e, em seguida, **selecione Delete resource group**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar um armazém de dados e a criar um utilizador para carregar dados. Usou a simples [declaração COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples) para carregar dados no seu armazém de dados.

Fez tudo isto:
> [!div class="checklist"]
>
> * Criou um armazém de dados no portal do Azure
> * Configurar uma regra de firewall ao nível do servidor no portal do Azure
> * Ligou ao armazém de dados com o SSMS
> * Criou um utilizador designado para carregar dados
> * Criei as tabelas para os dados da amostra
> * Usou a declaração COPY T-SQL para carregar dados no seu armazém de dados
> * Viu o progresso dos dados à medida que são carregados

Avance para a visão geral do desenvolvimento para aprender a migrar uma base de dados existente para a Azure Synapse Analytics:

> [!div class="nextstepaction"]
> [Decisões de design para migrar uma base de dados existente para a Azure Synapse Analytics](sql-data-warehouse-overview-develop.md)

Para mais exemplos de carregamento e referências, consulte a seguinte documentação:

- [Documentação de referência de declaração de CÓPIA](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)
- [Exemplos de COPY para cada método de autenticação](./quickstart-bulk-load-copy-tsql-examples.md)
- [INÍCIO DE CÓPIA para uma única tabela](./quickstart-bulk-load-copy-tsql.md)
