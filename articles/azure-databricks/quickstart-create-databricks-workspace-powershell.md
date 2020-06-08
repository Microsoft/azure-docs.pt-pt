---
title: Quickstart - Criar um espaço de trabalho Azure Databricks utilizando o PowerShell
description: Este quickstart mostra como usar o PowerShell para criar um espaço de trabalho Azure Databricks.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 06/02/2020
ms.custom: mvc
ms.openlocfilehash: 445832119a113f92dc1da51eb5b5e77dff176fa3
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485731"
---
# <a name="quickstart-create-an-azure-databricks-workspace-using-powershell"></a>Quickstart: Criar um espaço de trabalho Azure Databricks utilizando PowerShell

Este quickstart descreve como usar o PowerShell para criar um espaço de trabalho Azure Databricks. Pode utilizar o PowerShell para criar e gerir os recursos Azure de forma interativa ou em scripts.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se optar por utilizar o PowerShell localmente, este artigo requer que instale o módulo Az PowerShell e se conecte à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/Connect-AzAccount) Para obter mais informações sobre a instalação do módulo Az PowerShell, consulte [instalar a Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Enquanto o módulo PowerShell Az.Databricks estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o seguinte comando: `Install-Module -Name Az.Databricks -AllowPrerelease` .
> Uma vez que o módulo PowerShell Az.Databricks está geralmente disponível, torna-se parte de futuras libertações do módulo Az PowerShell e disponível nativamente a partir de Azure Cloud Shell.

Se esta for a sua primeira utilização de Azure Databricks, tem de registar o fornecedor de recursos **Microsoft.Databricks.**

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Databricks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se tiver várias subscrições do Azure, escolha a subscrição adequada na qual os recursos devem ser faturados. Selecione um ID de subscrição específico utilizando o [cmdlet Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um [grupo de recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) utilizando o cmdlet [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

O exemplo a seguir cria um grupo de recursos chamado **myresourcegroup** na região **oeste dos EUA 2.**

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus2
```

## <a name="create-an-azure-databricks-workspace"></a>Criar uma área de trabalho do Azure Databricks

Nesta secção, cria-se um espaço de trabalho Azure Databricks utilizando o PowerShell.

```azurepowershell-interactive
New-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup -Location westus2 -ManagedResourceGroupName databricks-group -Sku standard
```

Forneça os seguintes valores:

|       **Propriedade**       |                                                                                **Descrição**                                                                                 |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Nome                     | Indique um nome para a sua área de trabalho do Databricks                                                                                                                                   |
| ResourceGroupName        | Especificar um nome de grupo de recursos existente                                                                                                                                        |
| Localização                 | Selecione **E.U.A. Oeste 2**. Para outras regiões disponíveis, consulte [os serviços Azure disponíveis por região](https://azure.microsoft.com/regions/services/)                                     |
| Nome do ManageResourceGroup | Especifique se pretende criar um novo grupo de recursos geridos ou utilizar um existente.                                                                                        |
| Sku                      | Escolha entre **Standard,** **Premium**ou **Trial**. Para obter mais informações sobre estes níveis, consulte [os preços da Databricks](https://azure.microsoft.com/pricing/details/databricks/) |

A criação da área de trabalho demora alguns minutos. Uma vez concluído este processo, a sua conta de utilizador é automaticamente adicionada como um utilizador administrativo no espaço de trabalho.

Quando uma implantação do espaço de trabalho falha, o espaço de trabalho ainda é criado num estado falhado. Elimine o espaço de trabalho falhado e crie um novo espaço de trabalho que resolva os erros de implantação. Quando elimina o espaço de trabalho falhado, o grupo de recursos geridos e quaisquer recursos implantados com sucesso também são eliminados.

## <a name="determine-the-provisioning-state-of-a-databricks-workspace"></a>Determinar o estado de provisão de um espaço de trabalho databricks

Para determinar se um espaço de trabalho databricks foi a provisionado com sucesso, você pode usar o `Get-AzDatabricksWorkspace` cmdlet.

```azurepowershell-interactive
Get-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup |
  Select-Object -Property Name, SkuName, Location, ProvisioningState
```

```Output
Name            SkuName   Location  ProvisioningState
----            -------   --------  -----------------
mydatabricksws  standard  westus2   Succeeded
```

## <a name="clean-up-resources"></a>Limpar recursos

Se os recursos criados neste quickstart não forem necessários para outro arranque rápido ou tutorial, pode eliminá-los executando o seguinte exemplo.

> [!CAUTION]
> O exemplo a seguir elimina o grupo de recursos especificado e todos os recursos contidos no mesmo.
> Se existirem recursos fora do âmbito deste arranque rápido no grupo de recursos especificado, também serão eliminados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Para eliminar apenas o servidor criado neste arranque rápido sem eliminar o grupo de recursos, utilize o `Remove-AzDatabricksWorkspace` cmdlet.

```azurepowershell-interactive
Remove-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Criar um cluster do Spark no Databricks](quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks)
