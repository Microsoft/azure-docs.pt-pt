---
title: 'Quickstart: Configure workload isolation - T-SQL'
description: Utilize o T-SQL para configurar o isolamento da carga de trabalho.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/27/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9d69c1708e73ad7ce5610a0683835e9f304c3f54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98679759"
---
# <a name="quickstart-configure-workload-isolation-in-a-dedicated-sql-pool-using-t-sql"></a>Quickstart: Configurar o isolamento da carga de trabalho numa piscina SQL dedicada usando T-SQL

Neste arranque rápido, criará rapidamente um grupo de carga de trabalho e classificará para reservar recursos para o carregamento de dados. O grupo de trabalho irá alocar 20% dos recursos do sistema às cargas de dados.  O classificador de carga de trabalho atribuirá pedidos ao grupo de carga de carga de dados.  Com 20% de isolamento para cargas de dados, são recursos garantidos para atingir SLAs.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!NOTE]
> A criação de um exemplo de SQL synapse em Azure Synapse Analytics pode resultar num novo serviço faturador.  Para mais informações, consulte [os preços do Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Pré-requisitos

Este quickstart pressupõe que já tem uma instância Sinapse SQL em Azure Synapse e que tem permissões de BASE DE DADOS DE CONTROLO. Se precisar de criar um, utilize o [portal Create and Connect -](create-data-warehouse-portal.md) para criar uma piscina SQL dedicada chamada **mySampleDataWarehouse**.

## <a name="create-login-for-dataloads"></a>Criar login para DataLoads

Crie um login de autenticação SQL Server na base de dados utilizando o `master` CREATE [LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para 'ELTLogin'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Criar utilizador

[Criar utilizador](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), "ELTLogin", no mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Criar um grupo de carga de trabalho

Crie um [grupo de carga de trabalho](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para DataLoads com 20% de isolamento.

```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5)
;
```

## <a name="create-a-workload-classifier"></a>Criar um classificador de carga de trabalho

Crie um [classificador de carga de trabalho](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para mapear o ELTLogin para o grupo de carga de trabalho DataLoads.

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

## <a name="clean-up-resources"></a>Limpar os recursos

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

Está a ser cobrado por unidades de armazém de dados e dados armazenados na sua piscina de SQL dedicada. Estes recursos de computação e armazenamento são faturados em separado.

- Se quiser manter os dados armazenados, pode parar o cálculo quando não estiver a utilizar a piscina SQL dedicada. Ao fazer uma pausa no cálculo, só é cobrado para armazenamento de dados. Quando estiver pronto para trabalhar com os dados, retome o cálculo.
- Se quiser remover as cargas futuras, pode eliminar a piscina SQL dedicada.

## <a name="next-steps"></a>Passos seguintes

- Agora criaste um grupo de trabalho. Execute algumas consultas como ELTLogin para ver como se apresentam. Consulte [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para visualizar consultas e o grupo de trabalho atribuído.
- Para obter mais informações sobre a gestão da carga de trabalho Sinaapse SQL, consulte [a Gestão da Carga de Trabalho](sql-data-warehouse-workload-management.md) e o Isolamento da Carga de [Trabalho.](sql-data-warehouse-workload-isolation.md)
