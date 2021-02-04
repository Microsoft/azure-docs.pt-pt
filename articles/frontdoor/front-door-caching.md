---
title: Azure Front Door - | de caching Microsoft Docs
description: Este artigo ajuda-o a compreender o comportamento da Porta da Frente com regras de encaminhamento que permitiram o caching.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: d001a7a24d44c46a19bde08051e21d3ae3c5acb8
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538056"
---
# <a name="caching-with-azure-front-door"></a>Caching com Azure Front Door
O documento que se segue especifica comportamentos para a Porta frontal com regras de encaminhamento que permitiram o caching. Front Door é uma moderna Rede de Entrega de Conteúdos (CDN) com aceleração dinâmica do site e equilíbrio de carga, também suporta comportamentos de caching como qualquer outro CDN.

## <a name="delivery-of-large-files"></a>Entrega de grandes ficheiros
A porta frontal Azure entrega ficheiros grandes sem uma tampa no tamanho do ficheiro. A Porta da Frente usa uma técnica chamada "pancada de objetos". Quando é pedido um ficheiro grande, o Front Door obtém pequenas partes do ficheiro a partir do back-end. Depois de receber um pedido de ficheiro completo ou byte-range, o ambiente front door solicita o ficheiro a partir do backend em pedaços de 8 MB.

Depois de o pedaço chegar ao ambiente da Porta da Frente, é em cache e imediatamente servido ao utilizador. Porta da frente, em seguida, pré-buscar o próximo pedaço em paralelo. Esta pré-busca garante que o conteúdo permanece um pedaço à frente do utilizador, o que reduz a latência. Este processo continua até que todo o ficheiro seja descarregado (se solicitado) ou o cliente fechar a ligação.

Para obter mais informações sobre o pedido byte-range, leia [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
A Porta da Frente caches quaisquer pedaços como eles são recebidos para que todo o arquivo não precise ser em cache na cache da porta da frente. Os pedidos subsequentes para o ficheiro ou intervalos de byte são servidos a partir da cache. Se os pedaços não estiverem todos em cache, a pré-busca é usada para pedir pedaços do backend. Esta otimização baseia-se na capacidade do backend de suportar pedidos de alcance byte. Se o backend não suporta pedidos de byte-range, esta otimização não é eficaz.

## <a name="file-compression"></a>Compressão de ficheiros
A Porta frontal pode comprimir dinamicamente o conteúdo na borda, resultando num tempo de resposta cada vez menor e mais rápido para os seus clientes. Para que um ficheiro seja elegível para compressão, o caching deve ser ativado e o ficheiro deve ser de tipo MIME para ser elegível para compressão. Atualmente, a Porta da Frente não permite que esta lista seja alterada. A lista atual é:
- "aplicação/eot"
- "aplicação/fonte"
- "aplicação/fonte-sfnt"
- "aplicação/javascript"
- "application/json"
- "aplicação/opentipo"
- "aplicação/otf"
- "aplicação/pkcs7-mime"
- "aplicação/verdadeirotipo"
- "aplicação/ttf",
- "aplicação/vnd.ms-fontobject"
- "aplicação/xhtml+xml"
- "aplicação/xml"
- "aplicação/xml+rss"
- "aplicação/x-tipo de letra-opentype"
- "aplicação/x-fonte-truetipo"
- "aplicação/x-fonte-ttf"
- "aplicação/x-httpd-cgi"
- "aplicação/x-mpegurl"
- "aplicação/x-opentype"
- "aplicação/x-otf"
- "aplicação/x-perl"
- "aplicação/x-ttf"
- "aplicação/x-javascript"
- "fonte/eot"
- "fonte/ttf"
- "fonte/otf"
- "fonte/tipo aberto"
- "imagem/svg+xml"
- "texto/css"
- "texto/csv"
- "texto/html"
- "texto/javascript"
- "texto/js", "texto/planície"
- "texto/texto rico"
- "valores separados por texto/separados"
- "texto/xml"
- "texto/x-script"
- "componente text/x"
- "texto/x-java-fonte"

Além disso, o ficheiro também deve ter entre 1 KB e 8 MB de tamanho, inclusive.

Estes perfis suportam as seguintes codificações de compressão:
- [Gzip (zip GNU)](https://en.wikipedia.org/wiki/Gzip)
- [Rio Brotli](https://en.wikipedia.org/wiki/Brotli)

Se um pedido suporta a compressão gzip e Brotli, a compressão Brotli tem precedência.</br>
Quando um pedido de um ativo especifica a compressão e o pedido resulta numa falha de cache, a Porta Frontal faz a compressão do ativo diretamente no servidor POP. Depois, o ficheiro comprimido é servido a partir da cache. O item resultante é devolvido com uma codificação de transferência: em pedaços.

## <a name="query-string-behavior"></a>Comportamento da corda de consulta
Com a Porta Frontal, pode controlar como os ficheiros são cached para um pedido web que contém uma cadeia de consulta. Num pedido web com uma cadeia de consulta, a cadeia de consulta é essa parte do pedido que ocorre após um ponto de interrogação (?). Uma cadeia de consulta pode conter um ou mais pares de valor-chave, nos quais o nome do campo e o seu valor são separados por um sinal igual (=). Cada par de valores-chave é separado por um ampersand (&). Por exemplo, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Se há mais de um par de valores-chave numa sequência de consulta de um pedido, então a encomenda deles não importa.
- **Ignore as cordas de consulta**: Neste modo, a Porta Frontal passa as cordas de consulta do solicitador para o backend no primeiro pedido e caches o ativo. Todos os pedidos subsequentes para o ativo que são servidos a partir do ambiente da Porta frontal ignoram as cordas de consulta até que o ativo em cache expire.

- **Cache cada URL único**: Neste modo, cada pedido com um URL único, incluindo a cadeia de consulta, é tratado como um ativo único com a sua própria cache. Por exemplo, a resposta do backend para um pedido `www.example.ashx?q=test1` de pedido é em cache no ambiente da porta da frente e devolvida para caches subsequentes com a mesma cadeia de consulta. Um pedido `www.example.ashx?q=test2` é em cache como um ativo separado com a sua própria definição de tempo-a-vida.

## <a name="cache-purge"></a>Purga de cache

Porta frontal caches ativos até que o tempo de vida do ativo (TTL) expire. Sempre que um cliente solicita um ativo com TTL caducado, o ambiente front door recupera uma nova cópia atualizada do ativo para servir o pedido e, em seguida, armazena a cache renovada.

A melhor prática para garantir que os seus utilizadores obtenham sempre a cópia mais recente dos seus ativos é ver versão dos seus ativos para cada atualização e publicá-los como novos URLs. A Porta da Frente irá imediatamente recuperar os novos ativos para os próximos pedidos do cliente. Por vezes, pode querer expurgar o conteúdo em cache de todos os nós de borda e forçá-los a todos a recuperar novos ativos atualizados. A razão pode ser devido a atualizações para a sua aplicação web, ou para atualizar rapidamente ativos que contenham informações incorretas.

Selecione os ativos que pretende purgar dos nós de borda. Para limpar todos os ativos, **selecione Purgar todos**. Caso contrário, em **Caminho,** entre no caminho de cada ativo que pretende purgar.

Estes formatos são suportados nas listas de caminhos para purgar:

- **Purga de caminho único**: Purgar os ativos individuais especificando o percurso completo do ativo (sem o protocolo e domínio), com a extensão do ficheiro, por exemplo, /imagens/strasbourg.png;
- **A purga wildcard**: Asterisco \* pode ser usado como um wildcard. Purgue todas as pastas, sub-dobradeiras e ficheiros sob um ponto final \* com/no caminho ou purgue todas as sub-dobradeiras e ficheiros sob uma pasta específica especificando a pasta seguida por / \* , por exemplo, /imagens/ \* .
- **Purga do domínio raiz**: Purgue a raiz do ponto final com "/" no caminho.

> [!NOTE]
> **Purgar domínios wildcard**: Especificar caminhos em cache para purgar como discutido nesta secção não se aplica a quaisquer domínios wildcard que estejam associados à Porta da Frente. Atualmente, não apoiamos a purga direta de domínios wildcard. Pode purgar caminhos de subdomínios específicos especificando o subdomínio specfic e o caminho de purga. Por exemplo, se a minha Porta da Frente `*.contoso.com` tiver, posso purgar os bens do meu subdomínio `foo.contoso.com` `foo.contoso.com/path/*` digitando. Atualmente, especificar os nomes dos anfitriões na trajetória do conteúdo da purga é imiado a subdomínios de domínios wildcard, se aplicável.
>

As purgas de cache na porta da frente são insensíveis. Além disso, são agnósticos de cadeia de consulta, o que significa que purgar um URL irá purgar todas as variações de cadeias de consulta do mesmo. 

## <a name="cache-expiration"></a>Expiração da cache
É utilizada a seguinte ordem de cabeçalhos para determinar quanto tempo um item será armazenado na nossa cache:</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: max-age=\<seconds>
3. Expira: \<http-date>

Cache-Control cabeçalhos de resposta que indicam que a resposta não será em cache, como o Cache-Control: privado, Cache-Control: no-cache e Cache-Control: não há loja é honrada.  Se não houver Cache-Control, o comportamento padrão é que a Porta Frontal cache o recurso por X quantidade de tempo onde X é escolhido aleatoriamente entre 1 a 3 dias.

## <a name="request-headers"></a>Cabeçalhos do pedido

Os seguintes cabeçalhos de pedido não serão encaminhados para um backend quando utilizar o caching.
- Comprimento do conteúdo
- Transfer-Encoding

## <a name="cache-duration"></a>Duração da cache

A duração da cache pode ser configurada tanto no Front Door Designer como no Regimento. A duração da cache definida no designer da porta da frente é a duração mínima da cache. Esta sobreposição não funcionará se o cabeçalho de controlo de cache da origem tiver um TTL maior do que o valor de substituição. 

A duração da cache definida através do Rules Engine é uma verdadeira sobreposição de cache, o que significa que utilizará o valor de substituição independentemente do cabeçalho de resposta de origem.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
