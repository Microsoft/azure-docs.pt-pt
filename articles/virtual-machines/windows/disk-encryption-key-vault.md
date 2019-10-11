---
title: Criando e configurando um cofre de chaves para Azure Disk Encryption
description: Este artigo fornece etapas para criar e configurar um cofre de chaves para uso com Azure Disk Encryption
ms.service: virtual-machines
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 7c3d70610f8b26af17c5117896f4654a175473d2
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245243"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Criando e configurando um cofre de chaves para Azure Disk Encryption

Azure Disk Encryption usa Azure Key Vault para controlar e gerenciar chaves de criptografia de disco e segredos.  Para obter mais informações sobre cofres de chaves, consulte [introdução ao Azure Key Vault](../../key-vault/key-vault-get-started.md) e [proteger o cofre de chaves](../../key-vault/key-vault-secure-your-key-vault.md). 

> [!WARNING]
> - Se você tiver usado anteriormente Azure Disk Encryption com o Azure AD para criptografar uma VM, você deve continuar usando essa opção para criptografar sua VM. Consulte [criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)](disk-encryption-key-vault-aad.md) para obter detalhes.

Criar e configurar um cofre de chaves para uso com o Azure Disk Encryption envolve três etapas:

1. Criar um grupo de recursos, se necessário.
2. Criando um cofre de chaves. 
3. Definindo políticas de acesso avançado do cofre de chaves.

Essas etapas são ilustradas nos seguintes guias de início rápido:

- [Criar e criptografar uma VM do Windows com CLI do Azure](disk-encryption-cli-quickstart.md)
- [Criar e criptografar uma VM do Windows com Azure PowerShell](disk-encryption-cli-quickstart.md)

Você também pode, se desejar, gerar ou importar uma chave de criptografia de chave (KEK).

> [!Note]
> As etapas neste artigo são automatizadas no [script da CLI de Azure Disk Encryption pré-requisitos](https://github.com/ejarvi/ade-cli-getting-started) e [Azure Disk Encryption script do PowerShell de pré-requisitos](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Instalar ferramentas e conectar-se ao Azure

As etapas neste artigo podem ser concluídas com o [CLI do Azure](/cli/azure/), o [módulo Azure PowerShell Az](/powershell/azure/overview)ou o [portal do Azure](https://portal.azure.com).

Embora o portal possa ser acessado por meio de seu navegador, CLI do Azure e Azure PowerShell exigem a instalação local; consulte [Azure Disk Encryption para Windows: instalar ferramentas](disk-encryption-windows.md#install-tools-and-connect-to-azure) para obter detalhes.

### <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure

Antes de usar o CLI do Azure ou Azure PowerShell, você deve primeiro se conectar à sua assinatura do Azure. Você faz isso [entrando com CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [entrando com o Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)ou fornecendo suas credenciais para o portal do Azure quando solicitado.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Passos seguintes

- [Script da CLI de pré-requisitos Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started)
- [Script do PowerShell de Azure Disk Encryption pré-requisitos](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Saiba [Azure Disk Encryption cenários em VMs do Windows](disk-encryption-windows.md)
- Saiba como [solucionar problemas Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Leia os [scripts de exemplo do Azure Disk Encryption](disk-encryption-sample-scripts.md)
