---
title: Aviso de 1 extinção da família SO convidado | Documentos da Microsoft
description: Fornece informações sobre o quando ocorreu a desativação de 1 de família de SO de convidado do Azure e como determinar se são afetados
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 37b422e9-0713-4a81-a942-f553ef478064
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: d6429766b6aac547fd99279659acb1067298e77c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60518958"
---
# <a name="guest-os-family-1-retirement-notice"></a>Aviso de extinção de 1 de família de SO convidado
A desativação de 1 de família do SO foi anunciada pela primeira vez no dia 1 de Junho de 2013.

**2 de Setembro de 2014** The Azure sistema operativo (SO convidado) família 1.x, que se baseia no sistema operativo Windows Server 2008, foram retiradas oficialmente. Todas as tentativas para implementar novos serviços ou atualizar os serviços existentes com a família 1 irão falhar com uma mensagem de erro informando-o de que a família de SO convidado 1 foi extinguido.

**3 de Novembro de 2014** terminou de suporte estendido para 1 de família de SO convidado e totalmente é retirado. Todos os serviços ainda na família 1 serão afetados. Podemos pode parar os serviços em qualquer altura. Não há nenhuma garantia que seus serviços vão continuar a executar, a menos que tem de atualizar manualmente-los por conta própria.

Se necessitar de mais informações, visite o [fóruns de serviços Cloud](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) ou [contacte o suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>São afetados?
Os serviços Cloud são afetados se aplica-se de qualquer um dos seguintes procedimentos:

1. Tem um valor de "osFamily ="1"especificado explicitamente no arquivo serviceconfiguration. Cscfg do serviço em nuvem.
2. Não tem um valor para osFamily especificado explicitamente no arquivo serviceconfiguration. Cscfg do serviço em nuvem. Atualmente, o sistema usará o valor predefinido de "1" em vez disso.
3. O portal do Azure apresenta uma lista de seu valor de família do sistema operativo convidado como "Windows Server 2008".

Para localizar quais dos seus serviços cloud estão em execução que família de SO, pode execute o seguinte script no Azure PowerShell, embora tem [configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) primeiro. Para obter mais informações sobre o script, consulte [Azure convidado SO família 1 fim de vida: Junho de 2014](https://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Os serviços cloud vão ser afetados por extinção de SO família 1 se a coluna de osFamily na saída do script está vazia ou contém um "1".

## <a name="recommendations-if-you-are-affected"></a>Recomendações se for afectado
Recomendamos que migre as funções do serviço em nuvem a uma das famílias de SO convidados suportados:

**SO convidado família 4.x** -Windows Server 2012 R2 *(recomendado)*

1. Certifique-se de que a aplicação está a utilizar o SDK 2.1 ou posterior com o .NET framework 4.0, 4.5 ou 4.5.1.
2. Definir o atributo osFamily para "4" no arquivo serviceconfiguration. Cscfg e voltar a implementar seu serviço cloud.

**SO convidado 3.x família** -Windows Server 2012

1. Certifique-se de que a aplicação está a utilizar o SDK 1.8 ou posterior com o .NET framework 4.0 ou 4.5.
2. Definir o atributo osFamily "3" no arquivo serviceconfiguration. Cscfg e voltar a implementar seu serviço cloud.

**SO convidado 2.x família** -Windows Server 2008 R2

1. Certifique-se de que seu aplicativo está usando o SDK 1.3 e posterior com o .NET framework 3.5 ou 4.0.
2. Definir o atributo osFamily "2" no arquivo serviceconfiguration. Cscfg e voltar a implementar seu serviço cloud.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>O suporte expandido para 1 de família de SO convidado terminou 3 de Novembro de 2014
Serviços em nuvem na família de SO convidado 1 já não são suportados. Migração desativada família 1 logo que possível para evitar a interrupção de serviço.  

## <a name="next-steps"></a>Passos Seguintes
Reveja a versão mais recente [versões de SO convidado](cloud-services-guestos-update-matrix.md).
