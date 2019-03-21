---
title: FAQ - ExpressRoute do Azure | Documentos da Microsoft
description: FAQ do ExpressRoute contém informações sobre serviços suportados do Azure, custo, dados e ligações, SLA, fornecedores e localizações, largura de banda e detalhes técnicos adicionais.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: e078f838620f633743b27d26ba1218e753ff690e
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259827"
---
# <a name="expressroute-faq"></a>FAQ do ExpressRoute

## <a name="what-is-expressroute"></a>O que é o ExpressRoute?

O ExpressRoute é um serviço do Azure que lhe permite criar ligações privadas entre os datacenters da Microsoft e de infraestrutura no local ou numa instalação de colocalização. As ligações ExpressRoute não entram na Internet pública e oferecem maior segurança, fiabilidade e velocidade com latências mais baixas que as ligações normais pela Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Quais são as vantagens de utilizar o ExpressRoute e ligações de rede privada?

As ligações do ExpressRoute não passam para a Internet pública. Eles oferecem maior segurança, fiabilidade e velocidade, com latências inferiores e consistentes que as ligações normais pela Internet. Em alguns casos, a utilização de ligações ExpressRoute para transferir dados entre dispositivos no local e Azure pode oferecer em benefícios de custos significativos.

### <a name="where-is-the-service-available"></a>Onde o serviço está disponível?

Consulte esta página para a localização do serviço e disponibilidade: [ExpressRoute parceiros e localizações de](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Como posso utilizar o ExpressRoute para ligar à Microsoft, se eu não tenho parcerias com um dos parceiros a operadora do ExpressRoute?

Pode selecionar uma operadora regional e direcionado para ligações de Ethernet para um do exchange suportada localizações do fornecedor. Em seguida, pode configurar o peering com a Microsoft a localização do fornecedor. Verifique a última seção [ExpressRoute parceiros e localizações de](expressroute-locations.md) para ver se o seu fornecedor de serviços está presente em qualquer uma das localizações do exchange. Em seguida, é possível pedir um circuito do ExpressRoute por meio do Provedor de serviço para ligar ao Azure.

### <a name="how-much-does-expressroute-cost"></a>Qual é o custo do ExpressRoute?

Verifique [os detalhes dos preços](https://azure.microsoft.com/pricing/details/expressroute/) para informações sobre preços.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Se tenho de pagar um circuito do ExpressRoute de uma determinada largura de banda, a ligação de VPN que posso fazer compras no meu provedor de serviço de rede tem de ser a mesma velocidade?

Não. Pode comprar uma ligação de VPN de qualquer velocidade do seu fornecedor de serviços. No entanto, a ligação para o Azure está limitada a largura de banda do circuito ExpressRoute comprar.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Se tenho de pagar um circuito do ExpressRoute de uma determinada largura de banda, tenho a capacidade de expandir até velocidades mais elevadas, se necessário?

Sim. Circuitos do ExpressRoute são configurados para permitir que períodos de rajada de até duas vezes o largura de banda limite obtido sem custos adicionais. Contacte o seu fornecedor de serviços para ver se esta capacidade é suportada.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Posso utilizar a mesma conexão de rede privada com a rede virtual e outros serviços do Azure ao mesmo tempo?

Sim. Um circuito do ExpressRoute, uma vez configurado, permite-lhe aceder a serviços numa rede virtual e outros serviços do Azure em simultâneo. Ligar às redes virtuais ao longo do caminho de peering privado e a outros serviços ao longo do caminho de peering da Microsoft.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>O ExpressRoute oferece um contrato de nível de serviço (SLA)?

Para obter informações, consulte a [SLA do ExpressRoute](https://azure.microsoft.com/support/legal/sla/) página.

## <a name="supported-services"></a>Serviços suportados

O ExpressRoute suporta [três domínios de encaminhamento](expressroute-circuit-peerings.md) para vários tipos de serviços.

### <a name="private-peering"></a>Peering privado

* Redes virtuais, incluindo todas as máquinas virtuais e serviços cloud

### <a name="public-peering"></a>Peering público

>[!NOTE]
>Peering público foi desativado no novos circuitos do ExpressRoute. Serviços do Azure estão disponíveis no peering da Microsoft.
>

* Power BI
* Dynamics 365 para finanças e operações (anteriormente conhecidas como o Dynamics AX Online)
* A maioria dos serviços do Azure é suportada. Entre em contacto diretamente com o serviço que pretende utilizar para verificar o suporte.<br><br>
  **Os seguintes serviços não são suportados**:
    * CDN
    * Multi-Factor Authentication
    * Gestor de Tráfego

### <a name="microsoft-peering"></a>Peering da Microsoft

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Dynamics 365 
* Power BI
* Azure Active Directory
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (Comunidade de serviços globais do Azure)
* A maioria dos serviços do Azure é suportada. Entre em contacto diretamente com o serviço que pretende utilizar para verificar o suporte.<br><br>**Os seguintes serviços não são suportados**:
    * CDN
    * Multi-Factor Authentication
    * Gestor de Tráfego

## <a name="data-and-connections"></a>Ligações e dados

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Existem limites sobre a quantidade de dados que pode transferir a utilizar o ExpressRoute?

Não podemos definir um limite na quantidade de transferência de dados. Consulte a [os detalhes dos preços](https://azure.microsoft.com/pricing/details/expressroute/) para obter informações sobre as taxas de largura de banda.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>As velocidades de conexão são suportadas pelo ExpressRoute?

Suporte para as ofertas de largura de banda:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Os fornecedores de serviços estão disponíveis?

Ver [ExpressRoute parceiros e localizações de](expressroute-locations.md) para obter a lista de fornecedores de serviços e locais.

## <a name="technical-details"></a>Detalhes técnicos

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Quais são os requisitos técnicos para ligar a minha localização no local ao Azure?

Ver [página de pré-requisitos do ExpressRoute](expressroute-prerequisites.md) para requisitos.

### <a name="are-connections-to-expressroute-redundant"></a>São ligações redundantes ao ExpressRoute?

Sim. Cada circuito ExpressRoute tem um par redundante de cruzada ligações configuradas para proporcionar elevada disponibilidade.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Irei perder conectividade se não for uma das minhas ligações do ExpressRoute?

Não irá perder a conectividade se uma das ligações cruzadas falhar. Uma ligação redundante está disponível para suportar a carga da sua rede e proporcionar elevada disponibilidade do seu circuito do ExpressRoute. Além disso, pode criar um circuito numa localização diferente peering para obter resiliência ao nível do circuito.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Como garantir a elevada disponibilidade numa rede virtual ligada ao ExpressRoute?

Pode obter elevada disponibilidade ao ligar os circuitos do ExpressRoute em diferentes localizações de peering (por exemplo, Singapura, singapura2) à sua rede virtual. Se um circuito do ExpressRoute ficar inativo, conectividade irá efetuar a ativação pós-falha para outro circuito do ExpressRoute. Por predefinição, o tráfego que sai da rede virtual é encaminhado com base no igual custo multi caminho encaminhamento (ECMP). Pode usar o peso de ligação para dar preferência a um circuito para outro. Para obter mais informações, consulte [otimizar o encaminhamento do ExpressRoute](expressroute-optimize-routing.md).

### <a name="onep2plink"></a>Se não for localizado conjuntamente numa troca de cloud e meu provedor de serviço oferece a ligação ponto a ponto, é necessário fazer o pedido duas conexões físicos entre minha rede no local e a Microsoft?

Se o seu fornecedor de serviços pode estabelecer dois circuitos do Ethernet virtual através da ligação física, só precisa de uma ligação física. A ligação física (por exemplo, uma fibra ótica) é terminada numa camada 1 (L1) dispositivo (veja a imagem). Os dois circuitos de virtual de Ethernet são marcados com o ID de VLAN, um para o circuito primário e outro para o secundário. Essas IDs de VLAN estão a 802.1Q externa cabeçalho Ethernet. O 802.1Q interna cabeçalho Ethernet (não mostrado) é mapeado para um específico [domínio de encaminhamento do ExpressRoute](expressroute-circuit-peerings.md).

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Posso expandir um dos meus VLANs para o Azure através do ExpressRoute?

Não. Não suportamos a extensões em conectividade de 2 camadas no Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Pode ter mais do que um circuito do ExpressRoute na minha subscrição?

Sim. Pode ter mais do que um circuito do ExpressRoute na sua subscrição. O limite predefinido está definido para 10. Pode contactar Support da Microsoft para aumentar o limite, se necessário.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Posso ter circuitos do ExpressRoute de fornecedores de serviços diferentes?

Sim. Pode ter circuitos do ExpressRoute com muitos fornecedores de serviços. Cada circuito ExpressRoute está associado com o fornecedor de serviços de um só. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Vejo duas localizações de peering ExpressRoute no metro mesmo, por exemplo, Singapura e singapura2. A localização de peering devo escolher para criar meu circuito do ExpressRoute?
Se o seu fornecedor de serviços ExpressRoute em ambos os sites, pode trabalhar com o fornecedor e escolher qualquer um dos sites para configurar o ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Pode ter vários circuitos do ExpressRoute no metro mesmo? Posso ligá-los à mesma rede virtual?

Sim. Pode ter vários circuitos do ExpressRoute com os fornecedores de serviços idêntica ou diferente. Se o metro tem várias localizações de peering do ExpressRoute e os circuitos são criados em diferentes localizações de peering, pode ligá-las à mesma rede virtual. Se o circuitos é criado na mesma localização de peering, não pode associá-los à mesma rede virtual. Cada nome de localização no portal do Azure ou na API do PowerShell/CLI representa uma localização de peering. Por exemplo, pode selecionar as localizações de peering "Singapura" e "Singapura2" e ligar circuitos de cada à mesma rede virtual. 

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Como posso ligar minhas redes virtuais a um circuito do ExpressRoute

Os passos básicos são:

* Estabelecer um circuito do ExpressRoute e ter o fornecedor de serviços ativá-la.
* Ou o fornecedor, tem de configurar o BGP peering (s).
* Ligar a rede virtual para o circuito do ExpressRoute.

Para obter mais informações, consulte [fluxos de trabalho do ExpressRoute para o aprovisionamento e Estados dos circuitos](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Existem limites de conectividade de meu circuito do ExpressRoute?

Sim. O [ExpressRoute parceiros e localizações de](expressroute-locations.md) artigo fornece uma descrição geral dos limites de conectividade para um circuito do ExpressRoute. Conectividade para um circuito do ExpressRoute está limitada a uma única região geopolítica. Conectividade pode ser expandida para em várias regiões geopolíticas, permitindo que a funcionalidade premium do ExpressRoute.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Pode ligar a mais do que uma rede virtual para um circuito do ExpressRoute?

Sim. Pode ter até 10 ligações de redes virtuais num circuito ExpressRoute standard e até 100 [circuito do ExpressRoute premium](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Tenho várias subscrições do Azure que contêm as redes virtuais. Posso ligar redes virtuais que estão em subscrições separadas para um único circuito ExpressRoute?

Sim. Pode ligar até 10 redes virtuais na mesma subscrição que o circuito ou de diferentes subscrições com um único circuito ExpressRoute. Este limite pode se aumentada ao ativar a funcionalidade premium do ExpressRoute.

Para obter mais informações, consulte [partilha de um circuito do ExpressRoute entre várias subscrições](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Tenho várias subscrições do Azure associadas a diferentes inquilinos do Azure Active Directory ou inscrições de contrato Enterprise. Posso ligar redes virtuais em inquilinos separados e inscrições para um único circuito de ExpressRoute não no mesmo inquilino ou inscrição?

Sim. Autorizações do ExpressRoute podem abranger os limites de subscrição, inquilino e inscrição sem qualquer configuração adicional necessária. 

Para obter mais informações, consulte [partilha de um circuito do ExpressRoute entre várias subscrições](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Redes virtuais estão ligadas ao mesmo circuito isolado entre si?

Não. Da perspectiva do encaminhamento, todas as redes virtuais ligadas ao mesmo circuito de ExpressRoute fazem parte do mesmo domínio de encaminhamento e não estão isoladas umas das outras. Se precisar de isolamento de rota, terá de criar um circuito do ExpressRoute separado.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Pode ter uma rede virtual ligada a mais do que um circuito do ExpressRoute?

Sim. Pode ligar uma única rede virtual com até quatro circuitos do ExpressRoute. Tem de ser ordenadas por meio de quatro diferentes [localizações do ExpressRoute](expressroute-locations.md).

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Pode aceder à Internet de minhas redes virtuais ligadas a circuitos do ExpressRoute?

Sim. Se não tiver anunciado rotas predefinidas (0.0.0.0/0) ou prefixos de rota de Internet através da sessão BGP, pode ligar à Internet a partir de uma rede virtual ligada a um circuito do ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Posso bloquear a ligação à Internet para redes virtuais ligadas a circuitos do ExpressRoute?

Sim. Pode anunciar rotas predefinidas (0.0.0.0/0) para bloquear todas as conectividade à Internet para máquinas virtuais implementadas numa rede virtual e encaminhar todo o tráfego de saída através de circuito do ExpressRoute.

Se anunciar rotas predefinidas, podemos forçam o tráfego a serviços oferecidos através do peering (como o armazenamento do Azure e SQL DB) da Microsoft para o local. Terá de configurar seus roteadores para devolver o tráfego para o Azure através do caminho de peering da Microsoft ou através da Internet. Se ativou um ponto final de serviço para o serviço, o tráfego para o serviço não é forçado a local. O tráfego permanece na rede backbone do Azure. Para saber mais sobre pontos finais de serviço, consulte [pontos finais de serviço de rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Redes virtuais ligadas ao mesmo circuito de ExpressRoute podem conversar entre si?

Sim. Máquinas virtuais implementadas em redes virtuais ligadas ao mesmo circuito de ExpressRoute podem comunicar entre si.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Pode utilizar a conectividade de site a site para redes virtuais em conjunto com o ExpressRoute?

Sim. ExpressRoute pode coexistir com VPNs de site a site. Ver [configurar o ExpressRoute e ligações coexistentes de site a site](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Por que há um endereço IP público associado ao gateway ExpressRoute numa rede virtual?

O endereço IP público é utilizado para gestão de interno apenas e não constitui uma exposição de segurança da sua rede virtual.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Existem limites no número de rotas, pode anunciar?

Sim. Aceitamos até 4000 prefixos de rota para peering privado e 200 para peering da Microsoft. Isso pode aumentar para 10 000 rotas para peering privado, se habilitar o recurso de premium do ExpressRoute.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Existem restrições em intervalos IP, pode anunciar durante a sessão BGP?

Não aceitamos prefixos privados (RFC1918) para a sessão BGP peering do Microsoft.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>O que acontece se exceder o BGP limita?

Sessões de BGP serão ignorados. Serão repostas uma vez que a contagem de prefixo fica abaixo do limite.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>O que é o tempo de espera de BGP de ExpressRoute? Pode mesmo ser ajustado?

O tempo de espera é de 180. As mensagens keep-alive são enviadas a cada 60 segundos. Estes são fixos definições do lado da Microsoft que não pode ser alterada. É possível configurar diferentes temporizadores e os parâmetros de sessão BGP serão negociados em conformidade.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Pode alterar a largura de banda de um circuito do ExpressRoute?

Sim, pode tentar aumentar a largura de banda do seu circuito do ExpressRoute no portal do Azure ou através do PowerShell. Se existir capacidade disponível na porta física no qual o seu circuito foi criado, a alteração for concluída com êxito. 

Se sua alteração falha, significa que optar por não existir capacidade suficiente que se deixado na porta atual e terá de criar um novo circuito do ExpressRoute com a largura de banda superior, ou que não existe nenhuma capacidade adicional nessa localização, caso em que não será capaz de aumentar a largura de banda. 

Também terá com o fornecedor de conectividade para garantir que atualizam os limitadores dentro das suas redes para suportar o aumento da largura de banda. No entanto, não é possível, reduzir a largura de banda do seu circuito do ExpressRoute. Terá de criar um novo circuito do ExpressRoute com largura de banda inferior e eliminar o circuito antigo.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Como posso alterar a largura de banda de um circuito do ExpressRoute?

Pode atualizar a largura de banda do circuito ExpressRoute com a REST API ou o cmdlet do PowerShell.

## <a name="expressroute-premium"></a>ExpressRoute premium

### <a name="what-is-expressroute-premium"></a>O que é o ExpressRoute premium?

ExpressRoute premium é uma coleção das seguintes funcionalidades:

* Aumentou o encaminhamento de tabela de limite, de 4000 rotas a 10.000 rotas para peering privado.
* Aumento no número de ligações de VNets e alcance Global do ExpressRoute que pode ser ativada num circuito do ExpressRoute (a predefinição é 10). Para obter mais informações, consulte a [limites do ExpressRoute](#limits) tabela.
* Conectividade para o Office 365 e Dynamics 365.
* Conectividade global através da rede de principal da Microsoft. Agora pode ligar uma VNet na região geopolítica com um circuito do ExpressRoute noutra região.<br>
    **Exemplos:**

    *  Pode ligar uma VNet criada na Europa Ocidental para um circuito ExpressRoute criado no vale do silício. 
    *  No peering da Microsoft, os prefixos de outras regiões geopolíticas são anunciados, de modo a que se possa ligar, para, por exemplo, SQL Azure na Europa Ocidental, de um circuito no vale do silício.


### <a name="limits"></a>Quantas ligações de VNets e alcance Global do ExpressRoute posso habilitar num circuito de ExpressRoute se eu habilitei o ExpressRoute premium?

As tabelas seguintes mostram os limites do ExpressRoute e o número de ligações de VNets e alcance Global do ExpressRoute por circuito do ExpressRoute:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Como posso ativar o ExpressRoute premium?

Funcionalidades premium do ExpressRoute podem ser ativadas quando a funcionalidade está ativada e poderá ser desligadas por atualizar o estado do circuito. Pode ativar ExpressRoute premium no momento da criação de circuito, ou pode chamar a API de REST / cmdlet do PowerShell.

### <a name="how-do-i-disable-expressroute-premium"></a>Como posso desativar premium do ExpressRoute?

Pode desativar o ExpressRoute premium, chamando a REST API ou o cmdlet do PowerShell. Deve certificar-se de que tiver dimensionado suas necessidades de conectividade para satisfazer os limites predefinidos antes de desativar o ExpressRoute premium. Se a sua utilização dimensiona além dos limites padrão, o pedido para desativar o ExpressRoute premium falha.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Posso escolher os recursos que desejo do conjunto de funcionalidades premium?

Não. Não é possível escolher as funcionalidades. Podemos habilitar todos os recursos quando ativar o ExpressRoute premium.

### <a name="how-much-does-expressroute-premium-cost"></a>Qual é o custo do ExpressRoute premium?

Consulte a [os detalhes dos preços](https://azure.microsoft.com/pricing/details/expressroute/) custo.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Pago para o ExpressRoute premium para além dos custos do ExpressRoute standard?

Sim. Os custos do ExpressRoute premium aplicam-se sobre os custos de circuito do ExpressRoute e os custos envolvidos pelo fornecedor de conectividade.

## <a name="expressroute-for-office-365"></a>ExpressRoute para Office 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services"></a>Como posso criar um circuito do ExpressRoute para ligar a serviços do Office 365?

1. Reveja os [página de pré-requisitos do ExpressRoute](expressroute-prerequisites.md) para se certificar de que cumpre os requisitos.
2. Para garantir que suas necessidades de conectividade são cumpridas, reveja a lista de fornecedores de serviços e locais no [ExpressRoute parceiros e localizações de](expressroute-locations.md) artigo.
3. Planear os seus requisitos de capacidade, revendo [planeamento de rede e otimização de desempenho para o Office 365](https://aka.ms/tune/).
4. Siga os passos apresentados em fluxos de trabalho para configurar a conectividade [fluxos de trabalho do ExpressRoute para o aprovisionamento e Estados dos circuitos](expressroute-workflows.md).

> [!IMPORTANT]
> Certifique-se de que ativou o suplemento ExpressRoute premium ao configurar a conectividade para serviços do Office 365.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-dynamics-365"></a>Meu circuitos do ExpressRoute existentes podem suportar a conectividade com os serviços do Office 365 e Dynamics 365?

Sim. O circuito de ExpressRoute existente pode ser configurado para suportar a conectividade para serviços do Office 365. Certifique-se de que tem capacidade suficiente para ligar a serviços do Office 365 e que tiver ativado o suplemento premium. [Planeamento de rede e otimização de desempenho para o Office 365](https://aka.ms/tune/) tem do ajudará a planear a conectividade. Além disso, veja [criar e modificar um circuito do ExpressRoute](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Que serviços podem ser acedidos através de uma ligação de ExpressRoute do Office 365?

Consulte a [intervalos de endereços IP e URLs do Office 365](https://aka.ms/o365endpoints) página para obter uma lista atualizada dos serviços suportada através do ExpressRoute.

### <a name="how-much-does-expressroute-for-office-365-services-cost"></a>Qual é o ExpressRoute para o custo de serviços do Office 365?

Serviços do Office 365 requerem o suplemento premium para ser ativada. Consulte a [página de detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) para os custos.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Que regiões ExpressRoute para o Office 365 é suportada no?

Ver [ExpressRoute parceiros e localizações de](expressroute-locations.md) para obter informações.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Posso acessar o Office 365 através da Internet, mesmo que o ExpressRoute foi configurado para a minha organização?

Sim. Pontos finais de serviço do Office 365 estão acessíveis através da Internet, mesmo que o ExpressRoute foi configurado para a sua rede. Entre em contacto com a equipe de rede da sua organização se a rede na sua localização é configurada para ligar aos serviços do Office 365 através do ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Como pode posso planear a elevada disponibilidade para o tráfego de rede do Office 365 no ExpressRoute do Azure?
Consulte a recomendação para [elevada disponibilidade e ativação pós-falha com o Azure ExpressRoute](https://aka.ms/erhighavailability)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Posso aceder a serviços do Office 365 US Government Community (GCC) ao longo de um circuito do ExpressRoute de Governo do Azure-nos?

Sim. Pontos finais de serviço do Office 365 GCC se encontram acessíveis através do ExpressRoute do Azure-na administração pública. No entanto, tem primeiro de abrir um pedido de suporte no portal do Azure para fornecer os prefixos que planeia anunciar à Microsoft. Será possível estabelecer a conectividade a serviços do Office 365 GCC depois do pedido de suporte é resolvido. 

## <a name="route-filters-for-microsoft-peering"></a>Filtros de rota para peering da Microsoft

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Eu estou Ativando o peering da Microsoft pela primeira vez, quais rotas poderei ver?

Não verá quaisquer rotas. Terá de anexar um filtro de rota para o seu circuito para iniciar o prefixo de anúncios. Para obter instruções, consulte [configurar filtros de rota para peering da Microsoft](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Eu me virei no peering da Microsoft e agora estou tentando selecione Exchange Online, mas ele me fornece um erro que eu não estou tem autorização para fazê-lo.

Ao utilizar filtros de rota, todos os clientes podem ativar o peering da Microsoft. No entanto, para o consumo de serviços do Office 365, ainda precisa obter autorizado pelo Office 365.

### <a name="do-i-need-to-get-authorization-for-turning-on-dynamics-365-over-microsoft-peering"></a>É necessário obter autorização para ativar o Dynamics 365 através do peering da Microsoft?

Não, não terá autorização para o Dynamics 365. Pode criar uma regra e selecione a Comunidade do Dynamics 365 sem autorização.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Eu habilitei o peering antes de 1 de Agosto de 2017, como pode tirar partido de filtros de rota da Microsoft?

O circuito existente continuará a anunciar os prefixos do Office 365 e Dynamics 365. Se pretender adicionar os anúncios de prefixos públicos do Azure através do mesmo peering da Microsoft, pode criar um filtro de rota, selecione os serviços que precisa anunciados (incluindo os serviços do Office 365 que terá e do Dynamics 365) e anexar o filtro ao seu Microsoft peering. Para obter instruções, consulte [configurar filtros de rota para peering da Microsoft](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Eu tenho o peering num único local da Microsoft, agora estou tentando ativá-la em outro local e não estou a ver quaisquer prefixos.

* Peering da Microsoft dos circuitos do ExpressRoute que foram configurados antes de 1 de Agosto de 2017, terá todos os serviço prefixos anunciados através de peering, da Microsoft, mesmo se os filtros de rota não estão definidos.

* Peering da Microsoft dos circuitos do ExpressRoute que estão configurados em ou depois de 1 de Agosto de 2017 não terão qualquer prefixos anunciados até que um filtro de rota é anexado ao circuito. Verá que não existem prefixos por predefinição.

## <a name="expressRouteDirect"></a>Direct do ExpressRoute

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="globalreach"></a>Alcance global

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
