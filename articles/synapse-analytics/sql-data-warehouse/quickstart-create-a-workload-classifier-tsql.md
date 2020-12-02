---
title: 'Quickstart: Criar um classificador de carga de trabalho - T-SQL'
description: Utilize o T-SQL para criar um classificador de carga de trabalho de elevada importância.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: a63cd95fdcee7c9ed0c49ba41b4d7e7e6de8f4bf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458727"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Quickstart: Criar um classificador de carga de trabalho utilizando T-SQL

Neste arranque rápido, criará rapidamente um classificador de carga de trabalho com grande importância para o CEO da sua organização. Este classificador de carga de trabalho permitirá que as consultas de CEO têm precedência sobre outras consultas com menor importância na fila.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!NOTE]
> A criação de uma instância dedicada da piscina SQL no Azure Synapse Analytics pode resultar num novo serviço faturado.  Para mais informações, consulte [os preços do Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Pré-requisitos

Este quickstart pressupõe que já forneceste uma piscina SQL dedicada em Azure Synapse Analytics e que tens permissões de BASE DE DADOS DE CONTROLO. Se precisar de criar um, utilize o [portal Create and Connect -](create-data-warehouse-portal.md) para criar uma piscina SQL dedicada chamada **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Criar login para TheCEO

Crie um login de autenticação sql Server na base de dados utilizando o `master` CREATE [LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para 'TheCEO'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Criar utilizador

[Criar utilizador](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), "TheCEO", no mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Criar um classificador de carga de trabalho

Crie um [classificador de carga de trabalho](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para "TheCEO" com grande importância.

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Ver classificadores existentes

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Limpar os recursos

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

Está a ser cobrado por unidades de armazém de dados e dados armazenados na sua piscina de SQL dedicada. Estes recursos de computação e armazenamento são faturados em separado.

- Se quiser manter os dados armazenados, pode parar o cálculo quando não estiver a utilizar a piscina SQL dedicada. Ao fazer uma pausa no cálculo, só é cobrado para armazenamento de dados. Quando estiver pronto para trabalhar com os dados, retome o cálculo.
- Se quiser remover as cargas futuras, pode eliminar a piscina SQL dedicada.

Siga estes passos para limpar os recursos.

1. Inscreva-se no [portal Azure,](https://portal.azure.com)selecione a sua piscina SQL dedicada.

    ![Limpar os recursos](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Para parar o cálculo, selecione o **botão Pausa.** Quando a piscina SQL dedicada é pausada, vê-se um botão **Iniciar.**  Para retomar o cálculo, selecione **Start**.

3. Para remover a piscina SQL dedicada para que não seja cobrado para computação ou armazenamento, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

- Agora criaste um classificador de carga de trabalho. Faça algumas perguntas como TheCEO para ver como se apresentam. Consulte [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para visualizar as consultas e a importância atribuída.
- Para obter mais informações sobre a gestão dedicada da carga de trabalho do pool SQL, consulte [a Classificação da Importância da Carga de Trabalho](sql-data-warehouse-workload-importance.md) e da Carga de [Trabalho.](sql-data-warehouse-workload-classification.md)
- Consulte os artigos de como configurar a [importância da carga de trabalho](sql-data-warehouse-how-to-configure-workload-importance.md) e como gerir e monitorizar a [Gestão da Carga de Trabalho.](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
