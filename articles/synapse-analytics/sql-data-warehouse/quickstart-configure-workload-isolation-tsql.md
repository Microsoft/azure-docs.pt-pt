---
title: 'Quickstart: Configure o isolamento da carga de trabalho - T-SQL'
description: Utilize o T-SQL para configurar o isolamento da carga de trabalho.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/27/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 99c64e703158c40c2cc110a18be7b8c8d3800ff0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82207807"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>Quickstart: Configure o isolamento da carga de trabalho utilizando t-SQL

Neste arranque rápido, criará rapidamente um grupo de carga de trabalho e um classificador para reservar recursos para o carregamento de dados. O grupo de carga de trabalho irá atribuir 20% dos recursos do sistema a uma carga de dados.  O classificador de carga de trabalho atribuirá pedidos ao grupo de carga de trabalho de carga de carga de carga de carga de carga de dados.  Com 20% de isolamento para cargas de dados, são recursos garantidos para atingir Os SLAs.

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!NOTE]
> A criação de um caso SQL Analytics no Azure Synapse Analytics pode resultar num novo serviço de faturação.  Para mais informações, consulte o preço da [Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Pré-requisitos

Este quickstart pressupõe que já tem uma instância SQL Analytics em Azure Synapse e que tem permissões de BASE DE DADOS CONTROL. Se precisar de criar um, utilize [Criar e Ligar - Portal](create-data-warehouse-portal.md) para criar um armazém de dados chamado **mySampleDataWarehouse**.

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

## <a name="next-steps"></a>Passos seguintes

- Agora criaste um grupo de carga de trabalho. Faça algumas perguntas como ELTLogin para ver como se apresentam. Consulte [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para ver consultas e o grupo de carga de trabalho atribuído.
- Para obter mais informações sobre a gestão da carga de trabalho Synapse SQL, consulte [a Gestão da Carga de Trabalho](sql-data-warehouse-workload-management.md) e o Isolamento da Carga de [Trabalho.](sql-data-warehouse-workload-isolation.md)
