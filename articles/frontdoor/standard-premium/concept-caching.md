---
title: 'Porta da frente Azure: Caching'
description: Este artigo ajuda-o a compreender o comportamento do Azure Front Door Standard/Premium com regras de encaminhamento que permitiram o caching.
services: front-door
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 63ea252a4b4c673ae3028adb7ab793ac21fb2e99
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564590"
---
# <a name="caching-with-azure-front-door-standardpremium-preview"></a>Caching com Azure Front Door Standard/Premium (Pré-visualização)

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

Neste artigo, você vai aprender como as rotas e o conjunto de regras da Porta Frontal (Preview) se comportam quando tiver o caching ativado. Azure Front Door é uma moderna Rede de Entrega de Conteúdos (CDN) com aceleração dinâmica do site e equilíbrio de carga.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="request-methods"></a>Métodos de pedido

Apenas o método de pedido GET pode gerar conteúdo em cache na Porta Frontal Azure. Todos os outros métodos de pedido são sempre proximamente através da rede.

## <a name="delivery-of-large-files"></a>Entrega de grandes ficheiros

Front Door Standard/Premium (Preview) fornece ficheiros grandes sem uma tampa no tamanho do ficheiro. A Porta da Frente usa uma técnica chamada "pancada de objetos". Quando um ficheiro grande é solicitado, a Porta frontal recupera pedaços menores do ficheiro a partir da origem. Depois de receber um pedido de ficheiro completo ou byte-range, o ambiente front door solicita o ficheiro a partir das origens em pedaços de 8 MB.

Depois de o pedaço chegar ao ambiente da Porta da Frente, é em cache e imediatamente servido ao utilizador. Porta da frente, em seguida, pré-buscar o próximo pedaço em paralelo. Esta pré-busca garante que o conteúdo permanece um pedaço à frente do utilizador, o que reduz a latência. Este processo continua até que todo o ficheiro seja descarregado (se solicitado) ou o cliente fechar a ligação.

Para obter mais informações sobre o pedido byte-range, leia [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
A Porta da Frente caches quaisquer pedaços como eles são recebidos para que todo o arquivo não precise ser em cache na cache da porta da frente. Os pedidos subsequentes para o ficheiro ou intervalos de byte são servidos a partir da cache. Se os pedaços não estiverem todos em cache, a pré-busca é usada para pedir pedaços do backend. Esta otimização baseia-se na capacidade da origem de suportar pedidos de alcance byte. Se a origem não suporta pedidos de byte-range, esta otimização não é eficaz.

## <a name="file-compression"></a>Compressão de ficheiros

Consulte para melhorar o desempenho comprimindo ficheiros na Porta Frontal Azure.

## <a name="query-string-behavior"></a>Comportamento da corda de consulta

Com a Porta Frontal, pode controlar como os ficheiros são cached para um pedido web que contém uma cadeia de consulta. Num pedido web com uma cadeia de consulta, a cadeia de consulta é essa parte do pedido que ocorre após um ponto de interrogação (?). Uma cadeia de consulta pode conter um ou mais pares de valor-chave, nos quais o nome do campo e o seu valor são separados por um sinal igual (=). Cada par de valores-chave é separado por um ampersand (&). Por exemplo, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Se há mais de um par de valores-chave numa sequência de consulta de um pedido, então a encomenda deles não importa.

* **Ignore as cordas de consulta**: Neste modo, a Porta Frontal passa as cordas de consulta do solicitador para a origem no primeiro pedido e caches do ativo. Todos os pedidos subsequentes para o ativo que são servidos a partir do ambiente da Porta frontal ignoram as cordas de consulta até que o ativo em cache expire.

* **Cache cada URL único**: Neste modo, cada pedido com um URL único, incluindo a cadeia de consulta, é tratado como um ativo único com a sua própria cache. Por exemplo, a resposta da origem de um pedido `www.example.ashx?q=test1` de pedido é em cache no ambiente da Porta da Frente e devolvida para caches subsequentes com a mesma cadeia de consulta. Um pedido `www.example.ashx?q=test2` é em cache como um ativo separado com a sua própria definição de tempo-a-vida.
* Também pode utilizar o Rule set para especificar o comportamento **da cadeia de perguntas de teclas** de cache, para incluir ou excluir parâmetros especificados quando a tecla cache for gerada. Por exemplo, a chave de cache padrão é: /foo/imagem/asset.html, e o pedido de amostra é `https://contoso.com//foo/image/asset.html?language=EN&userid=100&sessionid=200` . Há uma regra definida para excluir a cadeia de consulta 'userid'. Então a chave de cache de cadeia de consulta seria `/foo/image/asset.html?language=EN&sessionid=200` .

## <a name="cache-purge"></a>Purga de cache

Consulte a purga de cache.

## <a name="cache-expiration"></a>Expiração da cache
É utilizada a seguinte ordem de cabeçalhos para determinar quanto tempo um item será armazenado na nossa cache:</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: max-age=\<seconds>
3. Expira: \<http-date>

Cache-Control cabeçalhos de resposta que indicam que a resposta não será em cache, como o Cache-Control: privado, Cache-Control: no-cache e Cache-Control: não há loja é honrada.  Se não houver Cache-Control presente, o comportamento padrão é que a Porta Frontal cacheá o recurso durante x quantidade de tempo. Onde X é escolhido aleatoriamente entre 1 a 3 dias.

## <a name="request-headers"></a>Cabeçalhos do pedido

Os seguintes cabeçalhos de pedido não serão encaminhados para uma origem quando utilizar o caching.
* Comprimento do conteúdo
* Transfer-Encoding

## <a name="cache-duration"></a>Duração da cache

A duração da cache pode ser configurada no Conjunto de Regras. A duração da cache definida através do Conjunto de Regras é uma verdadeira sobreposição de cache. O que significa que usará o valor de substituição independentemente do cabeçalho de resposta de origem.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [as condições de jogo definidas por regras](concept-rule-set-match-conditions.md)
* Saiba mais sobre [as ações definidas por regras](concept-rule-set-actions.md)