---
title: Criar uma única base de dados
description: Crie uma única base de dados na Base de Dados Azure SQL utilizando o portal Azure, PowerShell ou o Azure CLI. Consultar a base de dados com o Editor de Consulta no portal Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein, vanto
ms.date: 04/19/2020
ms.openlocfilehash: 1ae3159365e63159b8ed62c09bc20441ba65d8aa
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84343851"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Quickstart: Criar uma base de dados única Azure SQL Database

Neste arranque rápido, você usa o portal Azure, um script PowerShell ou um script Azure CLI para criar uma única base de dados na Base de Dados Azure SQL. Em seguida, consulta a base de **dados** utilizando o editor de consulta no portal Azure.

Uma [única base de dados](single-database-overview.md) é a opção mais rápida e simples para a Base de Dados Azure SQL. Gere uma única base de dados dentro de um [servidor](logical-servers.md), que está dentro de um [grupo de recursos Azure](../../active-directory-b2c/overview.md) numa região específica de Azure. Neste arranque rápido, cria-se um novo grupo de recursos e servidor para a nova base de dados.

Pode criar uma única base de dados no nível de computação *previsto* ou *sem servidor.* Uma base de dados alocada é pré-atribuída a uma quantidade fixa de recursos computacionais, incluindo CPU e memória, e utiliza um de dois [modelos de compra](purchasing-models.md). Este quickstart cria uma base de dados a provisionada utilizando o modelo de compra [baseado em vCore,](service-tiers-vcore.md) mas também pode escolher um modelo [baseado em DTU.](service-tiers-dtu.md)

O nível de computação sem servidor só está disponível no modelo de compra baseado em vCore, e tem uma gama automática de recursos computacional, incluindo CPU e memória. Para criar uma única base de dados no nível de computação sem servidor, consulte [criar uma base de dados sem servidor](serverless-tier-overview.md#create-a-new-database-in-the-serverless-compute-tier).

## <a name="prerequisite"></a>Pré-requisito

- Uma subscrição ativa do Azure. Se não tiver uma subscrição, [crie uma conta gratuita](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Criar uma única base de dados

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Consultar a base de dados

Uma vez criada a base de dados, pode utilizar o editor de **consulta** incorporado no portal Azure para ligar à base de dados e consultar os dados.

1. No portal, procure e selecione **bases de dados SQL**e, em seguida, selecione a sua base de dados na lista.
1. Na página **SQL Database** para a sua base de dados, selecione **O editor de consulta (pré-visualização)** no menu esquerdo.
1. Introduza as informações de login do seu servidor e selecione **OK**.

   ![Inscreva-se no editor de consulta](./media/single-database-create-quickstart/query-editor-login.png)

1. Insira a seguinte consulta no painel de **editores de consulta.**

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. Selecione **Executar**e, em seguida, reveja os resultados da consulta no painel **de resultados.**

   ![Resultados do editor de consulta](./media/single-database-create-quickstart/query-editor-results.png)

1. Feche a página do **editor de consulta** e selecione **OK** quando solicitado para descartar as suas edições não salvas.

## <a name="clean-up-resources"></a>Limpar recursos

Mantenha o grupo de recursos, servidor e base de dados única para passar aos próximos passos e aprenda a ligar e consultar a sua base de dados com diferentes métodos.

Quando terminar de utilizar estes recursos, pode eliminar o grupo de recursos que criou, que também eliminará o servidor e a base de dados única dentro do mesmo.

### <a name="the-azure-portal"></a>[O portal do Azure](#tab/azure-portal)

Para eliminar **o myResourceGroup** e todos os seus recursos utilizando o portal Azure:

1. No portal, procure e selecione **grupos de Recursos**e, em seguida, selecione **myResourceGroup** da lista.
1. Na página do grupo de recursos, selecione **Eliminar o grupo de recursos**.
1. Em **'Digite' o nome do grupo de recursos**, insira o *myResourceGroup*e, em seguida, selecione **Delete**.

### <a name="the-azure-cli"></a>[O Azure CLI](#tab/azure-cli)

Para eliminar o grupo de recursos e todos os seus recursos, executar o seguinte comando Azure CLI, utilizando o nome do seu grupo de recursos:

```azurecli-interactive
az group delete --name <your resource group>
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para eliminar o grupo de recursos e todos os seus recursos, executar o seguinte cmdlet PowerShell, utilizando o nome do seu grupo de recursos:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group>
```

---

## <a name="next-steps"></a>Próximos passos

[Conecte e questione a](connect-query-content-reference-guide.md) sua base de dados utilizando diferentes ferramentas e idiomas:
> [!div class="nextstepaction"]
> [Ligar e consultar com o SQL Server Management Studio](connect-query-ssms.md)
>
> [Ligar e consultar com o Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
 