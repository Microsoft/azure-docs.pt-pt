---
title: Descrição geral do DNS inverso no Azure | Documentos da Microsoft
description: Saiba como inversa DNS funciona e como ele pode ser usado no Azure
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: victorh
ms.openlocfilehash: 9d3a62ec1c9ede1f25f2b53f800642a792b3aa28
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534400"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Descrição geral do DNS inverso e de suporte no Azure

Este artigo fornece uma visão geral de como inversa DNS funciona e os cenários DNS reversos suportados no Azure.

## <a name="what-is-reverse-dns"></a>O que é inversa de DNS?

Registos DNS convencionais ativar um mapeamento a partir de um nome DNS (por exemplo, "www.contoso.com") para um endereço IP (por exemplo, 64.4.6.100).  DNS inverso permite a tradução de um endereço IP (64.4.6.100) para um nome ("www.contoso.com").

Registos DNS inversos são utilizados em várias situações. Por exemplo, os registos DNS inversos são amplamente utilizados no combate ao spam de e-mail ao verificar o remetente de uma mensagem de email.  O servidor de correio de recebimento obtém o registo DNS inverso do endereço IP do servidor de envio e verifica se esse anfitrião está autorizado a enviar e-mails a partir do domínio de origem. 

## <a name="how-reverse-dns-works"></a>Funciona como inversa de DNS

Registos DNS inversos estão alojados em zonas DNS especiais, conhecidas como zonas 'ARPA'.  Estas zonas formam uma hierarquia DNS separada em paralelo com a hierarquia normal, o alojamento dos domínios como "contoso.com".

Por exemplo, o DNS registos "www.contoso.com" está implementado com um registo DNS "A" com o nome "www" na zona "contoso.com".  Este registo aponta para o endereço IP correspondente, neste caso 64.4.6.100.  A pesquisa inversa é implementada em separado, com um registo de 'PTR' com o nome "100" na zona '6.4.64.in-addr.arpa' (Observe que os endereços IP são revertidos em zonas ARPA.)  Este registo PTR, se tiver sido configurada corretamente, pontos para o nome "www.contoso.com".

Quando uma organização é atribuída um bloco de endereços IP, eles também a adquirir o direito de gerir a zona ARPA correspondente. As zonas ARPA correspondente para os blocos de endereços IP utilizados pelo Azure estão alojadas e geridas pela Microsoft. Seu ISP pode alojar a zona ARPA para seus próprios endereços IP para, ou poderá permitir-lhe alojar a zona ARPA num serviço DNS da sua preferência, como o DNS do Azure.

> [!NOTE]
> Pesquisas de DNS direta e inversas de DNS de pesquisas são implementadas em hierarquias DNS separadas, paralelas. A pesquisa inversa para "www.contoso.com" está **não** alojado na zona "contoso.com", em vez disso, ele está hospedado na zona ARPA para o bloco de endereços IP correspondente. Zonas separadas são utilizadas para blocos de endereços IPv4 e IPv6.

### <a name="ipv4"></a>IPv4

O nome de uma zona de pesquisa inversa IPv4 deve estar no seguinte formato: `<IPv4 network prefix in reverse order>.in-addr.arpa`.

Por exemplo, ao criar uma zona inversa para registos de anfitrião para anfitriões com IPs que estejam no prefixo de 192.0.2.0/24, o nome da zona seria possível criar isolar o prefixo de rede do endereço (192.0.2) e, em seguida, para inverter a ordem (2.0.192) e adicionar o sufixo `.in-addr.arpa`.

|Classe de sub-rede|Prefixo de rede  |Prefixo de rede invertido  |Sufixo padrão  |Nome de zona inversa |
|-------|----------------|------------|-----------------|---------------------------|
|Classe A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|Classe B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|Classe C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Classless delegação de IPv4

Em alguns casos, o intervalo IP atribuído a uma organização é menor do que uma classe C (/ 24) intervalo. Neste caso, o intervalo de IP não se encontra num limite de zona dentro do `.in-addr.arpa` hierarquia da zona e, por conseguinte, não pode ser delegada como uma zona subordinada.

Em vez disso, um mecanismo diferente é utilizado para transferir o controle de registos de pesquisa inversa individuais (PTR) para uma zona DNS dedicada. Esse mecanismo delega uma zona subordinada para cada intervalo de IP, em seguida, mapeia cada endereço IP no intervalo individualmente para essa zona subordinada com os registos CNAME.

Por exemplo, suponha que uma organização é concedida o IP intervalo 192.0.2.128/26 pelo seu ISP. Isso representa 64 endereços IP, de 192.0.2.128 para 192.0.2.191. DNS inverso para este intervalo é implementado da seguinte forma:
- A organização cria uma zona de pesquisa inversa denominada 26.2.0.192.in-128-addr. O prefixo ' 128-26' representa o segmento de rede atribuído à organização dentro da classe C (/ 24) intervalo.
- O ISP cria registos NS para configurar a delegação de DNS para a zona acima da zona de principal da classe C. Ele também cria registos CNAME na zona de pesquisa inversa principal (classe C), o mapeamento de cada endereço IP no intervalo de IP para a nova zona criada pela organização:

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
- Em seguida, a organização gere os registos PTR individuais dentro de sua zona subordinada.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
Uma pesquisa inversa para as consultas de "192.0.2.129" do endereço IP para um registo PTR com o nome '129.2.0.192.in-addr.arpa'. Esta consulta é resolvido através de CNAME na zona principal para o registo PTR na zona subordinada.

### <a name="ipv6"></a>IPv6

O nome de uma zona de pesquisa inversa IPv6 deve ter o seguinte formato: `<IPv6 network prefix in reverse order>.ip6.arpa`

Por exemplo. Se criar uma zona inversa para registos de anfitrião para anfitriões com IPs que estiverem no 2001:db8:1000:abdc:: / prefixo 64, seria possível criar o nome da zona ao isolar o prefixo do endereço de rede (2001:db8:abdc::). Em seguida expanda o prefixo de rede IPv6 para remover [zero compressão](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx), se ele foi usado para reduzir o prefixo de endereço IPv6 (2001:0db8:abdc:0000::). Inverter a ordem, com um período como delimitador entre cada número hexadecimal no prefixo, para criar o prefixo de rede invertidos (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`) e adicione o sufixo `.ip6.arpa`.


|Prefixo de rede  |Prefixo de rede expandida e invertido |Sufixo padrão |Nome de zona inversa  |
|---------|---------|---------|---------|
|2001:db8:abdc::/64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | .ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102::/64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | .ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Suporte do Azure para DNS inverso

O Azure suporta dois cenários separados relacionados ao DNS inverso:

**Que alojam a zona de pesquisa inversa correspondente ao seu bloco de endereços IP.**
O DNS do Azure podem ser utilizado para [alojar zonas de pesquisa inversa e gerir os registos PTR para cada pesquisa reversa de DNS](dns-reverse-dns-hosting.md), para IPv4 e IPv6.  O processo de criar a zona de pesquisa inversa (ARPA), configurar a delegação e configurar os registos PTR é igual de zonas DNS normais.  As únicas diferenças estão que a delegação de deve ser configurada por meio de seu ISP, em vez de sua entidade de registo DNS, e deve ser utilizado apenas o tipo de registo PTR.

**Configure o registo DNS inverso para o endereço IP atribuído ao seu serviço do Azure.** O Azure permite-lhe [configurar a pesquisa inversa para os endereços IP alocados ao seu serviço do Azure](dns-reverse-dns-for-azure-services.md).  Esta pesquisa inversa é configurada pelo Azure como um registro PTR na zona ARPA correspondente.  Estas zonas ARPA, correspondente a todos os intervalos IP utilizados pelo Azure, estão alojadas pela Microsoft

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre DNS inverso, consulte [pesquisa reversa de DNS na Wikipédia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Saiba como [alojam a zona de pesquisa inversa para o seu intervalo IP atribuído por ISP no DNS do Azure](dns-reverse-dns-for-azure-services.md).
<br>
Saiba como [gerir registos DNS inversos para seus serviços do Azure](dns-reverse-dns-for-azure-services.md).

