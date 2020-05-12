---
title: Criar e configurar um cofre de chaves para o Azure Disk Encryption
description: Este artigo fornece passos para criar e configurar um cofre chave para uso com encriptação de disco azure
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 6350e85552a6c92592dbe2b1a9cf48a35f86a7be
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198452"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Criar e configurar um cofre de chaves para o Azure Disk Encryption

A Encriptação azure Disk usa o Cofre de Chave Azure para controlar e gerir chaves e segredos de encriptação do disco.  Para mais informações sobre os cofres chave, consulte [Start start with Azure Key Vault](../key-vault/key-vault-get-started.md) e Secure your key [vault](../key-vault/general/secure-your-key-vault.md).

Criar e configurar um cofre chave para utilização com encriptação de disco azure envolve três passos:

1. Criar um grupo de recursos, se necessário.
2. A criar um cofre chave. 
3. Definindo políticas de acesso avançados do cofre chave.

Estes passos são ilustrados nos seguintes arranques rápidos:

Também pode, se desejar, gerar ou importar uma chave de encriptação (KEK).

## <a name="install-tools-and-connect-to-azure"></a>Instale ferramentas e ligue-se ao Azure

Os passos deste artigo podem ser concluídos com o [Azure CLI,](/cli/azure/)o [módulo Azure PowerShell Az,](/powershell/azure/overview)ou o [portal Azure](https://portal.azure.com).

### <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure

Antes de utilizar o Azure CLI ou o Azure PowerShell, tem de se ligar primeiro à subscrição do Azure. Faça-o assinando com o [Azure CLI,](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)assinando com o [Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)ou fornecendo as suas credenciais ao portal Azure quando solicitado.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Passos seguintes

- [Visão geral da encriptação do disco azure](disk-encryption-overview.md)
- [Criptografe um conjunto de escala de máquina virtual usando o Azure CLI](disk-encryption-cli.md)
- [Criptografe um conjunto de escala de máquina virtual usando o Azure PowerShell](disk-encryption-powershell.md)
