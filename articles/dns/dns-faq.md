---
title: Perguntas frequentes-DNS do Azure
description: Neste artigo, saiba mais sobre as perguntas frequentes sobre o DNS do Azure
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 6/15/2019
ms.author: rohink
ms.openlocfilehash: 990adf73211e96370fd06f5e322301128321e81f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937298"
---
# <a name="azure-dns-faq"></a>Perguntas frequentes sobre o DNS do Azure

## <a name="about-azure-dns"></a>Sobre o DNS do Azure

### <a name="what-is-azure-dns"></a>O que é o Azure DNS?

O DNS (sistema de nomes de domínio) traduz ou resolve, um nome de site ou serviço para seu endereço IP. O DNS do Azure é um serviço de hospedagem para domínios DNS. Ele fornece a resolução de nomes usando Microsoft Azure infraestrutura. Ao alojar os seus domínios no Azure, pode gerir os recursos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos seus outros serviços do Azure.

Os domínios DNS no DNS do Azure são hospedados na rede global do Azure de servidores de nomes DNS. Esse sistema usa rede anycast para que cada consulta DNS seja respondida pelo servidor DNS mais próximo disponível. O DNS do Azure fornece desempenho rápido e alta disponibilidade para seu domínio.

O DNS do Azure é baseado em Azure Resource Manager. O Azure DNS beneficia-se dos recursos do Resource Manager, como o controle de acesso baseado em função, os logs de auditoria e o bloqueio de recursos. Você pode gerenciar domínios e registros por meio do portal do Azure, Azure PowerShell cmdlets e CLI do Azure de plataforma cruzada. Aplicativos que exigem gerenciamento de DNS automático podem se integrar ao serviço por meio da API REST e SDKs.

### <a name="how-much-does-azure-dns-cost"></a>Quanto custa o DNS do Azure?

O modelo de cobrança do DNS do Azure é baseado no número de zonas DNS hospedadas no DNS do Azure. Ele também se baseia no número de consultas DNS recebidas. Os descontos são fornecidos com base no uso.

Para obter mais informações, consulte a [página de preços do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Qual é o SLA do DNS do Azure?

O Azure garante que os pedidos dNS válidos recebem uma resposta de pelo menos um servidor de nome DNS Azure a 100% das vezes.

Para obter mais informações, consulte a [página SLA do DNS do Azure](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>O que é uma zona DNS? É o mesmo que um domínio de DNS? 

Um domínio é um nome exclusivo no sistema de nomes de domínio. Um exemplo é contoso.com.

Uma zona DNS é utilizada para alojar os registos de DNS de um domínio específico. Por exemplo, o domínio contoso.com pode conter vários registros DNS. Os registros podem incluir mail.contoso.com para um servidor de email e o www\.contoso.com para um site. Esses registros são hospedados na zona DNS contoso.com.

Um nome de domínio é *apenas um nome*. Uma zona DNS é um recurso de dados que contém os registros DNS para um nome de domínio. Pode utilizar o DNS do Azure para alojar uma zona DNS e gerir os registos DNS para um domínio no Azure. Ele também fornece servidores de nomes DNS para responder a consultas DNS da Internet.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Preciso comprar um nome de domínio DNS para usar o DNS do Azure? 

Não necessariamente.

Você não precisa comprar um domínio para hospedar uma zona DNS no DNS do Azure. Pode criar uma zona DNS a qualquer momento sem ter um nome de domínio. As consultas DNS para essa zona são resolvidas apenas se forem direcionadas para os servidores de nome DNS do Azure atribuídos à zona.

Para vincular a zona DNS à hierarquia de DNS global, você deve comprar o nome de domínio. Em seguida, as consultas DNS de qualquer lugar do mundo localizam sua zona DNS e respondem com seus registros DNS.

## <a name="azure-dns-features"></a>Recursos de DNS do Azure

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Há alguma restrição ao usar registros de alias para um nome de domínio Apex com o Gerenciador de tráfego?

Sim. Você deve usar endereços IP públicos estáticos com o Gerenciador de tráfego do Azure. Configure o destino do **ponto de extremidade externo** usando um endereço IP estático. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>O DNS do Azure dá suporte ao roteamento de tráfego baseado em DNS ou failover de ponto de extremidade?

O roteamento de tráfego baseado em DNS e o failover de ponto de extremidade são fornecidos pelo Gerenciador de tráfego. O Gerenciador de tráfego é um serviço do Azure separado que pode ser usado com o DNS do Azure. Para obter mais informações, consulte a [visão geral do Gerenciador de tráfego](../traffic-manager/traffic-manager-overview.md).

O DNS do Azure dá suporte apenas à Hospedagem de domínios DNS estáticos, em que cada consulta DNS para um determinado registro DNS sempre recebe a mesma resposta DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>O DNS do Azure dá suporte ao registro de nome de domínio?

Não. Atualmente, o DNS do Azure não oferece suporte à opção de comprar nomes de domínio. Para comprar domínios, você deve usar um registrador de nome de domínio de terceiros. O registrador normalmente cobra uma pequena taxa anual. Os domínios podem ser hospedados no DNS do Azure para o gerenciamento de registros DNS. Para obter mais informações, veja [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md).

O recurso para comprar nomes de domínio é acompanhado na pendência do Azure. Use o site de comentários para [registrar seu suporte para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>O DNS do Azure dá suporte a DNSSEC?

Não. No momento, o DNS do Azure não dá suporte ao DNSSEC (extensões de segurança do sistema) de nome de domínio.

O recurso DNSSEC é acompanhado no registro posterior de DNS do Azure. Use o site de comentários para [registrar seu suporte para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>O DNS do Azure dá suporte a transferências de zona (AXFR/IXFR)?

Não. No momento, o DNS do Azure não dá suporte a transferências de zona. As zonas DNS podem ser [importadas para o DNS do Azure usando o CLI do Azure](dns-import-export.md). Os registros DNS são gerenciados por meio do [portal de gerenciamento do DNS do Azure](dns-operations-recordsets-portal.md), da [API REST](https://docs.microsoft.com/powershell/module/az.dns), do [SDK](dns-sdk.md), dos [cmdlets do PowerShell](dns-operations-recordsets.md)ou da [ferramenta CLI](dns-operations-recordsets-cli.md).

O recurso de transferência de zona é acompanhado no registro posterior de DNS do Azure. Use o site de comentários para [registrar seu suporte para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>O DNS do Azure dá suporte a redirecionamentos de URL?

Não. Os serviços de redirecionamento de URL não são um serviço DNS. Eles funcionam no nível de HTTP em vez do nível de DNS. Alguns provedores DNS agrupam um serviço de redirecionamento de URL como parte de sua oferta geral. Atualmente, esse serviço não tem suporte do DNS do Azure.

O recurso de redirecionamento de URL é acompanhado no registro posterior de DNS do Azure. Use o site de comentários para [registrar seu suporte para esse recurso](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>O DNS do Azure dá suporte ao conjunto de codificação ASCII estendida (8 bits) para conjuntos de registros TXT?

Sim. O DNS do Azure dá suporte ao conjunto de codificação ASCII estendido para conjuntos de registros TXT. Mas você deve usar a versão mais recente das APIs REST do Azure, SDKs, PowerShell e a CLI. Versões anteriores a 1 de outubro de 2017 ou SDK 2,1 não dão suporte ao conjunto ASCII estendido. 

Por exemplo, você pode fornecer uma cadeia de caracteres como o valor para um registro TXT que tenha o caractere ASCII estendido \ 128. Um exemplo é "abcd\128efgh." O DNS do Azure usa o valor de byte desse caractere, que é 128, na representação interna. No momento da resolução de DNS, esse valor de byte é retornado na resposta. Observe também que "ABC" e "\ 097 \ 098 \ 099" são intercambiáveis no que diz respeito à resolução. 

Seguimos as regras de escape do formato mestre do arquivo de zona [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) para registros txt. Por exemplo, `\` agora realmente sai de tudo por RFC. Se você especificar `A\B` como o valor do registro TXT, ele será representado e resolvido como apenas `AB`. Se você realmente quiser que o registro TXT tenha `A\B` na resolução, será necessário escapar a `\` novamente. Por exemplo, especifique `A\\B`.

Esse suporte atualmente não está disponível para registros TXT criados no portal do Azure.

## <a name="alias-records"></a>Registos de alias

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Quais são alguns cenários em que os registros de alias são úteis?

Consulte a seção cenários na [visão geral de registros de alias do DNS do Azure](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Quais tipos de registro têm suporte para conjuntos de registros de alias?

Os conjuntos de registros de alias têm suporte para os seguintes tipos de registro em uma zona DNS do Azure:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Quais recursos têm suporte como destinos para conjuntos de registros de alias?

- **Aponte para um recurso IP público de um conjunto de registros DNS A/AAAA.** Você pode criar um conjunto de registros A/AAAA e torná-lo um conjunto de registros de alias para apontar para um recurso de IP público.
- **Aponte para um perfil do Gerenciador de tráfego de um conjunto de registros DNS A/AAAA/CNAME.** Você pode apontar para o CNAME de um perfil do Gerenciador de tráfego de um conjunto de registros DNS CNAME. Um exemplo é contoso.trafficmanager.net. Agora, você também pode apontar para um perfil do Gerenciador de tráfego que tem pontos de extremidade externos de um conjunto de registros A ou AAAA em sua zona DNS.
- **Aponte para um ponto de extremidade da CDN (rede de distribuição de conteúdo) do Azure**. Isso é útil quando você cria sites estáticos usando o armazenamento do Azure e a CDN do Azure.
- **Aponte para outro conjunto de registros DNS dentro da mesma zona.** Os registros de alias podem fazer referência a outros conjuntos de registros do mesmo tipo. Por exemplo, pode ter um conjunto de registos CNAME do DNS que funciona como alias de outro conjunto de registos CNAME do mesmo tipo. Essa organização será útil se você quiser que alguns conjuntos de registros sejam aliases e alguns não aliases.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Posso criar e atualizar registros de alias do portal do Azure?

Sim. Você pode criar ou gerenciar registros de alias no portal do Azure juntamente com as APIs REST do Azure, o PowerShell, a CLI e os SDKs.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Os registros de alias terão ajuda para garantir que meu conjunto de registros DNS seja excluído quando o IP público subjacente for excluído?

Sim. Esse recurso é um dos principais recursos de registros de alias. Ele ajuda a evitar possíveis interrupções para os usuários do seu aplicativo.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Os registros de alias ajudarão a garantir que meu conjunto de registros DNS seja atualizado para o endereço IP correto quando o endereço IP público subjacente for alterado?

Sim. Esse recurso é um dos principais recursos de registros de alias. Ele ajuda a evitar possíveis interrupções ou riscos de segurança para seu aplicativo.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Há restrições ao usar conjuntos de registros de alias para registros A ou AAAA para apontar para o Gerenciador de tráfego?

Sim. Para apontar para um perfil do Gerenciador de tráfego como um alias de um conjunto de registros A ou AAAA, o perfil do Gerenciador de tráfego deve usar somente pontos de extremidade externos. Quando você cria os pontos de extremidade externos no Gerenciador de tráfego, forneça os endereços IP reais dos pontos de extremidade.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Há um encargo adicional para usar registros de alias?

Os registros de alias são uma qualificação em um conjunto de registros DNS válido. Não há cobrança adicional para registros de alias.

## <a name="use-azure-dns"></a>Usar o DNS do Azure

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Posso cohospedar um domínio usando o DNS do Azure e outro provedor de DNS?

Sim. O DNS do Azure dá suporte a domínios de hospedagem cooperating com outros serviços DNS.

Para configurar a hospedagem cooperativa, modifique os registros NS para o domínio para apontar para os servidores de nome de ambos os provedores. Os registros de servidor de nomes (NS) controlam quais provedores recebem consultas DNS para o domínio. Você pode modificar esses registros NS no DNS do Azure, no outro provedor e na zona pai. A zona pai é normalmente configurada por meio do registrador de nome de domínio. Para obter mais informações sobre a delegação de DNS, consulte [delegação de domínio DNS](dns-domain-delegation.md).

Além disso, verifique se os registros DNS do domínio estão em sincronia entre ambos os provedores DNS. No momento, o DNS do Azure não oferece suporte a transferências de zona DNS. Os registros DNS devem ser sincronizados usando o [portal de gerenciamento do DNS do Azure](dns-operations-recordsets-portal.md), a [API REST](https://docs.microsoft.com/powershell/module/az.dns), o [SDK](dns-sdk.md), os [cmdlets do PowerShell](dns-operations-recordsets.md)ou a [ferramenta CLI](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>É necessário delegar meu domínio para todos os quatro servidores de nome do DNS do Azure?

Sim. O DNS do Azure atribui quatro servidores de nomes a cada zona DNS. Essa organização destina-se ao isolamento de falhas e maior resiliência. Para se qualificar para o SLA do DNS do Azure, delegue seu domínio a todos os quatro servidores de nomes.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Quais são os limites de uso do DNS do Azure?

Os limites padrão a seguir se aplicam quando você usa o DNS do Azure.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Posso mover uma zona DNS do Azure entre grupos de recursos ou entre assinaturas?

Sim. As zonas DNS podem ser movidas entre grupos de recursos ou entre assinaturas.

Não há nenhum efeito nas consultas DNS quando você move uma zona DNS. Os servidores de nomes atribuídos à zona permanecem os mesmos. As consultas DNS são processadas normalmente em todo o processo.

Para obter mais informações e instruções sobre como mover zonas DNS, consulte [mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Quanto tempo leva para que as alterações de DNS entrem em vigor?

As novas zonas DNS e os registros DNS normalmente aparecem nos servidores de nomes DNS do Azure rapidamente. O tempo é de alguns segundos.

As alterações nos registros DNS existentes podem demorar um pouco mais. Eles normalmente aparecem nos servidores de nomes do DNS do Azure dentro de 60 segundos. O cache DNS por clientes DNS e resolvedores de DNS recursivos fora do DNS do Azure também podem afetar o tempo. Para controlar essa duração de cache, use a propriedade TTL (vida útil) de cada conjunto de registros.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Como posso proteger minhas zonas DNS contra exclusão acidental?

O DNS do Azure é gerenciado usando Azure Resource Manager. O Azure DNS beneficia-se dos recursos de controle de acesso que o Azure Resource Manager fornece. O controle de acesso baseado em função controla quais usuários têm acesso de leitura ou gravação a zonas DNS e conjuntos de registros. Os bloqueios de recursos impedem a modificação acidental ou a exclusão de zonas DNS e conjuntos de registros.

Para obter mais informações, consulte [proteger zonas e registros DNS](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Como fazer configurar registros SPF no DNS do Azure?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Os servidores de nomes DNS do Azure são resolvidos sobre IPv6? 

Sim. Os servidores de nome DNS do Azure são pilha dupla. Pilha dupla significa que eles têm endereços IPv4 e IPv6. Para localizar o endereço IPv6 para os servidores de nome DNS do Azure atribuídos à sua zona DNS, use uma ferramenta como nslookup. Um exemplo é `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Como fazer configurar um IDN no DNS do Azure?

Os IDNs (nomes de domínio internacionalizados) codificam cada nome DNS usando [Punycode](https://en.wikipedia.org/wiki/Punycode). As consultas DNS são feitas usando esses nomes codificados em Punycode.

Para configurar IDNs no DNS do Azure, converta o nome da zona ou o nome do conjunto de registros em Punycode. Atualmente, o DNS do Azure não dá suporte à conversão interna de ou para Punycode.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre o DNS do Azure](dns-overview.md).

- [Saiba mais sobre como usar o DNS do Azure para domínios privados](private-dns-overview.md).

- [Saiba mais sobre zonas e registros DNS](dns-zones-records.md).

- [Introdução ao DNS do Azure](dns-getstarted-portal.md).
