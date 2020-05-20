---
title: 'Azure ExpressRoute: Amostras de configuração do router'
description: Esta página fornece amostras de config router para routers Cisco e Juniper.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: 6aa66ddc52665c22310fb58977fd516eea4e806a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651985"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Amostras de configuração do router para configurar e gerir o encaminhamento
Esta página fornece amostras de configuração de interface e encaminhamento para routers da série Cisco IOS-XE e Juniper MX quando estiver a trabalhar com o Azure ExpressRoute.

> [!IMPORTANT]
> As amostras desta página são puramente para orientação. Tem de trabalhar com a equipa técnica/de vendas do seu fornecedor e com a sua equipa de networking para encontrar configurações adequadas para atender às suas necessidades. A Microsoft não suportará problemas relacionados com configurações listadas nesta página. Contacte o fornecedor do seu dispositivo para obter problemas de suporte.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Definições mTU e TCP MSS nas interfaces do router
A unidade de transmissão máxima (MTU) para a interface ExpressRoute é de 1500, que é o MTU padrão típico para uma interface Ethernet num router. A menos que o seu router tenha um MTU diferente por padrão, não há necessidade de especificar um valor na interface do router.

Ao contrário de um gateway Azure VPN, o tamanho máximo do segmento TCP (MSS) para um circuito ExpressRoute não precisa de ser especificado.

As amostras de configuração do router neste artigo aplicam-se a todos os pares. Reveja [os pares expressRoute](expressroute-circuit-peerings.md) e os requisitos de [encaminhamento expressRoute](expressroute-routing.md) para mais detalhes sobre o encaminhamento.


## <a name="cisco-ios-xe-based-routers"></a>Routers baseados cisco IOS-XE
As amostras desta secção aplicam-se a qualquer router que execute a família IOS-XE OS.

### <a name="configure-interfaces-and-subinterfaces"></a>Configure interfaces e subinterfaces
Vai precisar de uma subinterface por olho em cada router que se conectem à Microsoft. Um subinterface pode ser identificado com um ID VLAN ou um par empilhado de IDs VLAN e um endereço IP.

**Definição de interface do T1Q**

Esta amostra fornece a definição de subinterface para uma subinterface com um único ID VLAN. O ID VLAN é único por observação. O último octeto do seu endereço IPv4 será sempre um número ímpar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Definição de interface QinQ**

Esta amostra fornece a definição de subinterface para uma subinterface com dois IDs VLAN. O ID VLAN exterior (s-tag), se utilizado, permanece o mesmo em todos os pares. O ID VLAN interior (c-tag) é único por observação. O último octeto do seu endereço IPv4 será sempre um número ímpar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="set-up-ebgp-sessions"></a>Configurar sessões de eBGP
Tem de preparar uma sessão de BGP com a Microsoft para cada espreitante. Instale uma sessão de BGP utilizando a seguinte amostra. Se o endereço IPv4 que usou para o seu subinterface fosse a.b.c.d,então o endereço IP do vizinho BGP (Microsoft) será a.b.c.d+1. O último octeto do endereço IPv4 do vizinho bGP será sempre um número par.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Configurar prefixos a serem anunciados durante a sessão do BGP
Configure o seu router para anunciar prefixos selecionados para a Microsoft utilizando a seguinte amostra.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="route-maps"></a>Mapas de rotas
Utilize mapas de rotas e listas de prefixos para filtrar prefixos propagados na sua rede. Consulte a amostra seguinte e certifique-se de que tem as listas de prefixo apropriadas configuradas.

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

### <a name="configure-bfd"></a>Configure BFD

Você vai configurar BFD em dois lugares: um ao nível da interface e outro a nível bGP. O exemplo aqui é para a interface QinQ. 

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


## <a name="juniper-mx-series-routers"></a>Routers da série Juniper MX
As amostras desta secção aplicam-se a qualquer router da série Juniper MX.

### <a name="configure-interfaces-and-subinterfaces"></a>Configure interfaces e subinterfaces

**Definição de interface do T1Q**

Esta amostra fornece a definição de subinterface para uma subinterface com um único ID VLAN. O ID VLAN é único por observação. O último octeto do seu endereço IPv4 será sempre um número ímpar.

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

Esta amostra fornece a definição de subinterface para uma subinterface com dois IDs VLAN. O ID VLAN exterior (s-tag), se utilizado, permanece o mesmo em todos os pares. O ID VLAN interior (c-tag) é único por observação. O último octeto do seu endereço IPv4 será sempre um número ímpar.

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

### <a name="set-up-ebgp-sessions"></a>Configurar sessões de eBGP
Tem de preparar uma sessão de BGP com a Microsoft para cada espreitante. Instale uma sessão de BGP utilizando a seguinte amostra. Se o endereço IPv4 que usou para o seu subinterface fosse a.b.c.d,então o endereço IP do vizinho BGP (Microsoft) será a.b.c.d+1. O último octeto do endereço IPv4 do vizinho bGP será sempre um número par.

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

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Configurar prefixos a serem anunciados durante a sessão do BGP
Configure o seu router para anunciar prefixos selecionados para a Microsoft utilizando a seguinte amostra.

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


### <a name="route-policies"></a>Políticas de rotas
Pode utilizar mapas de rotas e listas de prefixos para filtrar prefixos propagados na sua rede. Consulte a amostra seguinte e certifique-se de que tem as listas de prefixo apropriadas configuradas.

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

### <a name="configure-bfd"></a>Configure BFD
Configure o BFD apenas no âmbito da secção bGP do protocolo.

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

### <a name="configure-macsec"></a>Configure MACSec
Para a configuração MACSec, a Chave da Associação de Conectividade (CAK) e o Nome chave da Associação de Conectividade (CKN) devem coincidir com valores configurados através de comandos PowerShell.

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

## <a name="next-steps"></a>Passos seguintes
Veja [FAQ do ExpressRoute](expressroute-faqs.md) para obter mais detalhes.



