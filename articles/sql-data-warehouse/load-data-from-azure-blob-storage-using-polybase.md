---
title: 'Tutorial: carregar dados de dos táxis de Nova York no Azure SQL Data Warehouse | Microsoft Docs'
description: O tutorial usa portal do Azure e SQL Server Management Studio para carregar dados de dos táxis de Nova York de um blob global do Azure para o Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 04/26/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2e799d84aee9ba4d3bfb00ddfad358c9b90c3d59
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73482395"
---
# <a name="tutorial-load-new-york-taxicab-data-to-azure-sql-data-warehouse"></a>Tutorial: carregar dados de dos táxis de Nova York no Azure SQL Data Warehouse

Este tutorial usa o polybase para carregar dados de dos táxis de Nova York de um blob global do Azure para o Azure SQL Data Warehouse. Este tutorial utiliza o [portal do Azure](https://portal.azure.com) e o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) para: 

> [!div class="checklist"]
> * Criar um armazém de dados no portal do Azure
> * Configurar uma regra de firewall ao nível do servidor no portal do Azure
> * Ligar ao armazém de dados com o SSMS
> * Criar um utilizador designado para carregar dados
> * Criar tabelas externas para dados no armazenamento de blobs do Azure
> * Utilizar a instrução CTAS T-SQL para carregar dados para o armazém de dados
> * Ver o progresso dos dados à medida que são carregados
> * Criar estatísticas dos dados recentemente carregados

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar este tutorial, transfira e instale a versão mais recente do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).


## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-blank-sql-data-warehouse"></a>Criar um SQL Data Warehouse em branco

Uma SQL Data Warehouse do Azure é criada com um conjunto definido de [recursos de computação](memory-and-concurrency-limits.md). A base de dados é criada num [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) e num [servidor lógico SQL do Azure](../sql-database/sql-database-features.md). 

Siga estas etapas para criar um SQL Data Warehouse em branco. 

1. Clique em **Criar um recurso**, no canto superior esquerdo do portal do Azure.

2. Selecione **Bases de Dados** na página **Novo** e selecione **Armazém de Dados SQL** em **Destaques** na página **Novo**.

    ![criar um armazém de dados](media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Preencha o formulário do SQL Data Warehouse com as seguintes informações:   

   | Definição | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Nome da base de dados** | mySampleDataWarehouse | Para nomes de bases de dados válidos, veja [Database Identifiers](/sql/relational-databases/databases/database-identifiers) (Identificadores de Bases de Dados). | 
   | **Subscrição** | A sua subscrição  | Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
   | **Grupo de recursos** | myResourceGroup | Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). |
   | **Selecionar origem** | Base de dados vazia | Especifica para criar uma base de dados vazia. Tenha em atenção que um armazém de dados é um tipo de base de dados.|

    ![criar um armazém de dados](media/load-data-from-azure-blob-storage-using-polybase/create-data-warehouse.png)

4. Clique em **Servidor** para criar e configurar um novo servidor para a nova base de dados. Preencha o **Novo formulário de servidor** com as seguintes informações: 

    | Definição | Valor sugerido | Descrição | 
    | ------- | --------------- | ----------- |
    | **Nome do servidor** | Qualquer nome globalmente exclusivo | Para nomes de servidores válidos, veja [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). | 
    | **Início de sessão de administrador do servidor** | Qualquer nome válido | Para nomes de início de sessão válidos, veja [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificadores de Bases de Dados).|
    | **Palavra-passe** | Qualquer palavra-passe válida | A sua palavra-passe deve ter, pelo menos, oito carateres e deve conter carateres de três das seguintes categorias: carateres maiúsculos, carateres minúsculos, números e carateres não alfanuméricos. |
    | **Localização** | Nenhuma localização válida | Para obter mais informações sobre regiões, veja [Azure Regions](https://azure.microsoft.com/regions/) (Regiões do Azure). |

    ![criar um servidor de base de dados](media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Clique em **Selecionar**.

6. Clique em **nível de desempenho** para especificar se o data warehouse é Gen1 ou Gen2 e o número de unidades de data warehouse. 

7. Para este tutorial, selecione **Gen2** de SQL data warehouse. O controle deslizante é definido como **DW1000c** por padrão.  Experimente movê-lo para cima e para baixo para ver como funciona. 

    ![configurar o desempenho](media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Clique em **Aplicar**.
9. Na página do SQL Data Warehouse, selecione um **agrupamento** para a base de dados vazia. Para este tutorial, utilize o valor predefinido. Para obter mais informações sobre agrupamentos, veja [Agrupamentos](/sql/t-sql/statements/collations)

11. Agora que concluiu o formulário da Base de Dados SQL do Servidor, clique em **Criar** para aprovisionar a base de dados. O aprovisionamento demora alguns minutos. 

    ![clique em criar](media/load-data-from-azure-blob-storage-using-polybase/click-create.png)

12. Na barra de ferramentas, clique em **Notificações** para monitorizar o processo de implementação.
    
     ![notificação](media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall ao nível do servidor

O serviço SQL Data Warehouse cria uma firewall ao nível do servidor que impede que as aplicações e ferramentas externas estabeleçam uma ligação ao servidor ou a quaisquer bases de dados no servidor. Para permitir a conectividade, pode adicionar regras de firewall que permitem a conectividade para endereços IP específicos.  Siga estes passos para criar uma [regra de firewall ao nível do servidor](../sql-database/sql-database-firewall-configure.md) para o endereço IP do cliente. 

> [!NOTE]
> O SQL Data Warehouse comunica através da porta 1433. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 1433 poderá não ser permitido pela firewall da rede. Se assim for, não poderá ligar ao servidor da Base de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>

1. Depois de concluída a implementação, clique em **Bases de dados SQL** no menu do lado esquerdo e, em seguida, clique em **mySampleDatabase** na página **Bases de dados SQL**. A página Visão geral do seu banco de dados é aberta, mostrando o nome totalmente qualificado do servidor (como **mynewserver-20180430.Database.Windows.net**) e fornece opções para configuração adicional. 

2. Copie este nome de servidor totalmente qualificado para utilizar para ligar ao seu servidor e às respetivas bases de dados nos seguintes guias de introdução. Em seguida, clique no nome do servidor para abrir as definições do servidor.

    ![localizar nome do servidor](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png) 

3. Clique no nome do servidor para abrir as definições do servidor.

    ![definições do servidor](media/load-data-from-azure-blob-storage-using-polybase/server-settings.png) 

5. Clique em **Mostrar definições da firewall**. É aberta a página **Definições da firewall** do servidor da Base de Dados SQL. 

    ![regra de firewall do servidor](media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png) 

4. Clique em **Adicionar IP de cliente**, na barra de ferramentas, para adicionar o seu endereço IP atual a uma nova regra de firewall. Uma regra de firewall consegue abrir a porta 1433 para um único endereço IP ou para um intervalo de endereços IP.

5. Clique em **Guardar**. É criada uma regra de firewall ao nível do servidor para a sua porta de abertura 1433 do endereço IP atual no servidor lógico.

6. Clique em **OK** e, em seguida, feche a página **Definições da firewall**.

Agora, pode ligar ao servidor SQL e aos respetivos armazéns de dados com este endereço IP. A ligação funciona a partir do SQL Server Management Studio ou de outra ferramenta à sua escolha. Ao ligar, utilize a conta ServerAdmin que criou anteriormente.  

> [!IMPORTANT]
> Por predefinição, o acesso através da firewall da Base de Dados SQL está ativado para todos os serviços do Azure. Clique em **DESATIVADO** nesta página e, em seguida, clique em **Guardar** para desativar a firewall para todos os serviços do Azure.

## <a name="get-the-fully-qualified-server-name"></a>Obter o nome de servidor completamente qualificado

Obtenha o nome de servidor completamente qualificado para o servidor SQL no portal do Azure. Utilizará mais tarde o nome completamente qualificado quando ligar ao servidor.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **SQL data warehouses** no menu à esquerda e clique em seu banco de dados na página **SQL data warehouses** . 
3. No painel **Essentials** na página do portal do Azure da sua base de dados, localize e, em seguida, copie o **Nome do servidor**. Neste exemplo, o nome totalmente qualificado é mynewserver-20180430.database.windows.net. 

    ![informações da ligação](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>Ligar ao servidor como administrador do servidor

Esta secção utiliza o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) para estabelecer uma ligação ao servidor SQL do Azure.

1. Abra o SQL Server Management Studio.

2. Na caixa de dialogo **Ligar ao Servidor**, introduza as seguintes informações:

    | Definição      | Valor sugerido | Descrição | 
    | ------------ | --------------- | ----------- | 
    | Tipo de servidor | Motor de base de dados | Este valor é obrigatório |
    | Nome do servidor | O nome de servidor completamente qualificado | O nome deve ser algo assim: **mynewserver-20180430.Database.Windows.net**. |
    | Autenticação | Autenticação do SQL Server | A Autenticação do SQL é o único tipo de autenticação que configurámos neste tutorial. |
    | Iniciar sessão | A conta de administrador do servidor | Esta é a conta que especificou quando criou o servidor. |
    | Palavra-passe | A palavra-passe da sua conta de administrador do servidor | Esta é a palavra-passe que especificou quando criou o servidor. |

    ![ligar ao servidor](media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

4. Clique em **Ligar**. A janela do Object Explorer é aberta no SSMS. 

5. No Object Explorer, expanda **Databases**. Em seguida, expanda **Bases de dados do sistema** e **mestre** para ver os objetos na base de dados mestra.  Expanda **mySampleDatabase** para ver os objetos na nova base de dados.

    ![objetos da base de dados](media/load-data-from-azure-blob-storage-using-polybase/connected.png) 

## <a name="create-a-user-for-loading-data"></a>Criar um utilizador para carregar dados

A conta de administrador do servidor destina-se a efetuar operações de gestão e não é adequada para executar consultas em dados do utilizador. O carregamento de dados é uma operação de memória intensiva. Os máximos de memória são definidos de acordo com a geração de SQL Data Warehouse que você provisionou, [data warehouse unidades](what-is-a-data-warehouse-unit-dwu-cdwu.md)e [classe de recurso](resource-classes-for-workload-management.md). 

É melhor criar um início de sessão e utilizador dedicado para carregar dados. Em seguida, adicione o utilizador de carregamento a uma [classe de recursos](resource-classes-for-workload-management.md) que permita uma alocação de memória máxima adequada.

Uma vez que está atualmente ligado como o administrador do servidor, pode criar inícios de sessão e utilizadores. Utilize estes passos para criar um início de sessão e o utilizador com o nome **LoaderRC20**. Em seguida, atribua o utilizador à classe de recursos **staticrc20**. 

1.  No SSMS, faça duplo clique em **mestre** para mostrar um menu pendente e escolha **Nova Consulta**. É aberta uma nova janela de consulta.

    ![Nova consulta na base de dados mestra](media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. Na janela de consulta, introduza estes comandos T-SQL para criar um início de sessão e o utilizador com o nome LoaderRC20, substituindo a sua palavra-passe por “a123STRONGpassword!”. 

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Clique em **Executar**.

4. Clique com o botão direito do rato em **mySampleDataWarehouse** e escolha **Nova Consulta**. É aberta uma nova janela de consulta.  

    ![Nova consulta no armazém de dados de exemplo](media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)
 
5. Introduza os seguintes comandos T-SQL para criar um utilizador de base de dados com o nome LoaderRC20 para o início de sessão de LoaderRC20. A segunda linha concede ao novo utilizador permissões CONTROL no novo armazém de dados.  Estas permissões são semelhantes a tornar o utilizador o proprietário da base de dados. A terceira linha adiciona o novo utilizador como um membro da [classe de recursos](resource-classes-for-workload-management.md) staticrc20.

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Clique em **Executar**.

## <a name="connect-to-the-server-as-the-loading-user"></a>Ligar ao servidor como utilizador de carregamento

O primeiro passo para o carregamento de dados é iniciar sessão como LoaderRC20.  

1. No Object Explorer, clique no menu pendente **Ligar** e selecione **Motor de Base de Dados**. A caixa de diálogo **Ligar ao Servidor** é apresentada.

    ![Ligar com início de sessão novo](media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Introduza o nome de servidor completamente qualificado e introduza **LoaderRC20** como o Início de Sessão.  Introduza a palavra-passe para LoaderRC20.

3. Clique em **Ligar**.

4. Quando a ligação estiver pronta, verá duas ligações de servidor no Object Explorer. Uma ligação como ServerAdmin e uma ligação como MedRCLogin.

    ![Ligação com êxito](media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-external-tables-for-the-sample-data"></a>Criar tabelas externas para os dados de exemplo

Está pronto para iniciar o processo de carregamento de dados para o novo armazém de dados. Este tutorial mostra como usar tabelas externas para carregar dados cab de táxi da cidade de Nova York de um blob de armazenamento do Azure. Para referência futura e para saber como colocar os seus dados no armazenamento de blobs do Azure ou carregá-los diretamente da sua origem para o SQL Data Warehouse,veja a [descrição geral do carregamento](sql-data-warehouse-overview-load.md).

Execute os seguintes scripts SQL para especificar informações sobre os dados que pretende carregar. Estas informações incluem a localização dos dados, o formato do conteúdo dos dados e a definição da tabela dos dados. 

1. Na secção anterior, iniciou sessão no armazém de dados como LoaderRC20. No SSMS, clique com o botão direito do rato na sua ligação LoaderRC20 e selecione **Nova Consulta**.  É apresentada uma nova janela de consulta. 

    ![Nova janela de consulta de carregamento](media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Compare a janela de consulta com a imagem anterior.  Certifique-se de que a nova janela de consulta está em execução como LoaderRC20 e a efetuar consultas na base de dados MySampleDataWarehouse. Utilize esta janela de consulta para executar todos os passos de carregamento.

3. Crie uma chave mestra para a base de dados MySampleDataWarehouse. Só tem de criar uma chave mestra uma vez por base de dados. 

    ```sql
    CREATE MASTER KEY;
    ```

4. Execute a seguinte instrução [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) para definir a localização do blob do Azure. Esta é a localização dos dados externos de táxis.  Para executar um comando que anexou à janela de consulta, realce os comandos que pretende executar e clique em **Executar**.

    ```sql
    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
        TYPE = Hadoop,
        LOCATION = 'wasbs://2013@nytaxiblob.blob.core.windows.net/'
    );
    ```

5. Execute a seguinte instrução T-SQL [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql) para especificar as características de formatação e as opções para o ficheiro de dados externos. Esta instrução especifica que os dados externos são armazenados como texto e que os valores são separados pelo caráter de pipe (“|”). O ficheiro externo é comprimido com Gzip. 

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( 
            FIELD_TERMINATOR = ',',
            STRING_DELIMITER = '',
            DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        )
    );
    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH ( 
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = '|',
            STRING_DELIMITER = '',
        DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        ),
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

6.  Execute a seguinte instrução [CREATE SCHEMA](/sql/t-sql/statements/create-schema-transact-sql) para criar um esquema para o formato de ficheiro externo. O esquema é uma forma de organizar as tabelas externas que está prestes a criar.

    ```sql
    CREATE SCHEMA ext;
    ```

7. Crie as tabelas externas. As definições de tabela são armazenadas no SQL Data Warehouse, mas os dados de referência de tabela são armazenados no armazenamento de blobs do Azure. Execute os comandos T-SQL seguintes para criar várias tabelas externas que apontem para o blob do Azure que definimos anteriormente na origem de dados externa.

    ```sql
    CREATE EXTERNAL TABLE [ext].[Date] 
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
        LOCATION = 'Date',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    ); 
    CREATE EXTERNAL TABLE [ext].[Geography]
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
        LOCATION = 'Geography',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0 
    );      
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'HackneyLicense',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Medallion',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;  
    CREATE EXTERNAL TABLE [ext].[Time]
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
        LOCATION = 'Time',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Trip]
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
        LOCATION = 'Trip2013',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = compressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        LOCATION = 'Weather',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
    ```

8. No Object Explorer, expanda mySampleDataWarehouse para ver a lista de tabelas externas que acabou de criar.

    ![Ver tabelas externas](media/load-data-from-azure-blob-storage-using-polybase/view-external-tables.png)

## <a name="load-the-data-into-your-data-warehouse"></a>Carregar os dados para o armazém de dados

Esta secção utiliza as tabelas externas que acabou de definir para carregar os dados de exemplo do Azure Storage Blob para o SQL Data Warehouse.  

> [!NOTE]
> Este tutorial carrega os dados diretamente para a tabela final. Num ambiente de produção, normalmente, utilizará CREATE TABLE AS SELECT para carregar para uma tabela de testes. Enquanto os dados estiverem na tabela de teste, pode efetuar quaisquer transformações necessárias. Para acrescentar os dados na tabela de teste a uma tabela de produção, pode utilizar a instrução INSERT...SELECT. Para obter mais informações, veja [Inserir dados na tabela de produção](guidance-for-loading-data.md#inserting-data-into-a-production-table).
> 

O script utiliza a instrução T-SQL [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) para carregar os dados do Azure Storage Blob para novas tabelas no armazém de dados. CTAS cria uma nova tabela com base nos resultados de uma instrução select. A nova tabela tem as mesmas colunas e tipos de dados dos resultados da instrução select. Quando a instrução select seleciona de uma tabela externa, o SQL Data Warehouse importa os dados para uma tabela relacional no armazém de dados. 

1. Execute o script seguinte para carregar os dados para novas tabelas no armazém de dados.

    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    CREATE TABLE [dbo].[Geography]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    CREATE TABLE [dbo].[Medallion]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    CREATE TABLE [dbo].[Time]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    CREATE TABLE [dbo].[Weather]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    CREATE TABLE [dbo].[Trip]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

2. Veja os dados à medida que são carregados. Está a carregar vários GBs de dados e a comprimi-los em Índices columnstore em cluster de elevado desempenho. Execute a seguinte consulta que utiliza vistas de gestão dinâmica (DMVs) para mostrar o estado do carregamento. Depois de iniciar a consulta, agarre num café e num snack enquanto o SQL Data Warehouse faz alguns carregamentos pesados.

    ```sql
    SELECT
        r.command,
        s.request_id,
        r.status,
        count(distinct input_name) as nbr_files,
        sum(s.bytes_processed)/1024/1024/1024.0 as gb_processed
    FROM 
        sys.dm_pdw_exec_requests r
        INNER JOIN sys.dm_pdw_dms_external_work s
        ON r.request_id = s.request_id
    WHERE
        r.[label] = 'CTAS : Load [dbo].[Date]' OR
        r.[label] = 'CTAS : Load [dbo].[Geography]' OR
        r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
        r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
        r.[label] = 'CTAS : Load [dbo].[Time]' OR
        r.[label] = 'CTAS : Load [dbo].[Weather]' OR
        r.[label] = 'CTAS : Load [dbo].[Trip]'
    GROUP BY
        r.command,
        s.request_id,
        r.status
    ORDER BY
        nbr_files desc, 
        gb_processed desc;
    ```

3. Veja todas as consultas de sistema.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Veja os seus dados devidamente carregados para o armazém de dados e deleite-se.

    ![Ver tabelas carregadas](media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="authenticate-using-managed-identities-to-load-optional"></a>Autenticar usando identidades gerenciadas para carregar (opcional)
O carregamento usando o polybase e a autenticação por meio de identidades gerenciadas é o mecanismo mais seguro e permite que você aproveite os pontos de extremidade do serviço de VNet com o armazenamento do Azure. 

### <a name="prerequisites"></a>Pré-requisitos
1.  Instale Azure PowerShell usando este [guia](https://docs.microsoft.com/powershell/azure/install-az-ps).
2.  Se você tiver uma conta de armazenamento de blob v1 ou de uso geral, deverá primeiro atualizar para a versão v2 de uso geral usando este [guia](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
3.  Você deve ter **permitir que os serviços confiáveis da Microsoft acessem essa conta de armazenamento** ativada em firewalls da conta de armazenamento do Azure e no menu **de configurações de redes virtuais** . Consulte este [guia](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) para obter mais informações.

#### <a name="steps"></a>Passos
1. No PowerShell, **Registre seu servidor de banco de dados SQL** com Azure Active Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```
    
   1. Crie uma **conta de armazenamento de uso geral v2** usando este [guia](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

   > [!NOTE]
   > - Se você tiver uma conta de armazenamento de blob v1 ou de uso geral, deverá **primeiro atualizar para v2** usando este [guia](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
    
1. Em sua conta de armazenamento, navegue até **controle de acesso (iam)** e clique em **Adicionar atribuição de função**. Atribuir função de RBAC de **colaborador de dados de blob de armazenamento** ao servidor do banco de dados SQL.

   > [!NOTE] 
   > Somente membros com privilégio de proprietário podem executar esta etapa. Para várias funções internas para recursos do Azure, consulte este [guia](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  
1. **Conectividade do polybase com a conta de armazenamento do Azure:**
    
   1. Crie a credencial no escopo do banco de dados com **Identity = ' identidade de serviço gerenciada '** :

       ```SQL
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```
       > [!NOTE] 
       > - Não é necessário especificar o segredo com a chave de acesso de armazenamento do Azure porque esse mecanismo usa a [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) nos bastidores.
       > - O nome da identidade deve ser **' identidade de serviço gerenciada '** para que a conectividade do polybase funcione com a conta de armazenamento do Azure.
    
   1. Crie a fonte de dados externa especificando a credencial no escopo do banco de dado com o Identidade de Serviço Gerenciada.
        
   1. Consultar normalmente usando [tabelas externas](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql).

Consulte a [documentação](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) a seguir se desejar configurar pontos de extremidade de serviço de rede virtual para SQL data warehouse. 

## <a name="clean-up-resources"></a>Limpar recursos

Estão a ser-lhe cobrados os recursos de computação e os dados que carregou para o armazém de dados. São faturados em separado. 

- Se quiser manter os dados no armazenamento, pode interromper a computação quando não estiver a utilizar o armazém de dados. Ao colocar a computação em pausa, só lhe será cobrado o armazenamento de dados e pode retomar a computação quando estiver pronto para trabalhar com os dados.
- Se quiser remover futuras cobranças, pode eliminar o armazém de dados. 

Siga estes passos para limpar os recursos conforme quiser.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) e clique no seu armazém de dados.

    ![Limpar recursos](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Para interromper a computação, clique no botão **Pausar**. Quando o armazém de dados estiver em pausa, verá um botão **Iniciar**.  Para retomar a computação, clique em **Iniciar**.

3. Para remover o armazém de dados para não lhe vir a ser cobrada a computação ou o armazenamento, clique em **Eliminar**.

4. Para remover o SQL Server que você criou, clique em **mynewserver-20180430.Database.Windows.net** na imagem anterior e, em seguida, clique em **excluir**.  Tenha cuidado, uma vez que eliminar o servidor também eliminará todas as bases de dados atribuídas ao mesmo.

5. Para remover o grupo de recursos, clique em **myResourceGroup** e, em seguida, clique em **Eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes 
Neste tutorial, aprendeu a criar um armazém de dados e a criar um utilizador para carregar dados. Criou tabelas externas para definir a estrutura dos dados armazenados no Azure Storage Blob e, em seguida, utilizou a instrução PolyBase CREATE TABLE AS SELECT para carregar dados para o armazém de dados. 

Fez tudo isto:
> [!div class="checklist"]
> * Criou um armazém de dados no portal do Azure
> * Configurar uma regra de firewall ao nível do servidor no portal do Azure
> * Ligou ao armazém de dados com o SSMS
> * Criou um utilizador designado para carregar dados
> * Criou tabelas externas para dados no Azure Storage Blob
> * Utilizou a instrução CTAS T-SQL para carregar dados para o seu armazém de dados
> * Viu o progresso dos dados à medida que são carregados
> * Criou estatísticas dos dados recentemente carregados

Avance para a visão geral de desenvolvimento para saber como migrar um banco de dados existente para SQL Data Warehouse.

> [!div class="nextstepaction"]
>[Decisões de design para migrar um banco de dados existente para SQL Data Warehouse](sql-data-warehouse-overview-migrate.md)
