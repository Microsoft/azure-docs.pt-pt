---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 64db4de6628fcd8f3cf160bb2bde1b577219cb10
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729808"
---
A encriptação do disco Azure pode ser ativada e gerida através do [Azure CLI](/cli/azure) e [da Azure PowerShell](/powershell/azure/new-azureps-module-az). Para tal, tem de instalar as ferramentas localmente e ligar-se à sua subscrição Azure.

### <a name="azure-cli"></a>CLI do Azure

O [Azure CLI 2.0](/cli/azure) é uma ferramenta de linha de comando para gerir os recursos da Azure. O CLI foi concebido para consultar os dados de forma flexível, apoiar operações de longo prazo como processos de não bloqueio e facilitar a scripting. Pode instalá-lo localmente seguindo os passos na [Instalação do Azure CLI](/cli/azure/install-azure-cli).

Para [iniciar sessão na sua conta Azure com o Azure CLI,](/cli/azure/authenticate-azure-cli)utilize o comando [de login az.](/cli/azure/reference-index#az-login)

```azurecli
az login
```

Se você gostaria de selecionar um inquilino para iniciar sôm, use:
    
```azurecli
az login --tenant <tenant>
```

Se tiver várias subscrições e quiser especificar uma específica, obtenha a sua lista de subscrições com [a lista de conta az](/cli/azure/account#az-account-list) e especifique com a conta [az definida](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Para mais informações, consulte [Começar com a Azure CLI 2.0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
O [módulo Azure PowerShell az](/powershell/azure/new-azureps-module-az) fornece um conjunto de cmdlets que utiliza o modelo [Azure Resource Manager](../articles/azure-resource-manager/management/overview.md) para gerir os seus recursos Azure. Pode usá-lo no seu navegador com [a Azure Cloud Shell,](../articles/cloud-shell/overview.md)ou pode instalá-lo na sua máquina local utilizando as instruções no [Install the Azure PowerShell module](/powershell/azure/install-az-ps). 

Se já o tiver instalado localmente, certifique-se de que utiliza a versão mais recente da versão Azure PowerShell SDK para configurar a Encriptação do Disco Azure. Descarregue a versão mais recente do lançamento do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

Para [iniciar súm na sua conta Azure com a Azure PowerShell,](/powershell/azure/authenticate-azureps)utilize o [cmdlet Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

```powershell
Connect-AzAccount
```

Se tiver várias subscrições e quiser especificar uma, utilize o cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) para listá-las, seguida da cmdlet [Set-AzContext:](/powershell/module/az.accounts/set-azcontext)

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Executar o [cmdlet Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) verificará se a subscrição correta foi selecionada.

Para confirmar a instalação dos cmdlets de encriptação do disco Azure, utilize o cmdlet [do comando Get-command:](/powershell/module/microsoft.powershell.core/get-command)
     
```powershell
Get-command *diskencryption*
```
Para obter mais informações, consulte [Começar com a Azure PowerShell](/powershell/azure/get-started-azureps).