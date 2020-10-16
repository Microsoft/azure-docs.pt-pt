---
title: Configuração da amostra para ligar dispositivos Cisco ASA a gateways Azure VPN
description: Este artigo fornece uma configuração de amostra para ligar dispositivos Cisco ASA a gateways Azure VPN.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.openlocfilehash: 82e00e0b519040c57fdfd798d8992086c61e5481
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89426184"
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Configuração da amostra: Dispositivo Cisco ASA (IKEv2/no BGP)
Este artigo fornece configurações de amostra para ligar dispositivos Cisco Adaptive Security Appliance (ASA) a gateways Azure VPN. O exemplo aplica-se aos dispositivos Cisco ASA que estão a executar o IKEv2 sem o Protocolo de Gateway fronteiriço (BGP). 

## <a name="device-at-a-glance"></a>Dispositivo num relance

* Fornecedor de dispositivos: **Cisco**
* Modelo do dispositivo: **ASA**           
* Versão alvo: **8.4 e mais tarde**
* Modelo testado: **ASA 5505**
* Versão testada: **9.2**             
* Versão IKE: **IKEv2**                  
* BGP: **Não**      
* Tipo de gateway Azure VPN: **Gateway VPN baseado em rota**

> [!NOTE]
> A configuração da amostra liga um dispositivo Cisco ASA a um gateway VPN **baseado em rotas** Azure. A ligação utiliza uma política personalizada IPsec/IKE com a opção **UsePolicyBasedTrafficSelectors,** conforme descrito neste [artigo](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> A amostra requer que os dispositivos ASA utilizem a política **IKEv2** com configurações baseadas em listas de acesso, e não com base em VTI. Consulte as especificações do seu fornecedor de dispositivos VPN para verificar se a política IKEv2 é suportada nos seus dispositivos VPN no local.


## <a name="vpn-device-requirements"></a>Requisitos do dispositivo VPN
Os gateways Azure VPN utilizam as suítes de protocolo IPsec/IKE padrão para estabelecer túneis VPN site-to-site (S2S). Para os parâmetros detalhados do protocolo IPsec/IKE e algoritmos criptográficos padrão para gateways Azure VPN, consulte [sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md).

> [!NOTE]
> Pode especificar opcionalmente uma combinação exata de algoritmos criptográficos e pontos fortes chave para uma ligação específica, conforme descrito nos [requisitos criptográficos](vpn-gateway-about-compliance-crypto.md). Se especificar uma combinação exata de algoritmos e pontos fortes, certifique-se de que utiliza as especificações correspondentes nos seus dispositivos VPN.

## <a name="single-vpn-tunnel"></a>Túnel VPN único
Esta configuração consiste num único túnel S2S VPN entre um gateway VPN Azure e um dispositivo VPN no local. Pode configurar opcionalmente o BGP através do túnel VPN.

![Único túnel S2S VPN](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Para obter instruções passo a passo para construir as configurações do Azure, consulte a [configuração do túnel VPN único](vpn-gateway-3rdparty-device-config-overview.md#singletunnel).

### <a name="virtual-network-and-vpn-gateway-information"></a>Informações de rede virtual e gateway VPN
Esta secção lista os parâmetros da amostra.

| **Parâmetro**                | **Valor**                    |
| ---                          | ---                          |
| Prefixos de endereço de rede virtual        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN gateway IP         | Azure_Gateway_Public_IP      |
| Prefixos de endereço no local | 10.51.0.0/16<br>10.52.0.0/16 |
| IP dispositivo VPN no local    | OnPrem_Device_Public_IP     |
| * Rede virtual BGP ASN                | 65010                        |
| * Azure BGP peer IP           | 10.12.255.30                 |
| * No local BGP ASN         | 65050                        |
| * No local BGP peer IP     | 10.52.255.254                |
|                              |                              |

\* Parâmetro opcional apenas para BGP.

### <a name="ipsecike-policy-and-parameters"></a>Política e parâmetros IPsec/IKE
A tabela que se segue lista os algoritmos e parâmetros IPsec/IKE que são utilizados na amostra. Consulte as especificações do seu dispositivo VPN para verificar os algoritmos suportados para os modelos e versões de firmware do seu dispositivo VPN.

| **IPsec/IKEv2**  | **Valor**                            |
| ---              | ---                                  |
| Encriptação IKEv2 | AES256                               |
| Integridade do IKEv2  | SHA384                               |
| Grupo DH         | DHGroup24                            |
| * Encriptação do IPsec | AES256                               |
| * Integridade do IPsec  | SHA1                                 |
| Grupo PFS        | PFS24                                |
| Duração de SA QM   | 7.200 segundos                         |
| Seletor de tráfego | UsePolicyBasedTrafficSelectors $True |
| Chave Pré-partilhada   | PreSharedKey                         |
|                  |                                      |

\* Em alguns dispositivos, a Integridade IPsec deve ser um valor nulo quando o algoritmo de encriptação IPsec é AES-GCM.

### <a name="asa-device-support"></a>Suporte ao dispositivo ASA

* O suporte para iKEv2 requer a versão ASA 8.4 e posterior.

* O suporte ao Grupo DH e ao Grupo PFS para além do Grupo 5 requer a versão ASA 9.x.

* O suporte para encriptação IPsec com AES-GCM e IPsec Integrity com SHA-256, SHA-384 ou SHA-512 requer versão ASA 9.x. Este requisito de suporte aplica-se a dispositivos ASA mais recentes. No momento da publicação, os modelos ASA 5505, 5510, 5520, 5540, 5550 e 5580 não suportam estes algoritmos. Consulte as especificações do seu dispositivo VPN para verificar os algoritmos suportados para os modelos e versões de firmware do seu dispositivo VPN.


### <a name="sample-device-configuration"></a>Configuração do dispositivo de amostra
O script fornece uma amostra que se baseia na configuração e parâmetros descritos nas secções anteriores. A configuração do túnel S2S VPN consiste nas seguintes partes:

1. Interfaces e rotas
2. Listas de acesso
3. Política e parâmetros IKE (fase 1 ou modo principal)
4. Política e parâmetros IPsec (fase 2 ou modo rápido)
5. Outros parâmetros, como o aperto de SS TCP

> [!IMPORTANT]
> Complete os seguintes passos antes de utilizar o script da amostra. Substitua os valores do espaço reservado no script com as definições do dispositivo para a sua configuração.

* Especifique a configuração da interface para interfaces internas e externas.
* Identifique as rotas para as suas redes interior/privada e externa/pública.
* Certifique-se de que todos os nomes e números de apólices são únicos no seu dispositivo.
* Certifique-se de que os algoritmos criptográficos são suportados no seu dispositivo.
* Substitua os **seguintes valores de espaço reservado** por valores reais para a sua configuração:
  - Nome de interface exterior: **fora**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - **Pre_Shared_Key**
  - Nomes de gateway de rede virtual e de rede local: **VNetName** e **LNGName**
  - **Prefixos de** endereço de rede de rede virtuais e no local
  - **Máscaras de rede adequadas**

#### <a name="sample-script"></a>Script de exemplo

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec proposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>Simples comandos de depuragem

Utilize os seguintes comandos ASA para fins de depuragem:

* Mostre a associação de segurança IPsec ou IKE (SA):
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Introduza o modo de depuragem:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    Os `debug` comandos podem gerar uma saída significativa na consola.

* Mostrar as configurações atuais no dispositivo:
    ```
    show run
    ```
    Utilize `show` subcomecos para listar partes específicas da configuração do dispositivo, por exemplo:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>Passos seguintes
Para configurar as instalações cruzadas ativas ativas e as ligações VNet-to-VNet, consulte as [portas VPN ativas ativas .](vpn-gateway-activeactive-rm-powershell.md)
