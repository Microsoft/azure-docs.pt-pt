---
title: Visão geral de Zonas e Registos DNS - Azure DNS
description: Visão geral do suporte para hospedar zonas dNS e registos em DNS Microsoft Azure.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: rohink
ms.openlocfilehash: 19189af6424960b8e20be686af745b10f2d8578b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79265158"
---
# <a name="overview-of-dns-zones-and-records"></a>Visão geral das zonas e registos do DNS

Esta página explica os conceitos-chave de domínios, zonas DNS e registos dNS e conjuntos de registos, e como são suportados em DNS Azure.

## <a name="domain-names"></a>Nomes de domínio

O Sistema de Nomes de Domínio é uma hierarquia de domínios. A hierarquia começa a partir do domínio “raiz”, cujo nome é simplesmente “**.**”.  Abaixo deste domínio, surgem os domínios de nível superior, como “com”, “net”, “org”, “pt” ou “fr”.  Abaixo destes, estão os domínios de segundo nível, tais como “org.pt” ou “co.uk”. Os domínios na hierarquia do DNS são distribuídos globalmente, hospedados por servidores de nome DNS em todo o mundo.

Um registo de nome de domínio é uma organização que `contoso.com`lhe permite adquirir um nome de domínio, como .  A aquisição de um nome de domínio dá-lhe o direito de controlar a hierarquia `www.contoso.com` do DNS com esse nome, permitindo por exemplo direcionar o nome para o site da sua empresa. O registrador pode hospedar o domínio em seu próprio nome servidores em seu nome, ou permitir-lhe especificar servidores de nome alternativos.

O Azure DNS fornece uma infraestrutura de servidor de nome distribuído globalmente e de alta disponibilidade, que pode usar para hospedar o seu domínio. Ao hospedar os seus domínios em DNS Azure, pode gerir os seus registos DNS com as mesmas credenciais, APIs, ferramentas, faturação e suporte como os seus outros serviços Azure.

O Azure DNS não suporta atualmente a compra de nomes de domínio. Se quiser comprar um nome de domínio, tem de utilizar um registo de nome de domínio de terceiros. O registrador normalmente cobra uma pequena taxa anual. Os domínios podem então ser hospedados em DNS Azure para gestão de registos DNS. Veja [Delegar um Domínio ao DNS do Azure](dns-domain-delegation.md) para obter detalhes.

## <a name="dns-zones"></a>Zonas DNS

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>Registos dNS

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Tempo de viver

O tempo de viver, ou TTL, especifica quanto tempo cada registo é cached pelos clientes antes de ser requisido. No exemplo acima, o TTL é de 3600 segundos ou 1 hora.

No Azure DNS, o TTL é especificado para o conjunto de registos, não para cada registo, pelo que o mesmo valor é utilizado para todos os registos dentro desse conjunto de registos.  Pode especificar qualquer valor TTL entre 1 e 2.147.483.647 segundos.

### <a name="wildcard-records"></a>Registos wildcard

O DNS do Azure suporta [registos de carateres universais](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Os registos wildcard são devolvidos em resposta a qualquer consulta com um nome correspondente (a menos que haja uma correspondência mais próxima de um conjunto de recordes não-wildcard). O Azure DNS suporta séries de recordes wildcard para todos os tipos de discos, exceto NS e SOA.

Para criar um conjunto de registos\*wildcard, use o nome do conjunto de discos ' '. Em alternativa, também pode utilizar\*um nome com " como o\*seu rótulo mais à esquerda, por exemplo, ' .foo'.

### <a name="caa-records"></a>Registos da CAA

Os registos caa permitem que os proprietários de domínios especifiquem quais as autoridades de certificados (A) que estão autorizadas a emitir certificados para o seu domínio. Isto permite que os CA evitem a emissão incerta de certificados em algumas circunstâncias. Os registos da CAA têm três propriedades:
* **Bandeiras**: Este é um inteiro entre 0 e 255, usado para representar a bandeira crítica que tem um significado especial por [rfc](https://tools.ietf.org/html/rfc6844#section-3)
* **Etiqueta**: uma corda ASCII que pode ser uma das seguintes:
    * **problema**: utilize-o se pretender especificar os AA que são autorizados a emitir certs (todos os tipos)
    * **issuewild**: use isto se quiser especificar OS que são autorizados a emitir certs (apenas certificados wildcard)
    * **iodef**: especificar um endereço de e-mail ou nome de anfitrião ao qual os CAs podem notificar para pedidos de emissão cert não autorizados
* **Valor**: o valor da etiqueta específica escolhida

### <a name="cname-records"></a>Registos CNAME

Os conjuntos de registos CNAME não podem coexistir com outros conjuntos de registos com o mesmo nome. Por exemplo, não é possível criar um disco CNAME com o nome relativo 'www' e um registo A com o nome relativo 'www' ao mesmo tempo.

Como o ápice da\@zona (nome = ' ' contém sempre os conjuntos de registos NS e SOA que foram criados quando a zona foi criada, não se pode criar um registo CNAME estabelecido no ápice da zona.

Estes constrangimentos decorrem das normas DNS e não são limitações do DNS Azure.

### <a name="ns-records"></a>Registos de NS

O registo NS estabelecido no ápice da zona (nome ')\@é criado automaticamente com cada zona DNS, e é eliminado automaticamente quando a zona é eliminada (não pode ser eliminada separadamente).

Este conjunto de registos contém os nomes dos servidores de nome SNS Azure atribuídos à zona. Pode adicionar servidores de nome adicionais a este conjunto de registos NS, para suportar domínios de co-hospedagem com mais de um fornecedor DNS. Também pode modificar o TTL e os metadados para este conjunto de registos. No entanto, não é possível remover ou modificar os servidores de nome DNS Do DNS do Azure pré-povoados. 

Isto aplica-se apenas ao registo nS estabelecido no ápice da zona. Outros conjuntos de registos de NS na sua zona (como usado para delegar zonas infantis) podem ser criados, modificados e eliminados sem restrições.

### <a name="soa-records"></a>Registos SOA

Um conjunto de registos SOA é criado automaticamente no\@ápice de cada zona (nome = ' '), e é eliminado automaticamente quando a zona é eliminada.  Os registos SOA não podem ser criados ou eliminados separadamente.

Pode modificar todas as propriedades do registo SOA, exceto a propriedade 'host', que está pré-configurada para se referir ao nome principal do servidor fornecido pelo Azure DNS.

O número de série da zona no registo SOA não é atualizado automaticamente quando são feitas alterações nos registos da zona. Pode ser atualizado manualmente editando o registo SOA, se necessário.

### <a name="spf-records"></a>Registos SPF

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>Registos SRV

[Os registos SRV](https://en.wikipedia.org/wiki/SRV_record) são utilizados por vários serviços para especificar a localização do servidor. Ao especificar um registo SRV em DNS Azure:

* O *serviço* e o *protocolo* devem ser especificados como parte do nome do conjunto de registos, pré-fixado com sublinhados.  Por exemplo,\_'gole. \_tcp.name'.  Para um registo no ápice da zona,\@não há necessidade de especificar ' no nome\_de registo, basta utilizar o serviço e o protocolo, por exemplo ' gole . \_tcp'.
* A *prioridade*, *peso,* *porta*e *alvo* são especificados como parâmetros de cada recorde no recorde estabelecido.

### <a name="txt-records"></a>Registos TXT

Os registos TXT são usados para mapear nomes de domínio para cordas de texto arbitrárias. São utilizados em múltiplas aplicações, nomeadamente relacionadas com a configuração de e-mails, tais como o Quadro de Política de [Remetente (FpSe)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) e [o DomainKeys Identified Mail (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

As normas DNS permitem que um único disco TXT contenha várias cordas, cada uma das quais pode ter até 254 caracteres de comprimento. Quando várias cordas são usadas, são concatenadas pelos clientes e tratadas como uma única corda.

Ao ligar para a API de REPOUSO DNS Azure, tem de especificar cada corda TXT separadamente.  Ao utilizar as interfaces do portal Azure, PowerShell ou CLI deve especificar uma única cadeia por registo, que é automaticamente dividida em segmentos de 254 caracteres, se necessário.

As múltiplas cordas num disco DNS não devem ser confundidas com os múltiplos registos TXT num conjunto de registos TXT.  Um conjunto de registos TXT pode conter vários registos, *cada um dos quais* pode conter várias cordas.  O Azure DNS suporta um comprimento total de cadeia de até 1024 caracteres em cada conjunto de registos TXT (em todos os registos combinados).

## <a name="tags-and-metadata"></a>Etiquetas e metadados

### <a name="tags"></a>Etiquetas

As etiquetas são uma lista de pares de valor de nome e são usadas pelo Azure Resource Manager para rotular recursos.  O Azure Resource Manager utiliza tags para permitir visualizações filtradas da sua conta Azure, e também permite definir uma política sobre as etiquetas necessárias. Para obter mais informações sobre etiquetas, consulte [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md).

O Azure DNS suporta a utilização de tags do Gestor de Recursos Azure nos recursos da zona DNS.  Não suporta etiquetas em conjuntos de registos DNS, embora como um "metadados" alternativo seja suportado em conjuntos de registos DNS, como explicado abaixo.

### <a name="metadata"></a>Metadados

Como alternativa às etiquetas de recorde, o Azure DNS suporta anotar séries de registos utilizando 'metadados'.  Semelhante satisfaça as etiquetas, os metadados permitem associar pares de valor de nome a cada conjunto de registos.  Isto pode ser útil, por exemplo, para registar o propósito de cada conjunto de registos.  Ao contrário das etiquetas, os metadados não podem ser utilizados para fornecer uma visão filtrada da sua conta Azure e não podem ser especificados numa política do Gestor de Recursos Do Azure.

## <a name="etags"></a>Etags

Suponha que duas pessoas ou dois processos tentem modificar um registo de DNS ao mesmo tempo. Qual ganha? E o vencedor sabe que sobreescreveu as mudanças criadas por outra pessoa?

O Azure DNS utiliza etags para lidar com alterações simultâneas no mesmo recurso com segurança. Os etags são separados das ['Tags' do Gestor de Recursos Azure.](#tags) Cada recurso DNS (zona ou conjunto de registos) tem um Etag associado a ele. Sempre que um recurso é recuperado, o seu Etag também é recuperado. Ao atualizar um recurso, pode optar por reperver o Etag para que o Azure DNS possa verificar se o Etag no servidor corresponde. Uma vez que cada atualização a um recurso resulta na regeneração do Etag, uma incompatibilidade de Etag indica uma mudança simultânea. Os etags também podem ser usados na criação de um novo recurso para garantir que o recurso já não existe.

Por padrão, o Azure DNS PowerShell utiliza Etags para bloquear alterações simultâneas em zonas e recordes. O interruptor opcional *-Overwrite* pode ser usado para suprimir as verificações de Etag, caso em que quaisquer alterações simultâneas que ocorreram são substituídas.

Ao nível da API de REPOUSO DNS Azure, os Etags são especificados utilizando cabeçalhos HTTP.  O seu comportamento é dado na seguinte tabela:

| Cabeçalho | Comportamento |
| --- | --- |
| Nenhuma |PUT sempre tem sucesso (sem verificações de Etag) |
| Eveado \<se-jogo> |PUT só tem sucesso se o recurso existir e o Etag corresponder |
| Se-jogo * |PUT só tem sucesso se o recurso existir |
| Se-nenhum-fósforo * |PUT só tem sucesso se o recurso não existir |


## <a name="limits"></a>Limites

Aplicam-se os seguintes limites predefinidos ao utilizar o Azure DNS:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Passos seguintes

* Para começar a utilizar o Azure DNS, aprenda a [criar uma zona DNS](dns-getstarted-create-dnszone-portal.md) e [crie registos DNS](dns-getstarted-create-recordset-portal.md).
* Para migrar uma zona DNS existente, aprenda a importar e exportar um ficheiro de [zona DNS](dns-import-export.md).
