---
title: Como utilizar a Gestão da API em Rede Virtual com Gateway de Aplicações
titleSuffix: Azure API Management
description: Saiba como configurar e configurar a gestão da API Azure em Rede Virtual Interna com Gateway de Aplicação (WAF) como FrontEnd
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: sasolank
ms.openlocfilehash: 3db1c8bfc3a11151342589af0873d88e3d90c6a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91825622"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrar a Gestão de API numa VNET interna com o Gateway de Aplicação

## <a name="overview"></a><a name="overview"></a> Visão geral

O serviço de Gestão API pode ser configurado numa Rede Virtual em modo interno, o que o torna acessível apenas a partir da Rede Virtual. Azure Application Gateway é um serviço PAAS, que fornece um equilibrador de carga Camada-7. Funciona como um serviço de procuração inversa e fornece entre as suas ofertas uma Firewall de Aplicação Web (WAF).

Combinar a Gestão da API a provisionada num VNET interno com o frontend Do Gateway de Aplicação permite os seguintes cenários:

* Utilize o mesmo recurso de Gestão de API para consumo por parte dos consumidores internos e dos consumidores externos.
* Utilize um único recurso de Gestão de API e tenha um subconjunto de APIs definido na API Management disponível para consumidores externos.
* Fornecer uma forma chave-na-chave de mudar o acesso à API Management a partir da Internet pública ligado e desligado.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para seguir os passos descritos neste artigo, deve ter:

* Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certificados - pfx e cer para o nome de anfitrião da API e pfx para o nome de anfitrião do portal do desenvolvedor.

## <a name="scenario"></a><a name="scenario"></a> Cenário

Este artigo abrange a utilização de um único serviço de Gestão de API para consumidores internos e externos e faz com que este atue como um único frontend tanto nas instalações como nas APIs em nuvem. Também verá como expor apenas um subconjunto das suas APIs (no exemplo são realçados em verde) para consumo externo utilizando a funcionalidade de encaminhamento disponível no Gateway de Aplicações.

No primeiro exemplo de configuração, todas as suas APIs são geridas apenas a partir da sua Rede Virtual. Os consumidores internos (destacados a laranja) podem aceder a todas as suas APIs internas e externas. O trânsito nunca vai para a internet. A conectividade de alto desempenho é entregue através dos circuitos Express Route.

![rota url](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a><a name="before-you-begin"></a> Antes de começar

* Verifique se está a utilizar a versão mais recente do Azure PowerShell. Consulte as instruções de instalação no [Install Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>O que é necessário para criar uma integração entre a API Management e a Application Gateway?

* **Piscina de servidor de back-end:** Este é o endereço IP virtual interno do serviço de Gestão API.
* **Definições de piscina de servidor de back-end:** Cada piscina tem configurações como porta, protocolo e afinidade baseada em cookies. Estas definições são aplicadas a todos os servidores dentro da piscina.
* **Porta frontal:** Este é o porto público que é aberto no portão de aplicação. O tráfego que o atinge é redirecionado para um dos servidores de back-end.
* **Ouvinte:** O ouvinte tem uma porta frontal, um protocolo (Http ou Https, estes valores são sensíveis a casos) e o nome do certificado TLS/SSL (se configurar o descarregamento TLS).
* **Regra:** A regra liga um ouvinte a uma piscina de servidor de back-end.
* **Sonda de Saúde Personalizada:** O Gateway de Aplicação, por padrão, utiliza sondas baseadas em endereços IP para descobrir quais os servidores do BackendAddressPool que estão ativos. O serviço de Gestão API apenas responde a pedidos com o cabeçalho de anfitrião correto, daí que as sondas predefinidas falhem. Uma sonda de saúde personalizada precisa de ser definida para ajudar a aplicação gateway determinar que o serviço está vivo e deve encaminhar pedidos.
* **Certificados de domínio personalizados:** Para aceder à Gestão da API a partir da internet, é necessário criar um mapeamento CNAME do seu nome de anfitrião para o nome DNS frontal do Gateway de Aplicação. Isto garante que o cabeçalho e certificado de nome anfitrião enviados para o Application Gateway que é reencaminhado para a API Management é um apim que pode reconhecer como válido. Neste exemplo, usaremos dois certificados - para o backend e para o portal de desenvolvedores.  

## <a name="steps-required-for-integrating-api-management-and-application-gateway"></a><a name="overview-steps"></a> Medidas necessárias para integrar a API Management and Application Gateway

1. Crie um grupo de recursos para o Resource Manager.
2. Crie uma Rede Virtual, sub-rede e IP público para o Gateway de Aplicações. Criar outra sub-rede para a Gestão da API.
3. Crie um serviço de Gestão API dentro da sub-rede VNET criada acima e certifique-se de que utiliza o modo Interno.
4. Crie um nome de domínio personalizado no serviço de Gestão API.
5. Crie um objeto de configuração do Gateway de Aplicação.
6. Crie um recurso Application Gateway.
7. Crie um CNAME a partir do nome PÚBLICO DNS do Gateway de Aplicação para o nome de anfitrião de procuração da API Management.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Expondo o portal do desenvolvedor externamente através do Gateway de Aplicação

Neste guia também exporemos o **portal do desenvolvedor** a públicos externos através do Gateway de Aplicações. Requer medidas adicionais para criar o ouvinte, sonda, configurações e regras do portal do desenvolvedor. Todos os detalhes são fornecidos nos respetivos passos.

> [!WARNING]
> Se utilizar a Azure AD ou a autenticação de terceiros, por favor, ative a funcionalidade [de afinidade baseada em cookies](../application-gateway/features.md#session-affinity) no Gateway de Aplicações.

> [!WARNING]
> Para evitar que o Gateway WAF quebrou o download da especificação OpenAPI no portal do desenvolvedor, é necessário desativar a regra da firewall `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"` .
> 
> As regras do Gateway WAF, que podem quebrar a funcionalidade do portal, incluem:
> 
> - `920300``920330`, `931130` , , , , `942100` , , , para o modo `942110` `942180` `942200` `942260` `942340` `942370` administrativo
> - `942200`, `942260` `942370` , , para o `942430` portal `942440` publicado

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Resource Manager

### <a name="step-1"></a>Passo 1

Iniciar sessão no Azure

```powershell
Connect-AzAccount
```

Autenticar com as suas credenciais.

### <a name="step-2"></a>Passo 2

Selecione a subscrição desejada.

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

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização. Isto é utilizado como a localização predefinida para recursos nesse grupo de recursos. Certifique-se de que todos os comandos para criar um gateway de aplicação utilizem o mesmo grupo de recursos.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma Rede Virtual e uma sub-rede para o gateway de aplicações

O exemplo a seguir mostra como criar uma Rede Virtual utilizando o Gestor de Recursos.

### <a name="step-1"></a>Passo 1

Atribua o intervalo de endereços 10.0.0.0/24 à variável sub-rede a utilizar para o Gateway de Aplicações enquanto cria uma Rede Virtual.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Passo 2

Atribua o intervalo de endereços 10.0.1.0/24 à variável sub-rede a utilizar para a Gestão da API enquanto cria uma Rede Virtual.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Passo 3

Crie uma Rede Virtual chamada **appgwvnet** no grupo de recursos **apim-appGw-RG** para a região dos EUA. Utilize o prefixo 10.0.0.0/16 com as sub-redes 10.0.0.0.0/24 e 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Passo 4

Atribua uma variável de sub-rede para os próximos passos

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Criar um serviço de Gestão API dentro de um VNET configurado em modo interno

O exemplo a seguir mostra como criar um serviço de Gestão API num VNET configurado apenas para acesso interno.

### <a name="step-1"></a>Passo 1

Crie um objeto de Rede Virtual de Gestão API utilizando a sub-rede $apimsubnetdata criada acima.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Passo 2

Criar um serviço de Gestão API dentro da Rede Virtual.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Após o comando acima, consulte a [Configuração DNS necessária para aceder ao serviço interno de Gestão de API da VNET](api-management-using-with-internal-vnet.md#apim-dns-configuration) para aceder ao mesmo. Este passo pode demorar mais de meia hora.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Configurar um nome de domínio personalizado na Gestão da API

> [!IMPORTANT]
> O [novo portal de desenvolvimento](api-management-howto-developer-portal.md) também requer a conectividade com o ponto final de gestão da API Management, além dos passos abaixo.

### <a name="step-1"></a>Passo 1

Inicialize as seguintes variáveis com os detalhes dos certificados com chaves privadas para os domínios. Neste exemplo, vamos usar `api.contoso.net` e `portal.contoso.net` .  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>Passo 2

Crie e desemote os objetos de configuração do nome do anfitrião para o proxy e para o portal.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Para configurar a conectividade do portal do desenvolvedor legado, é necessário substituir `-HostnameType DeveloperPortal` `-HostnameType Portal` por .

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Criar um recurso IP **públicoIP01** no grupo de recursos.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

É atribuído um endereço IP ao gateway de aplicação quando o serviço é iniciado.

## <a name="create-application-gateway-configuration"></a>Criar configuração de gateway de aplicações

Tem de configurar todos os itens de configuração antes de criar o gateway de aplicação. Com os seguintes passos, irá criar os itens de configuração necessários para um recurso do gateway de aplicação.

### <a name="step-1"></a>Passo 1

Crie uma configuração IP de gateway de aplicação chamada **gatewayIP01**. Ao iniciar, o Application Gateway escolhe um endereço IP na subrede configurada e encaminha o tráfego da rede para os endereços IP no conjunto de IPs de back-end. Note que cada instância terá um endereço IP.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Passo 2

Configure a porta IP frontal para o ponto final IP público. Esta porta é a porta à qual os utilizadores finais se ligam.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Passo 3

Configure o IP de front-end com o ponto final de IP público.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Passo 4

Configure os certificados para o Gateway de Aplicação, que serão usados para desencriptar e reencrimar o tráfego que passa.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Passo 5

Crie os ouvintes HTTP para o Gateway de Aplicações. Atribua-lhes os certificados IP front-end, porta e TLS/SSL.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Passo 6

Crie sondas personalizadas para o ponto final de domínio de procuração do serviço API `ContosoApi` Management. O caminho `/status-0123456789abcdef` é um ponto final de saúde padrão hospedado em todos os serviços de Gestão da API. Desatado `api.contoso.net` como um nome de anfitrião de sonda personalizado para o fixar com o certificado TLS/SSL.

> [!NOTE]
> O nome de `contosoapi.azure-api.net` anfitrião é o nome de anfitrião padrão configurado quando um serviço nomeado `contosoapi` é criado em Azure público.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/internal-status-0123456789abcdef" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Passo 7

Faça o upload do certificado a utilizar nos recursos de backend de backend ativados pelo TLS. Este é o mesmo certificado que forneceu no passo 4 acima.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Passo 8

Configurar as definições de backend HTTP para o Gateway de aplicações. Isto inclui a definição de um limite de tempo para pedido de backend, após o qual são cancelados. Este valor é diferente do tempo de tempo da sonda.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Passo 9

Configure um conjunto de endereços IP back-end chamado **apimbackend**  com o endereço IP virtual interno do serviço de Gestão API criado acima.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Passo 10

Crie regras para o Gateway de Aplicação utilizar o encaminhamento básico.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Altere o -RuleType e o encaminhamento, para restringir o acesso a determinadas páginas do portal do desenvolvedor.

### <a name="step-11"></a>Passo 11

Configure o número de casos e tamanho para o Gateway de aplicação. Neste exemplo, estamos a usar o [WAF SKU](../web-application-firewall/ag/ag-overview.md) para aumentar a segurança do recurso de Gestão da API.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Passo 12

Configure a WAF para estar no modo "Prevenção".

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Criar Gateway de Aplicações

Crie um Gateway de aplicação com todos os objetos de configuração dos passos anteriores.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME, o nome de acolhimento de procuradores da API Management para o nome PÚBLICO DNS do recurso Application Gateway

Depois de o gateway ser criado, o passo seguinte consiste em configurar o front-end para a comunicação. Ao utilizar um IP público, o Application Gateway requer um nome DNS atribuído dinamicamente, o que pode não ser fácil de usar.

O nome DNS do Gateway de aplicação deve ser usado para criar um registo CNAME que aponta o nome de anfitrião de procuração APIM (por `api.contoso.net` exemplo, nos exemplos acima) para este nome DNS. Para configurar o registo IP CNAME de frontend, recupere os detalhes do Gateway de aplicação e do nome IP/DNS associado utilizando o elemento PublicIPAddress. A utilização de registos A não é recomendada, uma vez que o VIP pode mudar no reinício do gateway.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a><a name="summary"></a> Resumo
A Azure API Management configurada num VNET fornece uma interface de gateway única para todas as APIs configuradas, sejam elas hospedadas nas instalações ou na nuvem. A integração do Gateway de Aplicações com a API Management proporciona a flexibilidade de permitir seletivamente que apis particulares sejam acessíveis na Internet, bem como fornecer uma Firewall de Aplicação Web como um frontend para a sua instância de Gestão de API.

## <a name="next-steps"></a><a name="next-steps"></a> Próximos passos
* Saiba mais sobre o Azure Application Gateway
  * [Visão geral do Gateway de Aplicação](../application-gateway/overview.md)
  * [Application Gateway Web Application Firewall](../web-application-firewall/ag/ag-overview.md)
  * [Gateway de aplicação usando encaminhamento baseado em caminho](../application-gateway/tutorial-url-route-powershell.md)
* Saiba mais sobre API Management e VNETs
  * [Utilização da Gestão da API disponível apenas no VNET](api-management-using-with-internal-vnet.md)
  * [Utilização da Gestão da API em VNET](api-management-using-with-vnet.md)
