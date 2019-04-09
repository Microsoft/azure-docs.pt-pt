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
ms.date: 04/08/2019
ms.openlocfilehash: b28c947cb2ee3a60633fcaced18a8c353474ec9e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259734"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Início rápido: Criar uma base de dados na base de dados do SQL Azure usando o modelo Azure Resource Manager

Criar uma [base de dados individual](sql-database-single-database.md) é a opção de implementação mais rápida e simples para a criação de uma base de dados na base de dados do Azure SQL. Este guia de introdução mostra-lhe como criar uma base de dados usando o modelo Azure Resource Manager. Para obter mais informações, consulte [documentação do Azure Resource Manager](/azure/azure-resource-manager/).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Criar uma base de dados

Uma base de dados tem um conjunto definido de recursos de computação, memória, e/s e armazenamento através de uma de duas [modelos de compra](sql-database-purchase-models.md). Quando cria uma base de dados, também define um [servidor de base de dados SQL](sql-database-servers.md) para gerenciá-lo e colocá-lo dentro [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) numa determinada região.

O seguinte ficheiro JSON é o modelo que é utilizado neste artigo. O modelo é armazenado numa conta de armazenamento do Azure. Mais exemplos de modelo de base de dados SQL do Azure podem ser encontrados [aqui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serverName": {
      "type": "string",
      "defaultValue": "[concat('server-', uniqueString(resourceGroup().id, deployment().name))]",
      "metadata": {
        "description": "Name for the SQL server"
      }
    },
    "shouldDeployDb": {
      "type": "string",
      "allowedValues": [
        "Yes",
        "No"
      ],
      "defaultValue": "Yes",
      "metadata": {
        "description": "Whether an Azure SQL Database should be deployed under the server"
      }
    },
    "databaseName": {
      "type": "string",
      "defaultValue": "[concat('db-', uniqueString(resourceGroup().id, deployment().name), '-1')]",
      "metadata": {
        "description": "Name for the SQL database under the SQL server"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for server and optional DB"
      }
    },
    "emailAddresses": {
      "type": "array",
      "defaultValue": [
        "user1@example.com",
        "user2@example.com"
      ],
      "metadata": {
        "description": "Email addresses for receiving alerts"
      }
    },
    "adminUser": {
      "type": "string",
      "metadata": {
        "description": "Username for admin"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Password for admin"
      }
    }
  },
  "variables": {
    "databaseServerName": "[toLower(parameters('serverName'))]",
    "databaseName": "[parameters('databaseName')]",
    "shouldDeployDb": "[parameters('shouldDeployDb')]",
    "databaseServerLocation": "[parameters('location')]",
    "databaseServerAdminLogin": "[parameters('adminUser')]",
    "databaseServerAdminLoginPassword": "[parameters('adminPassword')]",
    "emailAddresses": "[parameters('emailAddresses')]"
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers",
      "name": "[variables('databaseServerName')]",
      "location": "[variables('databaseServerLocation')]",
      "apiVersion": "2015-05-01-preview",
      "properties": {
        "administratorLogin": "[variables('databaseServerAdminLogin')]",
        "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
        "version": "12.0"
      },
      "tags": {
        "DisplayName": "[variables('databaseServerName')]"
      },
      "resources": [
        {
          "type": "securityAlertPolicies",
          "name": "DefaultSecurityAlert",
          "apiVersion": "2017-03-01-preview",
          "dependsOn": [
            "[variables('databaseServerName')]"
          ],
          "properties": {
            "state": "Enabled",
            "emailAddresses": "[variables('emailAddresses')]",
            "emailAccountAdmins": true
          }
        }
      ]
    },
    {
      "condition": "[equals(variables('shouldDeployDb'), 'Yes')]",
      "type": "Microsoft.Sql/servers/databases",
      "name": "[concat(string(variables('databaseServerName')), '/', string(variables('databaseName')))]",
      "location": "[variables('databaseServerLocation')]",
      "apiVersion": "2017-10-01-preview",
      "dependsOn": [
        "[concat('Microsoft.Sql/servers/', variables('databaseServerName'))]"
      ],
      "properties": {},
      "tags": {
        "DisplayName": "[variables('databaseServerName')]"
      }
    }
  ]
}
```

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Farmtutorials.blob.core.windows.net%2Fcreatesql%2Fazuredeploy.json"><img src="./media/sql-database-single-database-get-started-template/deploy-to-azure.png" alt="deploy to azure"/></a>

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
  - [Ligar e consultar com o Studio de dados do Azure](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Para criar uma base de dados com a CLI do Azure, veja [amostras de CLI do Azure](sql-database-cli-samples.md).
- Para criar uma base de dados com o Azure PowerShell, veja [exemplos do Azure PowerShell](sql-database-powershell-samples.md).
