---
title: 'Azure ExpressRoute: exemplos de configuração de router'
description: Utilize estas amostras de configuração de interface e encaminhamento para os routers da série Cisco IOS-XE e Juniper MX como exemplos para trabalhar com o Azure ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: duau
ms.openlocfilehash: 3bc850f02884ae0547c2ecf56a46a57a4e66a752
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89397411"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Amostras de configuração do router para configurar e gerir o encaminhamento
Esta página fornece amostras de configuração de interface e encaminhamento para routers da série Cisco IOS-XE e Juniper MX quando estiver a trabalhar com o Azure ExpressRoute.

> [!IMPORTANT]
> As amostras desta página são puramente para orientação. Tem de trabalhar com a equipa técnica/vendas do seu fornecedor e com a sua equipa de networking para encontrar configurações adequadas para atender às suas necessidades. A Microsoft não suporta problemas relacionados com as configurações listadas nesta página. Contacte o seu fornecedor de dispositivos para obter problemas de suporte.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Definições DE MSS MTU e TCP nas interfaces do router
A unidade de transmissão máxima (MTU) para a interface ExpressRoute é 1500, que é o MTU típico padrão para uma interface Ethernet num router. A menos que o seu router tenha um MTU diferente por padrão, não há necessidade de especificar um valor na interface do router.

Ao contrário de um gateway Azure VPN, o tamanho máximo do segmento TCP (MSS) para um circuito ExpressRoute não precisa de ser especificado.

As amostras de configuração do router neste artigo aplicam-se a todos os seus pares. Reveja [os requisitos de encaminhamento ExpressRoute](expressroute-circuit-peerings.md) e [ExpressRoute](expressroute-routing.md) para obter mais detalhes sobre o encaminhamento.


## <a name="cisco-ios-xe-based-routers"></a>Routers baseados em Cisco IOS-XE
As amostras desta secção aplicam-se a qualquer router que coma a família IOS-XE OS.

### <a name="configure-interfaces-and-subinterfaces"></a>Configure interfaces e subinterfácios
Vai precisar de um subinterface por espreitar em todos os routers que ligar à Microsoft. Um subinterface pode ser identificado com um ID VLAN ou um par empilhado de IDs VLAN e um endereço IP.

**Definição de interface dot1Q**

Esta amostra fornece a definição de subinterface para um subinterfácio com um único ID VLAN. O ID VLAN é único por espreitar. O último octeto do seu endereço IPv4 será sempre um número ímpar.

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <VLAN_ID>
 ip address <IPv4_Address><Subnet_Mask>
```

**Definição de interface QinQ**

Esta amostra fornece a definição de subinterface para um subinterfácio com dois IDs VLAN. O ID VLAN exterior (s-tag), se usado, permanece o mesmo em todos os seus olhos. O ID VLAN interior (c-tag) é único por espreitar. O último octeto do seu endereço IPv4 será sempre um número ímpar.

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
 ip address <IPv4_Address><Subnet_Mask>
```

### <a name="set-up-ebgp-sessions"></a>Configurar sessões eBGP
Tem de marcar uma sessão de BGP com a Microsoft para cada um dos seus olhos. Configurar uma sessão de BGP utilizando a seguinte amostra. Se o endereço IPv4 que usou para o seu subinterface fosse a.b.c.d., então o endereço IP do vizinho BGP (Microsoft) será a.b.c.d+1. O último octeto do endereço IPv4 do vizinho BGP será sempre um número par.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
 neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Configurar prefixos a serem anunciados durante a sessão de BGP
Configure o seu router para anunciar prefixos selecionados para a Microsoft utilizando a seguinte amostra.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  network <Prefix_to_be_advertised> mask <Subnet_mask>
  neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="route-maps"></a>Mapas de rotas
Utilize mapas de rotas e listas de prefixos para filtrar prefixos propagados na sua rede. Consulte a amostra a seguir e certifique-se de que tem as listas de prefixos apropriadas configuradas.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  network <Prefix_to_be_advertised> mask <Subnet_mask>
  neighbor <IP#2_used_by_Azure> activate
  neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
 exit-address-family
!
route-map <MS_Prefixes_Inbound> permit 10
 match ip address prefix-list <MS_Prefixes>
!
```

### <a name="configure-bfd"></a>Configure BFD

Você vai configurar BFD em dois lugares: um ao nível da interface e outro ao nível BGP. O exemplo aqui é para a interface QinQ. 

```console
interface GigabitEthernet<Interface_Number>.<Number>
 bfd interval 300 min_rx 300 multiplier 3
 encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
 ip address <IPv4_Address><Subnet_Mask>

router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  neighbor <IP#2_used_by_Azure> activate
  neighbor <IP#2_used_by_Azure> fall-over bfd
 exit-address-family
!
```


## <a name="juniper-mx-series-routers"></a>Routers da série Juniper MX
As amostras desta secção aplicam-se a qualquer router da série Juniper MX.

### <a name="configure-interfaces-and-subinterfaces"></a>Configure interfaces e subinterfácios

**Definição de interface dot1Q**

Esta amostra fornece a definição de subinterface para um subinterfácio com um único ID VLAN. O ID VLAN é único por espreitar. O último octeto do seu endereço IPv4 será sempre um número ímpar.

```console
    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }
```


**Definição de interface QinQ**

Esta amostra fornece a definição de subinterface para um subinterfácio com dois IDs VLAN. O ID VLAN exterior (s-tag), se usado, permanece o mesmo em todos os seus olhos. O ID VLAN interior (c-tag) é único por espreitar. O último octeto do seu endereço IPv4 será sempre um número ímpar.

```console
    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           
```

### <a name="set-up-ebgp-sessions"></a>Configurar sessões eBGP
Tem de marcar uma sessão de BGP com a Microsoft para cada um dos seus olhos. Configurar uma sessão de BGP utilizando a seguinte amostra. Se o endereço IPv4 que usou para o seu subinterface fosse a.b.c.d., então o endereço IP do vizinho BGP (Microsoft) será a.b.c.d+1. O último octeto do endereço IPv4 do vizinho BGP será sempre um número par.

```console
    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Configurar prefixos a serem anunciados durante a sessão de BGP
Configure o seu router para anunciar prefixos selecionados para a Microsoft utilizando a seguinte amostra.

```console
    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
                route-filter; 
                <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="route-policies"></a>Políticas de rotas
Pode utilizar mapas de rotas e listas de prefixos para filtrar prefixos propagados na sua rede. Consulte a amostra a seguir e certifique-se de que tem as listas de prefixos apropriadas configuradas.

```console
    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
                    prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>;
                import <MS_Prefixes_Inbound>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="configure-bfd"></a>Configure BFD
Configure o BFD apenas ao abrigo da secção de BGP protocolar.

```console
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
                bfd-liveness-detection {
                       minimum-interval 3000;
                       multiplier 3;
                }
            }                               
        }                                   
    }
```

### <a name="configure-macsec"></a>Configure MACSec
Para a configuração MACSec, a chave da Associação de Conectividade (CAK) e o Nome chave da Associação de Conectividade (CKN) devem coincidir com valores configurados através dos comandos PowerShell.

```console
    security {
        macsec {
            connectivity-association <Connectivity_Association_Name> {
                cipher-suite gcm-aes-xpn-128;
                security-mode static-cak;
                pre-shared-key {
                    ckn <Connectivity_Association_Key_Name>;
                    cak <Connectivity_Association_Key>; ## SECRET-DATA
                }
            }
            interfaces {
                <Interface_Number> {
                    connectivity-association <Connectivity_Association_Name>;
                }
            }
        }
    }
```

## <a name="next-steps"></a>Passos seguintes
Veja [FAQ do ExpressRoute](expressroute-faqs.md) para obter mais detalhes.



