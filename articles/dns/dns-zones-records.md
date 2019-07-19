---
title: Visão geral de Zonas DNS e registros-DNS do Azure | Microsoft Docs
description: Visão geral do suporte para hospedar zonas e registros DNS no Microsoft Azure DNS.
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
ms.openlocfilehash: 1a62a4d5f06856ca0fe6356ca388047679097e3f
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68004464"
---
# <a name="overview-of-dns-zones-and-records"></a>Visão geral de zonas e registros DNS

Esta página explica os principais conceitos de domínios, zonas DNS e registros DNS e conjuntos de registros e como eles têm suporte no DNS do Azure.

## <a name="domain-names"></a>Nomes de domínio

O Sistema de Nomes de Domínio é uma hierarquia de domínios. A hierarquia começa a partir do domínio “raiz”, cujo nome é simplesmente “ **.** ”.  Abaixo deste domínio, surgem os domínios de nível superior, como “com”, “net”, “org”, “pt” ou “fr”.  Abaixo destes, estão os domínios de segundo nível, tais como “org.pt” ou “co.uk”. Os domínios na hierarquia de DNS são distribuídos globalmente, hospedados por servidores de nomes DNS em todo o mundo.

Um registrador de nome de domínio é uma organização que permite que você compre um nome de domínio, como ' contoso.com '.  A compra de um nome de domínio lhe dá o direito de controlar a hierarquia de DNS com esse nome, por exemplo, permitindo que você direcione o nome ' www.contoso.com ' para o site da sua empresa. O registrador pode hospedar o domínio em seus próprios servidores de nomes em seu nome ou permitir que você especifique servidores de nomes alternativos.

O DNS do Azure fornece uma infraestrutura de servidor de nome de alta disponibilidade globalmente distribuída, que pode ser usada para hospedar seu domínio. Ao hospedar seus domínios no DNS do Azure, você pode gerenciar seus registros DNS com as mesmas credenciais, APIs, ferramentas, cobrança e suporte que os outros serviços do Azure.

Atualmente, o DNS do Azure não dá suporte à compra de nomes de domínio. Se você quiser adquirir um nome de domínio, precisará usar um registrador de nome de domínio de terceiros. O registrador normalmente cobra uma pequena taxa anual. Os domínios podem ser hospedados no DNS do Azure para o gerenciamento de registros DNS. Veja [Delegar um Domínio ao DNS do Azure](dns-domain-delegation.md) para obter detalhes.

## <a name="dns-zones"></a>Zonas DNS

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>Registros DNS

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Vida útil

A vida útil, ou TTL, especifica quanto tempo cada registro é armazenado em cache pelos clientes antes de ser consultado novamente. No exemplo acima, o TTL é 3600 segundos ou 1 hora.

No DNS do Azure, o TTL é especificado para o conjunto de registros, não para cada registro, portanto, o mesmo valor é usado para todos os registros dentro desse conjunto de registros.  Você pode especificar qualquer valor de TTL entre 1 e 2.147.483.647 segundos.

### <a name="wildcard-records"></a>Registros curinga

O DNS do Azure suporta [registos de carateres universais](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Os registros curinga são retornados em resposta a qualquer consulta com um nome correspondente (a menos que haja uma correspondência mais próxima de um conjunto de registros não curinga). O DNS do Azure dá suporte a conjuntos de registros curinga para todos os tipos de registro, exceto NS e SOA.

Para criar um conjunto de registros curinga, use o nome do conjunto\*de registros ' '. Como alternativa, você também pode usar um nome com '\*' como o rótulo mais à esquerda, por exemplo, '\*. foo '.

### <a name="caa-records"></a>Registros de CAA

Os registros do CAA permitem que os proprietários do domínio especifiquem quais CAs (autoridades de certificação) estão autorizadas a emitir certificados para seu domínio. Isso permite que o CAs Evite certificados de emissão incorreta em algumas circunstâncias. Os registros CAA têm três propriedades:
* **Sinalizadores**: Este é um inteiro entre 0 e 255, usado para representar o sinalizador crítico que tem um significado especial de acordo com a [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Marca**: uma cadeia de caracteres ASCII que pode ser uma das seguintes:
    * **problema**: Use esta se desejar especificar as CAS que têm permissão para emitir certificados (todos os tipos)
    * **issuewild**: Use esta se desejar especificar as CAS que têm permissão para emitir certificados (somente certificados curinga)
    * **iodef**: especifique um endereço de email ou nome de host para o qual as CAS podem notificar para solicitações de emissão de certificado não autorizado
* **Valor**: o valor para a marca específica escolhida

### <a name="cname-records"></a>Registros CNAME

Os conjuntos de registos CNAME não podem coexistir com outros conjuntos de registos com o mesmo nome. Por exemplo, você não pode criar um conjunto de registros CNAME com o nome relativo ' www ' e um registro A com o nome relativo ' www ' ao mesmo tempo.

Como o Apex da zona (nome =\@' ') sempre contém os conjuntos de registros NS e soa que foram criados quando a zona foi criada, você não pode criar um conjunto de registros CNAME no Apex da zona.

Essas restrições surgem dos padrões de DNS e não são limitações do DNS do Azure.

### <a name="ns-records"></a>Registros NS

O conjunto de registros ns no Apex da zona (nome\@' ') é criado automaticamente com cada zona DNS e é excluído automaticamente quando a zona é excluída (não pode ser excluída separadamente).

Esse conjunto de registros contém os nomes dos servidores de nome DNS do Azure atribuídos à zona. Você pode adicionar servidores de nomes adicionais a esse conjunto de registros NS para dar suporte a domínios de hospedagem com mais de um provedor de DNS. Você também pode modificar o TTL e os metadados para este conjunto de registros. No entanto, não é possível remover ou modificar os servidores de nomes DNS do Azure populados previamente. 

Isso se aplica somente ao conjunto de registros NS no Apex da zona. Outros conjuntos de registros NS em sua zona (como usados para delegar zonas filhas) podem ser criados, modificados e excluídos sem restrição.

### <a name="soa-records"></a>Registros SOA

Um conjunto de registros SOA é criado automaticamente no Apex de cada zona (nome = '\@') e é excluído automaticamente quando a zona é excluída.  Os registros SOA não podem ser criados ou excluídos separadamente.

Você pode modificar todas as propriedades do registro SOA, exceto a propriedade ' host ', que é pré-configurada para se referir ao nome do servidor de nomes primário fornecido pelo DNS do Azure.

O número de série da zona no registro SOA não é atualizado automaticamente quando são feitas alterações nos registros na zona. Ele pode ser atualizado manualmente editando o registro SOA, se necessário.

### <a name="spf-records"></a>Registros SPF

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>Registros SRV

Os [Registros SRV](https://en.wikipedia.org/wiki/SRV_record) são usados por vários serviços para especificar locais de servidor. Ao especificar um registro SRV no DNS do Azure:

* O *serviço* e o *protocolo* devem ser especificados como parte do nome do conjunto de registros, prefixado com sublinhados.  Por exemplo, '\_SIP.\_ tcp.name '.  Para um registro no Apex da zona, não é necessário especificar '\@' no nome do registro, basta usar o serviço e o protocolo, por exemplo, ' SIP '.\_ \_ TCP '.
* A *prioridade*, o *peso*, a *porta*e o *destino* são especificados como parâmetros de cada registro no conjunto de registros.

### <a name="txt-records"></a>Registros TXT

Os registros TXT são usados para mapear nomes de domínio para cadeias de caracteres de texto arbitrários. Eles são usados em vários aplicativos, especialmente relacionados à configuração de email, como o [SPF (estrutura de política de remetente)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) e o [domínio (DKIM identificated mail)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

Os padrões de DNS permitem que um único registro TXT contenha várias cadeias de caracteres, cada uma das quais pode ter até 254 de comprimento. Onde várias cadeias de caracteres são usadas, elas são concatenadas por clientes e tratadas como uma única cadeia de caracteres.

Ao chamar a API REST do DNS do Azure, você precisa especificar cada cadeia de caracteres TXT separadamente.  Ao usar as interfaces portal do Azure, PowerShell ou CLI, você deve especificar uma única cadeia de caracteres por registro, que é dividida automaticamente em segmentos de 254 caracteres, se necessário.

As várias cadeias de caracteres em um registro DNS não devem ser confundidas com os vários registros TXT em um conjunto de registros TXT.  Um conjunto de registros TXT pode conter vários registros, *cada um deles* pode conter várias cadeias de caracteres.  O DNS do Azure dá suporte a um tamanho de cadeia de caracteres total de até 1024 caracteres em cada conjunto de registros TXT (em todos os registros combinados).

## <a name="tags-and-metadata"></a>Marcas e metadados

### <a name="tags"></a>Tags

As marcas são uma lista de pares nome-valor e são usadas por Azure Resource Manager para rotular recursos.  Azure Resource Manager usa marcas para habilitar exibições filtradas da sua fatura do Azure e também permite que você defina uma política na qual as marcas são necessárias. Para obter mais informações sobre etiquetas, consulte [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/resource-group-using-tags.md).

O DNS do Azure dá suporte ao uso de marcas de Azure Resource Manager em recursos de zona DNS.  Ele não dá suporte a marcas em conjuntos de registros DNS, embora como uma alternativa ' Metadata ' tenha suporte em conjuntos de registros DNS, conforme explicado abaixo.

### <a name="metadata"></a>Metadados

Como uma alternativa às marcas do conjunto de registros, o DNS do Azure dá suporte à anotação de conjuntos de registros usando ' metadados '.  Semelhante às marcas, os metadados permitem que você associe pares de nome-valor a cada conjunto de registros.  Isso pode ser útil, por exemplo, para registrar a finalidade de cada conjunto de registros.  Ao contrário das marcas, os metadados não podem ser usados para fornecer uma exibição filtrada da sua fatura do Azure e não podem ser especificados em uma política de Azure Resource Manager.

## <a name="etags"></a>ETags

Suponha que duas pessoas ou dois processos tentem modificar um registro DNS ao mesmo tempo. Qual deles vence? E o vencedor sabe que eles substituíram as alterações criadas por outra pessoa?

O DNS do Azure usa ETags para manipular alterações simultâneas no mesmo recurso com segurança. As ETags são separadas da [Azure Resource Manager ' tags '](#tags). Cada recurso DNS (zona ou conjunto de registros) tem uma ETag associada a ele. Sempre que um recurso é recuperado, sua ETag também é recuperada. Ao atualizar um recurso, você pode optar por repassar o ETag para que o DNS do Azure possa verificar se o ETag no servidor corresponde. Como cada atualização para um recurso resulta na regeneração da ETag, uma incompatibilidade de eTag indica que ocorreu uma alteração simultânea. As ETags também podem ser usadas ao criar um novo recurso para garantir que o recurso ainda não exista.

Por padrão, o PowerShell do DNS do Azure usa ETags para bloquear alterações simultâneas em zonas e conjuntos de registros. A opção *-overwrite* opcional pode ser usada para suprimir as verificações de eTag; nesse caso, quaisquer alterações simultâneas ocorridas serão substituídas.

No nível da API REST do DNS do Azure, as ETags são especificadas usando cabeçalhos HTTP.  Seu comportamento é fornecido na tabela a seguir:

| Cabeçalho | Comportamento |
| --- | --- |
| Nenhuma |PUT sempre sucede (nenhuma verificação de eTag) |
| If-match \<etag> |PUT só terá sucesso se o recurso existir e a ETag corresponder |
| If-Match * |PUT só terá sucesso se o recurso existir |
| If-None-Match * |PUT só terá sucesso se o recurso não existir |


## <a name="limits"></a>Limites

Os seguintes limites padrão se aplicam ao usar o DNS do Azure:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Passos Seguintes

* Para começar a usar o DNS do Azure, saiba como [criar uma zona DNS](dns-getstarted-create-dnszone-portal.md) e [criar registros DNS](dns-getstarted-create-recordset-portal.md).
* Para migrar uma zona DNS existente, saiba como [importar e exportar um arquivo de zona DNS](dns-import-export.md).
