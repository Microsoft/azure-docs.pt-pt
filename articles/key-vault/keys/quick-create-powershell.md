---
title: 'Quickstart: Definir e recuperar uma chave do Cofre de Chaves Azure'
description: Quickstart mostrando como definir e recuperar uma chave do Cofre de Chaves Azure usando O Azure PowerShell
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.openlocfilehash: c407c10327a80de6b3df18a3db3978468c9f8da0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424189"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-powershell"></a>Quickstart: Definir e recuperar uma chave do Cofre de Chaves Azure usando o Azure PowerShell

Neste arranque rápido, cria-se um cofre chave no Cofre chave Azure com a Azure PowerShell. O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Para mais informações sobre o Key Vault poderá rever a [visão geral](../general/overview.md). O Azure PowerShell é usado para criar e gerir recursos Azure usando comandos ou scripts. Assim que tiver concluído, armazenará uma chave.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer a versão 1.0.0 ou mais posterior do módulo PowerShell Azure. Escreva `$PSVersionTable.PSVersion` para encontrar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Login-AzAccount` para criar uma ligação com o Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos Azure com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Criar um Key Vault

Em seguida, vai criar um Key Vault. Ao efetuar este passo, precisa de algumas informações:

Embora usemos "Contoso KeyVault2" como nome para o nosso Cofre chave durante este quickstart, você deve usar um nome único.

- **Nome do cofre**: Contoso-Vault2.
- **Nome do grupo de recursos**: ContosoResourceGroup.
- **Localização** Leste dos EUA.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

O resultado deste cmdlet mostra as propriedades do cofre de chaves recém-criado. Tome nota das duas propriedades listadas abaixo:

* **Nome do Cofre**: no exemplo, o nome é **Contoso-Vault2**. Irá utilizar este nome para outros cmdlets do Cofre de Chaves.
* **URI do Cofre**: no exemplo, isto é https://Contoso-Vault2.vault.azure.net/. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

Após a criação do cofre, a sua conta do Azure é a única conta autorizada a realizar operações neste novo cofre.

![Resultado após a conclusão do comando creation do Key Vault](../media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="add-a-key-to-key-vault"></a>Adicione uma chave para o cofre da chave

Para adicionar uma chave ao cofre, só precisa dar alguns passos adicionais. Esta chave pode ser usada por uma aplicação. 

Digite os comandos abaixo para criar um chamado **ExemploChave:**

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName 'Contoso-Vault2' -Name 'ExampleKey' -Destination 'Software'
```

Agora pode fazer referência a esta chave que adicionou ao Cofre de Chaves Azure utilizando o seu URI. Use **https://Contoso-Vault2.vault.azure.net/keys/ExampleKey** para obter a versão atual. 

Para visualizar a chave previamente armazenada:

```azurepowershell-interactive
Get-AzKeyVaultKey -VaultName 'Contoso-Vault2' -KeyName 'ExampleKey'
```

Agora, criaste um Cofre chave, armazenaste uma chave e recuperaste-a.

## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.
Quando já não for necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos relacionados. Pode eliminar os recursos da seguinte forma:

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um Cofre chave e guardou um certificado nele. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do Cofre chave Azure](../general/overview.md)
- Consulte a referência para os [cmdlets](/powershell/module/az.keyvault/) de cofre de chave Azure PowerShell
- Rever [as melhores práticas do Cofre de Chaves Azure](../general/best-practices.md)
