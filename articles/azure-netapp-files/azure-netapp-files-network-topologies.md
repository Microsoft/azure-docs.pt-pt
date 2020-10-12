---
title: Diretrizes para o planeamento da rede Azure NetApp Files Microsoft Docs
description: Descreve diretrizes que podem ajudá-lo a projetar uma arquitetura de rede eficaz usando ficheiros Azure NetApp.
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
ms.date: 09/08/2020
ms.author: ramakk
ms.openlocfilehash: a8d81acc0fcb4afa0f981fca3fd099296a0361df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569497"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Diretrizes para o planeamento de rede dos Azure NetApp Files

O planeamento da arquitetura em rede é um elemento-chave para a conceção de qualquer infraestrutura de aplicação. Este artigo ajuda-o a conceber uma arquitetura de rede eficaz para as suas cargas de trabalho para beneficiar das ricas capacidades dos Ficheiros Azure NetApp.

Os volumes de Ficheiros Azure NetApp foram concebidos para serem contidos numa sub-rede de finalidades especial chamada [sub-rede delegada](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) dentro da sua Rede Virtual Azure. Portanto, pode aceder aos volumes diretamente a partir do seu VNet, a partir de VNets espreitados na mesma região, ou de instalações sobre um Gateway de Rede Virtual (ExpressRoute ou Gateway VPN) se necessário. A sub-rede é dedicada aos Ficheiros Azure NetApp e não existe conectividade com outros serviços Azure ou internet.

## <a name="considerations"></a>Considerações  

Deve compreender algumas considerações quando planeia a rede de Ficheiros Azure NetApp.

### <a name="constraints"></a>Restrições

As funcionalidades abaixo não são atualmente suportadas por Ficheiros Azure NetApp: 

* Grupos de segurança de rede (NSGs) aplicados à sub-rede delegada
* Rotas definidas pelo utilizador (UDRs) aplicadas à sub-rede delegada
* Políticas Azure (por exemplo, políticas de nomeação personalizadas) na interface Azure NetApp Files
* Balanceadores de carga para tráfego de ficheiros Azure NetApp
* WAN Virtual do Azure 
* Portas de rede virtual redundantes da zona (Gateway SKUs com Az) 
* GWs de rede virtual ativa/ativa 
* VNet de pilha dupla (IPv4 e IPv6)

As seguintes restrições de rede aplicam-se aos ficheiros Azure NetApp:

* O número de IPs em uso num VNet com Ficheiros Azure NetApp (incluindo VNets) não pode exceder 1000. Estamos a trabalhar no sentido de aumentar este limite para satisfazer as exigências da escala de clientes. 
* Em cada Rede Virtual Azure (VNet), apenas uma sub-rede pode ser delegada em Ficheiros Azure NetApp.


### <a name="supported-network-topologies"></a>Topologias de rede suportadas

A tabela seguinte descreve as topologias de rede suportadas pelos Ficheiros Azure NetApp.  Também descreve as soluções alternativas para as topologias não suportadas. 

|    Topologias    |    É apoiado    |     Solução    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Conectividade ao volume num VNet local    |    Sim    |         |
|    Conectividade ao volume num VNet espremiado (mesma região)    |    Sim    |         |
|    Conectividade ao volume num VNet espremiado (região cruzada ou persiódia global)    |    Não    |    Nenhum    |
|    Conectividade a um volume sobre gateway ExpressRoute    |    Sim    |         |
|    Conectividade desde as instalações até um volume num VNet falado sobre o gateway ExpressRoute e vNet a espreitar com o trânsito de gateway    |    Sim    |        |
|    Conectividade desde as instalações até um volume num VNet falado sobre o gateway VPN    |    Sim    |         |
|    Conectividade desde as instalações até um volume num VNet falado sobre gateway VPN e VNet a espreitar com o trânsito de gateway    |    Sim    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Rede virtual para volumes de ficheiros Azure NetApp

Esta secção explica conceitos que o ajudam no planeamento de redes virtuais.

### <a name="azure-virtual-networks"></a>Redes virtuais do Azure

Antes de a provisionar um volume de Ficheiros Azure NetApp, é necessário criar uma rede virtual Azure (VNet) ou utilizar uma que já exista na sua subscrição. O VNet define o limite de rede do volume.  Para obter mais informações sobre a criação de redes virtuais, consulte a [documentação da Rede Virtual Azure.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)

### <a name="subnets"></a>Sub-redes

As sub-redes segmentam a rede virtual em espaços de endereço separados que são utilizáveis pelos recursos Azure neles.  Os volumes de ficheiros Azure NetApp estão contidos numa sub-rede para fins especiais chamada [sub-rede delegada](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet). 

A delegação da sub-rede dá permissões explícitas ao serviço Azure NetApp Files para criar recursos específicos do serviço na sub-rede.  Usa um identificador único na implementação do serviço. Neste caso, é criada uma interface de rede para permitir a conectividade aos Ficheiros Azure NetApp.

Se utilizar um novo VNet, pode criar uma sub-rede e delegar a sub-rede para ficheiros Azure NetApp seguindo instruções em [Delegado uma sub-rede para ficheiros Azure NetApp](azure-netapp-files-delegate-subnet.md). Também pode delegar uma sub-rede vazia existente que ainda não está delegada noutros serviços.

Se o VNet for espremiado com outro VNet, não é possível expandir o espaço de endereço VNet. Por essa razão, a nova sub-rede delegada precisa de ser criada dentro do espaço de endereço VNet. Se precisar de estender o espaço do endereço, deve eliminar o espremiamento VNet antes de expandir o espaço do endereço.

### <a name="udrs-and-nsgs"></a>UDRs e NSGs

As rotas definidas pelo utilizador (UDRs) e os grupos de segurança da rede (NSGs) não são suportados em sub-redes delegadas para ficheiros Azure NetApp. No entanto, pode aplicar UDRs e NSGs a outras sub-redes, mesmo dentro do mesmo VNet que a sub-rede delegada nos Ficheiros Azure NetApp.

* As UDRs definem então os fluxos de tráfego das outras sub-redes para a sub-rede delegada do Azure NetApp. Isto ajuda a garantir que este está alinhado com o fluxo de tráfego de volta dos Ficheiros Azure NetApp para as outras sub-redes que utilizam as rotas do sistema.  
* Os NSGs permitem ou negam o tráfego de e para a sub-rede Azure NetApp. 

## <a name="azure-native-environments"></a>Ambientes nativos de Azure

O seguinte diagrama ilustra um ambiente nativo-azul:

![Ambiente de networking nativo-azure](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Local VNet

Um cenário básico é criar ou ligar-se a um volume de Ficheiros Azure NetApp a partir de uma máquina virtual (VM) no mesmo VNet. Para o VNet 2 no diagrama acima, o Volume 1 é criado numa sub-rede delegada e pode ser montado em VM 1 na sub-rede predefinida.

### <a name="vnet-peering"></a>VNet peering

Se tiver VNets adicionais na mesma região que precisam de acesso aos recursos uns dos outros, os VNets podem ser conectados usando [o par VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) para permitir uma conectividade segura através da infraestrutura Azure. 

Considere vNet 2 e VNet 3 no diagrama acima. Se o VM 1 precisar de se ligar ao VM 2 ou ao Volume 2, ou se o VM 2 precisar de ligar ao VM 1 ou ao Volume 1, então tem de ativar o observamento vNet entre o VNet 2 e o VNet 3. 

Além disso, considere um cenário em que o VNet 1 é espremiado com VNet 2, e o VNet 2 é espremiado com VNet 3 na mesma região. Os recursos do VNet 1 podem ligar-se aos recursos no VNet 2, mas não podem ligar-se aos recursos no VNet 3, a menos que o VNet 1 e o VNet 3 sejam espreitados. 

No diagrama acima, embora o VM 3 possa ligar-se ao Volume 1, o VM 4 não pode ligar-se ao Volume 2.  A razão para isso é que os VNets falados não são espreitados, e _o encaminhamento de trânsito não é suportado sobre o espremiado VNet_.

## <a name="hybrid-environments"></a>Ambientes híbridos

O seguinte diagrama ilustra um ambiente híbrido: 

![Ambiente de networking híbrido](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

No cenário híbrido, as aplicações dos datacenters no local precisam de acesso aos recursos em Azure.  É o caso de querer estender o seu datacenter ao Azure, ou se pretende utilizar serviços nativos do Azure ou para a recuperação de desastres. Consulte opções de [planeamento do Gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) para obter informações sobre como ligar vários recursos no local aos recursos em Azure através de uma VPN site-to-site ou um ExpressRoute.

Numa topologia híbrida falada por hub, o hub VNet em Azure funciona como um ponto central de conectividade para a sua rede no local. Os raios são VNets espreitados com o centro, e podem ser usados para isolar cargas de trabalho.

Dependendo da configuração, pode ligar recursos no local aos recursos no hub e nos raios.

Na topologia ilustrada acima, a rede no local está ligada a um hub VNet em Azure, e existem 2 VNets de fala na mesma região espreitadas com o hub VNet.  Neste cenário, as opções de conectividade suportadas para os volumes dos Ficheiros Azure NetApp são as seguintes:

* Os recursos no local VM 1 e VM 2 podem ligar-se ao Volume 1 no centro sobre um circuito VPN local ou ExpressRoute. 
* Os recursos no local VM 1 e VM 2 podem ligar-se ao Volume 2 ou Volume 3 sobre uma VPN local e um vnet regional.
* VM 3 no hub VNet pode ligar-se ao Volume 2 em VNet 1 e Volume 3 em VNet 2 falado.
* VM 4 de VNet 1 e VM 5 de spoke VNet 2 podem ligar-se ao Volume 1 no hub VNet.
* VM 4 em VNet 1 falado não pode ligar-se ao Volume 3 em VNet 2 falado. Além disso, vM 5 em VNet2 falado não pode ligar-se ao Volume 2 em VNet 1 falado. É o caso porque os VNets falados não são vigiados e _o encaminhamento de trânsito não é suportado sobre o espremiado VNet_.
* Na arquitetura acima referida, se houver uma porta de entrada no VNET falado também, a conectividade com o volume ANF a partir da ligação on-prem sobre o gateway no Hub será perdida. Por design, seria dada preferência ao gateway no VNet falado e assim apenas máquinas que se conectam sobre esse gateway podem ligar-se ao volume ANF.

## <a name="next-steps"></a>Passos seguintes

[Delegar uma sub-rede ao Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
