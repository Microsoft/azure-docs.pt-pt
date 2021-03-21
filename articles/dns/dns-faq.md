---
title: FAQ - Azure DNS
description: Neste artigo, saiba sobre perguntas frequentes sobre Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 01/11/2021
ms.author: rohink
ms.openlocfilehash: cb14cb95ec1362782a634b0e62cfa2f8237a5852
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98246387"
---
# <a name="azure-dns-faq"></a>Azure DNS FAQ

## <a name="about-azure-dns"></a>Sobre O Azure DNS

### <a name="what-is-azure-dns"></a>O que é o DNS do Azure?

O Sistema de Nome de Domínio (DNS) traduz ou resolve um nome de site ou de serviço para o seu endereço IP. O DNS do Azure é um serviço de alojamento para domínios DNS. Fornece resolução de nomes utilizando a infraestrutura Microsoft Azure. Ao alojar os seus domínios no Azure, pode gerir os recursos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos seus outros serviços do Azure.

Os domínios DNS em Azure DNS estão hospedados na rede global Azure de servidores de nome DNS. Este sistema utiliza a rede Anycast para que cada consulta DNS seja respondida pelo servidor DNS mais próximo disponível. O Azure DNS proporciona um desempenho rápido e alta disponibilidade para o seu domínio.

O Azure DNS é baseado no Azure Resource Manager. O Azure DNS beneficia de funcionalidades do Gestor de Recursos, tais como controlo de acesso baseado em funções Azure, registos de auditoria e bloqueio de recursos. Pode gerir domínios e registos através do portal Azure, dos cmdlets Azure PowerShell e do Azure CLI. As aplicações que requerem gestão automática de DNS podem integrar-se com o serviço através da REST API e SDKs.

### <a name="how-much-does-azure-dns-cost"></a>Quanto custa o Azure DNS?

O modelo de faturação Azure DNS baseia-se no número de zonas DNS hospedadas no Azure DNS. Também é baseado no número de consultas de DNS que recebem. Os descontos são fornecidos com base na utilização.

Para mais informações, consulte a [página de preços do Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>O que é o SLA do DNS do Azure?

A Azure garante que os pedidos de DNS válidos recebem uma resposta de pelo menos um servidor de nome Azure DNS 100% das vezes.

Para mais informações, consulte a [página Azure DNS SLA](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>O que é uma zona de DNS? É o mesmo que um domínio DNS? 

Um domínio é um nome único no sistema de nome de domínio. Um exemplo é contoso.com.

Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Por exemplo, o domínio contoso.com pode conter vários registos DNS. Os registos podem incluir mail.contoso.com para um servidor de correio e www \. contoso.com para um website. Estes registos estão alojados na zona do DNS contoso.com.

Um nome de domínio é *apenas um nome.* Uma zona DNS é um recurso de dados que contém os registos DNS para um nome de domínio. Pode utilizar o DNS do Azure para alojar uma zona DNS e gerir os registos DNS para um domínio no Azure. Também fornece servidores de nome DNS para responder a consultas dns da Internet.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Preciso de comprar um nome de domínio DNS para usar o Azure DNS? 

Não necessariamente.

Não é necessário comprar um domínio para hospedar uma zona de DNS em Azure DNS. Pode criar uma zona DNS a qualquer momento sem possuir o nome de domínio. As consultas de DNS para esta zona só resolvem se forem direcionadas para os servidores de nomeS DNS Azure atribuídos à zona.

Para ligar a sua zona DE DNS à hierarquia global de DNS, tem de comprar o nome de domínio. Em seguida, as consultas dns de qualquer parte do mundo encontram a sua zona de DNS e respondem com os seus registos DNS.

## <a name="azure-dns-features"></a>Funcionalidades do Azure DNS

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Existem restrições ao utilizar registos de pseudónimos para um apex de nome de domínio com o Traffic Manager?

Sim. Deve utilizar endereços IP públicos estáticos com o Gestor de Tráfego Azure. Configure o alvo **do ponto final externo** utilizando um endereço IP estático. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>O Azure DNS suporta o encaminhamento de tráfego baseado em DNS ou o failover do ponto final?

O encaminhamento de tráfego baseado em DNS e a falha no ponto final são fornecidos pelo Gestor de Tráfego. Traffic Manager é um serviço Azure separado que pode ser usado com Azure DNS. Para mais informações, consulte a [visão geral do Gestor de Tráfego.](../traffic-manager/traffic-manager-overview.md)

O Azure DNS suporta apenas o hospedagem de domínios DNS estáticos, onde cada consulta de DNS para um dado registo DNS recebe sempre a mesma resposta DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>O Azure DNS suporta o registo do nome de domínio?

N.º A Azure DNS não suporta atualmente a opção de comprar nomes de domínio. Para comprar domínios, deve utilizar um registo de nomes de domínio de terceiros. O registrador normalmente cobra uma pequena taxa anual. Os domínios podem então ser hospedados no Azure DNS para a gestão dos registos DNS. Para obter mais informações, consulte [delegado de um domínio para Azure DNS](dns-domain-delegation.md).

A funcionalidade de compra de nomes de domínio é rastreada no atraso do Azure. Utilize o site de comentários para [registar o seu suporte para esta função](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>O Azure DNS suporta o DNSSEC?

N.º O Azure DNS não suporta atualmente as extensões de segurança do sistema de nome de domínio (DNSSEC).

A função DNSSEC é rastreada no atraso do Azure DNS. Utilize o site de comentários para [registar o seu suporte para esta função](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>O Azure DNS suporta transferências de zona (AXFR/IXFR)?

N.º O Azure DNS não suporta transferências de zona. As zonas de DNS podem ser [importadas para O Azure DNS utilizando o Azure CLI](dns-import-export.md). Os registos DNS são geridos através do [portal de gestão Azure DNS](dns-operations-recordsets-portal.md), [REST API,](/powershell/module/az.dns) [SDK,](dns-sdk.md) [PowerShell cmdlets,](dns-operations-recordsets.md)ou a [ferramenta CLI](dns-operations-recordsets-cli.md).

A função de transferência de zona é rastreada no atraso do Azure DNS. Utilize o site de comentários para [registar o seu suporte para esta função](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>O Azure DNS suporta redirecionamentos de URL?

N.º Os serviços de redirecionamento de URL não são um serviço DNS. Funcionam ao nível HTTP e não ao nível DNS. Alguns fornecedores de DNS agregam um serviço de redirecionamento de URL como parte da sua oferta global. Este serviço não é atualmente suportado pelo Azure DNS.

A função de redirecionamento de URL é rastreada no atraso do Azure DNS. Utilize o site de comentários para [registar o seu suporte para esta função](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>O Azure DNS suporta a codificação ascii estendida (8 bits) definida para conjuntos de recordes TXT?

Sim. O Azure DNS suporta o conjunto de codificação ASCII alargado para conjuntos de recordes TXT. Mas deve utilizar a versão mais recente das APIs de REPOUSO Azure, SDKs, PowerShell e CLI. Versões com mais de 1 de outubro de 2017 ou SDK 2.1 não suportam o conjunto ASCII alargado. 

Por exemplo, pode fornecer uma cadeia como o valor de um registo TXT que tem o caráter ASCII estendido \128. Um exemplo é "abcd\128efgh". Azure DNS usa o valor byte deste personagem, que é 128, em representação interna. No momento da resolução do DNS, este valor byte é devolvido na resposta. Note-se também que "abc" e "\097\098\099" são permutáveis no que diz respeito à resolução. 

Seguimos as regras de fuga do formato principal de ficheiros [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) para registos TXT. Por exemplo, `\` agora realmente escapa a tudo por o RFC. Se especificar `A\B` como o valor de registo TXT, é representado e resolvido como justo `AB` . Se realmente quer que o registo TXT tenha `A\B` em resolução, tem de escapar de `\` novo. Como exemplo, especifique. `A\\B`

Este suporte não está disponível para registos TXT criados a partir do portal Azure.

## <a name="alias-records"></a>Registos de alias

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Quais são alguns cenários em que os registos de pseudónimos são úteis?

Consulte a secção de cenários na visão geral dos registos do [Azure DNS](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Que tipos de discos são suportados para conjuntos de recordes de pseudónimos?

Os conjuntos de recordes de alias são suportados para os seguintes tipos de registo numa zona de DNS Azure:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Que recursos são suportados como alvos para recordes de pseudónimos?

- **Aponte para um recurso IP público a partir de um conjunto de registos DNS A/AAAA.** Pode criar um conjunto de registos A/AAAA e torná-lo um registo de pseudónimo definido para apontar para um recurso IP público.
- **Aponte para um perfil de Gestor de Tráfego a partir de um conjunto de registos DNS A/AAAA/CNAME.** Pode apontar para o perfil CNAME de um Gestor de Tráfego a partir de um conjunto de discos DNS CNAME. Um exemplo é contoso.trafficmanager.net. Agora, também pode apontar para um perfil de Gestor de Tráfego que tenha pontos finais externos a partir de um disco A ou AAAA estabelecido na sua zona de DNS.
- **Aponte para um ponto final da Rede de Entrega de Conteúdos Azure (CDN).** Isto é útil quando cria websites estáticos usando o armazenamento Azure e a Azure CDN.
- **Aponte para outro recorde de DNS estabelecido dentro da mesma zona.** Os registos de pseudónimos podem fazer referência a outros conjuntos de registos do mesmo tipo. Por exemplo, pode ter um conjunto de registos CNAME do DNS que funciona como alias de outro conjunto de registos CNAME do mesmo tipo. Este arranjo é útil se quiser que alguns recordes sejam pseudónimos e alguns não-pseudónimos.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Posso criar e atualizar registos de pseudónimos do portal Azure?

Sim. Pode criar ou gerir registos de pseudónimos no portal Azure juntamente com as APIs Azure REST, PowerShell, CLI e SDKs.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Os registos de pseudónimos ajudarão a garantir que o meu conjunto de registos DNS seja eliminado quando o IP público subjacente for eliminado?

Sim. Esta funcionalidade é uma das principais capacidades dos registos de pseudónimos. Ajuda a evitar possíveis interrupções para os utilizadores da sua aplicação.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Os registos de pseudónimos ajudarão a garantir que o meu conjunto de registos DNS seja atualizado para o endereço IP correto quando o endereço IP público subjacente for alterado?

Sim. Esta funcionalidade é uma das principais capacidades dos registos de pseudónimos. Ajuda-o a evitar possíveis interrupções ou riscos de segurança para a sua aplicação.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Existem restrições ao utilizar conjuntos de registos de pseudónimos para registos A ou AAAA para apontar para o Gestor de Tráfego?

Sim. Para apontar para um perfil de Gestor de Tráfego como um pseudónimo de um conjunto de registos A ou AAAA, o perfil de Gestor de Tráfego deve utilizar apenas pontos finais externos. Quando criar os pontos finais externos no Gestor de Tráfego, forneça os endereços IP reais dos pontos finais.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Há uma taxa adicional para usar registos de pseudónimos?

Os registos de alias são uma qualificação num conjunto de discos de DNS válidos. Não há faturação adicional para registos de pseudónimos.

## <a name="use-azure-dns"></a>Use Azure DNS

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Posso co-hospedar um domínio utilizando o Azure DNS e outro fornecedor de DNS?

Sim. O Azure DNS suporta domínios de co-hospedagem com outros serviços DNS.

Para configurar o co-hosting, modifique os registos NS para o domínio para apontar para os servidores de nome de ambos os fornecedores. O servidor de nomes (NS) regista o controlo dos fornecedores que recebem consultas de DNS para o domínio. Pode modificar estes registos NS no Azure DNS, no outro fornecedor e na zona dos pais. A zona-mãe é tipicamente configurada através do registo de nomes de domínio. Para obter mais informações sobre a delegação do DNS, consulte [a delegação de domínio DNS](dns-domain-delegation.md).

Além disso, certifique-se de que os registos DNS para o domínio estão sincronizados entre ambos os fornecedores de DNS. A azure DNS não suporta atualmente transferências de zonas DNS. Os registos DNS devem ser sincronizados utilizando o [portal de gestão Azure DNS](dns-operations-recordsets-portal.md), [REST API,](/rest/api/dns/) [SDK,](dns-sdk.md) [PowerShell cmdlets,](dns-operations-recordsets.md)ou a [ferramenta CLI](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Tenho de delegar o meu domínio nos quatro servidores de nomes Azure DNS?

Sim. O Azure DNS atribui quatro servidores de nome a cada zona DNS. Este acordo é para o isolamento de falhas e maior resiliência. Para se qualificar para o Azure DNS SLA, delege o seu domínio para todos os quatro servidores de nome.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Quais são os limites de utilização para o Azure DNS?

Os seguintes limites predefinidos aplicam-se quando utiliza o Azure DNS.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Posso mover uma zona Azure DNS entre grupos de recursos ou entre subscrições?

Sim. As zonas de DNS podem ser movidas entre grupos de recursos ou entre subscrições.

Não há nenhum efeito nas consultas de DNS quando se move uma zona de DNS. Os servidores de nomes atribuídos à zona permanecem os mesmos. As consultas de DNS são processadas normalmente em toda a sua parte.

Para obter mais informações e instruções sobre como mover zonas DNS, consulte [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Quanto tempo demora as alterações ao DNS a produzirem efeitos?

As novas zonas DNS e os registos DNS aparecem normalmente nos servidores de nomes Azure DNS rapidamente. O tempo é de alguns segundos.

As alterações aos registos DNS existentes podem demorar um pouco mais. Normalmente aparecem nos servidores de nomes Azure DNS dentro de 60 segundos. O caching dns pelos clientes DNS e as correturas recursivas do DNS fora do Azure DNS também podem afetar o timing. Para controlar esta duração da cache, utilize a propriedade Time-To-Live (TTL) de cada conjunto de discos.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Como posso proteger as minhas zonas de DNS contra a eliminação acidental?

O Azure DNS é gerido utilizando o Azure Resource Manager. O Azure DNS beneficia das funcionalidades de controlo de acesso que o Azure Resource Manager fornece. O controlo de acesso baseado em funções Azure pode ser usado para controlar quais os utilizadores que leram ou escreveram acesso a zonas DNS e conjuntos de registos. Os bloqueios de recursos impedem a modificação acidental ou a eliminação de zonas DNS e conjuntos de registos.

Para obter mais informações, consulte [as zonas e registos do DNS Protect](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Como posso configurar registos de SPF no Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Os servidores de nomes DNS do Azure resolvem-se sobre o IPv6? 

Sim. Os servidores de nomes Azure DNS são dupla pilha. Pilha dupla significa que têm endereços IPv4 e IPv6. Para encontrar o endereço IPv6 para os servidores de nomeS DNS Azure atribuídos à sua zona DE DNS, utilize uma ferramenta como o nslookup. Um exemplo é `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Como posso criar um IDN em Azure DNS?

Os nomes de domínio internacionalizados (IDNs) codificam cada nome DNS utilizando [o código punycode](https://en.wikipedia.org/wiki/Punycode). As consultas de DNS são feitas usando estes nomes codificados por um insignificante código.

Para configurar iDNs em DNS Azure, converta o nome da zona ou o nome do conjunto de registos para o código punycode. O Azure DNS não suporta atualmente a conversão incorporada para ou a partir de um código de punycode.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre o Azure DNS](dns-overview.md).

- [Saiba mais sobre como utilizar o Azure DNS para domínios privados](private-dns-overview.md).

- [Saiba mais sobre as zonas e registos DNS](dns-zones-records.md).

- [Começa com o Azure DNS](dns-getstarted-portal.md).
