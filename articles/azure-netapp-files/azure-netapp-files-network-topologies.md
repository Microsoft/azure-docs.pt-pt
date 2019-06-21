---
title: Planeamento de rede de diretrizes para ficheiros de NetApp do Azure | Documentos da Microsoft
description: Descreve diretrizes que podem ajudá-lo a criar uma arquitetura de rede eficientes através de ficheiros do Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: bf2262d8a222cec6c5d0d7e53ded7b2994481656
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205660"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Diretrizes para o planeamento de rede dos Azure NetApp Files

Planejamento da arquitetura de rede é um elemento-chave da criação de qualquer infraestrutura de aplicativo. Este artigo ajuda-o a criar uma arquitetura de rede em vigor a partir das cargas de trabalho beneficiar de capacidades avançadas de ficheiros do Azure NetApp.

Volumes de ficheiros NetApp do Azure foram concebidos para ser contidos numa sub-rede de finalidade especial chamado [delegado sub-rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) na sua rede Virtual do Azure. Por conseguinte, pode aceder os volumes diretamente a partir da sua VNet, de VNets em modo de peering na mesma região ou no local através de um Gateway de rede Virtual (ExpressRoute ou o Gateway de VPN), se necessário. A sub-rede é dedicada aos ficheiros do Azure NetApp e nenhuma conectividade a outros serviços do Azure ou à internet.

## <a name="considerations"></a>Considerações  

Deve compreender algumas considerações ao planejar para a rede de ficheiros do Azure NetApp.

### <a name="constraints"></a>Restrições

As funcionalidades indicadas abaixo não são atualmente suportadas para ficheiros de NetApp do Azure: 

* Grupos de segurança de rede (NSGs) na sub-rede
* Definido pelo utilizador rotas (UDRs) com o próximo salto como a sub-rede de ficheiros do Azure NetApp
* Políticas do Azure (por exemplo, políticas nomenclatura personalizadas) na interface de ficheiros do Azure NetApp
* Balanceadores de carga para tráfego de ficheiros do Azure NetApp

As seguintes restrições de rede aplicam-se aos ficheiros do Azure NetApp:

* O número de IPs em utilização numa VNet com ficheiros de NetApp do Azure (incluindo VNets em modo de peering) não pode ter mais de 1000.
* Cada rede Virtual do Azure (VNet), apenas uma sub-rede pode ser delegada a ficheiros do Azure NetApp.


### <a name="supported-network-topologies"></a>Topologias de rede suportados

A tabela seguinte descreve as topologias de rede suportadas pelo Azure NetApp ficheiros.  Também descreve as soluções alternativas para as topologias não suportadas. 

|    Topologias    |    é suportada    |     Solução    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Conectividade ao volume numa local VNet    |    Sim    |         |
|    Conectividade ao volume numa VNet em modo de peering (mesma região)    |    Sim    |         |
|    Conectividade ao volume numa VNet em modo de peering (cruzada região ou o global peering)    |    Não    |    Nenhuma    |
|    Conectividade para um volume ao longo do gateway do ExpressRoute    |    Sim    |         |
|    Conectividade no local para um volume num spoke VNet ao longo do gateway do ExpressRoute e de VNet peering com trânsito de gateway    |    Não    |    Criar uma sub-rede de delegado na VNet (VNet do Azure com o Gateway) do hub    |
|    Conectividade no local para um volume num spoke VNet ao longo do gateway de VPN    |    Sim    |         |
|    Conectividade no local para um volume num spoke VNet ao longo do gateway de VPN e VNet peering com trânsito de gateway    |    Sim    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Rede virtual para volumes de ficheiros do Azure NetApp

Esta secção explica os conceitos que o ajudam a planos de rede virtual.

### <a name="azure-virtual-networks"></a>Redes virtuais do Azure

Antes de aprovisionar um volume de ficheiros do Azure NetApp, terá de criar uma rede virtual do Azure (VNet) ou utilizar um já existente na sua subscrição. A VNet define o limite de rede do volume.  Para obter mais informações sobre como criar redes virtuais, consulte a [documentação de rede Virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="subnets"></a>Sub-redes

Sub-redes segmentar a rede virtual em espaços de endereço separados que podem ser usados pelos recursos do Azure nos mesmos.  Volumes de ficheiros NetApp do Azure estão contidos numa sub-rede de finalidade especial chamado [delegado sub-rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet). 

Delegação de sub-rede dá permissões explícitas para o serviço de ficheiros de NetApp do Azure para criar recursos específicos do serviço na sub-rede.  Ele usa um identificador exclusivo na implementar o serviço. Neste caso, é criada uma interface de rede para permitir a conectividade aos ficheiros do Azure NetApp.

Se utilizar uma nova VNet, pode criar uma sub-rede e delegar a sub-rede para ficheiros de NetApp do Azure ao seguir as instruções em [delegar uma sub-rede para ficheiros do Azure NetApp](azure-netapp-files-delegate-subnet.md). Também pode delegar uma subrede vazia existente que já não é delegada para outros serviços.

Se a VNet é executado o peering com outra VNet, não é possível expandir o espaço de endereços da VNet. Por esse motivo, a nova sub-rede delegada tem de ser criada dentro do espaço de endereços da VNet. Se tiver de expandir o espaço de endereços, tem de eliminar o VNet peering antes de expandir o espaço de endereços.

### <a name="udrs-and-nsgs"></a>As UDRs e NSGs

Grupos de segurança de rede (NSGs) com um salto seguinte não podem ser utilizados como sub-redes delegadas para os ficheiros do Azure NetApp. Da mesma forma, as rotas definidas pelo utilizador (UDRs) também não são suportadas. 

Como solução, pode aplicar NSGs a outras sub-redes que permitem ou negam o tráfego de e para a sub-rede de ficheiros do Azure NetApp delegada.  

## <a name="azure-native-environments"></a>Ambientes nativos do Azure

O diagrama seguinte ilustra um ambiente nativo do Azure:

![Ambiente de rede do Azure nativo](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Local VNet

É um cenário básico para criar ou ligar a um volume de ficheiros de NetApp do Azure a partir de uma máquina virtual (VM) na mesma VNet. Para a VNet 2 no diagrama acima, o Volume 1 é criado numa sub-rede delegada e podem ser montadas em 1 da VM na sub-rede de predefinição.

### <a name="vnet-peering"></a>VNet peering

Se tiver adicionais VNets na mesma região que precisam de aceder a recursos uns dos outros, as VNets podem ser ligadas usando [VNet peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) para permitir a conectividade segura através da infraestrutura do Azure. 

Considere VNet 2 e 3 de VNet no diagrama acima. Se precisar de 1 da VM ligar à VM 2 e o Volume 2, ou se precisar de 2 de VM ligar a 1 da VM ou Volume 1, terá de ativar o VNet peering entre a VNet 2 e 3 de VNet. 

Além disso, considere um cenário em que VNet 1 está agrupada com VNet 2 e VNet 2 está agrupada com 3 VNet na mesma região. Os recursos de 1 de VNet podem ligar a recursos na VNet 2, mas não é possível ligar a recursos na VNet 3, a menos que a VNet 1 e 3 de VNet em modo de peering. 

No diagrama acima, embora 3 de VM pode ligar ao Volume 1, 4 de VM não é possível ligar ao Volume 2.  A razão disso é que as VNets spoke não em modo de peering, e _encaminhamento de tráfego não é suportado através do peering de VNet_.

## <a name="hybrid-environments"></a>Ambientes híbridos

O diagrama seguinte ilustra um ambiente híbrido: 

![Ambiente de rede híbrida](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

No cenário híbrido, aplicações a partir de centros de dados no local precisam de aceder aos recursos no Azure.  Esse é o caso, se deseja estender seu centro de dados para o Azure ou que pretende utilizar serviços nativos do Azure ou para recuperação após desastre. Ver [opções de planeamento de Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) sobre como ligar a vários recursos no local para recursos no Azure através de uma VPN de site a site ou do ExpressRoute.

Numa topologia hub-and-spoke híbrida, a VNet no Azure do hub atua como um ponto central de conectividade à sua rede no local. Os spokes são VNets em modo de peering com o hub e podem ser utilizadas para isolar cargas de trabalho.

Dependendo da configuração. Pode ligar recursos no local para recursos do hub e os spokes.

Na topologia ilustrada acima, a rede no local está ligada a um concentrador de VNet no Azure e, existem 2 spoke VNets na mesma região em modo de peering com a VNet do hub.  Neste cenário, as opções de conectividade suportadas para volumes de ficheiros do Azure NetApp são os seguintes:

* Recursos no local VM 1 e 2 de VM podem ligar ao Volume 1 no hub através de um site-site VPN ou Expressroute. 
* Recursos no local VM 1 e 2 de VM podem ligar ao Volume 2 ou 3 de Volume através de uma VPN site a site e o peering de Vnet regional.
* 3 de VM no hub VNet pode ligar ao volume 2 no spoke VNet 1 e 3 de Volume no spoke VNet 2.
* 4 de VM do spoke VNet 1 e 5 de VM do spoke VNet 2 podem ligar ao Volume 1 na VNet do hub.

4 de VM no spoke VNet 1 não é possível ligar ao Volume 3 no spoke VNet 2. Além disso, 5 de VM no spoke VNet2 não consegue ligar ao Volume 2 no spoke 1 da VNet. Esse é o caso, uma vez que as VNets spoke não em modo de peering e _encaminhamento de tráfego não é suportado através do peering de VNet_.

## <a name="next-steps"></a>Passos Seguintes

[Delegar uma sub-rede para ficheiros do Azure NetApp](azure-netapp-files-delegate-subnet.md)
