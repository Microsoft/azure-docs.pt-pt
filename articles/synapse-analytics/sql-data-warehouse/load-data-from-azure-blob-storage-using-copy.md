---
title: 'Tutorial: Carregue os dados do Táxi de Nova Iorque'
description: Tutorial usa o portal Azure e o SQL Server Management Studio para carregar os dados do Táxi de Nova Iorque a partir de uma bolha de Azure para Synapse SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/31/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: d2c2673e6863725e064f3ad8561ab77eb1b051eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371529"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>Tutorial: Carregue o conjunto de dados do Táxi de Nova Iorque

Este tutorial usa a [declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) para carregar o conjunto de dados do Táxi de Nova Iorque a partir de uma conta de armazenamento de blob Azure. Este tutorial utiliza o [portal do Azure](https://portal.azure.com) e o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) para:

> [!div class="checklist"]
>
> * Criar uma piscina SQL no portal Azure
> * Configurar uma regra de firewall ao nível do servidor no portal do Azure
> * Ligar ao armazém de dados com o SSMS
> * Criar um utilizador designado para carregar dados
> * Criar as tabelas para o conjunto de dados da amostra 
> * Utilize a declaração COPY T-SQL para carregar dados no seu armazém de dados
> * Ver o progresso dos dados à medida que são carregados

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar este tutorial, transfira e instale a versão mais recente do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Faça login no [portal Azure](https://portal.azure.com/).

## <a name="create-a-blank-database"></a>Criar uma base de dados vazia

Uma piscina SQL é criada com um conjunto definido de [recursos computativos.](memory-concurrency-limits.md) A base de dados é criada dentro de um [grupo de recursos Azure](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e num [servidor lógico SQL](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Siga estes passos para criar uma base de dados em branco.

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.

2. Selecione **Bases de Dados** **da** nova página e selecione **Azure Synapse Analytics** em **destaque** na página **Nova.**

    ![criar um armazém de dados](./media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Preencha o formulário com as informações seguintes:

   | Definição            | Valor sugerido       | Descrição                                                  |
   | ------------------ | --------------------- | ------------------------------------------------------------ |
   | *Nome**            | mySampleDataWarehouse | Para nomes de bases de dados válidos, veja [Database Identifiers](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (Identificadores de Bases de Dados). |
   | **Subscrição**   | A sua subscrição     | Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
   | **Grupo de recursos** | myResourceGroup       | Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (Atribuição de nomes de regras e restrições). |
   | **Selecionar origem**  | Base de dados vazia        | Especifica para criar uma base de dados vazia. Tenha em atenção que um armazém de dados é um tipo de base de dados. |

    ![criar um armazém de dados](./media/load-data-from-azure-blob-storage-using-polybase/create-data-warehouse.png)

4. Selecione **Servidor** para criar e configurar um novo servidor para a nova base de dados. Preencha o **Novo formulário de servidor** com as seguintes informações:

    | Definição                | Valor sugerido          | Descrição                                                  |
    | ---------------------- | ------------------------ | ------------------------------------------------------------ |
    | **Nome do servidor**        | Qualquer nome globalmente exclusivo | Para nomes de servidores válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (Atribuição de nomes de regras e restrições). |
    | **Início de sessão de administrador do servidor** | Qualquer nome válido           | Para nomes de início de sessão válidos, veja [Database Identifiers](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (Identificadores de Bases de Dados). |
    | **Palavra-passe**           | Qualquer palavra-passe válida       | A sua palavra-passe deve ter, pelo menos, oito carateres e deve conter carateres de três das seguintes categorias: carateres maiúsculos, carateres minúsculos, números e carateres não alfanuméricos. |
    | **Localização**           | Nenhuma localização válida       | Para obter mais informações sobre regiões, veja [Azure Regions](https://azure.microsoft.com/regions/) (Regiões do Azure). |

    ![criar servidor](./media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Selecione **Selecionar**.

6. Selecione **o nível de desempenho** para especificar se o armazém de dados é Gen1 ou Gen2, e o número de unidades de armazém de dados.

7. Para este tutorial, selecione SQL pool **Gen2**. O slider está definido para **DW1000c** por padrão.  Experimente movê-lo para cima e para baixo para ver como funciona.

    ![configurar o desempenho](./media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Selecione **Aplicar**.
9. Na lâmina de provisionamento, selecione uma **colagem** para a base de dados em branco. Para este tutorial, utilize o valor predefinido. Para obter mais informações sobre agrupamentos, veja [Agrupamentos](/sql/t-sql/statements/collations?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

10. Agora que preencheu o formulário, **selecione Criar** para a provisionar a base de dados. O aprovisionamento demora alguns minutos.

11. Na barra de ferramentas, selecione **Notificações** para monitorizar o processo de implementação.
  
     ![O screenshot mostra o portal Azure com o painel de Notificações aberto com a Implementação em curso.](./media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall ao nível do servidor

Uma firewall ao nível do servidor que impede que aplicações e ferramentas externas se conectem ao servidor ou a quaisquer bases de dados no servidor. Para permitir a conectividade, pode adicionar regras de firewall que permitem a conectividade para endereços IP específicos.  Siga estes passos para criar uma [regra de firewall ao nível do servidor](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) para o endereço IP do cliente.

> [!NOTE]
> A Azure Synapse Analytics comunica sobre a porta 1433. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 1433 poderá não ser permitido pela firewall da rede. Em caso afirmativo, não é possível ligar-se ao seu servidor a menos que o seu departamento de TI abra a porta 1433.

1. Após a implementação concluída, selecione bases de **dados SQL** a partir do menu à esquerda e, em seguida, selecione **mySampleDatabase** na página de **bases de dados SQL.** A página geral da sua base de dados abre, mostrando-lhe o nome do servidor totalmente qualificado (como **mynewserver-20180430.database.windows.net)** e fornece opções para uma configuração posterior.

2. Copie este nome de servidor totalmente qualificado para utilizar para ligar ao seu servidor e às respetivas bases de dados nos seguintes guias de introdução. Em seguida, selecione o nome do servidor para abrir as definições do servidor.

    ![localizar nome do servidor](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)

3. Selecione o nome do servidor para abrir as definições do servidor.

    ![definições do servidor](./media/load-data-from-azure-blob-storage-using-polybase/server-settings.png)

4. Selecione **Mostrar definições da firewall**. A página **de definições de Firewall** para o servidor abre.

    ![regra de firewall do servidor](./media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png)

5. **Selecione Adicionar** o IP do cliente na barra de ferramentas para adicionar o seu endereço IP atual a uma nova regra de firewall. Uma regra de firewall consegue abrir a porta 1433 para um único endereço IP ou para um intervalo de endereços IP.

6. Selecione **Guardar**. Uma regra de firewall ao nível do servidor é criada para o seu endereço IP atual que abre a porta 1433 no servidor.

7. Selecione **OK** e, em seguida, feche a página **de definições de Firewall.**

Pode agora ligar-se ao servidor e aos seus armazéns de dados utilizando este endereço IP. A ligação funciona a partir do SQL Server Management Studio ou de outra ferramenta à sua escolha. Ao ligar, utilize a conta ServerAdmin que criou anteriormente.  

> [!IMPORTANT]
> Por predefinição, o acesso através da firewall da Base de Dados SQL está ativado para todos os serviços do Azure. Selecione **OFF** nesta página e, em seguida, selecione **Guardar** para desativar a firewall para todos os serviços Azure.

## <a name="get-the-fully-qualified-server-name"></a>Obter o nome de servidor completamente qualificado

Obtenha o nome do servidor totalmente qualificado para o seu servidor no portal Azure. Utilizará mais tarde o nome completamente qualificado quando ligar ao servidor.

1. Faça login no [portal Azure](https://portal.azure.com/).
2. Selecione **Azure Synapse Analytics** a partir do menu da mão esquerda e selecione a sua base de dados na página **Azure Synapse Analytics.**
3. No painel **Essentials** na página do portal do Azure da sua base de dados, localize e, em seguida, copie o **Nome do servidor**. Neste exemplo, o nome totalmente qualificado é mynewserver-20180430.database.windows.net.

    ![informações da ligação](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>Ligar ao servidor como administrador do servidor

Esta secção utiliza [o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) para estabelecer uma ligação ao seu servidor.

1. Abra o SQL Server Management Studio.

2. Na caixa de dialogo **Ligar ao Servidor**, introduza as seguintes informações:

    | Definição        | Valor sugerido                            | Descrição                                                  |
    | -------------- | ------------------------------------------ | ------------------------------------------------------------ |
    | Tipo de servidor    | Motor de base de dados                            | Este valor é obrigatório                                       |
    | Nome do servidor    | O nome de servidor completamente qualificado            | O nome deve ser assim: **mynewserver-20180430.database.windows.net.** |
    | Autenticação | Autenticação do SQL Server                  | A Autenticação do SQL é o único tipo de autenticação que configurámos neste tutorial. |
    | Iniciar sessão          | A conta de administrador do servidor                   | Esta é a conta que especificou quando criou o servidor. |
    | Palavra-passe       | A palavra-passe da sua conta de administrador do servidor | Esta é a palavra-passe que especificou quando criou o servidor. |

    ![ligar ao servidor](./media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

3. Selecione **Ligar**. A janela do Object Explorer é aberta no SSMS.

4. No Object Explorer, expanda **Databases**. Em seguida, expanda **Bases de dados do sistema** e **mestre** para ver os objetos na base de dados mestra.  Expanda **mySampleDatabase** para ver os objetos na nova base de dados.

    ![objetos da base de dados](./media/load-data-from-azure-blob-storage-using-polybase/connected.png)

## <a name="create-a-user-for-loading-data"></a>Criar um utilizador para carregar dados

A conta de administrador do servidor destina-se a efetuar operações de gestão e não é adequada para executar consultas em dados do utilizador. O carregamento de dados é uma operação de memória intensiva. Os máximos de memória são definidos de acordo com as unidades de [armazém de dados](what-is-a-data-warehouse-unit-dwu-cdwu.md) e [a classe de recursos](resource-classes-for-workload-management.md) configurada.

É melhor criar um início de sessão e utilizador dedicado para carregar dados. Em seguida, adicione o utilizador de carregamento a uma [classe de recursos](resource-classes-for-workload-management.md) que permita uma alocação de memória máxima adequada.

Uma vez que está atualmente ligado como o administrador do servidor, pode criar inícios de sessão e utilizadores. Utilize estes passos para criar um início de sessão e o utilizador com o nome **LoaderRC20**. Em seguida, atribua o utilizador à classe de recursos **staticrc20**.

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

Está pronto para iniciar o processo de carregamento de dados para o novo armazém de dados. Esta parte do tutorial mostra-lhe como usar a declaração COPY para carregar o conjunto de dados do táxi de Nova Iorque a partir de uma bolha de armazenamento Azure. Para referência futura, para aprender a levar os seus dados para o armazenamento de blob Azure ou para carregá-lo diretamente da sua fonte, consulte a [visão geral](design-elt-data-loading.md)de carregamento .

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

Esta secção utiliza a [declaração COPY para carregar](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) os dados da amostra a partir da Bolha de Armazenamento Azure.  

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

5. Para remover o grupo de recursos, selecione **myResourceGroup**e, em seguida, **selecione Delete resource group**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar um armazém de dados e a criar um utilizador para carregar dados. Usou a simples [declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#examples) para carregar dados no seu armazém de dados.

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

- [Documentação de referência de declaração de CÓPIA](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [Exemplos de COPY para cada método de autenticação](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)
- [INÍCIO DE CÓPIA para uma única tabela](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql)
