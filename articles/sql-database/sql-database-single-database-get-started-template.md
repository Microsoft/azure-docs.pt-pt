---
title: 'Azure Resource Manager: Criar uma base de dados - base de dados SQL do Azure | Documentos da Microsoft'
description: Crie uma base de dados na base de dados do SQL Azure usando o modelo Azure Resource Manager.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: 01e14f86b16db6d998d60e74211ae5ad77381461
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373022"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Início rápido: Criar uma base de dados na base de dados do SQL Azure usando o modelo Azure Resource Manager

Criar uma [base de dados individual](sql-database-single-database.md) é a opção de implementação mais rápida e simples para a criação de uma base de dados na base de dados do Azure SQL. Este guia de introdução mostra-lhe como criar uma base de dados usando o modelo Azure Resource Manager. Para obter mais informações, consulte [documentação do Azure Resource Manager](/azure/azure-resource-manager/).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Criar uma base de dados

Uma base de dados tem um conjunto definido de recursos de computação, memória, e/s e armazenamento através de uma de duas [modelos de compra](sql-database-purchase-models.md). Quando cria uma base de dados, também define um [servidor de base de dados SQL](sql-database-servers.md) para gerenciá-lo e colocá-lo dentro [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) numa determinada região.

O modelo utilizado neste início rápido é partir [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/201-sql-threat-detection-server-policy-optional-db/). O seguinte ficheiro JSON é o modelo que é utilizado neste artigo. Mais exemplos de modelo de base de dados SQL do Azure podem ser encontrados [aqui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

[!code-json[create-azure-sql-database](~/quickstart-templates/201-sql-threat-detection-server-policy-optional-db/azuredeploy.json)]

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-threat-detection-server-policy-optional-db%2Fazuredeploy.json"><img src="./media/sql-database-single-database-get-started-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Selecione ou introduza os seguintes valores.  

    ![Modelo do Resource Manager criar base de dados sql do azure](./media/sql-database-single-database-get-started-template/create-azure-sql-database-resource-manager-template.png)

    Exceto se for especificado, utilize os valores predefinidos.

    * **Subscrição**: selecione uma subscrição do Azure.
    * **Grupo de recursos**: selecione **criar novo**, introduza um nome exclusivo para o grupo de recursos e, em seguida, clique em **OK**. 
    * **Localização**: selecione uma localização.  Por exemplo, **E.U.A. Central**.
    * **Utilizador administrador**: Especifique um nome de utilizador de administrador de servidor de base de dados do SQL.
    * **Palavra-passe de administrador**: Especifique uma palavra-passe de administrador. 
    * **Eu concordo com o estado de termos e condições acima**: Selecione.
3. Selecione **Comprar**.

## <a name="query-the-database"></a>Consultar a base de dados

Para consultar a base de dados, consulte [consultar a base de dados](./sql-database-single-database-get-started.md#query-the-database).

## <a name="clean-up-resources"></a>Limpar recursos

Manter este grupo de recursos, o servidor de base de dados e a única base de dados, se pretender ir para o [próximos passos](#next-steps). Os passos seguintes mostram como ligar e consultar a base de dados através de métodos diferentes.

Para eliminar o grupo de recursos com a CLI do Azure ou do Azure Powershell:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName 
```

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName 
```

## <a name="next-steps"></a>Passos Seguintes

- Crie uma regra de firewall ao nível do servidor para ligar à base de dados única a partir de ferramentas remotas ou no local. Para obter mais informações, consulte [criar uma regra de firewall ao nível do servidor](sql-database-server-level-firewall-rule.md).
- Depois de criar uma regra de firewall ao nível do servidor, [ligar e consultar](sql-database-connect-query.md) sua base de dados com várias ferramentas diferentes e linguagens.
  - [Ligar e consultar com o SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Ligar e consultar com o Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Para criar uma base de dados com a CLI do Azure, veja [amostras de CLI do Azure](sql-database-cli-samples.md).
- Para criar uma base de dados com o Azure PowerShell, veja [exemplos do Azure PowerShell](sql-database-powershell-samples.md).
