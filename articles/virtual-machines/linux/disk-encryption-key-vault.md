---
title: Criar e configurar um cofre de chaves para o Azure Disk Encryption
description: Este artigo fornece passos para criar e configurar um cofre chave para uso com encriptação de disco Azure num Linux VM.
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: d163ee552d95322a45acd571966a22e99aa1512e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562610"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Criar e configurar um cofre de chaves para o Azure Disk Encryption

A Azure Disk Encryption usa o Cofre da Chave Azure para controlar e gerir chaves e segredos de encriptação de discos.  Para obter mais informações sobre cofres chave, consulte [Começar com o Cofre da Chave Azure](../../key-vault/general/overview.md) e Proteger o cofre da [chave.](../../key-vault/general/secure-your-key-vault.md) 

> [!WARNING]
> - Se já utilizou a Encriptação do Disco Azure com AZure AD para encriptar um VM, deve continuar a utilizar esta opção para encriptar o seu VM. Consulte [criar e configurar um cofre chave para encriptação de disco Azure com Azure AD (versão anterior)](disk-encryption-key-vault-aad.md) para mais detalhes.

Criar e configurar um cofre chave para uso com encriptação de disco Azure envolve três passos:

1. Criar um grupo de recursos, se necessário.
2. Criando um cofre de chaves. 
3. Definição de políticas avançadas de acesso ao cofre de chaves.

Estes passos são ilustrados nos seguintes quickstarts:

- [Criar e encriptar uma VM do Linux com a CLI do Azure](disk-encryption-cli-quickstart.md)
- [Criar e encriptar uma VM do Linux com o Azure PowerShell](disk-encryption-powershell-quickstart.md)

Pode também, se desejar, gerar ou importar uma chave de encriptação chave (KEK).

> [!Note]
> Os passos deste artigo são automatizados na encriptação do [disco Azure pré-requisitos do script CLI](https://github.com/ejarvi/ade-cli-getting-started) e da encriptação do [disco Azure pré-requisitos do script PowerShell](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Instale ferramentas e ligue-se ao Azure

Os passos deste artigo podem ser preenchidos com o [Azure CLI,](/cli/azure/)o [módulo Azure PowerShell Az,](/powershell/azure/)ou o [portal Azure](https://portal.azure.com). 

Enquanto o portal é acessível através do seu navegador, a Azure CLI e a Azure PowerShell requerem instalação local; ver [Encriptação do Disco Azure para Linux: Instale ferramentas](disk-encryption-linux.md#install-tools-and-connect-to-azure) para mais detalhes.

### <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure

Antes de utilizar o Azure CLI ou a Azure PowerShell, tem primeiro de ligar-se à sua subscrição Azure. Fá-lo [assinando com a Azure CLI](/cli/azure/authenticate-azure-cli), [assinando com a Azure Powershell,](/powershell/azure/authenticate-azureps)ou fornecendo as suas credenciais ao portal Azure quando solicitado.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
 
## <a name="next-steps"></a>Passos seguintes

- [Encriptação de disco Azure pré-requisitos do script CLI](https://github.com/ejarvi/ade-cli-getting-started)
- [Encriptação de disco Azure pré-requisitos PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Aprenda [cenários de encriptação do disco Azure em VMs Linux](disk-encryption-linux.md)
- Saiba como [resolver problemas na encriptação do disco Azure](disk-encryption-troubleshooting.md)
- Leia os scripts da amostra de [encriptação do disco Azure](disk-encryption-sample-scripts.md)
