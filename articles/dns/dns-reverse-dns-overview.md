---
title: Visão geral do DNS reverso no Azure-DNS do Azure
description: Neste roteiro de aprendizagem, comece a aprender como o DNS reverso funciona e como ele pode ser usado no Azure
services: dns
documentationcenter: na
author: asudbring
manager: KumuD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: allensu
ms.openlocfilehash: 2788cc3957e9822e61c10f5f06a29802e225bcbf
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211018"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Visão geral de DNS reverso e suporte no Azure

Este artigo fornece uma visão geral de como funciona o DNS reverso e os cenários de DNS reverso com suporte no Azure.

## <a name="what-is-reverse-dns"></a>O que é o DNS reverso?

Os registros DNS convencionais habilitam um mapeamento de um nome DNS (como ' www.contoso.com ') para um endereço IP (como 64.4.6.100).  O DNS reverso permite a tradução de um endereço IP (64.4.6.100) de volta para um nome (' www.contoso.com ').

Os registros DNS reversos são usados em várias situações. Por exemplo, os registros DNS reversos são amplamente usados para combater spam por email, verificando o remetente de uma mensagem de email.  O servidor de recebimento de email recupera o registro DNS reverso do endereço IP do servidor de envio e verifica se o host está autorizado a enviar email do domínio de origem. 

## <a name="how-reverse-dns-works"></a>Como funciona o DNS reverso

Os registros DNS reversos são hospedados em zonas DNS especiais, conhecidas como zonas ' ARPA '.  Essas zonas formam uma hierarquia de DNS separada em paralelo com os domínios de Hospedagem de hierarquia normal, como ' contoso.com '.

Por exemplo, o registro DNS ' www.contoso.com ' é implementado usando um registro DNS ' A ' com o nome ' www ' na zona ' contoso.com '.  Esse registro a aponta para o endereço IP correspondente, neste caso, 64.4.6.100.  A pesquisa inversa é implementada separadamente, usando um registro ' PTR ' denominado ' 100 ' na zona ' 6.4.64.in-addr. arpa ' (Observe que os endereços IP são revertidos em zonas ARPA.)  Esse registro PTR, se tiver sido configurado corretamente, apontará para o nome ' www.contoso.com '.

Quando uma organização recebe um bloco de endereço IP, elas também adquirem o direito de gerenciar a zona ARPA correspondente. As zonas ARPA correspondentes aos blocos de endereços IP usados pelo Azure são hospedadas e gerenciadas pela Microsoft. Seu ISP pode hospedar a zona ARPA para seus próprios endereços IP para você ou pode permitir que você hospede a zona ARPA em um serviço DNS de sua escolha, como o DNS do Azure.

> [!NOTE]
> As pesquisas de DNS diretas e as pesquisas reversas de DNS são implementadas em hierarquias de DNS paralelas separadas. A pesquisa inversa para ' www.contoso.com ' **não** está hospedada na zona ' contoso.com ', em vez disso, ela está hospedada na zona arpa para o bloco de endereço IP correspondente. Zonas separadas são usadas para blocos de endereços IPv4 e IPv6.

### <a name="ipv4"></a>IPv4

O nome de uma zona de pesquisa inversa de IPv4 deve estar no seguinte formato: `<IPv4 network prefix in reverse order>.in-addr.arpa`.

Por exemplo, ao criar uma zona inversa para hospedar registros para hosts com IPs que estão no prefixo 192.0.2.0/24, o nome da zona seria criado isolando o prefixo de rede do endereço (192.0.2) e, em seguida, revertendo o pedido (2.0.192) e adicionando o sufixo `.in-addr.arpa`.

|Classe de sub-rede|Prefixo de rede  |Prefixo de rede invertido  |Sufixo padrão  |Nome da zona inversa |
|-------|----------------|------------|-----------------|---------------------------|
|Classe A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|Classe B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|Classe C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Delegação de IPv4 sem classe

Em alguns casos, o intervalo de IP alocado para uma organização é menor que um intervalo de classe C (/24). Nesse caso, o intervalo de IP não se enquadra em um limite de zona dentro da hierarquia de zona de `.in-addr.arpa` e, portanto, não pode ser delegado como uma zona filho.

Em vez disso, um mecanismo diferente é usado para transferir o controle de registros de pesquisa inversa individuais (PTR) para uma zona DNS dedicada. Esse mecanismo delega uma zona filho para cada intervalo de IP e, em seguida, mapeia cada endereço IP no intervalo individualmente para essa zona filho usando registros CNAME.

Por exemplo, suponha que uma organização receba o intervalo de IP 192.0.2.128/26 por seu ISP. Isso representa 64 endereços IP, de 192.0.2.128 para 192.0.2.191. O DNS reverso para esse intervalo é implementado da seguinte maneira:
- A organização cria uma zona de pesquisa inversa chamada 128-26.2.0.192.in-addr. arpa. O prefixo ' 128-26 ' representa o segmento de rede atribuído à organização dentro do intervalo de classe C (/24).
- O ISP cria registros NS para configurar a delegação de DNS para a zona acima da zona pai da classe C. Ele também cria registros CNAME na zona de pesquisa inversa pai (classe C), mapeando cada endereço IP no intervalo de IP para a nova zona criada pela organização:

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
- Em seguida, a organização gerencia os registros PTR individuais dentro de sua zona filho.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
Uma pesquisa inversa para o endereço IP ' 192.0.2.129 ' consulta um registro PTR denominado ' 129.2.0.192.in-addr. arpa '. Essa consulta é resolvida por meio do CNAME na zona pai para o registro PTR na zona filho.

### <a name="ipv6"></a>IPv6

O nome de uma zona de pesquisa inversa IPv6 deve estar no seguinte formato: `<IPv6 network prefix in reverse order>.ip6.arpa`

Por exemplo,. Ao criar uma zona inversa para hospedar registros para hosts com IPs que estão no prefixo 2001: DB8:1000: ABDC::/64, o nome da zona seria criado isolando o prefixo de rede do endereço (2001: DB8: ABDC::). Em seguida, expanda o prefixo de rede IPv6 para remover a [compactação zero](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx), se ela tiver sido usada para encurtar o prefixo de endereço IPv6 (2001:0DB8: ABDC: 0000::). Inverta a ordem, usando um período como o delimitador entre cada número hexadecimal no prefixo, para criar o prefixo de rede invertido (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`) e adicionar o sufixo `.ip6.arpa`.


|Prefixo de rede  |Prefixo de rede expandido e invertido |Sufixo padrão |Nome da zona inversa  |
|---------|---------|---------|---------|
|2001:db8:abdc::/64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | .ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102::/64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | .ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Suporte do Azure para DNS reverso

O Azure dá suporte a dois cenários separados relacionados ao DNS reverso:

**Hospedando a zona de pesquisa inversa correspondente ao seu bloco de endereço IP.**
O DNS do Azure pode ser usado para [hospedar suas zonas de pesquisa inversa e gerenciar os registros PTR para cada pesquisa de DNS reverso](dns-reverse-dns-hosting.md), tanto para IPv4 quanto para IPv6.  O processo de criar a zona de pesquisa inversa (ARPA), configurar a delegação e configurar os registros PTR é o mesmo que para zonas DNS regulares.  As únicas diferenças são que a delegação deve ser configurada por meio de seu ISP, em vez de seu registrador de DNS, e somente o tipo de registro PTR deve ser usado.

**Configure o registro DNS reverso para o endereço IP atribuído ao seu serviço do Azure.** O Azure permite que você [Configure a pesquisa inversa para os endereços IP alocados para o serviço do Azure](dns-reverse-dns-for-azure-services.md).  Essa pesquisa inversa é configurada pelo Azure como um registro PTR na zona ARPA correspondente.  Essas zonas ARPA, correspondentes a todos os intervalos de IP usados pelo Azure, são hospedadas pela Microsoft

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o DNS reverso, consulte [pesquisa reversa de DNS na Wikipédia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Saiba como [hospedar a zona de pesquisa inversa para seu intervalo de IP atribuído pelo ISP no DNS do Azure](dns-reverse-dns-for-azure-services.md).
<br>
Saiba como [gerenciar registros DNS reversos para seus serviços do Azure](dns-reverse-dns-for-azure-services.md).

