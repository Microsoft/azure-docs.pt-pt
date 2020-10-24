---
title: Configure o reposição do balançador de carga TCP e o tempo limite de marcha lenta em Azure
titleSuffix: Azure Load Balancer
description: Neste artigo, aprenda a configurar o tempo limite de marcha lenta do Azure Load Balancer TCP.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: allensu
ms.openlocfilehash: b507fbad4d9089d918ae7a85c07f30efcb118476
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487250"
---
# <a name="configure-tcp-idle-timeout-for-azure-load-balancer"></a>Configurar o tempo limite de marcha lenta para O Equilibrador de Carga Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar o PowerShell localmente, também precisa de correr `Connect-AzAccount` para criar uma ligação com o Azure.

O Azure Load Balancer tem a seguinte gama de tempo limite de marcha lenta:

4 minutos a 100 minutos para as regras de saída 4 minutos a 30 minutos para as regras do balançador de carga e regras NAT de entrada

Por predefinição, está programado para 4 minutos. Se um período de inatividade for maior do que o valor de tempo limite, não há garantias de que a sessão TCP ou HTTP seja mantida entre o cliente e o seu serviço na nuvem. Saiba mais sobre [o tempo limite de marcha lenta da TCP.](load-balancer-tcp-reset.md)

As secções seguintes descrevem como alterar as definições de tempo limite de marcha lenta para IP público e recursos do balançador de carga.


## <a name="configure-the-tcp-idle-timeout-for-your-public-ip"></a>Configure o tempo limite de inatividade da TCP para o seu IP público

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` é opcional. Se não estiver definido, o tempo limite de tempo é de 4 minutos. 

## <a name="set-the-tcp-idle-timeout-on-rules"></a>Desave o tempo limite de marcha lenta do TCP sobre as regras

Para definir o tempo de inatividade para um balanceador de carga, o 'IdleTimeoutInMinutes' é definido na regra equilibrada de carga. Por exemplo:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```

## <a name="next-steps"></a>Passos seguintes

[Visão geral do balançador de carga interna](load-balancer-internal-overview.md)

[Começar a configurar um equilibrador de carga virado para a Internet](quickstart-load-balancer-standard-public-powershell.md)

[Configurar um modo de distribuição de balanceador de carga](load-balancer-distribution-mode.md)
