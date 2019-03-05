---
title: Cabeçalhos HTTP de reescrita no Gateway de aplicação do Azure
description: Este artigo fornece informações sobre como criar um Gateway de aplicação do Azure e reescrever os cabeçalhos HTTP com o Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 4747d824dcf531ed883d476a0daad182ea081c39
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314454"
---
# <a name="tutorial-create-an-application-gateway-and-rewrite-http-headers"></a>Tutorial: Criar um gateway de aplicação e volte a escrever os cabeçalhos HTTP

Pode utilizar o Azure PowerShell para configurar [regras de reescrita de cabeçalhos de solicitação e resposta HTTP](rewrite-http-headers.md) quando cria o novo [dimensionamento automático e o SKU de gateway de aplicação com redundância de zona](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

> [!IMPORTANT] 
> O SKU do gateway de aplicação de dimensionamento automático e com redundância entre zonas está atualmente em pré-visualização pública. Esta pré-visualização é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Criar uma rede virtual do dimensionamento automático
> * Criar um IP público reservado
> * Configurar a sua infraestrutura de gateway de aplicação
> * Especifique a configuração de regra de reescrita de cabeçalho de http
> * Especificar o dimensionamento automático
> * Criar o gateway de aplicação
> * Testar o gateway de aplicação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial exige que execute o Azure PowerShell localmente. Tem de ter Az versão 1.0.0 do módulo ou posterior instalado. Execute `Import-Module Az` e, em seguida,`Get-Module Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](https://docs.microsoft.com/powershell/azure/install-az-ps). Depois de verificar a versão do PowerShell, execute `Login-AzAccount` para criar uma ligação ao Azure.

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

Crie uma rede virtual com uma sub-rede dedicada para um gateway de aplicação de dimensionamento automático. Atualmente, apenas um gateway de aplicação de dimensionamento automático pode ser implementado em cada sub-rede dedicada.

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

Obtenha os detalhes de IP, uma sub-rede e o grupo de recursos num objeto local para criar os detalhes de configuração de IP para o gateway de aplicação.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Configurar a infraestrutura

Configure o IP config, configuração IP Front-end, conjunto de back-end, HTTP definições, certificado, porta e serviço de escuta num formato idêntico para o gateway existente do aplicativo padrão. O novo SKU segue o mesmo modelo de objeto do SKU Standard.

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

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>Especifique a configuração de regra de reescrita de cabeçalho de HTTP

Configure os novos objetos necessários para reescreva os cabeçalhos de http:

- **RequestHeaderConfiguration**: este objecto é utilizado para especificar os campos de cabeçalho de solicitação que pretende reescrever e o novo valor que têm de ser reescrito para os cabeçalhos originais.
- **ResponseHeaderConfiguration**: este objecto é utilizado para especificar os campos de cabeçalho de resposta que pretende reescrever e o novo valor que têm de ser reescrito para os cabeçalhos originais.
- **ActionSet**: este objeto contém as configurações dos cabeçalhos de solicitação e resposta especificados acima. 
- **RewriteRule**: este objeto contém todos os *actionSets* especificado acima. 
- **RewriteRuleSet**-este objeto contém todos os *rewriteRules* e tem de ser vinculado a uma regra de encaminhamento para a solicitação como-básica ou baseado no caminho.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>Especificar a regra de encaminhamento

Crie uma regra de encaminhamento do pedido. Depois de criado, esta configuração de reescrita está ligada ao serviço de escuta de origem através da regra de encaminhamento. Quando utilizar uma regra básica de encaminhamento, a configuração de reescrita de cabeçalho está associada um serviço de escuta de origem e é uma reescrita de cabeçalho global. Quando é utilizada uma regra de encaminhamento baseado no caminho, a configuração de reescrita de cabeçalho é definida no mapa do caminho do URL. Por isso, ele só se aplica a área de caminho específico de um site. Abaixo, é criada uma regra básica de encaminhamento e o conjunto de regras de reescrita está ligado.

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>Especificar o dimensionamento automático

Agora pode especificar a configuração de dimensionamento automático para o gateway de aplicação. São suportados dois tipos de configuração de dimensionamento automático:

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

Criar o gateway de aplicação e incluem as zonas de redundância e a configuração de dimensionamento automático.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Utilize Get-AzPublicIPAddress para obter o endereço IP público do gateway de aplicação. Copie o endereço IP público ou o nome DNS e cole-o na barra de endereço do browser.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Limpar recursos

Primeiro, explore os recursos que foram criados com o gateway de aplicação. Em seguida, quando já não forem necessários, pode utilizar o `Remove-AzResourceGroup` comando para remover o grupo de recursos, o gateway de aplicação e todos os recursos relacionados.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar um gateway de aplicação com regras de encaminhamento com base no caminho de URL](./tutorial-url-route-powershell.md)
