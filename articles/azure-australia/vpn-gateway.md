---
title: Gateway de VPN do Azure na Austrália do Azure
description: Implementando o gateway de VPN na Austrália do Azure para estar em conformidade com o ISM e proteger as agências governamentais australianas com eficiência
author: galey801
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 00588042fa11ace51eef40cdedbae14c1bd99801
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575429"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Gateway de VPN do Azure na Austrália do Azure

Um serviço crítico com qualquer nuvem pública é a conexão segura de recursos e serviços de nuvem para sistemas locais existentes. O serviço que fornece esse recurso no Azure é o gateway de VPN do Azure. Este artigo descreve os principais pontos a serem considerados quando você configura um gateway de VPN para estar em conformidade com os controles do ISM (manual de [segurança de informações](https://acsc.gov.au/infosec/ism/)) dos sinais australianos do DIRECTORATE (ASD).

Um gateway de VPN é usado para enviar tráfego criptografado entre uma rede virtual no Azure e outra rede. Três cenários são tratados por gateways de VPN:

- Site a site (S2S)
- Ponto a site (P2S)
- Rede a rede

Este artigo se concentra em gateways de VPN S2S. O diagrama 1 mostra uma configuração de gateway de VPN S2S de exemplo.

![Gateway de VPN com conexões multissite](media/vpngateway-multisite-connection-diagram.png)

*Diagrama 1 – gateway de VPN S2S do Azure*

## <a name="key-design-considerations"></a>Considerações de design-chave

Há três opções de rede para conectar os clientes do Azure ao governo australiano:

- CONE
- Azure ExpressRoute
- Internet pública

O guia do consumidor da australiana do centro de segurança da Austrália [para o Azure](https://servicetrust.microsoft.com/viewpage/Australia) recomenda que o gateway de VPN (ou um serviço de terceiros com certificação protegido) seja usado em conjunto com as três opções de rede. Essa recomendação é garantir que as conexões estejam em conformidade com os controles do ISM para criptografia e integridade.

### <a name="encryption-and-integrity"></a>Criptografia e integridade

Por padrão, a VPN negocia os algoritmos de criptografia e integridade e os parâmetros durante o estabelecimento da conexão como parte dos Handshakes IKE. Durante o handshake IKE, a configuração e a ordem de preferência dependem se o gateway de VPN é o iniciador ou o respondente. Essa designação é controlada por meio do dispositivo VPN. A configuração final da conexão é controlada pela configuração do dispositivo VPN. Para obter mais informações sobre dispositivos VPN validados e sua configuração, consulte [sobre os serviços VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

Os gateways de VPN podem controlar a criptografia e a integridade Configurando uma política de IPsec/IKE personalizada na conexão.

### <a name="resource-operations"></a>Operações de recurso

Os gateways de VPN criam uma conexão entre ambientes do Azure e não Azure na Internet pública. O ISM tem controles relacionados à autorização explícita de conexões. Por padrão, é possível usar gateways de VPN para criar túneis não autorizados em ambientes seguros. É fundamental que as organizações usem o RBAC (controle de acesso baseado em função) do Azure para controlar quem pode criar e modificar gateways de VPN e suas conexões. O Azure não tem função interna para gerenciar gateways de VPN, portanto, uma função personalizada é necessária.

O acesso a funções de colaborador de rede, colaborador e proprietário é rigidamente controlado. Também recomendamos que você use Azure Active Directory Privileged Identity Management para controle de acesso mais granular.

### <a name="high-availability"></a>Elevada disponibilidade

Os gateways de VPN do Azure podem ter várias conexões e dar suporte a vários dispositivos VPN locais para o mesmo ambiente local. Consulte o diagrama 1.

As redes virtuais no Azure podem ter vários gateways de VPN que podem ser implantados em configurações independentes, ativas ou ativas no Active-Active.

Recomendamos que você implante todos os gateways de VPN em uma [configuração altamente disponível](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable). Um exemplo é dois dispositivos VPN locais conectados a dois gateways de VPN no modo ativo-passivo ou ativo-ativo. Consulte o diagrama 2.

![Conexões redundantes de gateway de VPN](media/dual-redundancy.png)

*Diagrama 2 – gateways de VPN ativo-ativo e dois dispositivos VPN*

### <a name="forced-tunneling"></a>Túnel forçado

Os redirecionamentos de túnel forçado, ou força, todo o tráfego de entrada na Internet para o ambiente local por meio do gateway de VPN para inspeção e auditoria. Sem o túnel forçado, o tráfego de entrada na Internet de VMs no Azure atravessa a infraestrutura de rede do Azure diretamente para a Internet pública, sem a opção de inspecionar ou auditar o tráfego. O túnel forçado é essencial quando uma organização é obrigada a usar um Secure Internet Gateway (SIG) para um ambiente.

## <a name="detailed-configuration"></a>Configuração detalhada

### <a name="service-attributes"></a>Atributos de serviço

Os gateways de VPN para conexões S2S configuradas para o governo australiano devem ter os seguintes atributos:

|Atributo | Deve|
|--- | --- |
|gatewayType | VPNS|
|

As configurações de atributo necessárias para obedecer aos controles do ISM para protegido são:

|Atributo | Deve|
|--- |---|
|vpnType |RouteBased|
|vpnClientConfiguration/vpnClientProtocols | Conexões|
|

Os gateways de VPN do Azure dão suporte a uma variedade de algoritmos criptográficos dos padrões de protocolo IKE e IPsec. A política padrão define a interoperabilidade máxima com uma ampla variedade de dispositivos VPN de terceiros. Como resultado, é possível que, durante o handshake IKE, uma configuração não compatível possa ser negociada. É altamente recomendável que você aplique parâmetros de [política IPSec/IKE personalizados](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell) para vpnClientConfiguration em gateways de VPN para garantir que as conexões atendam aos controles do ISM para conexões de ambiente local para o Azure. Os atributos de chave são mostrados na tabela a seguir.

|Atributo|Caso|Deve|
|---|---|---|
|saLifeTimeSeconds|< 14400 segundos|> 300 segundos|
|saDataSizeKilobytes| |> 1024 KB|
|ipsecEncryption| |AES256-GCMAES256|
|ipsecIntegrity| |SHA256-GCMAES256|
|ikeEncryption| |AES256-GCMAES256|
|ikeIntegrity| |SHA256-GCMAES256|
|dhGroup|DHGroup14, DHGroup24, ECP256, ECP384|DHGroup2|
|pfsGroup|PFS2048, PFS24, ECP256, ECP384||
|

Para dhGroup e pfsGroup na tabela anterior, ECP256 e ECP384 são preferenciais, embora outras configurações possam ser usadas.

### <a name="related-services"></a>Serviços relacionados

Quando você cria e configura um gateway de VPN do Azure, vários serviços relacionados também devem existir e ser configurados.

|Serviço | Ação requerida|
|--- | ---|
|Rede virtual | Os gateways de VPN são anexados a uma rede virtual. Crie uma rede virtual antes de criar um novo gateway de VPN.|
|Endereço IP público | Os gateways de VPN S2S precisam de um endereço IP público para estabelecer a conectividade entre o dispositivo VPN local e o gateway de VPN. Crie um endereço IP público antes de criar um gateway de VPN S2S.|
|Subnet | Crie uma sub-rede da rede virtual para o gateway de VPN.|
|

## <a name="implementation-steps-using-powershell"></a>Etapas de implementação usando o PowerShell

### <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

1. Crie uma função personalizada. Um exemplo é colaborador de virtualNetworkGateway. Crie uma função a ser atribuída aos usuários que terão permissão para criar e modificar gateways de VPN. A função personalizada deve permitir as seguintes operações:

   Microsoft. Network/virtualNetworkGateways/*  
   Microsoft. Network/Connections/*  
   Microsoft. Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft. Network/publicIPAddresses/*  
   Microsoft. Network/publicIPPrefixes/*  
   Microsoft.Network/routeTables/*  

2. Adicione a função personalizada aos usuários que têm permissão para criar e gerenciar gateways de VPN e conexões com ambientes locais.

### <a name="create-a-vpn-gateway"></a>Criar um gateway de VPN

Essas etapas pressupõem que você já criou uma rede virtual.

1. Crie um novo endereço IP público.
2. Crie uma sub-rede de gateway de VPN.
3. Crie um arquivo de configuração de IP do gateway de VPN.
4. Crie um gateway de VPN.
5. Crie um gateway de rede local para o dispositivo VPN no local.
6. Crie uma política de IPsec. Esta etapa pressupõe que você esteja usando políticas de IPsec/IKE personalizadas.
7. Crie uma conexão entre o gateway de VPN e um gateway de rede local usando a política de IPsec.

### <a name="enforce-tunneling"></a>Impor túnel

Se o túnel forçado for necessário, antes de criar o gateway de VPN:

1. Crie uma tabela de rotas e regras de rota.
2. Associe uma tabela de rotas às sub-redes apropriadas.

Depois de criar o gateway de VPN:

- Defina GatewayDefaultSite para o ambiente local no gateway de VPN.

### <a name="example-powershell-script"></a>Exemplo de script do PowerShell

Um exemplo de script do PowerShell usado para criar uma política de IPsec/IKE personalizada está em conformidade com os controles do ISM para a classificação de segurança protegida australiana.

Ele pressupõe que a rede virtual, o gateway de VPN e os gateways locais existem.

#### <a name="create-an-ipsecike-policy"></a>Criar uma política de IPsec/IKE

O script de exemplo a seguir cria uma política de IPsec/IKE com os seguintes algoritmos e parâmetros:

- IKEv2: AES256, SHA256, DHGroup ECP256
- IPSec AES256, SHA256, PFS ECP256, SA tempo de vida de 14.400 segundos e 102,4 milhões KB

```powershell
$custompolicy = New-AzIpsecPolicy `
                    -IkeEncryption AES256 `
                    -IkeIntegrity SHA256 `
                    -DhGroup ECP256 `
                    -IpsecEncryption AES256 `
                    -IpsecIntegrity SHA256 `
                    -PfsGroup ECP256 `
                    -SALifeTimeSeconds 14400 `
                    -SADataSizeKilobytes 102400000
```

#### <a name="create-a-s2s-vpn-connection-with-the-custom-ipsecike-policy"></a>Criar uma conexão VPN S2S com a política de IPsec/IKE personalizada

```powershell
$vpngw = Get-AzVirtualNetworkGateway `
                    -Name "<yourVPNGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"
$localgw = Get-AzLocalNetworkGateway  `
                    -Name "<yourLocalGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"

New-AzVirtualNetworkGatewayConnection `
                    -Name "ConnectionName" `
                    -ResourceGroupName "<yourResourceGroupName>" `
                    -VirtualNetworkGateway1 $vpngw `
                    -LocalNetworkGateway2 $localgw `
                    -Location "Australia Central" `
                    -ConnectionType IPsec `
                    -IpsecPolicies $custompolicy `
                    -SharedKey "AzureA1b2C3"
```

## <a name="next-steps"></a>Passos Seguintes

Este artigo abordou a configuração específica do gateway de VPN para atender aos requisitos especificados no manual de segurança de informações para proteger os dados protegidos do governo australiano em trânsito. Para obter as etapas sobre como configurar seu gateway de VPN, consulte:

- [Visão geral do gateway de rede virtual do Azure](https://docs.microsoft.com/azure/vpn-gateway/)  
- [O que é o gateway de VPN?](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
- [Criar uma rede virtual com uma conexão VPN site a site usando o PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
- [Criar e gerir um gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)