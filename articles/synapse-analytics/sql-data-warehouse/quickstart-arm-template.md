---
title: Crie uma piscina SQL usando o modelo de Gestor de Recursos Azure
description: Aprenda a criar uma piscina SQL Azure Synapse Analytics utilizando o modelo Azure Resource Manager.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 06/09/2020
ms.openlocfilehash: 3f84cd92a4c4f96831d39cac783df6710c5ea5f4
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2020
ms.locfileid: "85513424"
---
# <a name="quickstart-create-an-azure-synapse-analytics-sql-pool-by-using-an-arm-template"></a>Quickstart: Criar uma piscina SQL Azure Synapse Analytics usando um modelo ARM

Este modelo de Gestor de Recursos Azure (modelo ARM) criará um pool SQL Azure Synapse Analytics com encriptação de dados transparente ativada. O pool SYNAPSE SQL refere-se às funcionalidades de armazenamento de dados da empresa que estão geralmente disponíveis em Azure Synapse.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente satisfaça os pré-requisitos e estiver familiarizado com a utilização de modelos ARM, selecione o botão **Implementar para Azul.** O modelo será aberto no portal Azure.

[![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste arranque rápido é de [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/).

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json" highlight="57-97":::

O modelo define um recurso:

- [Microsoft.Sql/servidores](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a seguinte imagem para iniciar seduca e abrir o modelo. Este modelo cria uma piscina Sinapse SQL.
   
   [![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. Insira ou atualize os seguintes valores:

   * **Assinatura**: Selecione uma subscrição Azure.
   * **Grupo de recursos**: Selecione **Criar novo** e insira um nome único para o grupo de recursos e selecione **OK**. Um novo grupo de recursos facilitará a limpeza dos recursos.
   * **Região**: Selecione uma região.  Por exemplo, **E.U.A. Central**.
   * **Nome do Servidor SQL**: Aceite o nome predefinido ou introduza um nome para o nome SQL Server.
   * **Início de entrada do administrador SQL**: Introduza o nome de utilizador do administrador para o SqL Server.
   * **Palavra-passe do administrador SQL**: Introduza a palavra-passe do administrador para o Servidor SQL.
   * **Nome do Armazém de Dados**: Introduza um nome de piscina SQL.
   * **Encriptação de dados transparente:** Aceite o padrão, ativado. 
   * **Objetivo do Nível de Serviço**: Aceite o padrão, DW400c.
   * **Localização**: Aceite a localização padrão do grupo de recursos.
   * **Reveja e Crie**: Selecione.
   * **Criar**: Selecione.

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Pode utilizar o portal Azure para verificar os recursos implantados ou utilizar o script Azure CLI ou Azure PowerShell para listar os recursos implantados.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your Synapse SQL pool exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your SQL pool account exists"
(Get-AzResource -ResourceType "Microsoft.Sql/servers/databases" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos utilizando o Azure CLI ou o Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou uma piscina Azure Synapse Analytics SQL utilizando um modelo ARM e validou a implementação. Para saber mais sobre a Azure Synapse Analytics e Azure Resource Manager, continue para os artigos abaixo.

- Leia uma [visão geral do Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)
- Saiba mais sobre o [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Crie e implemente o seu primeiro modelo ARM](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
