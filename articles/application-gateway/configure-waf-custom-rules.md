---
title: Configurar regras personalizadas do firewall do aplicativo Web usando Azure PowerShell
description: Saiba como configurar regras personalizadas do firewall do aplicativo Web usando Azure PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 9e50b47e22f5760c213cd0cafad82ecca592dec8
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263739"
---
# <a name="configure-web-application-firewall-v2-custom-rules-by-using-azure-powershell"></a>Configurar regras personalizadas do firewall do aplicativo Web usando Azure PowerShell

<!--- If you make any changes to the PowerShell in this article, also make the change in the corresponding Sample file: azure-docs-powershell-samples/application-gateway/waf-rules/waf-custom-rules.ps1 --->

Com as regras personalizadas, você pode criar suas próprias regras, que são avaliadas para cada solicitação que passa pelo WAF (firewall do aplicativo Web). Essas regras têm uma prioridade mais alta do que o restante das regras nos conjuntos de regras gerenciadas. Para permitir a personalização completa, as regras personalizadas têm uma ação (para permitir ou bloquear), uma condição de correspondência e um operador.

Este artigo cria um Aplicativo Azure gateway WAF v2 que usa uma regra personalizada. A regra personalizada bloqueará o tráfego se o cabeçalho de solicitação contiver *evilbot*de agente de usuário.

Para exibir mais exemplos de regras personalizadas, consulte [criar e usar regras personalizadas de firewall do aplicativo Web](create-custom-waf-rules.md).

Para executar o código de Azure PowerShell neste artigo em um script contínuo que você pode copiar, colar e executar, consulte [exemplos do PowerShell do aplicativo Azure gateway](powershell-samples.md).

## <a name="prerequisites"></a>Pré-requisitos
* [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Você precisa de um módulo Azure PowerShell. Se você optar por instalar e usar Azure PowerShell localmente, esse script exigirá Azure PowerShell versão do módulo 2.1.0 ou posterior. Faça o seguinte:

  1. Para localizar a versão, execute `Get-Module -ListAvailable Az`. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps).
  2. Para criar uma conexão com o Azure, execute `Connect-AzAccount`.

## <a name="example-script"></a>Script de exemplo

### <a name="set-up-variables"></a>Configurar variáveis

Execute o seguinte código:

```azurepowershell
$rgname = "CustomRulesTest"

$location = "East US"

$appgwName = "WAFCustomRules"
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Execute o seguinte código:

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
```

### <a name="create-a-virtual-network"></a>Criar rede virtual

Execute o seguinte código:

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"

$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"

$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-vip"></a>Criar um VIP público estático

Execute o seguinte código:

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-a-pool-and-front-end-port"></a>Criar um pool e uma porta de front-end

Execute o seguinte código:

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet

$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip

$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 80
```

### <a name="create-a-listener-http-setting-rule-and-autoscale"></a>Criar um ouvinte, uma configuração de HTTP, uma regra e um dimensionamento automático

Execute o seguinte código:

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled

$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3

$sku = New-AzApplicationGatewaySku -Name WAF_v2 -Tier WAF_v2
```

### <a name="create-the-custom-rule-and-apply-it-to-waf-policy"></a>Criar a regra personalizada e aplicá-la à política WAF

Execute o seguinte código:

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestHeaders -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "evilbot" -Transform Lowercase -NegationCondition $False  

$rule = New-AzApplicationGatewayFirewallCustomRule -Name blockEvilBot -Priority 2 -RuleType MatchRule -MatchCondition $condition -Action Block

$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafPolicy -ResourceGroup $rgname -Location $location -CustomRule $rule

$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Execute o seguinte código:

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname `
   -Location $location -BackendAddressPools $pool `
   -BackendHttpSettingsCollection  $poolSetting01 `
   -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
   -FrontendPorts $fp01 -HttpListeners $listener01 `
   -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig `
   -WebApplicationFirewallConfig $wafConfig `
   -FirewallPolicy $wafPolicy
```

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre o Firewall do aplicativo Web](waf-overview.md)
