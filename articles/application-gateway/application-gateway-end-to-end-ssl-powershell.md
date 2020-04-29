---
title: Configure TLS de ponta a ponta com gateway de aplicação Azure
description: Este artigo descreve como configurar TLS de ponta a ponta com gateway de aplicação Azure usando powerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/8/2019
ms.author: victorh
ms.openlocfilehash: 481cbda1d35f7d630dabca00fd01677f542447c2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81312503"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-powershell"></a>Configure fim para fim do TLS utilizando o Gateway da Aplicação com powerShell

## <a name="overview"></a>Descrição geral

O Portal de Aplicações Azure suporta a encriptação de ponta a ponta do tráfego. O Gateway de aplicação termina a ligação TLS/SSL no gateway da aplicação. O portal aplica então as regras de encaminhamento ao tráfego, encripta o pacote e reencaminha o pacote para o servidor back-end apropriado com base nas regras de encaminhamento definidas. Qualquer resposta do servidor Web atravessa o mesmo processo para o utilizador final.

O Application Gateway suporta a definição de opções TLS personalizadas. Também suporta a desativação das seguintes versões protocolares: **TLSv1.0**, **TLSv1.1**e **TLSv1.2,** bem como definir quais as suites de cifra a utilizar e a ordem de preferência. Para saber mais sobre opções configuráveis de TLS, consulte a visão geral da [política do TLS](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> SSL 2.0 e SSL 3.0 são desativados por defeito e não podem ser ativados. São considerados inseguros e não podem ser utilizados com o Gateway de Aplicação.

![imagem de cenário][scenario]

## <a name="scenario"></a>Cenário

Neste cenário, aprende-se a criar um portal de aplicação utilizando TLS de ponta a ponta com powerShell.

Este cenário irá:

* Crie um grupo de recursos chamado **appgw-rg**.
* Crie uma rede virtual chamada **appgwvnet** com um espaço de endereço de **10.0.0.0.0/16**.
* Crie duas subredes chamadas **appgwsubnet** e **appsubnet**.
* Crie um pequeno portal de aplicações que suporte a encriptação TLS de ponta a ponta que limita versões de protocolo TLS e suítes cifra.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para configurar tLS de ponta a ponta com um gateway de aplicação, é necessário um certificado para o gateway e os certificados são necessários para os servidores de back-end. O certificado de gateway é utilizado para obter uma chave simétrica de acordo com a especificação do protocolo TLS. A chave simétrica é então usada encriptar e desencriptar o tráfego enviado para o portal. O certificado de gateway tem de estar no formato Personal Information Exchange (PFX). Este formato de ficheiro permite-lhe exportar a chave privada que é exigida pela porta de entrada de aplicação para realizar a encriptação e desencriptação do tráfego.

Para a encriptação TLS de ponta a ponta, a parte traseira deve ser explicitamente permitida pelo gateway da aplicação. Faça upload do certificado público dos servidores back-end para o gateway da aplicação. A adição do certificado garante que o gateway da aplicação comunica apenas com instâncias conhecidas. Isto assegura ainda a comunicação de ponta a ponta.

O processo de configuração é descrito nas seguintes secções.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Esta secção acompanha-o através da criação de um grupo de recursos que contém o portal de aplicação.

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

O exemplo seguinte cria uma rede virtual e duas subnets. Uma sub-rede é usada para manter o gateway de aplicação. A outra sub-rede é utilizada para as extremidades traseiras que acolhem a aplicação web.

1. Atribuir uma gama de endereços para a sub-rede a utilizar para o gateway de aplicação.

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > As subredes configuradas para um gateway de aplicação devem ser corretamente dimensionadas. Um gateway de aplicação pode ser configurado para até 10 instâncias. Cada instância retira um endereço IP da sub-rede. Uma sub-rede demasiado pequena pode afetar negativamente a escala de um gateway de aplicação.
   >

2. Atribua uma gama de endereços a utilizar para a piscina de endereços de back-end.

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. Criar uma rede virtual com as subredes definidas nos passos anteriores.

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. Recupere os recursos de rede virtual e os recursos de sub-rede a utilizar nos passos que se seguem.

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso IP público para ser utilizado para o gateway da aplicação. Este endereço IP público é usado num dos passos que se seguem.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> O Application Gateway não suporta a utilização de um endereço IP público criado com um rótulo de domínio definido. Apenas é suportado um endereço IP público com uma etiqueta de domínio criada dinamicamente. Se necessitar de um nome DNS amigável para o gateway da aplicação, recomendamos que utilize um registo CNAME como pseudónimo.

## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração do gateway de aplicação

Todos os itens de configuração são definidos antes de criar o gateway da aplicação. Com os seguintes passos, irá criar os itens de configuração necessários para um recurso do gateway de aplicação.

1. Crie uma configuração IP de gateway de aplicação. Esta definição configura qual das subnets que o gateway de aplicação utiliza. Quando o gateway da aplicação começa, ele recolhe um endereço IP da subnet configurada e o tráfego de rede de rotas para os endereços IP no pool IP de back-end. Note que cada instância terá um endereço IP.

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. Crie uma configuração IP frontal. Esta definição mapeia um endereço IP privado ou público para a parte frontal do gateway da aplicação. O passo seguinte associa o endereço IP público na etapa anterior com a configuração IP frontal.

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Configure o conjunto de endereços IP de back-end com os endereços IP dos servidores web de back-end. Estes endereços são os endereços IP que recebem o tráfego de rede que vem do ponto final do IP do front-end. Substitua os endereços IP na amostra por pontos finais de endereço IP da sua própria aplicação.

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Um nome de domínio totalmente qualificado (FQDN) também é um valor válido para ser usado no lugar de um endereço IP para os servidores back-end. Ativa-o utilizando o interruptor **-BackendFqdns.** 

4. Configure a porta IP frontal para o ponto final ip público. Esta porta é a porta a que os utilizadores finais se ligam.

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Configure o certificado para o gateway de candidatura. Este certificado é utilizado para desencriptar e reencriptar o tráfego no gateway da aplicação.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Esta amostra configura o certificado utilizado para a ligação TLS. O certificado tem de estar em formato .pfx, e a palavra-passe deve ser de 4 a 12 caracteres.

6. Crie o ouvinte HTTP para o gateway da aplicação. Atribuir a configuração IP frontal, porta e certificado TLS/SSL para utilizar.

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Faça o upload do certificado a utilizar nos recursos de piscina de back-end ativados pelo TLS.

   > [!NOTE]
   > A sonda predefinida obtém a chave pública a partir da ligação *padrão* do TLS no endereço IP do back-end e compara o valor de chave pública que recebe com o valor-chave público que fornece aqui. 
   > 
   > Se estiver a utilizar cabeçalhos de anfitrião e indicações de nome de servidor (SNI) na parte de trás, a chave pública recuperada pode não ser o local pretendido para o qual o tráfego flui. Em caso de dúvida, https://127.0.0.1/ visite os servidores de back-end para confirmar qual o certificado utilizado para a ligação *padrão* do TLS. Utilize a chave pública desse pedido nesta secção. Se estiver a utilizar cabeçalhos de anfitrião e SNI em encadernações https://127.0.0.1/ HTTPS e não receber uma resposta e certificado de um pedido manual de navegador para os servidores de back-end, tem de configurar uma ligação TLS padrão nos mesmos. Se não o fizer, as sondas falham e a parte de trás não está na lista branca.

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > O certificado fornecido no escalão anterior deve ser a chave pública do certificado .pfx presente na parte de trás. Exportar o certificado (não o certificado raiz) instalado no servidor back-end em formato Dereclamação, Evidência e Raciocínio (CER) e utilizá-lo neste passo. Este passo lista a parte de trás com o gateway de aplicação.

   Se estiver a utilizar o Gateway de aplicação v2 SKU, crie um certificado de raiz fidedigno em vez de um certificado de autenticação. Para mais informações, consulte [a visão geral do fim para o tLS final com o Gateway da Aplicação:](ssl-overview.md#end-to-end-tls-with-the-v2-sku)

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Configure as definições http para a extremidade traseira do gateway da aplicação. Atribuir o certificado enviado no passo anterior às definições http.

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   Para o Gateway de Aplicação v2 SKU, utilize o seguinte comando:

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Crie uma regra de encaminhamento de equilíbrio de carga que configura o comportamento do equilibrador de carga. Neste exemplo, é criada uma regra básica de rodapé.

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Configure o tamanho da instância do gateway de aplicação. Os tamanhos disponíveis são **Standard\_Small,** **\_Standard Medium**e Standard **\_Large.**  Para a capacidade, os valores disponíveis são **de 1** a **10**.

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Uma contagem de 1 pode ser escolhida para efeitos de teste. É importante saber que qualquer contagem de casos em duas instâncias não é abrangida pelo SLA e, por conseguinte, não é recomendada. Os pequenos portais devem ser utilizados para o teste de dev e não para fins de produção.

11. Configure a política de TLS a utilizar no gateway da aplicação. O Application Gateway suporta a capacidade de definir uma versão mínima para versões protocolares TLS.

    Os seguintes valores são uma lista de versões protocolares que podem ser definidas:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    O exemplo seguinte define a versão do protocolo mínimo para **TLSv1_2** e permite **\_tLS ECDHE\_ECDSA\_COM AES\_\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_COM\_AES\_256\_GCM\_SHA384**, e **TLS\_RSA\_apenas com\_AES\_128\_GCM\_SHA256.**

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

Utilizando todos os passos anteriores, crie o gateway da aplicação. A criação do portal é um processo que leva muito tempo a ser executado.

Para V1 SKU use o comando abaixo
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

Para V2 SKU use o comando abaixo
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>Aplicar um novo certificado se o certificado final expirar

Utilize este procedimento para aplicar um novo certificado se o certificado final expirar.

1. Recupere a porta de entrada da aplicação para atualizar.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Adicione o novo recurso de certificado do ficheiro .cer, que contém a chave pública do certificado e pode também ser o mesmo certificado adicionado ao ouvinte para a rescisão de TLS no gateway da aplicação.

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. Obtenha o novo objeto de certificado de autenticação numa variável (TypeName: Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayAuthenticationCertificate).

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. Designar o novo certificado na Definição **BackendHttp** e encaminhá-lo com a variável $AuthCert. (Especifique o nome de definição HTTP que pretende alterar.)
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. Comprometa a alteração no gateway da aplicação e passe a nova configuração contida na variável $out.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>Remova um certificado expirado não utilizado das definições http

Utilize este procedimento para remover um certificado expirado não utilizado das definições http.

1. Recupere a porta de entrada da aplicação para atualizar.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Enumera o nome do certificado de autenticação que pretende remover.

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. Retire o certificado de autenticação de um gateway de pedido.

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. Cometa o troco.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-tls-protocol-versions-on-an-existing-application-gateway"></a>Limite as versões de protocolo TLS numa porta de aplicação existente

Os passos anteriores levaram-no através da criação de uma aplicação com TLS de ponta a ponta e desativando certas versões de protocolo TLS. O exemplo que se segue desativa certas políticas de TLS numa porta de aplicação existente.

1. Recupere a porta de entrada da aplicação para atualizar.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. Defina uma política de TLS. No exemplo seguinte, **TLSv1.0** e **TLSv1.1** são desativados e as suites de cifra **TLS\_\_ECDHE ECDSA\_COM\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_COM\_AES\_256\_GCM\_SHA384**, e **TLS\_RSA\_COM\_AES\_128\_GCM\_SHA256** são os únicos permitidos.

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. Finalmente, atualize o portal. Este último passo é uma tarefa de longa data. Quando estiver feito, o TLS de ponta a ponta está configurado no gateway da aplicação.

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Obtenha um nome dNS de gateway de aplicação

Após a criação do portal, o próximo passo é configurar a extremidade frontal para a comunicação. O Gateway de aplicação requer um nome DNS dNS disperte dinamicamente atribuído ao utilizar um IP público, que não é amigável. Para garantir que os utilizadores finais podem chegar ao gateway da aplicação, pode utilizar um registo CNAME para apontar para o ponto final público do gateway da aplicação. Para mais informações, consulte Configurar um nome de [domínio personalizado para o Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Para configurar um pseudónimo, recupere detalhes do gateway da aplicação e do seu nome IP/DNS associado utilizando o elemento **PublicIPAddress** ligado ao gateway da aplicação. Utilize o nome DNS do gateway da aplicação para criar um registo CNAME que aponte as duas aplicações web para este nome DNS. Não recomendamos a utilização de registos A, porque o VIP pode alterar no reinício do gateway de aplicação.

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

Para obter mais informações sobre o endurecimento da segurança das suas aplicações web com firewall de aplicação web através do Gateway de aplicações, consulte a visão geral da firewall da [aplicação Web](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
