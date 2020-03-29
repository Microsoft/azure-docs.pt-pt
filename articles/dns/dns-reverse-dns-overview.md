---
title: Visão geral do DNS invertido em Azure - DNS Azure
description: Neste caminho de aprendizagem, começar a aprender como o DNS inversa funciona e como pode ser usado em Azure
services: dns
documentationcenter: na
author: rohinkoul
manager: KumuD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: bf3da62e989f0e029efdc8e9c70f5f45e0ddd765
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932303"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Visão geral do DNS invertido e apoio em Azure

Este artigo dá uma visão geral de como o DNS funciona invertido, e os cenários dNS invertidos suportados em Azure.

## <a name="what-is-reverse-dns"></a>O que é DNS invertido?

Os registos DNS convencionais permitem um mapeamento de um nome DNS (como 'www.contoso.com') para um endereço IP (como 64.4.6.100).  O DNS inverso permite a tradução de um endereço IP (64.4.6.100) de volta para um nome ('www.contoso.com').

Os registos dNS invertidos são usados em várias situações. Por exemplo, os registos inversos de DNS são amplamente utilizados no combate ao correio eletrónico de correio publicitário não solicitado, verificando o remetente de uma mensagem de correio eletrónico.  O servidor de correio recetor recupera o registo dNS invertido do endereço IP do servidor de envio, e verifica se esse anfitrião está autorizado a enviar e-mail do domínio de origem. 

## <a name="how-reverse-dns-works"></a>Como funciona o DNS invertido

Os registos DNS invertidos estão alojados em zonas especiais de DNS, conhecidas como zonas ARPA.  Estas zonas formam uma hierarquia DNS separada em paralelo com a hierarquia normal de domínios como 'contoso.com'.

Por exemplo, o registo dNS 'www.contoso.com' é implementado utilizando um registo DNS 'A' com o nome 'www' na zona 'contoso.com'.  Este registo aponta para o endereço IP correspondente, neste caso 64.4.6.100.  A procura inversa é implementada separadamente, utilizando um registo 'PTR' denominado '100' na zona '6.4.64.in-addr.arpa' (nota de que os endereços IP são invertidos nas zonas ARPA.)  Este registo ptr, se tiver sido configurado corretamente, aponta para o nome 'www.contoso.com'.

Quando uma organização é atribuída a um bloco de endereçoip, eles também adquirem o direito de gerir a zona ARPA correspondente. As zonas ARPA correspondentes aos blocos de endereçoIP utilizados pelo Azure são hospedadas e geridas pela Microsoft. O seu ISP poderá acolher a zona ARPA para os seus próprios endereços IP para si, ou poderá permitir-lhe hospedar a zona ARPA num serviço DNS à sua escolha, como o Azure DNS.

> [!NOTE]
> As pesquisas avançadas do DNS e as pesquisas de DNS inversas são implementadas em hierarquias dNS separadas e paralelas. O pedido inverso de 'www.contoso.com' **não** está alojado na zona 'contoso.com', mas está alojado na zona ARPA para o respetivo bloco de endereços IP. As zonas separadas são usadas para blocos de endereços IPv4 e IPv6.

### <a name="ipv4"></a>IPv4

O nome de uma zona de procura inversa IPv4 deve estar no seguinte formato: `<IPv4 network prefix in reverse order>.in-addr.arpa`.

Por exemplo, ao criar uma zona inversa para acolher registos para anfitriões com IPs que se encontram no prefixo de 192.0.2.0/24, o nome da zona seria criado isolando o prefixo da `.in-addr.arpa`rede do endereço (192.0.2) e, em seguida, invertendo a ordem (2.0.192) e adicionando o sufixo .

|Classe subnet|Prefixo de rede  |Prefixo de rede invertido  |Sufixo padrão  |Nome da zona inversa |
|-------|----------------|------------|-----------------|---------------------------|
|Classe A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|Classe B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|Classe C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Delegação IPv4 sem classe

Em alguns casos, a gama IP atribuída a uma organização é menor do que uma gama classe C (/24). Neste caso, o intervalo de IP não se `.in-addr.arpa` enquadra numa fronteira de zona dentro da hierarquia da zona, pelo que não pode ser delegada como zona infantil.

Em vez disso, um mecanismo diferente é usado para transferir o controlo de registos individuais de procura ção inversa (PTR) para uma zona dNS dedicada. Este mecanismo delega uma zona infantil para cada intervalo ip, em seguida, mapeia cada endereço IP na gama individualmente para aquela zona infantil usando registos CNAME.

Por exemplo, suponha que uma organização seja concedida a gama IP 192.0.2.128/26 pelo seu ISP. Este valor representa 64 endereços IP, de 192.0.2.128 para 192.0.2.191. O DNS inverso para esta gama é implementado da seguinte forma:
- A organização cria uma zona de procura inversa chamada 128-26.2.0.192.in-addr.arpa. O prefixo '128-26' representa o segmento de rede atribuído à organização dentro da gama Classe C (/24).
- O ISP cria registos de NS para criar a delegação dNS para a zona acima da zona-mãe da classe C. Também cria registos CNAME na zona de procura inversa dos pais (Classe C), mapeando cada endereço IP na gama IP para a nova zona criada pela organização:

```
$ORIGIN 2.0.192.in-addr.arpa
; Delegate child zone
128-26    NS       <name server 1 for 128-26.2.0.192.in-addr.arpa>
128-26    NS       <name server 2 for 128-26.2.0.192.in-addr.arpa>
; CNAME records for each IP address
129       CNAME    129.128-26.2.0.192.in-addr.arpa
130       CNAME    130.128-26.2.0.192.in-addr.arpa
131       CNAME    131.128-26.2.0.192.in-addr.arpa
; etc
```
- A organização gere então os registos individuais de PTR dentro da sua zona infantil.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
Uma retrospetiva inversa do endereço IP '192.0.2.129' para um registo de PTR chamado '129.2.0.192.in-addr.arpa'. Esta consulta resolve-se através do CNAME na zona dos pais até ao registo de PTR na zona infantil.

### <a name="ipv6"></a>IPv6

O nome de uma zona de procura inversa IPv6 deve estar na seguinte forma:`<IPv6 network prefix in reverse order>.ip6.arpa`

Por exemplo. Ao criar uma zona inversa para acolher registos para anfitriões com IPs que estejam no prefixo 2001:db8:1000::/64, o nome da zona seria criado isolando o prefixo da rede do endereço (2001:db8:abdc::). Em seguida, expanda o prefixo da rede IPv6 para remover [a compressão zero](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx), se foi usado para encurtar o prefixo de endereço IPv6 (2001:0db8:abdc:0000::). Inverta a ordem, utilizando um período como delimitador entre cada número hexadecimal`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`no prefixo, para `.ip6.arpa`construir o prefixo de rede invertido ( ) e adicionar o sufixo .


|Prefixo de rede  |Prefixo de rede expandido e invertido |Sufixo padrão |Nome da zona inversa  |
|---------|---------|---------|---------|
|2001:db8:abdc::/64    | 0.0.0.0.c.d.b.a.8.b.d.0.0.0.0.2        | .ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102::/64    | 2.0.1.9.0.0.1.8.b.d.0.0.0.0.2        | .ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Suporte azure para DNS invertido

O Azure suporta dois cenários distintos relativos ao DNS invertido:

**Hospedar a zona de procura inversa correspondente ao seu bloco de endereçoIP.**
O Azure DNS pode ser utilizado para alojar as suas zonas de [procura inversae e gerir os registos de PTR para cada procura de DNS inversa,](dns-reverse-dns-hosting.md)tanto para iPv4 como IPv6.  O processo de criação da zona de procura inversa (ARPA), a criação da delegação, e a configuração dos registos de PTR é o mesmo que para as zonas regulares de DNS.  As únicas diferenças são que a delegação deve ser configurada através do seu ISP em vez do seu registo DNS, e apenas o tipo de registo PTR deve ser utilizado.

**Configure o registo dNS invertido para o endereço IP atribuído ao seu serviço Azure.** O Azure [permite-lhe configurar a procura inversa dos endereços IP atribuídos ao seu serviço Azure](dns-reverse-dns-for-azure-services.md).  Esta retrospetiva inversa é configurada pelo Azure como um registo PTR na zona ARPA correspondente.  Estas zonas ARPA, correspondentes a todas as gamas IP utilizadas pelo Azure, são hospedadas pela Microsoft

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre dNS invertidos, consulte a [procura inversa de DNS na Wikipédia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Saiba como hospedar a zona de procura inversa para a sua gama IP atribuída ao [ISP em Azure DNS](dns-reverse-dns-for-azure-services.md).
<br>
Saiba como [gerir registos dNS invertidos para os seus serviços Azure](dns-reverse-dns-for-azure-services.md).

