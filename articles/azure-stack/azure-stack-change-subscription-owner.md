---
title: Atualizar o proprietário da subscrição de utilizador do Azure Stack | Documentos da Microsoft
description: Altere o proprietário de faturação para subscrições de utilizador do Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: shnatara
ms.lastreviewed: 10/19/2018
ms.openlocfilehash: 1d9dd7d19c196679ead9b552bcf296b4acd4ca68
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842894"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Alterar o proprietário de uma subscrição de utilizador do Azure Stack

Operadores do Azure Stack podem utilizar o PowerShell para alterar o proprietário de faturação de subscrição de utilizador. Por exemplo, é uma das razões para alterar o proprietário substituir um utilizador que deixa a sua organização.

Existem dois tipos de *proprietários* que são atribuídas a uma subscrição:

- **Proprietário de faturação**: Por predefinição, o proprietário de faturação é a conta de utilizador que obtém a subscrição a partir de uma oferta e, em seguida, é o proprietário da relação de faturação para essa subscrição. Esta conta também é um administrador da subscrição. Apenas uma conta de utilizador pode ter essa designação sobre uma subscrição. Um proprietário de faturação, muitas vezes, é um líder de organização ou equipa.

  Pode utilizar o cmdlet do PowerShell [Set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) para alterar o proprietário de faturação.  

- **Proprietários adicionados através de funções RBAC** – os utilizadores adicionais podem ser concedidos a **proprietário** função com o [controlo de acesso baseado em funções](azure-stack-manage-permissions.md) sistema (RBAC). Qualquer número de contas de utilizador adicionais pode ser adicionado como proprietários para complementar o proprietário de faturação. Os proprietários de adicionais também são administradores da subscrição e tem todos os privilégios para a subscrição, exceto a permissão para eliminar o proprietário de faturação.

  Pode utilizar o PowerShell para gerir os proprietários de adicionais. Para obter mais informações, consulte [este artigo](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>Alterar o proprietário de faturação

Execute o script seguinte para alterar o proprietário de faturação de subscrição de utilizador. O computador que utiliza para executar o script tem de ligar ao Azure Stack e executar o módulo do Azure Stack do PowerShell 1.3.0 ou posterior. Para obter mais informações, consulte [instalar o Azure Stack do PowerShell](azure-stack-powershell-install.md).

>[!NOTE]
>Num multi-inquilino do Azure Stack, o novo proprietário tem de estar no mesmo diretório que o proprietário existente. Antes de poder fornecer propriedade da subscrição a um utilizador que está em outro diretório, primeiro tem [convidar o utilizador como convidado no seu diretório](../active-directory/b2b/add-users-administrator.md).

Substitua os seguintes valores no script antes da execução:

- **$ArmEndpoint**: O ponto final do Gestor de recursos para o seu ambiente.
- **$TenantId**: O ID de inquilino.
- **$SubscriptionId**: ID da subscrição.
- **$OwnerUpn**: Uma conta, por exemplo **usuário\@example.com**, para adicionar como o novo proprietário de faturação.

```powershell
# Set up Azure Stack admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

## <a name="next-steps"></a>Passos Seguintes

- [Gerir controlo de acesso baseado em funções](azure-stack-manage-permissions.md)
