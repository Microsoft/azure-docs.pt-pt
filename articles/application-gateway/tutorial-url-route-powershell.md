---
title: Encaminhar o tráfego Web com base no URL - Azure PowerShell
description: Saiba como utilizar o Azure PowerShell para encaminhar o tráfego Web com base no URL para conjuntos dimensionáveis específicos de servidores.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/31/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: cc3498c6d8f385bcf63aa7860edd12f9bf343fb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552597"
---
# <a name="route-web-traffic-based-on-the-url-using-azure-powershell"></a>Encaminhar o tráfego Web com base no URL com o Azure PowerShell

Pode utilizar o Azure PowerShell para configurar o encaminhamento de tráfego Web para conjuntos dimensionáveis específicos de servidores com base no URL que é utilizado para aceder à sua aplicação. Neste artigo, cria um Gateway de [aplicação Azure](./overview.md) com três piscinas de backend utilizando [conjuntos de balança de máquina virtual.](../virtual-machine-scale-sets/overview.md) Cada um dos conjuntos de back-end tem um objetivo específico, como dados comuns, imagens e vídeos.  Encaminhar o tráfego para conjuntos separados garante que os seus clientes obtêm as informações de que precisam quando querem.

Para permitir o encaminhamento do tráfego, tem de [criar regras de encaminhamento](./url-route-overview.md) que são atribuídas a serviços de escuta que escutam em portas específicas, de modo a garantir que o tráfego Web chega aos servidores adequados nos conjuntos.

Neste artigo, vai aprender a:

* Configurar a rede
* Criar serviços de escuta, o mapa de caminho do URL e regras
* Criar conjuntos de back-end dimensionáveis

![Exemplo de encaminhamento de URL](./media/tutorial-url-route-powershell/scenario.png)

Se preferir, pode concluir este procedimento utilizando [o Azure CLI](tutorial-url-route-cli.md) ou o [portal Azure](create-url-route-portal.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 1.0.0 ou mais tarde do módulo Azure PowerShell. Para localizar a versão, execute `Get-Module -ListAvailable Az`. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar o PowerShell localmente, também precisa de correr `Login-AzAccount` para criar uma ligação com o Azure.

Devido ao tempo necessário para criar recursos, pode levar até 90 minutos para concluir este procedimento.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos que contenha todos os recursos para a sua aplicação. 

Criar um grupo de recursos Azure utilizando [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Criar recursos de rede

Quer já tenha uma rede virtual ou crie uma nova, tem de confirmar que a mesma contém uma sub-rede que só é utilizada para os gateways de aplicação. Neste artigo, cria-se uma sub-rede para o gateway de aplicação e uma sub-rede para os conjuntos de escala. Também vai criar um endereço IP público para permitir o acesso aos recursos no gateway de aplicação.

Crie as configurações da sub-rede *myAGSubnet* e *myBackendSubnet* utilizando [a New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Crie a rede virtual chamada *myVNet* utilizando [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) com as configurações da sub-rede. E, finalmente, crie o endereço IP público chamado *myAGPublicIPAddress* usando [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Estes recursos são utilizados para fornecer conectividade de rede ao gateway de aplicação e aos respetivos recursos associados.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Nesta secção, pode criar recursos que suportam o gateway de aplicação e que, por fim, o criam. Os recursos que cria incluem:

- *Configurações IP e porta frontal* - Associa a sub-rede que criou anteriormente ao gateway de aplicações e atribui uma porta para usar para aceder a ela.
- *Conjunto predefinido* - todos os gateways de aplicação precisam de ter, pelo menos, um conjunto de servidores de back-end.
- *Serviço de escuta e regra predefinidos* - o serviço de escuta predefinido escuta o tráfego na porta atribuída e a regra predefinida envia o tráfego para o conjunto predefinido.

### <a name="create-the-ip-configurations-and-frontend-port"></a>Criar as configurações de IP e a porta de front-end

Associe *o myAGSubnet* que criou anteriormente para o gateway de aplicações utilizando [a New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Atribua *myAGPublicIPAddress* ao gateway de aplicações utilizando [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig).

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

Crie o pool de backend padrão chamado *appGatewayBackendPool* para o gateway de aplicações usando [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Configure as definições para o pool backend utilizando [New-AzApplicationGatewayBackendHttpSetting](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Criar o serviço de escuta e a regra predefinidos

É necessário um serviço de escuta para permitir ao gateway de aplicação encaminhar o tráfego adequadamente para o conjunto de back-end. Neste artigo, cria-se dois ouvintes. O primeiro serviço de escuta básico que vai criar escuta o tráfego no URL de raiz. O segundo serviço de escuta que vai criar escuta o tráfego em URLs específicos.

Crie o ouvinte predefinido chamado *myDefaultListener* utilizando [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) com a configuração frontal e porta frontal que criou anteriormente. 

É necessária uma regra para o serviço de escuta saber qual o conjunto de back-end a utilizar para o tráfego de entrada. Crie uma regra básica chamada *regra1* utilizando [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myDefaultListener `
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

Agora que criou os recursos de apoio necessários, especifique os parâmetros para a porta de aplicação chamada *myAppGateway* usando [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku), e, em seguida, criá-lo usando [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2

$appgw = New-AzApplicationGateway `
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

Pode levar até 30 minutos para criar o gateway de aplicação. Aguarde até que a colocação termine com sucesso antes de passar para a secção seguinte. 

Neste momento, você tem um gateway de aplicação que ouve o tráfego na porta 80 e envia esse tráfego para uma piscina de servidor padrão.

### <a name="add-image-and-video-backend-pools-and-port"></a>Adicionar conjuntos e portas de back-end de imagens e vídeos

Adicione piscinas de backend chamadas *BackendPool* e *videoBackendPool* à sua porta [de aplicação Add-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/add-azapplicationgatewaybackendaddresspool). Adicione a porta frontal para as piscinas utilizando [Add-AzApplicationGatewayFrontendPort](/powershell/module/az.network/add-azapplicationgatewayfrontendport). Envie as alterações ao gateway de aplicações utilizando [o Set-AzApplicationGateway](/powershell/module/az.network/set-azapplicationgateway).

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

Set-AzApplicationGateway -ApplicationGateway $appgw
```

A atualização do gateway de aplicação também pode demorar até 20 minutos a ser concluída.

### <a name="add-backend-listener"></a>Adicionar o serviço de escuta de back-end

Adicione o ouvinte de backend nomeado *backendListener* que é necessário para encaminhar o tráfego usando [Add-AzApplicationGatewayHttpListener](/powershell/module/az.network/add-azapplicationgatewayhttplistener).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPort = Get-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport

$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -ApplicationGateway $appgw

Add-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $backendPort

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-url-path-map"></a>Adicionar o mapa de caminho do URL

Os mapas de caminho dos URLs asseguram que os URLs enviados para a sua aplicação são encaminhados para conjuntos de back-end específicos. Crie mapas de caminhos URL nomeados *imagePathRule* e *videoPathRule* usando [New-AzApplicationGatewayPathRuleConfig](/powershell/module/az.network/new-azapplicationgatewaypathruleconfig) e [Add-AzApplicationGatewayUrlPathMapConfig](/powershell/module/az.network/add-azapplicationgatewayurlpathmapconfig).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$poolSettings = Get-AzApplicationGatewayBackendHttpSetting `
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

### <a name="add-routing-rule"></a>Adicionar a regra de encaminhamento

A regra de encaminhamento associa o mapa do URL ao serviço de escuta que criou. Adicione a regra nomeada *regra 2* utilizando [Add-AzApplicationGatewayRequestRoutrule](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendlistener = Get-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener

$urlPathMap = Get-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name rule2 `
  -RuleType PathBasedRouting `
  -HttpListener $backendlistener `
  -UrlPathMap $urlPathMap

Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-virtual-machine-scale-sets"></a>Criar conjuntos de dimensionamento de máquinas virtuais

Neste exemplo, vai criar três conjuntos de dimensionamento de máquinas virtuais que suportam os três conjuntos de back-end que criou. Os conjuntos de dimensionamento que criar são denominados *myvmss1*, *myvmss2* e *myvmss3*. Pode atribuir o conjunto de dimensionamento ao conjunto de back-end quando configurar as definições de IP.

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
    -SkuName Standard_DS2_v2 `
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

Cada conjunto de dimensionamento contém duas instâncias de máquina virtual onde vai instalar o IIS.  É criada uma página de amostra para testar se o gateway de aplicação está a funcionar.

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

Utilize [o Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) para obter o endereço IP público do gateway de aplicações. Copie o endereço IP público e cole-o na barra de endereço do browser. Como, `http://52.168.55.24` `http://52.168.55.24:8080/images/test.htm` `http://52.168.55.24:8080/video/test.htm` ou.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testar o URL base no gateway de aplicação](./media/tutorial-url-route-powershell/application-gateway-iistest.png)

Altere o URL para http:// &lt; endereço ip &gt; :8080/images/test.htm, substituindo o seu endereço IP por &lt; ip-endereço &gt; , e deverá ver algo como o seguinte exemplo:

![Testar o URL de imagens no gateway de aplicação](./media/tutorial-url-route-powershell/application-gateway-iistest-images.png)

Altere o URL para http:// &lt; endereço ip &gt; :8080/vídeo/test.htm, substituindo o seu endereço IP por &lt; endereço IP , e deverá ver algo como o seguinte &gt; exemplo:

![Testar o URL de vídeo no gateway de aplicação](./media/tutorial-url-route-powershell/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, remova o grupo de recursos, o gateway de aplicação e todos os recursos relacionados utilizando [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Passos seguintes

[Redirecionar o tráfego Web com base no URL](./tutorial-url-redirect-powershell.md)