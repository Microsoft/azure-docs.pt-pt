---
title: Arquitetura de rede de SAP HANA em Azure (Grandes Instâncias) | Microsoft Docs
description: Arquitetura de rede de como implantar SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 92cfa5b2e399811754b57bda64569753ccfa6be8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668763"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA (Grandes Instâncias) arquitetura de rede

A arquitetura dos serviços de rede Azure é um componente fundamental da implementação bem sucedida de aplicações SAP em HANA Large Instance. Tipicamente, as implementações SAP HANA on Azure (Grandes Instâncias) têm uma paisagem SAP maior com várias soluções SAP diferentes com diferentes tamanhos de bases de dados, consumo de recursos de CPU e utilização de memória. É provável que nem todos os sistemas de TI já estejam localizados em Azure. A sua paisagem SAP é muitas vezes híbrida também do ponto de vista do DBMS e do ponto de vista da aplicação SAP usando uma mistura de NetWeaver, e S/4HANA e SAP HANA e outros DBMS. O Azure oferece diferentes serviços que lhe permitem executar os diferentes sistemas DBMS, NetWeaver e S/4HANA em Azure. O Azure também lhe oferece tecnologia de rede para fazer o Azure parecer um centro de dados virtual para as suas implementações de software no local

A menos que os seus sistemas de TI completos estejam alojados em Azure. A funcionalidade de networking Azure é usada para ligar o mundo no local com os seus ativos Azure para fazer com que o Azure pareça um datacenter virtual do seu. A funcionalidade da rede Azure utilizada é: 

- As redes virtuais Azure estão ligadas ao circuito [ExpressRoute](https://azure.microsoft.com/services/expressroute/) que se conecta aos seus ativos de rede no local.
- Um circuito ExpressRoute que ligue as instalações ao Azure deve ter uma largura de banda mínima igual ou superior a [1 Gbps](https://azure.microsoft.com/pricing/details/expressroute/). Esta largura de banda mínima permite uma largura de banda adequada para a transferência de dados entre sistemas e sistemas no local que funcionam em VMs. Também permite uma largura de banda adequada para a ligação aos sistemas Azure de utilizadores no local.
- Todos os sistemas SAP em Azure são criados em redes virtuais para comunicar uns com os outros.
- O Ative Directory e o DNS alojados no local são estendidos para Azure através do ExpressRoute a partir do local, ou estão a ser concluídos em Azure.

Para o caso específico de integrar HANA Large Instances no tecido de rede Azure data center, a tecnologia Azure ExpressRoute também é utilizada


> [!NOTE] 
> Apenas uma subscrição da Azure pode ser ligada a apenas um inquilino num carimbo HANA Large Instance numa região específica de Azure. Inversamente, um único inquilino de selo HANA Large Instance pode ser ligado a apenas uma subscrição da Azure. Este requisito é consistente com outros objetos de faturação em Azure.

Se o SAP HANA em Azure (Grandes Instâncias) for implantado em várias regiões de Azure diferentes, um inquilino separado é implantado no carimbo HANA Large Instance. Pode executar ambos sob a mesma subscrição Azure, desde que estes casos sejam parte da mesma paisagem SAP. 

> [!IMPORTANT] 
> Apenas o método de implementação do Gestor de Recursos Azure é suportado com SAP HANA em Azure (Grandes Instâncias).

 

## <a name="additional-virtual-network-information"></a>Informações adicionais da rede virtual

Para ligar uma rede virtual ao ExpressRoute, deve ser criado um gateway Azure ExpressRoute. Para mais informações, consulte [as portas de passagem da Express para o ExpressRoute.](../../../expressroute/expressroute-about-virtual-network-gateways.md) 

Um gateway Azure ExpressRoute é usado com ExpressRoute para uma infraestrutura fora de Azure ou para um carimbo Azure Large Instance. Pode ligar o gateway Azure ExpressRoute a um máximo de quatro circuitos ExpressRoute diferentes, desde que essas ligações venham de diferentes routers de borda da empresa da Microsoft. Para obter mais informações, consulte [a infraestrutura SAP HANA (Grandes Instâncias) e a conectividade em Azure.](hana-overview-infrastructure-connectivity.md) 

> [!NOTE] 
> A produção máxima que se pode obter com um gateway ExpressRoute é de 10 Gbps utilizando uma ligação ExpressRoute. Copiar ficheiros entre um VM que reside numa rede virtual e um sistema no local (como um único fluxo de cópia) não consegue o rendimento completo dos diferentes SKUs de gateway. Para aproveitar a largura de banda completa do gateway ExpressRoute, utilize vários fluxos. Ou deve copiar diferentes ficheiros em fluxos paralelos de um único ficheiro.


## <a name="networking-architecture-for-hana-large-instance"></a>Arquitetura de networking para HANA Large Instance
A arquitetura de networking para HANA Large Instance pode ser separada em quatro partes diferentes:

- Ligação em rede no local e ExpressRoute à Azure. Esta parte é domínio do cliente e está ligada ao Azure através do ExpressRoute. Este circuito Expressroute é totalmente pago por si como cliente. A largura de banda deve ser suficientemente grande para lidar com o tráfego de rede entre os seus ativos no local e a região de Azure contra a qual está a ligar. Veja o lado direito inferior na seguinte figura.
- Serviços de rede Azure, como anteriormente discutido, com redes virtuais, que precisam novamente de gateways ExpressRoute adicionados. Esta parte é uma área onde você precisa encontrar os projetos apropriados para seus requisitos de aplicação, segurança e conformidade. Se você usa HANA Large Instance é outro ponto a considerar em termos do número de redes virtuais e SKUs de gateway Azure para escolher. Veja a parte superior direita na figura.
- Conectividade da HANA Large Instance através da tecnologia ExpressRoute em Azure. Esta peça é implementada e tratada pela Microsoft. Tudo o que precisa de fazer é fornecer algumas gamas de endereços IP após a implantação dos seus ativos em HANA Large Instance ligar o circuito ExpressRoute às redes virtuais. Para obter mais informações, consulte [a infraestrutura SAP HANA (Grandes Instâncias) e a conectividade em Azure.](hana-overview-infrastructure-connectivity.md) Não existe qualquer taxa adicional para si como cliente para a conectividade entre o tecido de rede Azure data center e as unidades HANA Large Instance.
- Networking dentro do carimbo HANA Large Instance, que é maioritariamente transparente para si.

![Rede virtual ligada ao SAP HANA em Azure (Grandes Instâncias) e no local](./media/hana-overview-architecture/image1-architecture.png)

A exigência de que os seus ativos no local devem ligar-se através do ExpressRoute à Azure não muda porque utiliza a HANA Large Instance. A exigência de ter uma ou múltiplas redes virtuais que executam os VMs, que acolhem a camada de aplicação que se conecta às instâncias HANA hospedadas nas unidades HANA Large Instance, também não muda. 

As diferenças para as implementações da SAP em Azure são:

- As unidades HANA Large Instance do seu inquilino cliente estão ligadas através de outro circuito ExpressRoute nas suas redes virtuais. Para separar as condições de carga, os circuitos ExpressRoute da rede virtual Azure e os circuitos entre as redes virtuais Azure e as Grandes Instâncias da HANA não partilham os mesmos routers.
- O perfil de carga de trabalho entre a camada de aplicação SAP e a HANA Large Instance é de natureza diferente, com muitos pequenos pedidos e explosões como transferências de dados (conjuntos de resultados) do SAP HANA para a camada de aplicação.
- A arquitetura de aplicações SAP é mais sensível à latência da rede do que os cenários típicos onde os dados são trocados entre as instalações e o Azure.
- O gateway Azure ExpressRoute tem pelo menos duas ligações ExpressRoute. Um circuito que está ligado a partir do local e um que está ligado a partir de HANA Large Instances. Isto deixa apenas espaço para mais dois circuitos adicionais de diferentes MSEEs para se conectar em ExpressRoute Gateway. Esta restrição é independente do uso do Caminho Rápido ExpressRoute. Todos os circuitos conectados partilham a largura de banda máxima para os dados de entrada do gateway ExpressRoute.

Com a Revisão 3 dos selos HANA Large Instance, a latência da rede experimentada entre as unidades VMs e HANA Large Instance pode ser superior a uma latência típica de ida e volta de vm-a-VM. Dependentes da região de Azure, os valores medidos podem exceder a latência de ida e volta de 0,7 ms classificada como abaixo da média em [#1100926 SAP Nota - FAQ: Desempenho da rede](https://launchpad.support.sap.com/#/notes/1100926/E). Dependente da Região de Azure e ferramenta para medir a latência de ida e volta entre uma unidade Azure VM e HANA Large Instance, a latência medida pode ser de cerca de 2 milissegundos. No entanto, os clientes implantam aplicações SAP de produção baseadas em SAP HANA com sucesso na SAP HANA Large Instance. Certifique-se de testar cuidadosamente os seus processos de negócio em Azure HANA Large Instance. Uma nova funcionalidade, chamada ExpressRoute Fast Path, é capaz de reduzir a latência da rede entre HANA Large Instances e VMs de camada de aplicação em Azure substancialmente (ver abaixo). 

Com a Revisão 4 dos selos HANA Large Instance, a latência da rede entre VMs Azure que são implantados nas proximidades do carimbo HANA Large Instance, é experimentada para atender a classificação média ou superior à média, como documentado em [#1100926 SAP Nota - FAQ: Desempenho da rede](https://launchpad.support.sap.com/#/notes/1100926/E) se o Caminho Rápido Azure ExpressRoute estiver configurado (ver abaixo). Para implantar VMs Azure nas proximidades das unidades HANA Large Instance da Revisão 4, é necessário alavancar os Grupos de Colocação de [Proximidade do Azure](../../co-location.md). A forma como os grupos de colocação de proximidade podem ser usados para localizar a camada de aplicação SAP no mesmo centro de dados Azure como revisão 4 unidades HANA Large Instance é descrita em [Grupos de Colocação de Proximidade Azure para uma latência de rede ideal com aplicações SAP](sap-proximity-placement-scenarios.md).

Para fornecer latência de rede determinística entre VMs e HANA Large Instance, a escolha do gateway ExpressoRoute SKU é essencial. Ao contrário dos padrões de tráfego entre as instalações e os VMs, o padrão de tráfego entre VMs e HANA Large Instance pode desenvolver pequenas, mas elevadas explosões de pedidos e volumes de dados a serem transmitidos. Para lidar bem com estas explosões, recomendamos vivamente a utilização do Gateway UltraPerformance SKU. Para a classe Tipo II de SKUs de grande instância HANA, a utilização do gateway UltraPerformance SKU como porta de entrada ExpressRoute é obrigatória.

> [!IMPORTANT] 
> Dado o tráfego global de rede entre a aplicação SAP e as camadas de base de dados, apenas os SKUs de gateway highPerformance ou UltraPerformance para redes virtuais são suportados para a ligação ao SAP HANA em Azure (Grandes Instâncias). Para os SKUs do tipo II de grande instância HANA, apenas o gateway UltraPerformance SKU é suportado como porta de entrada ExpressRoute. As exceções aplicam-se quando se utiliza o Caminho Rápido ExpressRoute (ver abaixo)

### <a name="expressroute-fast-path"></a>Via rápida ExpressRoute
Para reduzir a latência, o ExpressRoute Fast Path foi introduzido e lançado em maio de 2019 para a conectividade específica da HANA Large Instances para as redes virtuais Azure que acolhem os VMs da aplicação SAP. A grande diferença para a solução lançada até agora é que os fluxos de dados entre VMs e HANA Large Instances já não são encaminhados através do gateway ExpressRoute. Em vez disso, os VMs atribuídos nas sub-redes(s) da rede virtual Azure estão a comunicar diretamente com o router de bordas da empresa dedicado. 

> [!IMPORTANT] 
> A funcionalidade ExpressRoute Fast Path requer que as sub-redes que executam os VMs da aplicação SAP estejam na mesma rede virtual Azure que se ligou às Grandes Instâncias HANA. Os VMs localizados em redes virtuais Azure que são espreitados com a rede virtual Azure ligada diretamente às unidades HANA Large Instance não estão a beneficiar do Caminho Rápido ExpressRoute. Como resultado, os modelos típicos de rede virtual e hub, onde os circuitos ExpressRoute estão a ligar-se a uma rede virtual do hub e as redes virtuais que contêm a camada de aplicação SAP (porta-vozes) estão a ser espreitadas, a otimização por ExpressRoute Fast Path não funcionará. Em addtion, ExpressRoute Fast Path não suporta regras de encaminhamento definidas pelo utilizador (UDR) hoje em dia. Para mais informações, consulte [o Portal de Rede Virtual ExpressRoute e o FastPath.](../../../expressroute/expressroute-about-virtual-network-gateways.md) 


Para obter mais detalhes sobre como configurar o Caminho Rápido ExpressRoute, leia o documento [Ligue uma rede virtual a grandes instâncias HANA](./hana-connect-vnet-express-route.md).    

> [!NOTE]
> Um gateway UltraPerformance ExpressRoute é necessário para ter o Caminho Rápido ExpressRoute funcionando


## <a name="single-sap-system"></a>Sistema SAP único

A infraestrutura no local anteriormente mostrada está ligada através do ExpressRoute em Azure. O circuito ExpressRoute liga-se a um router de borda da empresa Microsoft (MSEE). Para mais informações, consulte a [visão técnica do ExpressRoute.](../../../expressroute/expressroute-introduction.md) Após a rota ser estabelecida, liga-se à espinha dorsal do Azure.

> [!NOTE] 
> Para executar paisagens SAP em Azure, ligue-se ao router de borda empresarial mais próximo da região de Azure na paisagem SAP. Os selos HANA Large Instance são conectados através de dispositivos de router de bordas empresariais dedicados para minimizar a latência da rede entre os selos VMs em Azure IaaS e HANA Large Instance.

O gateway ExpressRoute para os VMs que acolhem instâncias de aplicação SAP estão ligados a um circuito ExpressRoute que se conecta ao local. A mesma rede virtual está ligada a um router de bordas empresariais separado dedicado à ligação aos selos de Grande Instância. Utilizando o Via Rápida ExpressRoute, os dados fluem de HANA Large Instances para a camada de aplicação SAP VMs já não são encaminhados através do gateway ExpressRoute e com isso reduzem a latência de ida e volta da rede.

Este sistema é um exemplo simples de um único sistema SAP. A camada de aplicação SAP está hospedada em Azure. A base de dados SAP HANA funciona em SAP HANA on Azure (Grandes Instâncias). O pressuposto é que a largura de banda expressRoute gateway de 2-Gbps ou 10 Gbps não representa um estrangulamento.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Múltiplos sistemas SAP ou grandes sistemas SAP

Se vários sistemas SAP ou grandes sistemas SAP forem implantados para ligar ao SAP HANA em Azure (Grandes Instâncias), a produção do gateway ExpressRoute pode tornar-se um estrangulamento. Ou quer isolar sistemas de produção e não produção em diferentes redes virtuais do Azure. Neste caso, divida as camadas de aplicação em várias redes virtuais. Também pode criar uma rede virtual especial que se conecta à HANA Large Instance para casos como:

- Executando backups diretamente das instâncias HANA em HANA Large Instance para um VM em Azure que acolhe ações NFS.
- Copiar grandes cópias de segurança ou outros ficheiros de unidades HANA Large Instance para o espaço de disco gerido em Azure.

Utilize uma rede virtual separada para hospedar VMs que gerem o armazenamento para transferência em massa de dados entre HANA Large Instances e Azure. Este arranjo evita os efeitos de um grande ficheiro ou transferência de dados da HANA Large Instance para Azure no gateway ExpressRoute que serve os VMs que executam a camada de aplicação SAP. 

Para uma arquitetura de rede mais escalável:

- Aproveite várias redes virtuais para uma única camada de aplicação SAP maior.
- Implementar uma rede virtual separada para cada sistema SAP implantado, em comparação com a combinação destes sistemas SAP em sub-redes separadas sob a mesma rede virtual.

  Uma arquitetura de networking mais escalável para SAP HANA em Azure (Grandes Instâncias):

![Implementar camada de aplicação SAP sobre várias redes virtuais](./media/hana-overview-architecture/image4-networking-architecture.png)

Dependendo das regras e restrições, pretende aplicar-se entre as diferentes redes virtuais que hospedam VMs de diferentes sistemas SAP, deve espreitar essas redes virtuais. Para obter mais informações sobre o espreitamento da rede virtual, consulte [a rede Virtual a espreitar.](../../../virtual-network/virtual-network-peering-overview.md)


## <a name="routing-in-azure"></a>Encaminhamento em Azure

Por utilização predefinida, três considerações de encaminhamento de rede são importantes para o SAP HANA em Azure (Grandes Instâncias):

* O SAP HANA on Azure (Grandes Instâncias) só pode ser acedido através de VMs Azure e da ligação ExpressRoute dedicada, e não diretamente a partir do local. O acesso direto das unidades HANA Large Instance, como lhe foi entregue pela Microsoft, não é possível imediatamente. As restrições de encaminhamento transitório devem-se à arquitetura atual da rede Azure utilizada para a SAP HANA Large Instance. Alguns clientes da administração e quaisquer aplicações que necessitem de acesso direto, como o SAP Solution Manager que funciona no local, não podem ligar-se à base de dados SAP HANA. Para exceções, consulte a secção "Encaminhamento Direto para HANA Grandes Instâncias".

* Se tiver unidades HANA Large Instance implantadas em duas regiões diferentes de Azure para recuperação de desastres, as mesmas restrições de encaminhamento transitórios aplicadas no passado. Por outras palavras, os endereços IP de uma unidade de grande instância HANA numa região (por exemplo, EUA Oeste) não foram encaminhados para uma unidade de grande instância HANA implantada noutra região (por exemplo, o Leste dos EUA). Esta restrição era independente da utilização da rede Azure que espreitava por regiões ou fazia a ligação cruzada dos circuitos ExpressRoute que ligam as unidades HANA Large Instance a redes virtuais. Para uma representação gráfica, consulte a figura na secção "Use unidades de grande instância HANA em várias regiões". Esta restrição, que veio com a arquitetura implantada, proibiu o uso imediato da Replicação do Sistema HANA como funcionalidade de recuperação de desastres. Para alterações recentes, procure a secção "Use unidades de grande instância HANA em várias regiões". 

* As unidades SAP HANA em Azure (Grandes Instâncias) têm um endereço IP atribuído a partir do intervalo de endereços IP do servidor que submeteu ao solicitar a implantação da HANA Large Instance. Para obter mais informações, consulte [a infraestrutura SAP HANA (Grandes Instâncias) e a conectividade em Azure.](hana-overview-infrastructure-connectivity.md) Este endereço IP é acessível através das subscrições e circuitoS Azure que ligam redes virtuais Azure a HANA Large Instances. O endereço IP atribuído fora do intervalo de endereços IP do servidor é diretamente atribuído à unidade de hardware. Já *não* é atribuído através da NAT, como foi o caso nas primeiras implementações desta solução. 

### <a name="direct-routing-to-hana-large-instances"></a>Encaminhamento direto para HANA Grandes Instâncias

Por padrão, o encaminhamento transitório não funciona nestes cenários:

* Entre unidades de grande instância HANA e uma implantação no local.

* Entre o encaminhamento HANA Large Instance que são implantados em duas regiões diferentes.

Existem três formas de permitir o encaminhamento transitório nesses cenários:

- Um representante invertido para encaminhar dados, de e para. Por exemplo, F5 BIG-IP, NGINX com Gestor de Tráfego implantado na rede virtual Azure que se conecta a HANA Large Instances e a instalações como uma solução virtual de firewall/traffic.
- Utilizando as regras do IPTables num VM Linux para permitir o encaminhamento entre locais no local e unidades de grande instância HANA, ou entre unidades de Grande Instância HANA em diferentes regiões. O VM que executa os IPTables tem de ser implantado na rede virtual Azure que se conecta a HANA Large Instances e a no local. O VM precisa de ser dimensionado em conformidade, de modo a que o rendimento da rede do VM seja suficiente para o tráfego de rede esperado. Para obter mais informações sobre a largura de banda da rede VM, verifique o artigo [Tamanhos das máquinas virtuais Linux em Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [A Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) seria outra solução para permitir o tráfego direto entre as unidades de hana large. 

Todo o tráfego destas soluções seria encaminhado através de uma rede virtual Azure e, como tal, o tráfego poderia ser adicionalmente restringido pelos aparelhos macios utilizados ou pelos Grupos de Segurança da Rede Azure, pelo que certos endereços IP ou endereços IP podem ser bloqueados ou explicitamente autorizados a aceder a HANA Large Instances. 

> [!NOTE]  
> Esteja ciente de que a implementação e suporte para soluções personalizadas que envolvam aparelhos de rede de terceiros ou IPTables não é fornecido pela Microsoft. O suporte deve ser fornecido pelo fornecedor do componente utilizado ou pelo integrador. 

#### <a name="express-route-global-reach"></a>Alcance Global da Rota Expresso
A Microsoft introduziu uma nova funcionalidade chamada [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md). Global Reach pode ser usado para HANA Grandes Instâncias em dois cenários:

- Permitir o acesso direto das instalações às suas unidades de Grande Instância HANA implantadas em diferentes regiões
- Ativar a comunicação direta entre as suas unidades de Grande Instância HANA implantadas em diferentes regiões


##### <a name="direct-access-from-on-premises"></a>Acesso direto a partir de instalações
Nas regiões Azure onde o Global Reach é oferecido, pode solicitar ativar a funcionalidade Global Reach para o seu circuito ExpressRoute que liga a sua rede no local à rede virtual Azure que também se conecta às suas unidades HANA Large Instance. Existem algumas implicações de custos para o lado no local do seu circuito ExpressRoute. Para obter preços, verifique os preços do [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/). Não existem custos adicionais para si relacionados com o circuito que liga a unidade(s) HANA Large Instance ao Azure. 

> [!IMPORTANT]  
> No caso de utilizar o Global Reach para permitir o acesso direto entre as suas unidades HANA Large Instance e os ativos no local, os dados de rede e o fluxo de controlo não são **encaminhados através de redes virtuais Azure**, mas diretamente entre os routers de câmbio da Empresa Microsoft. Como resultado, quaisquer regras NSG ou ASG, ou qualquer tipo de firewall, NVA, ou proxy que tenha implementado numa rede virtual Azure, não estão a ser tocadas. **Se utilizar o ExpressRoute Global Reach para permitir o acesso direto das instalações à HANA Restrições e permissões de unidades de grande instância para aceder a unidades de grande instância HANA devem ser definidas em firewalls do lado do local** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Ligação HANA Grandes Instâncias em diferentes regiões do Azure
Da mesma forma, como o ExpressRoute Global Reach pode ser usado para ligar no local às unidades HANA Large Instance, pode ser usado para ligar dois inquilinos HANA Large Instance que são implantados para você em duas regiões diferentes. O isolamento são os circuitos ExpressRoute que os seus inquilinos da HANA Large Instance estão a usar para se ligarem ao Azure em ambas as regiões. Não existem taxas adicionais para ligar dois inquilinos da HANA Large Instance que são implantados em duas regiões diferentes. 

> [!IMPORTANT]  
> O fluxo de dados e o fluxo de controlo do tráfego de rede entre os diferentes inquilinos de grande instância HANA não serão encaminhados através de redes azuis. Como resultado, você não pode usar a funcionalidade Azure ou NVAs para impor restrições de comunicação entre seus dois inquilinos HANA Large Instances. 

Para obter mais detalhes sobre como obter o ExpressRoute Global Reach ativado, leia o documento [Ligue uma rede virtual a grandes instâncias HANA](./hana-connect-vnet-express-route.md).


## <a name="internet-connectivity-of-hana-large-instance"></a>Conectividade na Internet da HANA Large Instance
Hana Large Instance *não* tem conectividade direta na Internet. Como exemplo, esta limitação pode restringir a sua capacidade de registar a imagem do SO diretamente com o fornecedor de SO. Poderá ter de trabalhar com o servidor local da Ferramenta de Gestão de Subscrição do Servidor da Empresa SUSE Linux ou com o Gestor de Assinaturas Linux da Red Hat Enterprise.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Encriptação de dados entre VMs e HANA Large Instance
Os dados transferidos entre HANA Large Instance e VMs não são encriptados. No entanto, apenas para a troca entre o lado HANA DBMS e as aplicações baseadas em JDBC/ODBC, pode permitir a encriptação do tráfego. Para mais informações, consulte [esta documentação da SAP.](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html)

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Utilize unidades de grande instância HANA em várias regiões

Para realizar a recuperação de desastres, é necessário ter unidades de Grande Instância SHANA em várias regiões do Azure. Mesmo com a utilização do Azure [Global Vnet Peering], o encaminhamento transitório por defeito não está a funcionar entre os inquilinos da HANA Large Instance em duas regiões diferentes. No entanto, o Global Reach abre a via de comunicação entre as unidades HANA Large Instance que a provisionou em duas regiões diferentes. Este cenário de utilização do ExpressRoute Global Reach permite:

 - Replicação do sistema HANA sem proxies ou firewalls adicionais
 - Cópia de cópias de segurança entre unidades HANA Large Instance em duas regiões diferentes para executar cópias do sistema ou atualizações do sistema


![Rede virtual ligada aos selos Azure Large Instance em diferentes regiões do Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

A figura mostra como as diferentes redes virtuais em ambas as regiões estão ligadas a dois circuitos ExpressRoute diferentes que são usados para ligar ao SAP HANA em Azure (Grandes Instâncias) em ambas as regiões Azure (linhas cinzentas). A razão para estas duas ligações cruzadas é proteger-se de uma interrupção dos PME de ambos os lados. O fluxo de comunicação entre as duas redes virtuais das duas regiões do Azure deverá ser tratado sobre o [espreitamento global](/archive/blogs/azureedu/how-to-setup-global-vnet-peering-in-azure) das duas redes virtuais nas duas regiões diferentes (linha pontilhada azul). A linha vermelha espessa descreve a ligação ExpressRoute Global Reach, que permite que as unidades HANA Large Instance dos seus inquilinos em duas regiões diferentes se comuniquem entre si. 

> [!IMPORTANT] 
> Se utilizar vários circuitos ExpressRoute, pré-gastos de caminhos AS e definições de BGP de preferência local devem ser utilizados para garantir o encaminhamento adequado do tráfego.

**Próximos passos**
- Consulte [a arquitetura de armazenamento SAP HANA (Grandes Instâncias)](hana-storage-architecture.md)