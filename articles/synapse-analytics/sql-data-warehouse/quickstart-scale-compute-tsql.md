---
title: 'Quickstart: Scale compute in dedicated SQL pool (anteriormente SQL DW) - T-SQL'
description: Cálculo de escala em piscina sql dedicada (anteriormente SQL DW) usando T-SQL e SQL Server Management Studio (SSMS). Dimensionar a computação para um melhor desempenho ou a escalar a computação novamente para reduzir os custos.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 37033e3c5f388d1a55a122899114914e661565f6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460231"
---
# <a name="quickstart-scale-compute-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-using-t-sql"></a>Quickstart: Cálculo de escala para piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics usando T-SQL

Cálculo de escala em piscina sql dedicada (anteriormente SQL DW) usando T-SQL e SQL Server Management Studio (SSMS). [Dimensionar a computação](sql-data-warehouse-manage-compute-overview.md) para um melhor desempenho ou a escalar a computação novamente para reduzir os custos.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Before you begin

Transfira e instale a versão mais recente do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).

## <a name="create-a-dedicated-sql-pool-formerly-sql-dw"></a>Crie uma piscina SQL dedicada (anteriormente SQL DW)

Use [Quickstart: create and Connect - portal](create-data-warehouse-portal.md) para criar uma piscina SQL dedicada (anteriormente SQL DW) chamada **mySampleDataWarehouse**. Complete o quickstart para garantir que tem uma regra de firewall e pode ligar-se à sua piscina SQL (anteriormente SQL DW) a partir do SqL Server Management Studio.

## <a name="connect-to-the-server-as-server-admin"></a>Ligar ao servidor como administrador do servidor

Esta secção utiliza o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) para estabelecer uma ligação ao servidor SQL do Azure.

1. Abra o SQL Server Management Studio.

2. Na caixa de dialogo **Ligar ao Servidor**, introduza as seguintes informações:

   | Definição       | Valor sugerido | Descrição |
   | ------------ | ------------------ | ------------------------------------------------- |
   | Tipo de servidor | Motor de base de dados | Este valor é obrigatório |
   | Nome do servidor | O nome de servidor completamente qualificado | Aqui está um exemplo: **mySampleDataWarehouseservername.database.windows.net.** |
   | Autenticação | Autenticação do SQL Server | A Autenticação do SQL é o único tipo de autenticação configurado neste tutorial. |
   | Iniciar sessão | A conta de administrador do servidor | A conta que especificou quando criou o servidor. |
   | Palavra-passe | A palavra-passe da sua conta de administrador do servidor | A palavra-passe que especificou quando criou o servidor. |

    ![Ligar ao servidor](./media/quickstart-scale-compute-tsql/connect-to-server.png)

3. Clique em **Ligar**. A janela do Object Explorer é aberta no SSMS.

4. No Object Explorer, expanda **Databases**. Em **seguida, expanda o mySampleDataWarehouse** para ver os objetos na sua nova base de dados.

    ![Objetos de base de dados](./media/quickstart-scale-compute-tsql/connected.png)

## <a name="view-service-objective"></a>Ver objetivo do serviço

A definição do objetivo de serviço contém o número de unidades de armazém de dados para a piscina de SQL dedicada (anteriormente SQL DW).

Para ver as atuais unidades de armazém de dados para a sua piscina SQL dedicada (anteriormente SQL DW):

1. Sob a ligação a **mySampleDataWarehouseservername.database.windows.net,** expandir **bases de dados do sistema**.
2. Clique com o botão direito do rato em **master** (mestra) e selecione **New Query** (Nova Consulta). É aberta uma nova janela de consulta.
3. Execute a seguinte consulta para selecionar a partir da vista de gestão dinâmica sys.database_service_objectives.

    ```sql
    SELECT
        db.name [Database]
    ,    ds.edition [Edition]
    ,    ds.service_objective [Service Objective]
    FROM
         sys.database_service_objectives ds
    JOIN
        sys.databases db ON ds.database_id = db.database_id
    WHERE
        db.name = 'mySampleDataWarehouse'
    ```

4. Os seguintes resultados mostram **mySampleDataWarehouse** tem um objetivo de serviço de DW400.

    ![iew-current-dwu](./media/quickstart-scale-compute-tsql/view-current-dwu.png)

## <a name="scale-compute"></a>Dimensionar computação

No pool de SQL dedicado (anteriormente SQL DW), pode aumentar ou diminuir os recursos computativos ajustando unidades de armazém de dados. O [Criar e Ligar - portal](create-data-warehouse-portal.md) criou **mySampleDataWarehouse** e inicializou-o com 400 DWUs. Os seguintes passos ajustam as DWUs para **mySampleDataWarehouse**.

Para alterar as unidades do data warehouse:

1. Clique com o botão direito do rato em **master** (mestra) e selecione **New Query** (Nova Consulta).
2. Utilize o [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) instrução de T-SQL para modificar o objetivo de serviço. Execute a consulta seguinte para alterar o objetivo de serviço para DW300.

    ```Sql
    ALTER DATABASE mySampleDataWarehouse
    MODIFY (SERVICE_OBJECTIVE = 'DW300c');
    ```

## <a name="monitor-scale-change-request"></a>Pedido de alteração de dimensionamento do monitor

Para ver o progresso do pedido de alteração anterior, pode utilizar a sintaxe de T-SQL `WAITFORDELAY` para consultar a vista de gestão dinâmica sys.dm_operation_status (DMV).

Para consultar o estado de alteração do objeto do serviço:

1. Clique com o botão direito do rato em **master** (mestra) e selecione **New Query** (Nova Consulta).
2. Execute a consulta seguinte para consultar o sys.dm_operation_status DMV.

    ```sql
    WHILE
    (
        SELECT TOP 1 state_desc
        FROM sys.dm_operation_status
        WHERE
            1=1
            AND resource_type_desc = 'Database'
            AND major_resource_id = 'mySampleDataWarehouse'
            AND operation = 'ALTER DATABASE'
        ORDER BY
            start_time DESC
    ) = 'IN_PROGRESS'
    BEGIN
        RAISERROR('Scale operation in progress',0,0) WITH NOWAIT;
        WAITFOR DELAY '00:00:05';
    END
    PRINT 'Complete';
    ```

3. A saída resultante mostra um registo da consulta de estado.

    ![Estado da operação](./media/quickstart-scale-compute-tsql/polling-output.png)

## <a name="check-dedicated-sql-pool-formerly-sql-dw-state"></a>Verifique o estado dedicado da piscina SQL (anteriormente SQL DW)

Quando uma piscina SQL dedicada (anteriormente SQL DW) é pausada, não pode ligar-se a ele com t-SQL. Para ver o estado atual da piscina de SQL dedicada (anteriormente SQL DW), pode utilizar um cmdlet PowerShell. Por exemplo, consulte [o estado da piscina SQL dedicada (anteriormente SQL DW) - PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state).

## <a name="check-operation-status"></a>Verificar o estado da operação

Para retornar informações sobre várias operações de gestão na sua piscina de SQL dedicada (anteriormente SQL DW), execute a seguinte consulta sobre o [DMV sys.dm_operation_status.](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) Por exemplo, devolve a operação e o estado da operação, que é IN_PROGRESS ou COMPLETED.

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND
    major_resource_id = 'mySampleDataWarehouse'
```

## <a name="next-steps"></a>Passos seguintes

Agora aprendeu a escalar o cálculo para a sua piscina DE SQL dedicada (anteriormente SQL DW). Para saber mais sobre o Azure Synapse Analytics, continue ao tutorial para carregar dados.

> [!div class="nextstepaction"]
>[Carregue os dados numa piscina de SQL dedicada](load-data-from-azure-blob-storage-using-polybase.md)
