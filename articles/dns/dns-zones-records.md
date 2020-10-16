---
title: Visão geral das Zonas e Registos do DNS - Azure DNS
description: Visão geral do suporte para hospedar zonas de DNS e registos no Microsoft Azure DNS.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: rohink
ms.openlocfilehash: 19189af6424960b8e20be686af745b10f2d8578b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85846840"
---
# <a name="overview-of-dns-zones-and-records"></a>Descrição geral de zonas e registos DNS

Esta página explica os conceitos-chave de domínios, zonas dns e registos de DNS e conjuntos de discos, e como são suportados no Azure DNS.

## <a name="domain-names"></a>Nomes de domínio

O Sistema de Nomes de Domínio é uma hierarquia de domínios. A hierarquia começa a partir do domínio “raiz”, cujo nome é simplesmente “**.**”.  Abaixo deste domínio, surgem os domínios de nível superior, como “com”, “net”, “org”, “pt” ou “fr”.  Abaixo destes, estão os domínios de segundo nível, tais como “org.pt” ou “co.uk”. Os domínios na hierarquia do DNS são distribuídos globalmente, hospedados por servidores de nomes DNS em todo o mundo.

Um registrador de nome de domínio é uma organização que lhe permite comprar um nome de domínio, como `contoso.com` .  A compra de um nome de domínio dá-lhe o direito de controlar a hierarquia do DNS com esse nome, permitindo-lhe, por exemplo, direcionar o nome para o `www.contoso.com` site da sua empresa. O registrador pode hospedar o domínio em servidores de nome próprio em seu nome, ou permitir-lhe especificar servidores de nome alternativos.

O Azure DNS fornece uma infraestrutura de servidor de nome de alta disponibilidade distribuída globalmente, que pode usar para hospedar o seu domínio. Ao hospedar os seus domínios em DNS Azure, pode gerir os seus registos DNS com as mesmas credenciais, APIs, ferramentas, faturação e suporte como os seus outros serviços Azure.

A Azure DNS não suporta atualmente a compra de nomes de domínio. Se quiser adquirir um nome de domínio, tem de utilizar um registo de nomes de domínio de terceiros. O registrador normalmente cobra uma pequena taxa anual. Os domínios podem então ser hospedados no Azure DNS para a gestão dos registos DNS. Veja [Delegar um Domínio ao DNS do Azure](dns-domain-delegation.md) para obter detalhes.

## <a name="dns-zones"></a>Zonas DNS

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>Registos dns

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Tempo de vida

O tempo de vida, ou TTL, especifica quanto tempo cada registo é cached por clientes antes de ser requerido. No exemplo acima, o TTL é de 3600 segundos ou 1 hora.

Em Azure DNS, o TTL é especificado para o recorde estabelecido, não para cada disco, pelo que o mesmo valor é usado para todos os registos dentro desse recorde estabelecido.  Pode especificar qualquer valor TTL entre 1 e 2.147.483,647 segundos.

### <a name="wildcard-records"></a>Registos wildcard

O DNS do Azure suporta [registos de carateres universais](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Os registos wildcard são devolvidos em resposta a qualquer consulta com um nome correspondente (a menos que haja uma correspondência mais próxima de um conjunto de recordes não wildcard). O Azure DNS suporta os recordes de wildcard para todos os tipos de discos, exceto NS e SOA.

Para criar um conjunto de recordes wildcard, use o nome de recorde ' \* ' . Em alternativa, também pode usar um nome com \* ' como o seu rótulo mais à esquerda, por exemplo, \* '.foo'.

### <a name="caa-records"></a>Registos da CAA

Os registos da CAA permitem que os proprietários de domínios especifiquem quais as Autoridades certificados (AC) autorizadas a emitir certificados para o seu domínio. Isto permite que os CAs evitem certificados de emissão errada em algumas circunstâncias. Os registos da CAA têm três propriedades:
* **Bandeiras**: Este é um número inteiro entre 0 e 255, usado para representar a bandeira crítica que tem um significado especial pelo [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Tag**: uma cadeia ASCII que pode ser uma das seguintes:
    * **emissão**: use isto se quiser especificar CAs que são autorizados a emitir certificados (todos os tipos)
    * **emitewild**: use isto se quiser especificar CAs que são permitidos emitir certificados (apenas certificados wildcard)
    * **iodef**: especifique um endereço de e-mail ou nome de anfitrião ao qual os CAs podem notificar para pedidos não autorizados de emissão de cert
* **Valor**: o valor para a etiqueta específica escolhida

### <a name="cname-records"></a>Registos da CNAME

Os conjuntos de registos CNAME não podem coexistir com outros conjuntos de registos com o mesmo nome. Por exemplo, não é possível criar um disco CNAME estabelecido com o nome relativo 'www' e um registo A com o nome relativo 'www' ao mesmo tempo.

Como o ápice da zona (nome = ' \@ ') contém sempre os conjuntos de registo NS e SOA que foram criados quando a zona foi criada, não é possível criar um recorde CNAME estabelecido no ápice da zona.

Estes constrangimentos surgem das normas DNS e não são limitações do Azure DNS.

### <a name="ns-records"></a>Registos NS

O registo NS estabelecido no ápice da zona (nome ' \@ ' ) é criado automaticamente com cada zona de DNS, e é eliminado automaticamente quando a zona é eliminada (não pode ser eliminada separadamente).

Este conjunto de registos contém os nomes dos servidores de nomeS DNS Azure atribuídos à zona. Pode adicionar servidores de nome adicionais a este conjunto de registos NS, para suportar domínios de co-hospedagem com mais de um fornecedor de DNS. Também pode modificar o TTL e metadados para este conjunto de registos. No entanto, não é possível remover ou modificar os servidores de nomeS DNS pré-povoados. 

Isto aplica-se apenas ao recorde de NS estabelecido no ápice da zona. Outros conjuntos de registos NS na sua zona (como usado para delegar zonas infantis) podem ser criados, modificados e eliminados sem restrições.

### <a name="soa-records"></a>Registos soa

Um conjunto de registos SOA é criado automaticamente no ápice de cada zona (nome = ' \@ ') e é eliminado automaticamente quando a zona é eliminada.  Os registos SOA não podem ser criados ou eliminados separadamente.

Pode modificar todas as propriedades do registo SOA, exceto para a propriedade 'host', que está pré-configurada para se referir ao nome do servidor de nome principal fornecido pelo Azure DNS.

O número de série da zona no registo SOA não é atualizado automaticamente quando são efetuadas alterações aos registos na zona. Pode ser atualizado manualmente editando o registo SOA, se necessário.

### <a name="spf-records"></a>Registos SPF

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>Registos SRV

[Os registos SRV](https://en.wikipedia.org/wiki/SRV_record) são utilizados por vários serviços para especificar as localizações do servidor. Ao especificar um registo SRV em Azure DNS:

* O *serviço* e *o protocolo* devem ser especificados como parte do nome de conjunto de registos, prefixado com sublinhados.  Por exemplo, \_ 'gole. \_ tcp.name.'  Para um registo no ápice da zona, não há necessidade de especificar \@ ' 'no nome do registo, basta usar o serviço e o protocolo, por exemplo ' \_ gole. \_ tcp'.
* A *prioridade*, *peso,* *porta*e *alvo* são especificados como parâmetros de cada registo no conjunto de recordes.

### <a name="txt-records"></a>Registos TXT

Os registos TXT são usados para mapear nomes de domínio para cadeias de texto arbitrárias. São utilizados em múltiplas aplicações, nomeadamente relacionadas com a configuração de e-mail, tais como o [Quadro de Política de Remetente (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) e o Correio Identificado de [DomainKeys (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

As normas DNS permitem que um único registo TXT contenha várias cordas, cada uma das quais pode ter até 254 caracteres de comprimento. Onde várias cordas são usadas, são concatenadas pelos clientes e tratadas como uma única corda.

Ao ligar para a API Azure DNS REST, tem de especificar cada fio TXT separadamente.  Ao utilizar as interfaces do portal Azure, PowerShell ou CLI deverá especificar uma única cadeia por registo, que é automaticamente dividida em segmentos de 254 caracteres, se necessário.

As múltiplas cordas num registo DNS não devem ser confundidas com os múltiplos registos TXT num conjunto de registos TXT.  Um conjunto de registos TXT pode conter vários registos, *cada um dos quais* pode conter várias cordas.  O Azure DNS suporta um comprimento total de cadeia de até 1024 caracteres em cada conjunto de registos TXT (em todos os registos combinados).

## <a name="tags-and-metadata"></a>Etiquetas e metadados

### <a name="tags"></a>Etiquetas

As etiquetas são uma lista de pares de valor-nome e são usadas pela Azure Resource Manager para rotular recursos.  O Azure Resource Manager utiliza tags para permitir visualizações filtradas da sua conta Azure, e também permite definir uma política sobre as etiquetas necessárias. Para obter mais informações sobre etiquetas, consulte [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md).

O Azure DNS suporta a utilização de tags Azure Resource Manager nos recursos da zona DNS.  Não suporta tags em conjuntos de registos DNS, embora como um 'metadados' alternativo seja suportado em conjuntos de registos DNS como explicado abaixo.

### <a name="metadata"></a>Metadados

Como alternativa a registar etiquetas definidas, o Azure DNS suporta a anotação de conjuntos de recordes utilizando 'metadados'.  Semelhante às tags, os metadados permitem associar pares de valor-nome a cada conjunto de registos.  Isto pode ser útil, por exemplo, para registar o propósito de cada conjunto de discos.  Ao contrário das etiquetas, os metadados não podem ser utilizados para fornecer uma visão filtrada da sua conta Azure e não podem ser especificados numa política do Gestor de Recursos Azure.

## <a name="etags"></a>Etags

Suponha que duas pessoas ou dois processos tentem modificar um registo de DNS ao mesmo tempo. Qual ganha? E o vencedor sabe que eles sobreesceram mudanças criadas por outra pessoa?

O Azure DNS utiliza Etags para lidar com alterações simultâneas no mesmo recurso com segurança. As etiquetas Etags são separadas das ['Tags' do Gestor de Recursos Azure.](#tags) Cada recurso DNS (zona ou conjunto de registos) tem um Etag associado a ele. Sempre que um recurso é recuperado, o seu Etag também é recuperado. Ao atualizar um recurso, pode optar por repercutir o Etag para que o Azure DNS possa verificar se o Etag no servidor corresponde. Uma vez que cada atualização a um recurso resulta na regeneração do Etag, um desfasamento do Etag indica que ocorreu uma alteração simultânea. Os Etags também podem ser utilizados ao criar um novo recurso para garantir que o recurso já não existe.

Por predefinição, o Azure DNS PowerShell utiliza Etags para bloquear alterações simultâneas em zonas e conjuntos de registos. O interruptor *opcional -Overwrite* pode ser utilizado para suprimir os controlos Etag, caso em que quaisquer alterações simultâneas que ocorreram são substituídas.

Ao nível da Azure DNS REST API, os Etags são especificados utilizando cabeçalhos HTTP.  O seu comportamento é dado na seguinte tabela:

| Cabeçalho | Comportamento |
| --- | --- |
| Nenhum |O PUT tem sempre sucesso (sem controlos Etag) |
| Se-jogo \<etag> |PUT só tem sucesso se o recurso existir e etag corresponder |
| Se-match * |PUT só tem sucesso se o recurso existir |
| Se-nenhum-match * |PUT só tem sucesso se o recurso não existir |


## <a name="limits"></a>Limites

Os seguintes limites por defeito aplicam-se quando se utiliza O DNS do Azure:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Passos seguintes

* Para começar a utilizar o Azure DNS, aprenda a [criar uma zona DE DNS](dns-getstarted-create-dnszone-portal.md) e crie [registos DNS](dns-getstarted-create-recordset-portal.md).
* Para migrar uma zona de DNS existente, aprenda a [importar e exportar um ficheiro de zona DNS](dns-import-export.md).
