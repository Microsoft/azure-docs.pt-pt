---
title: Zonas e registos DNS descrição geral - DNS do Azure | Documentos da Microsoft
description: Descrição geral do suporte para alojar zonas DNS e registos no DNS do Azure de Microsoft.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: victorh
ms.openlocfilehash: 2b9c8f1bb7407dd36623fd8ad68f9489172a1caf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64712235"
---
# <a name="overview-of-dns-zones-and-records"></a>Descrição geral das zonas e registos DNS

Esta página explica os conceitos chave dos domínios, zonas DNS e registos DNS e conjuntos de registos e como eles são suportados no DNS do Azure.

## <a name="domain-names"></a>Nomes de domínio

O Sistema de Nomes de Domínio é uma hierarquia de domínios. A hierarquia começa a partir do domínio “raiz”, cujo nome é simplesmente “ **.** ”.  Abaixo deste domínio, surgem os domínios de nível superior, como “com”, “net”, “org”, “pt” ou “fr”.  Abaixo destes, estão os domínios de segundo nível, tais como “org.pt” ou “co.uk”. Os domínios na hierarquia de DNS são distribuídos globalmente, alojadas por servidores de nome DNS em todo o mundo.

Uma entidade de registo de nome de domínio é uma organização que permite-lhe comprar um nome de domínio, como "contoso.com".  Comprar um nome de domínio dá-lhe o direito de controlar a hierarquia DNS com esse nome, por exemplo permitindo-lhe direcionar o nome "www.contoso.com" para o web site da sua empresa. A entidade de registo pode alojar o domínio no seus próprio servidores de nomes em seu nome ou permitem-lhe especificar os servidores de nome alternativo.

O DNS do Azure fornece uma infraestrutura de servidor de nome de elevada disponibilidade e distribuída globalmente, que pode utilizar para alojar o seu domínio. Ao alojar os seus domínios no DNS do Azure, pode gerir os registos DNS com as mesmas credenciais, APIs, ferramentas, faturação e suporte dos outros serviços do Azure.

O DNS do Azure não suporta atualmente a compra de nomes de domínio. Se deseja comprar um nome de domínio, terá de utilizar uma entidade de registo de nome de domínio de terceiros. A entidade de registo, normalmente, os custos de uma pequena taxa anual. Os domínios, em seguida, podem ser hospedados no DNS do Azure para a gestão de registos DNS. Veja [Delegar um Domínio ao DNS do Azure](dns-domain-delegation.md) para obter detalhes.

## <a name="dns-zones"></a>Zonas DNS

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>Registos DNS

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Tempo de vida

O tempo de duração, ou TTL, especifica o tempo em que cada registo é colocado em cache por clientes antes de a ser requeried. No exemplo acima, o TTL é de 3600 segundos ou 1 hora.

No DNS do Azure, o TTL é especificado para o conjunto de registos, não para cada registo, para que o mesmo valor é utilizado para todos os registos nesse conjunto de registos.  Pode especificar qualquer valor TTL entre 1 e 2,147,483,647 segundos.

### <a name="wildcard-records"></a>Registos de carateres universais

O DNS do Azure suporta [registos de carateres universais](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Registos de carateres universais são retornados em resposta a qualquer consulta com um nome correspondente (a menos que exista uma correspondência mais próxima de um conjunto de registos de carateres não universais). O DNS do Azure suporta conjuntos de registos de carateres universais para todos os tipos de registos, exceto NS e SOA.

Para criar um conjunto de registos de carateres universais, utilize o nome do conjunto de registos "\*'. Em alternativa, também pode utilizar um nome com '\*'como a mais à esquerda etiqueta, por exemplo,"\*foo".

### <a name="caa-records"></a>Registos CAA

Registos CAA permitem aos proprietários de domínio especificar quais autoridades de certificação (ACs) têm autorização para emitir certificados para os seus domínios. Isso permite que o ACS para evitar enganássemos emitir certificados em algumas circunstâncias. Registos CAA tem três propriedades:
* **Sinalizadores de**: Este é um número inteiro entre 0 e 255, usado para representar o sinalizador crítico que tem um significado especial pelo [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Etiqueta**: uma cadeia de caracteres ASCII que pode ser um dos seguintes procedimentos:
    * **problema**: Utilize esta opção se pretender especificar os CAs que têm permissão para emitir certificados (todos os tipos)
    * **issuewild**: Utilize esta opção se pretender especificar os CAs que têm permissão para emitir certificados (apenas para certificados de caráter universal)
    * **iodef**: Especifique um endereço de e-mail ou nome de anfitrião ao qual o ACS podem notificar para pedidos de problema do certificado não autorizado
* **Valor**: o valor para a marca específica escolhida

### <a name="cname-records"></a>Registos CNAME

Os conjuntos de registos CNAME não podem coexistir com outros conjuntos de registos com o mesmo nome. Por exemplo, não é possível criar um registo CNAME definido com o nome relativo "www" e um registo com o nome relativo "www" ao mesmo tempo.

Uma vez que o vértice da zona (nome = '\@') contém sempre os registos NS e SOA conjuntos que foram criados quando a zona foi criada, não é possível criar um CNAME conjunto de registos no vértice da zona.

Essas restrições surgirem das normas DNS e não são limitações do DNS do Azure.

### <a name="ns-records"></a>Registos NS

Os NS registram conjunto no vértice da zona (nome "\@") é criado automaticamente com cada zona DNS e é eliminado automaticamente quando a zona é eliminada (não é possível eliminar separadamente).

Este conjunto de registos contém os nomes dos servidores de nome DNS do Azure atribuídos à zona. Pode adicionar servidores para este registo NS definidos, para oferecer suporte a domínios co-hospedagem com mais de um fornecedor DNS de nome adicionais. Também pode modificar o valor de TTL e os metadados para este conjunto de registos. No entanto, não é possível remover ou modificar os servidores de nomes DNS do Azure preenchidos previamente. 

Isto aplica-se apenas para o conjunto no vértice da zona de registos NS. Outros conjuntos de registos NS na sua zona (como utilizado para delegar zonas subordinadas) podem ser criados, modificados e eliminados sem restrição.

### <a name="soa-records"></a>Registos SOA

Um conjunto de registos SOA é criado automaticamente no vértice da cada zona (nome = '\@") e é eliminado automaticamente quando a zona for eliminada.  Registos SOA não não possível criar ou eliminar separadamente.

Pode modificar todas as propriedades do registo SOA, exceto para a propriedade "host", que é pré-configurado para fazer referência ao nome do servidor de nome primário fornecido pelo DNS do Azure.

### <a name="spf-records"></a>Registos SPF

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>Registos SRV

[Os registos SRV](https://en.wikipedia.org/wiki/SRV_record) são utilizados por vários serviços para especificar localizações de servidor. Quando especificar um registo SRV no DNS do Azure:

* O *serviço* e *protocolo* tem de ser especificado como parte do nome do conjunto de registos, o prefixo com carateres de sublinhado.  Por exemplo, "\_sip.\_ TCP.name'.  Para um registo no vértice da zona, não é necessário especificar '\@'no nome do registo, basta usar o serviço e o protocolo, por exemplo'\_sip.\_ TCP ".
* O *prioridade*, *peso*, *porta*, e *destino* são especificadas como parâmetros de cada registo no conjunto de registos.

### <a name="txt-records"></a>Registos TXT

Registos TXT são utilizados para mapear nomes de domínio para cadeias de caracteres de texto arbitrário. São usados em vários aplicativos, especificamente relacionados à configuração de e-mail, tal como o [remetente política Framework (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) e [domainkeys da yahoo! identificado correio (domínio)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

As normas DNS permitem um único registo TXT para conter várias cadeias de caracteres, cada um dos quais pode ser até 254 carateres de comprimento. Em que várias cadeias de caracteres são usadas, são concatenadas pelos clientes e tratados como uma única cadeia de caracteres.

Ao chamar a API de REST do DNS do Azure, tem de especificar cada cadeia de caracteres TXT separadamente.  Ao utilizar o portal do Azure, PowerShell ou CLI interfaces deve especificar uma cadeia única por registro, o que é automaticamente dividido em segmentos de 254 carateres, se necessário.

Várias cadeias de caracteres num registro DNS não devem ser confundidas com os vários registos TXT num conjunto de registos TXT.  Um conjunto de registos TXT pode conter vários registos, *cada um dos quais* pode conter várias cadeias de caracteres.  O DNS do Azure suporta um comprimento de cadeia de caracteres de total de até 1024 carateres em cada registo TXT definir (em todos os registos combinados).

## <a name="tags-and-metadata"></a>As etiquetas e metadados

### <a name="tags"></a>Tags

As etiquetas são uma lista de pares nome-valor e são utilizadas pelo Azure Resource Manager para recursos de etiquetas.  O Azure Resource Manager utiliza etiquetas para permitir exibições filtradas da fatura do Azure e também permite-lhe definir uma política em que as etiquetas são necessárias. Para obter mais informações sobre etiquetas, consulte [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/resource-group-using-tags.md).

O DNS do Azure suporta utilizando etiquetas de Gestor de recursos do Azure nos recursos de zona DNS.  Não suporta etiquetas em conjuntos de registos de DNS, embora como uma alternativa "metadados" é suportada em conjuntos de registos DNS conforme explicado abaixo.

### <a name="metadata"></a>Metadados

Como alternativa às marcas de conjunto de registos, o DNS do Azure suporta anotar os conjuntos de registos com "metadados".  Semelhante às etiquetas, metadados permite-lhe associar pares nome-valor de cada conjunto de registos.  Isso pode ser útil, por exemplo, para a finalidade de cada conjunto de registos de registo.  Ao contrário de etiquetas, os metadados não podem ser utilizado para fornecer uma vista filtrada da fatura do Azure e não não possível especificar numa política do Azure Resource Manager.

## <a name="etags"></a>Etags

Suponha que duas pessoas ou dois processos tentam modificar um registo DNS ao mesmo tempo. Qual vence? E o vencedor sabe que eles já substituídos alterações criadas por outra pessoa?

O DNS do Azure utiliza Etags para processar alterações simultâneas para o mesmo recurso em segurança. Etags são separadas [do Azure Resource Manager "Etiquetas"](#tags). Cada recurso DNS (zona ou conjunto de registos) tem uma Etag associada a ele. Sempre que um recurso é obtido, o respetivo Etag também é recuperado. Ao atualizar um recurso, pode optar por transmitir volta a Etag para que o DNS do Azure pode verificar se a Etag as correspondências de servidor. Uma vez que cada atualização a um recurso resulta em Etag estão a ser regenerada, um erro de correspondência de Etag indica que tiver ocorrido uma alteração em simultâneo. Etags pode também ser utilizadas ao criar um novo recurso para se certificar de que o recurso ainda não existir.

Por predefinição, o PowerShell de DNS do Azure utiliza Etags para bloquear as alterações em simultâneo a zonas e conjuntos de registos. O opcional *-substituir* comutador pode ser utilizado para suprimir as verificações de Etag, caso em que qualquer simultâneas alterações que ocorreram são substituídas.

Ao nível da API de REST do DNS do Azure, Etags são especificadas usando os cabeçalhos HTTP.  O comportamento é fornecido na tabela a seguir:

| Cabeçalho | Comportamento |
| --- | --- |
| Nenhuma |PUT sempre for concluída com êxito (sem verificações de Etag) |
| If-match \<etag> |PUT apenas é bem-sucedida se o recurso existe e Etag corresponder à |
| If-match * |PUT apenas é bem-sucedida se o recurso existe |
| IF-none-match * |PUT apenas é bem-sucedida se o recurso não existe |


## <a name="limits"></a>Limits

Os limites predefinidos seguintes aplicam-se ao utilizar o DNS do Azure:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Passos Seguintes

* Para começar a utilizar o DNS do Azure, saiba como [criar uma zona DNS](dns-getstarted-create-dnszone-portal.md) e [criar registos DNS](dns-getstarted-create-recordset-portal.md).
* Para migrar uma zona DNS existente, saiba como [importar e exportar um ficheiro de zona DNS](dns-import-export.md).
