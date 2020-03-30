---
title: Redirecionamento baseado em caminhos de URL usando PowerShell - Gateway de aplicação Azure
description: Saiba como criar um gateway de aplicação com o tráfego redirecionado com base no caminho do URL com o Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 03/19/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: c577859f6e8a44dd3573537aecadba638a5f6fa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059391"
---
# <a name="create-an-application-gateway-with-url-path-based-redirection-using-azure-powershell"></a>Criar um gateway de aplicação com o redirecionamento com base no caminho do URL com o Azure PowerShell

Pode utilizar o Azure PowerShell para configurar [regras de encaminhamento com base no URL](application-gateway-url-route-overview.md) quando cria um [gateway de aplicação](application-gateway-introduction.md). Neste artigo, cria piscinas de backend utilizando conjuntos de escala de [máquinas virtuais.](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) Em seguida, vai criar regras de encaminhamento de URL que asseguram que o tráfego da Web é redirecionado para o conjunto de back-end adequado.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Configurar a rede
> * Criar um gateway de aplicação
> * Adicionar serviços de escuta e regras de encaminhamento
> * Criar conjuntos de dimensionamento de máquinas virtuais para conjuntos de back-end

O exemplo seguinte mostra o tráfego do site proveniente das portas 8080 e 8081 e que está a ser direcionado para os mesmos conjuntos de back-end:

![Exemplo de encaminhamento de URL](./media/tutorial-url-redirect-powershell/scenario.png)

Se preferir, pode concluir este procedimento utilizando [o Azure CLI](tutorial-url-redirect-cli.md).

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este procedimento requer a versão 1.0.0 ou posterior do módulo PowerShell Azure. Para localizar a versão, execute `Get-Module -ListAvailable Az`. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos Azure utilizando o [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Criar recursos de rede

Crie as configurações da sub-rede para *myBackendSubnet* e *myAGSubnet* utilizando [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Crie a rede virtual chamada *myVNet* utilizando [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) com as configurações da sub-rede. E, finalmente, crie o endereço IP público chamado *myAGPublicIPAddress* usando [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Estes recursos são utilizados para fornecer conectividade de rede ao gateway de aplicação e aos respetivos recursos associados.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig

New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Nesta secção, pode criar recursos que suportam o gateway de aplicação e que, por fim, o criam. Os recursos que criar incluem:

- *Configurações de IP e porta de front-end* - associa a sub-rede que criou anteriormente ao gateway de aplicação e atribui uma porta a utilizar para aceder ao mesmo.
- *Conjunto predefinido * - todos os gateways de aplicação precisam de ter, pelo menos, um conjunto de servidores de back-end.
- *Serviço de escuta e regra predefinidos* - o serviço de escuta predefinido escuta o tráfego na porta atribuída e a regra predefinida envia o tráfego para o conjunto predefinido.

### <a name="create-the-ip-configurations-and-frontend-port"></a>Criar as configurações de IP e a porta de front-end

Associe *myAGSubnet* que criou anteriormente para o gateway da aplicação utilizando a [Configuração New-AzApplicationGatewayIP .](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) Atribuir *o myAGPublicIPAddress* ao gateway da aplicação utilizando [o New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig). E depois pode criar a porta HTTP utilizando a [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[0]

$pip = Get-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Name myAGPublicIPAddress

$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet

$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip

$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-default-pool-and-settings"></a>Criar o conjunto predefinido e as definições

Crie o pool de backend predefinido chamado *appGatewayBackendPool* para o gateway da aplicação utilizando [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Configure as definições para o pool backend utilizando [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Criar o serviço de escuta e a regra predefinidos

É necessário um serviço de escuta para permitir ao gateway de aplicação encaminhar o tráfego adequadamente para um conjunto de back-end. Neste artigo, cria-se vários ouvintes. O primeiro serviço de escuta básico espera o tráfego no URL de raiz. Os outros serviços de escuta esperam o tráfego em URLs específicos, como `http://52.168.55.24:8080/images/` ou `http://52.168.55.24:8081/video/`.

Crie um ouvinte chamado *defaultListener* utilizando [o New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) com a configuração frontal e a porta frontend que criou anteriormente. É necessária uma regra para o serviço de escuta saber qual o conjunto de back-end a utilizar para o tráfego de entrada. Crie uma regra básica chamada *regra 1* usando [new-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name defaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

Agora que criou os recursos de apoio necessários, especifique parâmetros para o portal da aplicação chamado *myAppGateway* usando [New-AzApplicationGatewaySku,](/powershell/module/az.network/new-azapplicationgatewaysku)e depois crie-o usando [new-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2

New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="add-backend-pools-and-ports"></a>Adicionar conjuntos de back-end e portas

Pode adicionar piscinas de backend ao seu portal de aplicações utilizando [Add-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/add-azapplicationgatewaybackendaddresspool). Neste exemplo, são criados *imagesBackendPool* e *videoBackendPool*. Adicione a porta frontal para as piscinas utilizando [add-AzApplicationGatewayFrontendPort](/powershell/module/az.network/add-azapplicationgatewayfrontendport). Em seguida, submete as alterações ao gateway da aplicação utilizando [o Set-AzApplicationGateway](/powershell/module/az.network/set-azapplicationgateway).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

Add-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport `
  -Port 8080

Add-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name rport `
  -Port 8081

Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="add-listeners-and-rules"></a>Adicionar serviços de escuta e regras

### <a name="add-listeners"></a>Adicionar serviços de escuta

Adicione os ouvintes nomeados *backendListener* e *redirecionadoSListener* que são necessários para direcionar o tráfego usando [Add-AzApplicationGatewayHttpListener](/powershell/module/az.network/add-azapplicationgatewayhttplistener).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPort = Get-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport

$redirectPort = Get-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name rport

$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -ApplicationGateway $appgw

Add-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $backendPort

Add-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name redirectedListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $redirectPort

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-the-default-url-path-map"></a>Adicionar o mapa de caminho de URL predefinido

Os mapas de caminho de URL asseguram que são encaminhados URLs específicos para conjuntos de back-end específicos. Pode criar os mapas de caminhos de URL nomeados *imagemPathRule* e *videoPathRule* utilizando [New-AzApplicationGatewayPathRuleConfig](/powershell/module/az.network/new-azapplicationgatewaypathruleconfig) e [Add-AzApplicationGatewayUrlPathMapConfig](/powershell/module/az.network/add-azapplicationgatewayurlpathmapconfig).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$poolSettings = Get-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $appgw `
  -Name myPoolSettings

$imagePool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

$defaultPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool

$imagePathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name imagePathRule `
  -Paths "/images/*" `
  -BackendAddressPool $imagePool `
  -BackendHttpSettings $poolSettings

$videoPathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name videoPathRule `
  -Paths "/video/*" `
  -BackendAddressPool $videoPool `
  -BackendHttpSettings $poolSettings

Add-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap `
  -PathRules $imagePathRule, $videoPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-redirection-configuration"></a>Adicionar configuração de redirecionamento

Pode configurar a reorientação para o ouvinte utilizando a [Configuração de direção Add-AzApplicationGatewayRedirect](/powershell/module/az.network/add-azapplicationgatewayredirectconfiguration).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendListener = Get-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener

$redirectConfig = Add-AzApplicationGatewayRedirectConfiguration `
  -ApplicationGateway $appgw `
  -Name redirectConfig `
  -RedirectType Found `
  -TargetListener $backendListener `
  -IncludePath $true `
  -IncludeQueryString $true

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-the-redirection-url-path-map"></a>Adicionar o mapa de caminho de URL de redirecionamento

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$poolSettings = Get-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $appgw `
  -Name myPoolSettings

$defaultPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool

$redirectConfig = Get-AzApplicationGatewayRedirectConfiguration `
  -ApplicationGateway $appgw `
  -Name redirectConfig

$redirectPathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name redirectPathRule `
  -Paths "/images/*" `
  -RedirectConfiguration $redirectConfig

Add-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name redirectpathmap `
  -PathRules $redirectPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-routing-rules"></a>Adicionar regras de encaminhamento

As regras de encaminhamento associam os maps de URL aos serviços de escuta que criou. Pode adicionar as regras chamadas *defaultRule* e *redirecionadaRule* utilizando [Add-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule).


```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendlistener = Get-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener

$redirectlistener = Get-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name redirectedListener

$urlPathMap = Get-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap

$redirectPathMap = Get-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name redirectpathmap

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name defaultRule `
  -RuleType PathBasedRouting `
  -HttpListener $backendlistener `
  -UrlPathMap $urlPathMap

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name redirectedRule `
  -RuleType PathBasedRouting `
  -HttpListener $redirectlistener `
  -UrlPathMap $redirectPathMap

Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-virtual-machine-scale-sets"></a>Criar conjuntos de dimensionamento de máquinas virtuais

Neste exemplo, vai criar três conjuntos de dimensionamento de máquinas virtuais que suportam os três conjuntos de back-end que criou. Os conjuntos de dimensionamento que criar são denominados *myvmss1*, *myvmss2* e *myvmss3*. Cada conjunto de dimensionamento contém duas instâncias de máquina virtual onde vai instalar o IIS. Pode atribuir o conjunto de dimensionamento ao conjunto de back-end quando configurar as definições de IP.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw

$imagesPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name imagesBackendPool `
  -ApplicationGateway $appgw

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name videoBackendPool `
  -ApplicationGateway $appgw

for ($i=1; $i -le 3; $i++)
{
  if ($i -eq 1)
  {
     $poolId = $backendPool.Id
  }
  if ($i -eq 2) 
  {
    $poolId = $imagesPool.Id
  }
  if ($i -eq 3)
  {
    $poolId = $videoPool.Id
  }

  $ipConfig = New-AzVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId

  $vmssConfig = New-AzVmssConfig `
    -Location eastus `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic

  Set-AzVmssStorageProfile $vmssConfig `
    -ImageReferencePublisher MicrosoftWindowsServer `
    -ImageReferenceOffer WindowsServer `
    -ImageReferenceSku 2016-Datacenter `
    -ImageReferenceVersion latest `
    -OsDiskCreateOption FromImage

  Set-AzVmssOsProfile $vmssConfig `
    -AdminUsername azureuser `
    -AdminPassword "Azure123456!" `
    -ComputerNamePrefix myvmss$i

  Add-AzVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig

  New-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>Instalar o IIS

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 3; $i++)
{
  $vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss$i

  Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

  Update-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Pode utilizar o [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) para obter o endereço IP público do gateway da aplicação. Copie o endereço IP público e cole-o na barra de endereço do browser. Tais `http://52.168.55.24`como, `http://52.168.55.24:8080/images/test.htm` `http://52.168.55.24:8080/video/test.htm`, `http://52.168.55.24:8081/images/test.htm`, ou .

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testar o URL base no gateway de aplicação](./media/tutorial-url-redirect-powershell/application-gateway-iistest.png)

Altere o URL para http://&lt;ip-address&gt;:8080/images/test.htm, substituindo o endereço IP de &lt;ip-address&gt;, e deverá ver algo semelhante ao seguinte exemplo:

![Testar o URL de imagens no gateway de aplicação](./media/tutorial-url-redirect-powershell/application-gateway-iistest-images.png)

Altere o URL para http://&lt;ip-address&gt;:8080/video/test.htm, substituindo o endereço IP de &lt;ip-address&gt;, e deverá ver algo semelhante ao seguinte exemplo:

![Testar o URL de vídeo no gateway de aplicação](./media/tutorial-url-redirect-powershell/application-gateway-iistest-video.png)

Agora, altere o URL para http://&lt;&gt;:8081/images/test.htm, substituindo o endereço IP de &lt;ip-address&gt;, e deverá ver o tráfego redirecionado novamente para o conjunto de back-end de imagens em http://&lt;ip-address&gt;:8080/images.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, remova o grupo de recursos, o portal de aplicação e todos os recursos relacionados utilizando [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```
## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o que pode fazer com o gateway de aplicação](application-gateway-introduction.md)
