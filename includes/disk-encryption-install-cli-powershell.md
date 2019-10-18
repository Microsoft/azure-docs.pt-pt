---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 05794a046fdcb15a91145a75717a6a454d15a8da
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72511510"
---
Azure Disk Encryption pode ser habilitado e gerenciado por meio do [CLI do Azure](/cli/azure) e [Azure PowerShell](/powershell/azure/new-azureps-module-az). Para fazer isso, você deve instalar as ferramentas localmente e conectar-se à sua assinatura do Azure.

### <a name="azure-cli"></a>CLI do Azure

O [CLI do Azure 2,0](/cli/azure) é uma ferramenta de linha de comando para gerenciar recursos do Azure. A CLI foi projetada para consultar dados de maneira flexível, dar suporte a operações de longa execução como processos sem bloqueio e facilitar o script. Você pode instalá-lo localmente seguindo as etapas em [instalar o CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

Para [entrar em sua conta do Azure com o CLI do Azure](/cli/azure/authenticate-azure-cli), use o comando [AZ login](/cli/azure/reference-index?view=azure-cli-latest#az-login) .

```azurecli
az login
```

Se você quiser selecionar um locatário para entrar, use:
    
```azurecli
az login --tenant <tenant>
```

Se você tiver várias assinaturas e quiser especificar uma específica, obtenha sua lista de assinaturas com a lista de [contas AZ](/cli/azure/account#az-account-list) e especifique com o [conjunto de contas AZ](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Para obter mais informações, consulte Introdução [ao CLI do Azure 2,0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
O [módulo Azure PowerShell AZ](/powershell/azure/new-azureps-module-az) fornece um conjunto de cmdlets que usa o modelo de [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) para gerenciar seus recursos do Azure. Você pode usá-lo em seu navegador com [Azure cloud Shell](/azure/cloud-shell/overview), ou pode instalá-lo em seu computador local usando as instruções em [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps). 

Se você já o tiver instalado localmente, certifique-se de usar a versão mais recente da versão Azure PowerShell SDK para configurar Azure Disk Encryption. Baixe a versão mais recente do [Azure PowerShell versão](https://github.com/Azure/azure-powershell/releases).

Para [entrar em sua conta do Azure com Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0), use o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) .

```powershell
Connect-AzAccount
```

Se você tiver várias assinaturas e quiser especificar uma, use o cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) para listá-las, seguidos do cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) :

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Executar o cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) verificará se a assinatura correta foi selecionada.

Para confirmar se os cmdlets Azure Disk Encryption estão instalados, use o cmdlet [Get-Command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) :
     
```powershell
Get-command *diskencryption*
```
Para obter mais informações, consulte [introdução ao Azure PowerShell](/powershell/azure/get-started-azureps). 