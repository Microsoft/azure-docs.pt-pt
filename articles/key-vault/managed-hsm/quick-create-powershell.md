---
title: Criar e recuperar atributos de uma chave gerida no Cofre da Chave Azure – Azure PowerShell
description: Quickstart mostrando como definir e recuperar uma chave gerida a partir do Azure Key Vault usando Azure PowerShell
services: key-vault
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/26/2021
ms.topic: quickstart
ms.service: key-vault
ms.subservice: keys
tags:
- azure-resource-manager
ms.custom:
- mode-api
ms.openlocfilehash: aa984a8f3899db72ead878e2c4381ea6a080e32d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815437"
---
# <a name="quickstart-set-and-retrieve-a-managed-key-from-azure-key-vault-using-powershell"></a>Quickstart: Definir e recuperar uma chave gerida a partir do Cofre de Chaves Azure usando PowerShell

Neste arranque rápido, cria-se um cofre chave no Cofre da Chave Azure com o Azure PowerShell. O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Para mais informações sobre o Key Vault, poderá rever a [Visão Geral.](../general/overview.md) O Azure PowerShell é utilizado para criar e gerir recursos Azure usando comandos ou scripts. Uma vez concluída, guardará uma chave.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer a versão 1.0.0 ou posterior do módulo Azure PowerShell. Escreva `$PSVersionTable.PSVersion` para encontrar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Login-AzAccount` para criar uma ligação com o Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Utilize o cmdlet Azure PowerShell [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) para criar um grupo de recursos chamado *myResourceGroup* na localização *westus.* 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"

## Get your principal ID

To create a managed HSM, you will need your Azure Active Directory principal ID.  To obtain your ID, use the Azure PowerShell [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet, passing your email address to the "UserPrincipalName" parameter:

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName "<your@email.address>"
```

O seu ID principal será devolvido no formato, "xxxxxxx-xxxx-xxxx-xxxx-xxxxx".

## <a name="create-a-managed-hsm"></a>Criar um HSM gerido

Utilize o cmdlet Azure PowerShell [New-AzKeyVaagedHsm](/powershell/module/az.keyvault/new-azkeyvaultmanagedhsm) para criar um novo Cofre de Chaves gerido pelo HSM. Terá de fornecer algumas informações:

- Nome HSM gerido: Uma cadeia de 3 a 24 caracteres que pode conter apenas números (0-9), letras (a-z, A-Z) e hífenes (-)

  > [!Important]
  > Cada HSM gerido deve ter um nome único. Substitua <o seu nome de hsm gerido único> pelo nome do seu HSM gerido nos seguintes exemplos.

- Nome do grupo de recursos: **myResourceGroup**.
- A localização: **EastUS**.
- O seu ID principal: Passe o ID principal do Diretório Ativo Azure que obteve na última secção para o parâmetro "Administrador". 

```azurepowershell-interactive
New-AzKeyVaultManagedHsm -Name "<your-unique-managed-hsm-name>" -ResourceGroupName "myResourceGroup" -Location "West US" -Administrator "<your-principal-ID>"
```

A saída deste cmdlet mostra propriedades do HSM recém-criado. Tome nota das duas propriedades listadas abaixo:

- **Nome HSM gerido**: O nome que forneceu para o parâmetro de nome acima.
- **Cofre URI**: No exemplo, este é https:// &lt; o seu nome único-hsm-vault.azure.net/. &gt; As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

Nesta altura, a sua conta do Azure é a única autorizada a realizar quaisquer operações neste novo cofre.

## <a name="activate-your-managed-hsm"></a>Ativar o seu HSM gerido

Todos os comandos do plano de dados são desativados até que o HSM seja ativado. Não será capaz de criar chaves ou atribuir funções. Apenas os administradores designados que foram designados durante o comando de criação podem ativar o HSM. Para ativar o HSM tem de descarregar o [Domínio de Segurança](security-domain.md).

Para ativar o seu HSM, precisa de:
- Mínimos 3 pares de chaves RSA (máximo 10)
- Especificar o número mínimo de chaves necessárias para desencriptar o domínio de segurança (quórum)

Para ativar o HSM, envia pelo menos 3 (máximo 10) chaves públicas RSA para o HSM. O HSM encripta o domínio de segurança com estas chaves e envia-o de volta. Uma vez que este download de domínio de segurança esteja concluído com sucesso, o seu HSM está pronto a ser utilizado. Também precisa de especificar o quórum, que é o número mínimo de chaves privadas necessárias para desencriptar o domínio de segurança.

O exemplo abaixo mostra como usar `openssl` (disponível para o Windows [aqui)](https://slproweb.com/products/Win32OpenSSL.html)para gerar 3 certificado auto-assinado.

```console
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Crie e guarde os pares de chaves RSA e o ficheiro de domínio de segurança gerados neste passo de forma segura.

Utilize o cmdlet Azure PowerShell [Export-AzKeyVaultSecurityDomain](/powershell/module/az.keyvault/export-azkeyvaultsecuritydomain) para descarregar o domínio de segurança e ativar o seu HSM gerido. O exemplo abaixo, utiliza 3 pares de chaves RSA (apenas são necessárias chaves públicas para este comando) e define o quórum para 2.

```azurepowershell-interactive
Export-AzKeyVaultSecurityDomain -Name "<your-unique-managed-hsm-name>" -Certificates "cert_0.cer", "cert_1.cer", "cert_2.cer" -OutputPath "MHSMsd.ps.json" -Quorum 2
```

Por favor, guarde o ficheiro de domínio de segurança e os pares de chaves RSA de forma segura. Você precisará deles para recuperação de desastres ou para criar outro HSM gerido que partilhe o mesmo domínio de segurança, para que possam partilhar chaves.

Depois de descarregar com sucesso o domínio de segurança, o seu HSM estará em estado ativo e pronto para utilizar.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart criou um Cofre-Chave e guardou um certificado nele. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Consulte a referência para os [cmdlets Azure PowerShell Key Vault](/powershell/module/az.keyvault/)
- Reveja a visão geral da [segurança do Cofre-Chave](../general/security-features.md)
