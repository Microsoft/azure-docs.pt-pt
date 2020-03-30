---
title: Configure balancer de carga TCP tempo limite inativo em Azure
titleSuffix: Azure Load Balancer
description: Neste artigo, aprenda a configurar o tempo inativo do Azure Load Balancer TCP.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: d0bb73b58aa23e5f7eb784772acf37b05df463ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456833"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Configure as definições de tempo de tempo inativa do TCP para o Equilíbrio de Carga Sinuoso Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar a PowerShell localmente, também precisa de correr `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="tcp-idle-timeout"></a>TCP Idle Timeout
O Equilíbrio de Carga Azure tem um intervalo de tempo inativo de 4 minutos a 30 minutos. Por defeito, está definido para 4 minutos. Se um período de inatividade for superior ao valor de tempo limite, não há garantiade que a sessão de TCP ou HTTP seja mantida entre o cliente e o seu serviço na nuvem.

Quando a ligação estiver fechada, a aplicação do seu cliente pode receber a seguinte mensagem de erro: "A ligação subjacente foi fechada: Uma ligação que se esperava que fosse mantida viva foi fechada pelo servidor."

Uma prática comum é usar um TCP manter-se vivo. Esta prática mantém a ligação ativa por um período mais longo. Para mais informações, consulte estes [exemplos .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Com a manutenção ativada, os pacotes são enviados durante períodos de inatividade na ligação. Os pacotes de keep-alive garantem que o valor de tempo inativo não é atingido e a ligação é mantida por um longo período.

A definição funciona apenas para ligações de entrada. Para evitar perder a ligação, configure o TCP manter-vivo com um intervalo inferior ao tempo inativo ou aumentar o valor de tempo inativo. Para suportar estes cenários, foi adicionado suporte para um tempo de paragem inativo configurável.

A tCP mantém-se viva para cenários em que a vida útil da bateria não é um constrangimento. Não é recomendado para aplicações móveis. A utilização de um TCP manejá-lo vivo numa aplicação móvel pode drenar a bateria do dispositivo mais rapidamente.

![Intervalo de tempo para tcp](./media/load-balancer-tcp-idle-timeout/image1.png)

As seguintes secções descrevem como alterar as definições de tempo inativo para os recursos públicos de IP e de equilíbrio de carga.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Configure o tempo de tempo tCP para o seu IP público de nível de exemplo para 15 minutos

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` é opcional. Se não estiver definido, o tempo de paragem padrão é de 4 minutos. O intervalo de tempo aceitável é de 4 a 30 minutos.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>Delineie o tempo de tempo tCP numa regra equilibrada em carga para 15 minutos

Para definir o tempo inativo para um equilibrador de carga, o 'IdleTimeoutInMinutes' é definido na regra equilibrada da carga. Por exemplo:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Passos seguintes

[Visão geral do equilíbrio de carga interna](load-balancer-internal-overview.md)

[Começar a configurar um equilibrador de carga virado para a Internet](quickstart-create-standard-load-balancer-powershell.md)

[Configurar um modo de distribuição de balanceador de carga](load-balancer-distribution-mode.md)
