---
title: Configure TLS de ponta a ponta com gateway de aplicação Azure
description: Este artigo descreve como configurar TLS de ponta a ponta com Azure Application Gateway usando PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 47891dfa7fc0c9b30ccdbf2ed7710125eb36e4a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397812"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-powershell"></a>Configurar o TLS ponto a ponto com o Gateway de Aplicação através do PowerShell

## <a name="overview"></a>Descrição Geral

O Azure Application Gateway suporta encriptação de tráfego de ponta a ponta. O Gateway de Aplicação termina a ligação TLS/SSL no gateway de aplicações. O gateway aplica então as regras de encaminhamento para o tráfego, reencripta o pacote e encaminha o pacote para o servidor back-end apropriado com base nas regras de encaminhamento definidas. Qualquer resposta do servidor Web atravessa o mesmo processo para o utilizador final.

O Application Gateway suporta a definição de opções TLS personalizadas. Também suporta a desativação das seguintes versões protocolares: **TLSv1.0**, **TLSv1.1**, e **TLSv1.2**, bem como definir quais as suites cifras a utilizar e a ordem de preferência. Para saber mais sobre opções de TLS configuráveis, consulte a visão geral da política do [TLS](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> SSL 2.0 e SSL 3.0 são desativados por defeito e não podem ser ativados. São considerados inseguros e não podem ser utilizados com o Gateway de Aplicação.

![imagem de cenário][scenario]

## <a name="scenario"></a>Scenario

Neste cenário, aprende-se a criar um gateway de aplicações utilizando OLS de ponta a ponta com o PowerShell.

Este cenário:

* Criar um grupo de recursos chamado **appgw-rg**.
* Criar uma rede virtual chamada **appgwvnet** com um espaço de endereço de **10.0.0.0/16**.
* Crie duas sub-redes chamadas **appgwsubnet** e **appsubnet**.
* Crie uma pequena porta de aplicação suportando encriptação TLS de ponta a ponta que limita as versões do protocolo TLS e as suites de cifra.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para configurar o TLS de ponta a ponta com um gateway de aplicação, é necessário um certificado para o gateway e são necessários certificados para os servidores de back-end. O certificado de gateway é utilizado para obter uma chave simétrica de acordo com a especificação do protocolo TLS. A chave simétrica é então usada encriptada e desencripta o tráfego enviado para o portal. O certificado gateway tem de estar no formato Personal Information Exchange (PFX). Este formato de ficheiro permite-lhe exportar a chave privada que é exigida pelo gateway da aplicação para realizar a encriptação e desencriptação do tráfego.

Para a encriptação TLS de ponta a ponta, a parte traseira deve ser explicitamente permitida pelo gateway da aplicação. Faça o upload do certificado público dos servidores back-end para o gateway de aplicações. A adição do certificado garante que o gateway de aplicação apenas comunica com instâncias de back-end conhecidas. Isto assegura ainda mais a comunicação de ponta a ponta.

O processo de configuração é descrito nas seguintes secções.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Esta secção acompanha-o através da criação de um grupo de recursos que contém o gateway de aplicações.

1. Inicie sessão na sua conta do Azure.

   ```powershell
   Connect-AzAccount
   ```

2. Selecione a subscrição a utilizar para este cenário.

   ```powershell
   Select-Azsubscription -SubscriptionName "<Subscription name>"
   ```

3. Crie um grupo de recursos. (Ignore este passo se estiver a utilizar um grupo de recursos existente.)

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicação

O exemplo a seguir cria uma rede virtual e duas sub-redes. Uma sub-rede é usada para segurar o gateway de aplicação. A outra sub-rede é utilizada para as extremidades traseiras que hospedam a aplicação web.

1. Atribua um intervalo de endereços para a sub-rede a ser utilizada para o gateway de aplicações.

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > As sub-redes configuradas para um gateway de aplicação devem ser adequadamente dimensionadas. Um gateway de aplicação pode ser configurado para até 10 instâncias. Cada instância requer um endereço IP a partir da sub-rede. Uma sub-rede demasiado pequena pode afetar negativamente a escala de um gateway de aplicação.
   >

2. Atribua um intervalo de endereços para ser utilizado para o conjunto de endereços back-end.

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. Criar uma rede virtual com as sub-redes definidas nos passos anteriores.

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. Recupere o recurso de rede virtual e os recursos de sub-rede para serem utilizados nos passos que se seguem.

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso IP público para ser utilizado para o gateway de aplicações. Este endereço IP público é utilizado num dos passos que se seguem.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> O Application Gateway não suporta a utilização de um endereço IP público criado com uma etiqueta de domínio definida. Apenas um endereço IP público com uma etiqueta de domínio criada dinamicamente é suportado. Se necessitar de um nome de DNS amigável para o gateway de aplicações, recomendamos que utilize um registo CNAME como pseudónimo.

## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração do gateway de aplicação

Todos os itens de configuração são definidos antes de criar o gateway de aplicação. Com os seguintes passos, irá criar os itens de configuração necessários para um recurso do gateway de aplicação.

1. Crie uma configuração IP de gateway de aplicação. Esta definição configura quais das sub-redes que o gateway de aplicação utiliza. Quando o gateway de aplicações começa, capta um endereço IP da sub-rede configurada e encaminha o tráfego de rede para os endereços IP no pool IP back-end. Note que cada instância terá um endereço IP.

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. Crie uma configuração IP frontal. Esta definição mapeia um endereço IP privado ou público para a extremidade frontal do gateway de aplicações. O passo seguinte associa o endereço IP público no passo anterior com a configuração IP frontal.

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Configure o conjunto de endereços IP back-end com os endereços IP dos servidores web de back-end. Estes endereços são os endereços IP que recebem o tráfego de rede que vem do ponto final do IP do front-end. Substitua os endereços IP na amostra pelos pontos finais do endereço IP da sua própria aplicação.

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Um nome de domínio totalmente qualificado (FQDN) é também um valor válido a ser usado no lugar de um endereço IP para os servidores de back-end. Ativa-o utilizando o interruptor **-BackendFqdns.** 

4. Configure a porta IP frontal para o ponto final IP público. Esta porta é a porta à qual os utilizadores finais se ligam.

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Configurar o certificado para o gateway de aplicação. Este certificado é utilizado para desencriptar e reencrimar o tráfego no gateway de aplicações.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Esta amostra configura o certificado utilizado para a ligação TLS. O certificado tem de estar em formato .pfx, e a palavra-passe deve ser de 4 a 12 caracteres.

6. Crie o ouvinte HTTP para o gateway de aplicações. Atribua a configuração IP frontal, a porta e o certificado TLS/SSL para utilizar.

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Faça o upload do certificado a utilizar nos recursos de pool back-end ativados pelo TLS.

   > [!NOTE]
   > A sonda predefinida obtém a chave pública da ligação *padrão* do TLS no endereço IP do back-end e compara o valor de chave pública que recebe com o valor de chave pública que fornece aqui. 
   > 
   > Se estiver a utilizar cabeçalhos de anfitrião e indicação de nome do servidor (SNI) na parte de trás, a chave pública recuperada pode não ser o local pretendido para o qual o tráfego flui. Em caso de dúvida, visite https://127.0.0.1/ os servidores back-end para confirmar que certificado é utilizado para a ligação *padrão* TLS. Utilize a chave pública desse pedido nesta secção. Se estiver a utilizar cabeçalhos de anfitrião e SNI em encadernações HTTPS e não receber uma resposta e certificado de um pedido de navegador manual para https://127.0.0.1/ os servidores de back-end, tem de configurar uma ligação TLS predefinido sobre os mesmos. Se não o fizer, as sondas falham e a parte de trás não é permitida.
   
   Para obter mais informações sobre o SNI no Gateway de aplicações, consulte [a visão geral da rescisão do TLS e o fim do TLS com o Gateway de aplicações.](ssl-overview.md)

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > O certificado fornecido no passo anterior deve ser a chave pública do certificado .pfx presente na parte de trás. Exporte o certificado (não o certificado raiz) instalado no servidor back-end em formato Reivindicação, Evidência e Raciocínio (CER) e utilize-o neste passo. Este passo permite a parte traseira com o gateway de aplicação.

   Se estiver a utilizar o Application Gateway v2 SKU, então crie um certificado de raiz fidedigno em vez de um certificado de autenticação. Para mais informações, consulte [a visão geral do fim do TLS com o Gateway de aplicações:](ssl-overview.md#end-to-end-tls-with-the-v2-sku)

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Configure as definições HTTP para a extremidade traseira do gateway de aplicação. Atribua o certificado carregado no passo anterior às definições HTTP.

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   Para o Gateway de Aplicação v2 SKU, utilize o seguinte comando:

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Crie uma regra de encaminhamento de balançadores de carga que configura o comportamento do equilibrador de carga. Neste exemplo, é criada uma regra básica de rodapé.

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Configure o tamanho da instância do gateway de aplicação. Os tamanhos disponíveis são **Standard \_ Small,** **Standard \_ Medium** e **Standard \_ Large**.  Para capacidade, os valores disponíveis são **de 1** a **10**.

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Uma contagem de exemplos de 1 pode ser escolhida para fins de teste. É importante saber que qualquer caso contado em duas instâncias não é abrangido pelo SLA e, por conseguinte, não é recomendado. Os pequenos portais devem ser utilizados para o teste dev e não para fins de produção.

11. Configure a política TLS a utilizar no gateway de aplicações. O Application Gateway suporta a capacidade de definir uma versão mínima para versões de protocolo TLS.

    Os seguintes valores são uma lista de versões protocolares que podem ser definidas:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    O exemplo a seguir define a versão mínima do protocolo para **TLSv1_2** e permite **tLS \_ ECDHE \_ ECDSA \_ com \_ AES \_ 128 \_ GCM \_ SHA256,** **TLS \_ ECDHE \_ ECDSA \_ COM \_ AES \_ 256 \_ GCM \_ SHA384,** e **TLS \_ RSA \_ apenas com \_ AES \_ 128 \_ GCM \_ SHA256.**

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

Utilizando todos os passos anteriores, crie o gateway de aplicação. A criação da porta de entrada é um processo que demora muito tempo a ser executado.

Para V1 SKU use o comando abaixo
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

Para V2 SKU use o comando abaixo
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>Aplicar um novo certificado se o certificado de back-end estiver caducado

Utilize este procedimento para aplicar um novo certificado se o certificado de back-end estiver caducado.

1. Recupere o portal de aplicação para atualizar.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Adicione o novo recurso de certificado do ficheiro .cer, que contém a chave pública do certificado e também pode ser o mesmo certificado adicionado ao ouvinte para a rescisão de TLS no gateway de aplicação.

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. Obtenha o novo objeto de certificado de autenticação numa variável (TipoName: Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayAuthenticationCertificato).

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. Atribua o novo certificado à Definição **BackendHttp** e encaminhe-o para a variável $AuthCert. (Especificar o nome de definição HTTP que pretende alterar.)
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. Comprometa a alteração no gateway de aplicação e passe a nova configuração contida na variável $out.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>Remover um certificado expirado não-aused de definições HTTP

Utilize este procedimento para remover um certificado expirado não utilizado das definições HTTP.

1. Recupere o portal de aplicação para atualizar.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Lista o nome da certidão de autenticação que pretende remover.

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. Retire o certificado de autenticação de um gateway de aplicação.

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. Cometa a mudança.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-tls-protocol-versions-on-an-existing-application-gateway"></a>Limite as versões do protocolo TLS num gateway de aplicações existente

Os passos anteriores levaram-no a criar uma aplicação com TLS de ponta a ponta e a desativar certas versões do protocolo TLS. O exemplo a seguir desativa certas políticas TLS num gateway de aplicações existente.

1. Recupere o portal de aplicação para atualizar.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. Defina uma política de TLS. No exemplo seguinte, **os TLSv1.0** e **TLSv1.1** estão desativados e as suítes de cifra **TLS \_ \_ ECDHE ECDSA \_ com \_ AES \_ 128 \_ GCM \_ SHA256,** **TLS \_ ECDHE \_ ECDSA \_ COM \_ AES \_ 256 \_ GCM \_ SHA384**, e **TLS \_ RSA \_ COM \_ AES \_ 128 \_ GCM \_ SHA256** são os únicos permitidos.

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. Finalmente, atualize o portal. Este último passo é uma tarefa de longa data. Quando é feito, o TLS de ponta a ponta é configurado no gateway de aplicação.

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Obtenha um nome DNS de gateway de aplicação

Após a criação do portal, o próximo passo é configurar a extremidade frontal para a comunicação. O Gateway de Aplicação requer um nome DNS atribuído dinamicamente ao utilizar um IP público, o que não é amigável. Para garantir que os utilizadores finais podem atingir o gateway da aplicação, pode utilizar um registo CNAME para apontar para o ponto final público do gateway da aplicação. Para obter mais informações, consulte [configurar um nome de domínio personalizado para em Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Para configurar um pseudónimo, recupere detalhes do gateway de aplicação e do seu nome IP/DNS associado, utilizando o elemento **PublicIPAddress** ligado ao gateway de aplicações. Utilize o nome DNS do gateway de aplicação para criar um registo CNAME que aponta as duas aplicações web para este nome DNS. Não recomendamos a utilização de registos A, porque o VIP pode mudar no reinício do gateway de aplicações.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o endurecimento da segurança das suas aplicações web com firewall de aplicação web através do Gateway de aplicações, consulte a visão geral da firewall da [aplicação Web](../web-application-firewall/ag/ag-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png