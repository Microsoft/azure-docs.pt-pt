---
title: Visão geral das Zonas e Registos do DNS - Azure DNS
description: Visão geral do suporte para hospedar zonas de DNS e registos no Microsoft Azure DNS.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 04/20/2021
ms.author: rohink
ms.openlocfilehash: 26eefe5cbcef417dee1a400b492ee847394ca6a9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816968"
---
# <a name="overview-of-dns-zones-and-records"></a>Descrição geral de zonas e registos DNS

Este artigo explica os conceitos-chave de domínios, zonas dns, registos dns e conjuntos de discos. Vais aprender como é apoiado no Azure DNS.

## <a name="domain-names"></a>Nomes de domínio

O Sistema de Nomes de Domínio é uma hierarquia de domínios. A hierarquia começa a partir do domínio “raiz”, cujo nome é simplesmente “**.**”.  Abaixo deste domínio, surgem os domínios de nível superior, como “com”, “net”, “org”, “pt” ou “fr”.  Abaixo dos domínios de nível superior encontram-se domínios de segundo nível, tais como "org.uk" ou "co.jp". Os domínios na hierarquia do DNS são distribuídos globalmente, hospedados por servidores de nomes DNS em todo o mundo.

Um registrador de nome de domínio é uma organização que lhe permite comprar um nome de domínio, como `contoso.com` .  A compra de um nome de domínio dá-lhe o direito de controlar a hierarquia do DNS com esse nome, permitindo-lhe, por exemplo, direcionar o nome para o `www.contoso.com` site da sua empresa. O registrador pode hospedar o domínio em servidores de nome próprio em seu nome, ou permitir-lhe especificar servidores de nome alternativos.

O Azure DNS fornece uma infraestrutura de servidor de nomes distribuído globalmente e de alta disponibilidade que pode utilizar para hospedar o seu domínio. Ao hospedar os seus domínios em DNS Azure, pode gerir os seus registos DNS com as mesmas credenciais, APIs, ferramentas, faturação e suporte como os seus outros serviços Azure.

Atualmente, o Azure DNS não suporta a compra de nomes de domínio. Se quiser adquirir um nome de domínio, tem de utilizar um registo de nomes de domínio de terceiros. O registrador normalmente cobra uma pequena taxa anual. Os domínios podem então ser hospedados no Azure DNS para a gestão dos registos DNS. Veja [Delegar um Domínio ao DNS do Azure](dns-domain-delegation.md) para obter detalhes.

## <a name="dns-zones"></a>Zonas DNS

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>Registos dns

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Tempo de vida

O tempo de vida, ou TTL, especifica quanto tempo cada registo é cached por clientes antes de ser requerido. No exemplo acima, o TTL é de 3600 segundos ou 1 hora.

Em Azure DNS, o TTL é especificado para o recorde estabelecido, não para cada disco, pelo que o mesmo valor é usado para todos os registos dentro desse recorde estabelecido.  Pode especificar qualquer valor TTL entre 1 e 2.147.483,647 segundos.

### <a name="wildcard-records"></a>Registos wildcard

O DNS do Azure suporta [registos de carateres universais](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Os registos wildcard são devolvidos em resposta a qualquer consulta com um nome correspondente, a menos que haja uma correspondência mais próxima de um conjunto de recordes não wildcard. O Azure DNS suporta os recordes de wildcard para todos os tipos de discos, exceto NS e SOA.

Para criar um conjunto de recordes wildcard, use o nome de recorde ' \* ' . Também pode usar um nome com \* ' ' como o seu rótulo mais à esquerda, por exemplo, \* '.foo'.

### <a name="caa-records"></a>Registos da CAA

Os registos da CAA permitem que os proprietários de domínios especifiquem quais as Autoridades certificados (AC) autorizadas a emitir certificados para o seu domínio. Este registo permite aos CA evitar certificados de emissão errada em algumas circunstâncias. Os registos da CAA têm três propriedades:
* **Bandeiras**: Este campo é um número inteiro entre 0 e 255, usado para representar a bandeira crítica que tem um significado especial pelo [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Tag**: uma cadeia ASCII que pode ser uma das seguintes:
    * **emissão**: se quiser especificar CAs que são autorizados a emitir certificados (todos os tipos)
    * **emitewild**: se quiser especificar CAs que são autorizados a emitir certificados (apenas certificados wildcard)
    * **iodef**: especifique um endereço de e-mail ou nome de anfitrião ao qual os CAs podem notificar para pedidos não autorizados de emissão de cert
* **Valor**: o valor para a etiqueta específica escolhida

### <a name="cname-records"></a>Registos da CNAME

Os recordes da CNAME não podem coexistir com outros conjuntos de discos com o mesmo nome. Por exemplo, não é possível criar um disco CNAME estabelecido com o nome relativo 'www' e um registo A com o nome relativo 'www' ao mesmo tempo.

Uma vez que o ápice da zona (nome = ' \@ ') sempre conterá os conjuntos de recordes NS e SOA durante a criação da zona, não é possível criar um recorde CNAME estabelecido no ápice da zona.

Estas restrições derivam das normas DNS e não são limitações do DNS do Azure.

### <a name="ns-records"></a>Registos NS

O recorde NS estabelecido no ápice da zona (nome ' \@ ') é criado automaticamente com cada zona de DNS, e é apagado automaticamente quando a zona é eliminada. Não pode ser apagado separadamente.

Este conjunto de registos contém os nomes dos servidores de nomeS DNS Azure atribuídos à zona. Pode adicionar mais servidores de nomes a este conjunto de registos NS, para suportar domínios de co-ionsting com mais de um fornecedor de DNS. Também pode modificar o TTL e metadados para este conjunto de registos. No entanto, não é permitido remover ou modificar os servidores de nomes Azure DNS pré-povoados. 

Esta restrição aplica-se apenas ao recorde NS estabelecido no ápice da zona. Outros conjuntos de registos NS na sua zona (como usado para delegar zonas infantis) podem ser criados, modificados e eliminados sem restrições.

### <a name="soa-records"></a>Registos soa

Um conjunto de registos SOA é criado automaticamente no ápice de cada zona (nome = ' \@ ') e é apagado automaticamente quando a zona é eliminada.  Os registos SOA não podem ser criados ou eliminados separadamente.

Pode modificar todas as propriedades do registo SOA, exceto para a propriedade 'anfitrião'. Esta propriedade é pré-configurada para se referir ao nome do servidor de nome primário fornecido por Azure DNS.

O número de série da zona no registo SOA não é atualizado automaticamente quando são feitas alterações aos registos na zona. Pode ser atualizado manualmente editando o registo SOA, se necessário.

### <a name="spf-records"></a>Registos SPF

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>Registos SRV

[Os registos SRV](https://en.wikipedia.org/wiki/SRV_record) são utilizados por vários serviços para especificar as localizações do servidor. Ao especificar um registo SRV em Azure DNS:

* O *serviço* e *o protocolo* devem ser especificados como parte do nome de conjunto de registos, prefixado com sublinhados.  Por exemplo, \_ 'gole. \_ tcp.name.'  Para um registo no ápice da zona, não há necessidade de especificar \@ ' 'no nome do registo, basta usar o serviço e o protocolo, por exemplo ' \_ gole. \_ tcp'.
* A *prioridade*, *peso,* *porta* e *alvo* são especificados como parâmetros de cada registo no conjunto de recordes.

### <a name="txt-records"></a>Registos TXT

Os registos TXT são usados para mapear nomes de domínio para cadeias de texto arbitrárias. São utilizados em múltiplas aplicações, em particular relacionadas com a configuração de e-mail, tais como o [Quadro de Política de Remetente (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) e o Correio Identificado de [DomainKeys (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

As normas DNS permitem que um único registo TXT contenha várias cordas, cada uma das quais pode ter até 254 caracteres de comprimento. Onde várias cordas são usadas, são concatenadas pelos clientes e tratadas como uma única corda.

Ao ligar para a API Azure DNS REST, tem de especificar cada fio TXT separadamente.  Ao utilizar as interfaces do portal Azure, PowerShell ou CLI deverá especificar uma única cadeia por registo, que é automaticamente dividida em segmentos de 254 caracteres, se necessário.

As múltiplas cordas num registo DNS não devem ser confundidas com os múltiplos registos TXT num conjunto de registos TXT.  Um conjunto de registos TXT pode conter vários registos, *cada um dos quais* pode conter várias cordas.  O Azure DNS suporta um comprimento total de cadeia de até 1024 caracteres em cada conjunto de registos TXT (em todos os registos combinados).

## <a name="tags-and-metadata"></a>Etiquetas e metadados

### <a name="tags"></a>Etiquetas

As etiquetas são uma lista de pares de valor-nome e são usadas pela Azure Resource Manager para rotular recursos. O Azure Resource Manager utiliza tags para permitir visualizações filtradas da sua conta Azure e também permite definir uma política para determinadas etiquetas. Para obter mais informações sobre etiquetas, consulte [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md).

O Azure DNS suporta a utilização de tags Azure Resource Manager nos recursos da zona DNS.  Não suporta tags em conjuntos de registos DNS, embora como um 'metadados' alternativo seja suportado em conjuntos de registos DNS como explicado abaixo.

### <a name="metadata"></a>Metadados

Como alternativa a registar etiquetas definidas, o Azure DNS suporta a anotação de conjuntos de recordes utilizando 'metadados'.  Semelhante às tags, os metadados permitem associar pares de valor-nome a cada conjunto de registos.  Esta funcionalidade pode ser útil, por exemplo, para registar o propósito de cada conjunto de discos.  Ao contrário das etiquetas, os metadados não podem ser utilizados para fornecer uma visão filtrada da sua conta Azure e não podem ser especificados numa política do Gestor de Recursos Azure.

## <a name="etags"></a>Etags

Suponha que duas pessoas ou dois processos tentem modificar um registo de DNS ao mesmo tempo. Qual ganha? E o vencedor sabe que eles sobreesceram mudanças criadas por outra pessoa?

O Azure DNS utiliza Etags para lidar com alterações simultâneas no mesmo recurso com segurança. As etiquetas Etags são separadas das ['Tags' do Gestor de Recursos Azure.](#tags) Cada recurso DNS (zona ou conjunto de registos) tem um Etag associado a ele. Sempre que um recurso é recuperado, o seu Etag também é recuperado. Ao atualizar um recurso, pode optar por repercutir o Etag para que o Azure DNS possa verificar o Etag nas correspondências do servidor. Uma vez que cada atualização a um recurso resulta na regeneração do Etag, um desfasamento do Etag indica que ocorreu uma alteração simultânea. Os Etags também podem ser usados ao criar um novo recurso para garantir que o recurso já não existe.

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

* Para começar a utilizar o Azure DNS, aprenda a [criar uma zona DE DNS](./dns-getstarted-portal.md) e crie [registos DNS](./dns-getstarted-portal.md).
* Para migrar uma zona de DNS existente, aprenda a [importar e exportar um ficheiro de zona DNS](dns-import-export.md).