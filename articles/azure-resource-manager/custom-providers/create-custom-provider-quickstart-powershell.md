---
title: Criar um fornecedor de recursos personalizados Azure com a Azure PowerShell
description: Descreve como criar um fornecedor de recursos personalizados Azure com a Azure PowerShell
author: MSEvanhi
ms.author: evanhi
ms.topic: quickstart
ms.devlang: azurepowershell
ms.date: 09/22/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6912f7f6fdc88c5d611bfbfd78f15e5f7a949f70
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91951847"
---
# <a name="quickstart-create-an-azure-custom-resource-provider-with-azure-powershell"></a>Quickstart: Criar um fornecedor de recursos personalizados Azure com a Azure PowerShell

Neste arranque rápido, aprende a criar o seu próprio fornecedor de recursos personalizados Azure utilizando o módulo [Az.CustomProviders](/powershell/module/az.customproviders) PowerShell.

> [!CAUTION]
> A Azure Custom Providers está atualmente em pré-visualização pública. Esta versão de pré-visualização é disponibilizada sem contrato de nível de serviço. Não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se optar por utilizar o PowerShell localmente, este artigo requer que instale o módulo Az PowerShell e se conecte à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Para obter mais informações sobre a instalação do módulo Az PowerShell, consulte [instalar a Azure PowerShell](/powershell/azure/install-az-ps). Se optar por utilizar a Cloud Shell, consulte [a visão geral da Azure Cloud Shell](../../cloud-shell/overview.md) para obter mais informações.

> [!IMPORTANT]
> Enquanto o módulo **PowerShell Az.CustomProviders** estiver em pré-visualização, deve instalá-lo separadamente utilizando o `Install-Module` cmdlet. Uma vez que este módulo PowerShell fica geralmente disponível, torna-se parte de futuros lançamentos de módulos Az PowerShell e disponível de forma nativa a partir de Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.CustomProviders
```

Se tiver várias subscrições do Azure, escolha a subscrição adequada na qual os recursos devem ser faturados. Selecione uma subscrição específica utilizando o [cmdlet Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um [grupo de recursos Azure](../../azure-resource-manager/management/overview.md) utilizando o cmdlet [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

O exemplo a seguir cria um grupo de recursos com o nome especificado e no local especificado.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="create-a-custom-resource-provider"></a>Criar um fornecedor de recursos personalizado

Para criar ou atualizar um fornecedor de recursos personalizado, utilize o cmdlet [New-AzCustomProvider,](/powershell/module/az.customproviders/new-azcustomprovider) como mostrado no exemplo seguinte.

```azurepowershell-interactive
New-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type -Location westus2 -ResourceType @{Name='CustomRoute1'; Endpoint='https://www.contoso.com/'}
```

## <a name="get-the-custom-resource-provider-manifest"></a>Obtenha o manifesto do fornecedor de recursos personalizado

Para obter informações sobre o manifesto do fornecedor de recursos personalizados, utilize o cmdlet [Get-AzCustomProvider,](/powershell/module/az.customproviders/get-azcustomprovider) como mostra o exemplo seguinte.

```azurepowershell-interactive
Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type | Format-List
```

## <a name="create-an-association"></a>Criar uma associação

Para criar ou atualizar uma associação, utilize o cmdlet [de associação New-AzCustomProviderAssociation,](/powershell/module/az.customproviders/new-azcustomproviderassociation) como mostra o exemplo seguinte.

```azurepowershell-interactive
$provider = Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
New-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc -TargetResourceId $provider.Id
```

## <a name="get-an-association"></a>Obter uma associação

Para obter informações sobre uma associação, utilize o [cmdlet get-AzCustomProviderAssociation,](/powershell/module/az.customproviders/get-azcustomproviderassociation) como mostra o exemplo seguinte.

```azurepowershell-interactive
Get-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se os recursos criados neste artigo não forem necessários, pode eliminá-los executando os seguintes exemplos.

### <a name="delete-an-association"></a>Excluir uma associação

Para remover uma associação, utilize o [cmdlet remove-AzCustomProviderAssociation.](/powershell/module/az.customproviders/remove-azcustomproviderassociation) O exemplo a seguir elimina uma associação.

```azurepowershell
Remove-AzCustomProviderAssociation -Scope $id -Name Namespace.Type
```

### <a name="delete-a-custom-resource-provider"></a>Excluir um fornecedor de recursos personalizado

Para remover um fornecedor de recursos personalizado, utilize o [cmdlet Remove-AzCustomProvider.](/powershell/module/az.customproviders/remove-azcustomprovider) O exemplo a seguir elimina um fornecedor de recursos personalizado.

```azurepowershell-interactive
Remove-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
```

### <a name="delete-the-resource-group"></a>Eliminar o grupo de recursos

> [!CAUTION]
> O exemplo a seguir elimina o grupo de recursos especificado e todos os recursos contidos no mesmo.
> Se existirem recursos fora do âmbito deste artigo no grupo de recursos especificados, também serão eliminados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [os Fornecedores de Recursos Personalizados Azure.](overview.md)