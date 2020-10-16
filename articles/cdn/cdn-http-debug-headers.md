---
title: Cabeçalhos X-EC-Debug HTTP para motor de regras do Azure CDN Microsoft Docs
description: O cabeçalho de pedido de cache X-EC-Debug fornece informações adicionais sobre a política de cache que é aplicada ao ativo solicitado. Estes cabeçalhos são específicos de Verizon.
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
ms.date: 04/12/2018
ms.author: allensu
ms.openlocfilehash: 4154c6a1e739f935022271e7a101f39d3ee5c500
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84343025"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>Cabeçalhos X-EC-Debug HTTP para motor de regras Azure CDN
O cabeçalho de pedido de cache de depurado, `X-EC-Debug` fornece informações adicionais sobre a política de cache que é aplicada ao ativo solicitado. Estes cabeçalhos são específicos do **Azure CDN Premium dos** produtos Verizon.

## <a name="usage"></a>Utilização
A resposta enviada dos servidores POP a um utilizador inclui o `X-EC-Debug` cabeçalho apenas quando as seguintes condições são satisfeitas:

- A [função Debug Cache Response Headers](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) foi ativada no motor de regras para o pedido especificado.
- O pedido especificado define o conjunto de cabeçalhos de resposta cache de depurado que serão incluídos na resposta.

## <a name="requesting-debug-cache-information"></a>Solicitando informações sobre cache de depurg
Utilize as seguintes diretivas no pedido especificado para definir as informações de cache de depurada que serão incluídas na resposta:

Cabeçalho do pedido | Descrição |
---------------|-------------|
X-EC-Debug: x-ec-cache | [Código de estado da cache](#cache-status-code-information)
X-EC-Debug: x-ec-cache-remote | [Código de estado da cache](#cache-status-code-information)
X-EC-Debug: x-ec-check-cacheable | [Cacheable](#cacheable-response-header)
X-EC-Debug: x-ec-cache-key | [Cache-key](#cache-key-response-header)
X-EC-Debug: x-ec-cache-state | [Estado de cache](#cache-state-response-header)

### <a name="syntax"></a>Syntax

Podem ser solicitados cabeçalhos de resposta de cache de depurg, incluindo o seguinte cabeçalho e as diretivas especificadas no pedido:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Cabeçalho X-EC-Debug da amostra

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Informações sobre o código de estado da cache
O cabeçalho de resposta X-EC-Debug pode identificar um servidor e como lidou com a resposta através das seguintes diretivas:

Cabeçalho | Descrição
-------|------------
X-EC-Debug: x-ec-cache | Este cabeçalho é relatado sempre que o conteúdo é encaminhado através do CDN. Identifica o servidor POP que cumpriu o pedido.
X-EC-Debug: x-ec-cache-remote | Este cabeçalho só é relatado quando o conteúdo solicitado foi cached em um servidor de escudo de origem ou um servidor de gateway ADN.

### <a name="response-header-format"></a>Formato do cabeçalho de resposta

O cabeçalho X-EC-Debug informa informações sobre o código de estado da cache no seguinte formato:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

Os termos utilizados na sintaxe do cabeçalho de resposta acima são definidos da seguinte forma:
- StatusCode: Indica como o conteúdo solicitado foi tratado pelo CDN, que é representado através de um código de estado de cache.
    
    O código de estado TCP_DENIED pode ser reportado em vez de NENHUM quando um pedido não autorizado é negado devido a Token-Based Autenticação. No entanto, o código de estado DES continuará a ser utilizado ao visualizar relatórios do Estado da Cache ou dados de registo bruto.

- Plataforma: Indica a plataforma na qual o conteúdo foi solicitado. Os seguintes códigos são válidos para este campo:

    Código  | Plataforma
    ------| --------
    ECACC | HTTP Grande
    CES   | HTTP Pequeno
    ECD   | Rede de Entrega de Aplicações (ADN)

- POP: Indica o [POP](cdn-pop-abbreviations.md) que tratou do pedido. 

### <a name="sample-response-headers"></a>Cabeçalhos de resposta da amostra

Os seguintes cabeçalhos de amostra fornecem informações do código de estado da cache para um pedido:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Cabeçalho de resposta cacheável
O `X-EC-Debug: x-ec-check-cacheable` cabeçalho de resposta indica se o conteúdo solicitado pode ter sido em cache.

Este cabeçalho de resposta não indica se ocorreu o caching. Pelo contrário, indica se o pedido era elegível para o caching.

### <a name="response-header-format"></a>Formato do cabeçalho de resposta

O `X-EC-Debug` cabeçalho de resposta que informa se um pedido poderia ter sido em cache encontra-se no seguinte formato:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

O termo utilizado na sintaxe do cabeçalho de resposta acima é definido da seguinte forma:

Valor  | Descrição
-------| --------
SIM    | Indica que o conteúdo solicitado era elegível para o caching.
NO     | Indica que o conteúdo solicitado não era elegível para o caching. Este estatuto pode dever-se a uma das seguintes razões: <br /> - Customer-Specific Configuração: Uma configuração específica da sua conta pode impedir que os servidores pop caching um ativo. Por exemplo, o Rules Engine pode impedir que um ativo seja cached, permitindo a funcionalidade Cache bypass para pedidos de qualificação.<br /> - Cabeçalhos de resposta cache: Os cabeçalhos Cache-Control e expira do ativo solicitado podem impedir que os servidores POP o cache.
DESCONHECIDO | Indica que os servidores não foram capazes de avaliar se o ativo solicitado era cacheable. Este estado ocorre normalmente quando o pedido é negado devido à autenticação baseada em fichas.

### <a name="sample-response-header"></a>Cabeçalho de resposta da amostra

O seguinte cabeçalho de resposta da amostra indica se o conteúdo solicitado poderia ter sido em cache:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>cabeçalho de resposta Cache-Key
O `X-EC-Debug: x-ec-cache-key` cabeçalho de resposta indica a cache-chave física associada ao conteúdo solicitado. Um cache-chave físico consiste num caminho que identifica um ativo para efeitos de cache. Por outras palavras, os servidores verificarão uma versão em cache de um ativo de acordo com o seu caminho, tal como definido pela sua cache-key.

Esta cache-key física começa com um corte duplo para a frente (//) seguido pelo protocolo utilizado para solicitar o conteúdo (HTTP ou HTTPS). Este protocolo é seguido pelo caminho relativo para o ativo solicitado, que começa com o ponto de acesso ao conteúdo (por exemplo, _/000001/_).

Por padrão, as plataformas HTTP são configuradas para utilizar *cache padrão*, o que significa que as cadeias de consulta são ignoradas pelo mecanismo de cache. Este tipo de configuração impede que a chave de cache inclua dados de cadeia de consulta.

Se uma cadeia de consulta for gravada na chave de cache, é convertida para o seu equivalente de haxixe e depois inserida entre o nome do ativo solicitado e a sua extensão de ficheiro (por exemplo, &lt; valor de haxixe do ativo &gt; .html).

### <a name="response-header-format"></a>Formato do cabeçalho de resposta

O `X-EC-Debug` cabeçalho de resposta relata informações físicas de cache-chave no seguinte formato:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Cabeçalho de resposta da amostra

O seguinte cabeçalho de resposta da amostra indica a cache-chave física para o conteúdo solicitado:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Cabeçalho de resposta do estado de cache
O `X-EC-Debug: x-ec-cache-state` cabeçalho de resposta indica o estado da cache do conteúdo solicitado no momento em que foi solicitado.

### <a name="response-header-format"></a>Formato do cabeçalho de resposta

O `X-EC-Debug` cabeçalho de resposta informa informações do estado de cache no seguinte formato:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

Os termos utilizados na sintaxe do cabeçalho de resposta acima são definidos da seguinte forma:

- MASeconds: Indica a idade máxima (em segundos) definida pelos cabeçalhos Cache-Control do conteúdo solicitado.

- MATimePeriod: Converte o valor da idade máxima (isto é, MASeconds) para o equivalente aproximado de uma unidade maior (por exemplo, dias). 

- UnixTime: Indica o conjunto de tempos de cache do conteúdo solicitado no tempo Unix (também conhecido como tempo POSIX ou época Unix). O tempotamos de cache indica a data/hora de início a partir da qual será calculado o TTL de um ativo. 

    Se o servidor de origem não utilizar um servidor HTTP de terceiros ou se esse servidor não devolver o cabeçalho de resposta Age, então o relógio de cache será sempre a data/hora em que o ativo foi recuperado ou revalidado. Caso contrário, os servidores POP utilizarão o campo Age para calcular o TTL do ativo da seguinte forma: Retrieval/RevalidateDateTime - Idade.

- ddd, dd MMM yyy HH:mm:ss GMT: Indica o tempotamétrico de cache do conteúdo solicitado. Para mais informações, consulte o termo UnixTime acima.

- CASeconds: Indica o número de segundos decorridos desde o tempo de cache.

- RTSeconds: Indica o número de segundos restantes para os quais o conteúdo em cache será considerado fresco. Este valor é calculado da seguinte forma: RTSeconds = idade máxima - idade da cache.

- RTTimePeriod: Converte o valor TTL restante (isto é, RTSeconds) para o equivalente aproximado de uma unidade maior (por exemplo, dias).

- ExpiraSSegundos: Indica o número de segundos restantes antes da data/hora especificada no `Expires` cabeçalho de resposta. Se o `Expires` cabeçalho de resposta não foi incluído na resposta, então o valor deste termo *não*é nenhum .

### <a name="sample-response-header"></a>Cabeçalho de resposta da amostra

O seguinte cabeçalho de resposta da amostra indica o estado da cache do conteúdo solicitado no momento em que foi solicitado:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

