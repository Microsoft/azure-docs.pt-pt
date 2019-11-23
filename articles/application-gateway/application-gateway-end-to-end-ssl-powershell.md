---
title: Configurar o SSL de ponta a ponta com o gateway de Aplicativo Azure
description: Este artigo descreve como configurar o SSL de ponta a ponta com o gateway de Aplicativo Azure usando o PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/8/2019
ms.author: victorh
ms.openlocfilehash: 7ba273cddb6cf41872c4db1c34560c104b992787
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286463"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>Configurar o SSL de ponta a ponta usando o gateway de aplicativo com o PowerShell

## <a name="overview"></a>Descrição geral

Aplicativo Azure gateway dá suporte à criptografia de ponta a ponta de tráfego. O gateway de aplicativo encerra a conexão SSL no gateway de aplicativo. Em seguida, o gateway aplica as regras de roteamento ao tráfego, criptografa novamente o pacote e encaminha o pacote para o servidor back-end apropriado com base nas regras de roteamento definidas. Qualquer resposta do servidor Web atravessa o mesmo processo para o utilizador final.

O gateway de aplicativo dá suporte à definição de opções de SSL personalizadas. Ele também dá suporte à desabilitação das seguintes versões de protocolo: **tlsv 1.0**, **Tlsv 1.1**e **tlsv 1.2**, bem como definir quais pacotes de codificação usar e a ordem de preferência. Para saber mais sobre as opções de SSL configuráveis, confira a [visão geral da política SSL](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> O SSL 2,0 e o SSL 3,0 estão desabilitados por padrão e não podem ser habilitados. Eles são considerados não seguros e não podem ser usados com o gateway de aplicativo.

![imagem do cenário][scenario]

## <a name="scenario"></a>Cenário

Nesse cenário, você aprende a criar um gateway de aplicativo usando o SSL de ponta a ponta com o PowerShell.

Este cenário irá:

* Crie um grupo de recursos chamado **appgw-RG**.
* Crie uma rede virtual chamada **appgwvnet** com um espaço de endereço de **10.0.0.0/16**.
* Crie duas sub-redes chamadas **appgwsubnet** e **appsubnet**.
* Crie um gateway de aplicativo pequeno com suporte para criptografia SSL de ponta a ponta que limita as versões do protocolo SSL e os conjuntos de codificação.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para configurar o SSL de ponta a ponta com um gateway de aplicativo, um certificado é necessário para o gateway e os certificados são necessários para os servidores back-end. O certificado de gateway é usado para derivar uma chave simétrica de acordo com a especificação do protocolo SSL. Em seguida, a chave simétrica é usada para criptografar e descriptografar o tráfego enviado para o gateway. O certificado de gateway precisa estar no formato PFX (troca de informações pessoais). Esse formato de arquivo permite exportar a chave privada exigida pelo gateway de aplicativo para executar a criptografia e a descriptografia do tráfego.

Para a criptografia SSL de ponta a ponta, o back-end deve ser explicitamente permitido pelo gateway de aplicativo. Carregue o certificado público dos servidores back-end para o gateway de aplicativo. A adição do certificado garante que o gateway de aplicativo se comunique somente com instâncias de back-end conhecidas. Isso protege ainda mais a comunicação de ponta a ponta.

O processo de configuração é descrito nas seções a seguir.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Esta seção orienta você pela criação de um grupo de recursos que contém o gateway de aplicativo.

1. Inicie sessão na sua conta do Azure.

   ```powershell
   Connect-AzAccount
   ```

2. Selecione a assinatura a ser usada para este cenário.

   ```powershell
   Select-Azsubscription -SubscriptionName "<Subscription name>"
   ```

3. Crie um grupo de recursos. (Ignore este passo se estiver a utilizar um grupo de recursos existente.)

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicação

O exemplo a seguir cria uma rede virtual e duas sub-redes. Uma sub-rede é usada para manter o gateway de aplicativo. A outra sub-rede é usada para os back-ends que hospedam o aplicativo Web.

1. Atribua um intervalo de endereços para a sub-rede a ser usada para o gateway de aplicativo.

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > As sub-redes configuradas para um gateway de aplicativo devem ser dimensionadas corretamente. Um gateway de aplicativo pode ser configurado para até 10 instâncias. Cada instância usa um endereço IP da sub-rede. Uma sub-rede muito pequena pode afetar negativamente a expansão de um gateway de aplicativo.
   >

2. Atribua um intervalo de endereços a ser usado para o pool de endereços de back-end.

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. Crie uma rede virtual com as sub-redes definidas nas etapas anteriores.

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. Recupere os recursos de rede virtual e de sub-rede a serem usados nas etapas a seguir.

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso IP público a ser usado para o gateway de aplicativo. Esse endereço IP público é usado em uma das etapas a seguir.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> O gateway de aplicativo não dá suporte ao uso de um endereço IP público criado com um rótulo de domínio definido. Há suporte apenas para um endereço IP público com um rótulo de domínio criado dinamicamente. Se você precisar de um nome DNS amigável para o gateway de aplicativo, recomendamos que use um registro CNAME como um alias.

## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração do gateway de aplicação

Todos os itens de configuração são definidos antes da criação do gateway de aplicativo. Com os seguintes passos, irá criar os itens de configuração necessários para um recurso do gateway de aplicação.

1. Crie uma configuração de IP do gateway de aplicativo. Essa configuração define quais das sub-redes o gateway de aplicativo usa. Quando o gateway de aplicativo é iniciado, ele escolhe um endereço IP da sub-rede configurada e roteia o tráfego de rede para os endereços IP no pool de IPS de back-end. Note que cada instância terá um endereço IP.

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. Crie uma configuração de IP de front-end. Essa configuração mapeia um endereço IP público ou privado para o front-end do gateway de aplicativo. A etapa a seguir associa o endereço IP público na etapa anterior à configuração de IP de front-end.

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Configure o pool de endereços IP de back-end com os endereços IP dos servidores Web de back-end. Estes endereços são os endereços IP que recebem o tráfego de rede que vem do ponto final do IP do front-end. Substitua os endereços IP no exemplo pelos seus próprios pontos de extremidade de endereço IP do aplicativo.

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Um FQDN (nome de domínio totalmente qualificado) também é um valor válido a ser usado no lugar de um endereço IP para os servidores back-end. Habilite-o usando a opção **-BackendFqdns** . 

4. Configure a porta de IP de front-end para o ponto de extremidade de IP público. Essa porta é a porta à qual os usuários finais se conectam.

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Configure o certificado para o gateway de aplicativo. Esse certificado é usado para descriptografar e criptografar novamente o tráfego no gateway de aplicativo.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Esta amostra configura o certificado usado para a conexão SSL. O certificado precisa estar no formato. pfx e a senha deve ter de 4 a 12 caracteres.

6. Crie o ouvinte HTTP para o gateway de aplicativo. Atribua a configuração de IP de front-end, a porta e o certificado SSL a ser usado.

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Carregue o certificado a ser usado nos recursos do pool de back-end habilitado para SSL.

   > [!NOTE]
   > A investigação padrão Obtém a chave pública da Associação SSL *padrão* no endereço IP do back-end e compara o valor de chave pública que ele recebe para o valor de chave pública que você fornece aqui. 
   > 
   > Se você estiver usando cabeçalhos de host e Indicação de Nome de Servidor (SNI) no back-end, a chave pública recuperada poderá não ser o site pretendido para o qual o tráfego flui. Se você estiver em dúvida, visite https://127.0.0.1/ nos servidores de back-end para confirmar qual certificado é usado para a associação SSL *padrão* . Use a chave pública dessa solicitação nesta seção. Se você estiver usando cabeçalhos de host e SNI em associações HTTPS e não receber uma resposta e um certificado de uma solicitação de navegador manual para https://127.0.0.1/ nos servidores back-end, deverá configurar uma associação SSL padrão neles. Se você não fizer isso, as investigações falharão e o back-end não estará na lista de permissões.

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > O certificado fornecido na etapa anterior deve ser a chave pública do certificado. pfx presente no back-end. Exporte o certificado (não o certificado raiz) instalado no servidor back-end no formato CER (Claim, Evidence e raciocínio) e use-o nesta etapa. Esta etapa lista as permissões de back-end com o gateway de aplicativo.

   Se você estiver usando o SKU do gateway de aplicativo v2, crie um certificado raiz confiável em vez de um certificado de autenticação. Para obter mais informações, consulte [visão geral do SSL de ponta a ponta com o gateway de aplicativo](ssl-overview.md#end-to-end-ssl-with-the-v2-sku):

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Defina as configurações de HTTP para o back-end do gateway de aplicativo. Atribua o certificado carregado na etapa anterior às configurações de HTTP.

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   Para o SKU do gateway de aplicativo v2, use o seguinte comando:

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Crie uma regra de roteamento de balanceador de carga que configure o comportamento do balanceador de carga. Neste exemplo, uma regra básica de Round Robin é criada.

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Configure o tamanho da instância do gateway de aplicação. Os tamanhos disponíveis são **standard\_Small**, **Standard\_Medium**e **Standard\_grande**.  Para a capacidade, os valores disponíveis são de **1** a **10**.

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Uma contagem de instâncias de 1 pode ser escolhida para fins de teste. É importante saber que qualquer contagem de instâncias em duas instâncias não é coberta pelo SLA e, portanto, não é recomendada. Gateways pequenos devem ser usados para teste de desenvolvimento e não para fins de produção.

11. Configure a política SSL a ser usada no gateway de aplicativo. O gateway de aplicativo dá suporte à capacidade de definir uma versão mínima para versões de protocolo SSL.

    Os seguintes valores são uma lista de versões de protocolo que podem ser definidas:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    O exemplo a seguir define a versão mínima do protocolo para **TLSv1_2** e habilita o **TLS\_ECDHE\_ecdsa\_com\_aes\_128\_GCM\_sha256**, **tls\_ECDHE\_ECDSA\_with\_AES\_256\_** GCM\_Sha384 e **tls\_RSA\_with\_AES\_128\_GCM\_SHA256** apenas.

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

Usando todas as etapas anteriores, crie o gateway de aplicativo. A criação do gateway é um processo que leva muito tempo para ser executado.

Para a SKU v1, use o comando abaixo
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

Para a SKU v2, use o comando abaixo
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>Aplicar um novo certificado se o certificado de back-end tiver expirado

Use este procedimento para aplicar um novo certificado se o certificado de back-end tiver expirado.

1. Recupere o gateway de aplicativo para atualizar.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Adicione o novo recurso de certificado do arquivo. cer, que contém a chave pública do certificado e também pode ser o mesmo certificado adicionado ao ouvinte para terminação SSL no gateway de aplicativo.

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. Obtenha o novo objeto de certificado de autenticação em uma variável (TypeName: Microsoft. Azure. Commands. Network. Models. PSApplicationGatewayAuthenticationCertificate).

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. Atribua o novo certificado à configuração **BackendHttp** e faça referência a ele com a variável $AuthCert. (Especifique o nome da configuração HTTP que você deseja alterar.)
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. Confirme a alteração no gateway de aplicativo e passe a nova configuração contida na variável $out.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>Remover um certificado expirado não utilizado das configurações de HTTP

Use este procedimento para remover um certificado expirado não utilizado das configurações de HTTP.

1. Recupere o gateway de aplicativo para atualizar.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Liste o nome do certificado de autenticação que você deseja remover.

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. Remova o certificado de autenticação de um gateway de aplicativo.

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. Confirme a alteração.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>Limitar as versões do protocolo SSL em um gateway de aplicativo existente

As etapas anteriores levaram você durante a criação de um aplicativo com SSL de ponta a ponta e a desabilitação de determinadas versões de protocolo SSL. O exemplo a seguir desabilita determinadas políticas SSL em um gateway de aplicativo existente.

1. Recupere o gateway de aplicativo para atualizar.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. Defina uma política SSL. No exemplo a seguir, o **tlsv 1.0** e o **tlsv 1.1** estão desabilitados e os conjuntos de codificação **TLS\_ECDHE\_ECDSA\_com\_AES\_128\_** GCM\_SHA256, **tls\_ECDHE\_ECDSA\_with\_AES\_256\_** GCM\_Sha384 e **tls\_RSA\_with\_AES\_128\_GCM\_SHA256** são os únicos permitidos.

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. Por fim, atualize o gateway. Esta última etapa é uma tarefa de execução longa. Quando terminar, o SSL de ponta a ponta será configurado no gateway de aplicativo.

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Obter um nome DNS do gateway de aplicativo

Depois que o gateway é criado, a próxima etapa é configurar o front-end para comunicação. O gateway de aplicativo requer um nome DNS atribuído dinamicamente ao usar um IP público, o que não é amigável. Para garantir que os usuários finais possam acessar o gateway de aplicativo, você pode usar um registro CNAME para apontar para o ponto de extremidade público do gateway de aplicativo. Para obter mais informações, consulte [Configurando um nome de domínio personalizado para o no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Para configurar um alias, recupere os detalhes do gateway de aplicativo e seu nome DNS/IP associado usando o elemento **PublicIPAddress** anexado ao gateway de aplicativo. Use o nome DNS do gateway de aplicativo para criar um registro CNAME que aponte os dois aplicativos Web para esse nome DNS. Não recomendamos o uso de registros A, pois o VIP pode ser alterado na reinicialização do gateway de aplicativo.

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

Para obter mais informações sobre como proteger a segurança de seus aplicativos Web com o Firewall do aplicativo Web por meio do gateway de aplicativo, consulte [visão geral do firewall do aplicativo Web](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
