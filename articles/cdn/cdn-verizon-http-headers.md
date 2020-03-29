---
title: Cabeçalhos HTTP específicos de Verizon para motor de regras Azure CDN [ Microsoft Docs
description: Este artigo descreve como usar cabeçalhos HTTP específicos de Verizon com motor de regras Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: magattus
ms.openlocfilehash: a5881bea578f2791f8dc0d6e760fd15c6f47e435
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593251"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Cabeçalhos HTTP específicos de Verizon para motor de regras Azure CDN

Para **o Azure CDN Premium a partir de** produtos Verizon, quando um pedido HTTP é enviado para o servidor de origem, o servidor ponto de presença (POP) pode adicionar um ou mais cabeçalhos reservados (ou cabeçalhos especiais proxy) no pedido do cliente ao POP. Estes cabeçalhos são para além dos cabeçalhos de encaminhamento padrão recebidos. Para obter informações sobre os cabeçalhos de pedido padrão, consulte [os campos de pedido](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Se pretender evitar que um destes cabeçalhos reservados seja adicionado no pedido POP do Azure CDN (Rede de Entrega de Conteúdos) ao servidor de origem, deve criar uma regra com a [funcionalidade Proxy Special Headers](cdn-verizon-premium-rules-engine-reference-features.md#proxy-special-headers) no motor de regras. Nesta regra, exclua o cabeçalho que pretende remover da lista padrão de cabeçalhos no campo dos cabeçalhos. Se tiver ativado a [função Debug Cache Response Headers,](cdn-verizon-premium-rules-engine-reference-features.md#debug-cache-response-headers)certifique-se de adicionar os cabeçalhos necessários. `X-EC-Debug` 

Por exemplo, para `Via` remover o cabeçalho, o campo de cabeçalhos da regra deve incluir a seguinte lista de cabeçalhos: *X-Forwarded-For, X-Forwarded-Proto, X-Host, X-Midgress, X-Gateway-List, X-EC-Name, Host*. 

![Regra dos cabeçalhos especiais proxy](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

A tabela seguinte descreve os cabeçalhos que podem ser adicionados pelo Verizon CDN POP no pedido:

Cabeçalho do pedido | Descrição | Exemplo
---------------|-------------|--------
[Via](#via-request-header) | Identifica o servidor POP que proxiu o pedido a um servidor de origem. | HTTP/1.1 ECS (dca/1A2B)
X-Forward-For | Indica o endereço IP do solicitador.| 10.10.10.10
X-Forwarded-Proto | Indica o protocolo do pedido. | http
X-Host | Indica o nome de anfitrião do pedido. | cdn.mydomain.com
X-Midgress | Indica se o pedido foi solicitado através de um servidor Adicional de CDN. Por exemplo, um servidor de escudo de servidor-origem POP ou um servidor de gateway pop servidor-adn. <br />Este cabeçalho só é adicionado ao pedido quando o tráfego midgress ocorre. Neste caso, o cabeçalho está definido para 1 para indicar que o pedido foi proxid através de um servidor Adicional CDN.| 1
[Anfitrião](#host-request-header) | Identifica o hospedeiro e o porto onde o conteúdo solicitado pode ser encontrado. | marketing.mydomain.com:80
[Lista X-Gateway](#x-gateway-list-request-header) | ADN: Identifica a lista de falhas dos servidores ADN Gateway atribuídos a uma origem do cliente. <br />Escudo de origem: Indica o conjunto de servidores de escudode origem atribuídos à origem do cliente. | `icn1,hhp1,hnd1`
X-EC-_&lt;nome&gt;_ | Os cabeçalhos de pedido que começam com *X-EC* (por exemplo, X-EC-Tag, [X-EC-Debug)](cdn-http-debug-headers.md)são reservados para utilização pelo CDN.| waf-produção

## <a name="via-request-header"></a>Através de cabeçalho de pedido
O formato `Via` através do qual o cabeçalho de pedido identifica um servidor POP é especificado pela seguinte sintaxe:

`Via: Protocol from Platform (POP/ID)` 

Os termos utilizados na sintaxe são definidos da seguinte forma:
- Protocolo: Indica a versão do protocolo (por exemplo, HTTP/1.1) utilizado para proxy o pedido. 

- Plataforma: Indica a plataforma em que o conteúdo foi solicitado. Os seguintes códigos são válidos para este campo: 

    Código | Plataforma
    -----|---------
    Ecacc | HTTP Grande
    ECS   | HTTP Pequeno
    ECD   | Rede de entrega de aplicações (ADN)

- Indica o [POP](cdn-pop-abbreviations.md) que lidou com o pedido. 

- ID: Apenas para uso interno.

### <a name="example-via-request-header"></a>Exemplo através do cabeçalho de pedido

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Cabeçalho de pedido de anfitrião
Os servidores POP sobrepor-se-ão ao `Host` cabeçalho quando ambas as condições seguintes forem verdadeiras:
- A fonte para o conteúdo solicitado é um servidor de origem do cliente.
- A opção http host header da origem do cliente correspondente não está em branco.

O `Host` cabeçalho de pedido será substituído para refletir o valor definido na opção http host header.
Se a opção http host header da origem `Host` do cliente estiver definida em branco, então o cabeçalho de pedido que é submetido pelo solicitado será encaminhado para o servidor de origem do cliente.

## <a name="x-gateway-list-request-header"></a>Cabeçalho de pedido da Lista X-Gateway
Um servidor POP irá adicionar/substituir o 'Cabeçalho de pedido da Lista X-Gateway quando uma das seguintes condições for satisfeita:
- O pedido aponta para a plataforma ADN.
- O pedido é encaminhado para um servidor de origem do cliente que está protegido pela funcionalidade Origin Shield.

