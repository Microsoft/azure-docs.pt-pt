---
title: Cabeçalhos HTTP específicos da Verizon para o motor de regras de CDN do Azure | Documentos da Microsoft
description: Este artigo descreve como utilizar os cabeçalhos HTTP específicos da Verizon com o motor de regras de CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: magattus
ms.openlocfilehash: 7ce845fb272cea1d621e8ccc18203e3a071e8c29
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57992011"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Cabeçalhos HTTP específicos da Verizon para o motor de regras de CDN do Azure

Para **CDN do Azure Premium da Verizon** produtos, quando uma solicitação HTTP é enviada para o servidor de origem, o servidor do ponto de presença (POP) pode adicionar um ou mais cabeçalhos reservados (ou cabeçalhos especiais de proxy) no pedido do cliente para o POP. Esses cabeçalhos estão além do padrão de reencaminhamento recebidos de cabeçalhos. Para obter informações sobre cabeçalhos de solicitação padrão, consulte [pedir campos](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Se quiser impedir que um desses cabeçalhos reservada a ser adicionado no pedido de POP da CDN do Azure (rede de entrega de conteúdos) para o servidor de origem, tem de criar uma regra com o [funcionalidade de Proxy de cabeçalhos especiais](cdn-rules-engine-reference-features.md#proxy-special-headers) no mecanismo de regras. Esta regra, exclua o cabeçalho que pretende remover da lista padrão de cabeçalhos no campo de cabeçalhos. Se ativou a [funcionalidade de cabeçalhos de resposta de Cache depurar](cdn-rules-engine-reference-features.md#debug-cache-response-headers), certifique-se de que adicionar as informações necessárias `X-EC-Debug` cabeçalhos. 

Por exemplo, para remover o `Via` cabeçalho, o campo de cabeçalhos da regra deve incluir a seguinte lista de cabeçalhos: *X-reencaminhados-para, X-reencaminhados-Proto, X-Host, X-Midgress, X-Gateway-List, X-EC-Name, alojar*. 

![Regra de cabeçalhos especiais de proxy](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

A tabela seguinte descreve os cabeçalhos que podem ser adicionados ao POP do CDN da Verizon no pedido:

Cabeçalho do pedido | Descrição | Exemplo
---------------|-------------|--------
[Via](#via-request-header) | Identifica o servidor POP ou transmitidas por proxy o pedido para um servidor de origem. | ECS de HTTP/1.1 (dca/1A2B)
X-reencaminhados-para | Indica o endereço IP do autor do pedido.| 10.10.10.10
Proto X reencaminhados | Indica o protocolo do pedido. | http
X-Host | Indica o nome de anfitrião do pedido. | cdn.mydomain.com
X Midgress | Indica se o pedido foi transmitidas por proxy por meio de um servidor adicional da CDN. Por exemplo, um servidor de origem de servidor escudo POP ou um servidor de gateway do servidor para ADN POP. <br />Este cabeçalho é adicionado ao pedido, apenas quando o tráfego de midgress ocorre. Neste caso, o cabeçalho é definido como 1 para indicar que o pedido foi transmitidas por proxy por meio de um servidor adicional da CDN.| 1
[Anfitrião](#host-request-header) | Identifica o anfitrião e a porta onde o conteúdo solicitado pode ser encontrado. | marketing.mydomain.com:80
[X-Gateway-List](#x-gateway-list-request-header) | ADN: Identifica a lista de ativação pós-falha de servidores de Gateway de ADN atribuído a uma origem do cliente. <br />Escudo de origem: Indica o conjunto de servidores de escudo de origem atribuído a uma origem do cliente. | `icn1,hhp1,hnd1`
X-EC-_&lt;name&gt;_ | Cabeçalhos de pedido que começam com *X-EC* (por exemplo, X-EC-etiqueta, [EC-X-Debug](cdn-http-debug-headers.md)) estão reservados para utilização pela CDN.| produção de waf

## <a name="via-request-header"></a>Por meio do cabeçalho do pedido
O formato por meio do qual o `Via` pedido cabeçalho identifica um servidor POP é especificado pela seguinte sintaxe:

`Via: Protocol from Platform (POP/ID)` 

Os termos utilizados na sintaxe são definidos da seguinte forma:
- Protocolo: Indica que a versão do protocolo (por exemplo, HTTP/1.1) utilizado para o proxy, o pedido. 

- Plataforma: Indica a plataforma em que o conteúdo foi solicitado. Os códigos seguintes são válidos para este campo: 

    Código | Plataforma
    -----|---------
    ECAcc | HTTP grandes
    ECS   | Pequenas de HTTP
    ECD   | Rede de entrega de aplicações (ADN)

- POP: Indica a [POP](cdn-pop-abbreviations.md) que processada o pedido. 

- ID: Apenas para utilização interna.

### <a name="example-via-request-header"></a>Exemplo por meio do cabeçalho de pedido

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Cabeçalho de pedido de anfitrião
Os servidores de POP substituirá o `Host` cabeçalho quando ambas as condições seguintes forem verdadeiras:
- A origem para o conteúdo pedido é um servidor de origem do cliente.
- Opção de cabeçalho de anfitrião de HTTP da origem de cliente correspondente não está em branco.

O `Host` cabeçalho do pedido será substituído para refletir o valor definido na opção de cabeçalho de anfitrião de HTTP.
Se a opção de cabeçalho de anfitrião de HTTP a origem de cliente está definida como em branco, em seguida, o `Host` cabeçalho do pedido submetido pelo solicitante será reencaminhado para o servidor de origem do cliente.

## <a name="x-gateway-list-request-header"></a>Cabeçalho do pedido de lista de gateways de X
Um servidor POP adiciona/substituirá o "cabeçalho de pedido de lista de gateways de X quando qualquer uma das seguintes condições é cumprida:
- O pedido aponta para a plataforma ADN.
- A solicitação é encaminhada para um servidor de origem do cliente que está protegido pelo recurso do escudo de origem.

