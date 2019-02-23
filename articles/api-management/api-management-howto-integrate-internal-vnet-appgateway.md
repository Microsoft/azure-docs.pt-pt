---
title: Como utilizar a API Management do Azure na rede Virtual com o Gateway de aplicação | Documentos da Microsoft
description: Saiba como definir e configurar a gestão de API do Azure na rede Virtual interna com o Application Gateway (WAF) como front-end
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: sasolank
ms.openlocfilehash: a88ea248f1db90916c5dbcaefc24131eb976e2f6
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728347"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrar a gestão de API numa VNET interna com o Gateway de aplicação

## <a name="overview"> </a> Descrição geral

O serviço de gestão de API pode ser configurado numa rede Virtual no modo interno, o que faz com que seja acessível apenas a partir de dentro da rede Virtual. O Gateway de aplicação do Azure é um serviço PAAS, que fornece um balanceador de carga de camada 7. Ele atua como um serviço de proxy reverso e fornece entre a sua oferta de uma Firewall de aplicações Web (WAF).

A combinação de gestão de API aprovisionadas numa VNET interna com o front-end do Gateway de aplicação permite que os seguintes cenários:

* Utilize o mesmo recurso de gestão de API para consumo por clientes internos e externos consumidores.
* Utilize um único recurso de gestão de API e com um subconjunto de APIs definidas na gestão de API disponíveis para os consumidores externos.
* Proporcionam uma forma chave na mão para mudar o acesso à gestão de API da Internet pública e desativar.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para seguir os passos descritos neste artigo, tem de ter:

* Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certificates - pfx e cer para o nome de anfitrião da API e pfx para o nome de anfitrião do portal do programador.

## <a name="scenario"> </a> Cenário

Este artigo aborda como usar um único serviço de gestão de API para consumidores internos e externos e torná-lo para atuar como um único front-end para ambos no local e na cloud APIs. Também verá como expor apenas um subconjunto das APIs (no exemplo que eles são destacados em verde) para consumo externo com a funcionalidade de encaminhamento disponível no Gateway de aplicação.

No primeiro exemplo de configuração todas as suas APIs são geridas apenas a partir de dentro da sua rede Virtual. Os consumidores internos (destacado na cor de laranja) podem aceder a todas as suas APIs internos e externos. Tráfego nunca sai para Internet um elevado desempenho é entregue através de circuitos do Expressroute.

![rota de URL](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> Antes de começar

* Verifique se está a utilizar a versão mais recente do Azure PowerShell. Consulte as instruções de instalação em [instalar o Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>O que é necessário para criar uma integração entre a gestão de API e o Gateway de aplicação?

* **Agrupamento de servidores de back-end:** Este é o endereço IP virtual interno do serviço de gestão de API.
* **Definições de agrupamento de servidores de back-end:** Cada conjunto tem definições como a porta, protocolo e a afinidade com base no cookie. Estas definições são aplicadas a todos os servidores dentro do conjunto.
* **Porta de front-end:** Esta é a porta pública aberta no gateway de aplicação. Tráfego-lo é redirecionado para um dos servidores de back-end.
* **Serviço de escuta:** O serviço de escuta possui uma porta de front-end, um protocolo (Http ou Https; estes valores diferenciam maiúsculas de minúsculas) e o nome do certificado SSL (se configurar o SSL de descarga).
* **Regra:** A regra vincula um serviço de escuta a um agrupamento de servidores de back-end.
* **Sonda de estado de funcionamento personalizados:** Gateway de aplicação, por predefinição, utiliza sondas de baseada em endereço IP para descobrir quais os servidores no BackendAddressPool estão ativos. A gestão de API do serviço responde apenas às solicitações com o cabeçalho de anfitrião correto, pelo que as sondas de predefinição falharem. Uma sonda de estado de funcionamento personalizado deve ser definido para o ajudar a determinar que o serviço está ativo e deve encaminhar os pedidos de gateway de aplicação.
* **Certificados de domínio personalizado:** Para aceder a gestão de API a partir da internet, terá de criar um mapeamento CNAME do respetivo nome de anfitrião para o nome DNS de front-end do Gateway de aplicação. Isto garante que o cabeçalho de nome de anfitrião e o certificado enviado para o Gateway de aplicação é encaminhada para a gestão de API é uma APIM pode reconhecer como válido. Neste exemplo, utilizamos dois certificados - para o back-end e para o portal do programador.  

## <a name="overview-steps"> </a> Passos necessários para integrar a gestão de API e o Gateway de aplicação

1. Crie um grupo de recursos para o Resource Manager.
2. Crie uma rede Virtual, uma sub-rede e um IP público para o Gateway de aplicação. Crie outra sub-rede para a gestão de API.
3. Criar um serviço de gestão de API dentro da sub-rede VNET criada acima e certifique-se de que utilizar o modo interno.
4. Configure um nome de domínio personalizado no serviço de gestão de API.
5. Crie um objeto de configuração do Gateway de aplicação.
6. Crie um recurso de Gateway de aplicação.
7. Crie um CNAME do nome DNS público do Gateway de aplicação para o nome de anfitrião do proxy de gestão de API.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Expor o portal do programador externamente por meio do Gateway de aplicação

Neste guia, também expõe os **portal do programador** a públicos externos através do Gateway de aplicação. Requer passos adicionais para criar o serviço de escuta do portal do programador, pesquisa, definições e regras. Todos os detalhes são fornecidos nos respetivos passos.

> [!WARNING]
> Se utilizar o Azure AD ou autenticação de terceiros, ative [afinidade de sessão baseada em cookies](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity) recurso do Gateway de aplicação.

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Resource Manager

### <a name="step-1"></a>Passo 1

Iniciar sessão no Azure

```powershell
Login-AzAccount
```

Autenticar com as suas credenciais.

### <a name="step-2"></a>Passo 2

Selecione a subscrição pretendida.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>Passo 3

Crie um novo grupo de recursos (ignore este passo se estiver a utilizar um grupo de recursos existente).

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização, Isto é utilizado como a localização predefinida para recursos nesse grupo de recursos. Certifique-se de que todos os comandos para criar um gateway de aplicação utilizam o mesmo grupo de recursos.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede Virtual e uma sub-rede para o gateway de aplicação

O exemplo seguinte mostra como criar uma rede Virtual com o resource manager.

### <a name="step-1"></a>Passo 1

Atribua o endereço intervalo 10.0.0.0/24 à variável da sub-rede a utilizar para o Gateway de aplicação ao criar uma rede Virtual.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Passo 2

Atribua o endereço intervalo 10.0.1.0/24 à variável da sub-rede a utilizar para a gestão de API ao criar uma rede Virtual.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Passo 3

Criar uma rede Virtual denominada **appgwvnet** no grupo de recursos **apim-appGw-RG** para a região EUA oeste. Utilize o prefixo 10.0.0.0/16 com sub-redes 10.0.0.0/24 e 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Passo 4

Atribuir uma variável da sub-rede para os passos seguintes

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Criar um serviço de gestão de API dentro de uma VNET configurado no modo interno

O exemplo seguinte mostra como criar um serviço de gestão de API numa VNET configurada para apenas o acesso interno.

### <a name="step-1"></a>Passo 1

Crie um objeto de rede Virtual de gestão de API com a sub-rede $apimsubnetdata criada acima.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -Location $location -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Passo 2

Crie um serviço de gestão de API dentro da rede Virtual.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Depois do comando acima for concluída com êxito, consulte a [necessária configuração de DNS para aceder ao serviço de gestão de API de VNET interno](api-management-using-with-internal-vnet.md#apim-dns-configuration) para aceder ao mesmo. Este passo pode demorar mais do que meia hora.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Configurar um nome de domínio personalizado na gestão de API

### <a name="step-1"></a>Passo 1

Carregue os certificados com chaves privadas para os domínios. Neste exemplo, utilizamos `api.contoso.net` e `portal.contoso.net`.  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certUploadResult = Import-AzApiManagementHostnameCertificate -ResourceGroupName $resGroupName -Name $apimServiceName -HostnameType "Proxy" -PfxPath $gatewayCertPfxPath -PfxPassword $gatewayCertPfxPassword -PassThru
$certPortalUploadResult = Import-AzApiManagementHostnameCertificate -ResourceGroupName $resGroupName -Name $apimServiceName -HostnameType "Proxy" -PfxPath $portalCertPfxPath -PfxPassword $portalCertPfxPassword -PassThru
```

### <a name="step-2"></a>Passo 2

Depois dos certificados são carregados, crie nome de anfitrião objetos de configuração para o proxy de e para o portal.  

```powershell
$proxyHostnameConfig = New-AzApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname $gatewayHostname
$portalHostnameConfig = New-AzApiManagementHostnameConfiguration -CertificateThumbprint $certPortalUploadResult.Thumbprint -Hostname $portalHostname
$result = Set-AzApiManagementHostnames -Name $apimServiceName -ResourceGroupName $resGroupName –PortalHostnameConfiguration $portalHostnameConfig -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Criar um recurso IP público **publicIP01** no grupo de recursos.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

É atribuído um endereço IP ao gateway de aplicação quando o serviço é iniciado.

## <a name="create-application-gateway-configuration"></a>Criar a configuração do gateway de aplicação

Tem de configurar todos os itens de configuração antes de criar o gateway de aplicação. Com os seguintes passos, irá criar os itens de configuração necessários para um recurso do gateway de aplicação.

### <a name="step-1"></a>Passo 1

Crie uma configuração de IP do gateway de aplicação com o nome **gatewayIP01**. Ao iniciar, o Application Gateway escolhe um endereço IP na subrede configurada e encaminha o tráfego da rede para os endereços IP no conjunto de IPs de back-end. Note que cada instância terá um endereço IP.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Passo 2

Configure a porta IP de front-end para o ponto de final IP público. Esta porta é a porta que os utilizadores finais que se ligam a.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Passo 3

Configure o IP de front-end com o ponto final de IP público.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Passo 4

Configure os certificados para o Gateway de aplicação, que será utilizado para desencriptar e reencriptar o tráfego passa pelas.

```powershell
$certPwd = ConvertTo-SecureString $gatewayCertPfxPassword -AsPlainText -Force
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortalPwd = ConvertTo-SecureString $portalCertPfxPassword -AsPlainText -Force
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Passo 5

Crie os serviços de escuta HTTP para o Gateway de aplicação. Atribua os certificados de configuração, a porta e o ssl IP Front-end aos mesmos.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Passo 6

Criar sondas personalizadas para o serviço de gestão de API `ContosoApi` ponto final de domínio do proxy. O caminho `/status-0123456789abcdef` é um ponto final de estado de funcionamento predefinido alojado em todos os serviços de gestão de API. Definir `api.contoso.net` como um nome de anfitrião de sonda personalizada para protegê-lo com o certificado SSL.

> [!NOTE]
> O nome do anfitrião `contosoapi.azure-api.net` é o nome de anfitrião de proxy predefinida configurada quando um serviço com o nome `contosoapi` é criado no Azure público.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Passo 7

Carregue o certificado a ser utilizado nos recursos de agrupamento de back-end do habilitados para SSL. Este é o mesmo certificado que indicou no passo 4 acima.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Passo 8

Configure definições de back-end HTTP para o Gateway de aplicação. Isto inclui a definição de um limite de tempo limite para o pedido de back-end, após o qual estão canceladas. Este valor é diferente do que o limite de tempo de pesquisa.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Passo 9

Configurar um conjunto de endereços IP de back-end com o nome **apimbackend** com o IP virtual interno endereço do serviço de gestão de API criada acima.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Passo 10

Crie regras para o Gateway de aplicação utilizar o encaminhamento básica.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Alterar - RuleType e encaminhamento, para restringir o acesso a determinadas páginas do portal do programador.

### <a name="step-11"></a>Passo 11

Configure o número de instâncias e o tamanho para o Gateway de aplicação. Neste exemplo, estamos a utilizar o [SKU da WAF](../application-gateway/application-gateway-webapplicationfirewall-overview.md) para maior segurança do recurso de gestão de API.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Passo 12

Configure a WAF para estar no modo de "Prevenção".

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Criar Gateway de aplicação

Crie um Gateway de aplicação com todos os objetos de configuração dos passos anteriores.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>O nome de anfitrião de proxy de gestão de API para o nome DNS público do recurso de Gateway de aplicação de CNAME

Depois de o gateway ser criado, o passo seguinte consiste em configurar o front-end para a comunicação. Quando utilizar um IP público, o Gateway de aplicação requer um nome DNS dinamicamente atribuído, o que pode não ser fácil de usar.

Nome DNS do Gateway de aplicação deve ser utilizado para criar um registo CNAME que aponta o nome de anfitrião do proxy APIM (por exemplo, `api.contoso.net` nos exemplos acima) para este nome DNS. Para configurar o registo CNAME de IP de front-end, obter os detalhes do Gateway de aplicação e o respetivo nome IP/DNS associado com o elemento PublicIPAddress. Não é recomendada a utilização de registos, uma vez que o VIP pode ser alterado no reinício do gateway.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"> </a> Resumo
Gestão de API do Azure configurado numa VNET fornece uma interface de gateway único para todas as APIs de configurados, quer estejam alojado no local ou na cloud. Integrar o Gateway de aplicação com a gestão de API fornece a flexibilidade de forma seletiva ativar APIs específicas para ser acessível na Internet, bem como fornecendo uma Firewall de aplicações Web como um front-end para a sua instância de gestão de API.

##<a name="next-steps"> </a> Próximas etapas
* Saiba mais sobre o Gateway de aplicação do Azure
  * [Descrição geral do Gateway de aplicação](../application-gateway/application-gateway-introduction.md)
  * [Firewall de aplicações da Web de Gateway de aplicação](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Gateway de aplicação com encaminhamento baseado no caminho](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Saiba mais sobre a gestão de API e VNETs
  * [Utilizar a gestão de API disponíveis apenas dentro da VNET](api-management-using-with-internal-vnet.md)
  * [Utilizar a gestão de API na VNET](api-management-using-with-vnet.md)
