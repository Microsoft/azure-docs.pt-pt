---
title: Cabeçalhos HTTP específicos de Verizon para Azure CDN regras motor | Microsoft Docs
description: Este artigo descreve como utilizar cabeçalhos HTTP específicos da Verizon com motor de regras Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: allensu
ms.openlocfilehash: e20f6ce9540d357b61ae2cfdf0e8f96d127dc6c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84343222"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Cabeçalhos HTTP específicos de Verizon para motor de regras Azure CDN

Para **o Azure CDN Premium dos** produtos Verizon, quando um pedido HTTP é enviado para o servidor de origem, o servidor ponto de presença (POP) pode adicionar um ou mais cabeçalhos reservados (ou cabeçalhos especiais proxy) no pedido do cliente ao POP. Estes cabeçalhos são para além dos cabeçalhos de encaminhamento padrão recebidos. Para obter informações sobre os cabeçalhos de pedido padrão, consulte [os campos Request](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Se pretender evitar que um destes cabeçalhos reservados seja adicionado no pedido POP do Azure CDN (Rede de Entrega de Conteúdos) ao servidor de origem, tem de criar uma regra com a [função Proxy Special Headers](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) no motor de regras. Nesta regra, exclua o cabeçalho que pretende remover da lista predefinido dos cabeçalhos no campo dos cabeçalhos. Se tiver ativado a [função Debug Cache Response Headers,](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm)certifique-se de adicionar os `X-EC-Debug` cabeçalhos necessários. 

Por exemplo, para remover o `Via` cabeçalho, o campo de cabeçalhos da regra deve incluir a seguinte lista de cabeçalhos: *X-Forwarded-For, X-Forwarded-Proto, X-Host, X-Midgress, X-Gateway-List, X-EC-Name, Host*. 

![Regra dos cabeçalhos especiais proxy](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

A tabela a seguir descreve os cabeçalhos que podem ser adicionados pelo CDN POP Verizon no pedido:

Cabeçalho do pedido | Descrição | Exemplo
---------------|-------------|--------
[Via](#via-request-header) | Identifica o servidor POP que proxie o pedido a um servidor de origem. | HTTP/1.1 CES (dCA/1A2B)
X-Forward-For | Indica o endereço IP do solicitador.| 10.10.10.10
X-Forwarded-Proto | Indica o protocolo do pedido. | http
X-Host | Indica o nome de anfitrião do pedido. | cdn.mydomain.com
X-Midgress | Indica se o pedido foi solicitado através de um servidor CDN adicional. Por exemplo, um servidor de escudo pop-para-origem ou um servidor de gateway POP-para-ADN. <br />Este cabeçalho só é adicionado ao pedido quando ocorre o trânsito. Neste caso, o cabeçalho é definido para 1 para indicar que o pedido foi proxipado através de um servidor CDN adicional.| 1
[Anfitrião](#host-request-header) | Identifica o hospedeiro e a porta onde o conteúdo solicitado pode ser encontrado. | marketing.mydomain.com:80
[Lista de X-Gateway](#x-gateway-list-request-header) | ADN: Identifica a lista de falhas dos servidores ADN Gateway atribuídos a uma origem do cliente. <br />Escudo de origem: Indica o conjunto de servidores de escudo de origem atribuídos à origem do cliente. | `icn1,hhp1,hnd1`
X-EC-_&lt; nome &gt;_ | Os cabeçalhos de pedido que começam com *X-EC* (por exemplo, X-EC-Tag, [X-EC-Debug)](cdn-http-debug-headers.md)são reservados para utilização pelo CDN.| waf-produção

## <a name="via-request-header"></a>Através do cabeçalho de pedido
O formato através do qual o cabeçalho de `Via` pedido identifica um servidor POP é especificado pela seguinte sintaxe:

`Via: Protocol from Platform (POP/ID)` 

Os termos utilizados na sintaxe são definidos da seguinte forma:
- Protocolo: Indica a versão do protocolo (por exemplo, HTTP/1.1) utilizado para proxy o pedido. 

- Plataforma: Indica a plataforma na qual o conteúdo foi solicitado. Os seguintes códigos são válidos para este campo: 

    Código | Plataforma
    -----|---------
    ECACC | HTTP Grande
    CES   | HTTP Pequeno
    ECD   | Rede de entrega de aplicativos (ADN)

- POP: Indica o [POP](cdn-pop-abbreviations.md) que tratou do pedido. 

- ID: Apenas para uso interno.

### <a name="example-via-request-header"></a>Exemplo Através do cabeçalho de pedido

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Cabeçalho de pedido de anfitrião
Os servidores POP sobrepor-se-ão ao `Host` cabeçalho quando ambas as seguintes condições forem verdadeiras:
- A fonte para o conteúdo solicitado é um servidor de origem do cliente.
- A opção http host header de origem do cliente correspondente não está em branco.

O `Host` cabeçalho do pedido será substituído para refletir o valor definido na opção HTTP Host Header.
Se a opção HTTP Host Header da origem do cliente estiver definida em branco, então o cabeçalho de `Host` pedido que é submetido pelo solicitador será reencaminhado para o servidor de origem do cliente.

## <a name="x-gateway-list-request-header"></a>Cabeçalho de pedido da Lista X-Gateway
Um servidor POP adicionará/substituirá o cabeçalho de pedido 'Lista X-Gateway quando uma das seguintes condições estiverem satisfeitas:
- O pedido aponta para a plataforma ADN.
- O pedido é reencaminhado para um servidor de origem do cliente que está protegido pela função Origin Shield.

