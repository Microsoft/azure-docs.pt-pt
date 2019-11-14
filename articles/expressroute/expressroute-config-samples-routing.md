---
title: 'Azure ExpressRoute: exemplos de configuração do roteador'
description: Esta página fornece exemplos de configuração de router para os routers Cisco e Juniper.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.openlocfilehash: 2c37dadeb669fb88f858b5487379828a8dddec6c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076669"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Exemplos de configuração de router para configurar e gerir o encaminhamento
Esta página fornece interface e exemplos de configuração de encaminhamento para Cisco IOS-XE e os routers da série de Juniper MX ao trabalhar com o ExpressRoute. Estes destinam-se para ser amostras para obter orientações apenas e não pode ser usados como está. Pode trabalhar com o fornecedor para propor as configurações apropriadas para a sua rede. 

> [!IMPORTANT]
> Exemplos nesta página se destinam a ser puramente para obter orientações. Deve trabalhar com equipes de técnicos e de vendas do seu fornecedor e seu funcionamento em rede propor configurações adequadas às suas necessidades. A Microsoft irá suporta problemas relacionados com as configurações listadas nesta página. Tem de contactar o seu fornecedor de dispositivo para problemas de suporte.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Definições de MTU e TCP MSS em interfaces de router
* O MTU da interface do ExpressRoute é 1500, que é o padrão típico MTU para uma interface de Ethernet num roteador. A menos que seu roteador tem um MTU diferente, por predefinição, não há necessidade de especificar um valor na interface de router.
* Ao contrário de um Gateway de VPN do Azure, o MSS de TCP para um circuito do ExpressRoute não precisa de ser especificado.

Exemplos de configuração de router abaixo se aplicam a todos os peerings. Revisão [peerings do ExpressRoute](expressroute-circuit-peerings.md) e [requisitos de encaminhamento do ExpressRoute](expressroute-routing.md) para obter mais detalhes sobre o encaminhamento.


## <a name="cisco-ios-xe-based-routers"></a>IOS-XE da Cisco com routers base
Os exemplos nesta secção aplicam-se para qualquer router com a família de SO IOS XE.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configurando interfaces e subinterfaces
Vai precisar de uma interface de sub-rotina por peering em cada router que se liga à Microsoft. Uma interface de sub-rotina pode ser identificada com um ID de VLAN ou um par empilhado de IDs de VLAN e um endereço IP.

**Definição de interface de Dot1Q**

Este exemplo fornece a definição de interface secundária para uma interface secundárias com um único ID de VLAN. O ID de VLAN é exclusivo por peering. O último octeto do seu endereço IPv4 será sempre um número ímpar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Definição de interface de QinQ**

Este exemplo fornece a definição de interface secundária para uma interface de rede secundária com dois IDs de VLAN. O ID de VLAN externo (s-marca), se for utilizado permanece igual em todos os peerings. O ID de VLAN (c-etiqueta) interna é exclusivo por peering. O último octeto do seu endereço IPv4 será sempre um número ímpar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. Configurando sessões eBGP
Tem de configurar uma sessão de BGP com a Microsoft para cada peering. O exemplo abaixo permite-lhe configurar uma sessão BGP com a Microsoft. Se o endereço de IPv4 que utilizou para a sua interface de sub-rotina a.b.c. d, o endereço IP do vizinhos de BGP (Microsoft) será a.b.c.d+1. O último octeto de endereço IPv4 dos vizinhos de BGP será sempre um número par.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Configurando prefixos a serem anunciados na sessão BGP
Pode configurar o router para anunciar prefixos selecionados à Microsoft. Pode fazê-lo com o exemplo abaixo.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. mapas de rota
Pode utilizar mapas de rotas e apresenta uma lista de prefixo para prefixos de filtro propagados na sua rede. Pode utilizar o exemplo abaixo para realizar a tarefa. Certifique-se de que tem de configurar de listas de prefixo adequado.

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


## <a name="juniper-mx-series-routers"></a>Routers da série de Juniper MX
Os exemplos nesta secção aplicam-se todos os routers série Juniper MX.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configurando interfaces e subinterfaces

**Definição de interface de Dot1Q**

Este exemplo fornece a definição de interface secundária para uma interface secundárias com um único ID de VLAN. O ID de VLAN é exclusivo por peering. O último octeto do seu endereço IPv4 será sempre um número ímpar.

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


**Definição de interface de QinQ**

Este exemplo fornece a definição de interface secundária para uma interface de rede secundária com dois IDs de VLAN. O ID de VLAN externo (s-marca), se for utilizado permanece igual em todos os peerings. O ID de VLAN (c-etiqueta) interna é exclusivo por peering. O último octeto do seu endereço IPv4 será sempre um número ímpar.

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

### <a name="2-setting-up-ebgp-sessions"></a>2. Configurando sessões eBGP
Tem de configurar uma sessão de BGP com a Microsoft para cada peering. O exemplo abaixo permite-lhe configurar uma sessão BGP com a Microsoft. Se o endereço de IPv4 que utilizou para a sua interface de sub-rotina a.b.c. d, o endereço IP do vizinhos de BGP (Microsoft) será a.b.c.d+1. O último octeto de endereço IPv4 dos vizinhos de BGP será sempre um número par.

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

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Configurando prefixos a serem anunciados na sessão BGP
Pode configurar o router para anunciar prefixos selecionados à Microsoft. Pode fazê-lo com o exemplo abaixo.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. mapas de rota
Pode utilizar mapas de rotas e apresenta uma lista de prefixo para prefixos de filtro propagados na sua rede. Pode utilizar o exemplo abaixo para realizar a tarefa. Certifique-se de que tem de configurar de listas de prefixo adequado.

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
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>Passos Seguintes
Veja [FAQ do ExpressRoute](expressroute-faqs.md) para obter mais detalhes.

