---
title: 'Início rápido: configurar o isolamento de carga de trabalho-T-SQL'
description: Use o T-SQL para configurar o isolamento da carga de trabalho.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 11/21/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 2a6c5ca9f7d2ceaef08b28e78b38b94a459548f5
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74304758"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>Início rápido: configurar o isolamento de carga de trabalho usando o T-SQL

Neste guia de início rápido, você criará rapidamente um grupo de carga de trabalho e um classificador para reservar recursos para o carregamento de dados. O grupo de cargas de trabalho alocará 20% dos recursos do sistema para um carregamento de dados.  O classificador de carga de trabalho atribuirá solicitações ao grupo de cargas de trabalho de cargas de dados.  Com isolamento de 20% para cargas de dados, eles são recursos garantidos para atingir SLAs.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!NOTE]
> A criação de um SQL Data Warehouse poderá resultar num novo serviço sujeito a faturação.  Para obter mais informações, veja [Preços do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Pré-requisitos
 
Este início rápido pressupõe que você já tem um SQL Data Warehouse e que tem permissões de controle de banco de dados. Se precisar de criar um, utilize [Criar e Ligar - Portal](create-data-warehouse-portal.md) para criar um armazém de dados chamado **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Iniciar sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-login-for-dataloads"></a>Criar logon para cargas de carregamento

Crie um logon de autenticação SQL Server no banco de dados `master` usando [Create login](/sql/t-sql/statements/create-login-transact-sql) para ' ELTLogin'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Criar utilizador

[Criar usuário](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), "ELTLogin", em mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Criar um grupo de cargas de trabalho
Crie um [grupo de carga de trabalho](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) para carregamentos de carga com 20% de isolamento.
```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20   
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5) 
;
```

## <a name="create-a-workload-classifier"></a>Criar um classificador de carga de trabalho

Crie um [classificador de carga de trabalho](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) para mapear ELTLogin para o grupo de carga de trabalho de carregamentos.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>Exibir grupos de cargas de trabalho e classificadores e valores de tempo de execução existentes

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

Você está sendo cobrado por unidades de data warehouse e dados armazenados em seu data warehouse. Estes recursos de computação e armazenamento são faturados em separado.

- Se quiser manter os dados no armazenamento, pode interromper a computação quando não estiver a utilizar o armazém de dados. Ao pausar a computação, você é cobrado apenas pelo armazenamento de dados. Quando você estiver pronto para trabalhar com os dados, retome a computação.
- Se quiser remover futuras cobranças, pode eliminar o armazém de dados.

Siga estas etapas para limpar os recursos.

1. Entre no [portal do Azure](https://portal.azure.com), selecione na sua data warehouse.

    ![Limpar recursos](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Para pausar a computação, selecione o botão **Pausar** . Quando o armazém de dados estiver em pausa, verá um botão **Iniciar**.  Para retomar a computação, selecione **Iniciar**.

3. Para remover o data warehouse para que você não seja cobrado pela computação ou pelo armazenamento, selecione **excluir**.

4. Para remover o SQL Server que você criou, selecione **mynewserver-20180430.Database.Windows.net** na imagem anterior e, em seguida, selecione **excluir**.  Tenha cuidado com esta eliminação, uma vez que eliminar o servidor também elimina todas as bases de dados atribuídas ao mesmo.

5. Para remover o grupo de recursos, selecione **MyResource**Group e, em seguida, selecione **excluir grupo de recursos**.

## <a name="next-steps"></a>Passos Seguintes

- Agora você criou um grupo de cargas de trabalho. Execute algumas consultas como ELTLogin para ver como elas são executadas. Consulte [Sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) para exibir consultas e o grupo de carga de trabalho atribuído.
- Para obter mais informações sobre o gerenciamento de carga de trabalho SQL Data Warehouse do Azure, consulte [Gerenciamento de carga](sql-data-warehouse-workload-management.md) de trabalho e [isolamento de carga](sql-data-warehouse-workload-isolation.md)
