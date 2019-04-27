---
title: Cabeçalhos de HTTP X-EC-Debug para o motor de regras de CDN do Azure | Documentos da Microsoft
description: O cabeçalho de pedido de cache de depuração do EC-X-Debug fornece informações adicionais sobre a política de cache que é aplicada ao elemento de pedido. Esses cabeçalhos são específicos para Verizon.
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
ms.date: 04/12/2018
ms.author: magattus
ms.openlocfilehash: 4ba42850ee28e2e212d9bc2b7b64be103218757c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736977"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>Cabeçalhos de HTTP X-EC-Debug para o motor de regras de CDN do Azure
O cabeçalho de pedido de cache de depuração, `X-EC-Debug`, fornece informações adicionais sobre a política de cache que é aplicada ao elemento de pedido. Esses cabeçalhos são específicos **CDN do Azure Premium da Verizon** produtos.

## <a name="usage"></a>Utilização
A resposta enviada de servidores POP para um usuário inclui o `X-EC-Debug` cabeçalho apenas quando as seguintes condições são cumpridas:

- O [funcionalidade de cabeçalhos de resposta de Cache depurar](cdn-rules-engine-reference-features.md#debug-cache-response-headers) foi ativada no motor de regras para o pedido especificado.
- O pedido especificado define o conjunto de cabeçalhos de resposta de cache de depuração que será incluído na resposta.

## <a name="requesting-debug-cache-information"></a>Pedir informações de depuração na cache
Utilize as seguintes diretivas no pedido especificado para definir as informações de cache de depuração que serão incluídas na resposta:

Cabeçalho do pedido | Descrição |
---------------|-------------|
X-EC-Debug: x-ec-cache | [Código de estado de cache](#cache-status-code-information)
X-EC-Debug: x-ec-cache-remoto | [Código de estado de cache](#cache-status-code-information)
X-EC-Debug: x-ec-check-colocáveis em cache | [Colocáveis em cache](#cacheable-response-header)
X-EC-Debug: x-ec-cache-key | [Cache-key](#cache-key-response-header)
X-EC-Debug: x-ec-cache-Estado | [Estado da cache](#cache-state-response-header)

### <a name="syntax"></a>Sintaxe

Depure cabeçalhos podem ser solicitados, incluindo o seguinte cabeçalho e as diretivas especificadas no pedido de resposta de cache:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Cabeçalho X-EC-Debug de exemplo

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Informações de código de estado de cache
O cabeçalho de resposta de EC-X-Debug pode identificar um servidor e como ele manipulado a resposta por meio de diretivas de seguintes:

Cabeçalho | Descrição
-------|------------
X-EC-Debug: x-ec-cache | Este cabeçalho é comunicado sempre que o conteúdo é encaminhado através da CDN. Identifica o servidor POP que atendeu ao pedido.
X-EC-Debug: x-ec-cache-remoto | Este cabeçalho é comunicado apenas quando o conteúdo solicitado foi colocado em cache num servidor de escudo de origem ou de um servidor de gateway do ADN.

### <a name="response-header-format"></a>Formato do cabeçalho de resposta

O cabeçalho X-EC-Debug relatórios de informações de código de estado de cache no seguinte formato:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

Os termos utilizados na sintaxe do cabeçalho de resposta acima são definidos da seguinte forma:
- StatusCode: Indica como o conteúdo solicitado era manipulado pela CDN, que é representada por meio de um código de estado da cache.
    
    O código de estado TCP_DENIED possam ser relatado em vez de NONE, quando um pedido não autorizado é negado devido à autenticação baseada em tokens. No entanto, o código de estado NONE vai continuar a utilizar ao visualizar relatórios de estado da Cache ou dados de registo não processados.

- Plataforma: Indica a plataforma em que o conteúdo foi solicitado. Os códigos seguintes são válidos para este campo:

    Código  | Plataforma
    ------| --------
    ECAcc | HTTP grandes
    ECS   | Pequenas de HTTP
    ECD   | Rede de entrega de aplicações (ADN)

- POP: Indica a [POP](cdn-pop-abbreviations.md) que processada o pedido. 

### <a name="sample-response-headers"></a>Cabeçalhos de resposta de exemplo

Os cabeçalhos de exemplo seguintes fornecem informações de código de estado de cache para um pedido:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Cabeçalho de resposta em cache
O `X-EC-Debug: x-ec-check-cacheable` cabeçalho de resposta indica se o conteúdo solicitado foi estão em cache.

Este cabeçalho de resposta não indica se a colocação em cache ocorria. Em vez disso, ele indica se a solicitação foi elegível para a colocação em cache.

### <a name="response-header-format"></a>Formato do cabeçalho de resposta

O `X-EC-Debug` cabeçalho de resposta se um pedido pode estão em cache de relatórios é o seguinte formato:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

O termo utilizado na sintaxe do cabeçalho de resposta acima é definido da seguinte forma:

Value  | Descrição
-------| --------
SIM    | Indica que o conteúdo solicitado foi elegível para a colocação em cache.
NO     | Indica que o conteúdo solicitado foi não elegível para a colocação em cache. Este estado pode ser devido a um dos seguintes motivos: <br /> -Configuração específicas do cliente: Uma configuração específica para a sua conta pode impedir que os servidores de preenchimento da colocação em cache um recurso. Por exemplo, motor de regras pode impedir que um recurso de serem colocados em cache ao ativar a funcionalidade de ignorar a Cache para qualificar pedidos.<br /> -Cabeçalhos de resposta em cache: Cabeçalhos de Cache-Control e Expires do recurso solicitado podem impedir que os servidores de POP colocação em cache.
DESCONHECIDO | Indica que os servidores não é possível avaliar se o recurso pedido foi colocáveis em cache. Este estado normalmente ocorre quando o pedido é negado devido à autenticação baseada em tokens.

### <a name="sample-response-header"></a>Cabeçalho de resposta de exemplo

O cabeçalho de resposta de exemplo seguinte indica se o conteúdo solicitado foi estão em cache:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Cabeçalho de resposta de chave de cache
O `X-EC-Debug: x-ec-cache-key` cabeçalho de resposta indica a cache-chave física associada com os conteúdos solicitados. Uma chave de cache física consiste num caminho que identifica um recurso para fins de colocação em cache. Em outras palavras, os servidores irão verificar para uma versão em cache de um ativo, de acordo com seu caminho, conforme definido pela respetiva chave de cache.

Esta chave de cache físico começa com uma barra dupla (/ /) seguido do protocolo utilizado para pedir o conteúdo (HTTP ou HTTPS). Esse protocolo é seguido o caminho relativo para o elemento solicitado, que começa com o ponto de acesso ao conteúdo (por exemplo, _/000001/_).

Por predefinição, as plataformas HTTP são configuradas para utilizar *standard – cache*, que significa que cadeias de consulta são ignoradas do mecanismo de colocação em cache. Este tipo de configuração impede que a chave de cache incluindo dados de cadeia de caracteres de consulta.

Se uma cadeia de consulta é registrada na chave do cache, convertido em seu equivalente de hash e, em seguida, é inserido entre o nome do recurso solicitado e a extensão de ficheiro (por exemplo, asset&lt;valor de hash&gt;. HTML).

### <a name="response-header-format"></a>Formato do cabeçalho de resposta

O `X-EC-Debug` cabeçalho de resposta de relatórios de informações de chave de cache físicas no seguinte formato:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Cabeçalho de resposta de exemplo

O cabeçalho de resposta de exemplo seguinte indica a chave de cache de física para o conteúdo pedido:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Cabeçalho de resposta de estado de cache
O `X-EC-Debug: x-ec-cache-state` cabeçalho de resposta indica o estado de cache do conteúdo pedido ao tempo foi solicitada.

### <a name="response-header-format"></a>Formato do cabeçalho de resposta

O `X-EC-Debug` cabeçalho de resposta de relatórios de informações de estado de cache no seguinte formato:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

Os termos utilizados na sintaxe do cabeçalho de resposta acima são definidos da seguinte forma:

- MASeconds: Indica a idade máxima (em segundos), conforme definido pela cabeçalhos Cache-Control dos conteúdos solicitados.

- MATimePeriod: Converte o valor de duração máxima (ou seja, MASeconds) para o equivalente de uma unidade maior (por exemplo, dias). 

- UnixTime: Indica o carimbo de hora de cache do conteúdo pedido em tempo de Unix (também conhecido como POSIX tempo ou época do Unix). O carimbo de hora da cache indica a data/hora inicial da qual será calculada TTL de um recurso. 

    Se o servidor de origem não utiliza um pedido de HTTP de terceiros colocação em cache de servidor ou se nesse servidor não devolve o cabeçalho de resposta de idade, em seguida, o carimbo de hora de cache será sempre a data/hora quando o recurso foi obtido ou revalidar o. Caso contrário, os servidores de POP irão utilizar o campo de idade para calcular o valor de TTL do recurso da seguinte forma: Obtenção/RevalidateDateTime - idade.

- ddd, dd MMM aaaa hh: mm: GMT: Indica o carimbo de hora de cache do conteúdo pedido. Para obter mais informações, consulte o termo UnixTime acima.

- CASeconds: Indica o número de segundos decorridos desde o carimbo de hora de cache.

- RTSeconds: Indica o número de segundos restantes para o qual o conteúdo em cache será considerado atualizado. Este valor é calculado da seguinte forma: RTSeconds = max-age - idade da cache.

- RTTimePeriod: Converte o valor de TTL (ou seja, RTSeconds) restante para o equivalente de uma unidade maior (por exemplo, dias).

- ExpiresSeconds: Indica o número de segundos restantes antes da data/hora especificada no `Expires` cabeçalho de resposta. Se o `Expires` cabeçalho de resposta não foi incluído na resposta, em seguida, é o valor desse termo *none*.

### <a name="sample-response-header"></a>Cabeçalho de resposta de exemplo

O cabeçalho de resposta de exemplo seguinte indica o estado de cache do conteúdo pedido no momento em que foi solicitada:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

