---
title: Criar um gateway de Aplicativo Azure & reescrever cabeçalhos HTTP
description: Este artigo fornece informações sobre como criar um gateway de Aplicativo Azure e reescrever cabeçalhos HTTP usando Azure PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/19/2019
ms.author: absha
ms.openlocfilehash: 2663c049245a7025b5948a64fc5008bb9e7dee90
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173723"
---
# <a name="create-an-application-gateway-and-rewrite-http-headers"></a>Criar um gateway de aplicativo e reescrever cabeçalhos HTTP

Você pode usar Azure PowerShell para configurar [regras para reescrever cabeçalhos de solicitação e resposta http](rewrite-http-headers.md) ao criar a nova [SKU de autoescala e de gateway de aplicativo com redundância de zona](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Criar uma rede virtual de dimensionamento automático
> * Criar um IP público reservado
> * Configurar a infraestrutura do gateway de aplicativo
> * Especificar a configuração da regra de reescrita de cabeçalho http
> * Especificar o dimensionamento automático
> * Criar o gateway de aplicação
> * Testar o gateway de aplicação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo requer que você execute Azure PowerShell localmente. Você deve ter o módulo AZ versão 1.0.0 ou posterior instalado. Execute `Import-Module Az` e`Get-Module Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](https://docs.microsoft.com/powershell/azure/install-az-ps). Depois de verificar a versão do PowerShell, execute `Login-AzAccount` para criar uma ligação ao Azure.

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

Crie uma rede virtual com uma sub-rede dedicada para um gateway de aplicativo de dimensionamento automático. Atualmente, apenas um gateway de aplicação de dimensionamento automático pode ser implementado em cada sub-rede dedicada.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Criar um IP público reservado

Especifique o método de alocação de PublicIPAddress como **estático**. Um VIP de gateway de aplicação de dimensionamento automático só pode ser estático. Os IPs dinâmicos não são suportados. Apenas o SKU PublicIpAddress standard é suportado.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Obter os detalhes

Recupere detalhes do grupo de recursos, da sub-rede e do IP em um objeto local para criar os detalhes de configuração de IP para o gateway de aplicativo.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Configurar a infraestrutura

Configure a configuração de IP, a configuração de IP de front-end, o pool de back-end, as configurações de HTTP, o certificado, a porta e o ouvinte em um formato idêntico para o gateway de aplicativo padrão existente. O novo SKU segue o mesmo modelo de objeto do SKU Standard.

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

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>Especificar a configuração da regra de reescrita de cabeçalho HTTP

Configure os novos objetos necessários para reescrever os cabeçalhos http:

- **RequestHeaderConfiguration**: esse objeto é usado para especificar os campos de cabeçalho da solicitação que você pretende reescrever e o novo valor para o qual os cabeçalhos originais precisam ser regravados.
- **ResponseHeaderConfiguration**: esse objeto é usado para especificar os campos de cabeçalho de resposta que você pretende reescrever e o novo valor para o qual os cabeçalhos originais precisam ser regravados.
- **Actionset**: este objeto contém as configurações dos cabeçalhos de solicitação e resposta especificados acima. 
- **RewriteRule**: este objeto contém todos os *actionSets* especificados acima. 
- **RewriteRuleSet**-este objeto contém todas as *rewriteRules* e precisará ser anexado a uma regra de roteamento de solicitação-básica ou baseada em caminho.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>Especificar a regra de roteamento

Crie uma regra de roteamento de solicitação. Depois de criada, essa configuração de regravação é anexada ao ouvinte de origem por meio da regra de roteamento. Ao usar uma regra de roteamento básica, a configuração de reescrita de cabeçalho é associada a um ouvinte de origem e é uma reescrita de cabeçalho global. Quando uma regra de roteamento baseada em caminho é usada, a configuração de reescrita de cabeçalho é definida no mapa de caminho de URL. Portanto, ele só se aplica à área de caminho específica de um site. Abaixo, uma regra de roteamento básica é criada e o conjunto de regras de regravação é anexado.

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>Especificar o dimensionamento automático

Agora você pode especificar a configuração de dimensionamento automático para o gateway de aplicativo. São suportados dois tipos de configuração de dimensionamento automático:

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

Crie o gateway de aplicativo e inclua zonas de redundância e a configuração de dimensionamento automático.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Use Get-AzPublicIPAddress para obter o endereço IP público do gateway de aplicativo. Copie o endereço IP público ou o nome DNS e cole-o na barra de endereço do browser.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Limpar recursos

Primeiro, explore os recursos que foram criados com o gateway de aplicativo. Em seguida, quando eles não forem mais necessários, você poderá usar o comando `Remove-AzResourceGroup` para remover o grupo de recursos, o gateway de aplicativo e todos os recursos relacionados.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Passos seguintes

- [Criar um gateway de aplicação com regras de encaminhamento com base no caminho de URL](./tutorial-url-route-powershell.md)
