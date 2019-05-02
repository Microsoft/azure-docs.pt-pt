---
title: Início Rápido do Azure - Definir e obter um segredo do Key Vault com o PowerShell | Microsoft Docs
description: ''
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: eb9a18ad0fb00fc9f648e3b22b0db1ea4eda479d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64712746"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Início rápido: Definir e obter um segredo no Azure Key Vault com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Para obter mais informações sobre o Key Vault, pode ver a [Descrição Geral](key-vault-overview.md). Neste início rápido, vai utilizar o PowerShell para criar um cofre de chaves. Em seguida, vai armazenar um segredo no cofre recém-criado.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer a versão 1.0.0 do módulo Azure PowerShell ou posterior. Tipo de `$PSVersionTable.PSVersion` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Login-AzAccount` para criar uma ligação com o Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

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

* **Nome do cofre**: No exemplo que está **Contoso-Vault2**. Irá utilizar este nome para outros cmdlets do Cofre de Chaves.
* **URI do cofre**: Neste exemplo, que é https://contosokeyvault.vault.azure.net/. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

Após a criação do cofre, a sua conta do Azure é a única conta autorizada a realizar operações neste novo cofre.

![Resultado após a conclusão do comando creation do Key Vault](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, apenas tem de efetuar alguns passos. Neste caso, vai adicionar uma palavra-passe que possa ser utilizada por uma aplicação. A palavra-passe é denominada **ExamplePassword** e armazena o valor de **hVFkk965BuUv** nela.

Comece por converter o valor de **hVFkk965BuUv** numa cadeia segura ao escrever:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
```

Em seguida, escreva os comandos do PowerShell abaixo para criar um segredo no Key Vault denominado **ExamplePassword** com o valor **hVFkk965BuUv** :

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'ExamplePassword' -SecretValue $secretvalue
```

Para ver o valor contido no segredo como texto simples:

```azurepowershell-interactive
(Get-AzKeyVaultSecret -vaultName "Contosokeyvault" -name "ExamplePassword").SecretValueText
```

Agora, criou um Key Vault, armazenou um segredo e obteve-o.

## <a name="clean-up-resources"></a>Limpar recursos

 Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. Se quiser continuar a trabalhar com outros inícios rápidos e tutoriais, pode manter estes recursos.

Quando já não for necessário, pode utilizar o [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) de comando para remover o grupo de recursos, o Cofre de chaves, e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um Key Vault e armazenou uma chave de software. Para saber mais sobre o Key Vault e como utilizá-lo com as suas aplicações, avance para o tutorial referente às aplicações Web que funcionam com o Key Vault.

Para saber como ler um segredo do Key Vault a partir de um aplicativo web usando identidades geridas para recursos do Azure, continue o tutorial

> [!div class="nextstepaction"]
> [Configurar uma aplicação web do Azure para ler um segredo do Key vault](quick-create-net.md).
