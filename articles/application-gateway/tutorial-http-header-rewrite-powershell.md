---
title: Crie um Gateway de aplicação Azure & reescreva os cabeçalhos HTTP
description: Este artigo fornece informações sobre como criar um Gateway de aplicação Azure e reescrever cabeçalhos HTTP usando Azure PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/19/2019
ms.author: absha
ms.openlocfilehash: e18288dbc2a09c7e9dd5b0c0e96dfd04ec192596
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89595908"
---
# <a name="create-an-application-gateway-and-rewrite-http-headers"></a>Crie um gateway de aplicação e reescreva os cabeçalhos HTTP

Pode utilizar o Azure PowerShell para configurar [regras para reescrever os cabeçalhos de pedido e resposta HTTP](rewrite-http-headers.md) quando criar o novo portal de [aplicações auto-caling e redundante de zona SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

Neste artigo, vai aprender a:

* Criar uma rede virtual de escala automática
* Criar um IP público reservado
* Configurar a sua infraestrutura de gateway de aplicação
* Especifique a configuração da regra de reescrita do cabeçalho http
* Especificar o dimensionamento automático
* Criar o gateway de aplicação
* Testar o gateway de aplicação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo requer que você execute Azure PowerShell localmente. Deve ter a versão 1.0.0 do módulo Az ou posteriormente instalada. Corra `Import-Module Az` e depois para encontrar a `Get-Module Az` versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](https://docs.microsoft.com/powershell/azure/install-az-ps). Depois de verificar a versão do PowerShell, execute `Login-AzAccount` para criar uma ligação ao Azure.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos numa das localizações disponíveis.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual com uma sub-rede dedicada para um gateway de aplicações auto-caling. Atualmente, apenas um gateway de aplicação de dimensionamento automático pode ser implementado em cada sub-rede dedicada.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Criar um IP público reservado

Especifique o método de atribuição do PublicIPAddress como **Estático**. Um VIP de gateway de aplicação de dimensionamento automático só pode ser estático. Os IPs dinâmicos não são suportados. Apenas o SKU PublicIpAddress standard é suportado.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Obter os detalhes

Recupere os detalhes do grupo de recursos, sub-rede e IP num objeto local para criar os detalhes de configuração IP para o gateway de aplicação.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Configurar a infraestrutura

Configure o config IP, front-end IP config, back-end pool, DEFINIções HTTP, certificado, porta e ouvinte em formato idêntico ao portal de aplicação Standard existente. O novo SKU segue o mesmo modelo de objeto do SKU Standard.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$listener01 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp01

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>Especifique a configuração da regra de reescrita do cabeçalho HTTP

Configure os novos objetos necessários para reescrever os cabeçalhos http:

- **RequererConfiguration**: este objeto é utilizado para especificar os campos de cabeçalho de pedido que pretende reescrever e o novo valor a que os cabeçalhos originais precisam de ser reescritos.
- **RespostaConfiguration**: este objeto é utilizado para especificar os campos de cabeçalho de resposta que pretende reescrever e o novo valor a que os cabeçalhos originais precisam de ser reescritos.
- **ActionSet**: este objeto contém as configurações dos cabeçalhos de pedido e resposta especificados acima. 
- **ReescritaRule:** este objeto contém todos os *Conjuntos de ação especificados* acima. 
- **RewriteRuleSet**- este objeto contém todas as *regras de reescrita* e terá de ser anexado a uma regra de encaminhamento de pedidos - básico ou baseado em caminhos.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>Especificar a regra do encaminhamento

Crie uma regra de encaminhamento de pedidos. Uma vez criado, esta configuração de reescrita é anexada ao ouvinte de origem através da regra de encaminhamento. Ao utilizar uma regra de encaminhamento básico, a configuração de reescrita do cabeçalho está associada a um ouvinte de origem e é uma reescrita global do cabeçalho. Quando uma regra de encaminhamento baseada em caminhos é usada, a configuração de reescrita do cabeçalho é definida no mapa do caminho URL. Assim, só se aplica à área específica do caminho de um site. Abaixo, uma regra de encaminhamento básico é criada e o conjunto de regras de reescrita é anexado.

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>Especificar o dimensionamento automático

Agora pode especificar a configuração de escala automática para o gateway de aplicações. São suportados dois tipos de configuração de dimensionamento automático:

* **Modo de capacidade fixo**. Neste modo, o gateway de aplicação não dimensiona automaticamente e opera numa capacidade de Unidade de Escala fixa.

   ```azurepowershell
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Modo de dimensionamento automático**. Neste modo, o gateway de aplicação é dimensionado automaticamente com base no padrão de tráfego da aplicação.

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

Crie o gateway de aplicações e inclua zonas de redundância e a configuração de autoescala.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Utilize Get-AzPublicIPAddress para obter o endereço IP público do gateway de aplicações. Copie o endereço IP público ou o nome DNS e cole-o na barra de endereço do browser.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Limpar os recursos

Primeiro explore os recursos que foram criados com o gateway de aplicação. Depois, quando já não forem necessários, pode usar o `Remove-AzResourceGroup` comando para remover o grupo de recursos, o gateway de aplicação e todos os recursos relacionados.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Passos seguintes

- [Criar um gateway de aplicação com regras de encaminhamento com base no caminho de URL](./tutorial-url-route-powershell.md)
