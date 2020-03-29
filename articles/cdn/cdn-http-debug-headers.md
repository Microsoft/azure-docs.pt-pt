---
title: Cabeçalhos X-EC-Debug HTTP para motor de regras Azure CDN [ Motor] de regras Azure CDN [ Microsoft Docs
description: O cabeçalho de pedido de cache de depuração X-EC-Debug fornece informações adicionais sobre a política de cache que é aplicada ao ativo solicitado. Estes cabeçalhos são específicos de Verizon.
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
ms.date: 04/12/2018
ms.author: magattus
ms.openlocfilehash: dec753d7c891d226aa2e6d3efa993d8d24adfbaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593826"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>Cabeçalhos X-EC-Debug HTTP para motor de regras Azure CDN
O cabeçalho de `X-EC-Debug`pedido de cache de depuração, fornece informações adicionais sobre a política de cache que é aplicada ao ativo solicitado. Estes cabeçalhos são específicos do **Azure CDN Premium dos** produtos Verizon.

## <a name="usage"></a>Utilização
A resposta enviada dos servidores POP `X-EC-Debug` para um utilizador inclui o cabeçalho apenas quando as seguintes condições forem satisfeitas:

- A [função Debug Cache Response Headers](cdn-verizon-premium-rules-engine-reference-features.md#debug-cache-response-headers) foi ativada no motor de regras para o pedido especificado.
- O pedido especificado define o conjunto de cabeçalhos de resposta de depuração que serão incluídos na resposta.

## <a name="requesting-debug-cache-information"></a>Solicitando informações sobre cache de depuração
Utilize as seguintes diretivas no pedido especificado para definir as informações de cache de depuração que serão incluídas na resposta:

Cabeçalho do pedido | Descrição |
---------------|-------------|
X-EC-Debug: x-ec-cache | [Código de estado de cache](#cache-status-code-information)
X-EC-Debug: x-ec-cache-remoto | [Código de estado de cache](#cache-status-code-information)
X-EC-Debug: x-ec-check-cacheable | [Cacheable](#cacheable-response-header)
X-EC-Debug: x-ec-cache-key | [Chave cache](#cache-key-response-header)
X-EC-Debug: x-ec-cache-state | [Estado de cache](#cache-state-response-header)

### <a name="syntax"></a>Sintaxe

Os cabeçalhos de resposta de cache de depuração podem ser solicitados, incluindo o seguinte cabeçalho e as diretivas especificadas no pedido:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Cabeçalho de amostra X-EC-Debug

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Informações sobre código de estado de cache
O cabeçalho de resposta X-EC-Debug pode identificar um servidor e como lidou com a resposta através das seguintes diretivas:

Cabeçalho | Descrição
-------|------------
X-EC-Debug: x-ec-cache | Este cabeçalho é relatado sempre que o conteúdo é encaminhado através do CDN. Identifica o servidor POP que cumpriu o pedido.
X-EC-Debug: x-ec-cache-remoto | Este cabeçalho só é relatado quando o conteúdo solicitado foi colocado em cache num servidor de escudo de origem ou num servidor de gateway ADN.

### <a name="response-header-format"></a>Formato cabeçalho de resposta

O cabeçalho X-EC-Debug reporta informações sobre o código de estado de cache no seguinte formato:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

Os termos utilizados na sintaxe do cabeçalho de resposta acima são definidos da seguinte forma:
- StatusCode: Indica como o conteúdo solicitado foi tratado pela CDN, que é representada através de um código de estado de cache.
    
    O código de estado TCP_DENIED pode ser reportado em vez de NENHUM quando um pedido não autorizado é negado devido à autenticação baseada em Token. No entanto, o código de estado NONE continuará a ser utilizado ao visualizar relatórios de Estado de Cache ou dados de registo bruto.

- Plataforma: Indica a plataforma em que o conteúdo foi solicitado. Os seguintes códigos são válidos para este campo:

    Código  | Plataforma
    ------| --------
    Ecacc | HTTP Grande
    ECS   | HTTP Pequeno
    ECD   | Rede de Entrega de Aplicações (ADN)

- Indica o [POP](cdn-pop-abbreviations.md) que lidou com o pedido. 

### <a name="sample-response-headers"></a>Cabeçalhos de resposta da amostra

Os seguintes cabeçalhos da amostra fornecem informações sobre o código de estado da cache para um pedido:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Cabeçalho de resposta cacheable
O `X-EC-Debug: x-ec-check-cacheable` cabeçalho de resposta indica se o conteúdo solicitado poderia ter sido cache.

Este cabeçalho de resposta não indica se o cache ocorreu. Pelo contrário, indica se o pedido era elegível para o cache.

### <a name="response-header-format"></a>Formato cabeçalho de resposta

O `X-EC-Debug` cabeçalho de resposta que reporta se um pedido poderia ter sido cache é no seguinte formato:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

O termo utilizado na sintaxe de cabeçalho de resposta acima é definido da seguinte forma:

Valor  | Descrição
-------| --------
SIM    | Indica que o conteúdo solicitado era elegível para cache.
NO     | Indica que o conteúdo solicitado não era elegível para o cache. Este estatuto pode dever-se a uma das seguintes razões: <br /> - Configuração específica do cliente: Uma configuração específica da sua conta pode impedir que os servidores pop acacheum ativo. Por exemplo, o Rules Engine pode impedir que um ativo seja cached, permitindo a funcionalidade De Cache bypass para pedidos de qualificação.<br /> - Cabeçalhos de Resposta cache: Os cabeçalhos de Cache-Control e Expirados do ativo solicitado podem impedir que os servidores POP o apertem.
DESCONHECIDO | Indica que os servidores não foram capazes de avaliar se o ativo solicitado era cacheable. Este estado ocorre normalmente quando o pedido é negado devido à autenticação baseada em token.

### <a name="sample-response-header"></a>Cabeçalho de resposta da amostra

O seguinte cabeçalho de resposta da amostra indica se o conteúdo solicitado poderia ter sido cache:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Cabeçalho de resposta Cache-Key
O `X-EC-Debug: x-ec-cache-key` cabeçalho de resposta indica a chave de cache física associada ao conteúdo solicitado. Uma chave de cache física consiste num caminho que identifica um ativo para fins de cache. Por outras palavras, os servidores verificarão uma versão em cache de um ativo de acordo com o seu caminho, tal como definido pela sua chave de cache.

Esta chave de cache física começa com uma dupla barra para a frente (/) seguida do protocolo utilizado para solicitar o conteúdo (HTTP ou HTTPS). Este protocolo é seguido pelo caminho relativo para o ativo solicitado, que começa com o ponto de acesso ao conteúdo (por exemplo, _/000001/_).

Por padrão, as plataformas HTTP são configuradas para usar *cache padrão*, o que significa que as cordas de consulta são ignoradas pelo mecanismo de cache. Este tipo de configuração impede que a tecla cache inclua dados de cordas de consulta.

Se uma cadeia de consulta for gravada na chave cache, é convertida para o seu equivalente hash e, em seguida,&lt;inserida&gt;entre o nome do ativo solicitado e a sua extensão de ficheiro (por exemplo, valor haxixe do ativo .html).

### <a name="response-header-format"></a>Formato cabeçalho de resposta

O `X-EC-Debug` cabeçalho de resposta reporta informações físicas de cache-key no seguinte formato:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Cabeçalho de resposta da amostra

O seguinte cabeçalho de resposta da amostra indica a chave de cache física para o conteúdo solicitado:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Cabeçalho de resposta do estado cache
O `X-EC-Debug: x-ec-cache-state` cabeçalho de resposta indica o estado cache do conteúdo solicitado no momento em que foi solicitado.

### <a name="response-header-format"></a>Formato cabeçalho de resposta

O `X-EC-Debug` cabeçalho de resposta reporta informações do estado cache no seguinte formato:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

Os termos utilizados na sintaxe do cabeçalho de resposta acima são definidos da seguinte forma:

- MASeconds: Indica a idade máxima (em segundos) definida pelos cabeçalhos de Cache-Control do conteúdo solicitado.

- MATimePeriod: Converte o valor máximo de idade (isto é, MASeconds) para o equivalente aproximado de uma unidade maior (por exemplo, dias). 

- UnixTime: Indica o carimbo de cache do conteúdo solicitado no tempo Unix (também conhecido como tempo POSIX ou época Unix). O carimbo de tempo de cache indica a data/hora de início a partir da qual o TTL de um ativo será calculado. 

    Se o servidor de origem não utilizar um servidor de cache HTTP de terceiros ou se esse servidor não devolver o cabeçalho de resposta age, então a marca de tempo de cache será sempre a data/hora em que o ativo foi recuperado ou revalidado. Caso contrário, os servidores POP utilizarão o campo Age para calcular o TTL do ativo da seguinte forma: Recuperação/RevalidaçãoDateTime - Idade.

- ddd, dd MMM yyyy HH:mm:ss GMT: Indica o carimbo de cache do conteúdo solicitado. Para mais informações, consulte o termo UnixTime acima.

- CASeconds: Indica o número de segundos que decorreram desde a hora do cache.

- RTSeconds: Indica o número de segundos restantes para os quais o conteúdo em cache será considerado fresco. Este valor é calculado da seguinte forma: RTSeconds = idade máxima - idade cache.

- RTTimePeriod: Converte o valor TTL restante (isto é, RTSeconds) para o equivalente aproximado de uma unidade maior (por exemplo, dias).

- ExpiraSeconds: Indica o número de segundos restantes antes `Expires` da data/hora especificada no cabeçalho de resposta. Se `Expires` o cabeçalho de resposta não foi incluído na resposta, então o valor deste termo não é *nenhum*.

### <a name="sample-response-header"></a>Cabeçalho de resposta da amostra

O seguinte cabeçalho de resposta da amostra indica o estado cache do conteúdo solicitado no momento em que foi solicitado:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

