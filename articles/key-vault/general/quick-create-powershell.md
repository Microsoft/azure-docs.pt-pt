---
title: Quickstart - Crie um cofre de chaves Azure com Azure PowerShell
description: Quickstart mostrando como criar um cofre de chave Azure usando Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: cc6d9ca2621a56242d7472a088e55651f5502c9c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814807"
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

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido criou um Key Vault usando a Azure PowerShell. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do cofre da chave Azure](overview.md)
- Consulte a referência para os [cmdlets Azure PowerShell Key Vault](/powershell/module/az.keyvault/)
- Reveja a visão geral da segurança do [Cofre da Chave Azure](security-features.md)

