---
title: Troubleshooting guide - Azure DNS
description: In this learning path, get started troubleshooting common issues with Azure DNS
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 09/20/2019
ms.author: allensu
ms.openlocfilehash: b5fedba7b739c07a37f3aabf75ddd8ca465ba73b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74210943"
---
# <a name="azure-dns-troubleshooting-guide"></a>Azure DNS troubleshooting guide

This article provides troubleshooting information for common Azure DNS questions.

If these steps don't resolve your issue, you can also search for or post your issue on our [community support forum on MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork). Or, you can open an Azure support request.


## <a name="i-cant-create-a-dns-zone"></a>I can't create a DNS zone

Para resolver problemas comuns, experimente um ou mais dos métodos seguintes:

1.  Review the Azure DNS audit logs to determine the failure reason.
2.  Cada nome da zona DNS tem de ser exclusivo dentro do respetivo grupo de recursos. That is, two DNS zones with the same name can't share a resource group. Tente utilizar um nome de zona diferente ou um grupo de recursos diferente.
3.  Pode ver um erro "Atingiu ou excedeu o número máximo de zonas na subscrição {id da subscrição}." Utilize uma subscrição do Azure diferente, elimine algumas zonas ou contacte o suporte do Azure para aumentar o limite de sua subscrição.
4.  Pode ver um erro "A zona "{nome da zona}" não está disponível." Este erro significa que DNS do Azure não conseguiu alocar servidores de nomes para esta zona de DNS. Tente utilizar um nome de zona diferente. Or, if you are the domain name owner you can contact Azure support to allocate name servers for you.


### <a name="recommended-articles"></a>Recommended articles

* [Zonas e registos DNS](dns-zones-records.md)
* [Criar uma zona DNS](dns-getstarted-create-dnszone-portal.md)

## <a name="i-cant-create-a-dns-record"></a>Não consigo criar um registo DNS

Para resolver problemas comuns, experimente um ou mais dos métodos seguintes:

1.  Review the Azure DNS audit logs to determine the failure reason.
2.  O conjunto de registos já existe?  O DNS do Azure gere registos com *conjuntos* de registos, que são a recolha de registos do mesmo nome e do mesmo tipo. Se já existir um registo com o mesmo nome e tipo, para adicionar outro registo deve editar o conjunto de registos existente.
3.  Está a tentar criar um registo no apex de zona DNS (a “raiz” da zona)? Se for o caso, a convenção DNS consiste em utilizar o caráter “@” como o nome do registo. Also note that the DNS standards don't permit CNAME records at the zone apex.
4.  Tem um conflito com o CNAME?  The DNS standards don't allow a CNAME record with the same name as a record of any other type. Se tiver um CNAME existente, a criação de um registo com o mesmo nome de um tipo diferente falha.  Da mesma forma, a criação de um CNAME falha se o nome corresponder a um registo existente de um tipo diferente. Remova o conflito ao remover o outro registo ou ao escolher um nome de registo diferente.
5.  Atingiu o limite do número de conjuntos de registos permitido numa zona DNS? O número atual de conjuntos de registos e o número máximo de conjuntos de registos são apresentados no portal do Azure, em “Propriedades” para a zona. If you've reached this limit, then either delete some record sets or contact Azure Support to raise your record set limit for this zone, then try again. 


### <a name="recommended-articles"></a>Recommended articles

* [Zonas e registos DNS](dns-zones-records.md)
* [Criar uma zona DNS](dns-getstarted-create-dnszone-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>Não consigo resolver o meu registo DNS

A resolução de nomes DNS é um processo com vários passos que pode falhar por muitos motivos. Os passos seguintes ajudam a investigar por que motivo a resolução de DNS está a falhar para um registo DNS numa zona alojada no DNS do Azure.

1.  Confirme que os registos DNS foram configurados corretamente no DNS do Azure. Reveja os registos DNS no portal do Azure, verificando que o nome da zona, o nome do registo e o tipo de registo estão corretos.
2.  Confirme que os registos DNS resolvem corretamente nos servidores de nome do DNS do Azure.
    - Se fizer consultas de DNS a partir do seu PC local, poderá ver os resultados em cache que não refletem o estado atual dos servidores de nome.  Além disso, as redes empresariais utilizam frequentemente servidores de proxy do DNS que impedem que as consultas de DNS sejam direcionadas para servidores de nomes específicos.  Para evitar estes problemas, utilize um serviço de resolução de nome baseado na Web, tal como [digwebinterface](https://digwebinterface.com).
    - Confirme que especifica os servidores de nome corretos para a zona DNS — estes são apresentados no Portal do Azure.
    - Verifique se o nome DNS está correto (tem de especificar o nome totalmente qualificado, incluindo o nome da zona) e se o tipo de registo está correto
3.  Confirme que o nome de domínio DNS foi corretamente [delegado para os servidores de nomes DNS do Azure](dns-domain-delegation.md). Existem [muitos sites de terceiros que oferecem a validação de delegação DNS](https://www.bing.com/search?q=dns+check+tool). Este é um teste de delegação de *zona*, pelo que deve introduzir apenas o nome da zona DNS e não o nome de registo completamente qualificado.
4.  Tendo concluído o procedimento acima, o registo DNS deverá agora resolver corretamente. Para verificar, pode utilizar novamente [digwebinterface](https://digwebinterface.com), desta vez com as predefinições de servidor de nome.


### <a name="recommended-articles"></a>Recommended articles

* [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>Como posso especificar o “service” e o “protocol” para um registo SRV?

O DNS do Azure gere registos DNS como conjuntos de registos. A recolha de registos com o mesmo nome e o mesmo tipo. Para um conjunto de registos SRV, o “service” e o “protocol” têm de ser especificados como parte do nome do conjunto de registos. Os outros parâmetros SRV (“priority”, “weight”, “port” e “target”) são especificados em separado para cada registo no conjunto de registos.

Exemplos de nomes de registos SRV (“sip” do nome do serviço, “tcp” do protocolo):

- \_sip.\_tcp (cria um conjunto de registos no apex da zona)
- \_sip.\_tcp.sipservice (cria um conjunto de registos com o nome “sipservice”)

### <a name="recommended-articles"></a>Recommended articles

* [Zonas e registos DNS](dns-zones-records.md)
* [Criar registos e conjuntos de registos DNS com o portal do Azure](dns-getstarted-create-recordset-portal.md)
* [Tipo de registo SRV (Wikipédia)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>Passos seguintes

* Learn about [Azure DNS zones and records](dns-zones-records.md)
* To start using Azure DNS, learn how to [create a DNS zone](dns-getstarted-create-dnszone-portal.md) and [create DNS records](dns-getstarted-create-recordset-portal.md).
* To migrate an existing DNS zone, learn how to [import and export a DNS zone file](dns-import-export.md).

