---
title: Visão geral da delegação do Azure DNS
description: Compreenda como alterar a delegação de domínios e utilizar servidores de nomes de DNS do Azure para fornecer o alojamento de domínios.
services: dns
author: rohinkoul
ms.service: dns
ms.date: 04/19/2021
ms.author: rohink
ms.topic: conceptual
ms.openlocfilehash: 4753b07cc2f3ccd998c26a3392eb08c8761dd6f7
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738851"
---
# <a name="delegation-of-dns-zones-with-azure-dns"></a>Delegação de zonas DNS com o DNS do Azure

O DNS do Azure permite-lhe alojar uma zona DNS e gerir os registos de DNS para um domínio no Azure. Para que as consultas do DNS de um domínio alcancem o DNS do Azure, o domínio tem de ser delegado ao DNS do Azure a partir do domínio principal. Lembre-se que O DNS Azure não é o registo de domínio. Este artigo explica como funciona a delegação de domínios e como delegar domínios ao DNS do Azure.

## <a name="how-dns-delegation-works"></a>Como funciona a delegação ao DNS

### <a name="domains-and-zones"></a>Domínios e zonas

O Sistema de Nomes de Domínio é uma hierarquia de domínios. A hierarquia começa a partir do domínio “raiz”, cujo nome é simplesmente “**.**”.  Abaixo deste domínio, surgem os domínios de nível superior, como “com”, “net”, “org”, “pt” ou “fr”.  Abaixo destes domínios de nível superior, estão os domínios de segundo nível, tais como “org.pt” ou “co.uk”.  E assim sucessivamente. Os domínios na hierarquia do DNS estão alojados com recurso a zonas DNS separadas. Estas zonas são distribuídas globalmente, alojadas por servidores de nomes DNS em todo o mundo.

**Zona DNS** - um domínio é um nome exclusivo no Sistema de Nomes de Domínio, como, por exemplo, “contoso.com”. Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Por exemplo, o domínio “contoso.com” pode conter vários registos DNS, como “mail.contoso.com” (para um servidor de e-mail) e “www.contoso.com” (para um Web site).

**Entidade de registo de domínios** - uma entidade de registo de domínios é uma empresa que pode fornecer nomes de domínios de Internet. Esta entidade irá verificar se o domínio da Internet que pretende utilizar está disponível e permite-lhe que o compre. Uma vez registado o nome de domínio, é o proprietário legal do nome de domínio. Se já tiver um domínio de Internet, utilizará o registo de domínio atual para delegar no Azure DNS.

Para obter mais informações sobre os registos de domínio acreditados, consulte [os registos acreditados da ICANN.](https://www.icann.org/registrar-reports/accredited-list.html)

### <a name="resolution-and-delegation"></a>Resolução e delegação

Existem dois tipos de servidores DNS:

* Um servidor DNS *autoritativo* aloja zonas DNS. Responde a consultas DNS para os registos apenas dessas zonas.
* Um servidor DNS *recursivo* não acolhe zonas DNS. Responde a todas as consultas DNS ao chamar servidores DNS autoritativos para recolher os dados de que necessita.

O Azure DNS fornece um serviço DNS autoritativo.  Não fornece um serviço de DNS recursivo. Os Serviços Cloud e as VMs no Azure são automaticamente configurados para utilizar um serviço DNS recursivo, que é fornecido separadamente como parte da infraestrutura do Azure. Para obter informações sobre como alterar estas definições de DNS, veja [Name Resolution in Azure (Resolução de Nomes no Azure)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

Os clientes DNS em Computadores ou dispositivos móveis normalmente chamam um servidor DNS recursivo para fazer qualquer consulta de DNS que as aplicações do cliente precisam.

Quando um servidor DNS recursivo recebe uma consulta para um registo DNS, tal como “www.contoso.com”, tem primeiro de localizar o nome do servidor que aloja a zona do domínio “contoso.com”. Para tal, começa pelos servidores de nomes de raiz e, a partir daí, localiza os servidores de nomes que alojam a zona “com”. Em seguida, consulta os servidores de nomes “com” para localizar os servidores de nomes que alojam a zona “contoso.com”.  Finalmente, é capaz de consultar estes servidores de nomes para 'www.contoso.com'.

Este procedimento é denominado “resolver o nome DNS”. Em rigor, a resolução do DNS inclui mais passos como seguir cnames, mas isso não é importante para entender como funciona a delegação do DNS.

Como é que uma zona principal “aponta” para os servidores de nomes de uma zona subordinada? Consegue fazê-lo ao utilizar um tipo especial de registo DNS chamado registo NS (NS significa servidor de nomes, “name server” em inglês). Por exemplo, a zona raiz contém registos NS para “com” e mostra os servidores de nomes para a zona “com”. Por sua vez, a zona “com” contém registos NS para “contoso.com”, que mostra os servidores de nomes para a zona “contoso.com”. Configurar os registos NS para uma zona subordinada numa zona principal é denominado delegar o domínio.

A imagem seguinte mostra um exemplo de consulta DNS. As contoso.net e partners.contoso.net são zonas DNS do Azure.

![Dns-nameserver](./media/dns-domain-delegation/image1.png)

1. O cliente pede `www.partners.contoso.net` a partir do respetivo servidor DNS local.
2. O servidor DNS local não tem o registo, por isso faz um pedido ao servidor de nomes de raiz.
3. O servidor de nomes de raiz não tem o registo, mas conhece o endereço do servidor de `.net` nomes, fornece esse endereço para o servidor DNS
4. O servidor DNS local envia o pedido para o `.net` servidor de nomes.
5. O `.net` servidor de nomes não tem o registo, mas conhece o endereço do servidor de `contoso.net` nomes. Neste caso, responde com o endereço do servidor de nomes da zona DNS hospedada no DNS.
6. O servidor DNS local envia o pedido para o servidor de nomes para a `contoso.net` zona hospedada no Azure DNS.
7. A zona `contoso.net` não tem o registo, mas conhece o servidor de `partners.contoso.net` nomes e responde com o endereço. Neste caso, é uma zona de DNS hospedada no Azure DNS.
8. O servidor DNS local envia o pedido para o servidor de nomes para a `partners.contoso.net` zona.
9. A `partners.contoso.net` zona tem o registo A e responde com o endereço IP.
10. O servidor DNS local fornece o endereço IP ao cliente
11. O cliente liga-se ao site `www.partners.contoso.net`.

Cada delegação, na verdade, tem duas cópias dos registos NS; uma na zona principal a apontar para a subordinada e outra na própria zona subordinada. A zona “contoso.net” contém os registos NS para “contoso.net” (além dos registos NS em “net”). Estes são designados registos NS autoritativos e residem no vértice da zona subordinada.

## <a name="next-steps"></a>Passos seguintes

Aprenda a [delegar o domínio no DNS do Azure](dns-delegate-domain-azure-dns.md)
