---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 05794a046fdcb15a91145a75717a6a454d15a8da
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "72511510"
---
A encriptação do disco Azure pode ser ativada e gerida através do [Azure CLI](/cli/azure) e [da Azure PowerShell](/powershell/azure/new-azureps-module-az). Para tal, tem de instalar as ferramentas localmente e ligar-se à sua subscrição Azure.

### <a name="azure-cli"></a>CLI do Azure

O [Azure CLI 2.0](/cli/azure) é uma ferramenta de linha de comando para gerir os recursos da Azure. O CLI foi concebido para consultar os dados de forma flexível, apoiar operações de longo prazo como processos de não bloqueio e facilitar a scripting. Pode instalá-lo localmente seguindo os passos na [Instalação do Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Para [iniciar sessão na sua conta Azure com o Azure CLI,](/cli/azure/authenticate-azure-cli)utilize o comando [de login az.](/cli/azure/reference-index?view=azure-cli-latest#az-login)

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
O [módulo Azure PowerShell az](/powershell/azure/new-azureps-module-az) fornece um conjunto de cmdlets que utiliza o modelo [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) para gerir os seus recursos Azure. Pode usá-lo no seu navegador com [a Azure Cloud Shell,](/azure/cloud-shell/overview)ou pode instalá-lo na sua máquina local utilizando as instruções no [Install the Azure PowerShell module](/powershell/azure/install-az-ps). 

Se já o tiver instalado localmente, certifique-se de que utiliza a versão mais recente da versão Azure PowerShell SDK para configurar a Encriptação do Disco Azure. Descarregue a versão mais recente do lançamento do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

Para [iniciar súm na sua conta Azure com a Azure PowerShell,](/powershell/azure/authenticate-azureps?view=azps-2.5.0)utilize o [cmdlet Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)

```powershell
Connect-AzAccount
```

Se tiver várias subscrições e quiser especificar uma, utilize o cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) para listá-las, seguida da cmdlet [Set-AzContext:](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0)

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Executar o [cmdlet Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) verificará se a subscrição correta foi selecionada.

Para confirmar a instalação dos cmdlets de encriptação do disco Azure, utilize o cmdlet [do comando Get-command:](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6)
     
```powershell
Get-command *diskencryption*
```
Para obter mais informações, consulte [Começar com a Azure PowerShell](/powershell/azure/get-started-azureps). 