---
title: Crie uma piscina SQL dedicada (anteriormente SQL DW) utilizando o modelo do Gestor de Recursos Azure
description: Aprenda a criar uma piscina SQL Azure Synapse Analytics utilizando o modelo Azure Resource Manager.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 06/09/2020
ms.openlocfilehash: 70adb7409c44a79345a192df173a1a073cc9b7dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460732"
---
# <a name="quickstart-create-an-azure-synapse-analytics-dedicated-sql-pool-formerly-sql-dw-by-using-an-arm-template"></a>Quickstart: Criar uma piscina DE SQL dedicada Azure Synapse Analytics (anteriormente SQL DW) utilizando um modelo ARM

Este modelo de Gestor de Recursos Azure (modelo ARM) criará um pool DE SQL dedicado (anteriormente SQL DW) com encriptação de dados transparente ativada. Pool SQL dedicado (anteriormente SQL DW) refere-se às funcionalidades de armazenamento de dados da empresa que estão geralmente disponíveis em Azure Synapse.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/).

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json":::

O modelo define um recurso:

- [Microsoft.Sql/servidores](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a seguinte imagem para iniciar seduca e abrir o modelo. Este modelo cria uma piscina SQL dedicada (anteriormente SQL DW).
   
   [![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. Insira ou atualize os seguintes valores:

   * **Assinatura**: Selecione uma subscrição Azure.
   * **Grupo de recursos**: Selecione **Criar novo** e insira um nome único para o grupo de recursos e selecione **OK**. Um novo grupo de recursos facilitará a limpeza dos recursos.
   * **Região**: Selecione uma região.  Por exemplo, **E.U.A. Central**.
   * **Nome do Servidor SQL**: Aceite o nome predefinido ou introduza um nome para o nome SQL Server.
   * **Início de entrada do administrador SQL**: Introduza o nome de utilizador do administrador para o SqL Server.
   * **Palavra-passe do administrador SQL**: Introduza a palavra-passe do administrador para o Servidor SQL.
   * **Nome do Armazém de Dados**: Introduza um nome de piscina SQL dedicado.
   * **Encriptação de dados transparente:** Aceite o padrão, ativado. 
   * **Objetivo do Nível de Serviço**: Aceite o padrão, DW400c.
   * **Localização**: Aceite a localização padrão do grupo de recursos.
   * **Reveja e Crie**: Selecione.
   * **Criar**: Selecione.

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Pode utilizar o portal Azure para verificar os recursos implantados ou utilizar o script Azure CLI ou Azure PowerShell para listar os recursos implantados.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your dedicated SQL pool (formerly SQL DW) exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your dedicated SQL pool (formerly SQL DW) account exists"
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

Neste quickstart, criou uma piscina SQL dedicada (anteriormente SQL DW) usando um modelo ARM e validou a implementação. Para saber mais sobre a Azure Synapse Analytics e Azure Resource Manager, consulte os artigos abaixo.

- Leia uma [visão geral do Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)
- Saiba mais sobre o [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Crie e implemente o seu primeiro modelo ARM](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
