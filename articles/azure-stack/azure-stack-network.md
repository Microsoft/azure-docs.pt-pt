---
title: Considerações sobre a integração de sistemas integrados do Azure Stack de rede | Documentos da Microsoft
description: Saiba o que pode fazer para planear a integração de rede do Centro de dados com vários nós do Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 08/30/2018
ms.openlocfilehash: 3705b2dda7da8df2e6e3c98d5f6003bd3d771daf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58098581"
---
# <a name="network-connectivity"></a>Conectividade de rede
Este artigo fornece informações de infraestrutura de rede do Azure Stack para ajudar a decidir como integrar melhor o Azure Stack em seu ambiente de rede existente. 

> [!NOTE]
> Para resolver nomes DNS externos do Azure Stack (por exemplo, www\.bing.com), tem de fornecer servidores DNS para reencaminhar pedidos DNS. Para obter mais informações sobre os requisitos de DNS do Azure Stack, veja [integração no datacenter do Azure Stack - DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Design de rede física
A solução Azure Stack precisa de uma infraestrutura resiliente e altamente disponível para suportar o seu funcionamento e os seus serviços. Uplinks de ToR para comutadores de limite estão limitados a SFP + ou SFP28 suporte e 1 GB, 10 GB ou velocidades de 25 GB. Verifique junto do fornecedor de hardware do fabricante de equipamento original (OEM) de disponibilidade. O diagrama seguinte apresenta a nossa estrutura recomendada:

![Estrutura de rede recomendada do Azure Stack](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Redes Lógicas
Redes lógicas representam uma abstração da infraestrutura de rede física subjacente. São utilizados para organizar e simplificar atribuições de rede para anfitriões, máquinas virtuais e serviços. Como parte da criação de rede lógica, os sites de rede são criados para definir as redes locais virtuais (VLANs), sub-redes IP e pares de sub-rede/VLAN de IP que estão associados com a rede lógica em cada localização física.

A tabela seguinte mostra as redes lógicas e os intervalos de sub-rede de IPv4 associados, que terá de planear:

| Rede Lógica | Descrição | Tamanho | 
| -------- | ------------- | ------------ | 
| VIP público | O Azure Stack utiliza um total de 31 endereços desta rede. Oito endereços IP públicos são utilizados para um pequeno conjunto de serviços do Azure Stack, e o resto são utilizados por máquinas virtuais inquilinas. Se planeja usar o serviço de aplicações e os fornecedores de recursos do SQL, 7 mais endereços são utilizados. Os IPs de 15 restantes estão reservados para futuros serviços do Azure. | / 26 (62 anfitriões) - /22 (1022 anfitriões)<br><br>Recomendado = /24 (254 anfitriões) | 
| Infraestrutura de comutador | Endereços IP de ponto a ponto para fins de encaminhamento, dedicados mudam interfaces de gerenciamento e os endereços de loopback atribuídos ao comutador. | /26 | 
| Infraestrutura | Utilizado para componentes internos do Azure Stack para comunicar. | /24 |
| Privado | Utilizado para a rede de armazenamento e VIPs privadas. | /24 | 
| BMC | Utilizado para comunicar com BMCs em hosts físicos. | /26 | 
| | | |

## <a name="network-infrastructure"></a>Infraestrutura de rede
A infraestrutura de rede para o Azure Stack é composta por várias redes lógicas que estão configuradas nos comutadores. O diagrama seguinte mostra essas redes lógicas e como integrar com a top-of-rack (TOR), controlador de gestão da placa base (BMC) e comutadores de (rede de cliente) dos limites.

![Ligações de diagrama e o comutador de rede lógica](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>Rede BMC
Esta rede se destina a todos os controladores (também conhecido como serviço processadores, por exemplo, iDRAC, iLO, iBMC, etc.) a ligar à rede de gestão. Se estiver presente, o anfitrião de ciclo de vida do Hardware (HLH) está localizada nesta rede e pode fornecer o software de OEM específico para a manutenção de hardware ou de monitorização. 

O HLH aloja também a VM de implementação (DVM). O DVM é utilizada durante a implementação do Azure Stack e será removido quando a implementação estiver concluída. O DVM requer acesso à internet, nos cenários de implementação conectados para testar, validar e aceder a vários componentes. Esses componentes podem ser dentro e fora da sua rede empresarial; Por exemplo, NTP, DNS e o Azure. Para obter mais informações sobre os requisitos de conectividade, consulte a [secção NAT na integração da firewall do Azure Stack](azure-stack-firewall.md#network-address-translation). 

### <a name="private-network"></a>Rede privada
Este /24 (anfitrião 254 IPs) é privada para a região do Azure Stack (não expande para além dos dispositivos de comutador de limite da região do Azure Stack) de rede e é dividido em duas sub-redes:

- **Rede de armazenamento**. Um /25 (anfitrião de 126 IPs) utilizado para suportar a utilização de tráfego de armazenamento de espaços direto e o bloco de mensagem de servidor (SMB) e a migração em direto da máquina virtual de rede. 
- **Rede IP virtual interna**. R/25 rede dedicadas para apenas internos VIPs para o Balanceador de carga de software.

### <a name="azure-stack-infrastructure-network"></a>Rede de infraestrutura do Azure Stack
Isso/rede 24 é dedicada a componentes internos do Azure Stack para que eles podem se comunicar e trocar dados entre si. Esta sub-rede necessita de endereços IP encaminháveis, mas é mantida privada para a solução ao utilizar listas de controlo de acesso (ACL). Ele não se espera que ser encaminhado para além dos comutadores de limite, exceto para uma pequena gama equivalente em tamanho/27 rede utilizada por alguns desses serviços, ao necessitem de acesso a recursos externos e/ou da internet. 

### <a name="public-vip-network"></a>Rede VIP pública
A rede VIP público é atribuída para o controlador de rede no Azure Stack. Não é uma rede lógica no comutador. O SLB utiliza o conjunto de endereços e atribui/32 redes para cargas de trabalho de inquilino. Na tabela de encaminhamento de comutador, estes IPs 32 são anunciados como uma rota disponível através do BGP. Esta rede contém os endereços IP externo acessível ou públicos. A infraestrutura do Azure Stack reserva os primeiros 31 endereços desta rede VIP pública, enquanto o restante é utilizado pelas VMs inquilino. O tamanho de rede nesta sub-rede pode abranger desde um mínimo de /26 (64 anfitriões) até um máximo de /22 (1022 anfitriões), recomendamos que considere uma /24 rede.

### <a name="switch-infrastructure-network"></a>Rede de infraestrutura do comutador
Isso/26 de rede é a sub-rede que contém as sub-redes de (IPs de anfitrião 2) / de 30 de IP de ponto a ponto encaminháveis e loopbacks, dedicadas/32 sub-redes para em banda mudar a gestão e o ID de router BGP. Este intervalo de endereços IP tem de ser encaminháveis externamente da solução do Azure Stack para o seu datacenter, eles podem estar com IPs privado ou público.

### <a name="switch-management-network"></a>Rede de gestão de comutador
Este /29 (anfitrião de 6 IPs) dedicada para ligar as portas de gestão dos comutadores de rede. Ele permite o acesso de fora de banda para a implantação, gerenciamento e resolução de problemas. É calculada a partir da rede de infraestrutura de comutador mencionada acima.

## <a name="publish-azure-stack-services"></a>Publicar serviços do Azure Stack
Terá de disponibilizar serviços do Azure Stack para os usuários de fora do Azure Stack. O Azure Stack configura vários pontos de extremidade para as respetivas funções de infraestrutura. Estes pontos finais são atribuídos VIPs do conjunto de endereços IP público. Uma entrada DNS é criada para cada ponto de extremidade na zona DNS externo, que foi especificado no momento da implementação. Por exemplo, o portal de utilizador é atribuído a entrada de anfitrião DNS do portal.  *&lt;região >.&lt; FQDN >*.

### <a name="ports-and-urls"></a>Portas e URLs
Para tornar os serviços do Azure Stack (por exemplo, portais do Azure Resource Manager, DNS, etc.) disponível para redes externas, tem de permitir tráfego de entrada para estes pontos finais para URLs, portas e protocolos específicos.
 
Numa implementação em que um uplinks de proxy transparente para um servidor proxy tradicional, tem de permitir URLs e portas específicas para ambos [entrada](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound) e [saída](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound) comunicação. Estes incluem portas e URLs para os dados de identidade, o marketplace, patch e atualização, registro e utilização.

## <a name="next-steps"></a>Passos Seguintes
[Conectividade de limite](azure-stack-border-connectivity.md)
