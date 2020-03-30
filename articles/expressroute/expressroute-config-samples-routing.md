---
title: 'Azure ExpressRoute: Amostras de configuração do router'
description: Esta página fornece amostras de config router para routers Cisco e Juniper.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.openlocfilehash: 2c37dadeb669fb88f858b5487379828a8dddec6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076669"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Amostras de configuração do router para configurar e gerir o encaminhamento
Esta página fornece amostras de configuração de interface e encaminhamento para routers da série Cisco IOS-XE e Juniper MX ao trabalhar com o ExpressRoute. Destinam-se a ser amostras apenas para orientação e não devem ser utilizadas como está. Pode trabalhar com o seu fornecedor para criar configurações adequadas para a sua rede. 

> [!IMPORTANT]
> As amostras nesta página destinam-se exclusivamente a orientação. Tem de trabalhar com a equipa técnica/de vendas do seu fornecedor e com a sua equipa de networking para criar configurações adequadas para atender às suas necessidades. A Microsoft não suportará problemas relacionados com configurações listadas nesta página. Deve contactar o fornecedor do seu dispositivo para obter problemas de suporte.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Definições mTU e TCP MSS nas interfaces do router
* O MTU para a interface ExpressRoute é 1500, que é o MTU padrão típico para uma interface Ethernet num router. A menos que o seu router tenha um MTU diferente por padrão, não há necessidade de especificar um valor na interface do router.
* Ao contrário de um Azure VPN Gateway, o TCP MSS para um circuito ExpressRoute não precisa de ser especificado.

As amostras de configuração do router abaixo aplicam-se a todos os pares. Reveja [os pares expressRoute](expressroute-circuit-peerings.md) e os requisitos de [encaminhamento expressRoute](expressroute-routing.md) para mais detalhes sobre o encaminhamento.


## <a name="cisco-ios-xe-based-routers"></a>Routers baseados cisco IOS-XE
As amostras desta secção aplicam-se a qualquer router que execute a família IOS-XE OS.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configurar interfaces e subinterfaces
Necessitará de uma subinterface por observação em todos os routers que ligar à Microsoft. Uma sub interface pode ser identificada com um ID VLAN ou um par empilhado de IDs VLAN e um endereço IP.

**Definição de interface do T1Q**

Esta amostra fornece a definição de sub-interface para uma sub-interface com um único ID VLAN. O ID VLAN é único por observação. O último octeto do seu endereço IPv4 será sempre um número ímpar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Definição de interface QinQ**

Esta amostra fornece a definição de sub-interface para uma sub-interface com duas IDs VLAN. O ID VLAN exterior (s-tag), se utilizado permanece o mesmo em todos os pares. O ID VLAN interior (c-tag) é único por observação. O último octeto do seu endereço IPv4 será sempre um número ímpar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. Criação de sessões de EBGP
Tem de preparar uma sessão de BGP com a Microsoft para cada espreitante. A amostra abaixo permite-lhe configurar uma sessão de BGP com a Microsoft. Se o endereço IPv4 que usou para a sua subinterface fosse a.b.c.d,o endereço IP do vizinho BGP (Microsoft) será a.b.c.d+1. O último octeto do endereço IPv4 do vizinho bGP será sempre um número par.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Criação de prefixos a anunciar durante a sessão do BGP
Pode configurar o seu router para anunciar prefixos selecionados para a Microsoft. Pode fazê-lo utilizando a amostra abaixo.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. Mapas de rotas
Pode utilizar mapas de rotas e listas de prefixos para filtrar prefixos propagados na sua rede. Pode utilizar a amostra abaixo para realizar a tarefa. Certifique-se de que tem as listas de pré-fixação adequadas configuradas.

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


## <a name="juniper-mx-series-routers"></a>Routers da série Juniper MX
As amostras desta secção aplicam-se a quaisquer routers da série Juniper MX.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configurar interfaces e subinterfaces

**Definição de interface do T1Q**

Esta amostra fornece a definição de sub-interface para uma sub-interface com um único ID VLAN. O ID VLAN é único por observação. O último octeto do seu endereço IPv4 será sempre um número ímpar.

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


**Definição de interface QinQ**

Esta amostra fornece a definição de sub-interface para uma sub-interface com duas IDs VLAN. O ID VLAN exterior (s-tag), se utilizado permanece o mesmo em todos os pares. O ID VLAN interior (c-tag) é único por observação. O último octeto do seu endereço IPv4 será sempre um número ímpar.

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

### <a name="2-setting-up-ebgp-sessions"></a>2. Criação de sessões de EBGP
Tem de preparar uma sessão de BGP com a Microsoft para cada espreitante. A amostra abaixo permite-lhe configurar uma sessão de BGP com a Microsoft. Se o endereço IPv4 que usou para a sua subinterface fosse a.b.c.d,o endereço IP do vizinho BGP (Microsoft) será a.b.c.d+1. O último octeto do endereço IPv4 do vizinho bGP será sempre um número par.

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

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Criação de prefixos a anunciar durante a sessão do BGP
Pode configurar o seu router para anunciar prefixos selecionados para a Microsoft. Pode fazê-lo utilizando a amostra abaixo.

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


### <a name="4-route-maps"></a>4. Mapas de rotas
Pode utilizar mapas de rotas e listas de prefixos para filtrar prefixos propagados na sua rede. Pode utilizar a amostra abaixo para realizar a tarefa. Certifique-se de que tem as listas de pré-fixação adequadas configuradas.

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

