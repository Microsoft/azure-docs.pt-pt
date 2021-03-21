---
title: Visão geral do DNS invertido em Azure - Azure DNS
description: Neste caminho de aprendizagem, começa a aprender como funciona o DNS invertido e como pode ser usado em Azure
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
ms.openlocfilehash: 8af9549efc3e8dab54f55dd404346d87201dee2c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94965617"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Visão geral do DNS invertido e suporte em Azure

Este artigo dá uma visão geral de como o DNS invertido funciona, e os cenários DNS invertidos suportados no Azure.

## <a name="what-is-reverse-dns"></a>O que é DNS invertido?

Os registos DE DNS convencionais permitem um mapeamento de um nome DNS (como 'www.contoso.com') para um endereço IP (como 64.4.6.100).  O DNS invertido permite a tradução de um endereço IP (64.4.6.100) de volta a um nome ('www.contoso.com').

Os registos DNS invertidos são utilizados numa variedade de situações. Por exemplo, os registos DNS invertidos são amplamente utilizados no combate ao spam de correio eletrónico, verificando o remetente de uma mensagem de correio eletrónico.  O servidor de correio eletrónico recetor recupera o registo DNS invertido do endereço IP do servidor de envio e verifica se esse anfitrião está autorizado a enviar e-mails a partir do domínio de origem. 

## <a name="how-reverse-dns-works"></a>Como funciona o DNS invertido

Os registos DNS invertidos estão alojados em zonas especiais de DNS, conhecidas como zonas de ARPA.  Estas zonas formam uma hierarquia de DNS separada em paralelo com a hierarquia normal que acolhe domínios como o "contoso.com".

Por exemplo, o registo dns 'www.contoso.com' é implementado usando um registo DNS 'A' com o nome 'www' na zona 'contoso.com'.  Este registo A aponta para o endereço IP correspondente, neste caso 64.4.6.100.  O lookup inverso é implementado separadamente, utilizando um registo 'PTR' denominado '100' na zona '6.4.64.in-addr.arpa' (note que os endereços IP são invertidos nas zonas ARPA.)  Este registo DE PTR, se tiver sido configurado corretamente, aponta para o nome 'www.contoso.com'.

Quando uma organização é atribuída a um bloco de endereços IP, também adquire o direito de gerir a zona ARPA correspondente. As zonas ARPA correspondentes aos blocos de endereços IP utilizados pelo Azure são hospedadas e geridas pela Microsoft. O seu ISP pode hospedar a zona ARPA para os seus próprios endereços IP para si, ou pode permitir-lhe hospedar a zona ARPA num serviço DNS à sua escolha, como O DNS Azure.

> [!NOTE]
> As pesquisas de DNS para a frente e as pesquisas de DNS inversas são implementadas em hierarquias separadas e paralelas de DNS. O olhar inverso para o "www.contoso.com" **não** está hospedado na zona 'contoso.com', mas está hospedado na zona ARPA para o bloco de endereços IP correspondente. Zonas separadas são utilizadas para blocos de endereços IPv4 e IPv6.

### <a name="ipv4"></a>IPv4

O nome de uma zona de procura inversa IPv4 deve estar no seguinte formato: `<IPv4 network prefix in reverse order>.in-addr.arpa` .

Por exemplo, ao criar uma zona inversa para hospedar registos de anfitriões com IPs que se encontrem no prefixo de 192.0.2.0/24, o nome da zona seria criado isolando o prefixo de rede do endereço (192.0.2) e, em seguida, invertendo a ordem (2.0.192) e adicionando o sufixo `.in-addr.arpa` .

|Classe sub-rede|Prefixo de rede  |Prefixo de rede invertido  |Sufixo padrão  |Nome da zona inversa |
|-------|----------------|------------|-----------------|---------------------------|
|Classe A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|Classe B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|Classe C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Delegação IPv4 sem classe

Em alguns casos, a gama DEP atribuída a uma organização é menor do que uma gama de classe C (/24). Neste caso, o intervalo de PI não se enquadra num limite de zona dentro da hierarquia da `.in-addr.arpa` zona, pelo que não pode ser delegado como zona infantil.

Em vez disso, um mecanismo diferente é usado para transferir o controlo dos registos individuais de pesquisa inversa (PTR) para uma zona de DNS dedicada. Este mecanismo delega uma zona infantil para cada gama de IP, em seguida, mapeia cada endereço IP no intervalo individualmente para essa zona infantil usando registos CNAME.

Por exemplo, suponha que uma organização seja concedida ao intervalo IP 192.0.2.128/26 pelo seu ISP. Este valor representa 64 endereços IP, de 192.0.2.128 a 192.0.2.191. O DNS inverso para esta gama é implementado da seguinte forma:
- A organização cria uma zona de pesquisa inversa chamada 128-26.2.0.192.in-addr.arpa. O prefixo '128-26' representa o segmento de rede atribuído à organização dentro da gama classe C (/24).
- O ISP cria registos NS para configurar a delegação de DNS para a zona acima da zona-mãe da classe C. Também cria registos CNAME na zona de procura inversa (classe C), mapeando cada endereço IP na gama IP para a nova zona criada pela organização:

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
Uma pesquisa inversa para o endereço IP '192.0.2.129' consultas para um registo PTR chamado "129.2.0.192.in-addr.arpa". Esta consulta resolve-se através do CNAME na zona-mãe até ao registo DER na zona infantil.

### <a name="ipv6"></a>IPv6

O nome de uma zona de procura inversa IPv6 deve ser da seguinte forma: `<IPv6 network prefix in reverse order>.ip6.arpa`

Por exemplo. Ao criar uma zona inversa para hospedar registos para anfitriões com IPs que estão no prefixo 2001:db8:1000:abdc:::64, o nome da zona seria criado isolando o prefixo de rede do endereço (2001:db8:abdc::). Em seguida, expanda o prefixo da rede IPv6 para remover [a compressão zero,](/previous-versions/windows/it-pro/windows-server-2003/cc781672(v=ws.10))se foi utilizado para encurtar o prefixo de endereço IPv6 (2001:0db8:abdc:0000::). Inverta a ordem, utilizando um período como delimiter entre cada número hexadecimal no prefixo, para construir o prefixo de rede invertido ( `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2` ) e adicionar o sufixo `.ip6.arpa` .


|Prefixo de rede  |Prefixo de rede expandido e invertido |Sufixo padrão |Nome da zona inversa  |
|---------|---------|---------|---------|
|2001:db8:abdc::/64    | 0.0.0.0.c.d.b.a.8.b.d.0.0.0.0.2        | .ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102::/64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | .ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Suporte azure para DNS invertidos

O Azure suporta dois cenários distintos relativos ao DNS invertido:

**Hospedar a zona de procura inversa correspondente ao seu bloco de endereços IP.**
O Azure DNS pode ser utilizado para [hospedar as suas zonas de pesquisa inversa e gerir os registos PTR para cada pesquisa de DNS invertido](dns-reverse-dns-hosting.md), tanto para IPv4 como IPv6.  O processo de criação da zona de pesquisa inversa (ARPA), a criação da delegação e a configuração dos registos de PTR é o mesmo que para as zonas regulares de DNS.  As únicas diferenças são que a delegação deve ser configurada através do seu ISP em vez do seu registo DENS, e apenas o tipo de registo PTR deve ser usado.

**Configure o registo DNS invertido para o endereço IP atribuído ao seu serviço Azure.** O Azure [permite-lhe configurar o lookup inverso dos endereços IP atribuídos ao seu serviço Azure](dns-reverse-dns-for-azure-services.md).  Esta procura inversa é configurada pelo Azure como um registo PTR na zona ARPA correspondente.  Estas zonas ARPA, correspondentes a todas as gamas IP utilizadas pelo Azure, são hospedadas pela Microsoft

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre DNS invertidos, consulte [a pesquisa reversa do DNS na Wikipédia.](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)
<br>
Saiba como [hospedar a zona de pesquisa inversa para a gama IP atribuída ao ISP em Azure DNS](dns-reverse-dns-for-azure-services.md).
<br>
Saiba como [gerir registos DNS invertidos para os seus serviços Azure.](dns-reverse-dns-for-azure-services.md)