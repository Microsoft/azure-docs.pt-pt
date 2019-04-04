---
title: Utilizar o módulo de política do Azure Stack | Documentos da Microsoft
description: Saiba como restringir uma subscrição do Azure se comportar como uma subscrição do Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2019
ms.author: sethm
ms.lastreviewed: 03/26/2019
ms.openlocfilehash: 9fade97bbe783cf156f5b73523bc0834a34df926
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487452"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Gerir a política do Azure com o módulo de política do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O módulo de política do Azure Stack permite-lhe configurar uma subscrição do Azure com o mesmo controle de versão e a disponibilidade do serviço como o Azure Stack. O módulo utiliza a [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) cmdlet do PowerShell para criar uma política do Azure, o que limita os tipos de recursos e serviços disponíveis numa assinatura. Em seguida, criar uma atribuição de política no âmbito adequado ao utilizar o [New-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) cmdlet. Depois de configurar a política, pode utilizar a sua subscrição do Azure para desenvolver aplicações visadas para o Azure Stack.

## <a name="install-the-module"></a>Instalar o módulo

1. Instale a versão necessária do módulo AzureRM PowerShell, conforme descrito no passo 1 da [instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md).
2. [Transferir as ferramentas do Azure Stack a partir do GitHub](azure-stack-powershell-download.md).
3. [Configurar o PowerShell para utilização com o Azure Stack](azure-stack-powershell-configure-user.md).
4. Importe o módulo de AzureStack.Policy.psm1:


   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Aplicar a política de subscrição do Azure

Pode utilizar o seguinte comando para aplicar uma política do Azure Stack predefinida em relação a sua subscrição do Azure. Antes de executar este comando, substitua `Azure subscription name` com o nome da sua subscrição do Azure:

```powershell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

## <a name="apply-policy-to-a-resource-group"></a>Aplicar a política para um grupo de recursos

Pode querer aplicar as políticas que são mais granulares. Por exemplo, poderá ter outros recursos em execução na mesma subscrição. Pode definir o âmbito de aplicação de política para um grupo de recursos específico, o que permite que teste seus aplicativos para o Azure Stack com recursos do Azure. Antes de executar o seguinte comando, substitua `Azure subscription name` com o nome da sua subscrição do Azure:

```powershell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>Política em ação

Depois de implementar a política do Azure, recebe um erro ao tentar implementar um recurso que é proibido pela política:

![Resultado de falha de implementação de recursos devido à restrição de política](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Passos Seguintes

* [Implementar modelos com o PowerShell](azure-stack-deploy-template-powershell.md)
* [Implementar modelos com a CLI do Azure](azure-stack-deploy-template-command-line.md)
* [Implementar modelos com o Visual Studio](azure-stack-deploy-template-visual-studio.md)
