---
title: 'Azure ExpressRoute: Requisitos de encaminhamento'
description: Esta página apresenta os requisitos detalhados para configurar e gerir o encaminhamento para circuitos do ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: duau
ms.openlocfilehash: 0dc2b48d02eb8a69afc947891c263ef1510257a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721842"
---
# <a name="expressroute-routing-requirements"></a>Requisitos de encaminhamento do ExpressRoute
Para ligar aos serviços em nuvem da Microsoft com o ExpressRoute, terá de configurar e gerir o encaminhamento. Alguns fornecedores de conectividade oferecem a configuração e a gestão do encaminhamento como um serviço gerido. Contacte o seu fornecedor de conectividade para ver se oferece este serviço. Caso contrário, terá de cumprir os seguintes requisitos:

Veja o artigo [Circuits and routing domains (Circuitos e domínios de encaminhamento)](expressroute-circuit-peerings.md) para obter uma descrição das sessões de encaminhamento que têm de ser configuradas para facilitar a conectividade.

> [!NOTE]
> A Microsoft não suporta protocolos de redundância do router (por exemplo, HSRP, VRRP) para configurações de elevada disponibilidade. Precisamos de um par de sessões BGP redundante por peering para elevada disponibilidade.
> 
> 

## <a name="ip-addresses-used-for-peerings"></a>Endereços IP utilizados para peerings
Tem de reservar alguns blocos de endereços IP para configurar o encaminhamento entre a sua rede e os routers Enterprise Edge da Microsoft (MSEEs). Esta secção fornece uma lista de requisitos e descreve as regras relativas ao modo como estes endereços IP têm de ser obtidos e utilizados.

### <a name="ip-addresses-used-for-azure-private-peering"></a>Endereços IP utilizados para peering privado do Azure
Pode utilizar endereços IP privados ou endereços IP públicos para configurar os peerings. O intervalo de endereços utilizado para configurar as rotas não pode sobrepor-se aos intervalos de endereços utilizados para criar redes virtuais no Azure. 

* IPv4:
    * Tem de reservar uma sub-rede /29 ou duas sub-redes /30 para as interfaces de encaminhamento.
    * As sub-redes utilizadas para encaminhamento podem ser endereços IP privados ou endereços IP públicos.
    * As sub-redes não podem estar em conflito com o intervalo reservado pelo cliente para utilização no Microsoft Cloud.
    * Se for utilizada uma sub-rede /29, está dividida em duas sub-redes /30. 
      * A primeira sub-rede /30 é utilizada para a ligação primária e a segunda sub-rede /30 é utilizada para a ligação secundária.
      * Para cada uma das sub-redes /30, tem de utilizar o primeiro endereço IP da sub-rede /30 do router. A Microsoft utiliza o segundo endereço IP da sub-rede /30 para configurar uma sessão de BGP.
      * É necessário configurar as duas sessões de BGP para que o nosso [SLA de disponibilidade](https://azure.microsoft.com/support/legal/sla/) seja válido.
* IPv6:
    * Deve reservar uma sub-rede /125 ou duas sub-redes /126 para interfaces de encaminhamento.
    * As sub-redes utilizadas para encaminhamento podem ser endereços IP privados ou endereços IP públicos.
    * As sub-redes não podem estar em conflito com o intervalo reservado pelo cliente para utilização no Microsoft Cloud.
    * Se for utilizada uma sub-rede /125, está dividida em duas sub-redes /126. 
      * A primeira sub-rede /126 é utilizada para a ligação primária e a segunda sub-rede /30 é utilizada para a ligação secundária.
      * Para cada uma das sub-redes /126, tem de utilizar o primeiro endereço IP da sub-rede /126 do router. A Microsoft utiliza o segundo endereço IP da sub-rede /126 para configurar uma sessão de BGP.
      * É necessário configurar as duas sessões de BGP para que o nosso [SLA de disponibilidade](https://azure.microsoft.com/support/legal/sla/) seja válido.

#### <a name="example-for-private-peering"></a>Exemplo de peering privado
Se optar por utilizar a.b.c.d/29 para configurar o peering, este está dividido em duas sub-redes /30. No exemplo seguinte, note como é utilizada a sub-rede a.b.c.d/29:

* A a.b.c.d/29 está dividida em a.b.c.d/30 e a.b.c.d+4/30 e transmitida à Microsoft através de APIs de aprovisionamento.
  * Utilize a a.b.c.d+1 como IP VRF do PE primário e a Microsoft consumirá a a.b.c.d+2 como IP VRF do MSEE primário.
  * Utilize a a.b.c.d+5 como IP VRF do PE secundário e a Microsoft consumirá a a.b.c.d+6 como IP VRF do MSEE secundário.

Considere o caso em que selecionou 192.168.100.128/29 para configurar o peering privado. 192.168.100.128/29 inclui os endereços de 192.168.100.128 a 192.168.100.135, dos quais:

* o 192.168.100.128/30 será atribuído ao link1, com o fornecedor a utilizar o 192.168.100.129 e a Microsoft a utilizar o 192.168.100.130.
* o 192.168.100.132/30 será atribuído ao link2, com o fornecedor a utilizar o 192.168.100.133 e a Microsoft a utilizar o 192.168.100.134.

### <a name="ip-addresses-used-for-microsoft-peering"></a>Endereços IP utilizados para peering da Microsoft
Tem de utilizar endereços IP públicos da sua propriedade para configurar as sessões de BGP. A Microsoft tem de poder verificar a propriedade dos endereços IP através dos Registos de Internet de Encaminhamento e dos Registos de Encaminhamento de Internet.

* Os IPs listados no portal para Prefixos Públicos Anunciados para o Peering da Microsoft irá criar ACLs para os routers de núcleo da Microsoft para permitir tráfego de entrada a partir destes IPs. 
* Tem de utilizar uma sub-rede /29 (IPv4) ou /125 (IPv6) exclusiva ou duas sub-redes /30 (IPv4) ou /126 (IPv6) sub-redes para configurar o peering de BGP para cada peering por circuito ExpressRoute (se tiver mais do que um).
* Se for utilizada uma sub-rede /29, está dividida em duas sub-redes /30.
* A primeira sub-rede /30 é utilizada para a ligação primária e a segunda sub-rede /30 será utilizada para a ligação secundária.
* Para cada uma das sub-redes /30, tem de utilizar o primeiro endereço IP da sub-rede /30 do router. A Microsoft utiliza o segundo endereço IP da sub-rede /30 para configurar uma sessão de BGP.
* Se for utilizada uma sub-rede /125, está dividida em duas sub-redes /126.
* A primeira sub-rede /126 é utilizada para a ligação primária e a segunda sub-rede /126 será utilizada para a ligação secundária.
* Para cada uma das sub-redes /126, tem de utilizar o primeiro endereço IP da sub-rede /126 do router. A Microsoft utiliza o segundo endereço IP da sub-rede /126 para configurar uma sessão de BGP.
* É necessário configurar as duas sessões de BGP para que o nosso [SLA de disponibilidade](https://azure.microsoft.com/support/legal/sla/) seja válido.

### <a name="ip-addresses-used-for-azure-public-peering"></a>Endereços IP utilizados para peering público do Azure

> [!NOTE]
> O azure de espreitar o público não está disponível para novos circuitos.
> 

Tem de utilizar endereços IP públicos da sua propriedade para configurar as sessões de BGP. A Microsoft tem de poder verificar a propriedade dos endereços IP através dos Registos de Internet de Encaminhamento e dos Registos de Encaminhamento de Internet. 

* Tem de utilizar uma sub-rede /29 exclusiva ou duas sub-redes /30 para configurar o peering de BGP para cada peering por circuito do ExpressRoute (se tiver mais do que um). 
* Se for utilizada uma sub-rede /29, está dividida em duas sub-redes /30. 
  * A primeira sub-rede /30 é utilizada para a ligação primária e a segunda sub-rede /30 é utilizada para a ligação secundária.
  * Para cada uma das sub-redes /30, tem de utilizar o primeiro endereço IP da sub-rede /30 do router. A Microsoft utiliza o segundo endereço IP da sub-rede /30 para configurar uma sessão de BGP.
  * É necessário configurar as duas sessões de BGP para que o nosso [SLA de disponibilidade](https://azure.microsoft.com/support/legal/sla/) seja válido.

## <a name="public-ip-address-requirement"></a>Requisito de endereço IP público

### <a name="private-peering"></a>Peering privado
Pode optar por utilizar endereços IPv4 públicos ou privados para o peering privado. Oferecemos um isolamento do tráfego ponto a ponto, de modo a que a sobreposição de endereços com outros clientes não seja possível em caso de peering privado. Estes endereços não são anunciados na Internet. 

### <a name="microsoft-peering"></a>Peering da Microsoft
O caminho de observação da Microsoft permite-lhe ligar-se aos serviços de cloud da Microsoft. A lista de serviços inclui serviços Microsoft 365, tais como Exchange Online, SharePoint Online, Skype for Business e Microsoft Teams. A Microsoft suporta a conetividade bidirecional no peering da Microsoft. O tráfego destinado aos serviços em nuvem da Microsoft tem de utilizar endereços IPv4 públicos válidos antes de serem introduzidos na rede da Microsoft.

Certifique-se de que o endereço IP e o número AS estão registados em seu nome num dos registos seguintes:

* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](https://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](https://www.radb.net/)
* [ALTDB](https://altdb.net/)

Se os prefixos e o número SA não estiverem atribuídos a si nos registos anteriores, terá de abrir um pedido de suporte para a validação manual dos seus prefixos e ASN. O suporte requer documentação, como uma Carta de Autorização, que prova que está autorizado a utilizar os recursos.

É permitido um Número AS privado com Peering da Microsoft, mas também irá precisar de validação manual. Além disso, removemos os números privados COMO no COMO Caminho para os prefixos recebidos. Por consequência, não pode acrescentar números privados COMO no COMO Caminho de modo a [influenciar o encaminhamento para o Peering da Microsoft](expressroute-optimize-routing.md). 

> [!IMPORTANT]
> Não forneça os seus direitos de propriedade intelectual (IP) públicos e de forma pública. Para reduzir o risco de configuração incorreta que causa o encaminhamento assimétrico, recomendamos vivamente que os [endereços NAT IP anunciados](expressroute-nat.md) à Microsoft sobre o ExpressRoute sejam de uma gama que não é publicitada para a internet. Se isso não for possível, é essencial garantir que anuncia uma gama mais específica sobre o ExpressRoute do que a da ligação à Internet. Além da rota pública para o NAT, também pode anunciar através do ExpressRoute os endereços IP públicos utilizados pelos servidores da sua rede de instalações que comunicam com os pontos finais da Microsoft 365 dentro da Microsoft. 
> 
> 

### <a name="public-peering-deprecated---not-available-for-new-circuits"></a>Observação pública (depreciada - não disponível para novos circuitos)
O caminho de peering público Azure permite-lhe ligar a todos os serviços alojados no Azure ao longo dos respetivos endereços IP públicos. Estes incluem os serviços listados nas [FAQ do ExpressRoute](expressroute-faqs.md) e quaisquer serviços alojados pelos ISVs no Microsoft Azure. A conectividade aos serviços do Microsoft Azure em peering público é sempre iniciada a partir da sua rede para a rede da Microsoft. Tem de utilizar endereços IP Públicos para o tráfego destinado à rede da Microsoft.

> [!IMPORTANT]
> Todos os serviços Azure PaaS estão acessíveis através do persto da Microsoft.
>   

Um número AS privado é permitido com olhando público.

## <a name="dynamic-route-exchange"></a>Troca de rotas dinâmicas
A troca do encaminhamento será feita através do protocolo eBGP. São estabelecidas sessões de EBGP entre os MSEEs e os seus routers. A autenticação das sessões de BGP não é um requisito. Se necessário, pode ser configurado um hash MD5. Veja [Configurar encaminhamento](how-to-routefilter-portal.md) e [Fluxos de trabalho de aprovisionamento dos circuitos e estados dos circuitos](expressroute-workflows.md) para obter informações sobre como configurar sessões de BGP.

## <a name="autonomous-system-numbers"></a>Números de Sistema Autónomos
A Microsoft utiliza AS 12076 para o peering público do Azure, o peering privado do Azure e o peering da Microsoft. Reservamos os ASNs do 65515 ao 65520 para utilização interna. São suportados números AS de 16 e de 32 bits.

Não há requisitos quanto à simetria da transferência de dados. Os caminhos de reencaminhamento e do remetente podem atravessar pares de routers diferentes. Rotas idênticas devem ser publicitadas de ambos os lados através de vários pares de circuitos que lhe pertencem. Não é necessário que a métrica das rotas seja idêntica.

## <a name="route-aggregation-and-prefix-limits"></a>Agregação de rotas e limites de prefixo
Apoiamos até 4000 prefixos IPv4 e 100 prefixos IPv6 anunciados através do espremedura privada Azure. Isto pode ser aumentado até 10.000 prefixos IPv4 se o addon premium ExpressRoute estiver ativado. Aceitamos até 200 prefixos por sessão de BGP para o peering público do Azure e o peering da Microsoft. 

A sessão de BGP é ignorada se o número de prefixos exceder o limite. Só aceitamos rotas predefinidas na ligação do peering privado. O fornecedor tem de filtrar a rota predefinida e os endereços IP privados (RFC 1918) nos caminhos do peering público do Azure e do peering da Microsoft. 

## <a name="transit-routing-and-cross-region-routing"></a>Encaminhamento de trânsito e encaminhamento por várias regiões
Não é possível encaminhar o ExpressRoute como router de trânsito. Precisará do seu fornecedor de conectividade para os serviços de encaminhamento de trânsito.

## <a name="advertising-default-routes"></a>Anunciar rotas predefinidas
As rotas predefinidas só são permitidas em sessões do peering privado do Azure. Numa situação dessas, encaminharemos para a sua rede todo o tráfego das redes virtuais associadas. O anúncio de rotas predefinidas no peering privado resultará no bloqueio do caminho da Internet a partir do Azure. Precisará da sua periferia empresarial para encaminhar o tráfego de e para a Internet para serviços alojados no Azure. 

 Para ativar a conectividade para outros serviços do Azure e outros serviços de infraestrutura, tem de se certificar de que implementou um dos seguintes itens:

* O azure de espreitar o público está habilitado a encaminhar o tráfego para os pontos finais públicos.
* Utiliza o encaminhamento definido pelo utilizador para permitir a ligação à Internet para cada sub-rede que necessite de ligação à Internet.

> [!NOTE]
> O anúncio de rotas predefinidas interrompe a ativação de licenças do Windows e de outras licenças de VMs. Siga as instruções [aqui](/archive/blogs/mast/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling) para contornar este problema.
> 
> 

## <a name="support-for-bgp-communities"></a><a name="bgp"></a>Suporte para comunidades de BGP
Esta secção apresenta uma descrição geral de como as comunidades de BGP serão utilizadas com o ExpressRoute. A Microsoft anuncia as rotas nos caminhos do peering público e do peering da Microsoft com rotas etiquetadas com valores de comunidade apropriados. A lógica por detrás disto e os detalhes sobre os valores das comunidades são descritos abaixo. A Microsoft não irá, no entanto, honrar os valores das comunidades marcadas em rotas anunciadas para a Microsoft.

Se estiver a ligar à Microsoft através do ExpressRoute numa localização de peering dentro de uma região geopolítica, terá acesso a todos os serviços em nuvem da Microsoft em todas as regiões dentro dos limites geopolíticos. 

Por exemplo, se ligar à Microsoft em Amesterdão através do ExpressRoute, terá acesso a todos os serviços em nuvem da Microsoft alojados na Europa do Norte e na Europa Ocidental. 

Veja a página [Parceiros e localizações de peering do ExpressRoute ](expressroute-locations.md) para obter uma lista detalhada das regiões geopolíticas, das regiões do Azure associadas e das localizações de peering do ExpressRoute correspondentes.

Pode comprar mais do que um circuito do ExpressRoute por região geopolítica. Ter várias ligações oferece vantagens significativas de elevada disponibilidade, devido à georredundância. Nos casos em que tenha vários circuitos ExpressRoute, receberá o mesmo conjunto de prefixos anunciados pela Microsoft nos percursos de observação e de observação pública da Microsoft. o que significa que terá vários caminhos da sua rede para a Microsoft. Esta situação pode, potencialmente, levar a decisões de encaminhamento inferiores às ideais na sua rede. Sendo assim, poderá ter experiências de conectividade aos diferentes serviços inferiores às ideais. Também pode utilizar os valores das comunidades para tomar decisões de encaminhamento adequadas para oferecer [encaminhamento ideal aos utilizadores](expressroute-optimize-routing.md).

| **Região do Microsoft Azure** | **Comunidade Regional de BGP** | **Comunidade BGP de armazenamento** | **Comunidade SQL BGP** | **Cosmos DB BGP comunidade** | **Comunidade BGP de backup** |
| --- | --- | --- | --- | --- | --- |
| **América do Norte** | |
| E.U.A. Leste | 12076:51004 | 12076:52004 | 12076:53004 | 12076:54004 | 12076:55004 |
| E.U.A. Leste 2 | 12076:51005 | 12076:52005 | 12076:53005 | 12076:54005 | 12076:55005 |
| E.U.A. Oeste | 12076:51006 | 12076:52006 | 12076:53006 | 12076:54006 | 12076:55006 |
| E.U.A. Oeste 2 | 12076:51026 | 12076:52026 | 12076:53026 | 12076:54026 | 12076:55026 |
| E.U.A. Centro-Oeste | 12076:51027 | 12076:52027 | 12076:53027 | 12076:54027 | 12076:55027 |
| E.U.A. Centro-Norte | 12076:51007 | 12076:52007 | 12076:53007 | 12076:54007 | 12076:55007 |
| E.U.A. Centro-Sul | 12076:51008 | 12076:52008 | 12076:53008 | 12076:54008 | 12076:55008 |
| E.U.A. Central | 12076:51009 | 12076:52009 | 12076:53009 | 12076:54009 | 12076:55009 |
| Canadá Central | 12076:51020 | 12076:52020 | 12076:53020 | 12076:54020 | 12076:55020 |
| Leste do Canadá | 12076:51021 | 12076:52021 | 12076:53021 | 12076:54021 | 12076:55021 |
| **América do Sul** | |
| Sul do Brasil | 12076:51014 | 12076:52014 | 12076:53014 | 12076:54014 | 12076:55014 |
| **Europa** | |
| Europa do Norte | 12076:51003 | 12076:52003 | 12076:53003 | 12076:54003 | 12076:55003 |
| Europa Ocidental | 12076:51002 | 12076:52002 | 12076:53002 | 12076:54002 | 12076:55002 |
| Sul do Reino Unido | 12076:51024 | 12076:52024 | 12076:53024 | 12076:54024 | 12076:55024 |
| Oeste do Reino Unido | 12076:51025 | 12076:52025 | 12076:53025 | 12076:54025 | 12076:55025 |
| França Central | 12076:51030 | 12076:52030 | 12076:53030 | 12076:54030 | 12076:55030 |
| Sul de França | 12076:51031 | 12076:52031 | 12076:53031 | 12076:54031 | 12076:55031 |
| Suíça Norte | 12076:51038 | 12076:52038 | 12076:53038 | 12076:54038 | 12076:55038 |
| Suíça Oeste | 12076:51039 | 12076:52039 | 12076:53039 | 12076:54039 | 12076:55039 | 
| Alemanha Norte | 12076:51040 | 12076:52040 | 12076:53040 | 12076:54040 | 12076:55040 | 
| Alemanha Centro-Oeste | 12076:51041 | 12076:52041 | 12076:53041 | 12076:54041 | 12076:55041 | 
| Leste da Noruega | 12076:51042 | 12076:52042 | 12076:53042 | 12076:54042 | 12076:55042 | 
| Oeste da Noruega | 12076:51043 | 12076:52043 | 12076:53043 | 12076:54043 | 12076:55043 | 
| **Ásia-Pacífico** | |
| Ásia Leste | 12076:51010 | 12076:52010 | 12076:53010 | 12076:54010 | 12076:55010 |
| Sudeste Asiático | 12076:51011 | 12076:52011 | 12076:53011 | 12076:54011 | 12076:55011 |
| **Japão** | |
| Leste do Japão | 12076:51012 | 12076:52012 | 12076:53012 | 12076:54012 | 12076:55012 |
| Oeste do Japão | 12076:51013 | 12076:52013 | 12076:53013 | 12076:54013 | 12076:55013 |
| **Austrália** | |
| Leste da Austrália | 12076:51015 | 12076:52015 | 12076:53015 | 12076:54015 | 12076:55015 |
| Austrália Sudeste | 12076:51016 | 12076:52016 | 12076:53016 | 12076:54016 | 12076:55016 |
| **Governo da Austrália** | |
| Austrália Central | 12076:51032 | 12076:52032 | 12076:53032 | 12076:54032 | 12076:55032 |
| Austrália Central 2 | 12076:51033 | 12076:52033 | 12076:53033 | 12076:54033 | 12076:55033 |
| **Índia** | |
| Índia do Sul | 12076:51019 | 12076:52019 | 12076:53019 | 12076:54019 | 12076:55019 |
| Oeste da Índia | 12076:51018 | 12076:52018 | 12076:53018 | 12076:54018 | 12076:55018 |
| Índia Central | 12076:51017 | 12076:52017 | 12076:53017 | 12076:54017 | 12076:55017 |
| **Coreia** | |
| Sul da Coreia do Sul | 12076:51028 | 12076:52028 | 12076:53028 | 12076:54028 | 12076:55028 |
| Coreia do Sul Central | 12076:51029 | 12076:52029 | 12076:53029 | 12076:54029 | 12076:55029 |
| **África do Sul**| |
| Norte da África do Sul | 12076:51034 | 12076:52034 | 12076:53034 | 12076:54034 | 12076:55034 |
| Oeste da África do Sul | 12076:51035 | 12076:52035 | 12076:53035 | 12076:54035 | 12076:55035 |
| **E.A.U.**| |
| Uae Norte | 12076:51036 | 12076:52036 | 12076:53036 | 12076:54036 | 12076:55036 |
| Centro dos Emirados Árabes Unidos | 12076:51037 | 12076:52037 | 12076:53037 | 12076:54037 | 12076:55037 |


Todas as rotas anunciadas a partir da Microsoft serão etiquetadas com o valor da comunidade adequado. 

> [!IMPORTANT]
> Os prefixos globais são marcados com um valor de comunidade adequado.
> 
> 

### <a name="service-to-bgp-community-value"></a>Serviço ao valor comunitário BGP
Para além do que foi dito acima, a Microsoft também marcará prefixos baseados no serviço a que pertencem. Este caso aplica-se apenas ao peering da Microsoft. A tabela abaixo fornece um mapeamento de serviço para o valor das comunidades de BGP. Pode executar o cmdlet 'Get-AzBgpServiceCommunity' para obter uma lista completa dos valores mais recentes.

| **Serviço** | **Valor das comunidades de BGP** |
| --- | --- |
| Troca Online\*\* | 12076:5010 |
| SharePoint Online\*\* | 12076:5020 |
| Skype para negócios online\*\*/\*\*\* | 12076:5030 |
| CRM Online\*\*\*\* |12076:5040 |
| Serviços Globais Azure\* | 12076:5050 |
| Azure Active Directory |12076:5060 |
| Azure Resource Manager |12076:5070 |
| Outros serviços do Office 365 Online** | 12076:5100 |

\* A Azure Global Services inclui apenas Azure DevOps neste momento.

\*\* Autorização exigida pela Microsoft, consulte os filtros de [rota Configure para o Microsoft Peering](how-to-routefilter-portal.md)

\*\*\* Esta comunidade também publica as rotas necessárias para os serviços da Microsoft Teams.

\*\*\*\* CRM Online suporta Dinâmica v8.2 e abaixo. Para versões mais altas, selecione a comunidade regional para as suas implementações Dynamics.

> [!NOTE]
> A Microsoft não honra valores das comunidades de BGP definidos por si nas rotas anunciadas para a Microsoft.
> 
> 

### <a name="bgp-community-support-in-national-clouds"></a>Suporte da Comunidade BGP nas Clouds National

| **Região do Azure de Clouds Nacionais**| **Valor das comunidades de BGP** |
| --- | --- |
| **Governo dos EUA** |  |
| US Gov - Arizona | 12076:51106 |
| US Gov - Iowa | 12076:51109 |
| US Gov - Virginia | 12076:51105 |
| US Gov - Texas | 12076:51108 |
| US DoD Centro | 12076:51209 |
| US DoD - Leste | 12076:51205 |


| **Serviço nas Clouds Nacionais** | **Valor das comunidades de BGP** |
| --- | --- |
| **Governo dos EUA** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Skype para Empresas Online |12076:5130 |
| Azure Active Directory |12076:5160 |
| Outros serviços do Office 365 Online |12076:5200 |

## <a name="next-steps"></a>Passos seguintes
* Configurar a ligação do ExpressRoute.
  
  * [Criar e modificar um circuito](expressroute-howto-circuit-arm.md)
  * [Criar e modificar a configuração de peering](expressroute-howto-routing-arm.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
