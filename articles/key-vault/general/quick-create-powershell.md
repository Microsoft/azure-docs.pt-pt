---
title: Quickstart - Crie um cofre de chaves Azure com Azure PowerShell
description: Quickstart mostrando como criar um cofre de chave Azure usando Azure PowerShell
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2019
ms.author: mbaldwin
ms.openlocfilehash: e24f1e546de1ce01896e271dbc9155c47f6c2bb6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102352"
---
# <a name="quickstart-create-a-key-vault-using-powershell"></a>Quickstart: Criar um cofre de chaves usando PowerShell

Azure Key Vault é um serviço de nuvem que fornece uma loja segura para [chaves,](../keys/index.yml) [segredos](../secrets/index.yml)e [certificados.](../certificates/index.yml) Para obter mais informações sobre o Cofre de Chaves, consulte [Sobre o Cofre da Chave Azure;](overview.md) para obter mais informações sobre o que pode ser armazenado num cofre de chaves, consulte [sobre chaves, segredos e certificados.](about-keys-secrets-certificates.md)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Neste arranque rápido, cria-se um cofre-chave com [a Azure PowerShell.](/powershell/azure/) Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer a versão 1.0.0 ou posterior do módulo Azure PowerShell. Escreva `$PSVersionTable.PSVersion` para encontrar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Login-AzAccount` para criar uma ligação com o Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um grupo de recursos Azure com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

```azurepowershell-interactive
New-AzResourceGroup -Name 'myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Crie um Cofre-Chave no grupo de recursos a partir do passo anterior. Terá de fornecer algumas informações:

- Nome do cofre chave: Uma sequência de 3 a 24 caracteres que pode conter apenas números (0-9), letras (a-z, A-Z) e hífenes (-)

  > [!Important]
  > Cada cofre deve ter um nome único. Substitua <o seu nome único de teclado> pelo nome do seu cofre-chave nos seguintes exemplos.

- Nome do grupo de recursos: **myResourceGroup**.
- A localização: **EastUS**.

```azurepowershell-interactive
New-AzKeyVault -Name "&lt;your-unique-key-vault-name&gt; -ResourceGroupName "myResourceGroup" -Location "East US"
```

O resultado deste cmdlet mostra as propriedades do cofre de chaves recém-criado. Tome nota das duas propriedades listadas abaixo:

- **Nome do cofre**: O nome que forneceu ao parâmetro de nome acima.
- **Cofre URI**: No exemplo, este é https:// &lt; o seu nome único de keyvault &gt; .vault.azure.net/. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

Nesta altura, a sua conta do Azure é a única autorizada a realizar quaisquer operações neste novo cofre.

## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. Se quiser continuar a trabalhar com outros inícios rápidos e tutoriais, pode manter estes recursos.

Quando já não é necessário, pode utilizar o comando Azure PowerShell [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido criaste um Cofre-Chave e guardaste um segredo nele. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do cofre da chave Azure](overview.md)
- Consulte a referência para os [cmdlets Azure PowerShell Key Vault](/powershell/module/az.keyvault/?view=azps-2.6.0#key_vault)
- Rever [as melhores práticas do Azure Key Vault](best-practices.md)
