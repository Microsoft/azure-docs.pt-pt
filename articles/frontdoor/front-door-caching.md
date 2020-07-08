---
title: Azure Front Door - caching / Microsoft Docs
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79471749"
---
# <a name="caching-with-azure-front-door"></a>Caching com Azure Front Door
O documento que se segue especifica o comportamento para a Porta frontal com regras de encaminhamento que permitiram o caching. Front Door é uma moderna Rede de Entrega de Conteúdos (CDN) e assim, juntamente com a aceleração dinâmica do site e o equilíbrio de carga, também suporta comportamentos de caching como qualquer outro CDN.

## <a name="delivery-of-large-files"></a>Entrega de grandes ficheiros
A porta frontal Azure entrega ficheiros grandes sem uma tampa no tamanho do ficheiro. A Porta da Frente usa uma técnica chamada "pancada de objetos". Quando é pedido um ficheiro grande, o Front Door obtém pequenas partes do ficheiro a partir do back-end. Depois de receber um pedido de ficheiro completo ou de intervalo de bytes, um ambiente do Front Door pede o ficheiro ao back-end em segmentos de 8 MB.

</br>Depois de o pedaço chegar ao ambiente da porta da frente, é em cache e imediatamente servido ao utilizador. Porta da frente, em seguida, pré-buscar o próximo pedaço em paralelo. Esta pré-busca garante que o conteúdo permanece um pedaço à frente do utilizador, o que reduz a latência. Este processo continua até que todo o ficheiro seja descarregado (se solicitado), todos os intervalos byte estão disponíveis (se solicitado), ou o cliente termina a ligação.

</br>Para obter mais informações sobre o pedido byte-range, leia [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
A Porta da Frente caches quaisquer pedaços à medida que são recebidos e por isso todo o ficheiro não precisa de ser colocado na cache da porta da frente. Os pedidos subsequentes para o ficheiro ou intervalos de byte são servidos a partir da cache. Se nem todos os pedaços estiverem em cache, é usado para solicitar pedaços do backend. Esta otimização baseia-se na capacidade do backend de suportar pedidos byte-range; se o backend não suporta pedidos de byte-range, esta otimização não é eficaz.

## <a name="file-compression"></a>Compressão de ficheiros
A Porta frontal pode comprimir dinamicamente o conteúdo na borda, resultando numa resposta menor e mais rápida aos seus clientes. Todos os ficheiros são elegíveis para compressão. No entanto, um ficheiro deve ser de um tipo MIME que seja elegível para lista de compressão. Atualmente, a Porta frontal não permite que esta lista seja alterada. A lista atual é:</br>
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
Quando um pedido de um ativo especifica a compressão e o pedido resulta numa falha de cache, a Porta Frontal executa a compressão do ativo diretamente no servidor POP. Depois, o ficheiro comprimido é servido a partir da cache. O item resultante é devolvido com uma codificação de transferência: em pedaços.

## <a name="query-string-behavior"></a>Comportamento da corda de consulta
Com a Porta Frontal, pode controlar como os ficheiros são cached para um pedido web que contém uma cadeia de consulta. Num pedido web com uma cadeia de consulta, a cadeia de consulta é essa parte do pedido que ocorre após um ponto de interrogação (?). Uma cadeia de consulta pode conter um ou mais pares de valor-chave, nos quais o nome do campo e o seu valor são separados por um sinal igual (=). Cada par de valores-chave é separado por um ampersand (&). Por exemplo, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Se houver mais de um par de valores-chave numa sequência de consulta de um pedido, a sua encomenda não importa.
- **Ignore as cadeias de consulta**: Modo padrão. Neste modo, a Porta Frontal passa as cordas de consulta do solicitador para o backend no primeiro pedido e caches do ativo. Todos os pedidos subsequentes para o ativo que são servidos a partir do ambiente da Porta frontal ignoram as cordas de consulta até que o ativo em cache expire.

- **Cache cada URL único**: Neste modo, cada pedido com um URL único, incluindo a cadeia de consulta, é tratado como um ativo único com a sua própria cache. Por exemplo, a resposta do backend para um pedido `www.example.ashx?q=test1` de pedido é em cache no ambiente da porta da frente e devolvida para caches subsequentes com a mesma cadeia de consulta. Um pedido `www.example.ashx?q=test2` é em cache como um ativo separado com a sua própria definição de tempo-a-vida.

## <a name="cache-purge"></a>Purga de cache
A Porta frontal cache ativos até que o tempo de vida do ativo (TTL) expire. Após o termo do TTL do ativo, quando um cliente solicita o ativo, o ambiente front door recuperará uma nova cópia atualizada do ativo para servir o pedido do cliente e armazenar a cache.
</br>A melhor prática para garantir que os seus utilizadores obtenham sempre a cópia mais recente dos seus ativos é ver versão dos seus ativos para cada atualização e publicá-los como novos URLs. A Porta da Frente irá imediatamente recuperar os novos ativos para os próximos pedidos do cliente. Por vezes, pode querer expurgar o conteúdo em cache de todos os nós de borda e forçá-los a todos a recuperar novos ativos atualizados. Isto pode ser devido a atualizações para a sua aplicação web, ou para atualizar rapidamente ativos que contenham informações incorretas.

</br>Selecione quais os ativos que deseja purgar dos nós de borda. Se desejar limpar todos os ativos, clique na caixa de verificação Purgar. Caso contrário, digite o caminho de cada ativo que pretende purgar na caixa de texto Path. Os formatos abaixo são suportados no caminho.
1. **Purga de caminho único**: Purpurar os ativos individuais especificando o caminho completo do ativo (sem o protocolo e domínio), com a extensão do ficheiro, por exemplo, /imagens/strasbourg.png;
2. **A purga wildcard**: Asterisco \* pode ser usado como um wildcard. Purgue todas as pastas, sub-dobradeiras e ficheiros sob um ponto final \* com/no caminho ou purgue todas as sub-dobradeiras e ficheiros sob uma pasta específica especificando a pasta seguida por / \* , por exemplo, /imagens/ \* .
3. **Purga do domínio raiz**: Purgue a raiz do ponto final com "/" no caminho.

As purgas de cache na porta da frente são insensíveis. Além disso, são agnósticos de cadeia de consulta, o que significa que purgar um URL irá purgar todas as variações de cadeias de consulta do mesmo. 

## <a name="cache-expiration"></a>Expiração da cache
É utilizada a seguinte ordem de cabeçalhos para determinar quanto tempo um item será armazenado na nossa cache:</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: max-age=\<seconds>
3. Expira:\<http-date>

Cabeçalhos de resposta cache-Control que indicam que a resposta não será em cache como Cache-Control: privado, Cache-Control: no-cache e Cache-Control: não há loja é honrada. No entanto, se houver vários pedidos a bordo de um POP para o mesmo URL, podem partilhar a resposta. Se não houver cache-control o comportamento padrão é que o AFD cache o recurso por X quantidade de tempo onde X é escolhido aleatoriamente entre 1 a 3 dias.

## <a name="request-headers"></a>Cabeçalhos do pedido

Os seguintes cabeçalhos de pedido não serão encaminhados para um backend durante a utilização do caching.
- Comprimento do conteúdo
- Codificação de transferências

## <a name="next-steps"></a>Próximos passos

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
