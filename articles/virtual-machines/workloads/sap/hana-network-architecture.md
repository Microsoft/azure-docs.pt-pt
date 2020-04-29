---
title: Arquitetura de rede da SAP HANA em Azure (Grandes Instâncias) [ Microsoft Docs
description: Arquitetura de rede de como implantar SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b3bc87b183803c0854542d6925af7429b593d2af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605173"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Arquitetura de rede SAP HANA (Grandes Instâncias)

A arquitetura dos serviços de rede Azure é um componente chave da implementação bem sucedida de aplicações SAP em HANA Large Instance. Tipicamente, as implementações do SAP HANA em Azure (Grandes Instâncias) têm uma paisagem SAP maior com várias soluções SAP diferentes com diferentes tamanhos de bases de dados, consumo de recursos CPU e utilização da memória. É provável que nem todos os sistemas informáticos já estejam localizados em Azure. A sua paisagem SAP é muitas vezes híbrida, bem como de um ponto DBMS e do ponto de vista de aplicação SAP, utilizando uma mistura de NetWeaver, s/4HANA e SAP HANA e outros DBMS. A Azure oferece diferentes serviços que lhe permitem executar os diferentes sistemas DBMS, NetWeaver e S/4HANA em Azure. O Azure também oferece tecnologia de rede para fazer o Azure parecer um centro de dados virtual para as suas implementações de software no local

A menos que os seus sistemas completos de TI estejam hospedados em Azure. A funcionalidade de networking Azure é usada para ligar o mundo no local com os seus ativos Azure para fazer com que o Azure pareça um centro de dados virtual seu. A funcionalidade de rede Azure utilizada é: 

- As redes virtuais Azure estão ligadas ao circuito [ExpressRoute](https://azure.microsoft.com/services/expressroute/) que se liga aos seus ativos de rede no local.
- Um circuito ExpressRoute que ligue as instalações ao Azure deve ter uma largura de banda mínima de [1 Gbps ou superior](https://azure.microsoft.com/pricing/details/expressroute/). Esta largura de banda mínima permite a largura de banda adequada para a transferência de dados entre sistemas no local e sistemas que funcionam em VMs. Também permite uma largura de banda adequada para a ligação aos sistemas Azure a partir de utilizadores no local.
- Todos os sistemas SAP em Azure estão configurados em redes virtuais para comunicar uns com os outros.
- O Diretório Ativo e o DNS alojados no local são estendidos ao Azure através da ExpressRoute a partir do local, ou estão a funcionar completos em Azure.

Para o caso específico de integração hana grandes instâncias no tecido da rede de data center Azure, a tecnologia Azure ExpressRoute também é usada


> [!NOTE] 
> Apenas uma subscrição Azure pode ser ligada a apenas um inquilino num carimbo HANA Large Instance numa região específica de Azure. Inversamente, um único inquilino de selos HANA Large Instance pode ser ligado a apenas uma assinatura Azure. Este requisito é consistente com outros objetos de faturação em Azure.

Se o SAP HANA em Azure (Grandes Instâncias) for implantado em várias regiões de Azure diferentes, um inquilino separado é implantado no carimbo hana large instância. Pode executar ambos sob a mesma subscrição Azure, desde que estes casos façam parte da mesma paisagem SAP. 

> [!IMPORTANT] 
> Apenas o método de implementação do Gestor de Recursos Azure é suportado com o SAP HANA em Azure (Grandes Instâncias).

 

## <a name="additional-virtual-network-information"></a>Informações adicionais sobre a rede virtual

Para ligar uma rede virtual ao ExpressRoute, deve ser criado um portal Azure ExpressRoute. Para mais informações, consulte [os gateways da Expressroute para a ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Um gateway Azure ExpressRoute é usado com expressroute para uma infraestrutura fora de Azure ou para um carimbo azure large instância. Pode ligar a porta de entrada Azure ExpressRoute a um máximo de quatro circuitos ExpressRoute diferentes, desde que essas ligações venham de diferentes routers de borda empresarial da Microsoft. Para mais informações, consulte a [infraestrutura SAP HANA (Grandes Instâncias) e a conectividade no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> A entrada máxima que pode obter com um gateway ExpressRoute é de 10 Gbps utilizando uma ligação ExpressRoute. Copiar ficheiros entre um VM que reside numa rede virtual e um sistema no local (como um único fluxo de cópia) não alcança a entrada completa das diferentes SKUs de gateway. Para alavancar a largura de banda completa do gateway ExpressRoute, utilize vários fluxos. Ou deve copiar ficheiros diferentes em fluxos paralelos de um único ficheiro.


## <a name="networking-architecture-for-hana-large-instance"></a>Arquitetura de networking para HANA Grande Instância
A arquitetura de networking para HANA Large Instance pode ser separada em quatro partes diferentes:

- Ligação em rede no local e ligação ExpressRoute ao Azure. Esta peça é o domínio do cliente e está ligada ao Azure através do ExpressRoute. Este circuito Expressroute é totalmente pago por si como cliente. A largura de banda deve ser suficientemente grande para lidar com o tráfego de rede entre os seus ativos no local e a região azure contra a qual está a ligar. Consulte a parte inferior direita na figura seguinte.
- Os serviços de rede Azure, como já foi discutido, com redes virtuais, que voltam a necessitar de gateways ExpressRoute adicionados. Esta peça é uma área onde você precisa encontrar os projetos apropriados para os seus requisitos de aplicação, segurança e requisitos de conformidade. Se utiliza a HANA Large Instance é outro ponto a ter em conta em termos do número de redes virtuais e de Gateway SKUs azure para escolher. Veja a parte superior direita na figura.
- Conectividade da HANA Large Instance através da tecnologia ExpressRoute para Azure. Esta peça é implementada e tratada pela Microsoft. Tudo o que precisa de fazer é fornecer algumas gamas de endereços IP após a implementação dos seus ativos em HANA Large Instance ligar o circuito ExpressRoute às redes virtuais. Para mais informações, consulte a [infraestrutura SAP HANA (Grandes Instâncias) e a conectividade no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Não existe uma taxa adicional para si como cliente pela conectividade entre o tecido da rede de data center Azure e as unidades HANA Large Instance.
- Networking dentro do carimbo HANA Large Instance, que é maioritariamente transparente para si.

![Rede virtual ligada ao SAP HANA em Azure (Grandes Instâncias) e no local](./media/hana-overview-architecture/image1-architecture.png)

A exigência de que os seus ativos no local devem ligar através da ExpressRoute ao Azure não muda porque utiliza a HANA Large Instance. A exigência de ter uma ou várias redes virtuais que executam os VMs, que acolhem a camada de aplicação que se conecta às instâncias HANA alojadas nas unidades hana large instance, também não muda. 

As diferenças para as implantações sap em Azure são:

- As unidades HANA Large Instance do seu inquilino cliente estão ligadas através de outro circuito ExpressRoute nas suas redes virtuais. Para separar as condições de carga, as instalações para os circuitos Da Rede Virtual Azure ExpressRoute e os circuitos entre redes virtuais Azure e HANA Large Instances não partilham os mesmos routers.
- O perfil de carga de trabalho entre a camada de aplicação SAP e a Grande Instância HANA é de natureza diferente, com muitos pequenos pedidos e explosões como transferências de dados (conjuntos de resultados) do SAP HANA para a camada de aplicação.
- A arquitetura de aplicação SAP é mais sensível à latência da rede do que cenários típicos onde os dados são trocados entre as instalações e o Azure.
- O portal Azure ExpressRoute tem pelo menos duas ligações ExpressRoute. Um circuito que está ligado a partir do local e outro que está ligado a partir de Grandes Instâncias HANA. Isto deixa apenas espaço para mais dois circuitos adicionais de diferentes MSEEs para ligar no ExpressRoute Gateway. Esta restrição é independente do uso do Caminho Rápido ExpressRoute. Todos os circuitos conectados partilham a largura de banda máxima para os dados de entrada do gateway ExpressRoute.

Com a Revisão 3 dos selos HANA Large Instance, a latência da rede experimentada entre VMs e unidades HANA Large Instance pode ser superior a uma latência típica de ida e volta da rede VM-to-VM. Dependendo da região de Azure, os valores medidos podem exceder a latência de ida e volta de 0,7 ms classificada como abaixo da média em [SAP Note #1100926 - FAQ: Desempenho](https://launchpad.support.sap.com/#/notes/1100926/E)da rede . Dependendo da região de Azure e ferramenta para medir a latência de ida e volta da rede entre uma unidade Azure VM e HANA Large Instance, a latência medida pode chegar a cerca de 2 milissegundos. No entanto, os clientes implementam aplicações SAP de produção baseadas em SAP HANA com sucesso na SAP HANA Large Instance. Certifique-se de testar cuidadosamente os seus processos de negócio em Azure HANA Large Instance. Uma nova funcionalidade, chamada ExpressRoute Fast Path, é capaz de reduzir substancialmente a latência da rede entre as grandes instâncias hana e os VMs da camada de aplicação em Azure (ver abaixo). 

Com a Revisão 4 dos selos de Grande Instância HANA, a latência da rede entre Os VMs Azure que são implantados nas proximidades do carimbo HANA Large Instance, é experimentado para cumprir a classificação média ou melhor do que a média, como documentado na [Nota SAP #1100926 - FAQ: Desempenho](https://launchpad.support.sap.com/#/notes/1100926/E) da rede se o Caminho Rápido da Rota Rápida azure ExpressRoute estiver configurado (ver abaixo). Para implantar VMs Azure nas proximidades das unidades HANA Large Instance da Revisão 4, é necessário alavancar os Grupos de Colocação de [Proximidade de Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). A forma como os grupos de colocação de proximidade podem ser usados para localizar a camada de aplicação SAP no mesmo centro de dados Azure como a Revisão 4 unidades de grande instância hana hospedadas é descrita em Grupos de Colocação de [Proximidade Azure para uma latência de rede ótima com aplicações SAP](sap-proximity-placement-scenarios.md).

Para proporcionar latência de rede determinística entre VMs e HANA Large Instance, a escolha do gateway ExpressRoute SKU é essencial. Ao contrário dos padrões de tráfego entre as instalações e os VMs, o padrão de tráfego entre VMs e HANA Large Instance pode desenvolver pequenas, mas elevadas explosões de pedidos e volumes de dados a transmitir. Para lidar bem com estas explosões, recomendamos vivamente a utilização do gateway UltraPerformance SKU. Para a classe Tipo II de HANA Large Instance SKUs, a utilização do gateway UltraPerformance SKU como gateway ExpressRoute é obrigatória.

> [!IMPORTANT] 
> Dado o tráfego global de rede entre a aplicação SAP e as camadas de base de dados, apenas as SKUs de gateway HighPerformance ou UltraPerformance para redes virtuais são suportadas para a ligação ao SAP HANA em Azure (Grandes Instâncias). Para HANA Large Instance Type II SKUs, apenas o gateway UltraPerformance SKU é suportado como um gateway ExpressRoute. As exceções aplicam-se quando se utiliza o Caminho Rápido expressRoute (ver abaixo)

### <a name="expressroute-fast-path"></a>Caminho rápido da Rota Expressa
Para reduzir a latência, o ExpressRoute Fast Path foi introduzido e lançado em maio de 2019 para a conectividade específica das grandes instâncias HANA às redes virtuais Azure que acolhem os VMs da aplicação SAP. A grande diferença para a solução lançada até agora, é que os fluxos de dados entre VMs e HANA Grandes Instâncias já não são encaminhados através da porta de entrada ExpressRoute. Em vez disso, os VMs atribuídos nas subredes da rede virtual Azure estão a comunicar diretamente com o router de borda empresarial dedicado. 

> [!IMPORTANT] 
> A funcionalidade ExpressRoute Fast Path requer que as subredes que executam os VMs da aplicação SAP estejam na mesma rede virtual Azure que foi ligada às Grandes Instâncias HANA. VMs localizados em redes virtuais Azure que são espreitadas com a rede virtual Azure ligada diretamente às unidades HANA Large Instance não estão beneficiando do Caminho Rápido ExpressRoute. Como resultado, os modelos típicos de rede virtual e hub, onde os circuitos ExpressRoute estão a ligar-se contra uma rede virtual hub e redes virtuais contendo a camada de aplicação SAP (porta-vozes) estão a ser espreitadas, a otimização pelo Caminho Rápido expressRoute não funcionará. Em adição, o ExpressRoute Fast Path não suporta as regras de encaminhamento definidas pelo utilizador (UDR) hoje. Para mais informações, consulte o gateway da [rede virtual ExpressRoute e o FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


Para mais detalhes sobre como configurar o Caminho Rápido ExpressRoute, leia o documento [Ligue uma rede virtual a grandes instâncias HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).    

> [!NOTE]
> Um gateway UltraPerformance ExpressRoute é necessário para ter o Caminho Rápido ExpressRoute funcionando


## <a name="single-sap-system"></a>Sistema SAP único

A infraestrutura no local anteriormente mostrada está ligada através da ExpressRoute para o Azure. O circuito ExpressRoute liga-se a um router de borda empresarial da Microsoft (MSEE). Para mais informações, consulte a visão geral técnica do [ExpressRoute.](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Após a criação da rota, liga-se à espinha dorsal Azure.

> [!NOTE] 
> Para executar paisagens SAP em Azure, ligue-se ao router de borda empresarial mais próximo da região de Azure na paisagem SAP. Os selos HANA Large Instance são ligados através de dispositivos de router de borda empresarial dedicados para minimizar a latência da rede entre VMs em selos De Grande Instância Azure IaaS e HANA Large Instance.

A porta de entrada ExpressRoute para os VMs que acolhem instâncias de aplicação SAP estão ligadas a um circuito ExpressRoute que se conecta às instalações. A mesma rede virtual está ligada a um router de borda empresarial separado dedicado à ligação com selos de Grande Instância. Utilizando o Caminho Rápido ExpressRoute, os dados fluem de HANA Grandes Instâncias para a camada de aplicação SAP VMs já não são encaminhados através do gateway ExpressRoute e com isso reduzem a latência de ida e volta da rede.

Este sistema é um exemplo simples de um único sistema SAP. A camada de aplicação SAP está alojada em Azure. A base de dados SAP HANA funciona no SAP HANA em Azure (Grandes Instâncias). O pressuposto é que a largura de banda de gateway ExpressRoute de 2 Gbps ou 10 Gbps de entrada não representa um estrangulamento.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Múltiplos sistemas SAP ou grandes sistemas SAP

Se vários sistemas SAP ou grandes sistemas SAP forem implantados para ligar ao SAP HANA em Azure (Grandes Instâncias), a entrada do gateway ExpressRoute pode tornar-se um estrangulamento. Ou quer isolar sistemas de produção e não produção em diferentes redes virtuais Azure. Neste caso, divida as camadas de aplicação em várias redes virtuais. Também pode criar uma rede virtual especial que se conecta à HANA Large Instance para casos como:

- Executando cópias de segurança diretamente dos casos HANA em HANA Large Instance para um VM em Azure que acolhe ações da NFS.
- Copiar grandes cópias de cópias ou outros ficheiros de unidades HANA Large Instance para o espaço de disco gerido em Azure.

Utilize uma rede virtual separada para hospedar VMs que gerem o armazenamento para transferência em massa de dados entre as grandes instâncias HANA e o Azure. Este arranjo evita os efeitos de grandes ficheiros ou transferência de dados da HANA Large Instance para O Azure no gateway ExpressRoute que serve os VMs que executam a camada de aplicação SAP. 

Para uma arquitetura de rede mais escalável:

- Aproveite várias redes virtuais para uma única camada de aplicação SAP maior.
- Implementar uma rede virtual separada para cada sistema SAP implantado, em comparação com a combinação destes sistemas SAP em subredes separadas sob a mesma rede virtual.

  Uma arquitetura de networking mais escalável para SAP HANA em Azure (Grandes Instâncias):

![Implementar camada de aplicação SAP sobre várias redes virtuais](./media/hana-overview-architecture/image4-networking-architecture.png)

Dependendo das regras e restrições, pretende aplicar-se entre as diferentes redes virtuais que hospedam VMs de diferentes sistemas SAP, deve ser o par dessas redes virtuais. Para obter mais informações sobre o epeering da rede virtual, consulte o peering da [rede virtual.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)


## <a name="routing-in-azure"></a>Encaminhamento em Azure

Por predefinição, três considerações de encaminhamento de rede são importantes para o SAP HANA em Azure (Grandes Instâncias):

* O SAP HANA on Azure (Grandes Instâncias) só pode ser acedido através de VMs Azure e da conexão ExpressRoute dedicada, não diretamente a partir do local. O acesso direto a partir das instalações para as unidades HANA Large Instance, tal como lhe foi entregue pela Microsoft, não é possível imediatamente. As restrições transitivas de encaminhamento devem-se à atual arquitetura de rede Azure utilizada para a Grande Instância SAP HANA. Alguns clientes da administração e quaisquer aplicações que necessitem de acesso direto, como o SAP Solution Manager que funciona no local, não podem ligar-se à base de dados SAP HANA. Para exceções, verifique a secção "Encaminhamento direto para as grandes instâncias HANA".

* Se tiver unidades HANA Large Instance implantadas em duas regiões azure diferentes para recuperação de desastres, as mesmas restrições transitórias de encaminhamento aplicadas no passado. Por outras palavras, os endereços IP de uma unidade HANA Large Instance numa região (por exemplo, US West) não foram encaminhados para uma unidade HANA Large Instance implantada noutra região (por exemplo, EUA Leste). Esta restrição era independente da utilização da rede Azure que espreitava por regiões ou ligava os circuitos ExpressRoute que ligam unidades HANA Large Instance a redes virtuais. Para uma representação gráfica, consulte a figura na secção "Use unidades HANA Large Instance em várias regiões." Esta restrição, que veio com a arquitetura implantada, proibiu o uso imediato da Replicação do Sistema HANA como funcionalidade de recuperação de desastres. Para alterações recentes, procure a secção "Use as unidades HANA Large Instance em várias regiões". 

* As unidades SAP HANA on Azure (Grandes Instâncias) têm um endereço IP atribuído a partir da gama de endereços IP do servidor que submeteu ao solicitar a implementação de Big Instance HANA. Para mais informações, consulte a [infraestrutura SAP HANA (Grandes Instâncias) e a conectividade no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Este endereço IP é acessível através das assinaturas e circuito sinuosos do Azure que liga as redes virtuais Azure às Grandes Instâncias HANA. O endereço IP atribuído a partir do intervalo de endereço ip do servidor é diretamente atribuído à unidade de hardware. Já *não* está atribuído através do NAT, como foi o caso nas primeiras implementações desta solução. 

### <a name="direct-routing-to-hana-large-instances"></a>Encaminhamento direto para grandes instâncias HANA

Por predefinição, o encaminhamento transitório não funciona nestes cenários:

* Entre unidades HANA Large Instance e uma implantação no local.

* Entre o encaminhamento de grandes instâncias hana que são implantados em duas regiões diferentes.

Existem três formas de permitir o encaminhamento transitório nesses cenários:

- Um representante inverso para direcionar os dados, de e para. Por exemplo, F5 BIG-IP, NGINX com Traffic Manager implantado na rede virtual Azure que se conecta a HANA Grandes Instâncias e às instalações como uma solução virtual de encaminhamento de firewall/tráfego.
- Utilizando [as regras ipTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) num VM Linux para permitir o encaminhamento entre as localizações no local e as unidades HANA Large Instance, ou entre unidades HANA Large Instance em diferentes regiões. O VM que executa ipTables precisa de ser implantado na rede virtual Azure que se conecta às grandes instâncias HANA e às instalações. O VM precisa de ser dimensionado em conformidade, de modo a que a entrada da rede do VM seja suficiente para o tráfego de rede esperado. Para mais detalhes sobre a largura de banda da rede VM, consulte o artigo [Tamanhos de máquinas virtuais Linux em Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [A Firewall Azure](https://azure.microsoft.com/services/azure-firewall/) seria outra solução para permitir o tráfego direto entre as unidades de instância seletiva hana. 

Todo o tráfego destas soluções seria encaminhado através de uma rede virtual Azure e, como tal, o tráfego poderia ser adicionalmente restringido pelos aparelhos macios utilizados ou pelos Grupos de Segurança da Rede Azure, de modo a que certos endereços IP ou endereços IP a partir do local pudessem ser bloqueados ou autorizados explicitamente a aceder a Grandes Instâncias HANA. 

> [!NOTE]  
> Esteja ciente de que a implementação e suporte para soluções personalizadas que envolvam aparelhos de rede de terceiros ou IPTables não é fornecido pela Microsoft. O suporte deve ser prestado pelo fornecedor do componente utilizado ou pelo integrador. 

#### <a name="express-route-global-reach"></a>Rota Expressa Global Alcance
A Microsoft introduziu uma nova funcionalidade chamada [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). Global Reach pode ser usado para grandes instâncias HANA em dois cenários:

- Permitir o acesso direto a partir das instalações para as suas unidades HANA Large Instance implantadas em diferentes regiões
- Permitir a comunicação direta entre as suas unidades HANA Large Instance implantadas em diferentes regiões


##### <a name="direct-access-from-on-premises"></a>Acesso direto a partir do local
Nas regiões azure onde o Global Reach é oferecido, pode solicitar a funcionalidade Global Reach para o seu circuito ExpressRoute que liga a sua rede no local à rede virtual Azure que se conecta também às suas unidades HANA Large Instance. Existem algumas implicações de custos para o lado no local do seu circuito ExpressRoute. Para preços, verifique os preços do [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/). Não existem custos adicionais para si relacionados com o circuito que liga a unidade(s) hana large instance(s) ao Azure. 

> [!IMPORTANT]  
> Em caso de utilização do Global Reach para permitir o acesso direto entre as suas unidades HANA Large Instance e os seus ativos no local, os dados da rede e o fluxo de controlo não são encaminhados através de **redes virtuais Azure,** mas diretamente entre os routers de intercâmbio empresarial da Microsoft. Como resultado, quaisquer regras NSG ou ASG, ou qualquer tipo de firewall, NVA ou procuração que implementou numa rede virtual Azure, não estão a ser tocadas. **Se utilizar o ExpressRoute Global Reach para permitir o acesso direto a partir do local para HANA Grandes unidades de unidades de instância restrições e permissões para aceder a unidades de grandes instâncias HANA precisam de ser definidas em firewalls do lado no local** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Ligando as grandes instâncias hana em diferentes regiões de Azure
Da mesma forma, como o ExpressRoute Global Reach pode ser usado para ligar no local às unidades HANA Large Instance, pode ser usado para ligar dois inquilinos hana de grande instância que são implantados para você em duas regiões diferentes. O isolamento são os circuitos ExpressRoute que os seus inquilinos hana large instance estão usando para ligar a Azure em ambas as regiões. Não existem taxas adicionais para ligar dois inquilinos hana de grande instância que são implantados em duas regiões diferentes. 

> [!IMPORTANT]  
> O fluxo de dados e o fluxo de controlo do tráfego de rede entre os diferentes inquilinos de instância HANA Grande não serão encaminhados através de redes azure. Como resultado, não pode utilizar a funcionalidade Azure ou nVAs para impor restrições de comunicação entre os seus dois inquilinos HANA Large Instances. 

Para obter mais detalhes sobre como obter o ExpressRoute Global Reach ativado, leia o documento [Ligue uma rede virtual a grandes instâncias HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).


## <a name="internet-connectivity-of-hana-large-instance"></a>Conectividade da Internet da GRANDE Instância HANA
Hana Large Instance *não* tem conectividade direta na Internet. Como exemplo, esta limitação pode restringir a sua capacidade de registar a imagem de OS diretamente com o fornecedor de SO. Poderá ter de trabalhar com o servidor local de gestão de ferramentas de gestão de subscrição do Servidor de Subscrição do Servidor Empresarial SUSE Linux ou com o Gestor de Assinaturas Red Hat Enterprise Linux.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Encriptação de dados entre VMs e HANA Large Instance
Os dados transferidos entre a HANA Large Instance e os VMs não são encriptados. No entanto, exclusivamente para a troca entre o lado HANA DBMS e as aplicações baseadas em JDBC/ODBC, pode ativar a encriptação do tráfego. Para mais informações, consulte [esta documentação através da SAP.](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html)

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Utilize unidades HANA Large Instance em várias regiões

Para perceber as instalações de recuperação de desastres, é preciso ter unidades shana large instance em várias regiões de Azure. Mesmo com a utilização do Azure [Global Vnet Peering], o encaminhamento transitório por defeito não está a funcionar entre inquilinos da HANA Large Instance em duas regiões diferentes. No entanto, a Global Reach abre a via de comunicação entre as unidades hana large instance que você disponibilizou em duas regiões diferentes. Este cenário de utilização do ExpressRoute Global Reach permite:

 - Replicação do sistema HANA sem quaisquer proxies ou firewalls adicionais
 - Copiar cópias de cópias entre unidades HANA Large Instance em duas regiões diferentes para executar cópias do sistema ou atualizações do sistema


![Rede virtual ligada a selos de grande instância Azure em diferentes regiões de Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

O número mostra como as diferentes redes virtuais em ambas as regiões estão ligadas a dois circuitos expressroute diferentes que são usados para ligar ao SAP HANA em Azure (Grandes Instâncias) em ambas as regiões de Azure (linhas cinzentas). A razão para estas duas ligações cruzadas é proteger de uma interrupção dos EEE de ambos os lados. O fluxo de comunicação entre as duas redes virtuais nas duas regiões de Azure deverá ser tratado sobre o [epeering global](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) das duas redes virtuais nas duas regiões diferentes (linha pontilhada azul). A linha vermelha espessa descreve a conexão ExpressRoute Global Reach, que permite que as unidades hana large instance dos seus inquilinos em duas regiões diferentes se comuniquem entre si. 

> [!IMPORTANT] 
> Se utilizou vários circuitos ExpressRoute, as predespesas do AS Path e as definições de BGP de preferência local devem ser utilizadas para garantir o encaminhamento adequado do tráfego.

**Passos seguintes**
- Consulte a arquitetura de [armazenamento SAP HANA (Grandes Instâncias)](hana-storage-architecture.md)
