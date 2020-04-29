---
title: FAQ - Azure ExpressRoute [ Azure ExpressRoute ] Microsoft Docs
description: O ExpressRoute FAQ contém informações sobre serviços suportados do Azure, Custos, Dados e Conexões, SLA, Fornecedores e Localizações, Largura de Banda e detalhes técnicos adicionais.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: jaredro
ms.openlocfilehash: 845c53ec970777901ae8d1c0abf5032ac705d3e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79264924"
---
# <a name="expressroute-faq"></a>FAQ do ExpressRoute

## <a name="what-is-expressroute"></a>O que é o ExpressRoute?

O ExpressRoute é um serviço Azure que permite criar ligações privadas entre datacenters da Microsoft e infraestruturas que estão nas suas instalações ou numa instalação de colocalização. As ligações ExpressRoute não passam pela Internet pública e oferecem maior segurança, fiabilidade e velocidades com latências mais baixas do que as ligações típicas através da Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Quais são os benefícios da utilização do ExpressRoute e das ligações de rede privada?

As ligações do ExpressRoute não passam para a Internet pública. Oferecem maior segurança, fiabilidade e velocidades, com tardios mais baixos e consistentes do que as ligações típicas através da Internet. Em alguns casos, a utilização de ligações ExpressRoute para transferir dados entre dispositivos no local e o Azure pode trazer benefícios significativos de custos.

### <a name="where-is-the-service-available"></a>Onde está o serviço disponível?

Consulte esta página para obter localização e disponibilidade de serviço: [Parceiros e locais ExpressRoute](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Como posso usar o ExpressRoute para ligar à Microsoft se não tenho parcerias com um dos parceiros da transportadora ExpressRoute?

Pode selecionar uma transportadora regional e aterrar ligações Ethernet a uma das localizações de fornecedores de intercâmbio suportados. Em seguida, pode consultar a Microsoft no local do fornecedor. Consulte a última secção de [parceiros e locais expressRoute](expressroute-locations.md) para ver se o seu prestador de serviços está presente em algum dos locais de troca. Em seguida, pode encomendar um circuito ExpressRoute através do prestador de serviços para ligar ao Azure.

### <a name="how-much-does-expressroute-cost"></a>Quanto custa o ExpressRoute?

Verifique [os detalhes dos preços](https://azure.microsoft.com/pricing/details/expressroute/) para obter informações sobre preços.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Se eu pagar um circuito ExpressRoute de uma dada largura de banda, a ligação VPN que eu adquirido ao meu fornecedor de serviços de rede tem de ser a mesma velocidade?

Não. Pode adquirir uma ligação VPN de qualquer velocidade ao seu prestador de serviços. No entanto, a sua ligação ao Azure está limitada à largura de banda do circuito ExpressRoute que compra.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Se eu pagar por um circuito ExpressRoute de uma dada largura de banda, tenho a capacidade de rebentar a velocidades mais altas, se necessário?

Sim. Os circuitos ExpressRoute estão configurados para permitir que rebente até duas vezes o limite de largura de banda que adquiriu sem custos adicionais. Consulte o seu prestador de serviços para ver se suportam esta capacidade. Isto não é por um período de tempo prolongado e não está garantido.  Se o tráfego fluir através de um Gateway ExpressRoute, a largura de banda para o sku é fixa e não é desatável.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Posso utilizar simultaneamente a mesma ligação de rede privada com rede virtual e outros serviços Azure?

Sim. Um circuito ExpressRoute, uma vez configurado, permite-lhe aceder a serviços dentro de uma rede virtual e outros serviços Azure simultaneamente. Conecta-se a redes virtuais através do caminho de observação privada e a outros serviços ao longo do caminho de peering da Microsoft.

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>Como são anunciados VNets no ExpressRoute Private Peering?

O gateway ExpressRoute irá anunciar o Espaço de *Endereços* do Azure VNet, não pode incluir/excluir ao nível da subnet. É sempre o Espaço de EndereçoVNet que é anunciado. Além disso, se o VNet Peering for utilizado e o VNet peered tiver ativado "Use Remote Gateway", o Espaço de Endereço seletiva vNet também será anunciado.

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>Quantos prefixos podem ser anunciados de um VNet para no local no ExpressRoute Private Peering?

Existe um máximo de 200 prefixos anunciados numa única ligação ExpressRoute, ou através de vNet peering usando o trânsito de gateway. Por exemplo, se tiver 199 espaços de endereço num único VNet ligado a um circuito ExpressRoute, todos os 199 prefixos serão anunciados no local. Em alternativa, se tiver um VNet habilitado a permitir o trânsito de gateway com 1 espaço de endereço e 150 VNets de porta-voz habilitados com a opção "Permitir gateway remoto", o VNet implantado com o gateway irá anunciar 151 prefixos para o local.

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>O que acontece se eu exceder o limite de prefixo numa ligação ExpressRoute?

A ligação entre o circuito ExpressRoute e o gateway (e vNets com recurso ao trânsito de gateway, se aplicável) irá diminuir. Restabelecer-se-á quando o limite de prefixo deixar de ser ultrapassado.  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>Posso filtrar rotas vindos da minha rede no local?

A única maneira de filtrar/incluir rotas é no router de borda no local. As Rotas definidas pelo utilizador podem ser adicionadas no VNet para afetar o encaminhamento específico, mas isso será estático e não fará parte do anúncio do BGP.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>A ExpressRoute oferece um Acordo de Nível de Serviço (SLA)?

Para obter informações, consulte a página [ExpressRoute SLA.](https://azure.microsoft.com/support/legal/sla/)

## <a name="supported-services"></a>Serviços suportados

O ExpressRoute suporta três domínios de [encaminhamento](expressroute-circuit-peerings.md) para vários tipos de serviços: peering privado, peering da Microsoft e peering público (depreciado).

### <a name="private-peering"></a>Peering privado

**Apoiado:**

* Redes virtuais, incluindo todas as máquinas virtuais e serviços de nuvem

### <a name="microsoft-peering"></a>Peering da Microsoft

Se o seu circuito ExpressRoute estiver ativado para o peering da Microsoft Azure, pode aceder às gamas públicas de [endereços IP utilizadas](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) em Azure sobre o circuito. O peering da Azure Microsoft fornecerá acesso aos serviços atualmente hospedados no Azure (com restrições geo-restrições dependendo do SKU do seu circuito). Para validar a disponibilidade de um serviço específico, pode consultar a documentação desse serviço para ver se existe uma gama reservada publicada para esse serviço. Em seguida, procure as gamas IP do serviço alvo e compare com as gamas listadas nas [gamas e etiquetas de serviço Azure IP – Ficheiro Public Cloud XML](https://www.microsoft.com/download/details.aspx?id=56519). Em alternativa, pode abrir um bilhete de apoio para o serviço em questão para esclarecimento.

**Apoiado:**

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Power BI - Disponível através de uma Comunidade Regional Azure, consulte [aqui](https://docs.microsoft.com/power-bi/service-admin-where-is-my-tenant-located) como descobrir a região do seu inquilino Power BI.
* Azure Active Directory
* [Windows Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/)
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (comunidade azure global services)
* Endereços IP públicos azure para IaaS (Máquinas Virtuais, Gateways de Rede Virtual, Balanceadores de Carga, etc)  
* A maioria dos outros serviços azure também são apoiados. Consulte diretamente o serviço que pretende utilizar para verificar o suporte.

**Não suportado:**

* CDN
* Azure Front Door
* Servidor de Autenticação Multifactor (legado)
* Gestor de Tráfego

### <a name="public-peering"></a>Peering público

O público foi desativado em novos circuitos ExpressRoute. Os serviços Azure já estão disponíveis na Microsoft peering. Se você um circuito que foi criado antes de o público espreitar ser depreciado, você pode optar por usar o peering da Microsoft ou o público peering, dependendo dos serviços que deseja.

Para obter mais informações e etapas de configuração para o público, consulte o [público expressRoute.](about-public-peering.md)

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Porque é que vejo o estatuto de "prefixos públicos anunciados" como "Validação necessária", enquanto configurao o peering da Microsoft?

A Microsoft verifica se os "prefixos públicos anunciados" e "Peer ASN" (ou 'Customer ASN') são-lhe atribuídos no Registo de Encaminhamento de Internet. Se estiver a receber os prefixos públicos de outra entidade e se a atribuição não for registada com o registo de encaminhamento, a validação automática não estará concluída e exigirá validação manual. Se a validação automática falhar, verá a mensagem 'Validação necessária'.

Se vir a mensagem 'Validação necessária', recolha os documentos que mostram que os prefixos públicos são atribuídos à sua organização pela entidade que está listada como proprietária dos prefixos no registo de encaminhamento e submeta estes documentos para validação manual, abrindo um bilhete de apoio como mostrado abaixo.

![](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>A Dynamics 365 é suportada no ExpressRoute?

Os ambientes Dynamics 365 e Common Data Service (CDS) estão alojados no Azure e, portanto, os clientes beneficiam do apoio da ExpressRoute subjacente aos recursos Azure. Pode ligar-se aos seus pontos finais de serviço se o filtro do router incluir as regiões Azure onde os ambientes Dynamics 365/CDS estão hospedados.

> [!NOTE]
> [O ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium) **não** é necessário para a conectividade Dynamics 365 via Azure ExpressRoute.

## <a name="data-and-connections"></a>Dados e ligações

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Existem limites na quantidade de dados que posso transferir através do ExpressRoute?

Não estabelecemos um limite para a quantidade de transferência de dados. Consulte [os detalhes](https://azure.microsoft.com/pricing/details/expressroute/) de preços para obter informações sobre as tarifas de largura de banda.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Que velocidades de ligação são suportadas pela ExpressRoute?

Ofertas de largura de banda suportadas:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Que prestadores de serviços estão disponíveis?

Consulte [os parceiros e localizações](expressroute-locations.md) do ExpressRoute para a lista de prestadores de serviços e locais.

## <a name="technical-details"></a>Detalhes técnicos

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Quais são os requisitos técnicos para ligar a minha localização no local ao Azure?

Consulte a [página de pré-requisitos](expressroute-prerequisites.md) do ExpressRoute para os requisitos.

### <a name="are-connections-to-expressroute-redundant"></a>As ligações com a ExpressRoute são redundantes?

Sim. Cada circuito ExpressRoute tem um par redundante de ligações cruzadas configuradas para fornecer alta disponibilidade.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Vou perder conectividade se uma das minhas ligações expressroute falhar?

Não perderá a conectividade se uma das ligações cruzadas falhar. Uma ligação redundante está disponível para suportar a carga da sua rede e fornecer uma alta disponibilidade do seu circuito ExpressRoute. Pode ainda criar um circuito num local de observação diferente para alcançar a resiliência ao nível do circuito.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Como implemento a redundância no epeering privado?

Vários circuitos ExpressRoute de diferentes locais de observação podem ser ligados à mesma rede virtual para fornecer alta disponibilidade no caso de um único circuito ficar indisponível. Em seguida, pode [atribuir pesos mais elevados](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection) à ligação local para favorecer um circuito específico. Recomenda-se vivamente que os clientes instalem pelo menos dois circuitos ExpressRoute para evitar pontos de falha únicos. 

Consulte [aqui](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) para projetar para alta disponibilidade e [aqui](https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering) para projetar para a recuperação de desastres.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Como implemento a redundância no peering da Microsoft?

É altamente recomendado quando os clientes estão a usar a Microsoft a espreitar para aceder a serviços públicos do Azure como o Azure Storage ou o Azure SQL, bem como aos clientes que estão a usar a Microsoft a espreitar o Office 365 que implementam múltiplos circuitos em diferentes locais de observação para evitar pontos únicos de falha. Os clientes podem anunciar o mesmo prefixo em ambos os circuitos e usar [as predespesas AS PATH](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending) ou anunciar diferentes prefixos para determinar o caminho a partir do local.

Consulte [aqui](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) para projetar para alta disponibilidade.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Como posso garantir uma elevada disponibilidade numa rede virtual ligada ao ExpressRoute?

Pode obter uma elevada disponibilidade ligando circuitos ExpressRoute em diferentes locais de observação (por exemplo, Singapura, Singapura2) à sua rede virtual. Se um circuito ExpressRoute cair, a conectividade falhará em outro circuito ExpressRoute. Por padrão, o tráfego que sai da sua rede virtual é encaminhado com base no Encaminhamento Multi-Caminho de Custo Igual (ECMP). Pode utilizar o Peso de Ligação para preferir um circuito ao outro. Para mais informações, consulte Otimização do [Encaminhamento ExpressRoute](expressroute-optimize-routing.md).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Como posso garantir que o meu tráfego destinado a serviços públicos azure como o Azure Storage e o Azure SQL na Microsoft a espreitar ou a espreitar público é preferido no caminho da ExpressRoute?

Tem de implementar o atributo *de Preferência Local* no seu(s) router(s) para garantir que o caminho desde as instalações até ao Azure é sempre preferido no seu(s) circuito ExpressRoute.

Consulte detalhes adicionais [aqui](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#path-selection-on-microsoft-and-public-peerings) sobre a seleção de caminhos BGP e configurações comuns do router. 

### <a name="if-im-not-co-located-at-a-cloud-exchange-and-my-service-provider-offers-point-to-point-connection-do-i-need-to-order-two-physical-connections-between-my-on-premises-network-and-microsoft"></a><a name="onep2plink"></a>Se não estou co-localizado numa troca de nuvens e o meu fornecedor de serviços oferece ligação ponto a ponto, preciso de encomendar duas ligações físicas entre a minha rede no local e a Microsoft?

Se o seu prestador de serviços conseguir estabelecer dois circuitos virtuais Ethernet sobre a ligação física, só precisa de uma ligação física. A ligação física (por exemplo, uma fibra ótica) é terminada num dispositivo de camada 1 (L1) (ver imagem). Os dois circuitos virtuais Ethernet estão marcados com diferentes IDs VLAN, um para o circuito primário, e um para o secundário. As iDs VLAN estão no cabeçalho ethernet de 802.1Q exterior. O cabeçalho interior 802.1Q Ethernet (não mostrado) é mapeado para um domínio de [encaminhamento expressroute](expressroute-circuit-peerings.md)específico .

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Posso estender um dos meus VLANs ao Azure usando a ExpressRoute?

Não. Não suportamos extensões de conectividade camada 2 em Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Posso ter mais do que um circuito ExpressRoute na minha subscrição?

Sim. Pode ter mais do que um circuito ExpressRoute na sua subscrição. O limite de predefinição está definido para 10. Pode contactar o Microsoft Support para aumentar o limite, se necessário.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Posso ter circuitos ExpressRoute de diferentes prestadores de serviços?

Sim. Pode ter circuitos ExpressRoute com muitos prestadores de serviços. Cada circuito ExpressRoute está associado apenas a um prestador de serviços. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Vejo dois locais de observação da ExpressRoute no mesmo metro, por exemplo, Singapura e Singapura2. Que localização de espreitar devo escolher para criar o meu circuito ExpressRoute?
Se o seu prestador de serviços oferecer expressRoute em ambos os sites, pode trabalhar com o seu fornecedor e escolher qualquer um dos sites para configurar o ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Posso ter vários circuitos ExpressRoute no mesmo metro? Posso ligá-los à mesma rede virtual?

Sim. Pode ter vários circuitos ExpressRoute com os mesmos ou diferentes prestadores de serviços. Se o metrô tiver vários locais de observação da ExpressRoute e os circuitos forem criados em diferentes locais de observação, pode ligá-los à mesma rede virtual. Se os circuitos forem criados no mesmo local de observação, pode ligar até 4 circuitos à mesma rede virtual.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Como ligo as minhas redes virtuais a um circuito ExpressRoute

Os passos básicos são:

* Estabeleça um circuito ExpressRoute e o fornecedor de serviços o ative.
* Você, ou o fornecedor, deve configurar os epeerings bGP.
* Ligue a rede virtual ao circuito ExpressRoute.

Para obter mais informações, consulte os fluxos de [trabalho expressRoute para os estados de fornecimento de circuitos e circuitos](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Existem limites de conectividade para o meu circuito ExpressRoute?

Sim. O artigo de [parceiros e localizações expressRoute](expressroute-locations.md) fornece uma visão geral dos limites de conectividade para um circuito ExpressRoute. A conectividade para um circuito ExpressRoute está limitada a uma única região geopolítica. A conectividade pode ser expandida para atravessar regiões geopolíticas, permitindo a funcionalidade premium ExpressRoute.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Posso ligar-me a mais de uma rede virtual a um circuito ExpressRoute?

Sim. Pode ter até 10 ligações de redes virtuais num circuito expressRoute padrão e até 100 num [circuito premium ExpressRoute](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Tenho várias subscrições azure que contêm redes virtuais. Posso ligar redes virtuais que estão em subscrições separadas a um único circuito ExpressRoute?

Sim. Pode ligar até 10 redes virtuais na mesma subscrição que o circuito ou subscrições diferentes utilizando um único circuito ExpressRoute. Este limite pode ser aumentado permitindo a funcionalidade premium ExpressRoute.

Para mais informações, consulte [Partilhar um circuito ExpressRoute em várias subscrições.](expressroute-howto-linkvnet-arm.md)

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Tenho várias assinaturas Azure associadas a diferentes inquilinos do Azure Ative Directory ou inscrições do Acordo Empresarial. Posso ligar redes virtuais que estão em inquilinos separados e matriculas a um único circuito ExpressRoute que não esteja no mesmo inquilino ou matricula?

Sim. As autorizações ExpressRoute podem abranger limites de subscrição, inquilino e inscrição sem necessidade de configuração adicional. 

Para mais informações, consulte [Partilhar um circuito ExpressRoute em várias subscrições.](expressroute-howto-linkvnet-arm.md)

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>As redes virtuais estão ligadas ao mesmo circuito isoladas umas das outras?

Não. Do ponto de vista do encaminhamento, todas as redes virtuais ligadas ao mesmo circuito ExpressRoute fazem parte do mesmo domínio de encaminhamento e não estão isoladas umas das outras. Se necessitar de isolamento de rotas, precisa de criar um circuito expressroute separado.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Posso ter uma rede virtual ligada a mais do que um circuito ExpressRoute?

Sim. Pode ligar uma única rede virtual com até quatro circuitos ExpressRoute nos mesmos locais de observação ou diferentes locais de observação. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Posso aceder à Internet a partir das minhas redes virtuais ligadas aos circuitos ExpressRoute?

Sim. Se não tiver anunciado rotas por defeito (0.0.0.0)ou prefixos de rota da Internet através da sessão bGP, pode ligar-se à Internet a partir de uma rede virtual ligada a um circuito ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Posso bloquear a conectividade da Internet com redes virtuais ligadas aos circuitos ExpressRoute?

Sim. Pode anunciar rotas padrão (0.0.0.0/0) para bloquear toda a conectividade da Internet com máquinas virtuais implantadas dentro de uma rede virtual e encaminhar todo o tráfego através do circuito ExpressRoute.

Se publicitar rotas padrão, forçamos o tráfego a serviços oferecidos sobre o armazenamento da Microsoft (como o armazenamento Azure e o SQL DB) de volta às suas instalações. Terá de configurar os seus routers para devolver o tráfego ao Azure através do caminho de observação da Microsoft ou através da Internet. Se tiver ativado um ponto final de serviço para o serviço, o tráfego para o serviço não é forçado às suas instalações. O tráfego permanece dentro da rede de espinha dorsal Azure. Para saber mais sobre os pontos finais do serviço, consulte [pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json) do serviço de rede Virtual

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>As redes virtuais ligadas ao mesmo circuito ExpressRoute podem falar entre si?

Sim. As máquinas virtuais implantadas em redes virtuais ligadas ao mesmo circuito ExpressRoute podem comunicar entre si.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Posso usar a conectividade site-a-site para redes virtuais em conjunto com o ExpressRoute?

Sim. ExpressRoute pode coexistir com VPNs site-to-site. Consulte a [Configure ExpressRoute e as ligações coexistentes site-to-site](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Por que existe um endereço IP público associado à gateway ExpressRoute numa rede virtual?

O endereço IP público é utilizado apenas para gestão interna, e não constitui uma exposição de segurança da sua rede virtual.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Há limites no número de rotas que posso anunciar?

Sim. Aceitamos até 4000 prefixos de rota para o peering privado e 200 para o peering da Microsoft. Pode aumentar isto para 10.000 rotas para o peering privado se ativar a funcionalidade premium ExpressRoute.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Existem restrições nas gamas IP que posso anunciar durante a sessão do BGP?

Não aceitamos prefixos privados (RFC1918) para a sessão de BGP da Microsoft. Aceitamos qualquer tamanho de prefixo (até /32) tanto na Microsoft como no peering privado.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>O que acontece se eu exceder os limites de BGP?

As sessões de BGP serão retiradas. Serão reiniciados assim que a contagem de prefixo sairá abaixo do limite.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>O que é o ExpressRoute BGP tempo de espera? Pode ser ajustado?

O tempo de espera é 180. As mensagens de manutenção são enviadas a cada 60 segundos. Estas são configurações fixas no lado da Microsoft que não podem ser alteradas. É possível configurar diferentes tempos, e os parâmetros da sessão bGP serão negociados em conformidade.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Posso alterar a largura de banda de um circuito ExpressRoute?

Sim, pode tentar aumentar a largura de banda do seu circuito ExpressRoute no portal Azure, ou utilizando o PowerShell. Se houver capacidade disponível na porta física em que o seu circuito foi criado, a sua mudança é bem sucedida. 

Se a sua mudança falhar, significa que ou não resta capacidade suficiente na porta atual e precisa de criar um novo circuito ExpressRoute com maior largura de banda, ou que não haja capacidade adicional nesse local, caso em que não será capaz de aumentar a largura de banda. 

Também terá de acompanhar o seu fornecedor de conectividade para garantir que atualizam os aceleradores dentro das suas redes para suportar o aumento da largura de banda. No entanto, não é possível reduzir a largura de banda do seu circuito ExpressRoute. Tem de criar um novo circuito ExpressRoute com largura de banda mais baixa e eliminar o circuito antigo.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Como posso alterar a largura de banda de um circuito ExpressRoute?

Pode atualizar a largura de banda do circuito ExpressRoute utilizando o CMDLET REST API ou PowerShell.

## <a name="expressroute-premium"></a>Prémio ExpressRoute

### <a name="what-is-expressroute-premium"></a>O que é o prémio ExpressRoute?

O prémio ExpressRoute é uma coleção das seguintes funcionalidades:

* Aumento do limite da tabela de encaminhamento de 4000 rotas para 10.000 rotas para o epeering privado.
* Aumento do número de ligações VNets e ExpressRoute Global Reach que podem ser ativadas num circuito ExpressRoute (padrão é 10). Para mais informações, consulte a tabela [Limites ExpressRoute.](#limits)
* Conectividade com o Office 365
* Conectividade global sobre a rede central da Microsoft. Agora pode ligar um VNet numa região geopolítica com um circuito ExpressRoute noutra região.<br>
    **Exemplos:**

    *  Pode ligar um VNet criado na Europa West a um circuito ExpressRoute criado em Silicon Valley. 
    *  No olhar da Microsoft, os prefixos de outras regiões geopolíticas são anunciados de tal forma que pode ligar-se, por exemplo, ao SQL Azure na Europa West a partir de um circuito em Silicon Valley.


### <a name="how-many-vnets-and-expressroute-global-reach-connections-can-i-enable-on-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a><a name="limits"></a>Quantas ligações VNets e ExpressRoute Global Reach posso ativar num circuito ExpressRoute se eu ativar o prémio ExpressRoute?

As tabelas que se seguem mostram os limites expressRoute e o número de ligações VNets e ExpressRoute Global Reach por circuito ExpressRoute:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Como posso ativar o prémio ExpressRoute?

As funcionalidades premium ExpressRoute podem ser ativadas quando a funcionalidade está ativada e podem ser desligadas atualizando o estado do circuito. Pode ativar o prémio ExpressRoute no tempo de criação do circuito, ou pode chamar o CMDLET REST API/PowerShell.

### <a name="how-do-i-disable-expressroute-premium"></a>Como desabilito o prémio ExpressRoute?

Pode desativar o prémio ExpressRoute chamando o CMDLET REST API ou PowerShell. Tem de se certificar de que a sua conectividade tem de cumprir os limites padrão antes de desativar o prémio ExpressRoute. Se a sua utilização ultrapassar os limites de predefinição, o pedido de desativação do prémio ExpressRoute falha.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Posso escolher as funcionalidades que quero do conjunto de funcionalidades premium?

Não. Não pode escolher as características. Ativamos todas as funcionalidades quando liga o prémio ExpressRoute.

### <a name="how-much-does-expressroute-premium-cost"></a>Quanto custa o prémio ExpressRoute?

Consulte [os detalhes](https://azure.microsoft.com/pricing/details/expressroute/) de preços para o custo.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Pago o prémio ExpressRoute para além das tarifas padrão da ExpressRoute?

Sim. Os encargos premium ExpressRoute aplicam-se em cima das tarifas e encargos do circuito ExpressRoute exigidos pelo fornecedor de conectividade.

## <a name="expressroute-local"></a>Rota Expressa Local
### <a name="what-is-expressroute-local"></a>O que é ExpressRoute Local?
ExpressRoute Local é um circuito SKU da ExpressRoute, além do Standard SKU e do Premium SKU. Uma característica chave do Local é que um circuito local num local de observação ExpressRoute dá-lhe acesso apenas a uma ou duas regiões Azure dentro ou perto do mesmo metrô. Em contraste, um circuito Standard dá-lhe acesso a todas as regiões azure em uma área geopolítica e um circuito Premium para todas as regiões Azure em todo o mundo. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>Quais são os benefícios do ExpressRoute Local?
Embora necessite de pagar a transferência de dados da Saída para o circuito Standard ou Premium ExpressRoute, não paga transferência de dados de saída separadamente para o seu circuito ExpressRoute Local. Por outras palavras, o preço do ExpressRoute Local inclui taxas de transferência de dados. O ExpressRoute Local é uma solução mais económica se tiver uma quantidade massiva de dados para transferir e pode trazer os seus dados através de uma ligação privada a um local de observação expressRoute perto das regiões desejadas do Azure. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Quais as funcionalidades disponíveis e o que não está no ExpressRoute Local?
Em comparação com um circuito Standard ExpressRoute, um circuito local tem o mesmo conjunto de funcionalidades exceto:
* Âmbito de acesso às regiões de Azure, tal como acima descrito
* ExpressRoute Global Reach não está disponível no Local

O ExpressRoute Local também tem os mesmos limites de recursos (por exemplo, o número de VNets por circuito) que o Standard. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Onde está disponível o ExpressRoute Local e quais as regiões de Azure a que cada local de observação está mapeado?
O ExpressRoute Local está disponível nos locais onde uma ou duas regiões Azure estão próximas. Não está disponível num local onde não exista nenhuma região de Azure nesse estado ou província ou país. Por favor, consulte os mapeamentos exatos [na página Locais](expressroute-locations-providers.md).  

## <a name="expressroute-for-office-365"></a>Rota expressa para o Escritório 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services"></a>Como posso criar um circuito ExpressRoute para ligar aos serviços do Office 365?

1. Reveja a [página de pré-requisitos expressRoute](expressroute-prerequisites.md) para se certificar de que cumpre os requisitos.
2. Para garantir que as suas necessidades de conectividade são satisfeitas, reveja a lista de prestadores de serviços e localizações no artigo dos [parceiros e locais expressRoute.](expressroute-locations.md)
3. Planeie os seus requisitos de capacidade revendo o planeamento da rede e a [sintonização de desempenho para o Office 365](https://aka.ms/tune/).
4. Siga os passos listados nos fluxos de trabalho para configurar fluxos de trabalho de conectividade [ExpressRoute para os estados de fornecimento de circuitos e circuitos](expressroute-workflows.md).

> [!IMPORTANT]
> Certifique-se de que ativou o add-on premium ExpressRoute ao configurar a conectividade com os serviços do Office 365.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services"></a>Os meus circuitos ExpressRoute existentes podem suportar a conectividade com os serviços do Office 365?

Sim. O circuito ExpressRoute existente pode ser configurado para suportar a conectividade com os serviços do Office 365. Certifique-se de que tem capacidade suficiente para se ligar aos serviços do Office 365 e que permitiu um complemento premium. [O planeamento da rede e a sintonização de desempenho do Office 365](https://aka.ms/tune/) ajudam-no a planear as suas necessidades de conectividade. Consulte também [Criar e modificar um circuito ExpressRoute](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Que serviços do Office 365 podem ser acedidos sobre uma ligação ExpressRoute?

Consulte a página de intervalos de [endereços office 365 URLs e IP](https://aka.ms/o365endpoints) para obter uma lista atualizada de serviços suportados sobre o ExpressRoute.

### <a name="how-much-does-expressroute-for-office-365-services-cost"></a>Quanto custa o ExpressRoute para os serviços do Office 365?

Os serviços do Office 365 exigem que o complemento premium seja ativado. Consulte a página de detalhes de [preços](https://azure.microsoft.com/pricing/details/expressroute/) para obter custos.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Em que regiões está apoiada a ExpressRoute para o Office 365?

Consulte [os parceiros e locais expressRoute](expressroute-locations.md) para obter informações.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Posso aceder ao Office 365 através da Internet, mesmo que o ExpressRoute tenha sido configurado para a minha organização?

Sim. Os pontos finais do serviço office 365 são acessíveis através da Internet, embora o ExpressRoute tenha sido configurado para a sua rede. Consulte a equipa de networking da sua organização se a rede na sua localização está configurada para ligar aos serviços do Office 365 através do ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Como posso planear uma elevada disponibilidade para o tráfego de rede do Office 365 no Azure ExpressRoute?
Consulte a recomendação de [alta disponibilidade e failover com azure ExpressRoute](https://aka.ms/erhighavailability)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Posso aceder aos serviços do Office 365 Da Comunidade governamental dos EUA (CcG) sobre um circuito Azure Us Government ExpressRoute?

Sim. Os pontos finais do serviço do Office 365 GCC são acessíveis através da Rota Expressa do Governo dos EUA. No entanto, primeiro precisa de abrir um bilhete de suporte no portal Azure para fornecer os prefixos que pretende anunciar à Microsoft. A sua conectividade com os serviços do Office 365 GCC será estabelecida após a resolução do bilhete de apoio. 

## <a name="route-filters-for-microsoft-peering"></a>Filtros de rota para o peering da Microsoft

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Estou a ligar a Microsoft a espreitar pela primeira vez, que rotas vou ver?

Não verá nenhuma rota. Tem de fixar um filtro de rota no seu circuito para iniciar anúncios prefixos. Para obter instruções, consulte os [filtros de rota Configure para o peering da Microsoft](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Virei a Microsoft a espreitar e agora estou a tentar selecionar o Exchange Online, mas está a dar-me um erro por não estar autorizado a fazê-lo.

Ao utilizar filtros de rota, qualquer cliente pode ligar o olhar da Microsoft. No entanto, para consumir os serviços do Office 365, ainda precisa de ser autorizado pelo Office 365.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Permiti que a Microsoft espreitasse antes de 1 de agosto de 2017, como posso tirar partido dos filtros de rotas?

O seu circuito existente continuará a publicitar os prefixos do Office 365. Se pretender adicionar anúncios de prefixos públicos Do Azure sobre o mesmo peering da Microsoft, pode criar um filtro de rota, selecionar os serviços de que necessita (incluindo o(s) serviço (s) do Office 365 que necessita) e anexar o filtro ao seu peering microsoft. Para obter instruções, consulte os [filtros de rota Configure para o peering da Microsoft](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Tenho a Microsoft a espreitar para um local, agora estou a tentar capacitá-lo noutro local e não estou a ver nenhum prefixo.

* O peering da Microsoft dos circuitos ExpressRoute que foram configurados antes de 1 de agosto de 2017 terá todos os prefixos de serviço anunciados através do peering da Microsoft, mesmo que os filtros de rota não estejam definidos.

* O peering da Microsoft dos circuitos ExpressRoute que estejam configurados em ou depois de 1 de agosto de 2017 não terá quaisquer prefixos anunciados até que um filtro de rota seja ligado ao circuito. Não verá prefixos por defeito.

## <a name="expressroute-direct"></a><a name="expressRouteDirect"></a>ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="global-reach"></a><a name="globalreach"></a>Alcance Global

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
