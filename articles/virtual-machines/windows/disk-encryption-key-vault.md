---
title: Criar e configurar um cofre de chaves para o Azure Disk Encryption
description: Este artigo fornece passos para criar e configurar um cofre chave para uso com encriptação de disco Azure num VM do Windows.
ms.service: virtual-machines
ms.subservice: security
ms.topic: how-to
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 9d9d3d8456e0623ea3f1ef17c5f9f7acb28d0ecd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90977900"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Criar e configurar um cofre de chaves para o Azure Disk Encryption

A Azure Disk Encryption usa o Cofre da Chave Azure para controlar e gerir chaves e segredos de encriptação de discos.  Para obter mais informações sobre cofres chave, consulte [Começar com o Cofre da Chave Azure](../../key-vault/general/overview.md) e Proteger o cofre da [chave.](../../key-vault/general/secure-your-key-vault.md) 

> [!WARNING]
> - Se já utilizou a Encriptação do Disco Azure com AZure AD para encriptar um VM, deve continuar a utilizar esta opção para encriptar o seu VM. Consulte [criar e configurar um cofre chave para encriptação de disco Azure com Azure AD (versão anterior)](disk-encryption-key-vault-aad.md) para mais detalhes.

Criar e configurar um cofre chave para uso com encriptação de disco Azure envolve três passos:

> [!Note]
> Tem de selecionar a opção nas definições de política de acesso do Azure Key Vault para permitir o acesso à Encriptação do Disco Azure para encriptação de volume. Se ativou a firewall no cofre da chave, tem de ir ao separador 'Networking' no cofre de teclas e permitir o acesso aos Serviços Fidedignos da Microsoft. 

1. Criar um grupo de recursos, se necessário.
2. Criando um cofre de chaves. 
3. Definição de políticas avançadas de acesso ao cofre de chaves.

Estes passos são ilustrados nos seguintes quickstarts:

- [Criar e encriptar uma VM do Windows com a CLI do Azure](disk-encryption-cli-quickstart.md)
- [Criar e encriptar uma VM do Windows com o Azure PowerShell](disk-encryption-powershell-quickstart.md)

Pode também, se desejar, gerar ou importar uma chave de encriptação chave (KEK).

> [!Note]
> Os passos deste artigo são automatizados na encriptação do [disco Azure pré-requisitos do script CLI](https://github.com/ejarvi/ade-cli-getting-started) e da encriptação do [disco Azure pré-requisitos do script PowerShell](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Instale ferramentas e ligue-se ao Azure

Os passos deste artigo podem ser preenchidos com o [Azure CLI,](/cli/azure/)o [módulo Azure PowerShell Az,](/powershell/azure/)ou o [portal Azure](https://portal.azure.com).

Enquanto o portal é acessível através do seu navegador, a Azure CLI e a Azure PowerShell requerem instalação local; ver [Encriptação do Disco Azure para Windows: Instale ferramentas](disk-encryption-windows.md#install-tools-and-connect-to-azure) para mais detalhes.

### <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure

Antes de utilizar o Azure CLI ou a Azure PowerShell, tem primeiro de ligar-se à sua subscrição Azure. Fá-lo [assinando com a Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [assinando com a Azure Powershell,](/powershell/azure/authenticate-azureps?view=azps-2.5.0)ou fornecendo as suas credenciais ao portal Azure quando solicitado.

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
- Aprenda [cenários de encriptação do disco Azure em VMs do Windows](disk-encryption-windows.md)
- Saiba como [resolver problemas na encriptação do disco Azure](disk-encryption-troubleshooting.md)
- Leia os scripts da amostra de [encriptação do disco Azure](disk-encryption-sample-scripts.md)
