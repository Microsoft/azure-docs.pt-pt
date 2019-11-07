---
title: 'Início rápido: criar um classificador de carga de trabalho-T-SQL '
description: Use o T-SQL para criar um classificador de carga de trabalho com alta importância.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 1375605b6dab385b53af9212023767003e686e60
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646296"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Início rápido: criar um classificador de carga de trabalho usando o T-SQL

Neste guia de início rápido, você criará rapidamente um classificador de carga de trabalho com alta importância para o CEO da sua organização. Esse classificador de carga de trabalho permitirá que as consultas do CEO tenham precedência sobre outras consultas com menor importância na fila.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!NOTE]
> A criação de um SQL Data Warehouse poderá resultar num novo serviço sujeito a faturação.  Para obter mais informações, veja [Preços do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido pressupõe que você já tem um SQL Data Warehouse e que tem permissões de controle de banco de dados. Se precisar de criar um, utilize [Criar e Ligar - Portal](create-data-warehouse-portal.md) para criar um armazém de dados chamado **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Iniciar sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Criar logon para TheCEO

Crie um logon de autenticação SQL Server no banco de dados `master` usando [Create login](/sql/t-sql/statements/create-login-transact-sql) para ' TheCEO '.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Criar usuário

[Criar usuário](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), "TheCEO", em mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Criar um classificador de carga de trabalho

Crie um [classificador de carga de trabalho](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) para "TheCEO" com alta importância.

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Exibir classificadores existentes

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Limpar recursos

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
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

## <a name="next-steps"></a>Passos seguintes

- Agora você criou um classificador de carga de trabalho. Execute algumas consultas como TheCEO para ver como elas são executadas. Consulte [Sys. dm _pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) para exibir consultas e a importância atribuída.
- Para obter mais informações sobre o gerenciamento de carga de trabalho SQL Data Warehouse do Azure, consulte [importância da carga](sql-data-warehouse-workload-importance.md) de trabalho e [classificação de carga](sql-data-warehouse-workload-classification.md)
- Consulte os artigos de instruções para [Configurar a importância da carga de trabalho](sql-data-warehouse-how-to-configure-workload-importance.md) e como [gerenciar e monitorar o gerenciamento de carga de trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
