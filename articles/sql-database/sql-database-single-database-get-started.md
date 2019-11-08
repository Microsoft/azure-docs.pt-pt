---
title: Criar um banco de dados individual
description: Crie e consulte um banco de dados individual no banco de dados SQL do Azure usando o portal do Azure, o PowerShell e o CLI do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein
ms.date: 09/09/2019
ms.openlocfilehash: 482fc3f08fb986908efcab83a82338ced36ee689
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821012"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal-powershell-and-azure-cli"></a>Início rápido: criar um banco de dados individual no banco de dados SQL do Azure usando o portal do Azure, o PowerShell e o CLI do Azure

Criar um [banco de dados individual](sql-database-single-database.md) é a opção de implantação mais rápida e mais simples para criar um banco de dados no banco de dados SQL do Azure. Este guia de início rápido mostra como criar e consultar um banco de dados individual usando o portal do Azure.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/). 

Para todas as etapas neste guia de início rápido, entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-single-database"></a>Criar um banco de dados individual

Um banco de dados individual pode ser criado na camada de computação provisionada ou sem servidor.

- Um único banco de dados na camada de computação provisionada é previamente alocado uma quantidade fixa de recursos de computação, incluindo CPU e memória, usando um dos dois [modelos de compra](sql-database-purchase-models.md).
- Um único banco de dados na camada de computação sem servidor tem uma variedade de recursos de computação, incluindo CPU e memória que são dimensionados automaticamente e só estão disponíveis nos [modelos de compra baseados em vCore](sql-database-service-tiers-vcore.md).

Ao criar um banco de dados individual, você também define um [servidor de banco de dados SQL](sql-database-servers.md) para gerenciá-lo e colocá-lo no [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) em uma região especificada.

> [!NOTE]
> Este guia de início rápido usa o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md), mas o [modelo de compra baseado em DTU](sql-database-service-tiers-DTU.md) também está disponível.

Para criar um único banco de dados que contenha o AdventureWorksLT de exemplo:

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Consultar o banco de dados

Agora que você criou o banco de dados, use a ferramenta de consulta interna no portal do Azure para conectar-se ao banco de dados e consultar os dados.

1. Na página do banco de dados **SQL** do seu banco de dados, selecione **Editor de consultas (visualização)** no menu à esquerda.

   ![Entrar no editor de consultas](./media/sql-database-get-started-portal/query-editor-login.png)

2. Insira suas informações de logon e selecione **OK**.
3. Insira a consulta a seguir no painel do **Editor de consultas** .

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Selecione **executar**e examine os resultados da consulta no painel **resultados** .

   ![Resultados do editor de consultas](./media/sql-database-get-started-portal/query-editor-results.png)

5. Feche a página **Editor de consultas** e selecione **OK** quando for solicitado a descartar as edições não salvas.

## <a name="clean-up-resources"></a>Limpar recursos

Mantenha este grupo de recursos, servidor de banco de dados e banco de dados individual se desejar ir para as [próximas etapas](#next-steps). As próximas etapas mostram como se conectar e consultar seu banco de dados usando métodos diferentes.

Quando tiver terminado de usar esses recursos, você poderá excluí-los da seguinte maneira:

1. No menu à esquerda na portal do Azure, selecione **grupos de recursos**e, em seguida, selecione grupo de **recursos**.
2. Na página do grupo de recursos, selecione **excluir grupo de recursos**.
3. Insira *MyResource* no campo e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

- Crie uma regra de firewall no nível de servidor para se conectar ao banco de dados individual de ferramentas locais ou remotas. Para obter mais informações, consulte [criar uma regra de firewall no nível de servidor](sql-database-server-level-firewall-rule.md).
- Depois de criar uma regra de firewall no nível de servidor, [conecte e consulte](sql-database-connect-query.md) seu banco de dados usando várias ferramentas e idiomas diferentes.
  - [Ligar e consultar com o SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Ligar e consultar com o Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Para criar um banco de dados individual na camada de computação provisionada usando CLI do Azure, consulte [CLI do Azure exemplos](sql-database-cli-samples.md).
- Para criar um banco de dados individual na camada de computação provisionada usando Azure PowerShell, consulte [Azure PowerShell exemplos](sql-database-powershell-samples.md).
- Para criar um banco de dados individual na camada de computação sem servidor usando o Azure PowerShell, consulte [criar banco de dados sem servidor](sql-database-serverless.md#create-new-database-in-serverless-compute-tier).
