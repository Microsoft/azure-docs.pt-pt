---
title: Criar e configurar um cofre de chaves para o Azure Disk Encryption
description: Este artigo fornece passos para criar e configurar um cofre chave para uso com encriptação de disco azure
ms.service: virtual-machines
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 7c3d70610f8b26af17c5117896f4654a175473d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72245243"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Criar e configurar um cofre de chaves para o Azure Disk Encryption

A Encriptação azure Disk usa o Cofre de Chave Azure para controlar e gerir chaves e segredos de encriptação do disco.  Para mais informações sobre os cofres chave, consulte [Start start with Azure Key Vault](../../key-vault/key-vault-get-started.md) e Secure your key [vault](../../key-vault/key-vault-secure-your-key-vault.md). 

> [!WARNING]
> - Se já utilizou anteriormente a Encriptação do Disco Azure com a AD Azure para encriptar um VM, tem de continuar a utilizar esta opção para encriptar o seu VM. Consulte [criar e configurar um cofre chave para encriptação de disco azure com AD Azure (versão anterior)](disk-encryption-key-vault-aad.md) para obter detalhes.

Criar e configurar um cofre chave para utilização com encriptação de disco azure envolve três passos:

1. Criar um grupo de recursos, se necessário.
2. A criar um cofre chave. 
3. Definindo políticas de acesso avançados do cofre chave.

Estes passos são ilustrados nos seguintes arranques rápidos:

- [Criar e encriptar uma VM do Windows com a CLI do Azure](disk-encryption-cli-quickstart.md)
- [Criar e encriptar uma VM do Windows com o Azure PowerShell](disk-encryption-cli-quickstart.md)

Também pode, se desejar, gerar ou importar uma chave de encriptação (KEK).

> [!Note]
> Os passos neste artigo são automatizados no [pré-requisito de encriptação](https://github.com/ejarvi/ade-cli-getting-started) do disco Azure e na [encriptação do disco Azure pré-requisitos do script PowerShell](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Instale ferramentas e ligue-se ao Azure

Os passos deste artigo podem ser concluídos com o [Azure CLI,](/cli/azure/)o [módulo Azure PowerShell Az,](/powershell/azure/overview)ou o [portal Azure](https://portal.azure.com).

Enquanto o portal está acessível através do seu navegador, o Azure CLI e o Azure PowerShell exigem a instalação local; ver [encriptação do disco Azure para Windows: Instale ferramentas](disk-encryption-windows.md#install-tools-and-connect-to-azure) para mais detalhes.

### <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure

Antes de utilizar o Azure CLI ou o Azure PowerShell, tem de se ligar primeiro à subscrição do Azure. Faça-o assinando com o [Azure CLI,](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)assinando com a [Azure Powershell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)ou fornecendo as suas credenciais ao portal Azure quando solicitado.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Passos seguintes

- [Encriptação do disco azure pré-requisitos script CLI](https://github.com/ejarvi/ade-cli-getting-started)
- [Encriptação de disco azure pré-requisitos PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Conheça cenários de [encriptação de discos azure em VMs do Windows](disk-encryption-windows.md)
- Saiba como resolver a [encriptação do disco azure](disk-encryption-troubleshooting.md)
- Leia os scripts da amostra de [encriptação do disco azure](disk-encryption-sample-scripts.md)
