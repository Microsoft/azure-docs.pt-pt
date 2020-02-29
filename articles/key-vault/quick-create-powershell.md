---
title: 'Quickstart: set & recuperar um segredo do Cofre chave usando powerShell'
description: Neste arranque rápido, aprenda a criar, recuperar e apagar segredos de um cofre de chaves Azure usando powerShell
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
ms.openlocfilehash: f2b3b7d42ec7eac6dba402c0f553760548c78044
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197798"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Início Rápido: Definir e obter um segredo do Azure Key Vault com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Para obter mais informações sobre o Key Vault, pode ver a [Descrição Geral](key-vault-overview.md). Neste início rápido, vai utilizar o PowerShell para criar um cofre de chaves. Em seguida, vai armazenar um segredo no cofre recém-criado.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer a versão 1.0.0 ou mais posterior do módulo PowerShell Azure. Escreva `$PSVersionTable.PSVersion` para encontrar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Login-AzAccount` para criar uma ligação com o Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Crie um grupo de recursos Azure com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Criar um Key Vault

Em seguida, vai criar um Key Vault. Ao efetuar este passo, precisa de algumas informações:

Embora utilizemos "Contoso KeyVault2" como o nome do nosso Key Vault neste início rápido, tem de utilizar um nome exclusivo.

- **Nome do cofre**: Contoso-Vault2.
- **Nome do grupo de recursos**: ContosoResourceGroup.
- **Localização**: E.U.A Leste.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

O resultado deste cmdlet mostra as propriedades do cofre de chaves recém-criado. Tome nota das duas propriedades listadas abaixo:

* **Nome do Cofre**: no exemplo, o nome é **Contoso-Vault2**. Irá utilizar este nome para outros cmdlets do Cofre de Chaves.
* **URI do Cofre**: no exemplo, isto é https://Contoso-Vault2.vault.azure.net/. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

Após a criação do cofre, a sua conta do Azure é a única conta autorizada a realizar operações neste novo cofre.

![Resultado após a conclusão do comando creation do Key Vault](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, apenas tem de efetuar alguns passos. Neste caso, vai adicionar uma palavra-passe que possa ser utilizada por uma aplicação. A palavra-passe **chama-se ExemploPassword** e armazena o valor do **hVFkk965BuUv** na seleção.

Primeiro converta o valor do **hVFkk965BuUv** numa corda segura digitando:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
```

Em seguida, digite os comandos PowerShell abaixo para criar um segredo em Key Vault chamado **ExamplePassword** com o valor **hVFkk965BuUv** :

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName 'Contoso-Vault2' -Name 'ExamplePassword' -SecretValue $secretvalue
```

Para ver o valor contido no segredo como texto simples:

```azurepowershell-interactive
(Get-AzKeyVaultSecret -vaultName "Contoso-Vault2" -name "ExamplePassword").SecretValueText
```

Agora, criou um Key Vault, armazenou um segredo e obteve-o.

## <a name="clean-up-resources"></a>Limpar recursos

 Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. Se quiser continuar a trabalhar com outros inícios rápidos e tutoriais, pode manter estes recursos.

Quando já não for necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, o Cofre chave e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criaste um Cofre chave e guardaste um segredo nele. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do Cofre chave Azure](key-vault-overview.md)
- Consulte a referência para os [cmdlets](/powershell/module/az.keyvault/?view=azps-2.6.0#key_vault) de cofre de chave Azure PowerShell
- Saiba mais sobre [chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- Rever [as melhores práticas do Cofre de Chaves Azure](key-vault-best-practices.md)
