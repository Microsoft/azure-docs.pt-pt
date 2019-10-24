---
title: Criando e configurando um cofre de chaves para Azure Disk Encryption
description: Este artigo fornece etapas para criar e configurar um cofre de chaves para uso com Azure Disk Encryption
ms.service: virtual-machine-scale-sets
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 0252fff84307577ab307e591948e16dc30c38f66
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72749999"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Criando e configurando um cofre de chaves para Azure Disk Encryption

Azure Disk Encryption usa Azure Key Vault para controlar e gerenciar chaves de criptografia de disco e segredos.  Para obter mais informações sobre cofres de chaves, consulte [introdução ao Azure Key Vault](../key-vault/key-vault-get-started.md) e [proteger o cofre de chaves](../key-vault/key-vault-secure-your-key-vault.md).

Criar e configurar um cofre de chaves para uso com o Azure Disk Encryption envolve três etapas:

1. Criar um grupo de recursos, se necessário.
2. Criando um cofre de chaves. 
3. Definindo políticas de acesso avançado do cofre de chaves.

Essas etapas são ilustradas nos seguintes guias de início rápido:

Você também pode, se desejar, gerar ou importar uma chave de criptografia de chave (KEK).

## <a name="install-tools-and-connect-to-azure"></a>Instalar ferramentas e conectar-se ao Azure

As etapas neste artigo podem ser concluídas com o [CLI do Azure](/cli/azure/), o [módulo Azure PowerShell Az](/powershell/azure/overview)ou o [portal do Azure](https://portal.azure.com).

### <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure

Antes de usar o CLI do Azure ou Azure PowerShell, você deve primeiro se conectar à sua assinatura do Azure. Você faz isso [entrando com CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [entrando com o Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)ou fornecendo suas credenciais para o portal do Azure quando solicitado.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Passos seguintes

- [Visão geral de Azure Disk Encryption](disk-encryption-overview.md)
- [Criptografar conjuntos de dimensionamento de máquinas virtuais usando o CLI do Azure](disk-encryption-cli.md)
- [Criptografar conjuntos de dimensionamento de máquinas virtuais usando o Azure PowerShell](disk-encryption-powershell.md)
