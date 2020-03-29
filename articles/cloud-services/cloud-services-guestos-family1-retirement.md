---
title: Família de hóspedes da família OS 1 aviso de aposentadoria / Microsoft Docs
description: Fornece informações sobre quando aconteceu a reforma do Os Família 1 do Os 1 do Azure Guest e como determinar se você é afetado
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
ms.service: cloud-services
ms.topic: article
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: ae2df6f47d99fc5d452a6d3ea70f2dd2e4e7416b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68359578"
---
# <a name="guest-os-family-1-retirement-notice"></a>Aviso de extinção de SO convidado de Família 1
A reforma da Família OS 1 foi anunciada pela primeira vez em 1 de junho de 2013.

2 de setembro de **2014** O sistema operativo Azure Guest (Guest OS) Family 1.x, baseado no sistema operativo Windows Server 2008, foi oficialmente retirado. Todas as tentativas de implantar novos serviços ou atualizar os serviços existentes usando a Família 1 falharão com uma mensagem de erro informando-o de que o Hóspede OS Family 1 foi retirado.

**3 de novembro de 2014** O apoio alargado à Guest OS Family 1 terminou e está totalmente aposentado. Todos os serviços ainda na Família 1 serão afetados. Podemos parar esses serviços a qualquer momento. Não há garantias de que os seus serviços continuem a funcionar a menos que os atualize manualmente.

Se tiver dúvidas adicionais, visite os [Cloud Services Forums](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) ou contacte o [suporte azure](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Está afetado?
Os seus Serviços cloud são afetados se qualquer um dos seguintes se aplicar:

1. Tem um valor de "osFamily = "1" explicitamente especificado no ficheiro ServiceConfiguration.cscfg para o seu Serviço cloud.
2. Não tem um valor para ososFamiliarmente especificado no ficheiro ServiceConfiguration.cscfg para o seu Serviço cloud. Atualmente, o sistema utiliza o valor padrão de "1" neste caso.
3. O portal Azure lista o seu valor familiar do Sistema Operativo Convidado como "Windows Server 2008".

Para descobrir qual dos seus serviços na nuvem está a funcionar qual o OS Family, pode executar o seguinte script em Azure PowerShell, embora tenha de configurar primeiro o [Azure PowerShell.](/powershell/azureps-cmdlets-docs) Para mais informações sobre o guião, consulte [Azure Guest OS Family 1 End of Life: junho de 2014](https://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Os seus serviços na nuvem serão impactados pela reforma da Família OS 1 se a coluna osFamily na saída do script estiver vazia ou contiver um "1".

## <a name="recommendations-if-you-are-affected"></a>Recomendações se for afetado
Recomendamos que emigra as suas funções de Serviço cloud para uma das famílias de ossias de hóspedes apoiadas:

**Família de Os 4.x** - Windows Server 2012 R2 *(recomendado)*

1. Certifique-se de que a sua aplicação está a utilizar SDK 2.1 ou mais tarde com a estrutura .NET 4.0, 4.5 ou 4.5.1.
2. Detete o atributo osFamily a "4" no ficheiro ServiceConfiguration.cscfg e recoloque o seu serviço na nuvem.

**Família de Os 3.x** - Windows Server 2012

1. Certifique-se de que a sua aplicação está a utilizar SDK 1.8 ou mais tarde com a estrutura .NET 4.0 ou 4.5.
2. Detete o atributo osFamily a "3" no ficheiro ServiceConfiguration.cscfg e recoloque o seu serviço na nuvem.

**Família de Os OS convidado 2.x** - Windows Server 2008 R2

1. Certifique-se de que a sua aplicação está a utilizar SDK 1.3 ou superior com a estrutura .NET 3.5 ou 4.0.
2. Detete o atributo osFamily a "2" no ficheiro ServiceConfiguration.cscfg e recoloque o seu serviço na nuvem.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Apoio alargado para a Família Os 1 de Hóspedes terminou em 3 de novembro de 2014
Os serviços de nuvem na família 1 do Guest OS já não são suportados. Migrar para fora da família 1 o mais rápido possível para evitar perturbações no serviço.  

## <a name="next-steps"></a>Passos seguintes
Reveja os mais recentes [lançamentos do Guest OS](cloud-services-guestos-update-matrix.md).
