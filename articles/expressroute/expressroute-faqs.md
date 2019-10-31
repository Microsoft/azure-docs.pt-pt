---
title: Perguntas frequentes-Azure ExpressRoute | Microsoft Docs
description: As perguntas frequentes do ExpressRoute contêm informações sobre os serviços do Azure, custo, dados e conexões com suporte, SLA, provedores e locais, largura de banda e detalhes técnicos adicionais.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 4dd4831d6437b7026459dc358455335dd0fa221b
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063107"
---
# <a name="expressroute-faq"></a>FAQ do ExpressRoute

## <a name="what-is-expressroute"></a>O que é o ExpressRoute?

O ExpressRoute é um serviço do Azure que permite criar conexões privadas entre os data centers da Microsoft e a infraestrutura que está no local ou em uma instalação de colocalização. As conexões do ExpressRoute não passam pela Internet pública e oferecem maior segurança, confiabilidade e velocidades com latências menores do que conexões típicas pela Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Quais são os benefícios de usar o ExpressRoute e as conexões de rede privada?

As ligações do ExpressRoute não passam para a Internet pública. Eles oferecem maior segurança, confiabilidade e velocidades, com latências menores e consistentes do que conexões típicas pela Internet. Em alguns casos, o uso de conexões do ExpressRoute para transferir dados entre dispositivos locais e o Azure pode gerar benefícios de custo significativos.

### <a name="where-is-the-service-available"></a>Onde o serviço está disponível?

Consulte esta página para localização de serviço e disponibilidade: [locais e parceiros do ExpressRoute](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Como posso usar o ExpressRoute para se conectar à Microsoft se eu não tiver parcerias com um dos parceiros da operadora do ExpressRoute?

Você pode selecionar uma portadora regional e conexões Ethernet Land para um dos locais do provedor do Exchange com suporte. Em seguida, você pode emparelhar com a Microsoft no local do provedor. Verifique a última seção de [parceiros e locais do ExpressRoute](expressroute-locations.md) para ver se o seu provedor de serviços está presente em qualquer um dos locais do Exchange. Em seguida, você pode solicitar um circuito do ExpressRoute por meio do provedor de serviços para se conectar ao Azure.

### <a name="how-much-does-expressroute-cost"></a>Quanto custa o ExpressRoute?

Verifique os [detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) para obter informações sobre preços.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Se eu pagar por um circuito de ExpressRoute de uma determinada largura de banda, a conexão VPN que eu comprar do provedor de serviços de rede precisaria ser a mesma velocidade?

Não. Você pode comprar uma conexão VPN de qualquer velocidade do seu provedor de serviços. No entanto, sua conexão com o Azure é limitada à largura de banda do circuito do ExpressRoute comprada.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Se eu pagar por um circuito de ExpressRoute de uma determinada largura de banda, tenho a capacidade de aumentar para velocidades mais altas, se necessário?

Sim. Os circuitos do ExpressRoute são configurados para permitir a intermitência até duas vezes o limite de largura de banda adquirido sem custo adicional. Verifique com seu provedor de serviços para ver se eles dão suporte a essa capacidade. Isso não é por um período de tempo prolongado e não é garantido.  Se o tráfego fluir por um gateway de ExpressRoute, a largura de banda para a SKU será fixa e não ficará intermitente.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Posso usar a mesma conexão de rede privada com rede virtual e outros serviços do Azure simultaneamente?

Sim. Um circuito do ExpressRoute, uma vez configurado, permite que você acesse os serviços em uma rede virtual e outros serviços do Azure simultaneamente. Você se conecta a redes virtuais por meio do caminho de emparelhamento privado e a outros serviços por meio do caminho de emparelhamento da Microsoft.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>O ExpressRoute oferece um Contrato de Nível de Serviço (SLA)?

Para obter informações, consulte a página [SLA do ExpressRoute](https://azure.microsoft.com/support/legal/sla/) .

## <a name="supported-services"></a>Serviços com suporte

O ExpressRoute dá suporte a [três domínios de roteamento](expressroute-circuit-peerings.md) para vários tipos de serviços: emparelhamento privado, emparelhamento da Microsoft e emparelhamento público.

### <a name="private-peering"></a>Peering privado

* Redes virtuais, incluindo todas as máquinas virtuais e serviços de nuvem

### <a name="microsoft-peering"></a>Peering da Microsoft

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Power BI-disponível por meio de uma comunidade regional do Azure, consulte [aqui](https://docs.microsoft.com/power-bi/service-admin-where-is-my-tenant-located) para saber como descobrir a região do seu locatário de Power bi.
* Azure Active Directory
* [Área de trabalho virtual do Windows](https://azure.microsoft.com/services/virtual-desktop/)
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (comunidade de serviços globais do Azure)
* Há suporte para a maioria dos serviços do Azure. Consulte diretamente o serviço que você deseja usar para verificar o suporte.<br><br>**Não há suporte para os seguintes serviços**:
    * CDN
    * Azure Front Door
    * Servidor de autenticação multifator (Herdado)
    * Gestor de Tráfego

### <a name="public-peering"></a>Peering público

>[!NOTE]
>O emparelhamento público foi desabilitado em novos circuitos do ExpressRoute. Os serviços do Azure estão disponíveis no emparelhamento da Microsoft.
>

* Power BI
* Há suporte para a maioria dos serviços do Azure. Consulte diretamente o serviço que você deseja usar para verificar o suporte.<br><br>
  **Não há suporte para os seguintes serviços**:
    * CDN
    * Azure Front Door
    * Servidor de autenticação multifator (Herdado)
    * Gestor de Tráfego

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Por que vejo o status de ' prefixos públicos anunciados ' como ' validação necessária ' ao configurar o emparelhamento da Microsoft?

A Microsoft verifica se os ' prefixos públicos publicados ' e ' peer ASN ' (ou ' cliente ASN ') especificados estão atribuídos a você no registro de roteamento da Internet. Se você estiver obtendo os prefixos públicos de outra entidade e se a atribuição não for registrada com o registro de roteamento, a validação automática não será concluída e exigirá validação manual. Se a validação automática falhar, você verá a mensagem "validação necessária".

Se você vir a mensagem "validação necessária", colete os documentos que mostram os prefixos públicos são atribuídos à sua organização pela entidade que está listada como o proprietário dos prefixos no registro de roteamento e envie esses documentos para validação manual por abrindo um tíquete de suporte, conforme mostrado abaixo.

![](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>O Dynamics 365 tem suporte no ExpressRoute?

Os ambientes do Dynamics 365 e do Common Data Service (CDS) são hospedados no Azure e, portanto, os clientes se beneficiam do suporte do ExpressRoute subjacente para os recursos do Azure. Você pode se conectar a seus pontos de extremidade de serviço se o filtro do roteador incluir as regiões do Azure em que seus ambientes Dynamics 365/CDS estão hospedados.

> [!NOTE]
> O [Expressroute Premium](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-faqs#expressroute-premium) **não** é necessário para a conectividade do Dynamics 365 por meio do Azure ExpressRoute.

## <a name="data-and-connections"></a>Dados e conexões

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Há limites na quantidade de dados que posso transferir usando o ExpressRoute?

Não definimos um limite na quantidade de transferência de dados. Consulte os [detalhes de preço](https://azure.microsoft.com/pricing/details/expressroute/) para obter informações sobre taxas de largura de banda.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Quais velocidades de conexão têm suporte pelo ExpressRoute?

Ofertas de largura de banda com suporte:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Quais provedores de serviços estão disponíveis?

Consulte [parceiros e locais do ExpressRoute](expressroute-locations.md) para obter a lista de provedores de serviços e locais.

## <a name="technical-details"></a>Detalhes técnicos

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Quais são os requisitos técnicos para conectar meu local ao Azure?

Consulte a [página pré-requisitos do ExpressRoute](expressroute-prerequisites.md) para obter os requisitos.

### <a name="are-connections-to-expressroute-redundant"></a>As conexões com o ExpressRoute são redundantes?

Sim. Cada circuito do ExpressRoute tem um par redundante de conexões cruzadas configuradas para fornecer alta disponibilidade.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Perderei a conectividade se um dos meus links do ExpressRoute falhar?

Você não perderá a conectividade se uma das conexões cruzadas falhar. Uma conexão redundante está disponível para dar suporte à carga de sua rede e fornecer alta disponibilidade de seu circuito do ExpressRoute. Além disso, você pode criar um circuito em um local de emparelhamento diferente para obter resiliência no nível do circuito.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Como fazer implementar a redundância no emparelhamento privado?

Vários circuitos de ExpressRoute de diferentes locais de emparelhamento podem ser conectados à mesma rede virtual para fornecer alta disponibilidade no caso de um único circuito ficar indisponível. Em seguida, você pode [atribuir pesos mais altos](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection) à conexão local para favorecer um circuito específico. É altamente recomendável que os clientes configurem pelo menos dois circuitos de ExpressRoute para evitar pontos únicos de falha. 

Consulte [aqui](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) para obter alta disponibilidade e [aqui](https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering) para design para recuperação de desastres.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Como faço para implementar a redundância no emparelhamento da Microsoft?

É altamente recomendável quando os clientes estão usando o emparelhamento da Microsoft para acessar os serviços públicos do Azure, como o armazenamento do Azure ou o SQL do Azure, bem como clientes que estão usando o emparelhamento da Microsoft para o Office 365 que implementam vários circuitos em um emparelhamento diferente locais para evitar pontos únicos de falha. Os clientes podem anunciar o mesmo prefixo em ambos os circuitos e usar [como caminho prependente](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending) ou anunciar prefixos diferentes para determinar o caminho do local.

Consulte [aqui](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) para design para alta disponibilidade.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Como fazer garantir a alta disponibilidade em uma rede virtual conectada ao ExpressRoute?

Você pode obter alta disponibilidade conectando circuitos do ExpressRoute em diferentes locais de emparelhamento (por exemplo, Cingapura, Cingapura2) à sua rede virtual. Se um circuito do ExpressRoute falhar, a conectividade fará failover para outro circuito do ExpressRoute. Por padrão, o tráfego que sai de sua rede virtual é roteado com base no roteamento de vários caminhos de custo igual (ECMP). Você pode usar o peso de conexão para preferir um circuito para outro. Para obter mais informações, consulte [otimizando o roteamento do ExpressRoute](expressroute-optimize-routing.md).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-or-public-peering-is-preferred-on-the-expressroute-path"></a>Como fazer garantir que meu tráfego destinado a serviços públicos do Azure, como o armazenamento do Azure e o SQL do Azure na Microsoft ou o emparelhamento público, seja preferencial no caminho do ExpressRoute?

Você deve implementar o atributo de *preferência local* no (s) roteador (es) para garantir que o caminho do local para o Azure seja sempre preferido em seus circuitos do ExpressRoute.

Veja mais detalhes [aqui](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#path-selection-on-microsoft-and-public-peerings) na seleção de caminho BGP e configurações de roteador comuns. 

### <a name="onep2plink"></a>Se eu não estiver colocalizado em uma troca de nuvem e meu provedor de serviços oferecer conexão ponto a ponto, preciso solicitar duas conexões físicas entre minha rede local e a Microsoft?

Se o seu provedor de serviços puder estabelecer dois circuitos virtuais Ethernet pela conexão física, você só precisará de uma conexão física. A conexão física (por exemplo, uma fibra óptica) é encerrada em um dispositivo de camada 1 (L1) (consulte a imagem). Os dois circuitos virtuais de Ethernet são marcados com IDs de VLAN diferentes, um para o circuito primário e outro para o secundário. Essas IDs de VLAN estão no cabeçalho Ethernet 802.1 Q externo. O cabeçalho Ethernet 802.1 Q interno (não mostrado) é mapeado para um [domínio de roteamento do ExpressRoute](expressroute-circuit-peerings.md)específico.

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Posso estender uma das minhas VLANs para o Azure usando o ExpressRoute?

Não. Não há suporte para extensões de conectividade de camada 2 no Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Posso ter mais de um circuito de ExpressRoute em minha assinatura?

Sim. Você pode ter mais de um circuito de ExpressRoute em sua assinatura. O limite padrão é definido como 10. Você pode entrar em contato com Suporte da Microsoft para aumentar o limite, se necessário.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Posso ter circuitos do ExpressRoute de diferentes provedores de serviços?

Sim. Você pode ter circuitos de ExpressRoute com muitos provedores de serviços. Cada circuito do ExpressRoute é associado apenas a um provedor de serviços. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Vejo dois locais de emparelhamento do ExpressRoute no mesmo metro, por exemplo, Cingapura e Cingapura2. Qual local de emparelhamento devo escolher para criar meu circuito do ExpressRoute?
Se seu provedor de serviços oferecer o ExpressRoute em ambos os sites, você poderá trabalhar com seu provedor e escolher um dos sites para configurar o ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Posso ter vários circuitos de ExpressRoute no mesmo metro? Posso vinculá-los à mesma rede virtual?

Sim. Você pode ter vários circuitos do ExpressRoute com os mesmos provedores de serviço ou diferentes. Se o metro tiver vários locais de emparelhamento de ExpressRoute e os circuitos forem criados em locais de emparelhamento diferentes, você poderá vinculá-los à mesma rede virtual. Se os circuitos forem criados no mesmo local de emparelhamento, você poderá vincular até 4 circuitos para a mesma rede virtual.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Como fazer conectar minhas redes virtuais a um circuito do ExpressRoute

As etapas básicas são:

* Estabeleça um circuito do ExpressRoute e faça com que o provedor de serviços o habilite.
* Você, ou o provedor, deve configurar o emparelhamento via protocolo BGP.
* Vincule a rede virtual ao circuito do ExpressRoute.

Para obter mais informações, consulte [fluxos de trabalho do ExpressRoute para provisionamento de circuito e Estados de circuito](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Há limites de conectividade para meu circuito do ExpressRoute?

Sim. O artigo [parceiros e locais do expressroute](expressroute-locations.md) fornece uma visão geral dos limites de conectividade para um circuito do expressroute. A conectividade para um circuito do ExpressRoute é limitada a uma única região geopolítica. A conectividade pode ser expandida para várias regiões geopolítica habilitando o recurso Premium do ExpressRoute.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Posso vincular mais de uma rede virtual a um circuito do ExpressRoute?

Sim. Você pode ter até 10 conexões de redes virtuais em um circuito de ExpressRoute padrão e até 100 em um [circuito de expressroute Premium](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Tenho várias assinaturas do Azure que contêm redes virtuais. Posso conectar redes virtuais que estão em assinaturas separadas a um único circuito do ExpressRoute?

Sim. Você pode vincular até 10 redes virtuais na mesma assinatura que o circuito ou assinaturas diferentes usando um único circuito do ExpressRoute. Esse limite pode ser aumentado com a habilitação do recurso Premium do ExpressRoute.

Para obter mais informações, consulte [compartilhando um circuito do ExpressRoute entre várias assinaturas](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Tenho várias assinaturas do Azure associadas a locatários Azure Active Directory diferentes ou registros de Enterprise Agreement. Posso conectar redes virtuais que estão em locatários e registros separados a um único circuito do ExpressRoute que não está no mesmo locatário ou registro?

Sim. As autorizações do ExpressRoute podem abranger os limites de assinatura, locatário e registro sem a necessidade de configuração adicional. 

Para obter mais informações, consulte [compartilhando um circuito do ExpressRoute entre várias assinaturas](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>As redes virtuais estão conectadas ao mesmo circuito isoladas umas das outras?

Não. De uma perspectiva de roteamento, todas as redes virtuais vinculadas ao mesmo circuito de ExpressRoute fazem parte do mesmo domínio de roteamento e não são isoladas umas das outras. Se precisar de isolamento de rota, você precisará criar um circuito de ExpressRoute separado.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Posso ter uma rede virtual conectada a mais de um circuito do ExpressRoute?

Sim. Você pode vincular uma única rede virtual com até quatro circuitos de ExpressRoute nos mesmos ou em locais de emparelhamento diferentes. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Posso acessar a Internet de minhas redes virtuais conectadas a circuitos do ExpressRoute?

Sim. Se você não tiver anunciado rotas padrão (0.0.0.0/0) ou prefixos de rota da Internet por meio da sessão BGP, poderá se conectar à Internet de uma rede virtual vinculada a um circuito do ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Posso bloquear a conectividade com a Internet para redes virtuais conectadas a circuitos do ExpressRoute?

Sim. Você pode anunciar rotas padrão (0.0.0.0/0) para bloquear toda a conectividade com a Internet para máquinas virtuais implantadas em uma rede virtual e rotear todo o tráfego por meio do circuito do ExpressRoute.

Se você anunciar rotas padrão, forçaremos o tráfego para os serviços oferecidos pelo emparelhamento da Microsoft (como o armazenamento do Azure e o banco de BD SQL) de volta para seu local. Você precisará configurar seus roteadores para retornar o tráfego para o Azure por meio do caminho de emparelhamento da Microsoft ou pela Internet. Se você tiver habilitado um ponto de extremidade de serviço para o serviço, o tráfego para o serviço não será forçado para seu local. O tráfego permanece dentro da rede de backbone do Azure. Para saber mais sobre pontos de extremidade de serviço, consulte [pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>As redes virtuais vinculadas ao mesmo circuito do ExpressRoute se comunicam entre si?

Sim. As máquinas virtuais implantadas em redes virtuais conectadas ao mesmo circuito do ExpressRoute podem se comunicar entre si.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Posso usar a conectividade site a site para redes virtuais em conjunto com o ExpressRoute?

Sim. O ExpressRoute pode coexistir com VPNs site a site. Consulte [Configurar conexões coexistentes de ExpressRoute e site a site](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Por que há um endereço IP público associado ao gateway de ExpressRoute em uma rede virtual?

O endereço IP público é usado apenas para gerenciamento interno e não constitui uma exposição de segurança de sua rede virtual.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Há limites no número de rotas que posso anunciar?

Sim. Aceitamos até 4000 prefixos de rota para emparelhamento privado e 200 para emparelhamento da Microsoft. Você pode aumentar isso para 10.000 rotas para o emparelhamento privado se habilitar o recurso do ExpressRoute Premium.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Há restrições em intervalos de IP que posso anunciar pela sessão BGP?

Não aceitamos RFC1918 (prefixos privados) para a sessão BGP de emparelhamento da Microsoft. Aceitamos qualquer tamanho de prefixo (até/32) tanto na Microsoft quanto no emparelhamento privado.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>O que acontece se eu exceder os limites de BGP?

As sessões BGP serão descartadas. Eles serão redefinidos quando a contagem de prefixo ficar abaixo do limite.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>O que é o tempo de espera de BGP do ExpressRoute? Ele pode ser ajustado?

O tempo de espera é 180. As mensagens Keep-Alive são enviadas a cada 60 segundos. Essas são configurações fixas no lado da Microsoft que não podem ser alteradas. É possível configurar diferentes temporizadores e os parâmetros de sessão BGP serão negociados de acordo.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Posso alterar a largura de banda de um circuito do ExpressRoute?

Sim, você pode tentar aumentar a largura de banda do circuito do ExpressRoute no portal do Azure ou usando o PowerShell. Se houver capacidade disponível na porta física na qual o circuito foi criado, sua alteração terá êxito. 

Se a alteração falhar, significa que não há capacidade suficiente restante na porta atual e você precisa criar um novo circuito de ExpressRoute com maior largura de banda ou que não haja capacidade adicional nesse local. nesse caso, você não poderá aumentar o Larga. 

Você também terá de acompanhar seu provedor de conectividade para garantir que eles atualizem as restrições dentro de suas redes para dar suporte ao aumento da largura de banda. No entanto, não é possível reduzir a largura de banda do circuito do ExpressRoute. Você precisa criar um novo circuito do ExpressRoute com menor largura de banda e excluir o circuito antigo.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Como fazer alterar a largura de banda de um circuito do ExpressRoute?

Você pode atualizar a largura de banda do circuito do ExpressRoute usando a API REST ou o cmdlet do PowerShell.

## <a name="expressroute-premium"></a>ExpressRoute Premium

### <a name="what-is-expressroute-premium"></a>O que é o ExpressRoute Premium?

O ExpressRoute Premium é uma coleção dos seguintes recursos:

* Aumento do limite da tabela de roteamento de 4000 rotas para 10.000 rotas para emparelhamento privado.
* Maior número de conexões de Alcance Global VNets e ExpressRoute que podem ser habilitadas em um circuito do ExpressRoute (o padrão é 10). Para obter mais informações, consulte a tabela de [limites do ExpressRoute](#limits) .
* Conectividade com o Office 365
* Conectividade global na rede Microsoft Core. Agora você pode vincular uma VNet em uma região geopolítica com um circuito de ExpressRoute em outra região.<br>
    **Disso**

    *  Você pode vincular uma VNet criada na Europa Ocidental a um circuito de ExpressRoute criado no vale do silício. 
    *  No emparelhamento da Microsoft, os prefixos de outras regiões geopolítica são anunciados de modo que você possa se conectar ao, por exemplo, SQL Azure no oeste da Europa de um circuito no vale do silício.


### <a name="limits"></a>Quantas conexões do VNets e do ExpressRoute Alcance Global posso habilitar em um circuito do ExpressRoute se eu tiver habilitado o ExpressRoute Premium?

As tabelas a seguir mostram os limites de ExpressRoute e o número de conexões de Alcance Global VNets e ExpressRoute por circuito de ExpressRoute:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Como fazer habilitar o ExpressRoute Premium?

Os recursos do ExpressRoute Premium podem ser habilitados quando o recurso está habilitado e podem ser desligados atualizando o estado do circuito. Você pode habilitar o ExpressRoute Premium no momento da criação do circuito ou pode chamar a API REST/o cmdlet do PowerShell.

### <a name="how-do-i-disable-expressroute-premium"></a>Como fazer desabilitar o ExpressRoute Premium?

Você pode desabilitar o ExpressRoute Premium chamando a API REST ou o cmdlet do PowerShell. Você deve verificar se você dimensionou suas necessidades de conectividade para atender aos limites padrão antes de desabilitar o ExpressRoute Premium. Se a utilização for dimensionada além dos limites padrão, a solicitação para desabilitar o ExpressRoute Premium falhará.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Posso escolher os recursos que desejo do conjunto de recursos premium?

Não. Você não pode escolher os recursos. Habilitamos todos os recursos quando você ativa o ExpressRoute Premium.

### <a name="how-much-does-expressroute-premium-cost"></a>Quanto custa o ExpressRoute Premium?

Consulte os [detalhes de preço](https://azure.microsoft.com/pricing/details/expressroute/) para obter o custo.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Eu pago pelo ExpressRoute Premium além dos encargos do ExpressRoute padrão?

Sim. Os encargos do ExpressRoute Premium se aplicam aos encargos e encargos do circuito do ExpressRoute exigidos pelo provedor de conectividade

## <a name="expressroute-local"></a>Local do ExpressRoute
### <a name="what-is-expressroute-local"></a>O que é o ExpressRoute local?
O ExpressRoute local é um SKU do circuito do ExpressRoute, além do SKU Standard e do SKU Premium. Um dos principais recursos do local é que um circuito local em um local de emparelhamento do ExpressRoute fornece acesso apenas a uma ou duas regiões do Azure no ou perto do mesmo metro. Por outro lado, um circuito padrão fornece acesso a todas as regiões do Azure em uma área geopolítica e um circuito Premium para todas as regiões do Azure globalmente. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>Quais são os benefícios do ExpressRoute local?
Embora você precise pagar a transferência de dados de saída para o circuito de ExpressRoute Standard ou Premium, não pague a transferência de dados de saída separadamente para o circuito local do ExpressRoute. Em outras palavras, o preço do ExpressRoute local inclui taxas de transferência de dados. O ExpressRoute local é uma solução mais econômica se você tiver uma grande quantidade de dados a serem transferidos e você pode trazer seus dados por uma conexão privada para um local de emparelhamento do ExpressRoute perto das regiões do Azure desejadas. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Quais recursos estão disponíveis e o que não está no ExpressRoute local?
Em comparação com um circuito de ExpressRoute padrão, um circuito local tem o mesmo conjunto de recursos, exceto:
* Escopo de acesso às regiões do Azure, conforme descrito acima
* O ExpressRoute Alcance Global não está disponível no local

O ExpressRoute local também tem os mesmos limites de recursos (por exemplo, o número de VNets por circuito) que o padrão. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Onde o ExpressRoute local está disponível e a quais regiões do Azure cada local de emparelhamento está mapeado?
O ExpressRoute local está disponível nos locais de emparelhamento onde uma ou duas regiões do Azure são fechadas. Ele não está disponível em um local de emparelhamento onde não há nenhuma região do Azure nesse estado ou província ou país. Consulte os mapeamentos exatos na [página locais](expressroute-locations-providers.md).  

## <a name="expressroute-for-office-365"></a>ExpressRoute para Office 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services"></a>Como fazer criar um circuito de ExpressRoute para se conectar aos serviços do Office 365?

1. Examine a [página pré-requisitos do ExpressRoute](expressroute-prerequisites.md) para verificar se você atende aos requisitos.
2. Para garantir que suas necessidades de conectividade sejam atendidas, examine a lista de provedores de serviços e locais no artigo [parceiros e locais do ExpressRoute](expressroute-locations.md) .
3. Planeje seus requisitos de capacidade revisando [planejamento de rede e ajuste de desempenho para o Office 365](https://aka.ms/tune/).
4. Siga as etapas listadas nos fluxos de trabalho para configurar a conectividade [fluxos de trabalho do ExpressRoute para provisionamento de circuito e Estados de circuito](expressroute-workflows.md).

> [!IMPORTANT]
> Certifique-se de que você habilitou o complemento Premium do ExpressRoute ao configurar a conectividade com os serviços do Office 365.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services"></a>Meus circuitos de ExpressRoute existentes podem dar suporte à conectividade com os serviços do Office 365?

Sim. O circuito do ExpressRoute existente pode ser configurado para dar suporte à conectividade com os serviços do Office 365. Verifique se você tem capacidade suficiente para se conectar aos serviços do Office 365 e se você habilitou o complemento Premium. O [planejamento de rede e o ajuste de desempenho para o Office 365](https://aka.ms/tune/) ajuda você a planejar suas necessidades de conectividade. Além disso, consulte [criar e modificar um circuito do ExpressRoute](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Quais serviços do Office 365 podem ser acessados através de uma conexão do ExpressRoute?

Consulte a página de [intervalos de endereços IP e URLs do Office 365](https://aka.ms/o365endpoints) para obter uma lista atualizada dos serviços com suporte no ExpressRoute.

### <a name="how-much-does-expressroute-for-office-365-services-cost"></a>Quanto custa o ExpressRoute para os serviços do Office 365?

Os serviços do Office 365 exigem o complemento Premium para serem habilitados. Consulte a [página de detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) para obter os custos.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Em quais regiões há suporte para o ExpressRoute para Office 365?

Consulte [parceiros e locais do ExpressRoute](expressroute-locations.md) para obter informações.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Posso acessar o Office 365 pela Internet, mesmo que o ExpressRoute tenha sido configurado para minha organização?

Sim. Os pontos de extremidade de serviço do Office 365 podem ser acessados pela Internet, embora o ExpressRoute tenha sido configurado para sua rede. Entre em contato com a equipe de rede da sua organização se a rede em seu local estiver configurada para se conectar aos serviços do Office 365 por meio do ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Como posso planejar a alta disponibilidade para o tráfego de rede do Office 365 no Azure ExpressRoute?
Consulte a recomendação para [alta disponibilidade e failover com o Azure ExpressRoute](https://aka.ms/erhighavailability)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Posso acessar os serviços da comunidade de governo dos EUA (GCC) do Office 365 em um circuito de ExpressRoute do governo dos EUA do Azure?

Sim. Os pontos de extremidade de serviço do Office 365 GCC podem ser acessados por meio do ExpressRoute do governo dos EUA do Azure. No entanto, primeiro você precisa abrir um tíquete de suporte no portal do Azure para fornecer os prefixos que pretende anunciar à Microsoft. Sua conectividade com os serviços do Office 365 GCC será estabelecida depois que o tíquete de suporte for resolvido. 

## <a name="route-filters-for-microsoft-peering"></a>Filtros de rota para o emparelhamento da Microsoft

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Estou ligando o emparelhamento da Microsoft pela primeira vez, quais rotas veremos?

Você não verá nenhuma rota. Você precisa anexar um filtro de rota ao seu circuito para iniciar os anúncios de prefixo. Para obter instruções, consulte [Configurar filtros de rota para o emparelhamento da Microsoft](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Ativei o emparelhamento da Microsoft e agora estou tentando selecionar o Exchange Online, mas ele está me dando um erro informando que não estou autorizado a fazê-lo.

Ao usar filtros de rota, qualquer cliente pode ativar o emparelhamento da Microsoft. No entanto, para consumir os serviços do Office 365, você ainda precisará ser autorizado pelo Office 365.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Habilitei o emparelhamento da Microsoft antes de 1º de agosto de 2017, como posso aproveitar os filtros de rota?

Seu circuito existente continuará anunciando os prefixos para o Office 365. Se desejar adicionar anúncios de prefixos públicos do Azure no mesmo emparelhamento da Microsoft, você poderá criar um filtro de rota, selecionar os serviços que precisa anunciar (incluindo os serviços do Office 365 necessários) e anexar o filtro ao emparelhamento da Microsoft. Para obter instruções, consulte [Configurar filtros de rota para o emparelhamento da Microsoft](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Tenho emparelhamento da Microsoft em um local, agora estou tentando habilitá-lo em outro local e não estou vendo nenhum prefixo.

* O emparelhamento da Microsoft de circuitos de ExpressRoute que foram configurados antes de 1º de agosto de 2017 terá todos os prefixos de serviço anunciados por meio do emparelhamento da Microsoft, mesmo que os filtros de rota não estejam definidos.

* O emparelhamento da Microsoft de circuitos do ExpressRoute que são configurados em ou após 1º de agosto de 2017 não terão prefixos anunciados até que um filtro de rota seja anexado ao circuito. Você não verá nenhum prefixo por padrão.

## <a name="expressRouteDirect"></a>ExpressRoute direto

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="globalreach"></a>Alcance Global

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
