---
title: FAQ - Azure ExpressRoute | Microsoft Docs
description: O ExpressRoute FAQ contém informações sobre serviços de Azure suportados, custos, dados e conexões, SLA, Fornecedores e Localizações, Largura de Banda e Detalhes Técnicos adicionais.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/07/2021
ms.author: duau
ms.openlocfilehash: 59194f8d7b1783867ab1422597b361aa3a4a2a60
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449871"
---
# <a name="expressroute-faq"></a>FAQ do ExpressRoute

## <a name="what-is-expressroute"></a>O que é o ExpressRoute?

O ExpressRoute é um serviço Azure que permite criar ligações privadas entre datacenters da Microsoft e infraestruturas que estão nas suas instalações ou numa instalação de co-essão. As ligações ExpressRoute não passam pela Internet pública e oferecem maior segurança, fiabilidade e velocidades com latências mais baixas do que as ligações típicas através da Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Quais são os benefícios da utilização de ligações ExpressRoute e rede privada?

As ligações do ExpressRoute não passam para a Internet pública. Oferecem maior segurança, fiabilidade e velocidades, com latências mais baixas e consistentes do que as ligações típicas através da Internet. Em alguns casos, a utilização de ligações ExpressRoute para transferir dados entre dispositivos no local e a Azure pode trazer benefícios significativos de custos.

### <a name="where-is-the-service-available"></a>Onde está o serviço disponível?

Consulte esta página para obter localização e disponibilidade de serviço: [Parceiros e locais ExpressRoute](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Como posso utilizar o ExpressRoute para ligar à Microsoft se não tenho parcerias com um dos parceiros da ExpressRoute?

Pode selecionar uma transportadora regional e aterrar ligações Ethernet para um dos locais do fornecedor de intercâmbio suportado. Em seguida, pode fazer o peer com a Microsoft na localização do fornecedor. Consulte a última secção de [parceiros e locais da ExpressRoute](expressroute-locations.md) para ver se o seu fornecedor de serviços está presente em algum dos locais de troca. Em seguida, pode encomendar um circuito ExpressRoute através do prestador de serviços para ligar ao Azure.

### <a name="how-much-does-expressroute-cost"></a>Quanto custa o ExpressRoute?

Verifique [os detalhes dos preços](https://azure.microsoft.com/pricing/details/expressroute/) para obter informações sobre preços.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-this-bandwidth-allocated-for-ingress-and-egress-traffic-separately"></a>Se eu pagar por um circuito ExpressRoute de uma determinada largura de banda, tenho esta largura de banda atribuída para o tráfego de entradas e saídas separadamente?

Sim, a largura de banda do circuito ExpressRoute é duplex. Por exemplo, se comprar um circuito ExpressRoute de 200 mbps, está a adquirir 200 mbps para tráfego de entrada e 200 mbps para tráfego de saída.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-private-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Se eu pagar por um circuito ExpressRoute de uma determinada largura de banda, a ligação privada que compro ao meu fornecedor de serviços de rede tem de ser a mesma velocidade?

N.º Pode adquirir uma ligação privada de qualquer velocidade ao seu prestador de serviços. No entanto, a sua ligação ao Azure está limitada à largura de banda do circuito ExpressRoute que compra.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-use-more-than-my-procured-bandwidth"></a>Se eu pagar por um circuito ExpressRoute de uma determinada largura de banda, tenho a capacidade de usar mais do que a minha largura de banda?

Sim, pode utilizar até duas vezes o limite de largura de banda que adquiriu utilizando a largura de banda disponível na ligação secundária do seu circuito ExpressRoute. A redundância incorporada do seu circuito é configurada utilizando ligações primárias e secundárias, cada uma das larguras de banda adquiridas, a dois routers Microsoft Enterprise Edge (MSEEs). A largura de banda disponível através da sua ligação secundária pode ser utilizada para tráfego adicional, se necessário. No entanto, uma vez que a ligação secundária se destina a despedimentos, não está garantida e não deve ser utilizada para tráfego adicional durante um período de tempo prolongado. Para saber mais sobre como utilizar ambas as connneções para transmitir tráfego, consulte [utilização como PATH.](./expressroute-optimize-routing.md#solution-use-as-path-prepending)

Se pretender utilizar apenas a sua ligação primária para transmitir tráfego, a largura de banda para a ligação é fixada e tentar subscrever-se excessivamente resultará em quedas de pacotes aumentadas. Se o tráfego fluir através de um Portal ExpressRoute, a largura de banda para o Gateway SKU é fixa e não é reprovada. Para a largura de banda de cada Gateway SKU, consulte [as portas de rede virtual Sobre ExpressRoute](expressroute-about-virtual-network-gateways.md#aggthroughput).

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Posso utilizar simultaneamente a mesma ligação de rede privada com rede virtual e outros serviços Azure?

Sim. Um circuito ExpressRoute, uma vez configurado, permite-lhe aceder a serviços dentro de uma rede virtual e outros serviços Azure simultaneamente. Você conecta-se a redes virtuais através do caminho de observação privado, e a outros serviços sobre o caminho de observação da Microsoft.

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>Como são anunciados os VNets no ExpressRoute Private Peering?

O gateway ExpressRoute irá anunciar o *Address Space(s)* do Azure VNet, não pode incluir/excluir ao nível da sub-rede. É sempre o Espaço de Endereços VNet que é anunciado. Além disso, se o VNet Peering for utilizado e o VNet esprevado tiver "Use Remote Gateway" ativado, o Espaço de Endereço do VNet perso também será anunciado.

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>Quantos prefixos podem ser anunciados de um VNet para as instalações do ExpressRoute Private Peering?

Há um máximo de 200 prefixos anunciados numa única ligação ExpressRoute, ou através do VNet espreitando através do trânsito de gateway. Por exemplo, se tiver 199 espaços de endereço num único VNet ligado a um circuito ExpressRoute, todos os 199 prefixos serão anunciados no local. Em alternativa, se tiver um VNet habilitado a permitir o trânsito de gateway com 1 espaço de endereço e 150 VNets de fala ativados utilizando a opção "Permitir gateway remoto", o VNet implantado com o gateway irá anunciar 151 prefixos para o local.

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>O que acontece se eu exceder o limite de prefixo numa ligação ExpressRoute?

A ligação entre o circuito ExpressRoute e o gateway (e os VNets espreitados utilizando o trânsito de gateway, se aplicável) diminuirá. Restabelecer-se-á quando o limite do prefixo deixar de ser ultrapassado.  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>Posso filtrar rotas vindas da minha rede no local?

A única forma de filtrar/incluir rotas é no router de borda no local. As rotas definidas pelo utilizador podem ser adicionadas no VNet para afetar o encaminhamento específico, mas esta será estática e não faz parte do anúncio do BGP.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>O ExpressRoute oferece um Acordo de Nível de Serviço (SLA)?

Para obter informações, consulte a página [ExpressRoute SLA.](https://azure.microsoft.com/support/legal/sla/)

## <a name="supported-services"></a>Serviços suportados

O ExpressRoute suporta três domínios de encaminhamento para vários tipos de [serviços:](expressroute-circuit-peerings.md) peering privado, observação da Microsoft e observação pública (depreciada).

### <a name="private-peering"></a>Peering privado

**Apoiado:**

* Redes virtuais, incluindo todas as máquinas virtuais e serviços na nuvem

### <a name="microsoft-peering"></a>Peering da Microsoft

Se o seu circuito ExpressRoute estiver ativado para o Azure Microsoft, pode aceder às [gamas de endereços IP públicas utilizadas](../virtual-network/public-ip-addresses.md#public-ip-addresses) no Azure sobre o circuito. O Azure Microsoft irá fornecer acesso aos serviços atualmente alojados no Azure (com geo-restrições dependendo do SKU do seu circuito). Para validar a disponibilidade de um serviço específico, pode consultar a documentação desse serviço para ver se existe uma gama reservada publicada para esse serviço. Em seguida, procure as gamas IP do serviço-alvo e compare com as gamas listadas nos [intervalos Azure IP Ranges and Service Tags – Public Cloud XML file](https://www.microsoft.com/download/details.aspx?id=56519). Em alternativa, pode abrir um bilhete de apoio para o serviço em questão para esclarecimento.

**Apoiado:**

* [Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* Power BI - Disponível através de uma Comunidade Regional Azure, consulte [aqui](/power-bi/service-admin-where-is-my-tenant-located) como descobrir a região do seu inquilino Power BI.
* Azure Active Directory
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (comunidade Azure Global Services)
* Endereços IP públicos Azure para IaaS (Máquinas Virtuais, Gateways de Rede Virtual, Equilibradores de Carga, etc.)  
* A maioria dos outros serviços da Azure também são apoiados. Verifique diretamente com o serviço que pretende utilizar para verificar o suporte.

**Não suportado:**

* CDN
* Azure Front Door
* [Windows Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/)
* Servidor de Autenticação multi-factor (legado)
* Gestor de Tráfego

### <a name="public-peering"></a>Peering público

O espreitamento público foi desativado nos novos circuitos ExpressRoute. Os serviços Azure estão agora disponíveis no espreitamento da Microsoft. Se for criado um circuito que foi criado antes de o olhar público ser depreciado, pode optar por utilizar o espreitamento da Microsoft ou o seu olhar público, dependendo dos serviços que pretende.

Para obter mais informações e passos de configuração para espreitar o público, consulte [o expressRoute public peering](about-public-peering.md).

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Por que vejo o estatuto de "prefixos públicos anunciados" como "Validação necessária", enquanto configura olhando a Microsoft?

A Microsoft verifica se os prefixos públicos anunciados especificados e 'Peer ASN' (ou 'Cliente ASN') são-lhe atribuídos no Registo de Encaminhamento de Internet. Se estiver a receber os prefixos públicos de outra entidade e se a atribuição não for registada com o registo de encaminhamento, a validação automática não será completa e exigirá validação manual. Se a validação automática falhar, verá a mensagem "Validação necessária".

Se vir a mensagem "Validação necessária", recolha os documentos que mostram que os prefixos públicos são atribuídos à sua organização pela entidade que está listada como o proprietário dos prefixos no registo de encaminhamento e submeta esses documentos para validação manual, abrindo um bilhete de apoio como mostrado abaixo.

![Screenshot mostrando um novo pedido de suporte (bilhete de apoio) para "Prova de propriedade para prefixos públicos".](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>A Dynamics 365 está apoiada no ExpressRoute?

Os ambientes dinâmicos 365 e Common Data Service (CDS) estão hospedados no Azure e, por isso, os clientes beneficiam do apoio subjacente ao ExpressRoute para os recursos da Azure. Pode ligar-se aos seus pontos finais de serviço se o seu filtro de router incluir as regiões Azure onde estão hospedados os ambientes Dynamics 365/CDS.

> [!NOTE]
> [O ExpressRoute Premium](#expressroute-premium) **não** é necessário para a conectividade Dynamics 365 via Azure ExpressRoute se o circuito ExpressRoute for implantado na mesma [região geopolítica.](./expressroute-locations-providers.md#expressroute-locations)

## <a name="data-and-connections"></a>Dados e ligações

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Existem limites na quantidade de dados que posso transferir através do ExpressRoute?

Não estabelecemos um limite na quantidade de transferência de dados. Consulte os [detalhes dos preços](https://azure.microsoft.com/pricing/details/expressroute/) para obter informações sobre as taxas de largura de banda.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Que velocidades de ligação são suportadas pelo ExpressRoute?

Ofertas de largura de banda suportadas:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Que prestadores de serviços estão disponíveis?

Consulte [os parceiros e locais da ExpressRoute](expressroute-locations.md) para a lista de prestadores de serviços e locais.

## <a name="technical-details"></a>Detalhes técnicos

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Quais são os requisitos técnicos para ligar o meu local ao Azure?

Consulte [a página de pré-requisitos ExpressRoute](expressroute-prerequisites.md) para obter os requisitos.

### <a name="are-connections-to-expressroute-redundant"></a>As ligações com o ExpressRoute são redundantes?

Sim. Cada circuito ExpressRoute tem um par de ligações cruzadas redundantes configuradas para proporcionar alta disponibilidade.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Perderei a conectividade se uma das minhas ligações ExpressRoute falhar?

Não perderá a conectividade se uma das ligações cruzadas falhar. Uma ligação redundante está disponível para suportar a carga da sua rede e fornecer alta disponibilidade do seu circuito ExpressRoute. Além disso, pode criar um circuito num local diferente para obter resiliência ao nível do circuito.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Como implemento o despedimento em olhares privados?

Vários circuitos ExpressRoute de diferentes locais de observação ou até quatro ligações a partir do mesmo local de observação podem ser ligados à mesma rede virtual para fornecer alta disponibilidade no caso de um único circuito ficar indisponível. Em seguida, pode [atribuir pesos mais elevados](./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection) a uma das ligações locais para preferir um circuito específico. Recomenda-se vivamente que os clientes confendam pelo menos dois circuitos ExpressRoute para evitar pontos únicos de falha. 

Consulte [aqui](./designing-for-high-availability-with-expressroute.md) para projetar para alta disponibilidade e [aqui](./designing-for-disaster-recovery-with-expressroute-privatepeering.md) para projetar para recuperação de desastres.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Como implemento a redundância no olhar da Microsoft?

É altamente recomendado quando os clientes estão a usar a Microsoft a espreitar para aceder a serviços públicos Azure, como o Azure Storage ou o Azure SQL, bem como os clientes que estão a usar a Microsoft a espreitar para o Microsoft 365, que implementam vários circuitos em diferentes locais de observação para evitar pontos únicos de falha. Os clientes podem anunciar o mesmo prefixo em ambos os circuitos e usar [prefixos AS PATH](./expressroute-optimize-routing.md#solution-use-as-path-prepending) ou anunciar prefixos diferentes para determinar o caminho a partir do local.

Consulte [aqui](./designing-for-high-availability-with-expressroute.md) para desenhar para obter alta disponibilidade.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Como posso garantir uma elevada disponibilidade numa rede virtual ligada ao ExpressRoute?

Pode obter uma elevada disponibilidade ligando até quatro circuitos ExpressRoute no mesmo local de observação à sua rede virtual, ou ligando circuitos ExpressRoute em diferentes locais de observação (por exemplo, Singapura, Singapura2) à sua rede virtual. Se um circuito ExpressRoute se avariar, a conectividade falhará noutro circuito ExpressRoute. Por predefinição, o tráfego que sai da sua rede virtual é encaminhado com base no Roteamento multi-caminhos de custo igual (ECMP). Pode utilizar o Peso de Ligação para preferir um circuito a outro. Para obter mais informações, consulte [o Optimizing ExpressRoute Routing](expressroute-optimize-routing.md).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Como posso garantir que o meu tráfego destinado a serviços públicos Azure como o Azure Storage e o Azure SQL na Microsoft a espreitar ou a espreitar publicamente é preferido no caminho ExpressRoute?

Tem de implementar o atributo *Preferência Local* no seu router(s) para garantir que o caminho entre as instalações e o Azure é sempre preferido no seu(s) circuito ExpressRoute.

Consulte detalhes adicionais [aqui](./expressroute-optimize-routing.md#path-selection-on-microsoft-and-public-peerings) na seleção de caminhos BGP e configurações comuns do router. 

### <a name="if-im-not-co-located-at-a-cloud-exchange-and-my-service-provider-offers-point-to-point-connection-do-i-need-to-order-two-physical-connections-between-my-on-premises-network-and-microsoft"></a><a name="onep2plink"></a>Se não estiver co-localizado numa troca de nuvens e o meu fornecedor de serviços oferecer ligação ponto-a-ponto, preciso de encomendar duas ligações físicas entre a minha rede no local e a Microsoft?

Se o seu fornecedor de serviços pode estabelecer dois circuitos virtuais Ethernet sobre a ligação física, só precisa de uma ligação física. A ligação física (por exemplo, uma fibra ótica) é terminada num dispositivo de camada 1 (L1) (ver imagem). Os dois circuitos virtuais Ethernet estão marcados com diferentes IDs VLAN, um para o circuito primário e outro para o secundário. Os IDs VLAN estão no cabeçalho Ethernet 802.1Q exterior. O cabeçalho interno 802.1Q Ethernet (não mostrado) está mapeado para um domínio específico [de encaminhamento ExpressRoute](expressroute-circuit-peerings.md).

![Diagrama realçando os circuitos virtuais primários e secundários da camada 1 (L1) que compõem a ligação física entre os interruptores de um Site do Cliente e uma Localização ExpressRoute.](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Posso estender um dos meus VLANs a Azure usando o ExpressRoute?

N.º Não suportamos extensões de conectividade de camada 2 em Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Posso ter mais do que um circuito ExpressRoute na minha assinatura?

Sim. Pode ter mais do que um circuito ExpressRoute na sua subscrição. O limite por defeito está definido para 10. Pode contactar o Microsoft Support para aumentar o limite, se necessário.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Posso ter circuitos ExpressRoute de diferentes prestadores de serviços?

Sim. Pode ter circuitos ExpressRoute com muitos fornecedores de serviços. Cada circuito ExpressRoute está associado apenas a um prestador de serviços. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Vejo dois locais de observação expressRoute no mesmo metro, por exemplo, Singapura e Singapura2. Que localização de espreitar devo escolher para criar o meu circuito ExpressRoute?
Se o seu fornecedor de serviços oferecer ExpressRoute em ambos os sites, pode trabalhar com o seu fornecedor e escolher qualquer um dos sites para configurar o ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Posso ter vários circuitos ExpressRoute no mesmo metro? Posso ligá-los à mesma rede virtual?

Sim. Pode ter vários circuitos ExpressRoute com os mesmos ou diferentes fornecedores de serviços. Se o metrô tiver vários locais de observação ExpressRoute e os circuitos forem criados em diferentes locais de observação, pode ligá-los à mesma rede virtual. Se os circuitos forem criados no mesmo local de observação, pode ligar até quatro circuitos à mesma rede virtual.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Como posso ligar as minhas redes virtuais a um circuito ExpressRoute

Os passos básicos são:

* Estabeleça um circuito ExpressRoute e mandem o prestador de serviços a capacitá-lo.
* Você, ou o fornecedor, deve configurar o (s) de observação de BGP.
* Ligue a rede virtual ao circuito ExpressRoute.

Para obter mais informações, consulte [os fluxos de trabalho ExpressRoute para o provisionamento de circuitos e estados de circuito](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Existem limites de conectividade para o meu circuito ExpressRoute?

Sim. O artigo [de parceiros e localizações ExpressRoute](expressroute-locations.md) fornece uma visão geral dos limites de conectividade para um circuito ExpressRoute. A conectividade para um circuito ExpressRoute está limitada a uma única região geopolítica. A conectividade pode ser expandida para atravessar regiões geopolíticas, permitindo a funcionalidade premium ExpressRoute.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Posso ligar-me a mais de uma rede virtual a um circuito ExpressRoute?

Sim. Pode ter até 10 ligações de redes virtuais num circuito Standard ExpressRoute e até 100 num [circuito ExpressRoute premium](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Tenho várias assinaturas Azure que contêm redes virtuais. Posso ligar redes virtuais que estão em subscrições separadas a um único circuito ExpressRoute?

Sim. Pode ligar até 10 redes virtuais na mesma subscrição que o circuito ou diferentes subscrições utilizando um único circuito ExpressRoute. Este limite pode ser aumentado permitindo a funcionalidade de prémio ExpressRoute. Note que os encargos de conectividade e largura de banda para o circuito dedicado serão aplicados ao proprietário do circuito ExpressRoute; todas as redes virtuais partilham a mesma largura de banda.

Para obter mais informações, consulte [partilhar um circuito ExpressRoute em várias subscrições](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Tenho várias assinaturas Azure associadas a diferentes inquilinos do Azure Ative Directory ou inscrições do Enterprise Agreement. Posso ligar redes virtuais que estão em inquilinos separados e matriculas a um único circuito ExpressRoute que não esteja no mesmo inquilino ou inscrição?

Sim. As autorizações ExpressRoute podem abranger limites de subscrição, inquilino e inscrição sem necessidade de configuração adicional. Note que os encargos de conectividade e largura de banda para o circuito dedicado serão aplicados ao proprietário do circuito ExpressRoute; todas as redes virtuais partilham a mesma largura de banda.

Para obter mais informações, consulte [partilhar um circuito ExpressRoute em várias subscrições](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>As redes virtuais estão ligadas ao mesmo circuito isoladas umas das outras?

N.º Do ponto de vista do encaminhamento, todas as redes virtuais ligadas ao mesmo circuito ExpressRoute fazem parte do mesmo domínio de encaminhamento e não estão isoladas umas das outras. Se precisar de isolamento de rotas, precisa de criar um circuito ExpressRoute separado.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Posso ter uma rede virtual ligada a mais de um circuito ExpressRoute?

Sim. Pode ligar uma única rede virtual a até quatro circuitos ExpressRoute nos mesmos locais de observação ou diferentes. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Posso aceder à Internet a partir das minhas redes virtuais ligadas aos circuitos ExpressRoute?

Sim. Se não tiver anunciado as rotas predefinidas (0.0.0.0/0) ou as prefixos da rota da Internet através da sessão BGP, pode ligar-se à Internet a partir de uma rede virtual ligada a um circuito ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Posso bloquear a conectividade da Internet com redes virtuais ligadas aos circuitos ExpressRoute?

Sim. Pode anunciar rotas predefinidas (0.0.0.0/0) para bloquear toda a conectividade da Internet com máquinas virtuais implantadas dentro de uma rede virtual e encaminhar todo o tráfego através do circuito ExpressRoute.

> [!NOTE]
> Se a rota anunciada de 0.0.0.0/0 for retirada das rotas anunciadas (por exemplo, devido a uma falha ou configuração errada), a Azure fornecerá uma [rota de sistema](../virtual-network/virtual-networks-udr-overview.md#system-routes) para recursos na Rede Virtual conectada para fornecer conectividade à internet.  Para garantir que o tráfego de saída para a internet está bloqueado, é aconselhável colocar um Grupo de Segurança de Rede em todas as sub-redes com uma regra de Outbound Deny para o tráfego de internet.

Se você anunciar rotas predefinidas, forçamos o tráfego para os serviços oferecidos sobre o espremo da Microsoft (como armazenamento Azure e SQL DB) de volta às suas instalações. Terá de configurar os seus routers para devolver o tráfego ao Azure através do caminho de observação da Microsoft ou através da Internet. Se tiver ativado um ponto final de serviço para o serviço, o tráfego para o serviço não é forçado a ir para as suas instalações. O tráfego permanece dentro da rede de espinha dorsal de Azure. Para saber mais sobre os pontos finais de serviço, consulte [os pontos finais do serviço de rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>As redes virtuais ligadas ao mesmo circuito ExpressRoute podem falar entre si?

Sim. As máquinas virtuais implantadas em redes virtuais ligadas ao mesmo circuito ExpressRoute podem comunicar entre si. Recomendamos a criação [de um espreitamento de rede virtual](../virtual-network/virtual-network-peering-overview.md) para facilitar esta comunicação.

### <a name="can-i-set-up-a-site-to-site-vpn-connection-to-my-virtual-network-in-conjunction-with-expressroute"></a>Posso criar uma ligação VPN site-to-site à minha rede virtual em conjunto com o ExpressRoute?

Sim. O ExpressRoute pode coexistir com VPNs site-to-site. Consulte [o Configure ExpressRoute e as ligações de coexistência site-to-site](expressroute-howto-coexist-resource-manager.md).

### <a name="how-do-i-enable-routing-between-my-site-to-site-vpn-connection-and-my-expressroute"></a>Como posso permitir o encaminhamento entre a minha ligação VPN site-to-site e o meu ExpressRoute?

Se pretender ativar o encaminhamento entre a sua sucursal ligada ao Expressoute e a sua sucursal ligada a uma ligação VPN site-to-site, terá de configurar [o Azure Route Server](../route-server/expressroute-vpn-support.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Por que existe um endereço IP público associado à porta de entrada ExpressRoute numa rede virtual?

O endereço IP público é utilizado apenas para gestão interna, e não constitui uma exposição de segurança da sua rede virtual.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Há limites para o número de rotas que posso anunciar?

Sim. Aceitamos até 4000 prefixos de rota para espreitar privados e 200 para o espreitar pela Microsoft. Pode aumentar isto para 10.000 rotas para observação privada se ativar a funcionalidade premium ExpressRoute.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Existem restrições nas gamas IP que posso anunciar durante a sessão de BGP?

Não aceitamos prefixos privados (RFC1918) para a sessão de BGP que espreita a Microsoft. Aceitamos qualquer tamanho de prefixo (até /32) tanto na Microsoft como no espreitamento privado.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>O que acontece se eu exceder os limites do BGP?

As sessões de BGP serão retiradas. Serão reiniciados assim que a contagem de prefixos for abaixo do limite.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Qual é o tempo de espera do ExpressRoute BGP? Pode ser ajustado?

A hora de espera é de 180. As mensagens vivas são enviadas a cada 60 segundos. Estas são definições fixas no lado da Microsoft que não podem ser alteradas. É possível configurar diferentes temporizadores, e os parâmetros da sessão de BGP serão negociados em conformidade.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Posso mudar a largura de banda de um circuito ExpressRoute?

Sim, pode tentar aumentar a largura de banda do seu circuito ExpressRoute no portal Azure, ou utilizando o PowerShell. Se houver capacidade disponível na porta física em que o seu circuito foi criado, a sua mudança terá sucesso. 

Se a sua mudança falhar, significa que ou não há capacidade suficiente na porta atual e precisa de criar um novo circuito ExpressRoute com a largura de banda mais alta, ou que não haja capacidade adicional nesse local, caso em que não poderá aumentar a largura de banda. 

Também terá de acompanhar o seu fornecedor de conectividade para garantir que atualizam os aceleradores dentro das suas redes para suportar o aumento da largura de banda. No entanto, não é possível reduzir a largura de banda do seu circuito ExpressRoute. Tem de criar um novo circuito ExpressRoute com largura de banda inferior e apagar o circuito antigo.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Como posso mudar a largura de banda de um circuito ExpressRoute?

Pode atualizar a largura de banda do circuito ExpressRoute utilizando o cmdlet REST API ou PowerShell.

## <a name="expressroute-premium"></a>Prémio ExpressRoute

### <a name="what-is-expressroute-premium"></a>O que é o prémio ExpressRoute?

O prémio ExpressRoute é uma coleção das seguintes características:

* Aumento do limite de tabela de encaminhamento de 4000 rotas para 10.000 rotas para o aconselhamento privado.
* Número aumentado de VNets e conexões ExpressRoute Global Reach que podem ser ativadas num circuito ExpressRoute (o padrão é 10). Para mais informações, consulte a tabela [ExpressRoute Limits.](#limits)
* Conectividade com a Microsoft 365
* Conectividade global sobre a rede central da Microsoft. Agora pode ligar um VNet numa região geopolítica com um circuito ExpressRoute noutra região.<br>
    **Exemplos:**

    *  Pode ligar um VNet criado na Europa West a um circuito ExpressRoute criado em Silicon Valley. 
    *  No que diz sobre o estojo da Microsoft, os prefixos de outras regiões geopolíticas são anunciados de modo a que se possa ligar, por exemplo, ao SQL Azure na Europa Oeste a partir de um circuito em Silicon Valley.


### <a name="how-many-vnets-and-expressroute-global-reach-connections-can-i-enable-on-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a><a name="limits"></a>Quantas ligações VNets e ExpressRoute Global Reach posso permitir num circuito ExpressRoute se eu habilifiquei o Prémio ExpressRoute?

As tabelas a seguir mostram os limites ExpressRoute e o número de VNets e conexões ExpressRoute Global Reach por circuito ExpressRoute:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Como posso habilitar o prémio ExpressRoute?

As funcionalidades premium ExpressRoute podem ser ativadas quando a funcionalidade está ativada e podem ser desligadas atualizando o estado do circuito. Pode ativar o ExpressRoute premium na hora da criação do circuito, ou pode ligar para o CMDLET REST API / PowerShell.

### <a name="how-do-i-disable-expressroute-premium"></a>Como desativar o prémio ExpressRoute?

Pode desativar o prémio ExpressRoute ligando para o CMDE REST ou PowerShell. Tem de se certificar de que aumentou a sua conectividade para cumprir os limites predefinidos antes de desativar o prémio ExpressRoute. Se a sua utilização ultrapassar os limites padrão, o pedido de desativação do prémio ExpressRoute falha.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Posso escolher as funcionalidades que quero do conjunto de funcionalidades premium?

N.º Não pode escolher as características. Ativamos todas as funcionalidades quando liga o Prémio ExpressRoute.

### <a name="how-much-does-expressroute-premium-cost"></a>Quanto custa o Prémio ExpressRoute?

Consulte os [detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) para o custo.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Pago o prémio ExpressRoute para além dos custos normais da ExpressRoute?

Sim. Os custos premium ExpressRoute aplicam-se em cima dos custos e encargos do circuito ExpressRoute exigidos pelo fornecedor de conectividade.

## <a name="expressroute-local"></a>ExpressRoute Local
### <a name="what-is-expressroute-local"></a>O que é ExpressRoute Local?
ExpressRoute Local é um circuito SKU da ExpressRoute, além do Standard SKU e do Premium SKU. Uma característica chave do Local é que um circuito local em um local de observação ExpressRoute dá-lhe acesso apenas a uma ou duas regiões de Azure dentro ou perto do mesmo metro. Em contraste, um circuito Standard dá-lhe acesso a todas as regiões do Azure numa área geopolítica e um circuito Premium a todas as regiões do Azure a nível global. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>Quais são os benefícios do ExpressRoute Local?
Embora precise de pagar transferência de dados de saída para o seu circuito Standard ou Premium ExpressRoute, não paga transferência de dados de saída separadamente para o seu circuito ExpressRoute Local. Por outras palavras, o preço do ExpressRoute Local inclui taxas de transferência de dados. O ExpressRoute Local é uma solução mais económica se tiver uma quantidade massiva de dados para transferir e pode trazer os seus dados através de uma ligação privada a um local de observação ExpressRoute perto das regiões de Azure desejadas. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Quais as funcionalidades disponíveis e as que não estão no ExpressRoute Local?
Em comparação com um circuito Standard ExpressRoute, um circuito local tem o mesmo conjunto de funcionalidades, exceto:
* Âmbito de acesso às regiões de Azure, tal como acima descrito
* ExpressRoute Global Reach não está disponível no Local

O ExpressRoute Local também tem os mesmos limites de recursos (por exemplo, o número de VNets por circuito) que o Standard. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Onde está disponível o ExpressRoute Local e quais as regiões de Azure a que se mapeia cada local de observação?
O ExpressRoute Local está disponível nos locais de observação onde uma ou duas regiões de Azure estão por perto. Não está disponível num local de observação onde não exista região de Azure nesse estado ou província ou país/região. Por favor, consulte os mapeamentos exatos [na página Locais.](expressroute-locations-providers.md)  

## <a name="expressroute-for-microsoft-365"></a>ExpressRoute para a Microsoft 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-microsoft-365-services"></a>Como posso criar um circuito ExpressRoute para ligar aos serviços microsoft 365?

1. Reveja a [página de pré-requisitos expressRoute](expressroute-prerequisites.md) para se certificar de que cumpre os requisitos.
2. Para garantir que as suas necessidades de conectividade são satisfeitas, reveja a lista de prestadores de serviços e locais no artigo [de parceiros e localizações ExpressRoute.](expressroute-locations.md)
3. Planeie os seus requisitos de capacidade, revendo [o planeamento da Rede e a afinação de desempenho para o Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance).
4. Siga os passos indicados nos fluxos de trabalho para configurar [os fluxos de trabalho expressRoute de conectividade para o provisionamento de circuitos e estados de circuito .](expressroute-workflows.md)

> [!IMPORTANT]
> Certifique-se de que ativou o addon premium ExpressRoute ao configurar a conectividade com os serviços microsoft 365.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-microsoft-365-services"></a>Os meus circuitos ExpressRoute existentes podem suportar a conectividade com os serviços da Microsoft 365?

Sim. O circuito ExpressRoute existente pode ser configurado para suportar a conectividade com os serviços da Microsoft 365. Certifique-se de que tem capacidade suficiente para ligar aos serviços da Microsoft 365 e que ativou um complemento premium. [O planeamento de redes e a afinação de desempenho para o Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance) ajudam-no a planear as suas necessidades de conectividade. Consulte também [criar e modificar um circuito ExpressRoute](expressroute-howto-circuit-classic.md).

### <a name="what-microsoft-365-services-can-be-accessed-over-an-expressroute-connection"></a>Quais os serviços microsoft 365 que podem ser acedidos numa ligação ExpressRoute?

Consulte a página [de gamas de endereços URLs e IP da Microsoft 365](/microsoft-365/enterprise/urls-and-ip-address-ranges) para obter uma lista atualizada de serviços suportados sobre o ExpressRoute.

### <a name="how-much-does-expressroute-for-microsoft-365-services-cost"></a>Quanto custa o ExpressRoute para os serviços Microsoft 365?

Os serviços da Microsoft 365 requerem um complemento premium para serem ativados. Consulte [a página de detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) para obter custos.

### <a name="what-regions-is-expressroute-for-microsoft-365-supported-in"></a>Em que regiões é suportada o ExpressRoute para a Microsoft 365?

Consulte [os parceiros e locais da ExpressRoute](expressroute-locations.md) para obter informações.

### <a name="can-i-access-microsoft-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Posso aceder ao Microsoft 365 através da Internet, mesmo que o ExpressRoute tenha sido configurado para a minha organização?

Sim. Os pontos finais de serviço da Microsoft 365 são alcançáveis através da Internet, apesar de o ExpressRoute ter sido configurado para a sua rede. Por favor, consulte a equipa de networking da sua organização se a rede na sua localização está configurada para se ligar aos serviços da Microsoft 365 através do ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-microsoft-365-network-traffic-on-azure-expressroute"></a>Como posso planear uma elevada disponibilidade para o tráfego de rede Microsoft 365 no Azure ExpressRoute?
Consulte a recomendação de [Alta disponibilidade e failover com a Azure ExpressRoute](/microsoft-365/enterprise/network-planning-with-expressroute)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Posso aceder aos serviços do Office 365 Government Community (GCC) sobre um circuito ExpressRoute do Governo dos EUA?

Sim. Os pontos finais de serviço do Office 365 GCC são alcançáveis através do Expresso Do Governo dos EUA Azure. No entanto, primeiro precisa de abrir um bilhete de apoio no portal Azure para fornecer os prefixos que pretende anunciar à Microsoft. A sua conectividade com os serviços do Office 365 GCC será estabelecida após a solução do bilhete de apoio. 

## <a name="route-filters-for-microsoft-peering"></a>Filtros de rota para o espreitar microsoft

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Estou a ligar a Microsoft a espreitar pela primeira vez, que rotas vou ver?

Não verá nenhuma rota. Tem de anexar um filtro de rota ao seu circuito para iniciar anúncios pré-fixos. Para obter instruções, consulte [filtros de rota de configuração para o microsoft espreitar](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Liguei o olho da Microsoft e agora estou a tentar selecionar o Exchange Online, mas está a dar-me um erro que não estou autorizado a fazê-lo.

Ao utilizar filtros de rota, qualquer cliente pode ligar o olho da Microsoft. No entanto, para consumir os serviços da Microsoft 365, ainda precisa de ser autorizado pela Microsoft 365.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Permiti que a Microsoft espreitasse antes de 1 de agosto de 2017, como posso tirar partido dos filtros de rotas?

O circuito existente continuará a publicitar os prefixos para o Microsoft 365. Se pretender adicionar anúncios públicos Azure sobre o mesmo olho da Microsoft, pode criar um filtro de rota, selecionar os serviços que necessita anunciado (incluindo os serviços(s) Microsoft 365 que necessita) e anexar o filtro ao seu microsoft espreitando. Para obter instruções, consulte [filtros de rota de configuração para o microsoft espreitar](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Tenho a Microsoft a espreitar num local, agora estou a tentar habilitá-lo noutro local e não estou a ver nenhum prefixo.

* O perspitamento da Microsoft nos circuitos ExpressRoute que foram configurados antes de 1 de agosto de 2017 terá todos os prefixos de serviço anunciados através do olho da Microsoft, mesmo que os filtros de rota não estejam definidos.

* O perspitamento da Microsoft dos circuitos ExpressRoute que estejam configurados em ou depois de 1 de agosto de 2017 não terá quaisquer prefixos anunciados até que um filtro de rota seja ligado ao circuito. Não verá prefixos por defeito.

## <a name="expressroute-direct"></a><a name="expressRouteDirect"></a>ExpressRoute Direto

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="global-reach"></a><a name="globalreach"></a>Alcance Global

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]

## <a name="privacy"></a>Privacidade

### <a name="does-the-expressroute-service-store-customer-data"></a>O serviço ExpressRoute armazena os dados do cliente?

N.º
