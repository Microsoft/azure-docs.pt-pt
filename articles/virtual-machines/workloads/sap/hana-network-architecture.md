---
title: Arquitetura do SAP HANA no Azure (instâncias grandes) de rede | Documentos da Microsoft
description: Arquitetura de rede de como implementar o SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 724a91b6ba0be030a2281bce366e4378892df59b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011576"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Arquitetura de rede do SAP HANA (instâncias grandes)

A arquitetura dos serviços de rede do Azure é um componente fundamental da implementação de aplicações SAP numa instância grande do HANA com êxito. Normalmente, o SAP HANA nas implementações do Azure (instâncias grandes) tem um ambiente SAP maior com várias soluções diferentes de SAP com diversos tamanhos de bases de dados, o consumo de recursos de CPU e utilização da memória. É provável que nem todos os esses sistemas SAP baseiam-se no SAP HANA. A paisagem SAP é provavelmente uma híbrida que utiliza:

- Implementar SAP sistemas no local. Devido a seus tamanhos, esses sistemas atualmente não é possível alojados no Azure. Um exemplo é um sistema de SAP ERP, que é executado no SQL Server (como a base de dados) e requer mais recursos de CPU ou memória que podem fornecer as VMs de produção.
- Implementado com base em SAP HANA SAP sistemas no local.
- Sistemas implantados de SAP em VMs. Estes sistemas podem ser o desenvolvimento, teste, área de segurança, ou instâncias de produção para qualquer um dos aplicativos baseados em SAP NetWeaver, que podem implementar com êxito no Azure (em VMs), com base na procura de memória e de consumo de recursos. Estes sistemas também podem ser baseados nas bases de dados como o SQL Server. Para obter mais informações, consulte [1928533 de n. º de nota de suporte de SAP – aplicações SAP no Azure: Produtos suportados e tipos de VM do Azure](https://launchpad.support.sap.com/#/notes/1928533/E). E esses sistemas podem basear-se nas bases de dados, como o SAP HANA. Para obter mais informações, consulte [plataformas IaaS com certificação SAP HANA](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).
- Implementar servidores de aplicações SAP no Azure (em VMs) que tiram partido do SAP HANA no Azure (instâncias grandes) no carimbos de instâncias grandes do Azure.

Um ambiente SAP com quatro ou mais cenários de implementação diferentes de híbrido é normal. Também há muitos casos de clientes de cenários SAP completos, que são executadas no Azure. À medida que as VMs se tornar mais poderosas, aumenta o número de clientes que mover todas as suas soluções SAP no Azure.

Redes no contexto de sistemas SAP implementadas no Azure do Azure não é complicado. Baseia-se os seguintes princípios:

- Redes virtuais do Azure tem de estar ligados ao circuito do ExpressRoute que se liga a uma rede no local.
- Um circuito do ExpressRoute que se liga no local para o Azure, normalmente, deve ter uma largura de banda de 1 Gbps ou superior. Esta largura de banda mínima permite a largura de banda suficiente para a transferência de dados entre sistemas no local e que são executadas em VMs. Também permite que a largura de banda suficiente para a ligação a sistemas do Azure dos utilizadores no local.
- Todos os sistemas SAP no Azure tem de ser configurados nas redes virtuais para comunicar entre si.
- Active Directory e DNS alojadas no local são expandidos para o Azure através do ExpressRoute no local.


> [!NOTE] 
> Do ponto de vista faturação, apenas uma subscrição do Azure pode ser associada ao inquilino apenas um num carimbo de data / instância grande numa região do Azure específica. Por outro lado, um único inquilino de carimbo de data / instância grande pode ser ligado a apenas uma subscrição do Azure. Este requisito é consistente com outros objetos cobrar no Azure.

Se o SAP HANA no Azure (instâncias grandes) é implementado em várias regiões do Azure diferentes, um inquilino separado é implementado no carimbo de data / instância grande. Pode executar ambos na mesma subscrição do Azure, desde que estas instâncias são parte do mesmo ambiente SAP. 

> [!IMPORTANT] 
> Apenas a implementação do Azure Resource Manager é suportada com o SAP HANA no Azure (instâncias grandes).

 

## <a name="additional-virtual-network-information"></a>Informações de rede virtual adicionais

Para ligar uma rede virtual para o ExpressRoute, tem de criar um gateway do Azure. Para obter mais informações, consulte [sobre os gateways de rede virtual para o ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Um gateway do Azure pode ser utilizado com o ExpressRoute para uma infra-estrutura de fora do Azure ou para um carimbo de instâncias grandes do Azure. Um gateway do Azure também pode ser utilizado para estabelecer ligação entre redes virtuais. Para obter mais informações, consulte [configurar uma ligação de rede de rede para o Resource Manager com o PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Pode ligar o gateway do Azure para um máximo de quatro diferentes conexões do ExpressRoute, desde que essas conexões provêm de diferentes routers de limite de enterprise da Microsoft. Para obter mais informações, consulte [infraestrutura de SAP HANA (instâncias grandes) e a conectividade no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> A taxa de transferência que fornece um gateway do Azure é diferente para os dois casos de utilização. Para obter mais informações, consulte [sobre o Gateway de VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). O débito máximo que pode conseguir com um gateway de rede virtual é de 10 Gbps ao utilizar uma ligação do ExpressRoute. Copiar ficheiros entre uma VM que residem numa rede virtual e um sistema no local (como um fluxo de cópia única) não alcançar o débito total do gateway diferente SKUs. Para aproveitar a largura de banda completa do gateway de rede virtual, utilize vários fluxos. Ou, tem de copiar ficheiros diferentes em fluxos paralelos de um único ficheiro.


## <a name="networking-architecture-for-hana-large-instance"></a>Arquitetura de rede para a instância grande do HANA
A arquitetura de rede para a instância grande do HANA pode ser separada em quatro partes distintas:

- Local de rede e a ligação do ExpressRoute para o Azure. Esta parte é o domínio do cliente e está ligada ao Azure através do ExpressRoute. Consulte a parte inferior direita na figura a seguir.
- Serviços de rede do Azure, conforme discutidos anteriormente, com redes virtuais, que novamente tem gateways. Esta parte é uma área em que precisa localizar os designs apropriados para os seus requisitos de aplicações, segurança e os requisitos de conformidade. Se utilizar a instância grande do HANA é outro ponto a considerar em termos de número de redes virtuais e os SKUs de gateway do Azure à sua escolha. Veja o canto superior direito da figura.
- Conectividade de instância grande do HANA através de tecnologia de ExpressRoute para o Azure. Esta parte é implementada e processada pela Microsoft. Tudo que precisa fazer é fornecer alguns intervalos de endereços IP após a implementação de seus ativos na instância grande do HANA ligarem o circuito do ExpressRoute para as redes virtuais. Para obter mais informações, consulte [infraestrutura de SAP HANA (instâncias grandes) e a conectividade no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- Funcionamento em rede na instância grande do HANA, que é principalmente transparente para.

![Rede virtual ligada ao SAP HANA no Azure (instâncias grandes) e no local](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

O requisito de que seus ativos no local tem de se ligar através do ExpressRoute para o Azure não muda o fato de usar a instância grande do HANA. O requisito de ter uma ou várias redes virtuais que executam as VMs que alojam a camada da aplicação que liga para o HANA nas instâncias alojada em unidades de instância grande do HANA, também não é alterado. 

As diferenças para implementações de SAP no Azure são:

- As unidades de instância grande do HANA do seu inquilino do cliente estão ligadas através de outro circuito do ExpressRoute em suas redes virtuais. Para separar as condições de carga, o local para ligações do ExpressRoute de redes virtuais e as ligações entre redes virtuais e a instância grande do HANA não partilha os routers da mesmos.
- O perfil de carga de trabalho entre a camada de aplicação SAP e a instância grande do HANA é de natureza diferente, com muitas solicitações pequenas e o aumento, como transferências de dados (conjuntos de resultados) da SAP HANA para a camada de aplicativo.
- A arquitetura de aplicação SAP é mais sensível à latência de rede que cenários típicos onde os dados são trocados entre no local e o Azure.
- O gateway de rede virtual tem, pelo menos, duas ligações do ExpressRoute. Ambas as ligações partilham a largura de banda máxima para os dados recebidos do gateway de rede virtual.

A latência de rede teve entre VMs e instâncias grandes do HANA unidades podem ser mais do que um típica latência ida e volta de rede de VM para VM. Depende da região do Azure, os valores de medida podem exceder a latência de ida e volta de 0,7 ms classificada como abaixo da média em [1100926 no SAP Note # - FAQ: Desempenho de rede](https://launchpad.support.sap.com/#/notes/1100926/E). Dependente de região do Azure e a ferramenta para medir a latência de ida e volta de rede entre uma VM do Azure e a unidade de instância grande do HANA, a latência de medida pode ser até e em torno de 2 milissegundos. No entanto, os clientes implementar aplicações de SAP de produção com base em SAP HANA com êxito na instância grande do SAP HANA. Certifique-se de que testar os processos empresariais minuciosamente em instâncias grandes do HANA do Azure.
 
Para fornecer a latência de rede determinística entre VMs e instâncias grandes do HANA, a escolha do gateway de rede virtual SKU é essencial. Ao contrário dos padrões de tráfego entre VMs e no local, o padrão de tráfego entre VMs e instâncias grandes do HANA pode desenvolver pequenas mas elevadas rajadas de volumes de pedidos e dados a serem transmitidos. Para lidar com essas extrapolações bem, recomendamos vivamente a utilização do SKU de gateway de UltraPerformance. Para a classe de tipo II de SKUs de instância grande do HANA, a utilização do SKU de gateway de UltraPerformance como um gateway de rede virtual é obrigatória.

> [!IMPORTANT] 
> Tendo em conta o tráfego de rede geral entre o aplicativo SAP e camadas de base de dados, apenas o HighPerformance ou SKUs de gateway de UltraPerformance para redes virtuais são suportados para ligar ao SAP HANA no Azure (instâncias grandes). Para HANA grandes instância de tipo II SKUs, apenas o SKU do gateway UltraPerformance é suportado como um gateway de rede virtual.



## <a name="single-sap-system"></a>Único sistema SAP

A infraestrutura no local mostrada anteriormente está ligada através do ExpressRoute para o Azure. O circuito do ExpressRoute liga-se num router de periferia de empresa. Para obter mais informações, consulte [visão geral técnica do ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Depois da rota é estabelecida, liga-se para o backbone do Azure, e todas as regiões do Azure estão acessíveis.

> [!NOTE] 
> Para executar cenários SAP no Azure, ligue o router de periferia de empresa mais próxima da região do Azure no ambiente SAP. Carimbos de instâncias grandes do Azure estão ligados através de dispositivos de router de limite de enterprise dedicado para minimizar a latência de rede entre VMs na carimbos de IaaS do Azure e a instância grande.

O gateway de rede virtual para as VMs que alojam as instâncias da aplicação SAP está ligado ao circuito do ExpressRoute. Da mesma rede virtual está ligada a um router de limite de enterprise separado dedicado à ligação a carimbos de data / instância grande.

Este sistema é um exemplo simples de um único sistema SAP. Camada de aplicação SAP está alojada no Azure. A base de dados do SAP HANA é executado no SAP HANA no Azure (instâncias grandes). A pressuposição é de que a largura de banda de gateway de rede virtual de 2 Gbps ou 10 Gbps débito não representa um afunilamento.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Vários sistemas SAP ou sistemas grandes de SAP

Se vários sistemas SAP ou sistemas de grande dimensão SAP forem implementados para ligar ao SAP HANA no Azure (instâncias grandes), o débito do gateway de rede virtual pode se tornar um afunilamento. Nesse caso, divida as camadas do aplicativo em várias redes virtuais. Também pode criar uma rede virtual especial que se liga a instância grande do HANA para casos como:

- Efetuar cópias de segurança diretamente a partir do HANA nas instâncias na instância grande do HANA para uma VM no Azure que aloja partilhas NFS.
- Copiar as cópias de segurança grandes ou outros ficheiros de unidades de instância grande do HANA para espaço de disco gerido no Azure.

Utilize uma rede virtual separada para as VMs que gerir o armazenamento do anfitrião. Essa organização evita os efeitos de arquivo grande ou transferência de dados de instância grande do HANA para o Azure no gateway de rede virtual que serve de VMs que executam a camada de aplicação SAP. 

Para uma arquitetura de rede mais escalonável:

- Tire partido de várias redes virtuais numa camada de aplicação SAP única e maior.
- Implemente uma rede virtual separada para cada sistema SAP implementado, em comparação com a combinação desses sistemas SAP em sub-redes separadas na mesma rede virtual.

  Uma arquitetura de rede mais escalonável para o SAP HANA no Azure (instâncias grandes):

![Implementar a camada de aplicação SAP através de várias redes virtuais](./media/hana-overview-architecture/image4-networking-architecture.png)

A figura mostra a camada de aplicação SAP ou a componentes, implantados através de várias redes virtuais. Esta configuração introduziu a sobrecarga de latência inevitável que ocorreram durante a comunicação entre as aplicações alojadas nessas redes virtuais. Por predefinição, o tráfego de rede entre VMs localizadas na rota de redes virtuais diferentes por meio dos routers de limite de enterprise nesta configuração. É a forma de otimizar e reduzir a latência de comunicação entre duas redes virtuais através do peering de redes virtual na mesma região. Este método funciona mesmo que essas redes virtuais estejam em subscrições diferentes. Com o peering de rede virtual, a comunicação entre VMs em duas redes virtuais diferentes pode utilizar o backbone de rede do Azure para comunicar diretamente entre si. Latência mostra como se as VMs estão na mesma rede virtual. O tráfego que reflete os intervalos de endereços IP que estão ligados através do gateway de rede virtual do Azure é encaminhado através do gateway de rede virtual individual da rede virtual. 

Para obter mais informações sobre o peering de rede virtual, consulte [peering de rede Virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Encaminhamento do Azure

Três considerações de encaminhamento de rede são importantes para o SAP HANA no Azure (instâncias grandes):

* SAP HANA no Azure (instâncias grandes) podem ser acedido apenas por meio de VMs e a ligação dedicada do ExpressRoute, não diretamente a partir de locais. Acesso direto no local para as unidades de instância grande do HANA, conforme fornecido pela Microsoft, não é possível imediatamente. As restrições de encaminhamento transitivas são devido à arquitetura de rede do Azure atual utilizada para a instância grande do SAP HANA. Alguns clientes da administração e todas as aplicações que precisam direcionar o acesso, como o SAP solução Manager em execução no local, não é possível ligar à base de dados SAP HANA.

* Se tiver implementadas em duas regiões do Azure diferentes para recuperação após desastre de unidades de instância grande do HANA, as restrições de encaminhamento transitórias se aplicam. Em outras palavras, os endereços IP de uma unidade de instância grande do HANA numa única região (por exemplo, e.u.a. Centro-Oeste) não são encaminhados para uma unidade de instância grande do HANA implementada noutra região (por exemplo, E.u.a. Leste). Esta restrição é independente do uso de peering entre regiões ou ligar os circuitos do ExpressRoute que se ligam a unidades de instância grande do HANA para redes virtuais entre a rede do Azure. Para uma representação gráfica, consulte a figura na secção "Instância grande do HANA de utilização de unidades em várias regiões." Esta restrição, que vem com a arquitetura de implementada, proíbe o uso imediato do HANA System Replication como funcionalidade de recuperação após desastre.

* SAP HANA nas unidades do Azure (instâncias grandes) tem um endereço IP atribuído o intervalo de endereços de conjunto IP de servidor enviada. Para obter mais informações, consulte [infraestrutura de SAP HANA (instâncias grandes) e a conectividade no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Este endereço IP está acessível através de subscrições do Azure e ExpressRoute que se liga a redes virtuais para HANA no Azure (instâncias grandes). O endereço IP atribuído de que o intervalo de endereços do conjunto IP do servidor é diretamente atribuído para a unidade de hardware. Ele possui *não* atribuída através de NAT, como era o caso nas Implantações primeiro desta solução. 

> [!NOTE]
> Para superar a restrição no encaminhamento transitório, conforme explicado nos primeiros itens de lista de dois, use componentes adicionais para o encaminhamento. Componentes que podem ser usados para superar a restrição podem ser:
> 
> * Um proxy inverso para encaminhar os dados, de e para. Por exemplo, F5 BIG-IP, o NGINX com o Gestor de tráfego seja implementado no Azure como uma solução de encaminhamento de tráfego/firewall virtual.
> * Usando [regras de IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) numa VM do Linux para ativar o encaminhamento entre localizações no local e unidades de instância grande do HANA ou entre unidades de instância grande do HANA em regiões diferentes.
> 
> Lembre-se de que o suporte para soluções personalizadas que envolvem de terceiros e de implementação de aplicações de rede ou IPTables não foi fornecido pela Microsoft. Suporte deve ser fornecido pelo fornecedor do componente utilizado ou o integrador de. 

## <a name="internet-connectivity-of-hana-large-instance"></a>Conectividade de Internet da instância grande do HANA
Instância grande do HANA faz *não* tem conectividade internet direta. Por exemplo, esta limitação pode restringir a capacidade de registar a imagem do SO diretamente com o fornecedor do sistema operacional. Talvez precise trabalhar com o seu servidor local da ferramenta de gestão de subscrição do SUSE Linux Enterprise Server ou o Gestor de subscrições do Red Hat Enterprise Linux.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Encriptação de dados entre VMs e instâncias grandes do HANA
Dados transferidos entre a instância grande do HANA e VMs não estão encriptados. No entanto, apenas para a troca entre o lado do HANA DBMS e as aplicações baseadas em JDBC/ODBC, pode ativar a encriptação de tráfego. Para obter mais informações, consulte [esta documentação pela SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Utilizar unidades de instância grande do HANA em várias regiões

Talvez tenha motivos para implantar o SAP HANA no Azure (instâncias grandes) em várias regiões do Azure diferente para recuperação após desastre. Talvez deseja acessar a instância grande do HANA de cada uma das VMs implementadas em redes virtuais diferentes nas regiões. Os endereços IP atribuídos a diferentes unidades de instância grande do HANA não serão propagados para além de redes virtuais que estejam diretamente ligadas através do seu gateway para as instâncias. Como resultado, uma pequena alteração é introduzida para o design de rede virtual. Um gateway de rede virtual pode lidar com quatro diferentes circuitos do ExpressRoute fora de routers de periferia de empresa diferente. Cada rede virtual que está ligada a um dos carimbos de data / instância grande pode ser ligada para o carimbo de data / instância grande noutra região do Azure.

![Rede virtual ligado a carimbos de instâncias grandes do Azure em diferentes regiões do Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

A figura mostra como as redes virtuais diferentes em ambas as regiões estão ligadas a dois circuitos do ExpressRoute diferentes que são utilizados para ligar ao SAP HANA no Azure (instâncias grandes) em ambas as regiões do Azure. As recém-lançado as ligações são as linhas vermelhas retangulares. Com estas ligações, de redes virtuais, as VMs em execução em uma dessas redes virtuais podem aceder a cada um dos diferentes unidades de instância grande do HANA implementadas em duas regiões. Conforme mostrado na figura, presume que tem duas ligações do ExpressRoute no local para as duas regiões do Azure. Essa disposição é recomendada por motivos de recuperação após desastre.

> [!IMPORTANT] 
> Se utilizou vários circuitos do ExpressRoute, prefixação como caminho e as definições de BGP de preferência Local devem ser utilizadas para garantir que o encaminhamento adequado de tráfego.

**Passos seguintes?**
- Consulte [arquitetura de armazenamento do SAP HANA (instâncias grandes)](hana-storage-architecture.md)