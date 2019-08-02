---
title: Gateway de VPN do Azure na Austrália do Azure
description: Implementando o gateway de VPN na Austrália do Azure para estar em conformidade com o ISM e proteger as agências governamentais australianas com eficiência
author: galey801
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 442ad6334a1775033018005d4a85875dbcb08ada
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571851"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Gateway de VPN do Azure na Austrália do Azure

Um serviço crítico com qualquer nuvem pública é a conexão segura de recursos e serviços de nuvem para sistemas locais existentes.  O serviço que fornece esse recurso no Azure é o gateway de VPN do Azure (gateway de VPN). Este artigo descreve as principais considerações com a configuração do gateway de VPN para obedecer aos [controles manuais de segurança de informações](https://acsc.gov.au/infosec/ism/) (ASD) da Directorate de sinais da Austrália.

Um gateway de VPN é usado para enviar tráfego criptografado entre uma rede virtual no Azure e outra rede.  Há três cenários endereçados por gateways de VPN:

- **Site a site** S2S
- **Ponto a site** P2S
- **VNet-to-VNet**

Este artigo se concentrará em gateways de VPN S2S. O diagrama 1 mostra um exemplo de configuração de gateway de VPN site a site.

![Gateway de VPN com conexões multissite](media/vpngateway-multisite-connection-diagram.png)

*Diagrama 1 – gateway de VPN site a site do Azure*

## <a name="key-design-considerations"></a>Considerações de design-chave

Há três opções de rede para conectar os clientes do Azure ao governo australiano:

- **CONE**
- **ExpressRoute**
- **Internet pública**

O guia do consumidor do centro de segurança australiano da Austrália [para o Azure](https://servicetrust.microsoft.com/viewpage/Australia) recomenda que o gateway de VPN (ou serviço de terceiros certificado protegido) seja usado em conjunto com as três opções de rede para garantir que as conexões estejam em conformidade com o Controles do ISM para criptografia e integridade.

### <a name="encryption-and-integrity"></a>Criptografia e integridade

Por padrão, a VPN negocia os algoritmos de criptografia e integridade e os parâmetros durante o estabelecimento da conexão como parte dos Handshakes IKE.  Durante o handshake IKE, a configuração e a ordem de preferência dependerão se o gateway de VPN é o iniciador ou o Respondente (NB: isso é controlado por meio do dispositivo VPN).  A configuração final da conexão é controlada pela configuração do dispositivo VPN.  Para obter detalhes sobre os dispositivos VPN validados e sua configuração, consulte aqui: [Sobre dispositivos VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)

Os gateways de VPN podem controlar a criptografia e a integridade Configurando uma política de IPsec/IKE personalizada na conexão.

### <a name="resource-operations"></a>Operações de recurso

Os gateways de VPN criam uma conexão entre ambientes do Azure e não Azure na Internet pública.  O ISM tem controles relacionados à autorização explícita de conexões.  Por padrão, é possível usar gateways de VPN para criar túneis não autorizados em ambientes seguros.  Portanto, é essencial que as organizações usem o RBAC (controle de acesso baseado em função) do Azure para controlar quem pode criar e modificar gateways de VPN e suas conexões.  O Azure não tem função "interna" para gerenciar gateways de VPN, portanto, isso exigirá uma função personalizada.

O acesso às funções "proprietário", "colaborador" e "colaborador de rede" é rigidamente controlado.  Também é recomendável que Azure AD Privileged Identity Management seja usado para controle de acesso mais granular.

### <a name="high-availability"></a>Elevada disponibilidade

Os gateways de VPN do Azure podem ter várias conexões (consulte o diagrama 1) e oferecem suporte a vários dispositivos VPN locais para o mesmo ambiente local.  

As redes virtuais no Azure podem ter vários gateways de VPN que podem ser implantados em configurações independentes, ativas ou ativas no Active-Active.

É recomendável que todos os gateways de VPN sejam implantados em uma [configuração altamente disponível](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable): por exemplo, dois dispositivos VPN locais conectados a dois gateways de VPN no modo ativo-passivo ou ativo-ativo (consulte o diagrama 2).

![Conexões redundantes de gateway de VPN](media/dual-redundancy.png)

*Diagrama 2 – gateways de VPN ativo-ativo e dois dispositivos VPN*

### <a name="forced-tunneling"></a>Túnel forçado

O túnel forçado redireciona ou "força" todo o tráfego ligado à Internet de volta para o ambiente local por meio do gateway de VPN para inspeção e auditoria. Sem o túnel forçado, o tráfego de entrada na Internet de VMs no Azure atravessa a infraestrutura de rede do Azure diretamente para a Internet pública, sem a opção de inspecionar ou auditar o tráfego.  Isso é essencial quando a organização é obrigada a usar um Secure Internet Gateway (SIG) para um ambiente.

## <a name="detailed-configuration"></a>Configuração detalhada

### <a name="service-attributes"></a>Atributos de serviço

Gateways de VPN para conexões S2S configuradas para o governo australiano precisam ter os seguintes atributos:

|Atributo | DEVE|
|--- | --- |
|gatewayType | VPNS|
|

As configurações de atributo necessárias para obedecer aos controles do ISM para protegido são:

|Atributo | DEVE|
|--- |---|
|vpnType |RouteBased|
|vpnClientConfiguration/vpnClientProtocols | Conexões|
|

Os gateways de VPN do Azure dão suporte a uma variedade de algoritmos criptográficos dos padrões de protocolo IKE e IPsec.  A política padrão define a interoperabilidade maximizá com uma ampla variedade de dispositivos VPN de terceiros.  Como resultado, é possível que, durante o handshake IKE, uma configuração não compatível seja negociada.  Portanto, é altamente recomendável que os parâmetros de [política IPSec/IKE personalizados](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell) sejam aplicados ao VpnClientConfiguration nos gateways de VPN para garantir que as conexões atendam aos controles do ISM para conexões de ambiente local para o Azure.  Os principais atributos são:

|Atributo|CASO|DEVE|
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

*Para dhGroup e pfsGroup na tabela acima, ECP256 e ECP384 são preferenciais mesmo que outras configurações possam ser usadas*

### <a name="related-services"></a>Serviços relacionados

Ao criar e configurar um gateway de VPN do Azure, há vários serviços relacionados que também devem existir e ser configurados:

|Serviço | Ação Obrigatória|
|--- | ---|
|Rede virtual | Os gateways de VPN são anexados a uma rede virtual.  Uma rede virtual precisa ser criada antes da criação de um novo gateway de VPN.|
|Endereço IP Público | Os gateways de VPN S2S precisam de um endereço IP público para estabelecer a conectividade entre o dispositivo VPN local e o gateway de VPN.  Um endereço IP público precisa ser criado antes da criação de um gateway de VPN S2S.|
|Subnet | Uma sub-rede da rede virtual precisa ser criada para o gateway de VPN.|
|

## <a name="implementation-steps-using-powershell"></a>Etapas de implementação usando o PowerShell

### <a name="role-based-access-control-rbac"></a>Controlo de Acesso Baseado em Funções (RBAC)

1. Crie uma função personalizada (por exemplo, colaborador de virtualNetworkGateway).  Crie uma função a ser atribuída aos usuários que terão permissão para criar e modificar gateways de VPN. A função personalizada deve permitir as seguintes operações:

   Microsoft. Network/virtualNetworkGateways/*  
   Microsoft. Network/Connections/*  
   Microsoft. Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft. Network/publicIPAddresses/*  
   Microsoft. Network/publicIPPrefixes/*  
   Microsoft.Network/routeTables/*  

2. Adicione uma função personalizada aos usuários que têm permissão para criar e gerenciar gateways de VPN e conexões com ambientes locais.

### <a name="create-vpn-gateway"></a>Criar gateway de VPN

*Estas etapas pressupõem que uma rede virtual já foi criada*

1. Criar um novo endereço IP público
2. Criar uma sub-rede de gateway de VPN
3. Criar uma configuração de IP do gateway de VPN
4. Criar um Gateway de VPN
5. Criar um gateway de rede local para o dispositivo VPN no local
6. Criar uma política IPsec (supondo-se usar políticas de IPsec/IKE personalizadas)
7. Criar conexão entre o gateway de VPN e o gateway de rede local usando a política de IPsec

### <a name="enforce-tunneling"></a>Impor túnel

Se o túnel forçado for necessário, antes de criar o gateway de VPN:

1. Criar regra (s) de rota e tabela de rotas
2. Associar tabela de rotas às sub-redes apropriadas

Depois de criar o gateway de VPN:

1. Definir GatewayDefaultSite para o ambiente local no gateway de VPN

### <a name="example-powershell-script"></a>Exemplo de script do PowerShell

Um exemplo de script do PowerShell para criar uma política de IPSEC/IKE personalizada que está em conformidade com os controles do ISM para a classificação de segurança protegida australiana.

Ele pressupõe que a rede virtual, o gateway de VPN e os gateways locais existem.

#### <a name="create-an-ipsecike-policy"></a>Criar uma política de IPsec/IKE

O script de exemplo a seguir cria uma política de IPsec/IKE com os seguintes algoritmos e parâmetros:

- IKEv2: AES256, SHA256, DHGroup ECP256
- IPSec AES256, SHA256, PFS ECP256, SA tempo de vida de 14.400 segundos & 102,4 milhões KB

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

Este artigo abordou a configuração específica do gateway de VPN para atender aos requisitos especificados no manual de segurança da informação (ISM) para proteger os dados protegidos do governo australianos em trânsito. Para obter etapas detalhadas para configurar seu gateway de VPN:

- [Visão geral do gateway de rede virtual do Azure](https://docs.microsoft.com/azure/vpn-gateway/)  
- [O que é o gateway de VPN?](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
- [Criar uma VNet com uma conexão VPN site a site usando o PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
- [Criar e gerir um gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)