---
title: Diretrizes para o planeamento da rede de ficheiros Azure NetApp / Microsoft Docs
description: Descreve diretrizes que podem ajudá-lo a projetar uma arquitetura de rede eficaz usando Ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
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
ms.openlocfilehash: 12be766f36a0901079a5a26f20ea7dacc75268de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80667864"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Diretrizes para o planeamento de rede dos Azure NetApp Files

O planeamento da arquitetura de rede é um elemento chave para a conceção de qualquer infraestrutura de aplicação. Este artigo ajuda-o a conceber uma arquitetura de rede eficaz para as suas cargas de trabalho para beneficiar das ricas capacidades dos Ficheiros Azure NetApp.

Os volumes de Ficheiros Azure NetApp foram concebidos para serem contidos numa subnet de finalidade especial chamada [subnet delegada](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) dentro da sua Rede Virtual Azure. Portanto, pode aceder aos volumes diretamente do seu VNet, a partir de VNets na mesma região, ou de instalações através de um Gateway de Rede Virtual (ExpressRoute ou VPN Gateway) conforme necessário. A subnet é dedicada aos Ficheiros Azure NetApp e não há conectividade com outros serviços Azure ou com a Internet.

## <a name="considerations"></a>Considerações  

Deve compreender algumas considerações quando planeia a rede Deficheiros Azure NetApp.

### <a name="constraints"></a>Restrições

As funcionalidades abaixo não são suportadas atualmente para ficheiros Azure NetApp: 

* Grupos de segurança de rede (NSGs) aplicados à subnet delegada
* Rotas definidas pelo utilizador (UDRs) com prefixo de endereço como subnet de ficheiros Azure NetApp
* Políticas azure (por exemplo, políticas de nomeação personalizada) na interface De Ficheiros Azure NetApp
* Balanceadores de carga para tráfego de Ficheiros Azure NetApp
* Os Ficheiros Azure NetApp não são suportados com o Azure Virtual WAN

As seguintes restrições de rede aplicam-se aos Ficheiros Azure NetApp:

* O número de IPs em uso num VNet com Ficheiros Azure NetApp (incluindo VNets peered) não pode exceder 1000. Estamos a trabalhar no sentido de aumentar este limite para satisfazer as exigências à escala dos clientes. 
* Em cada Rede Virtual Azure (VNet), apenas uma subnet pode ser delegada em Ficheiros Azure NetApp.


### <a name="supported-network-topologies"></a>Topologs de rede suportadas

A tabela seguinte descreve as topoologias da rede suportadas pelos Ficheiros Azure NetApp.  Também descreve as sobras para as topoologias não suportadas. 

|    Topologias    |    É apoiado    |     Solução    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Conectividade ao volume em um VNet local    |    Sim    |         |
|    Conectividade ao volume em um VNet peered (Mesma região)    |    Sim    |         |
|    Conectividade ao volume num VNet peered (região transversal ou peering global)    |    Não    |    Nenhuma    |
|    Conectividade a um volume sobre gateway ExpressRoute    |    Sim    |         |
|    Conectividade desde as instalações até um volume em um VNet falado sobre gateway ExpressRoute e VNet espreitando com trânsito de gateway    |    Sim    |        |
|    Conectividade desde as instalações até um volume em um VNet falado sobre gateway VPN    |    Sim    |         |
|    Conectividade desde as instalações até um volume em um VNet falado sobre gateway VPN e VNet espreitando com trânsito de gateway    |    Sim    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Rede virtual para volumes de ficheiros Azure NetApp

Esta secção explica conceitos que o ajudam no planeamento virtual da rede.

### <a name="azure-virtual-networks"></a>Redes virtuais do Azure

Antes de fornecer um volume de Ficheiros Azure NetApp, precisa de criar uma rede virtual Azure (VNet) ou utilizar uma que já exista na sua subscrição. O VNet define o limite de rede do volume.  Para obter mais informações sobre a criação de redes virtuais, consulte a documentação da [Rede Virtual Azure.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)

### <a name="subnets"></a>Sub-redes

As subredes segmentam a rede virtual em espaços de endereçoseparados que são utilizáveis pelos recursos do Azure neles.  Os volumes de ficheiros Azure NetApp estão contidos numa subnet de propósito especial chamada [subnet delegada](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet). 

A delegação da Subnet dá permissões explícitas ao serviço Azure NetApp Files para criar recursos específicos do serviço na subnet.  Utiliza um identificador único na implementação do serviço. Neste caso, é criada uma interface de rede para permitir a conectividade com o Azure NetApp Files.

Se utilizar um novo VNet, pode criar uma subnet e delegar a subnet para ficheiros Azure NetApp seguindo instruções em [Delegar uma subnet a Ficheiros Azure NetApp](azure-netapp-files-delegate-subnet.md). Também pode delegar uma sub-rede vazia existente que ainda não está delegada noutros serviços.

Se o VNet for espreitado por outro VNet, não pode expandir o espaço de endereço VNet. Por essa razão, a nova subnet delegada precisa de ser criada dentro do espaço de endereço VNet. Se necessitar de alargar o espaço de endereço, tem de eliminar o vnet antes de expandir o espaço de endereço.

### <a name="udrs-and-nsgs"></a>UDRs e NSGs

As rotas definidas pelo utilizador (UDRs) e os grupos de segurança da rede (NSGs) não são suportadas em subredes delegadas para ficheiros Azure NetApp.

Como suposição, pode aplicar NSGs a outras subredes que permitam ou negam o tráfego de e para a subnet dedelegado dos Ficheiros Azure NetApp.  

## <a name="azure-native-environments"></a>Ambientes nativos de Azure

O diagrama que se segue ilustra um ambiente azure-nativo:

![Ambiente de networking nativo-azul](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Local VNet

Um cenário básico é criar ou ligar-se a um volume de Ficheiros Azure NetApp a partir de uma máquina virtual (VM) no mesmo VNet. Para vNet 2 no diagrama acima, o Volume 1 é criado numa subnet delegada e pode ser montado em VM 1 na sub-rede predefinida.

### <a name="vnet-peering"></a>VNet peering

Se tiver VNets adicionais na mesma região que precisam de acesso aos recursos uns dos outros, os VNets podem ser conectados usando [o peering VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) para permitir uma conectividade segura através da infraestrutura Azure. 

Considere vNet 2 e VNet 3 no diagrama acima. Se o VM 1 precisar de ligar ao VM 2 ou ao Volume 2, ou se o VM 2 precisar de ligar ao VM 1 ou ao Volume 1, então tem de ativar o vNet entre vNet 2 e VNet 3. 

Além disso, considere um cenário em que vNet 1 é espreitado com VNet 2, e VNet 2 é espreitado com VNet 3 na mesma região. Os recursos da VNet 1 podem ligar-se aos recursos em VNet 2, mas não podem ligar-se aos recursos em VNet 3, a menos que o VNet 1 e o VNet 3 sejam espreitados. 

No diagrama acima, embora o VM 3 possa ligar-se ao Volume 1, o VM 4 não consegue ligar-se ao Volume 2.  A razão para isso é que os VNets falados não são evidecidos, e o _encaminhamento de trânsito não é suportado sobre o peering VNet_.

## <a name="hybrid-environments"></a>Ambientes híbridos

O diagrama que se segue ilustra um ambiente híbrido: 

![Ambiente de networking híbrido](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

No cenário híbrido, as aplicações dos datacenters no local precisam de acesso aos recursos em Azure.  É o caso de querer estender o seu datacenter ao Azure, ou se pretende utilizar os serviços nativos do Azure ou para a recuperação de desastres. Consulte opções de [planeamento vpn Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) para informações sobre como ligar vários recursos no local aos recursos em Azure através de uma VPN site-to-site ou uma ExpressRoute.

Numa topologia híbrida de hub-spoke, o hub VNet em Azure funciona como um ponto central de conectividade com a sua rede no local. Os raios são VNets espreitados com o centro, e podem ser usados para isolar cargas de trabalho.

Dependendo da configuração, pode ligar recursos no local aos recursos no centro e nos raios.

Na topologia ilustrada acima, a rede no local está ligada a um hub VNet em Azure, e existem 2 VNets de porta-voz na mesma região espreitadas com o hub VNet.  Neste cenário, as opções de conectividade suportadas para os volumes de Ficheiros Azure NetApp são as seguintes:

* Os recursos no local VM 1 e VM 2 podem ligar-se ao Volume 1 no centro através de um circuito VPN ou ExpressRoute local. 
* Os recursos no local VM 1 e VM 2 podem ligar-se ao Volume 2 ou Volume 3 através de uma VPN local-a-site e peering regional Vnet.
* VM 3 no hub VNet pode ligar-se ao Volume 2 em VNet 1 falado e Volume 3 em VNet 2 falado.
* VM 4 de VNet 1 e VM 5 de vNet 2 falado pode ligar ao Volume 1 no hub VNet.
* VM 4 em VNet 1 falado não pode ligar ao Volume 3 em VNet 2 falado. Além disso, vM 5 em vNet2 falado não pode ligar ao Volume 2 em VNet 1 falado. É o caso porque os VNets falados não são evidecidos e o _encaminhamento de trânsito não é suportado por um olhar vnet._
* Na arquitetura acima, se houver uma porta de entrada no VNET falado também, perder-se-á a conectividade com o volume ANF a partir da ligação on-prem sobre a porta de entrada no Hub. Por design, a preferência seria dada ao gateway no VNet falado e, por isso, apenas as máquinas que ligam essa porta podem ligar-se ao volume ANF.

## <a name="next-steps"></a>Passos seguintes

[Delegar uma sub-rede para os Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
