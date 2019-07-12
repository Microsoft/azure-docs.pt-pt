---
title: Arquitetura do SAP HANA no Azure (instâncias grandes) de rede | Documentos da Microsoft
description: Arquitetura de rede de como implementar o SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2359f1ea1d55b8ce153295cc71fdf78040e8e316
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707405"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Arquitetura de rede do SAP HANA (instâncias grandes)

A arquitetura dos serviços de rede do Azure é um componente fundamental da implementação de aplicações SAP numa instância grande do HANA com êxito. Normalmente, o SAP HANA nas implementações do Azure (instâncias grandes) tem um ambiente SAP maior com várias soluções diferentes de SAP com diversos tamanhos de bases de dados, o consumo de recursos de CPU e utilização da memória. É provável que nem todos os sistemas de TI estão localizados no Azure, já. A paisagem SAP, muitas vezes, é híbrida também a partir de um ponto do DBMS e SAP aplicação ponto de vista usando uma combinação de NetWeaver e S/4HANA e SAP HANA e outras DBMS. O Azure oferece serviços diferentes que permitem-lhe executar os diferentes sistemas DBMS e S/4HANA NetWeaver no Azure. O Azure também oferece que tecnologia para fazem do Azure a aparência de rede, como um centro de dados virtuais para as implementações de software no local

A menos que seus sistemas de TI completos estão alojados no Azure. Funcionalidade de rede do Azure é utilizada para ligar o mundo no local com os recursos do Azure para tornar a aparência de seu datacenter virtual do Azure. A funcionalidade de rede do Azure utilizada é: 

- Redes virtuais do Azure estão ligadas para o [ExpressRoute](https://azure.microsoft.com/services/expressroute/) circuito que se liga a seus ativos de rede no local.
- Um circuito do ExpressRoute que se liga a no local para o Azure deve ter uma largura de banda mínima [1 Gbps ou superior](https://azure.microsoft.com/pricing/details/expressroute/). Esta largura de banda mínima permite a largura de banda suficiente para a transferência de dados entre sistemas no local e que são executadas em VMs. Também permite que a largura de banda suficiente para a ligação a sistemas do Azure dos utilizadores no local.
- Todos os sistemas SAP no Azure são configurados nas redes virtuais para comunicar entre si.
- O Active Directory e DNS alojadas no local são expandidos para o Azure através do ExpressRoute no local ou em execução completa no Azure.

Para o caso específico da integração de instâncias grandes do HANA para a malha de rede do Centro de dados do Azure, Azure ExpressRoute a tecnologia utilizada também


> [!NOTE] 
> Apenas uma subscrição do Azure pode ser ligada a apenas um inquilino num carimbo de data / instância grande do HANA numa região do Azure específica. Por outro lado, um único inquilino de carimbo de data / instância grande do HANA pode ser associado a apenas uma subscrição do Azure. Este requisito é consistente com outros objetos cobrar no Azure.

Se o SAP HANA no Azure (instâncias grandes) é implementado em várias regiões do Azure diferentes, um inquilino separado é implementado no carimbo de data / instância grande do HANA. Pode executar ambos na mesma subscrição do Azure, desde que estas instâncias são parte do mesmo ambiente SAP. 

> [!IMPORTANT] 
> Apenas o método de implementação Azure Resource Manager é suportado com o SAP HANA no Azure (instâncias grandes).

 

## <a name="additional-virtual-network-information"></a>Informações de rede virtual adicionais

Para ligar uma rede virtual para o ExpressRoute, tem de criar um gateway do ExpressRoute do Azure. Para obter mais informações, consulte [gateways sobre o Expressroute para o ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Um gateway do ExpressRoute do Azure é utilizado com o ExpressRoute para uma infra-estrutura de fora do Azure ou para um carimbo de instâncias grandes do Azure. Pode ligar o gateway do ExpressRoute do Azure para um máximo de quatro diferentes circuitos do ExpressRoute, desde que essas conexões provêm de diferentes routers de limite de enterprise da Microsoft. Para obter mais informações, consulte [infraestrutura de SAP HANA (instâncias grandes) e a conectividade no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> O débito máximo que pode conseguir com um gateway do ExpressRoute é 10 Gbps ao utilizar uma ligação do ExpressRoute. Copiar ficheiros entre uma VM que residem numa rede virtual e um sistema no local (como um fluxo de cópia única) não alcançar o débito total do gateway diferente SKUs. Para aproveitar a largura de banda completa do gateway do ExpressRoute, utilize vários fluxos. Ou, tem de copiar ficheiros diferentes em fluxos paralelos de um único ficheiro.


## <a name="networking-architecture-for-hana-large-instance"></a>Arquitetura de rede para a instância grande do HANA
A arquitetura de rede para a instância grande do HANA pode ser separada em quatro partes distintas:

- Local de rede e a ligação do ExpressRoute para o Azure. Esta parte é o domínio do cliente e está ligada ao Azure através do ExpressRoute. Este circuito do Expressroute é totalmente pagos por si como um cliente. A largura de banda deve ser suficientemente grande para processar o tráfego de rede entre os seus recursos no local e a região do Azure em relação a que está a ligar. Consulte a parte inferior direita na figura a seguir.
- Serviços de rede do Azure, conforme discutidos anteriormente, com as redes virtuais, o que precisam novamente os gateways do ExpressRoute adicionados. Esta parte é uma área em que precisa localizar os designs apropriados para os seus requisitos de aplicações, segurança e os requisitos de conformidade. Se utilizar a instância grande do HANA é outro ponto a considerar em termos de número de redes virtuais e os SKUs de gateway do Azure à sua escolha. Veja o canto superior direito da figura.
- Conectividade de instância grande do HANA através de tecnologia de ExpressRoute para o Azure. Esta parte é implementada e processada pela Microsoft. Tudo que precisa fazer é fornecer alguns intervalos de endereços IP após a implementação de seus ativos na instância grande do HANA ligarem o circuito do ExpressRoute para as redes virtuais. Para obter mais informações, consulte [infraestrutura de SAP HANA (instâncias grandes) e a conectividade no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Não existe uma taxa adicional para como um cliente para a conectividade entre os recursos de infraestrutura de rede de centro de dados do Azure e as unidades de instância grande do HANA.
- Funcionamento em rede no carimbo de data / instância grande do HANA, que é principalmente transparente para.

![Rede virtual ligada ao SAP HANA no Azure (instâncias grandes) e no local](./media/hana-overview-architecture/image1-architecture.png)

O requisito de que seus ativos no local tem de se ligar através do ExpressRoute para o Azure não muda o fato de usar a instância grande do HANA. O requisito de ter uma ou várias redes virtuais que executam as VMs que alojam a camada da aplicação que liga para o HANA nas instâncias alojada em unidades de instância grande do HANA, também não é alterado. 

As diferenças para implementações de SAP no Azure são:

- As unidades de instância grande do HANA do seu inquilino do cliente estão ligadas através de outro circuito do ExpressRoute em suas redes virtuais. Para separar as condições de carga, o local para circuitos do ExpressRoute de rede virtual do Azure e os circuitos entre redes virtuais do Azure e instâncias grandes do HANA não partilha os routers da mesmos.
- O perfil de carga de trabalho entre a camada de aplicação SAP e a instância grande do HANA é de natureza diferente, com muitas solicitações pequenas e o aumento, como transferências de dados (conjuntos de resultados) da SAP HANA para a camada de aplicativo.
- A arquitetura de aplicação SAP é mais sensível à latência de rede que cenários típicos onde os dados são trocados entre no local e o Azure.
- O gateway do ExpressRoute do Azure tem, pelo menos, duas ligações do ExpressRoute. Um circuito que está ligado no local e que está ligado a partir de instâncias grandes do HANA. Isso deixa apenas espaço para outro dois circuitos adicionais de MSEEs diferentes para ligar ao gateway do ExpressRoute. Esta restrição é independente da utilização do caminho rápido do ExpressRoute. Todos os circuitos ligados partilham a largura de banda máxima para os dados recebidos do ExpressRoute gateway.

A latência de rede teve entre VMs e instâncias grandes do HANA unidades podem ser mais do que um típica latência ida e volta de rede de VM para VM. Depende da região do Azure, os valores de medida podem exceder a latência de ida e volta de 0,7 ms classificada como abaixo da média em [1100926 no SAP Note # - FAQ: Desempenho de rede](https://launchpad.support.sap.com/#/notes/1100926/E). Dependente de região do Azure e a ferramenta para medir a latência de ida e volta de rede entre uma VM do Azure e a unidade de instância grande do HANA, a latência de medida pode ser até e em torno de 2 milissegundos. No entanto, os clientes implementar aplicações de SAP de produção com base em SAP HANA com êxito na instância grande do SAP HANA. Certifique-se de que testar os processos empresariais minuciosamente em instâncias grandes do HANA do Azure. Uma nova funcionalidade, chamada ExpressRoute rápida caminho, é capaz de reduzir a latência de rede entre instâncias grandes do HANA e VMs no Azure de camada de aplicativo substancialmente (ver abaixo). 

Para fornecer a latência de rede determinística entre VMs e instâncias grandes do HANA, a escolha do ExpressRoute gateway de SKU é essencial. Ao contrário dos padrões de tráfego entre VMs e no local, o padrão de tráfego entre VMs e instâncias grandes do HANA pode desenvolver pequenas mas elevadas rajadas de volumes de pedidos e dados a serem transmitidos. Para lidar com essas extrapolações bem, recomendamos vivamente a utilização do SKU de gateway de UltraPerformance. Para a classe de tipo II de SKUs de instância grande do HANA, a utilização do SKU de gateway de UltraPerformance como um gateway de ExpressRotue é obrigatória.

> [!IMPORTANT] 
> Tendo em conta o tráfego de rede geral entre o aplicativo SAP e camadas de base de dados, apenas o HighPerformance ou SKUs de gateway de UltraPerformance para redes virtuais são suportados para ligar ao SAP HANA no Azure (instâncias grandes). Para HANA grandes instância de tipo II SKUs, apenas o SKU do gateway UltraPerformance é suportado como um gateway do ExpressRoute. Exceções aplicam-se ao utilizar o ExpressRoute rápida caminho (ver abaixo)

### <a name="expressroute-fast-path"></a>Caminho rápido de ExpressRoute
Para reduzir a latência, o caminho rápido do ExpressRoute foi introduzido e lançado em Maio de 2019 para a conectividade específica do HANA nas instâncias grandes para redes virtuais do Azure que alojam as VMs de aplicação SAP. A principal diferença para a solução implementada até agora, é, o que os fluxos de dados entre VMs e instâncias grandes do HANA não sejam encaminhados através do gateway do ExpressRoute mais. Em vez disso, as VMs atribuídas em sub-redes da rede virtual do Azure diretamente estão a comunicar com o router de limite de enterprise dedicado. 

> [!IMPORTANT] 
> A funcionalidade de caminho rápido de ExpressRoute requer que as sub-redes que executam as VMs de aplicação SAP estão na mesma rede virtual do Azure que tem ligada a instâncias grandes do HANA. VMs localizadas em redes virtuais do Azure que estão em modo de peering com a rede virtual do Azure ligado diretamente para as unidades de instância grande do HANA não estão se beneficiando caminho rápido do ExpressRoute. Como resultado hub- and -spoke rede virtual dos designs típicos, onde os circuitos do ExpressRoute estão a ligar em relação a uma rede virtual do concentrador e redes virtuais que contêm a camada de aplicação SAP (spokes) introdução em modo de peering, a otimização por ExpressRoute Fast Caminho não funcionará. Além das, caminho rápido do ExpressRoute não suporta regras de encaminhamento definido pelo utilizador (UDR) hoje mesmo. Para obter mais informações, consulte [ExpressRoute virtual network gateway e FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


Para obter mais detalhes sobre como configurar o ExpressRoute rápida caminho, leia o documento [ligar uma rede virtual para instâncias grandes do HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).    

> [!NOTE]
> Um gateway do UltraPerformance ExpressRoute é necessário ter o trabalho de caminho rápido do ExpressRoute


## <a name="single-sap-system"></a>Único sistema SAP

A infraestrutura no local mostrada anteriormente está ligada através do ExpressRoute para o Azure. O circuito do ExpressRoute liga-se num router de limite do Microsoft enterprise (MSEE). Para obter mais informações, consulte [visão geral técnica do ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Depois da rota é estabelecida, liga-se para o backbone do Azure.

> [!NOTE] 
> Para executar cenários SAP no Azure, ligue o router de periferia de empresa mais próxima da região do Azure no ambiente SAP. Carimbos de data / instância grande do HANA está ligados através de dispositivos de router de limite de enterprise dedicado para minimizar a latência de rede entre VMs no Azure IaaS e carimbos de data / instância grande do HANA.

O gateway do ExpressRoute para as VMs que alojam as instâncias da aplicação SAP está ligado a um circuito do ExpressRoute que se liga a no local. Da mesma rede virtual está ligada a um router de limite de enterprise separado dedicado à ligação a carimbos de data / instância grande. Através do ExpressRoute rápida caminho, o fluxo de dados de instâncias grandes do HANA para a camada de aplicação SAP VMs não são encaminhadas através do gateway do ExpressRoute mais e com que reduzem a rede arredondar latência.

Este sistema é um exemplo simples de um único sistema SAP. Camada de aplicação SAP está alojada no Azure. A base de dados do SAP HANA é executado no SAP HANA no Azure (instâncias grandes). A pressuposição é de que a largura de banda do gateway do ExpressRoute de débito de 2 Gbps ou 10 Gbps não representa um afunilamento.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Vários sistemas SAP ou sistemas grandes de SAP

Se vários sistemas SAP ou sistemas de grande dimensão SAP forem implementados para ligar ao SAP HANA no Azure (instâncias grandes), o débito do ExpressRoute gateway pode se tornar um afunilamento. Ou, se pretender isolar os sistemas de não produção em diferentes redes virtuais do Azure e de produção. Nesse caso, divida as camadas do aplicativo em várias redes virtuais. Também pode criar uma rede virtual especial que se liga a instância grande do HANA para casos como:

- Efetuar cópias de segurança diretamente a partir do HANA nas instâncias na instância grande do HANA para uma VM no Azure que aloja partilhas NFS.
- Copiar as cópias de segurança grandes ou outros ficheiros de unidades de instância grande do HANA para espaço de disco gerido no Azure.

Utilize uma rede virtual separada para o anfitrião de VMs que gerir o armazenamento de transferência em massa de dados entre instâncias grandes do HANA e o Azure. Essa organização evita os efeitos de arquivo grande ou transferência de dados de instância grande do HANA para o Azure no gateway ExpressRoute que serve de VMs que executam a camada de aplicação SAP. 

Para uma arquitetura de rede mais escalonável:

- Tire partido de várias redes virtuais numa camada de aplicação SAP única e maior.
- Implemente uma rede virtual separada para cada sistema SAP implementado, em comparação com a combinação desses sistemas SAP em sub-redes separadas na mesma rede virtual.

  Uma arquitetura de rede mais escalonável para o SAP HANA no Azure (instâncias grandes):

![Implementar a camada de aplicação SAP através de várias redes virtuais](./media/hana-overview-architecture/image4-networking-architecture.png)

Depende de regras e restrições, que pretende aplicar entre as redes virtuais diferentes, hospedar VMs de diferentes sistemas SAP, deve configurar o peering entre as redes virtuais. Para obter mais informações sobre o peering de rede virtual, consulte [peering de rede Virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Encaminhamento do Azure

Pela implementação do padrão, três considerações de encaminhamento de rede são importantes para o SAP HANA no Azure (instâncias grandes):

* SAP HANA no Azure (instâncias grandes) podem ser acedido apenas por meio de VMs do Azure e a ligação dedicada do ExpressRoute, não diretamente a partir de locais. Acesso direto no local para as unidades de instância grande do HANA, conforme fornecido pela Microsoft, não é possível imediatamente. As restrições de encaminhamento transitivas são devido à arquitetura de rede do Azure atual utilizada para a instância grande do SAP HANA. Alguns clientes da administração e todas as aplicações que precisam direcionar o acesso, como o SAP solução Manager em execução no local, não é possível ligar à base de dados SAP HANA. Para exceções, veja a secção 'Encaminhamento direto para instâncias grandes do HANA'.

* Se tiver unidades de instância grande do HANA implementadas em duas diferentes regiões do Azure para recuperação após desastre, as mesmas restrições encaminhamento transitórias aplicadas no passado. Em outras palavras, os endereços IP de uma unidade de instância grande do HANA numa única região (por exemplo, e.u.a. Centro-Oeste) não foram encaminhados para uma unidade de instância grande do HANA implementada noutra região (por exemplo, E.u.a. Leste). Esta restrição era independente do uso de peering entre regiões ou ligar os circuitos do ExpressRoute que se ligam a unidades de instância grande do HANA para redes virtuais entre a rede do Azure. Para uma representação gráfica, consulte a figura na secção "Instância grande do HANA de utilização de unidades em várias regiões." Esta restrição, que vem com a arquitetura de implementada, proibida a utilização imediata do HANA System Replication como funcionalidade de recuperação após desastre. Alterações recentes, procure a secção 'Unidades de instância grande do HANA de utilização em várias regiões'. 

* SAP HANA nas unidades do Azure (instâncias grandes) tem um endereço IP atribuído o intervalo de endereços de conjunto IP de servidor enviada ao solicitar a implementação de instância grande do HANA. Para obter mais informações, consulte [infraestrutura de SAP HANA (instâncias grandes) e a conectividade no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Este endereço IP está acessível através de subscrições do Azure e o circuito do que se liga a redes virtuais do Azure para instâncias grandes do HANA. O endereço IP atribuído de que o intervalo de endereços do conjunto IP do servidor é diretamente atribuído para a unidade de hardware. Ele possui *não* atribuída através de NAT, como era o caso nas Implantações primeiro desta solução. 

### <a name="direct-routing-to-hana-large-instances"></a>Direcionar o encaminhamento para instâncias grandes do HANA
Por predefinição o encaminhamento transitivo entre unidades de instância grande do HANA e no local ou entre instâncias grandes do HANA encaminhamento que são implementadas em duas regiões diferentes não funciona. Existem várias possibilidades para permitir que tal um encaminhamento transitivo.

- Um proxy inverso para encaminhar os dados, de e para. Por exemplo, F5 BIG-IP, o NGINX com o Gestor de tráfego implementado na rede virtual do Azure que liga-se para instâncias grandes do HANA e no local como uma solução de encaminhamento de tráfego/firewall virtual.
- Usando [regras de IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) numa VM do Linux para ativar o encaminhamento entre localizações no local e unidades de instância grande do HANA ou entre unidades de instância grande do HANA em regiões diferentes. A VM em execução IPTables tem de ser implementado na rede virtual do Azure que liga ao HANA nas instâncias grandes e no local. A VM tem de ser redimensionados em conformidade, isso, que o débito de rede da VM é suficiente para o tráfego de rede esperado. Para obter detalhes sobre a VM a largura de banda de rede, consulte o artigo [tamanhos das máquinas virtuais Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Firewall do Azure](https://azure.microsoft.com/services/azure-firewall/) seria outra solução para ativar a direcionar o tráfego entre no local e unidades de instância grande do HANA. 

Todo o tráfego dessas soluções seria encaminhado através de uma rede virtual do Azure e como tal, o tráfego pode ser além disso, restrito aos dispositivos de forma recuperável utilizados ou ao Azure grupos de segurança, por isso, que varia de certos endereços IP ou o endereço IP de no local foi bloqueado ou permitido explicitamente a aceder a instâncias grandes do HANA. 

> [!NOTE]  
> Lembre-se de que o suporte para soluções personalizadas que envolvem de terceiros e de implementação de aplicações de rede ou IPTables não foi fornecido pela Microsoft. Suporte deve ser fornecido pelo fornecedor do componente utilizado ou o integrador de. 

#### <a name="express-route-global-reach"></a>Express Route de alcance Global
A Microsoft introduziu uma nova funcionalidade denominada [alcance Global do ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). Alcance global pode ser usado para instâncias grandes do HANA em dois cenários:

- Ativar o acesso direto a partir do local para unidades de instância grande do HANA implantados em diferentes regiões
- Ativar a comunicação direta entre unidades de instância grande do HANA implantados em diferentes regiões


##### <a name="direct-access-from-on-premise"></a>Acesso direto no local
Nas regiões do Azure onde o alcance Global é oferecido, pode pedir a ativar a funcionalidade de alcance Global de mensagens em fila para o seu circuito do ExpressRoute que se liga a sua rede no local para a rede virtual do Azure que se liga a unidades de instância grande do HANA. Há algumas implicações de custo para o lado no local do seu circuito do ExpressRoute. Para os preços, consulte os preços para [suplemento alcance Global](https://azure.microsoft.com/pricing/details/expressroute/). Não existem sem custos adicionais para relacionados ao circuito que se liga a instância grande do HANA unidade (s) para o Azure. 

> [!IMPORTANT]  
> Em caso de utilizar o alcance Global para permitir o acesso direto entre as suas unidades de instância grande do HANA e ativos no local, o fluxo de dados e o controlo de rede é **não é encaminhado através de redes virtuais do Azure**, mas diretamente entre a Microsoft routers de exchange Enterprise. Como resultado quaisquer regras NSG ou ASG ou qualquer tipo de firewall, NVA ou proxy implementado numa rede virtual do Azure, não são obter tocado. **Se utilizar o alcance Global do ExpressRoute para ativar o acesso direto a partir do local para restrições de unidades de instância grande do HANA e permissões para aceder a unidades de instância grandes do HANA têm de ser definidos em firewalls no lado do local** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Ligar a instâncias grandes do HANA em diferentes regiões do Azure
Da mesma forma, como o alcance Global do ExpressRoute pode ser utilizado para ligar no local para unidades de instância grande do HANA, ele pode ser utilizado para ligar tow instância grande do HANA inquilinos que são implementados por si em duas regiões diferentes. O isolamento é dos circuitos do ExpressRoute que os inquilinos de instância grande do HANA estão a utilizar para ligar ao Azure em ambas as regiões. Não existem não existem custos adicionais para ligar os dois inquilinos de instância grande do HANA que são implementados em duas regiões diferentes. 

> [!IMPORTANT]  
> O fluxo de dados e a controlar o fluxo do tráfego de rede entre os diferentes HANA grandes inquilinos de instância não serão encaminhados através de redes do azure. Como resultado não é possível utilizar a funcionalidade do Azure ou de NVAs para impor restrições de comunicação entre os dois inquilinos de instâncias grandes do HANA. 

Para obter mais detalhes sobre como obter o ExpressRoute alcance Global ativada, leia o documento [ligar uma rede virtual para instâncias grandes do HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).


## <a name="internet-connectivity-of-hana-large-instance"></a>Conectividade de Internet da instância grande do HANA
Instância grande do HANA faz *não* tem conectividade internet direta. Por exemplo, esta limitação pode restringir a capacidade de registar a imagem do SO diretamente com o fornecedor do sistema operacional. Talvez precise trabalhar com o seu servidor local da ferramenta de gestão de subscrição do SUSE Linux Enterprise Server ou o Gestor de subscrições do Red Hat Enterprise Linux.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Encriptação de dados entre VMs e instâncias grandes do HANA
Dados transferidos entre a instância grande do HANA e VMs não estão encriptados. No entanto, apenas para a troca entre o lado do HANA DBMS e as aplicações baseadas em JDBC/ODBC, pode ativar a encriptação de tráfego. Para obter mais informações, consulte [esta documentação pela SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Utilizar unidades de instância grande do HANA em várias regiões

Para obter ups de conjunto de recuperação após desastre, tem de ter unidades de instância grande do SHANA em várias regiões do Azure. Mesmo com utilizar o Azure [Global Vnet Peering], o encaminhamento transitivo por predefinição não está a funcionar entre inquilinos de instância grande do HANA em duas regiões diferentes. No entanto, alcance Global abre o caminho de comunicação entre as unidades de instância grande do HANA que aprovisionou em duas regiões diferentes. Neste cenário de utilização de alcance Global do ExpressRoute permite:

 - HANA System Replication sem firewalls ou proxies adicionais
 - Copiar as cópias de segurança entre unidades de instância grande do HANA em duas regiões diferentes para efetuar cópias de sistema ou atualizações de sistema


![Rede virtual ligado a carimbos de instâncias grandes do Azure em diferentes regiões do Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

A figura mostra como as redes virtuais diferentes em ambas as regiões estão ligadas a dois circuitos do ExpressRoute diferentes que são utilizados para ligar ao SAP HANA no Azure (instâncias grandes) em ambas as regiões do Azure (linhas cinza). Motivo para esta duas ligações cruzadas é para proteger contra uma falha de MSEEs em ambos os lados. O fluxo de comunicação entre as duas redes virtuais em duas regiões do Azure deve ser processada através da [global peering](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) das duas redes virtuais em duas regiões diferentes (linha ponteada com o azul). A linha vermelha espessa descreve a ligação alcance Global do ExpressRoute, que permite que as unidades de instância grande do HANA dos seus inquilinos em duas regiões diferentes para comunicar entre si. 

> [!IMPORTANT] 
> Se utilizou vários circuitos do ExpressRoute, prefixação como caminho e as definições de BGP de preferência Local devem ser utilizadas para garantir que o encaminhamento adequado de tráfego.

**Passos seguintes?**
- Consulte [arquitetura de armazenamento do SAP HANA (instâncias grandes)](hana-storage-architecture.md)