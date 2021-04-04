---
title: Hóspede família OS 1 aviso de aposentadoria | Microsoft Docs
description: Fornece informações sobre quando aconteceu a aposentadoria Azure Guest OS Family 1 e como determinar se você é afetado
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
ms.service: cloud-services
ms.topic: article
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: 7f6d3feee95d4cce654b2cc1547b8bd7f4eb45d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743020"
---
# <a name="guest-os-family-1-retirement-notice"></a>Aviso de extinção de SO convidado de Família 1

A reforma da Família OS 1 foi anunciada pela primeira vez em 1 de junho de 2013.

**2 de setembro de 2014** O sistema operativo Azure Guest (Guest OS) Family 1.x, baseado no sistema operativo Windows Server 2008, foi oficialmente retirado. Todas as tentativas de implantação de novos serviços ou de atualização dos serviços existentes utilizando a Família 1 falharão com uma mensagem de erro informando-o de que o Guest OS Family 1 foi reformado.

**3 de novembro de 2014** O apoio alargado à Família OS 1 terminou e está totalmente reformado. Todos os serviços ainda na Família 1 serão impactados. Podemos parar esses serviços a qualquer momento. Não há garantias de que os seus serviços continuem a funcionar a menos que os atualize manualmente.

Se tiver dúvidas adicionais, visite a [página de perguntas do Microsoft Q&Uma página de perguntas para serviços na Nuvem](/answers/topics/azure-cloud-services.html) ou contacte o suporte do [Azure](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Está afetado?
Os seus Serviços cloud são afetados se algum dos seguintes se aplicar:

1. Tem um valor de "osFamily = "1" explicitamente especificado no ficheiro ServiceConfiguration.cscfg para o seu Serviço cloud.
2. Não tem um valor para osFamily explicitamente especificado no ficheiro ServiceConfiguration.cscfg para o seu Serviço cloud. Atualmente, o sistema utiliza o valor padrão de "1" neste caso.
3. O portal Azure lista o valor familiar do Sistema Operativo convidado como "Windows Server 2008".

Para descobrir qual dos seus serviços na nuvem está a funcionar que o OS Family, pode executar o seguinte script em Azure PowerShell, embora tenha de [configurar o Azure PowerShell](/powershell/azure/) primeiro. Para mais informações sobre o script, consulte [Azure Guest OS Family 1 End of Life: June 2014](/archive/blogs/ryberry/azure-guest-os-family-1-end-of-life-june-2014).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Os seus serviços na nuvem serão impactados pela reforma da FAMÍLIA OS 1 se a coluna osFamily na saída do script estiver vazia ou contiver um "1".

## <a name="recommendations-if-you-are-affected"></a>Recomendações se for afetado
Recomendamos que emigre as suas funções de Cloud Service para uma das famílias de hóspedes apoiadas:

**Família de OS convidados 4.x** - Windows Server 2012 R2 *(recomendado)*

1. Certifique-se de que a sua aplicação está a utilizar o SDK 2.1 ou posteriormente com o quadro .NET 4.0, 4.5 ou 4.5.1.
2. Desloje o atributo osFamily a "4" no ficheiro ServiceConfiguration.cscfg e reimplante o seu serviço de cloud.

**Família de OS convidados 3.x** - Windows Server 2012

1. Certifique-se de que a sua aplicação está a utilizar o SDK 1.8 ou mais tarde com o quadro .NET 4.0 ou 4.5.
2. Desloje o atributo osFamily a "3" no ficheiro ServiceConfiguration.cscfg e reimplante o seu serviço de cloud.

**Família de OS convidados 2.x** - Windows Server 2008 R2

1. Certifique-se de que a sua aplicação está a utilizar o SDK 1.3 e acima com o quadro .NET 3.5 ou 4.0.
2. Desloje o atributo osFamily a "2" no ficheiro ServiceConfiguration.cscfg e reimplante o seu serviço de cloud.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Apoio alargado à família guest OS 1 terminou 3 de novembro de 2014
Os serviços em nuvem na família Guest OS 1 já não são suportados. Migrar para fora da família 1 o mais rápido possível para evitar perturbações de serviço.  

## <a name="next-steps"></a>Passos seguintes
Reveja as [últimas versões do Guest OS](cloud-services-guestos-update-matrix.md).