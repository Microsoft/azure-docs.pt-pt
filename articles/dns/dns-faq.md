---
title: FAQ - Azure DNS
description: Neste artigo, saiba sobre perguntas frequentes sobre O DNS azure
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 6/15/2019
ms.author: rohink
ms.openlocfilehash: 76b19cfb3c00a26d81eab81f67d8e156a520f377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77121717"
---
# <a name="azure-dns-faq"></a>Azure DNS FAQ

## <a name="about-azure-dns"></a>Sobre o Azure DNS

### <a name="what-is-azure-dns"></a>O que é o DNS do Azure?

O Sistema de Nome de Domínio (DNS) traduz, ou resolve, um website ou nome de serviço para o seu endereço IP. O DNS do Azure é um serviço de alojamento para domínios DNS. Fornece resolução de nome utilizando a infraestrutura do Microsoft Azure. Ao alojar os seus domínios no Azure, pode gerir os recursos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos seus outros serviços do Azure.

Os domínios DNS em DNS Azure estão hospedados na rede global Azure de servidores de nome DNS. Este sistema utiliza a rede Anycast para que cada consulta de DNS seja respondida pelo servidor DNS mais próximo disponível. O Azure DNS proporciona um desempenho rápido e uma elevada disponibilidade para o seu domínio.

O Azure DNS baseia-se no Azure Resource Manager. O Azure DNS beneficia de funcionalidades do Gestor de Recursos, tais como controlo de acesso baseado em papéis, registos de auditoria e bloqueio de recursos. Pode gerir domínios e registos através do portal Azure, dos cmdlets Azure PowerShell e do Azure CLI de plataforma cruzada. As aplicações que requerem gestão automática de DNS podem integrar-se com o serviço através dos REST API e SDKs.

### <a name="how-much-does-azure-dns-cost"></a>Quanto custa o Azure DNS?

O modelo de faturação Azure DNS baseia-se no número de zonas DNS alojadas em DNS Azure. É também baseado no número de consultas de DNS que recebem. Os descontos são fornecidos com base na utilização.

Para mais informações, consulte a página de [preços do DNS Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>O que é o SLA do DNS do Azure?

O Azure garante que os pedidos dNS válidos recebem uma resposta de pelo menos um servidor de nome DNS Azure a 100% das vezes.

Para mais informações, consulte a [página Azure DNS SLA](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>O que é uma zona DNS? É o mesmo que um domínio DNS? 

Um domínio é um nome único no sistema de nome de domínio. Um exemplo é contoso.com.

Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Por exemplo, o domínio contoso.com pode conter vários registos DNS. Os registos podem incluir mail.contoso.com\.para um servidor de correio e www contoso.com para um website. Estes registos estão alojados na zona dNS contoso.com.

Um nome de domínio é *apenas um nome.* Uma zona DNS é um recurso de dados que contém os registos DNS para um nome de domínio. Pode utilizar o DNS do Azure para alojar uma zona DNS e gerir os registos DNS para um domínio no Azure. Também fornece servidores de nome DNS para responder a consultas de DNS da Internet.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Preciso comprar um nome de domínio DNS para usar O DNS Azure? 

Não necessariamente.

Não é preciso comprar um domínio para acolher uma zona DNS em Azure DNS. Pode criar uma zona DNS a qualquer momento sem possuir o nome de domínio. As consultas de DNS para esta zona só resolvem se forem direcionadas para os servidores de nome DNS Azure atribuídos à zona.

Para ligar a sua zona DNS à hierarquia global do DNS, tem de comprar o nome de domínio. Em seguida, as consultas dNS de qualquer parte do mundo encontram a sua zona DNS e respondem com os seus registos dNS.

## <a name="azure-dns-features"></a>Características do DNS azure

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Existem restrições ao utilizar registos de pseudónimos para um ápice de nome de domínio com o Traffic Manager?

Sim. Você deve usar endereços IP públicos estáticos com o Gestor de Tráfego Azure. Configure o alvo do **ponto final externo** utilizando um endereço IP estático. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>O Azure DNS suporta o encaminhamento de tráfego baseado em DNS ou falha no ponto final?

O encaminhamento de tráfego baseado no DNS e a falha no ponto final são fornecidos pelo Gestor de Tráfego. Traffic Manager é um serviço Azure separado que pode ser usado com DNS Azure. Para mais informações, consulte a [visão geral do Gestor de Tráfego.](../traffic-manager/traffic-manager-overview.md)

O Azure DNS apenas suporta a hospedagem de domínios DNS estáticos, onde cada consulta de DNS para um determinado disco DNS recebe sempre a mesma resposta DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>O Azure DNS suporta o registo de nomede domínio?

Não. O Azure DNS não suporta atualmente a opção de comprar nomes de domínio. Para comprar domínios, deve utilizar um registo de nome de domínio de terceiros. O registrador normalmente cobra uma pequena taxa anual. Os domínios podem então ser hospedados em DNS Azure para gestão de registos DNS. Para mais informações, consulte [Delegar um domínio para o DNS Azure](dns-domain-delegation.md).

A funcionalidade para comprar nomes de domínio está rastreada no atraso do Azure. Utilize o site de feedback para [registar o seu suporte para esta funcionalidade](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>O Azure DNS suporta o DNSSEC?

Não. O Azure DNS não suporta atualmente as extensões de segurança do sistema de nome de domínio (DNSSEC).

A funcionalidade DNSSEC é rastreada no atraso do DNS Azure. Utilize o site de feedback para [registar o seu suporte para esta funcionalidade](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>O Azure DNS suporta transferências de zonas (AXFR/IXFR)?

Não. O Azure DNS não suporta atualmente transferências de zonas. As zonas DNS podem ser [importadas para O DNS Azure utilizando o Azure CLI](dns-import-export.md). Os registos DNS são geridos através do portal de [gestão Azure DNS,](dns-operations-recordsets-portal.md) [REST API,](https://docs.microsoft.com/powershell/module/az.dns) [SDK,](dns-sdk.md) [PowerShell cmdlets,](dns-operations-recordsets.md)ou da [ferramenta CLI](dns-operations-recordsets-cli.md).

A funcionalidade de transferência de zona é rastreada no atraso do DNS Azure. Utilize o site de feedback para [registar o seu suporte para esta funcionalidade](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>O Azure DNS suporta redirecionamentos de URL?

Não. Os serviços de redirecionamento de URL não são um serviço DNS. Trabalham ao nível http e não ao nível dNS. Alguns fornecedores de DNS agregam um serviço de redirecionamento de URL como parte da sua oferta global. Este serviço não é atualmente suportado pelo Azure DNS.

A função de redirecionamento de URL é rastreada no atraso do DNS Azure. Utilize o site de feedback para [registar o seu suporte para esta funcionalidade](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>O Azure DNS suporta o conjunto alargado de codificação ASCII (8 bits) para os conjuntos de discos TXT?

Sim. O Azure DNS suporta o conjunto alargado de codificação ASCII para séries de discos TXT. Mas tem de utilizar a versão mais recente das APIs, SDKs, PowerShell e CLI. Versões mais antigas do que 1 de outubro de 2017 ou SDK 2.1 não suportam o conjunto alargado DeSciI. 

Por exemplo, pode fornecer uma corda como o valor para um disco TXT que tem o caráter ASCII estendido \128. Um exemplo é "abcd\128efgh". O Azure DNS usa o valor byte deste personagem, que é 128, em representação interna. No momento da resolução do DNS, este valor byte é devolvido na resposta. Note também que "abc" e "\097\098\099" são permutáveis no que diz respeito à resolução. 

Seguimos as regras de fuga de formato de [ficheiros rFC 1035](https://www.ietf.org/rfc/rfc1035.txt) para registos TXT. Por exemplo, `\` agora escapa a tudo por RFC. Se especificar `A\B` como o valor recorde do TXT, está `AB`representado e resolvido como apenas . Se realmente quer que o registo `A\B` TXT tenha em `\` resolução, tem de escapar novamente. Como exemplo, `A\\B`especifique .

Este suporte atualmente não está disponível para registos TXT criados a partir do portal Azure.

## <a name="alias-records"></a>Registos de alias

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Quais são alguns cenários em que os registos de pseudónimos são úteis?

Consulte a secção de cenários no conjunto de registos de [pseudónimos do DNS Azure](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Que tipos de discos são suportados para conjuntos de recordes de pseudónimos?

Os conjuntos de recordes de alias são suportados para os seguintes tipos de discos numa zona DeNs Azure:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Que recursos são apoiados como alvos para conjuntos de recordes de pseudónimos?

- **Aponte para um recurso IP público de um conjunto de recordes DNS A/AAAA.** Pode criar um conjunto de registos A/AAAA e torná-lo um recorde de pseudónimo sacana para apontar para um recurso IP público.
- **Aponte para um perfil de Gestor de Tráfego de um conjunto de registos DNS A/AAAA/CNAME.** Pode apontar para o CNAME de um perfil do Gestor de Tráfego a partir de um conjunto de registos DNS CNAME. Um exemplo é contoso.trafficmanager.net. Agora, você também pode apontar para um perfil de Gestor de Tráfego que tem pontos finais externos de um recorde A ou AAAA estabelecido na sua zona DNS.
- Apontar para um ponto final da Rede de Entrega de **Conteúdos Azure (CDN).** Isto é útil quando se criam sites estáticos utilizando o armazenamento Azure e o Azure CDN.
- **Aponte para outro recorde de DNS estabelecido na mesma zona.** Os registos de alias podem referir-se a outros conjuntos de registos do mesmo tipo. Por exemplo, pode ter um conjunto de registos CNAME do DNS que funciona como alias de outro conjunto de registos CNAME do mesmo tipo. Este acordo é útil se quiser que alguns conjuntos de recordes sejam pseudónimos e alguns não-pseudónimos.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Posso criar e atualizar registos de pseudónimos do portal Azure?

Sim. Pode criar ou gerir registos de pseudónimos no portal Azure juntamente com as APIs, PowerShell, CLI e SDKs.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Os registos de pseudónimos ajudarão a certificar-se de que o meu conjunto de registos DNS é eliminado quando o IP público subjacente for eliminado?

Sim. Esta funcionalidade é uma das principais capacidades dos registos de pseudónimos. Ajuda-o a evitar possíveis interrupções para os utilizadores da sua aplicação.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Os registos do pseudónimo ajudarão a certificar-se de que o meu conjunto de registos DNS é atualizado para o endereço IP correto quando o endereço IP público subjacente mudar?

Sim. Esta funcionalidade é uma das principais capacidades dos registos de pseudónimos. Ajuda-o a evitar possíveis interrupções ou riscos de segurança para a sua aplicação.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Existem restrições ao utilizar conjuntos de recordes de pseudónimos para registos A ou AAAA para apontar para o Gestor de Tráfego?

Sim. Para apontar para um perfil do Gestor de Tráfego como um pseudónimo de um conjunto de registos A ou AAAA, o perfil do Gestor de Tráfego deve utilizar apenas pontos finais externos. Quando criar os pontos finais externos no Traffic Manager, forneça os endereços IP reais dos pontos finais.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Há alguma taxa adicional para usar registos de pseudónimos?

Os registos do Alias são uma qualificação num recorde de DNS válido. Não há faturação adicional para registos de pseudónimos.

## <a name="use-azure-dns"></a>Utilizar DNS Azure

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Posso co-acolher um domínio usando o Azure DNS e outro fornecedor de DNS?

Sim. O Azure DNS suporta domínios de co-hospedagem com outros serviços DNS.

Para configurar o co-hosting, modifique os registos de NS para o domínio apontar para os servidores de nome de ambos os fornecedores. O servidor de nomes (NS) regista o controlo de que os fornecedores recebem consultas de DNS para o domínio. Pode modificar estes registos de NS no Azure DNS, no outro fornecedor, e na zona-mãe. A zona dos pais é tipicamente configurada através do registo de nome de domínio. Para obter mais informações sobre a delegação do DNS, consulte a delegação de [domínio dNS](dns-domain-delegation.md).

Além disso, certifique-se de que os registos dNS para o domínio estão sincronizados entre ambos os fornecedores de DNS. O Azure DNS não suporta atualmente transferências de zona DNS. Os registos DNS devem ser sincronizados utilizando o portal de [gestão Azure DNS,](dns-operations-recordsets-portal.md) [REST API,](https://docs.microsoft.com/rest/api/dns/) [SDK,](dns-sdk.md) [PowerShell cmdlets,](dns-operations-recordsets.md)ou a [ferramenta CLI](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Tenho de delegar o meu domínio nos quatro servidores de nome SN Do Azure?

Sim. O Azure DNS atribui quatro servidores de nome seleções a cada zona DNS. Este acordo é para o isolamento de falhas e maior resiliência. Para se qualificar para o Azure DNS SLA, delege o seu domínio para os quatro servidores de nome.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Quais são os limites de utilização para o Azure DNS?

Aplicam-se os seguintes limites de predefinição quando utiliza O DNS Azure.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Posso mover uma zona DeNs Azure entre grupos de recursos ou entre subscrições?

Sim. As zonas de DNS podem ser movidas entre grupos de recursos ou entre subscrições.

Não há nenhum efeito nas consultas de DNS quando se move uma zona de DNS. Os servidores de nome atribuídos à zona permanecem os mesmos. As consultas de DNS são processadas normalmente em toda a parte.

Para obter mais informações e instruções sobre como mover as zonas de DNS, consulte [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Quanto tempo demora as alterações do DNS a produzir em vigor?

Novas zonas DNS e registos DNS normalmente aparecem nos servidores de nome SNS Azure rapidamente. O momento é de alguns segundos.

As alterações aos registos DNS existentes podem demorar um pouco mais. Normalmente aparecem nos servidores de nome SN Do Azure dentro de 60 segundos. O caching dNS por clientes DNS e dNS resolvers recursivos fora do DNS Azure também pode afetar o tempo. Para controlar esta duração da cache, utilize a propriedade Time-To-Live (TTL) de cada conjunto de registos.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Como posso proteger as minhas zonas de DNS contra a eliminação acidental?

O Azure DNS é gerido utilizando o Azure Resource Manager. O Azure DNS beneficia das funcionalidades de controlo de acesso que o Azure Resource Manager fornece. Controlos de controlo de acesso baseados em funções que os utilizadores leram ou escrevem acesso a zonas DeNs e conjuntos de registos. Os bloqueios de recursos impedem a modificação acidental ou a eliminação de zonas DNS e conjuntos de registos.

Para mais informações, consulte [Proteger as zonas e registos do DNS](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Como é que eu estarei com registos spf no Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Os servidores de nome SN do Azure resolvem-se sobre o IPv6? 

Sim. Os servidores de nome SN azure são de dupla pilha. Stack dupla significa que eles têm endereços IPv4 e IPv6. Para encontrar o endereço IPv6 para os servidores de nome DNS Azure atribuídos à sua zona DNS, utilize uma ferramenta como nslookup. Um exemplo é `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Como é que eu instalo um IDN no Azure DNS?

Os nomes de domínio internacionalizados (IDNs) codificam cada nome DNS utilizando [o punycode](https://en.wikipedia.org/wiki/Punycode). As consultas dNS são feitas usando estes nomes codificados por punycode.

Para configurar idns em DNS Azure, converta o nome da zona ou o nome do conjunto de registos em punycode. O Azure DNS não suporta atualmente a conversão incorporada de ou para o punycode.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre o Azure DNS.](dns-overview.md)

- [Saiba mais sobre como usar o DNS Azure para domínios privados.](private-dns-overview.md)

- [Saiba mais sobre zonas e registos DNS.](dns-zones-records.md)

- [Começar com o Azure DNS.](dns-getstarted-portal.md)
