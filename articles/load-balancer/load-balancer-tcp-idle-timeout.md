---
title: Configurar o tempo limite de marcha lenta TCP em Azure
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
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: 38db681655a839983ebf38e94ec28eb05ed65d1f
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/16/2020
ms.locfileid: "84808582"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Configurar as definições de tempo limite de marcha lenta da TCP para o balançador de carga Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar o PowerShell localmente, também precisa de correr `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="tcp-idle-timeout"></a>TCP Inativo Tempo
O Azure Load Balancer tem uma definição de tempo limite de 4 minutos a 30 minutos. Por predefinição, está programado para 4 minutos. Se um período de inatividade for maior do que o valor de tempo limite, não há garantias de que a sessão TCP ou HTTP seja mantida entre o cliente e o seu serviço na nuvem.

Quando a ligação estiver fechada, a aplicação do seu cliente poderá receber a seguinte mensagem de erro: "A ligação subjacente foi encerrada: Uma ligação que se esperava que fosse mantida viva foi fechada pelo servidor."

Uma prática comum é usar um TCP de vida. Esta prática mantém a ligação ativa por um período mais longo. Para obter mais informações, consulte estes [exemplos .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Com o tempo de vida ativo, os pacotes são enviados durante períodos de inatividade na ligação. Os pacotes de manutenção vivos asseguram que o valor de tempo de saída não é atingido e a ligação é mantida por um longo período de tempo.

A regulação funciona apenas para ligações de entrada. Para evitar perder a ligação, configufique a fixação do TCP com um intervalo inferior ao da definição de tempo de marcha lenta inativa ou aumente o valor de tempo de marcha lenta inativo. Para apoiar estes cenários, foi adicionado o apoio a um tempo limite configurável.

A TCP mantém-se viva para cenários em que a vida útil da bateria não é um constrangimento. Não é recomendado para aplicações móveis. A utilização de um TCP de vida numa aplicação móvel pode drenar a bateria do dispositivo mais rapidamente.

![Tempo limite de TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

As secções seguintes descrevem como alterar as definições de tempo limite de marcha lenta para IP público e recursos do balançador de carga.

>[!NOTE]
> O tempo limite de inatividade da TCP não afeta as regras de equilíbrio de carga no protocolo UDP.


## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Configure o tempo limite de TCP para o seu IP público de nível de exemplo para 15 minutos

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` é opcional. Se não estiver definido, o tempo limite de tempo é de 4 minutos. O intervalo aceitável é de 4 a 30 minutos.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>Desabrague o tempo limite de TCP numa regra equilibrada de carga para 15 minutos

Para definir o tempo de inatividade para um balanceador de carga, o 'IdleTimeoutInMinutes' é definido na regra equilibrada de carga. Por exemplo:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Passos seguintes

[Visão geral do balançador de carga interna](load-balancer-internal-overview.md)

[Começar a configurar um equilibrador de carga virado para a Internet](quickstart-create-standard-load-balancer-powershell.md)

[Configurar um modo de distribuição de balanceador de carga](load-balancer-distribution-mode.md)
