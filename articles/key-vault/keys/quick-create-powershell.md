---
title: Criar e recuperar atributos de uma chave no Cofre da Chave Azure – Azure PowerShell
description: Quickstart mostrando como definir e recuperar uma chave do Azure Key Vault usando Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: adbf3080367e54147c981c8ccf0bb6236111b8c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99071210"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-powershell"></a>Quickstart: set and recuperar uma chave do Azure Key Vault usando Azure PowerShell

Neste arranque rápido, cria-se um cofre chave no Cofre da Chave Azure com o Azure PowerShell. O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Para mais informações sobre o Key Vault, poderá rever a [Visão Geral.](../general/overview.md) O Azure PowerShell é utilizado para criar e gerir recursos Azure usando comandos ou scripts. Uma vez concluída, guardará uma chave.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer a versão 1.0.0 ou posterior do módulo Azure PowerShell. Escreva `$PSVersionTable.PSVersion` para encontrar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Login-AzAccount` para criar uma ligação com o Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="add-a-key-to-key-vault"></a>Adicione uma chave ao Cofre de Chaves

Para adicionar uma chave ao cofre, só precisa dar alguns passos adicionais. Esta chave pode ser utilizada por uma aplicação. 

Digite os comandos abaixo para criar um **chamado ExemploKey** :

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "ExampleKey" -Destination "Software"
```

Agora pode fazer referência a esta chave que adicionou ao Azure Key Vault utilizando o seu URI. Utilize **"https://<o seu nome de chave-C1.vault.azure.net/keys/ExampleKey único para** obter a versão atual. 

Para ver a chave previamente armazenada:

```azurepowershell-interactive
Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -KeyName "ExampleKey"
```

Criaste um Cofre-Chave, armazenaste uma chave e recuperaste-a.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart criou um Cofre-Chave e guardou um certificado nele. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Consulte a referência para os [cmdlets Azure PowerShell Key Vault](/powershell/module/az.keyvault/)
- Reveja a visão geral da [segurança do Cofre-Chave](../general/security-overview.md)
