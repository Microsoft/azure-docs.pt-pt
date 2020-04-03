---
title: 'Quickstart: Configure o isolamento da carga de trabalho - T-SQL'
description: Utilize o T-SQL para configurar o isolamento da carga de trabalho.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: c4920b2a5b4ff0b1a94fa8fa0e83f72761802b97
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583804"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>Quickstart: Configure o isolamento da carga de trabalho utilizando t-SQL

Neste arranque rápido, criará rapidamente um grupo de carga de trabalho e um classificador para reservar recursos para o carregamento de dados. O grupo de carga de trabalho irá atribuir 20% dos recursos do sistema a uma carga de dados.  O classificador de carga de trabalho atribuirá pedidos ao grupo de carga de trabalho de carga de carga de carga de carga de carga de dados.  Com 20% de isolamento para cargas de dados, são recursos garantidos para atingir Os SLAs.

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!NOTE]
> A criação de uma piscina SQL Synapse em Azure Synapse Analytics pode resultar num novo serviço de faturação.  Para mais informações, consulte o preço da [Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Pré-requisitos
 
Este quickstart assume que já tem uma piscina SYnapse SQL em Azure Synapse e que tem permissões CONTROL DATABASE. Se precisar de criar um, utilize [Criar e Ligar - Portal](create-data-warehouse-portal.md) para criar um armazém de dados chamado **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-login-for-dataloads"></a>Criar login para DataLoads

Crie um login de autenticação `master` do Servidor SQL na base de dados utilizando [o CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para 'ELTLogin'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Criar utilizador

[Criar utilizador](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), "ELTLogin", no mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Criar um grupo de carga de trabalho
Crie um [grupo de carga de trabalho](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para DataLoads com 20% de isolamento.
```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20   
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5) 
;
```

## <a name="create-a-workload-classifier"></a>Criar um classificador de carga de trabalho

Crie um [classificador](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) de carga de trabalho para mapear ELTLogin para o grupo de carga de trabalho DataLoads.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>Ver grupos de carga de trabalho existentes e classificadores e valores de tempo de execução

```sql
--Workload groups
SELECT * FROM 
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM 
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM 
sys.dm_workload_management_workload_groups_stats
```

## <a name="clean-up-resources"></a>Limpar recursos

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

Está a ser cobrado por unidades de armazém de dados e dados armazenados no seu armazém de dados. Estes recursos de computação e armazenamento são faturados em separado.

- Se quiser manter os dados armazenados, pode parar a computação quando não estiver a utilizar a piscina SQL. Ao fazer uma pausa na computação, só é cobrado para armazenamento de dados. Quando estiver pronto para trabalhar com os dados, retome a computação.
- Se quiser remover futuras cobranças, pode eliminar o armazém de dados.

Siga estes passos para limpar recursos.

1. Inscreva-se no [portal Azure,](https://portal.azure.com)selecione no seu armazém de dados.

    ![Limpar recursos](./media/quickstart-configure-workload-isolation-tsql/clean-up-resources.png)

2. Para parar a computação, selecione o botão **Pausa.** Quando o armazém de dados estiver em pausa, verá um botão **Iniciar**.  Para retomar a computação, selecione **Iniciar**.

3. Para remover o armazém de dados para que não seja cobrado para computação ou armazenamento, **selecione Eliminar**.

4. Para remover o servidor SQL que criou, selecione **mynewserver-20180430.database.windows.net** na imagem anterior e, em seguida, selecione **Delete**.  Tenha cuidado com esta eliminação, uma vez que eliminar o servidor também elimina todas as bases de dados atribuídas ao mesmo.

5. Para remover o grupo de recursos, selecione **myResourceGroup**, e, em seguida, **selecione Eliminar o grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

- Agora criaste um grupo de carga de trabalho. Faça algumas perguntas como ELTLogin para ver como se apresentam. Consulte [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para ver consultas e o grupo de carga de trabalho atribuído.
- Para obter mais informações sobre a gestão da carga de trabalho Synapse SQL, consulte [a Gestão da Carga de Trabalho](sql-data-warehouse-workload-management.md) e o Isolamento da Carga de [Trabalho.](sql-data-warehouse-workload-isolation.md)
