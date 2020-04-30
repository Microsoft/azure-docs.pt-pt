---
title: Criar uma única base de dados
description: Crie uma base de dados única Azure SQL utilizando o portal Azure, PowerShell ou Azure CLI. Consulta a base de dados com o Editor de Consulta no portal Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein, vanto
ms.date: 04/19/2020
ms.openlocfilehash: b06c255f94b4d539c7c85e318238b10629cbb907
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81685154"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Quickstart: Criar uma base de dados única Azure SQL

Neste arranque rápido, você usa o portal Azure, um script PowerShell ou um script Azure CLI para criar uma base de dados única Azure SQL. Em seguida, consulta a base de dados usando o **editor da Consulta** no portal Azure. 

Uma [única base de dados](sql-database-single-database.md) é a opção de implementação mais rápida e simples para a Base de Dados Azure SQL. Gere uma única base de dados dentro de um servidor de base de [dados SQL,](sql-database-servers.md)que está dentro de um grupo de [recursos Azure](../azure-resource-manager/management/overview.md) numa região específica do Azure. Neste arranque rápido, cria um novo grupo de recursos e servidor SQL para a nova base de dados.

Pode criar uma única base de dados no nível de computação *aprovisionado* ou *sem servidores.* Uma base de dados provisionada é pré-atribuída uma quantidade fixa de recursos computacionais, incluindo CPU e memória, e utiliza um dos dois modelos de [compra.](sql-database-purchase-models.md) Este quickstart cria uma base de dados aprovisionada utilizando o modelo de compra [baseado em vCore,](sql-database-service-tiers-vcore.md) mas também pode escolher um modelo [baseado em DTU.](sql-database-service-tiers-DTU.md) 

O nível de computação sem servidor só está disponível no modelo de compra baseado em vCore, e tem uma gama automática de recursos computacionais, incluindo CPU e memória. Para criar uma única base de dados no nível de computação sem servidor, consulte [Criar uma base de dados sem servidores](sql-database-serverless.md#create-new-database-in-serverless-compute-tier).

## <a name="prerequisite"></a>Pré-requisito

- Uma subscrição ativa do Azure. Se não tiver uma subscrição, [crie uma conta gratuita](https://azure.microsoft.com/free/). 

## <a name="create-a-single-database"></a>Criar uma única base de dados

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Consulta na base de dados

Uma vez criada a sua base de dados, pode utilizar o editor de **Consulta** incorporada no portal Azure para ligar à base de dados e consultar os dados.

1. No portal, procure e selecione bases de **dados SQL**e, em seguida, selecione a sua base de dados da lista.
1. Na página base de **dados SQL** para a sua base de dados, selecione **O editor de Consulta (pré-visualização)** no menu esquerdo.
1. Introduza as informações de login do seu servidor e selecione **OK**.
   
   ![Inscreva-se no editor da Consulta](./media/sql-database-single-database-get-started/query-editor-login.png)

1. Introduza a seguinte consulta no painel do editor da **Consulta.**

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. Selecione **Executar**e, em seguida, reveja os resultados da consulta no painel **resultados.**

   ![Resultados do editor de consulta](./media/sql-database-single-database-get-started/query-editor-results.png)

1. Feche a página do editor da **Consulta** e selecione **OK** quando solicitado a descartar as suas edidas não guardadas.

## <a name="clean-up-resources"></a>Limpar recursos

Mantenha o grupo de recursos, servidor e base de dados única para passar aos próximos passos e aprender a ligar e consultar a sua base de dados com diferentes métodos.

Quando terminar de utilizar estes recursos, pode eliminar o grupo de recursos que criou, que também eliminará o servidor e uma única base de dados dentro dele.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para eliminar o **myResourceGroup** e todos os seus recursos utilizando o portal Azure:

1. No portal, procure e selecione **grupos de Recursos,** e, em seguida, selecione **myResourceGroup** da lista.
1. Na página do grupo de recursos, **selecione Eliminar o grupo de recursos**.
1. Em **Sescreva o nome do grupo de recursos,** introduza *o myResourceGroup,* e, em seguida, selecione **Eliminar**.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para eliminar o grupo de recursos e todos os seus recursos, execute o seguinte comando Azure CLI, utilizando o nome do seu grupo de recursos:

```azurecli-interactive
az group delete --name <your resource group>
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para eliminar o grupo de recursos e todos os seus recursos, execute o seguinte cmdlet PowerShell, utilizando o nome do seu grupo de recursos:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group>
```

---

## <a name="next-steps"></a>Passos seguintes

[Conecte e consulta](sql-database-connect-query.md) a sua base de dados utilizando diferentes ferramentas e idiomas:
> [!div class="nextstepaction"]
> [Ligar e consultar com o SQL Server Management Studio](sql-database-connect-query-ssms.md)
> 
> [Ligar e consultar com o Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
