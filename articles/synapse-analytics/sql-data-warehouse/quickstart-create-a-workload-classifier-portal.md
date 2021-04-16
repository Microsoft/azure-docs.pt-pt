---
title: 'Quickstart: Criar um classificador de carga de trabalho - Portal'
description: Utilize o portal Azure para criar um classificador de carga de trabalho de elevada importância.
services: synapse-analytics
author: ronortloff
ms.author: rortloff
manager: craigg
ms.reviewer: jrasnick
ms.date: 05/04/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- azure-synapse
- mode-portal
ms.openlocfilehash: 6f1997a80e66d1ca7928afc02acf92f376fc3376
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534282"
---
# <a name="quickstart-create-a-dedicated-sql-pool-workload-classifier-using-the-azure-portal"></a>Quickstart: Criar um classificador dedicado à carga de trabalho da piscina SQL utilizando o portal Azure

Neste arranque rápido, irá criar um [classificador de carga de trabalho](sql-data-warehouse-workload-classification.md) para atribuir consultas a um grupo de carga de trabalho.  O classificador atribuirá pedidos do `ELTLogin` utilizador SQL ao `DataLoads` grupo de carga de trabalho.   Siga o [Quickstart: Configurar](quickstart-configure-workload-isolation-portal.md) o tutorial de isolamento da carga de trabalho para criar o `DataLoads` grupo de carga de trabalho.  Este tutorial criará um classificador de carga de trabalho com a opção WLM_LABEL para ajudar a classificar os pedidos corretamente.  O classificador atribuirá `HIGH` [também importância](sql-data-warehouse-workload-importance.md) à carga de trabalho a estes pedidos.


Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.


## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

> [!NOTE]
> A criação de uma instância dedicada da piscina SQL no Azure Synapse Analytics pode resultar num novo serviço faturado.  Para mais informações, consulte [os preços do Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Pré-requisitos

Este quickstart pressupõe que já tem uma instância dedicada da piscina SQL que tem permissões de BASE DE DADOS DE CONTROLO. Se precisar de criar um, utilize o [portal Create and Connect -](create-data-warehouse-portal.md) para criar uma piscina SQL dedicada chamada **mySampleDataWarehouse**.
<br><br>
Existe um grupo de `DataLoads` trabalho.  Consulte o [Quickstart: Configurar](quickstart-configure-workload-isolation-portal.md) tutorial de isolamento de carga de trabalho para criar o grupo de carga de trabalho.
<br><br>
>[!IMPORTANT] 
>A sua piscina SQL dedicada deve estar online para configurar a gestão da carga de trabalho. 


## <a name="create-a-login-for-eltlogin"></a>Criar um login para ELTLogin

Crie um login de autenticação do SQL Server na `master` base de dados utilizando [o CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para `ELTLogin` .

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user-and-grant-permissions"></a>Criar permissões de utilizador e concessão

Após a criação do login, um utilizador precisa de ser criado na base de dados.  Utilize [o UTILIZADOR CREATE](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para criar o utilizador SQL na `ELTRole` **mySampleDataWarehouse**.  Uma vez que vamos testar a classificação durante este tutorial, conceda `ELTLogin` permissões ao **mySampleDataWarehouse**. 

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
GRANT CONTROL ON DATABASE::mySampleDataWarehouse TO ELTLogin 
END
;
```

## <a name="configure-workload-classification"></a>Classificação da carga de trabalho configure
A classificação permite-lhe encaminhar os pedidos, com base num conjunto de regras, para um grupo de carga de trabalho.  No [Quickstart: Configure](quickstart-configure-workload-isolation-portal.md) o tutorial de isolamento da carga de trabalho criamos o `DataLoads` grupo de carga de trabalho.  Agora vai criar um classificador de carga de trabalho para encaminhar consultas para o `DataLoads` grupo de carga de trabalho.


1.  Navegue para a sua página de piscina dedicada SQL **mySampleDataWarehouse.**
3.  Selecione **gestão da carga de trabalho**.

    ![Clique no menu](./media/quickstart-create-a-workload-classifier-portal/menu.png)

4.  Selecione **Definições & classificadores** no lado direito do grupo de `DataLoads` carga de trabalho.

    ![Clique em Criar](./media/quickstart-create-a-workload-classifier-portal/settings-classifiers.png)

5. Selecione  **Não configurado** sob a coluna Classificadores.
6. **Selecione + Adicionar classificador**.

    ![Clique em Adicionar](./media/quickstart-create-a-workload-classifier-portal/add-wc.png)

7.  `ELTLoginDataLoads`Insira para o **nome**.
8.  `ELTLogin`Insira para **Membro**.
9.  Escolha `High` para **a importância do pedido**.  *Opcional,* a importância normal é o predefinição.
10. `fact_loads`Insira para **etiquetagem**.
11. Selecione **Adicionar**.
12. Selecione **Guardar**.

    ![Clique em Config](./media/quickstart-create-a-workload-classifier-portal/config-wc.png)

## <a name="verify-and-test-classification"></a>Verificar e testar classificação
Verifique a [vista do](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql?view=azure-sqldw-latest&preserve-view=true) catálogo sys.workload_management_workload_classifiers para verificar a existência do `ELTLoginDataLoads` classificador.

```sql
SELECT * FROM sys.workload_management_workload_classifiers WHERE name = 'ELTLoginDataLoads'
```

Verifique a [vista do](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest&preserve-view=true) catálogo sys.workload_management_workload_classifier_details para verificar os detalhes do classificador.

```sql
SELECT c.[name], c.group_name, c.importance, cd.classifier_type, cd.classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ELTLoginDataLoads'
```

Executar as seguintes declarações para testar a classificação.  Certifique-se de que está ligado ``ELTLogin`` como e é utilizado em ``Label`` consulta.
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

Verifique a `CREATE TABLE` declaração classificada para o grupo de carga de trabalho utilizando o `DataLoads` `ELTLoginDataLoads` classificador de carga de trabalho.
```sql 
SELECT TOP 1 request_id, classifier_name, group_name, resource_allocation_percentage, submit_time, [status], [label], command 
FROM sys.dm_pdw_exec_requests 
WHERE [label] = 'fact_loads'
ORDER BY submit_time DESC
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para eliminar o `ELTLoginDataLoads` classificador de carga de trabalho criado neste tutorial:

1. Clique em **1 Classificador** no lado direito do grupo de `DataLoads` carga de trabalho.

    ![Clique em Eliminar](./media/quickstart-create-a-workload-classifier-portal/delete-wc.png)

2. Clique em **Classificadores**.
3. Clique no **`...`** direito do `ELTLoginDataLoads` classificador de carga de trabalho.
4. Clique em **Apagar**.
5. Clique em **Guardar**.

    ![Clicar em Guardar](./media/quickstart-create-a-workload-classifier-portal/delete-save-wc.png)

Está a ser cobrado por unidades de armazém de dados e dados armazenados na sua piscina de SQL dedicada. Estes recursos de computação e armazenamento são faturados em separado.

- Se quiser manter os dados armazenados, pode parar o cálculo quando não estiver a utilizar a sua piscina SQL dedicada. Ao fazer uma pausa no cálculo, só é cobrado para armazenamento de dados. Quando estiver pronto para trabalhar com os dados, retome o cálculo.
- Se quiser remover as cargas futuras, pode eliminar a sua piscina SQL dedicada.

Siga estes passos para limpar os recursos.

1. Inscreva-se no [portal Azure,](https://portal.azure.com)selecione a sua piscina SQL dedicada.

    ![Limpar os recursos](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Para parar o cálculo, selecione o **botão Pausa.** Quando a piscina SQL dedicada é pausada, vê-se um botão **Iniciar.**  Para retomar o cálculo, selecione **Start**.

3. Para remover a sua piscina SQL dedicada para que não seja cobrado para computação ou armazenamento, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Monitorize a sua carga de trabalho utilizando as métricas de monitorização do portal Azure.  Consulte [Gerir e monitorizar a Gestão da Carga de Trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) para obter mais detalhes.
