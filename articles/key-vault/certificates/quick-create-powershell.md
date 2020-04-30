---
title: 'Quickstart: Definir e recuperar um certificado do Cofre de Chaves Azure'
description: Quickstart mostrando como definir e recuperar um certificado do Cofre chave Azure usando O Azure PowerShell
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: ecb9262a96d1c9857283de00224950d9bc7a583f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424714"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-powershell"></a>Quickstart: Definir e recuperar um certificado do Cofre de Chaves Azure usando o Azure PowerShell

Neste arranque rápido, cria-se um cofre chave no Cofre chave Azure com a Azure PowerShell. O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Para mais informações sobre o Key Vault poderá rever a [visão geral](../general/overview.md). O Azure PowerShell é usado para criar e gerir recursos Azure usando comandos ou scripts. Assim que tiver concluído, irá armazenar um certificado.

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

## <a name="add-a-certificate-to-key-vault"></a>Adicione um certificado ao Cofre chave

Para adicionar um certificado ao cofre, só precisa de dar mais alguns passos. Este certificado pode ser utilizado por um pedido. 

Digite os comandos abaixo para criar um certificado auto-assinado com a política **denominada ExemploCertificado:**

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal
Add-AzKeyVaultCertificate -VaultName "Contoso-Vault2" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Agora pode fazer referência a este certificado que adicionou ao Azure Key Vault utilizando o seu URI. Use **https://Contoso-Vault2.vault.azure.net/certificates/ExampleCertificate** para obter a versão atual. 

Para visualizar o certificado previamente armazenado:

```azurepowershell-interactive
Get-AzKeyVaultCertificate -VaultName "Contoso-Vault2" -Name "ExampleCertificate"
```

Agora, criaste um Cofre chave, armazenaste um certificado e recuperaste-o.

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
