---
title: Configurar o tempo limite de TCP ocioso do balanceador de carga no Azure
titleSuffix: Azure Load Balancer
description: Neste artigo, saiba como configurar Azure Load Balancer tempo limite de ociosidade de TCP.
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
ms.openlocfilehash: 565707b0e081a495f01f369125584038981b4ae8
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834656"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Definir configurações de tempo limite de ociosidade de TCP para Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver executando o PowerShell localmente, terá também de executar `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="tcp-idle-timeout"></a>Tempo limite de ociosidade de TCP
Em sua configuração padrão, Azure Load Balancer tem uma configuração de tempo limite de ociosidade de 4 minutos. Se um período de inatividade for maior do que o valor de tempo limite, não haverá garantia de que a sessão TCP ou HTTP seja mantida entre o cliente e o serviço de nuvem.

Quando a conexão é fechada, o aplicativo cliente pode receber a seguinte mensagem de erro: "a conexão subjacente foi fechada: uma conexão que deveria ser mantida ativa foi fechada pelo servidor".

Uma prática comum é usar um TCP Keep-Alive. Essa prática mantém a conexão ativa por um período mais longo. Para obter mais informações, consulte estes [exemplos do .net](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Com keep-alive habilitado, os pacotes são enviados durante períodos de inatividade na conexão. Os pacotes Keep-Alive garantem que o valor de tempo limite de ociosidade não seja atingido e a conexão seja mantida por um longo período.

A configuração funciona apenas para conexões de entrada. Para evitar a perda da conexão, configure o TCP Keep-Alive com um intervalo menor que a configuração de tempo limite de ociosidade ou aumente o valor de tempo limite de ociosidade. Para dar suporte a esses cenários, o suporte a um tempo limite de ociosidade configurável foi adicionado. Agora você pode defini-lo por uma duração de 4 a 30 minutos.

O TCP Keep-Alive funciona para cenários em que a vida útil da bateria não é uma restrição. Não é recomendável para aplicativos móveis. Usar um TCP Keep-Alive em um aplicativo móvel pode drenar a bateria do dispositivo mais rapidamente.

![Tempo limite de TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

As seções a seguir descrevem como alterar as configurações de tempo limite de ociosidade para os recursos de IP público e de balanceador de carga.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Configurar o tempo limite de TCP para o IP público em nível de instância para 15 minutos

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` é opcional. Se não estiver definido, o tempo limite padrão será de 4 minutos. O intervalo de tempo limite aceitável é de 4 a 30 minutos.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>Definir o tempo limite de TCP em uma regra de balanceamento de carga para 15 minutos

Para definir o tempo limite de ociosidade para um balanceador de carga, o ' IdleTimeoutInMinutes ' é definido na regra de balanceamento de carga. Por exemplo:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Passos seguintes

[Visão geral do balanceador de carga interno](load-balancer-internal-overview.md)

[Introdução à configuração de um balanceador de carga voltado para a Internet](load-balancer-get-started-internet-arm-ps.md)

[Configurar um modo de distribuição de balanceador de carga](load-balancer-distribution-mode.md)
