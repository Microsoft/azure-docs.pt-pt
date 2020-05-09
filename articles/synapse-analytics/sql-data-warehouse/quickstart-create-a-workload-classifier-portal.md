---
title: 'Quickstart: Criar um classificador de carga de trabalho - Portal'
description: Utilize o portal Azure para criar um classificador de carga de trabalho com grande importância.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 05/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9b67d3205e95fe7cca6cacaab7e82a1a7e71f3f3
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82794106"
---
# <a name="quickstart-create-a-synapse-sql-pool-workload-classifier-using-the-azure-portal"></a>Quickstart: Criar um classificador de carga de trabalho synapse SQL usando o portal Azure

Neste arranque rápido, você vai criar um [classificador](sql-data-warehouse-workload-classification.md) de carga de trabalho para atribuir consultas a um grupo de carga de trabalho.  O classificador atribuirá pedidos do `ELTLogin` utilizador SQL `DataLoads` ao grupo de carga de trabalho.   Siga o [Quickstart: Configure](quickstart-configure-workload-isolation-portal.md) o `DataLoads` tutorial de isolamento da carga de trabalho para criar o grupo de carga de trabalho.  Este tutorial criará um classificador de carga de trabalho com a opção WLM_LABEL para ajudar a classificar ainda mais os pedidos corretamente.  O classificador atribuirá `HIGH` [também importância](sql-data-warehouse-workload-importance.md) à carga de trabalho a estes pedidos.


Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.


## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

> [!NOTE]
> A criação de uma instância de piscina SQL no Azure Synapse Analytics pode resultar num novo serviço de faturação.  Para mais informações, consulte o preço da [Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Pré-requisitos

Este quickstart pressupõe que já tem uma instância de piscina SQL no Synapse SQL e que tem permissões de BASE DE DADOS CONTROL. Se precisar de criar um, utilize [Criar e Ligar - Portal](create-data-warehouse-portal.md) para criar um armazém de dados chamado **mySampleDataWarehouse**.
<br><br>
Existe um `DataLoads` grupo de carga de trabalho.  Consulte o [Quickstart: Configure](quickstart-configure-workload-isolation-portal.md) o tutorial de isolamento da carga de trabalho para criar o grupo de carga de trabalho.
<br><br>
>[!IMPORTANT] 
>A sua piscina SQL deve estar online para configurar a gestão da carga de trabalho. 


## <a name="create-a-login-for-eltlogin"></a>Crie um login para ELTLogin

Crie um login de autenticação `master` do Servidor SQL na base de dados utilizando [o CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para `ELTLogin`.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user-and-grant-permissions"></a>Criar permissões de utilizador e concessão

Após a criação do login, um utilizador precisa de ser criado na base de dados.  Utilize o [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para `ELTRole` criar o utilizador SQL no **mySampleDataWarehouse**.  Uma vez que vamos testar a `ELTLogin` classificação durante este tutorial, conceda permissões ao **mySampleDataWarehouse**. 

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
GRANT CONTROL ON DATABASE::mySampleDataWarehouse TO ELTLogin 
END
;
```

## <a name="configure-workload-classification"></a>Configurar a classificação da carga de trabalho
A classificação permite-lhe encaminhar pedidos, com base num conjunto de regras, para um grupo de carga de trabalho.  No [Quickstart: Configure](quickstart-configure-workload-isolation-portal.md) tutorial de `DataLoads` isolamento de carga de trabalho criamos o grupo de carga de trabalho.  Agora vai criar um classificador de carga `DataLoads` de trabalho para encaminhar consultas para o grupo de carga de trabalho.


1.  Clique em **Azure Synapse Analytics (anteriormente SQL DW)** na página esquerda do portal Azure.
2.  Selecione **mySampleDataWarehouse** da página **Azure Synapse Analytics (anteriormente SQL DW).** A piscina SQL abre.
3.  Clique na **gestão da carga de trabalho.**

    ![Clique no Menu](./media/quickstart-create-a-workload-classifier-portal/menu.png)

4.  Clique em **Definições & classificadores** no lado `DataLoads` direito do grupo de carga de trabalho.

    ![Clique em Criar](./media/quickstart-create-a-workload-classifier-portal/settings-classifiers.png)

5. Clique em **Classifiers**.
6. Clique em **Adicionar classifier**.

    ![Clique em Adicionar](./media/quickstart-create-a-workload-classifier-portal/add-wc.png)

7.  Introduza `ELTLoginDataLoads` para **Nome**.
8.  Insira `ELTLogin` para **Membro**.
9.  Escolha `High` por **Importância de Pedido**.  *Opcional,* a importância normal é o padrão.
10. Introduza `fact_loads` para **Etiqueta**.
11. Clique em **Adicionar**.
12. Clique em **Guardar**.

    ![Clique em Config](./media/quickstart-create-a-workload-classifier-portal/config-wc.png)

## <a name="verify-and-test-classification"></a>Verificar e testar classificação
Verifique a vista de catálogo [sys.workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql?view=azure-sqldw-latest) para verificar a existência do `ELTLoginDataLoads` classificador.

```sql
SELECT * FROM sys.workload_management_workload_classifiers WHERE name = 'ELTLoginDataLoads'
```

Verifique a vista de catálogo [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest) para verificar os detalhes do classifier.

```sql
SELECT c.[name], c.group_name, c.importance, cd.classifier_type, cd.classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ELTLoginDataLoads'
```

Executar as seguintes declarações para testar classificação.  Certifique-se de ``ELTLogin`` ``Label`` que está ligado como e é utilizado em consulta.
```sql
CREATE TABLE factstaging (ColA int)
INSERT INTO factstaging VALUES(0)
INSERT INTO factstaging VALUES(1)
INSERT INTO factstaging VALUES(2)
GO

CREATE TABLE testclassifierfact WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT * FROM factstaging
OPTION (LABEL='fact_loads')
```

Verifique `CREATE TABLE` a declaração `DataLoads` classificada para `ELTLoginDataLoads` o grupo de carga de trabalho utilizando o classificador de carga de trabalho.
```sql 
SELECT TOP 1 request_id, classifier_name, group_name, resource_allocation_percentage, submit_time, [status], [label], command 
FROM sys.dm_pdw_exec_requests 
WHERE [label] = 'fact_loads'
ORDER BY submit_time DESC
```



## <a name="clean-up-resources"></a>Limpar recursos

Para eliminar `ELTLoginDataLoads` o classificador de carga de trabalho criado neste tutorial:

1. Clique em **1 Classifier** no lado `DataLoads` direito do grupo de carga de trabalho.

    ![Clique em Excluir](./media/quickstart-create-a-workload-classifier-portal/delete-wc.png)

2. Clique em **Classifiers**.
3. Clique à **`...`** direita do `ELTLoginDataLoads` classificador de carga de trabalho.
4. Clique em **Eliminar**.
5. Clique em **Guardar**.

    ![Clicar em Guardar](./media/quickstart-create-a-workload-classifier-portal/delete-save-wc.png)

Está a ser cobrado por unidades de armazém de dados e dados armazenados no seu armazém de dados. Estes recursos de computação e armazenamento são faturados em separado.

- Se quiser manter os dados no armazenamento, pode interromper a computação quando não estiver a utilizar o armazém de dados. Ao fazer uma pausa na computação, só é cobrado para armazenamento de dados. Quando estiver pronto para trabalhar com os dados, retome a computação.
- Se quiser remover futuras cobranças, pode eliminar o armazém de dados.

Siga estes passos para limpar recursos.

1. Inscreva-se no [portal Azure,](https://portal.azure.com)selecione no seu armazém de dados.

    ![Limpar recursos](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Para parar a computação, selecione o botão **Pausa.** Quando o armazém de dados estiver em pausa, verá um botão **Iniciar**.  Para retomar a computação, selecione **Iniciar**.

3. Para remover o armazém de dados para que não seja cobrado para computação ou armazenamento, **selecione Eliminar**.

4. Para remover o servidor SQL que criou, selecione **sqlpoolservername.database.windows.net** na imagem anterior e, em seguida, selecione **Delete**.  Tenha cuidado com esta eliminação, uma vez que eliminar o servidor também elimina todas as bases de dados atribuídas ao mesmo.

5. Para remover o grupo de recursos, selecione **myResourceGroup**, e, em seguida, **selecione Eliminar o grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

Monitorize a sua carga de trabalho utilizando as métricas de monitorização do portal Azure.  Consulte gerir e monitorizar a [Gestão da Carga de Trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) para obter mais detalhes.
