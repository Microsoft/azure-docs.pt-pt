---
title: FAQ sobre DNS do Azure
description: Perguntas freqüentes sobre DNS do Azure
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: d0c5260fcc2e7ac2acbeec308c6a0cba7d6a81be
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098098"
---
# <a name="azure-dns-faq"></a>FAQ sobre DNS do Azure

## <a name="about-azure-dns"></a>Sobre o DNS do Azure

### <a name="what-is-azure-dns"></a>O que é o DNS do Azure?

O sistema de nomes de domínio (DNS) traduz ou resolvido, um nome do Web site ou serviço para o endereço IP. O DNS do Azure é um serviço de alojamento para domínios DNS. Ele fornece resolução de nomes através da infraestrutura do Microsoft Azure. Ao alojar os seus domínios no Azure, pode gerir os recursos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos seus outros serviços do Azure.

Domínios DNS no DNS do Azure estão alojados na rede do Azure global de servidores de nomes DNS. Este sistema utiliza o funcionamento em rede Anycast, de modo a que cada consulta DNS for atendida pelo servidor DNS disponível mais próximo. O DNS do Azure fornece um desempenho rápido e de elevada disponibilidade para o seu domínio.

O DNS do Azure baseia-se no Azure Resource Manager. O DNS do Azure beneficia de funcionalidades do Gestor de recursos, tais como o controlo de acesso baseado em funções, registos de auditoria e bloqueios de recursos. Pode gerir domínios e registos através do portal do Azure, cmdlets do PowerShell do Azure e a CLI do Azure de várias plataformas. Aplicações que necessitam de gestão automática de DNS podem integrar com o serviço através da REST API e SDKs.

### <a name="how-much-does-azure-dns-cost"></a>Quanto custa o DNS do Azure?

O modelo de faturação do DNS do Azure baseia-se no número de zonas DNS alojadas no DNS do Azure. Ele também se baseia no número de consultas DNS recebidas. Descontos são oferecidos com base na utilização.

Para obter mais informações, consulte a [DNS do Azure, página de preços](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Qual é o SLA do DNS do Azure?

O Azure garante que os pedidos DNS válidos recebem uma resposta de, pelo menos, um servidor de nome de DNS do Azure 100% do tempo.

Para obter mais informações, consulte a [página do SLA de DNS do Azure](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>O que é uma zona DNS? É o mesmo que um domínio de DNS? 

Um domínio é um nome exclusivo no sistema de nome de domínio. Um exemplo é contoso.com.

Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Por exemplo, o domínio contoso.com pode conter vários registos DNS. Os registos podem incluir mail.contoso.com para um servidor de email e www\.contoso.com para um Web site. Estes registos estão alojados na zona DNS contoso.com.

É um nome de domínio *apenas um nome*. Uma zona DNS é um recurso de dados que contém os registos DNS para um nome de domínio. Pode utilizar o DNS do Azure para alojar uma zona DNS e gerir os registos DNS para um domínio no Azure. Ele também fornece servidores de nomes DNS para responder a consultas DNS da Internet.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Preciso de comprar um nome de domínio DNS para utilizar o DNS do Azure? 

Não necessariamente.

Não precisa de comprar um domínio para alojar uma zona DNS no DNS do Azure. Pode criar uma zona DNS a qualquer momento sem ter um nome de domínio. Consultas DNS para esta zona resolver apenas se estiver direcionados para os servidores de nomes de DNS do Azure atribuídos à zona.

Para ligar a sua zona DNS para a hierarquia DNS global, tem de comprar o nome de domínio. Em seguida, consultas de DNS de qualquer lugar do mundo encontrar a zona DNS e a resposta com os registos DNS.

## <a name="azure-dns-features"></a>Funcionalidades DNS do Azure

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Existem restrições ao utilizar registos de alias para um vértice de nome de domínio com o Gestor de tráfego?

Sim. Tem de utilizar endereços IP públicos estáticos com o Gestor de tráfego do Azure. Configurar o **ponto final externo** destino utilizando um endereço IP estático. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>O DNS do Azure suporta a ativação pós-falha de encaminhamento ou ponto final de tráfego baseado em DNS?

Ativação pós-falha de encaminhamento e o ponto final de tráfego baseado em DNS são fornecidos pelo Gestor de tráfego. Gestor de tráfego é um serviço do Azure separado que pode ser utilizado com o DNS do Azure. Para obter mais informações, consulte a [descrição geral do Gestor de tráfego](../traffic-manager/traffic-manager-overview.md).

O DNS do Azure só suporta o alojamento dos domínios DNS estáticos, onde cada consulta DNS para um determinado registo DNS recebe sempre a mesma resposta DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>O DNS do Azure suporta o registo de nome de domínio?

Não. O DNS do Azure atualmente não suporta a opção de comprar os nomes de domínio. Para comprar domínios, tem de utilizar uma entidade de registo de nome de domínio de terceiros. A entidade de registo, normalmente, os custos de uma pequena taxa anual. Os domínios, em seguida, podem ser hospedados no DNS do Azure para a gestão de registos DNS. Para obter mais informações, veja [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md).

A funcionalidade de comprar os nomes de domínio está registada no registo de segurança do Azure. Utilize o site de comentários para [registar o suporte para esta funcionalidade](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>O DNS do Azure suporta DNSSEC?

Não. O DNS do Azure atualmente não suporta o domínio nome de sistema extensões de segurança (DNSSEC).

A funcionalidade DNSSEC é controlada na lista de pendências de DNS do Azure. Utilize o site de comentários para [registar o suporte para esta funcionalidade](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>O DNS do Azure suporta transferências de zona (AXFR/IXFR)?

Não. O DNS do Azure atualmente não suporta transferências de zona. Zonas DNS podem ser [importados para o DNS do Azure com a CLI do Azure](dns-import-export.md). Registos DNS são geridos através da [portal de gestão do DNS do Azure](dns-operations-recordsets-portal.md), [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [cmdlets do PowerShell](dns-operations-recordsets.md), ou o [ Ferramenta CLI](dns-operations-recordsets-cli.md).

A funcionalidade de transferência de zona é controlada na lista de pendências de DNS do Azure. Utilize o site de comentários para [registar o suporte para esta funcionalidade](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>O DNS do Azure suporta redirecionamentos de URL?

Não. Serviços de redirecionamento de URL não são um serviço DNS. Eles trabalham em nível HTTP em vez de nível do DNS. Alguns fornecedores DNS agrupar um serviço de redirecionamento de URL como parte da sua oferta geral. Este serviço não é atualmente suportado pelo DNS do Azure.

A funcionalidade de redirecionamento de URL é controlada na lista de pendências de DNS do Azure. Utilize o site de comentários para [registar o suporte para esta funcionalidade](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>O DNS do Azure suporta o ASCII estendido codificação (8 bits) conjunto para conjuntos de registos TXT?

Sim. O DNS do Azure suporta o conjunto para conjuntos de registos TXT de codificação de ASCII estendido. Mas tem de utilizar a versão mais recente do API de REST do Azure, SDKs, PowerShell e a CLI. Versões anteriores a 1 de Outubro de 2017, ou o SDK 2.1 não suporta o conjunto de ASCII estendido. 

Por exemplo, poderá fornecer uma cadeia de caracteres como o valor para um registo TXT que tenha o expandida caráter \128 ASCII. Um exemplo é "abcd\128efgh." O DNS do Azure utiliza o valor de byte deste caráter, o que é 128, na representação interna. No momento da resolução de DNS, este valor de bytes é devolvido na resposta. Observe também que "abc" e "\097\098\099" são intercambiáveis como diz respeito a resolução. 

Seguimos [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) zona regras de escape de mestre de formato de ficheiro para registos TXT. Por exemplo, `\` agora escapes, na verdade, tudo por de RFC. Se especificar `A\B` como o valor de registo TXT, que tem representado e resolvido apenas como `AB`. Se realmente desejar, de facto, o registo TXT ter `A\B` resolução, terá de escape o `\` novamente. Por exemplo, especificar `A\\B`.

Esse suporte atualmente não está disponível para registos TXT criados a partir do portal do Azure.

## <a name="alias-records"></a>Registos de alias

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Quais são alguns cenários em que os registos de alias são úteis?
Consulte os cenários de secção a [DNS do Azure registos descrição geral de alias](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Que tipos de registo são suportados para conjuntos de registos de alias?
Conjuntos de registos de alias são suportados para os seguintes tipos de registos numa zona de DNS do Azure:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Os recursos que são suportados como destinos para conjuntos de registos de alias?

- **Ponto para um recurso IP público a partir de um conjunto de registos de A/AAAA de DNS.** Pode criar um conjunto de registos A/AAAA e torná-lo um conjunto de registos de alias para apontar para um recurso IP público.
- **Ponto para um perfil do Gestor de tráfego a partir de um conjunto de registos DNS A/AAAA/CNAME.** Pode apontar para o CNAME de um perfil do Gestor de tráfego de um conjunto de registos CNAME no DNS. Um exemplo é contoso.trafficmanager.net. Agora, também pode apontar para um perfil de Gestor de tráfego que tem pontos finais externos a partir de um conjunto na sua zona DNS de registos A ou AAAA.
- **Aponte para outro conjunto de registos de DNS na mesma zona.** Registos de alias podem fazer referência a outros conjuntos de registos do mesmo tipo. Por exemplo, pode ter um conjunto de registos CNAME do DNS que funciona como alias de outro conjunto de registos CNAME do mesmo tipo. Essa disposição é útil se desejar alguns conjuntos de registos para ser aliases e alguns não aliases.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Pode criar e atualizar alias registos a partir do portal do Azure?
Sim. Pode criar ou gerir os registos de alias no portal do Azure, juntamente com as APIs de REST do Azure, PowerShell, CLI e SDKs.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Registos de alias ajudará a certificar-se de que meu conjunto de registos de DNS é eliminado quando o IP público subjacente é eliminado?
Sim. Esta funcionalidade é um dos principais recursos de registos de alias. Ele ajuda a evitar potenciais falhas para os utilizadores da sua aplicação.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Será o alias regista a ajuda para se certificar de que o meu conjunto de registos de DNS é atualizado para o endereço IP correto quando o endereço IP público subjacente é alterada?
Sim. Esta funcionalidade é um dos principais recursos de registos de alias. Ele ajuda a evitar potenciais falhas ou os riscos de segurança para a sua aplicação.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Existem restrições ao utilizar o registo de alias conjuntos para A ou AAAA regista para apontar para o Gestor de tráfego?
Sim. Para apontar para um perfil do Gestor de tráfego, como um alias de um A ou AAAA conjunto de registos, o Gestor de tráfego perfil tem de utilizar apenas pontos finais externos. Quando cria os pontos finais externos no Gestor de tráfego, forneça os endereços IP reais dos pontos finais.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Existe um custo adicional para utilizar os registos de alias?
Os registos de alias são uma qualificação por um conjunto de registos de DNS válido. Há uma faturação sem adicional para os registos de alias.

## <a name="use-azure-dns"></a>Utilizar o DNS do Azure

### <a name="can-i-cohost-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Pode cohost um domínio com o DNS do Azure e de outro fornecedor DNS?

Sim. O DNS do Azure suporta cohosting domínios com outros serviços DNS.

Para configurar a cohosting, modificar os registos NS para o domínio para que apontem para os servidores de nomes de ambos os fornecedores. O servidor de nomes (NS) regista o controle de quais fornecedores recebem consultas DNS para o domínio. Pode modificar estes registos NS no DNS do Azure, o outro fornecedor e na zona principal. Zona principal é normalmente configurada a entidade de registo de nome de domínio. Para obter mais informações sobre delegação de DNS, consulte [delegação de domínio DNS](dns-domain-delegation.md).

Além disso, certifique-se de que os registos DNS para o domínio são sincronizados entre os dois provedores DNS. O DNS do Azure atualmente não suporta transferências de zona DNS. Registos DNS têm de ser sincronizados através do [portal de gestão do DNS do Azure](dns-operations-recordsets-portal.md), [REST API](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [cmdlets do PowerShell](dns-operations-recordsets.md), ou o [Ferramenta CLI](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>É necessário delegar meu domínio para todos os quatro servidores de nomes de DNS do Azure?

Sim. O DNS do Azure atribui quatro servidores de nomes para cada zona DNS. Essa disposição é para isolamento de falhas e uma maior resiliência. Para se qualificar para o SLA de DNS do Azure, delegar o domínio para todos os quatro servidores de nomes.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Quais são os limites de utilização para o DNS do Azure?

Os limites predefinidos seguintes aplicam-se ao utilizar o DNS do Azure.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Posso mover uma zona DNS do Azure entre grupos de recursos ou entre subscrições?

Sim. Zonas DNS podem ser movidas entre grupos de recursos ou entre subscrições.

Não é sem qualquer efeito nas consultas DNS ao mover uma zona DNS. Os servidores de nomes atribuídos à zona permanecem iguais. Consultas DNS são processadas como habitualmente durante todo.

Para obter mais informações e instruções sobre como mover zonas DNS, consulte [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Quanto tempo demora para alterações DNS entrem em vigor?

Novas zonas DNS e registos DNS, normalmente, são apresentados nos servidores de nomes do DNS do Azure rapidamente. O período de tempo é alguns segundos.

As alterações aos registos DNS existentes podem demorar um pouco mais. Normalmente, aparecem no DNS do Azure servidores de nomes em 60 segundos. DNS cache por clientes DNS e o DNS recursivo resoluções de fora do DNS do Azure também podem afetar a temporização. Para controlar a duração da cache, utilize a propriedade do Time-To-Live (TTL) de cada conjunto de registos.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Como posso proteger meus zonas DNS eliminações acidentais?

O DNS do Azure é gerido através da utilização do Azure Resource Manager. O DNS do Azure se beneficia os recursos de controle de acesso do Azure Resource Manager fornece. Controlo de acesso baseado em funções controla quais os utilizadores tem de leitura ou acesso de escrita para zonas DNS e conjuntos de registos. Bloqueios de recursos impedem acidental modificação ou eliminação de zonas DNS e conjuntos de registos.

Para obter mais informações, consulte [zonas e registos DNS proteger](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Como configurar registros SPF no DNS do Azure?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Resolver os servidores de nomes de DNS do Azure através de IPv6? 

Sim. Servidores de nomes DNS do Azure são de pilha dupla. Pilha dupla significa que eles têm o IPv4 e IPv6 resolve. Para localizar o endereço IPv6 para os servidores de nome de DNS do Azure atribuídos à sua zona DNS, utilize uma ferramenta como a nslookup. Um exemplo é `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Como configurar um IDN no DNS do Azure?

Nomes de domínio internacionalizados (IDNs) codificar cada nome DNS usando [punycode](https://en.wikipedia.org/wiki/Punycode). Consultas DNS são feitas usando esses nomes com codificação punycode.

Para configurar IDNs no DNS do Azure, converta o nome da zona ou o nome do conjunto de registos punycode. O DNS do Azure atualmente não suporta a conversão incorporada de ou para punycode.

## <a name="private-dns"></a>DNS privado

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>O DNS do Azure suporta domínios privados?

Suporte para domínios privados é implementado através da funcionalidade zonas privadas. Esta funcionalidade está atualmente disponível em pré-visualização pública. Zonas privadas são geridas com as mesmas ferramentas como zonas de DNS do Azure para a internet. Eles são resolvíveis apenas a partir de dentro de suas redes virtuais especificados. Para obter mais informações, consulte a [descrição geral](private-dns-overview.md).

Atualmente, as zonas privadas não são suportadas no portal do Azure.

Para obter informações sobre outras opções de DNS internas no Azure, consulte [resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="whats-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>O que é a diferença entre a rede virtual de registo e de rede virtual de resolução no contexto de zonas privadas?

Pode ligar redes virtuais a uma zona DNS privada, como uma rede virtual de registo ou como uma rede virtual de resolução. Em ambos os casos, as máquinas virtuais na rede virtual resolver com êxito em relação a registos na zona privada. Com uma rede virtual de registo, os registos DNS são automaticamente registrados para a zona para as máquinas virtuais na rede virtual. Quando uma máquina virtual no registo de uma rede virtual é eliminada, o registo DNS correspondente da zona privada ligado é removido automaticamente. 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>As zonas privadas do DNS do Azure irá funcionar entre regiões do Azure?

Sim. As zonas privadas é suportada para a resolução DNS entre redes virtuais em regiões do Azure. As zonas privadas do funciona até mesmo sem peering explicitamente as redes virtuais. Todas as redes virtuais têm de ser especificadas como redes virtuais de resolução para a zona privada. Clientes podem ter as redes virtuais para ser executado o peering para o tráfego TCP/HTTP para o fluxo de uma região para outra.

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>É a conectividade à Internet de redes virtuais necessárias para zonas privadas?

Não. Zonas privadas trabalham em conjunto com redes virtuais. Os clientes usá-los para gerir domínios para máquinas virtuais ou outros recursos dentro e entre redes virtuais. Conectividade com a Internet não é necessária para resolução de nomes. 

### <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Da mesma zona privada pode ser utilizada para várias redes virtuais para a resolução?

Sim. Os clientes podem associar até 10 redes virtuais de resolução com uma única zona privada.

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>Pode uma rede virtual que pertence a uma subscrição diferente ser adicionada como uma rede virtual de resolução para uma zona privada?

Sim. Tem de ter permissão de operação de escrita sobre as redes virtuais e a zona DNS privada. A permissão de escrita pode ser concedida a várias funções do RBAC. Por exemplo, a função RBAC de contribuinte de rede clássica tem permissões de escrita para redes virtuais. Para obter mais informações sobre funções RBAC, veja [controlo de acesso baseado em funções](../role-based-access-control/overview.md).

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>Os registos DNS da máquina de virtual automaticamente registado numa zona privada sejam eliminados automaticamente quando as máquinas virtuais são eliminadas pelo cliente?

Sim. Se eliminar uma máquina virtual dentro de uma rede virtual de registo, os registos DNS que foram registados para a zona são automaticamente eliminados. 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>Um registo de máquina de virtual automaticamente registrados numa zona privada de uma rede virtual de registo pode ser eliminado manualmente?

Não. Os registos DNS de máquina virtual que são automaticamente registrados numa zona privada de uma rede virtual de registo não estão visíveis ou editável pelos clientes. Pode substituir os registos DNS registados automaticamente com um registo DNS criado manualmente na zona. A seguinte pergunta e resposta abordar neste tópico.

### <a name="what-happens-when-we-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>O que acontece quando tentamos criar manualmente um novo registo DNS para uma zona privada que tem o mesmo nome do anfitrião como uma máquina virtual existente automaticamente registrada numa rede virtual de registo?

Tente criar manualmente um novo registo DNS para uma zona privada que tem o mesmo nome do anfitrião como uma máquina de virtual existente, automaticamente registrada numa rede virtual de registo. Quando o fizer, o novo registo DNS substitui o registo de máquina de virtual registado automaticamente. Se tentar eliminar este registo DNS criado manualmente a partir da zona da novamente, a eliminação for concluída com êxito. O registo automático voltar a ocorrer, desde que a máquina virtual ainda existe e tem um IP privado ligados ao mesmo. O registo DNS seja recriado automaticamente na zona.

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>O que acontece quando podemos desassociar a uma rede virtual de registo de uma zona privada? Serão os registos de máquina de virtual registado automaticamente da rede virtual removidos da zona demasiado?

Sim. Para desassociar a uma rede virtual de registo de uma zona privada, atualize a zona DNS para remover a rede virtual de registo associada. Nesse processo, os registos de máquina virtual que foram registados automaticamente são removidos da zona. 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>O que acontece quando vamos eliminar um registo ou resolução da rede virtual que está ligada a uma zona privada? Temos de atualizar manualmente a zona privada ao desassociar a rede virtual como um registo ou rede virtual de resolução da zona?

Sim. Quando elimina uma rede virtual de registo ou resolução sem desassociar primeiro de uma zona privada, a operação de eliminação for concluída com êxito. Mas a rede virtual não é automaticamente desassociada da sua zona privada, se aplicável. Deve desassociar manualmente a rede virtual a partir da zona privada. Por esse motivo, desassocie a rede virtual da sua zona privada antes de o eliminar.

### <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Resolução de DNS ao utilizar a predefinição FQDN (internal.cloudapp.net) continuarão a funcionar mesmo quando uma zona privada (por exemplo, private.contoso.com) está ligada a uma rede virtual?

Sim. As zonas privadas do não substitui as resoluções DNS padrão através da utilização da zona de internal.cloudapp.net fornecida pelo Azure. É oferecido como uma funcionalidade adicional ou um aprimoramento. Se se basear no internal.cloudapp.net fornecida pelo Azure ou em sua própria zona privada, utilize o FQDN da zona que pretende resolver contra. 

### <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Será alterado o sufixo DNS em máquinas virtuais numa rede virtual ligada para que a zona privada?

Não. O sufixo DNS nas máquinas virtuais na sua rede virtual ligado permanece como o sufixo predefinido de fornecida pelo Azure ("*. internal.cloudapp.net"). Pode alterar manualmente este sufixo DNS nas suas máquinas virtuais para que a zona privada. 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>Existem limitações para zonas privadas durante esta pré-visualização?

Sim. Durante a pré-visualização pública, existem as seguintes limitações.
* Uma rede virtual de registo por zona privada.
* Até 10 redes virtuais de resolução por zona privada.
* R dado ligações de rede virtual a apenas uma zona privada como uma rede virtual de registo.
* R dado ligações de rede virtual para zonas privadas do até 10 como uma rede virtual de resolução.
* Se for especificada uma rede virtual de registo, os registos DNS para as VMs da rede virtual que estão registados para a zona privada não podem ser visualizados ou obtidos a partir do PowerShell, CLI ou APIs. Os registos VM estão registados e resolver com êxito.
* Inversa de DNS de funciona apenas para o espaço IP privados na rede virtual de registo.
* DNS inverso para um IP privado que não está registado na zona privada devolve "internal.cloudapp.net" como o sufixo DNS. Este sufixo não puder ser resolvido. Um exemplo é um IP privado para uma máquina virtual numa rede virtual que é ligada como a uma rede virtual de resolução para uma zona privada.
* Uma rede virtual pode estar vazia quando ele liga pela primeira vez a uma zona privada como uma rede virtual de registo ou resolução. A rede virtual, em seguida, pode estar vazio para a ligação futuras como uma rede virtual de registo ou resolução para outras zonas privadas.
* Reencaminhamento condicional não é suportado, por exemplo, para habilitar a resolução entre redes do Azure e no local. Saiba como os clientes podem obter este cenário através de outros mecanismos. Consulte [resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>Existem quotas ou limites de zonas ou registos para zonas privadas?

Não há nenhum limite no número de zonas permitidas por subscrição para zonas privadas. Não há nenhum limite no número de conjuntos de registos por zona para zonas privadas. Zonas públicas e privadas contam para os limites DNS globais. Para obter mais informações, consulte o [subscrição do Azure e limites do serviço](../azure-subscription-service-limits.md#azure-dns-limits)

### <a name="is-there-portal-support-for-private-zones"></a>Existe suporte para zonas privadas do portal?

Zonas privadas já criadas através de APIs, PowerShell, CLI e SDKs são visíveis no portal do Azure. Mas os clientes não é possível criar novas zonas privadas ou gerir as associações com as redes virtuais. Para as redes virtuais associadas como registo de redes virtuais, registados automaticamente registos VM não visíveis no portal. 

## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais sobre o DNS do Azure](dns-overview.md).
<br>
- [Saiba mais sobre como utilizar o DNS do Azure para domínios privados](private-dns-overview.md).
<br>
- [Saiba mais sobre zonas DNS e registos](dns-zones-records.md).
<br>
- [Introdução ao Azure DNS](dns-getstarted-portal.md).

