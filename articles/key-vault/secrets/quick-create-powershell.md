---
title: 'Quickstart: Definir & recuperar um segredo do Cofre de Chaves usando PowerShell'
description: Neste quickstart, aprenda a criar, recuperar e apagar segredos de um Cofre de Chaves Azure usando a Azure PowerShell.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell
ms.date: 09/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 1b70a8330f6350ef66244ae03e89f685162df5c7
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92786331"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Início Rápido: Definir e obter um segredo do Azure Key Vault com o PowerShell

O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Para obter mais informações sobre o Key Vault, pode ver a [Descrição Geral](../general/overview.md). Neste início rápido, vai utilizar o PowerShell para criar um cofre de chaves. Em seguida, vai armazenar um segredo no cofre recém-criado.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer a versão 5.0.0 ou posterior do módulo Azure PowerShell. Escreva `$PSVersionTable.PSVersion` para encontrar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um grupo de recursos Azure com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Criar um Key Vault

Em seguida, vai criar um Key Vault. Ao efetuar este passo, precisa de algumas informações:

Embora utilizemos "Contoso KeyVault2" como nome para o nosso Key Vault ao longo deste quickstart, você deve usar um nome único.

- **Nome do cofre** : Contoso-Vault2.
- **Nome do grupo de recursos** : ContosoResourceGroup.
- **Localização** Leste dos EUA.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

O resultado deste cmdlet mostra as propriedades do cofre de chaves recém-criado. Tome nota das duas propriedades listadas abaixo:

* **Nome do Cofre** : no exemplo, o nome é **Contoso-Vault2** . Irá utilizar este nome para outros cmdlets do Cofre de Chaves.
* **URI do Cofre** : no exemplo, isto é https://Contoso-Vault2.vault.azure.net/. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

Após a criação do cofre, a sua conta do Azure é a única conta autorizada a realizar operações neste novo cofre.

## <a name="give-your-user-account-permissions-to-manage-secrets-in-key-vault"></a>Dê permissões à sua conta de utilizador para gerir segredos em Key Vault

Utilize o cmdlet Azure PowerShell Set-AzKeyVaultAccessPolicy para atualizar a política de acesso ao Cofre de Chaves e conceder permissões secretas à sua conta de utilizador.
```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName 'Contoso-Vault2' -UserPrincipalName 'user@domain.com' -PermissionsToSecrets get,set,delete
```

## <a name="adding-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, apenas tem de efetuar alguns passos. Neste caso, vai adicionar uma palavra-passe que possa ser utilizada por uma aplicação. A palavra-passe chama-se **ExemploPassword** e armazena o valor de **hVFkk965BuUv** na sua.

Primeiro converta o valor de **hVFkk965BuUv** para uma cadeia segura digitando:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
```

Em seguida, digite os comandos PowerShell abaixo para criar um segredo no Key Vault chamado **ExemploPassword** com o valor **hVFkk965BuUv** :

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName 'Contoso-Vault2' -Name 'ExamplePassword' -SecretValue $secretvalue
```

Para ver o valor contido no segredo como texto simples:

```azurepowershell-interactive
(Get-AzKeyVaultSecret -VaultName "Contoso-Vault2" -Name "ExamplePassword").SecretValue | ConvertFrom-SecureString -AsPlainText
```

Agora, criou um Key Vault, armazenou um segredo e obteve-o.

## <a name="clean-up-resources"></a>Limpar os recursos

 Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. Se quiser continuar a trabalhar com outros inícios rápidos e tutoriais, pode manter estes recursos.

Quando já não é necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, o Key Vault e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido criaste um Cofre-Chave e guardaste um segredo nele. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Consulte a referência para os [cmdlets Azure PowerShell Key Vault](/powershell/module/az.keyvault/#key_vault)
- Rever [as melhores práticas do Azure Key Vault](../general/best-practices.md)
