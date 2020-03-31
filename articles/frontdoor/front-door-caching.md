---
title: Porta da Frente Azure - cache Microsoft Docs
description: Este artigo ajuda-o a entender como a Porta Frontal Azure monitoriza a saúde dos seus backends
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: d4fed878e2c0b1430e963f43743fd772493d3270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471749"
---
# <a name="caching-with-azure-front-door"></a>Caching com porta da frente azure
O seguinte documento especifica o comportamento para a Porta da Frente com regras de encaminhamento que permitiram o cache. Front Door é uma moderna Rede de Entrega de Conteúdos (CDN) e assim, juntamente com a aceleração dinâmica do site e o equilíbrio de carga, também suporta comportamentos de cache como qualquer outro CDN.

## <a name="delivery-of-large-files"></a>Entrega de ficheiros grandes
A Porta Frontal Azure entrega ficheiros grandes sem tampa no tamanho do ficheiro. A Porta da Frente usa uma técnica chamada chunking de objetos. Quando é pedido um ficheiro grande, o Front Door obtém pequenas partes do ficheiro a partir do back-end. Depois de receber um pedido de ficheiro completo ou de intervalo de bytes, um ambiente do Front Door pede o ficheiro ao back-end em segmentos de 8 MB.

</br>Depois de o pedaço chegar ao ambiente da Porta da Frente, é emcache e imediatamente servido ao utilizador. Porta da frente, em seguida, pré-fetchs o próximo pedaço em paralelo. Esta pré-busca garante que o conteúdo fica um pedaço à frente do utilizador, o que reduz a latência. Este processo continua até que todo o ficheiro seja descarregado (se solicitado), todas as gamas de byte estão disponíveis (se solicitado), ou o cliente termina a ligação.

</br>Para obter mais informações sobre o pedido de byte-range, leia [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
A porta da frente caches quaisquer pedaços que sejam recebidos e por isso todo o ficheiro não precisa de ser cache na cache da porta da frente. Os pedidos subsequentes para o ficheiro ou gama de bytes são servidos a partir da cache. Se nem todos os pedaços estiverem em cache, a pré-busca é usada para solicitar pedaços do backend. Esta otimização baseia-se na capacidade do backend de suportar pedidos de alcance byte; se o backend não apoiar pedidos de alcance byte, esta otimização não é eficaz.

## <a name="file-compression"></a>Compressão de ficheiros
A Porta frontal pode comprimir dinamicamente o conteúdo na borda, resultando numa resposta cada vez mais rápida aos seus clientes. Todos os ficheiros são elegíveis para compressão. No entanto, um ficheiro deve ser de um tipo MIME que seja elegível para a lista de compressão. Atualmente, a Porta da Frente não permite que esta lista seja alterada. A lista atual é:</br>
- "aplicação/eot"
- "aplicação/fonte"
- "aplicação/fonte-sfnt"
- "aplicação/javascript"
- "application/json"
- "aplicação/opentype"
- "Aplicação/otf"
- "aplicação/pkcs7-mime"
- "aplicação/truetype"
- "aplicação/ttf",
- "aplicação/vnd.ms-fontobject"
- "application/xhtml+xml"
- "aplicação/xml"
- "aplicação/xml+rss"
- "aplicação/x-tipo de letra-opentype"
- "aplicação/x-tipo de letra".
- "aplicação/x-font-ttf"
- "aplicação/x-httpd-cgi"
- "application/x-mpegurl"
- "aplicação/x-opentype"
- "aplicação/x-otf"
- "aplicação/x-perl"
- "aplicação/x-ttf"
- "aplicação/x-javascript"
- "fonte/eot"
- "fonte/ttf"
- "fonte/otf"
- "tipo de letra/opentype"
- "image/svg+xml"
- "Texto/css"
- "Texto/csv"
- "Texto/html"
- "Texto/javascript"
- "Texto/js", "texto/planície"
- "Texto/texto rico"
- "Valores separados por texto/separados por separados"
- "Texto/xml"
- "texto/x-script"
- "Texto/componente x"
- "Texto/x-java-fonte"

Além disso, o ficheiro também deve ter entre 1 KB e 8 MB de tamanho, inclusive.

Estes perfis suportam as seguintes codificações de compressão:
- [Gzip (zip GNU)](https://en.wikipedia.org/wiki/Gzip)
- [Rio Brotli](https://en.wikipedia.org/wiki/Brotli)

Se um pedido apoiar a compressão de gzip e Brotli, a compressão brotli tem precedência.</br>
Quando um pedido de um ativo especifica a compressão e o pedido resulta numa falha de cache, a Porta frontal executa a compressão do ativo diretamente no servidor POP. Depois, o ficheiro comprimido é servido a partir da cache. O item resultante é devolvido com uma codificação de transferência: em pedaços.

## <a name="query-string-behavior"></a>Comportamento de corda de consulta
Com a Porta da Frente, pode controlar como os ficheiros são cached para um pedido web que contém uma cadeia de consulta. Num pedido web com uma corda de consulta, a corda de consulta é essa parte do pedido que ocorre após um ponto de interrogação (?). Uma corda de consulta pode conter um ou mais pares de valor-chave, em que o nome de campo e o seu valor são separados por um sinal igual (=). Cada par de valor-chave é separado por um ampersand (&). Por exemplo, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Se houver mais de um par de valor-chave numa sequência de consulta de um pedido, a sua ordem não importa.
- **Ignore as cordas de consulta**: Modo padrão. Neste modo, a Porta Da Frente passa as cordas de consulta do solicitador para o backend no primeiro pedido e caches o ativo. Todos os pedidos subsequentes para o ativo que são servidos a partir do ambiente porta da frente ignoram as cordas de consulta até que o ativo em cache expire.

- **Cache cada URL único**: Neste modo, cada pedido com um URL único, incluindo a corda de consulta, é tratado como um ativo único com a sua própria cache. Por exemplo, a resposta do backend `www.example.ashx?q=test1` para um pedido é cached no ambiente da Porta da Frente e devolvida para caches subsequentes com a mesma corda de consulta. Um pedido `www.example.ashx?q=test2` é cached como um ativo separado com o seu próprio cenário de tempo para viver.

## <a name="cache-purge"></a>Purga de cache
A Porta da Frente cache os ativos até que o tempo de vida do ativo (TTL) expire. Após a expiração do TTL do ativo, quando um cliente solicita o ativo, o ambiente Porta Frontal recuperará uma nova cópia atualizada do ativo para servir o pedido do cliente e armazenar refrescar o cache.
</br>A melhor prática para garantir que os seus utilizadores obtenham sempre a cópia mais recente dos seus ativos é versonizar os seus ativos para cada atualização e publicá-los como novos URLs. A Porta da Frente recuperará imediatamente os novos ativos para os próximos pedidos do cliente. Por vezes, pode querer expurgar o conteúdo em cache de todos os nós de borda e forçá-los a todos a recuperar novos ativos atualizados. Isto pode ser devido a atualizações à sua aplicação web, ou a atualizar rapidamente os ativos que contêm informações incorretas.

</br>Selecione quais os ativos que pretende expurgar dos nódosos de borda. Se desejar limpar todos os ativos, clique em purgar todas as caixas de verificação. Caso contrário, digite o caminho de cada ativo que pretende expurgar na caixa de texto Path. Os formatos abaixo são suportados no caminho.
1. **Purga**de caminho único : Expurgar o(s) ativo individual através da especificação do caminho completo do ativo (sem o protocolo e o domínio), com a extensão do ficheiro, por exemplo, /pictures/estrasburgo.png;
2. **Purga wildcard**: Asterisco ()\*pode ser usado como um wildcard. Expurgue todas as pastas, subpastas e\* ficheiros sob um ponto final com/no caminho ou purgue\*todas as subpastas\*e ficheiros sob uma pasta específica, especificando a pasta seguida por /, por exemplo, /imagens/ .
3. **Purga**do domínio raiz : Expurgar a raiz do ponto final com "/" no caminho.

As purgas de cache na porta da frente são insensíveis ao caso. Além disso, são agnósticos de cordas de consulta, o que significa que purgar um URL irá expurgar todas as variações de cadeias de consulta do mesmo. 

## <a name="cache-expiration"></a>Expiração da cache
A seguinte ordem de cabeçalhos é usada para determinar quanto tempo um item será armazenado na nossa cache:</br>
1. Cache-Control: s-maxage=\<segundos>
2. Cache-Control: idade máxima=\<segundos>
3. Expira: \<> de data de validade

Cabeçalhos de resposta cache-control que indicam que a resposta não será em cache como Cache-Control: privado, Cache-Control: no-cache, e Cache-Control: no-store são honrados. No entanto, se houver vários pedidos a bordo num POP para o mesmo URL, podem partilhar a resposta. Se não houver cache-Control o comportamento padrão é que a AFD irá cache o recurso durante x quantidade de tempo em que X é escolhido aleatoriamente entre 1 a 3 dias.

## <a name="request-headers"></a>Cabeçalhos do pedido

Os seguintes cabeçalhos de pedido não serão encaminhados para um backend quando utilizar em cache.
- Comprimento do conteúdo
- Codificação de transferências

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
