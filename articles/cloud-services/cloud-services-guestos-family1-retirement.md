---
title: Aviso de aposentadoria da família 1 do SO convidado | Microsoft Docs
description: Fornece informações sobre quando ocorreu a desativação da família 1 do sistema operacional convidado do Azure e como determinar se você foi afetado
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
ms.service: cloud-services
ms.topic: article
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: ae2df6f47d99fc5d452a6d3ea70f2dd2e4e7416b
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359578"
---
# <a name="guest-os-family-1-retirement-notice"></a>Aviso de aposentadoria da família 1 do SO convidado
A aposentadoria da família 1 do sistema operacional foi anunciada primeiro em 1º de junho de 2013.

**2 de setembro de 2014** A família 1. x do sistema operacional convidado do Azure (SO convidado), que se baseia no sistema operacional Windows Server 2008, foi oficialmente desativada. Todas as tentativas de implantar novos serviços ou atualizar os serviços existentes usando a família 1 falharão com uma mensagem de erro informando que a família 1 do sistema operacional convidado foi desativada.

**3 de novembro de 2014** O suporte estendido para a família 1 do sistema operacional convidado foi encerrado e está totalmente desativado. Todos os serviços que ainda estiverem na família 1 serão afetados. Poderemos parar esses serviços a qualquer momento. Não há nenhuma garantia de que seus serviços continuarão a ser executados, a menos que você os atualize manualmente.

Se você tiver outras dúvidas, visite os [fóruns de serviços de nuvem](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) ou [entre em contato com o suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Você é afetado?
Os serviços de nuvem serão afetados se qualquer uma das seguintes opções se aplicar:

1. Você tem um valor de "osFamily =" 1 "especificado explicitamente no arquivo Service Configuration. cscfg para seu serviço de nuvem.
2. Você não tem um valor para osFamily especificado explicitamente no arquivo Service Configuration. cscfg para seu serviço de nuvem. Atualmente, o sistema usa o valor padrão "1" nesse caso.
3. O portal do Azure lista o valor da família do sistema operacional convidado como "Windows Server 2008".

Para descobrir quais dos seus serviços de nuvem estão executando qual família de sistema operacional, você pode executar o script a seguir em Azure PowerShell, embora seja necessário [configurar Azure PowerShell](/powershell/azureps-cmdlets-docs) primeiro. Para obter mais informações sobre o script, [consulte fim da vida útil da família 1 do SO convidado do Azure: Junho de](https://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx)2014.

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Seus serviços de nuvem serão afetados pela desativação da família 1 do sistema operacional se a coluna osFamily na saída do script estiver vazia ou contiver um "1".

## <a name="recommendations-if-you-are-affected"></a>Recomendações se você for afetado
Recomendamos que você migre suas funções de serviço de nuvem para uma das famílias de sistema operacional convidado com suporte:

**Família de sistemas operacionais convidados 4. x** -Windows Server 2012 R2 *(recomendado)*

1. Verifique se seu aplicativo está usando o SDK 2,1 ou posterior com o .NET Framework 4,0, 4,5 ou 4.5.1.
2. Defina o atributo osFamily como "4" no arquivo Service Configuration. cscfg e reimplante o serviço de nuvem.

**Família de sistemas operacionais convidados 3. x** -Windows Server 2012

1. Verifique se seu aplicativo está usando o SDK 1,8 ou posterior com o .NET Framework 4,0 ou 4,5.
2. Defina o atributo osFamily como "3" no arquivo Service Configuration. cscfg e reimplante o serviço de nuvem.

**Família de sistemas operacionais convidados 2. x** -Windows Server 2008 R2

1. Verifique se seu aplicativo está usando o SDK 1,3 e superior com o .NET Framework 3,5 ou 4,0.
2. Defina o atributo osFamily como "2" no arquivo Service Configuration. cscfg e reimplante o serviço de nuvem.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>O suporte estendido para a família 1 do sistema operacional convidado terminou em 3 de novembro de 2014
OS serviços de nuvem na família de sistemas operacionais convidados 1 não têm mais suporte. Migre da família 1 o mais rápido possível para evitar a interrupção do serviço.  

## <a name="next-steps"></a>Passos Seguintes
Examine as versões mais recentes do [sistema operacional convidado](cloud-services-guestos-update-matrix.md).
