---
title: Descrição geral da delegação de DNS do Azure
description: Compreenda como alterar a delegação de domínios e utilizar servidores de nomes de DNS do Azure para fornecer o alojamento de domínios.
services: dns
author: vhorne
ms.service: dns
ms.date: 2/19/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 70c1c1ab0dd5d1998054cf0c68325022803dff06
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791618"
---
# <a name="delegation-of-dns-zones-with-azure-dns"></a>Delegação de zonas DNS com o DNS do Azure

O DNS do Azure permite-lhe alojar uma zona DNS e gerir os registos de DNS para um domínio no Azure. Para que as consultas do DNS de um domínio alcancem o DNS do Azure, o domínio tem de ser delegado ao DNS do Azure a partir do domínio principal. Tenha em atenção que o DNS do Azure não é a entidade de registo de domínios. Este artigo explica como funciona a delegação de domínios e como delegar domínios ao DNS do Azure.

## <a name="how-dns-delegation-works"></a>Como funciona a delegação ao DNS

### <a name="domains-and-zones"></a>Domínios e zonas

O Sistema de Nomes de Domínio é uma hierarquia de domínios. A hierarquia começa a partir do domínio “raiz”, cujo nome é simplesmente “**.**”.  Abaixo deste domínio, surgem os domínios de nível superior, como “com”, “net”, “org”, “pt” ou “fr”.  Abaixo destes domínios de nível superior, estão os domínios de segundo nível, tais como “org.pt” ou “co.uk”.  E assim sucessivamente. Os domínios na hierarquia do DNS estão alojados com recurso a zonas DNS separadas. Estas zonas são distribuídas globalmente, alojadas por servidores de nomes DNS em todo o mundo.

**Zona DNS** - um domínio é um nome exclusivo no Sistema de Nomes de Domínio, como, por exemplo, “contoso.com”. Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Por exemplo, o domínio “contoso.com” pode conter vários registos DNS, como “mail.contoso.com” (para um servidor de e-mail) e “www.contoso.com” (para um Web site).

**Entidade de registo de domínios** - uma entidade de registo de domínios é uma empresa que pode fornecer nomes de domínios de Internet. Esta entidade irá verificar se o domínio da Internet que pretende utilizar está disponível e permite-lhe que o compre. Assim que o nome de domínio estiver registado, será o proprietário legal do mesmo. Se já tiver um domínio da Internet, irá utilizar a entidade de registo de domínios atual para delegar ao DNS do Azure.

Para obter mais informações sobre entidades de registo do domínio acreditada, consulte [ICANN-Accredited entidades de registo](https://www.icann.org/registrar-reports/accredited-list.html).

### <a name="resolution-and-delegation"></a>Resolução e delegação

Existem dois tipos de servidores DNS:

* Um servidor DNS *autoritativo* aloja zonas DNS. Responde a consultas DNS para os registos apenas dessas zonas.
* Um servidor DNS *recursivo* não aloja zonas DNS. Responde a todas as consultas DNS ao chamar servidores DNS autoritativos para recolher os dados de que necessita.

O Azure DNS fornece um serviço DNS autoritativo.  Não fornece um serviço DNS recursivo. Os Serviços Cloud e as VMs no Azure são automaticamente configurados para utilizar um serviço DNS recursivo, que é fornecido separadamente como parte da infraestrutura do Azure. Para obter informações sobre como alterar estas definições de DNS, veja [Name Resolution in Azure (Resolução de Nomes no Azure)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

Normalmente, os clientes DNS em PCs ou em dispositivos móveis chamam um servidor DNS recursivo para efetuar quaisquer consultas de DNS que as aplicações de cliente necessitem.

Quando um servidor DNS recursivo recebe uma consulta para um registo DNS, tal como “www.contoso.com”, tem primeiro de localizar o nome do servidor que aloja a zona do domínio “contoso.com”. Para tal, começa pelos servidores de nomes de raiz e, a partir daí, localiza os servidores de nomes que alojam a zona “com”. Em seguida, consulta os servidores de nomes “com” para localizar os servidores de nomes que alojam a zona “contoso.com”.  Finalmente, é capaz de consultar estes servidores de nome para “www.contoso.com”.

Este procedimento é denominado “resolver o nome DNS”. Em termos mais rigorosos, a resolução de DNS inclui passos adicionais, tais como o seguimento de CNAMEs, mas que não são importantes para compreender como funciona a delegação do DNS.

Como é que uma zona principal “aponta” para os servidores de nomes de uma zona subordinada? Consegue fazê-lo ao utilizar um tipo especial de registo DNS chamado registo NS (NS significa servidor de nomes, “name server” em inglês). Por exemplo, a zona raiz contém registos NS para “com” e mostra os servidores de nomes para a zona “com”. Por sua vez, a zona “com” contém registos NS para “contoso.com”, que mostra os servidores de nomes para a zona “contoso.com”. Configurar os registos NS para uma zona subordinada numa zona principal é denominado delegar o domínio.

A imagem seguinte mostra um exemplo de consulta DNS. As contoso.net e partners.contoso.net são zonas DNS do Azure.

![Dns-nameserver](./media/dns-domain-delegation/image1.png)

1. O cliente pede `www.partners.contoso.net` a partir do respetivo servidor DNS local.
2. O servidor DNS local não tem o registo, por isso faz um pedido ao respetivo servidor de nomes de raiz.
3. O servidor de nomes de raiz não tem o registo, mas sabe o endereço do servidor de nomes `.net`, e fornece esse endereço ao servidor DNS
4. O servidor DNS local envia o pedido para o `.net` nome do servidor.
5. O `.net` nome do servidor não tem o registo, mas souber o endereço do `contoso.net` nome do servidor. Neste caso, ele responde com o endereço do servidor nome da zona DNS alojada no DNS do Azure.
6. O servidor DNS local envia o pedido para o nome do servidor para o `contoso.net` zona alojada no DNS do Azure.
7. A zona `contoso.net` não tem o registo, mas sabe o nome do servidor para `partners.contoso.net` e responde com o endereço. Neste caso, é uma zona DNS alojada no DNS do Azure.
8. O servidor DNS local envia o pedido para o nome do servidor para o `partners.contoso.net` zona.
9. O `partners.contoso.net` zona tem o registo e responde com o endereço IP.
10. O servidor DNS local fornece o endereço IP do cliente
11. O cliente liga-se ao site `www.partners.contoso.net`.

Cada delegação, na verdade, tem duas cópias dos registos NS; uma na zona principal a apontar para a subordinada e outra na própria zona subordinada. A zona “contoso.net” contém os registos NS para “contoso.net” (além dos registos NS em “net”). Estes são designados registos NS autoritativos e residem no vértice da zona subordinada.

## <a name="next-steps"></a>Passos Seguintes

Aprenda a [delegar o domínio no DNS do Azure](dns-delegate-domain-azure-dns.md)

