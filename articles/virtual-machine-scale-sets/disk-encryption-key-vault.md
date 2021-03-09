---
title: Criar e configurar um cofre de chaves para o Azure Disk Encryption
description: Este artigo fornece passos para criar e configurar um cofre chave para uso com encriptação de disco Azure
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 3d30d506ae3b49b2e247ee77ef17d101f94854c7
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501022"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption"></a>Criar e configurar um cofre chave para encriptação de disco Azure

A Azure Disk Encryption usa o Cofre da Chave Azure para controlar e gerir chaves e segredos de encriptação de discos.  Para obter mais informações sobre cofres chave, consulte [Começar com o Cofre da Chave Azure](../key-vault/general/overview.md) e Proteger o cofre da [chave.](../key-vault/general/secure-your-key-vault.md)

Criar e configurar um cofre chave para uso com encriptação de disco Azure envolve três passos:

1. Criar um grupo de recursos, se necessário.
2. Criando um cofre de chaves. 
3. Definição de políticas avançadas de acesso ao cofre de chaves.

Estes passos são ilustrados nos seguintes quickstarts:

Pode também, se desejar, gerar ou importar uma chave de encriptação chave (KEK).

## <a name="install-tools-and-connect-to-azure"></a>Instale ferramentas e ligue-se ao Azure

Os passos deste artigo podem ser preenchidos com o [Azure CLI,](/cli/azure/)o [módulo Azure PowerShell Az,](/powershell/azure/)ou o [portal Azure](https://portal.azure.com).

### <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure

Antes de utilizar o Azure CLI ou a Azure PowerShell, tem primeiro de ligar-se à sua subscrição Azure. Fá-lo [assinando com a Azure CLI](/cli/azure/authenticate-azure-cli), [assinando com a Azure PowerShell,](/powershell/azure/authenticate-azureps)ou fornecendo as suas credenciais ao portal Azure quando solicitado.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Passos seguintes

- [Visão geral da encriptação do disco Azure](disk-encryption-overview.md)
- [Criptografe conjuntos de escala de máquina virtual usando o Azure CLI](disk-encryption-cli.md)
- [Criptografe conjuntos de escala de máquina virtual usando o Azure PowerShell](disk-encryption-powershell.md)
