---
title: Exemplo de configuração para conectar dispositivos Cisco ASA a gateways de VPN do Azure
description: Este artigo fornece um exemplo de configuração para conectar dispositivos Cisco ASA a gateways de VPN do Azure.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/19/2018
ms.author: yushwang
ms.openlocfilehash: 96e5c26ea7b5f1baa33fd8830491ee3aa1e60221
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75778087"
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Exemplo de configuração: dispositivo Cisco ASA (IKEv2/sem BGP)
Este artigo fornece exemplos de configurações para conectar dispositivos Cisco Adaptive Security Appliance (ASA) a gateways de VPN do Azure. O exemplo se aplica aos dispositivos Cisco ASA que estão executando IKEv2 sem o Border Gateway Protocol (BGP). 

## <a name="device-at-a-glance"></a>Visão geral do dispositivo

|                        |                                   |
| ---                    | ---                               |
| Fornecedor do dispositivo          | Cisco                             |
| Modelo do dispositivo           | ASA                               |
| Versão de destino         | 8,4 e posterior                     |
| Modelo testado           | ASA 5505                          |
| Versão testada         | 9.2                               |
| Versão do IKE            | IKEv2                             |
| BGP                    | Não                                |
| Tipo de gateway de VPN do Azure | Gateway de VPN baseado em rota           |
|                        |                                   |

> [!NOTE]
> A configuração de exemplo conecta um dispositivo Cisco ASA a um gateway **de VPN baseado em rota** do Azure. A conexão usa uma política de IPsec/IKE personalizada com a opção **UsePolicyBasedTrafficSelectors** , conforme descrito neste [artigo](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> O exemplo requer que os dispositivos ASA usem a política **IKEv2** com configurações baseadas na lista de acesso, não com base em VTI. Consulte as especificações do fornecedor do dispositivo VPN para verificar se há suporte para a política IKEv2 em seus dispositivos VPN locais.


## <a name="vpn-device-requirements"></a>Requisitos do dispositivo VPN
Os gateways de VPN do Azure usam os pacotes de protocolo IPsec/IKE padrão para estabelecer túneis VPN S2S (site a site). Para obter os parâmetros de protocolo IPsec/IKE detalhados e os algoritmos criptográficos padrão para gateways de VPN do Azure, consulte [sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md).

> [!NOTE]
> Opcionalmente, você pode especificar uma combinação exata de algoritmos criptográficos e forças de chave para uma conexão específica, conforme descrito em [sobre os requisitos de criptografia](vpn-gateway-about-compliance-crypto.md). Se você especificar uma combinação exata de algoritmos e forças de chave, certifique-se de usar as especificações correspondentes em seus dispositivos VPN.

## <a name="single-vpn-tunnel"></a>Túnel de VPN único
Essa configuração consiste em um único túnel VPN S2S entre um gateway de VPN do Azure e um dispositivo VPN local. Opcionalmente, você pode configurar o BGP por meio do túnel VPN.

![Túnel VPN S2S único](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Para obter instruções detalhadas sobre como criar as configurações do Azure, consulte [instalação de túnel de VPN único](vpn-gateway-3rdparty-device-config-overview.md#singletunnel).

### <a name="virtual-network-and-vpn-gateway-information"></a>Informações de rede virtual e gateway de VPN
Esta seção lista os parâmetros para o exemplo.

| **Parâmetro**                | **Valor**                    |
| ---                          | ---                          |
| Prefixos de endereço de rede virtual        | 10.11.0.0/16<br>10.12.0.0/16 |
| IP do gateway de VPN do Azure         | Azure_Gateway_Public_IP      |
| Prefixos de endereço local | 10.51.0.0/16<br>10.52.0.0/16 |
| IP do dispositivo VPN local    | OnPrem_Device_Public_IP     |
| * ASN de BGP de rede virtual                | 65010                        |
| * IP do par do Azure BGP           | 10.12.255.30                 |
| * ASN do BGP local         | 65050                        |
| * IP do par de BGP local     | 10.52.255.254                |
|                              |                              |

\* parâmetro opcional somente para BGP.

### <a name="ipsecike-policy-and-parameters"></a>Política e parâmetros de IPsec/IKE
A tabela a seguir lista os algoritmos e parâmetros de IPsec/IKE que são usados no exemplo. Consulte suas especificações de dispositivo VPN para verificar os algoritmos que têm suporte para seus modelos de dispositivo VPN e versões de firmware.

| **IPsec/IKEv2**  | **Valor**                            |
| ---              | ---                                  |
| Encriptação IKEv2 | AES256                               |
| Integridade do IKEv2  | SHA384                               |
| Grupo DH         | DHGroup24                            |
| * Criptografia IPsec | AES256                               |
| * Integridade do IPsec  | SHA1                                 |
| Grupo PFS        | PFS24                                |
| Duração de SA QM   | 7\.200 segundos                         |
| Seletor de tráfego | UsePolicyBasedTrafficSelectors $True |
| Chave Pré-partilhada   | PreSharedKey                         |
|                  |                                      |

\* em alguns dispositivos, a integridade do IPsec deverá ser um valor nulo quando o algoritmo de criptografia IPsec for AES-GCM.

### <a name="asa-device-support"></a>Suporte a dispositivo ASA

* O suporte para IKEv2 requer o ASA versão 8,4 e posterior.

* O suporte para grupo DH e grupo PFS além do grupo 5 requer o ASA versão 9. x.

* O suporte para criptografia IPsec com AES-GCM e integridade IPsec com SHA-256, SHA-384 ou SHA-512 requer o ASA versão 9. x. Esse requisito de suporte se aplica a dispositivos ASA mais recentes. No momento da publicação, os modelos ASA 5505, 5510, 5520, 5540, 5550 e 5580 não dão suporte a esses algoritmos. Consulte suas especificações de dispositivo VPN para verificar os algoritmos que têm suporte para seus modelos de dispositivo VPN e versões de firmware.


### <a name="sample-device-configuration"></a>Configuração de dispositivo de exemplo
O script fornece um exemplo baseado na configuração e nos parâmetros descritos nas seções anteriores. A configuração de túnel VPN S2S consiste nas seguintes partes:

1. Interfaces e rotas
2. Listas de acesso
3. Política e parâmetros de IKE (fase 1 ou modo principal)
4. Política e parâmetros IPsec (fase 2 ou modo rápido)
5. Outros parâmetros, como TCP MSS fixação MSS

> [!IMPORTANT]
> Conclua as etapas a seguir antes de usar o script de exemplo. Substitua os valores de espaço reservado no script pelas configurações do dispositivo para sua configuração.

* Especifique a configuração de interface para as interfaces interna e externa.
* Identifique as rotas para suas redes internas/privadas e externas/públicas.
* Verifique se todos os nomes e números de política são exclusivos em seu dispositivo.
* Verifique se os algoritmos criptográficos têm suporte no seu dispositivo.
* Substitua os seguintes **valores de espaço reservado** por valores reais para sua configuração:
  - Nome da interface externa: **fora**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE: **Pre_Shared_Key**
  - Nomes de gateway de rede local e rede virtual: **VNetName** e **LNGName**
  - **Prefixos** de endereço de rede virtual e rede local
  - **Máscaras** de netadequadas

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

## <a name="simple-debugging-commands"></a>Comandos de depuração simples

Use os comandos ASA a seguir para fins de depuração:

* Mostrar SA (Associação de segurança) de IPsec ou IKE:
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Insira o modo de depuração:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    Os comandos `debug` podem gerar uma saída significativa no console.

* Mostrar as configurações atuais no dispositivo:
    ```
    show run
    ```
    Use `show` subcomandos para listar partes específicas da configuração do dispositivo, por exemplo:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>Passos seguintes
Para configurar conexões entre locais e VNet para VNet do Active-Active, consulte [configurar gateways de VPN ativo-ativo](vpn-gateway-activeactive-rm-powershell.md).
