---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: f7fa018225d82e2db4a3c51b333aea6fcb9a1514
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160943"
---
### <a name="record-names"></a>Nomes de registo

No DNS do Azure, os registos são especificados com nomes relativos. Um nome de domínio *completamente qualificado* (FQDN) inclui o nome da zona, enquanto um nome *relativo* não. Por exemplo, o nome do registo relativo “www” na zona “contoso.com” fornece o nome do registo completamente qualificado “www.contoso.com”.

Um registo *apex* é um registo DNS na raiz (ou *apex*) de uma zona DNS. Por exemplo, o zona DNS "contoso.com", um registo de apex também tem o nome totalmente qualificado "contoso.com" (por vezes denominado um domínio *sem prefixo*).  Por convenção, o nome relativo "\@" é usado para representar registos apex.

### <a name="record-types"></a>Tipos de registo

Cada registo DNS tem um nome e um tipo. Os registos são organizados em vários tipos de acordo com os dados que contêm. O tipo mais comum é um registo “A”, que mapeia um nome para um endereço IPv4. Outro tipo comum é um registo “MX”, que mapeia um nome para um servidor de correio.

O DNS do Azure suporta todos os tipos de registo DNS comuns: A, AAAA, CAA, CNAME, MX, NS, PTR, SOA, SRV, and TXT. Tenha em atenção que [os registos SPF são representados utilizando registos TXT](../articles/dns/dns-zones-records.md#spf-records).

### <a name="record-sets"></a>Conjuntos de registos

Por vezes, precisa de criar mais do que um registo DNS com um determinado nome e tipo. Por exemplo, suponha que o site “www.contoso.com” está alojado em dois endereços IP diferentes. O site necessita de dois registos A diferentes, um para cada endereço IP. Aqui está um exemplo de um conjunto de registos:

    www.contoso.com.        3600    IN    A    134.170.185.46
    www.contoso.com.        3600    IN    A    134.170.188.221

O DNS do Azure gere todos os registos DNS com *conjuntos de registos*. Um conjunto de registos (também conhecido como conjunto de registos de *recurso*) é uma coleção de registos DNS numa zona com o mesmo nome e do mesmo tipo. A maioria dos conjuntos de registos contêm um único registo. No entanto, os exemplos como o apresentado acima, no qual um conjunto um registos contém mais de um registo, não são invulgares.

Por exemplo, imagine que já criou um registo "www" A na zona "contoso.com", a apontar para o endereço IP "134.170.185.46" (o primeiro registo acima).  Para criar o segundo registo, deverá adicionar esse registo para o conjunto de registos existente, em vez de criar um conjunto de registos adicional.

Os tipos de registos SOA e CNAME são exceções. As normas DNS não permitirem vários registos com o mesmo nome para estes tipos, por conseguinte, estes conjuntos de registos só podem conter um único registo.