---
title: Ranking semântico
titleSuffix: Azure Cognitive Search
description: Saiba como funciona o algoritmo semântico do ranking na Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: bf311eb2b2d0ff7a9c17380d2e384bc05c6f05f3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562040"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Ranking semântico em Pesquisa Cognitiva Azure

> [!IMPORTANT]
> As funcionalidades de pesquisa semântica estão em pré-visualização pública, disponíveis através da pré-visualização REST API e portal. As funcionalidades de pré-visualização são oferecidas como está, nos [Termos Complementares de Utilização,](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)e não estão garantidas a mesma implementação em disponibilidade geral. Estas características são faturas. Para mais informações, consulte [Disponibilidade e preços.](semantic-search-overview.md#availability-and-pricing)

O ranking semântico é uma extensão do pipeline de execução de consultas que melhora a precisão ressaltando os jogos superiores de um conjunto de resultados iniciais. O ranking semântico é apoiado por grandes redes baseadas em transformadores, treinadas para capturar o significado semântico dos termos de consulta, em oposição à correspondência linguística nas palavras-chave. Em contraste com o [algoritmo de classificação de semelhança padrão,](index-ranking-similarity.md)o ranker semântico usa o contexto e significado das palavras para determinar a relevância.

O ranking semântico é tanto de recursos como de tempo intensivo. A fim de completar o processamento dentro da latência esperada de uma operação de consulta, as entradas para o ranker semântico são consolidadas e reduzidas de modo a que os passos de resumo e reclasse subjacente possam ser concluídos o mais rapidamente possível.

## <a name="pre-processing"></a>Pré-processamento

Antes de marcar por relevância, o conteúdo deve ser reduzido a um número manejável de entradas que podem ser manuseadas de forma eficiente pelo ranker semântico.

1. Em primeiro lugar, a redução de conteúdo começa com o conjunto de resultados iniciais devolvido pelo algoritmo de [classificação](index-ranking-similarity.md) de semelhança padrão usado para pesquisa de palavras-chave. Para qualquer consulta, os resultados podem ser um punhado de documentos, até ao limite máximo de 1.000. Porque processar um grande número de partidas levaria muito tempo, apenas o top 50 progrediu para o ranking semântico.

   Seja qual for a contagem do documento, seja um ou 50, o conjunto de resultados iniciais estabelece a primeira iteração do corpus documental para o ranking semântico.

1. Em seguida, através do corpus, o conteúdo de cada campo em "searchFields" é extraído e combinado numa longa cadeia.

1. Após a consolidação das cordas, quaisquer cordas excessivamente longas são aparadas para garantir que o comprimento total satisfaz os requisitos de entrada do passo de resumo.

   Este exercício de aparar é o motivo pelo qual é importante posicionar os campos concisos primeiro em "searchFields", para garantir que estão incluídos na cadeia. Se tiver documentos muito grandes com campos pesados de texto, qualquer coisa após o limite máximo é ignorada.

Cada documento é agora representado por uma única corda longa.

> [!NOTE]
> A corda é composta por fichas, não por caracteres ou palavras. A tokenização é determinada em parte pela atribuição do analisador em campos pes pesjáveis. Se estiver a utilizar um analisador especializado, como o nGram ou o EdgeNGram, é melhor excluir esse campo do searchFields. Para obter informações sobre como as cordas são tokenizadas, pode rever a saída simbólica de um analisador utilizando a API do [Analisador de Teste](/rest/api/searchservice/test-analyzer)REST .

## <a name="extraction"></a>Extração

Após a redução de cordas, é agora possível passar as entradas reduzidas através da compreensão da leitura da máquina e dos modelos de representação linguística para determinar quais as frases e frases que melhor resumem o documento, em relação à consulta. Esta fase extrai o conteúdo da corda que irá avançar para o ranker semântico.

As entradas para a resumo são as longas cordas obtidas para cada documento na fase de preparação. De cada corda, o modelo de resumo encontra uma passagem que é a mais representativa. Esta passagem constitui também uma [legenda semântica](semantic-how-to-query-request.md) para o documento. Cada legenda está disponível numa versão de texto simples e numa versão de destaque, e é frequentemente menos de 200 palavras por documento.

Uma [resposta semântica](semantic-answers.md) também será devolvida se especificar o parâmetro "respostas", se a consulta foi colocada como uma questão, e se uma passagem pode ser encontrada na corda longa que é suscetível de fornecer uma resposta à pergunta.

## <a name="semantic-ranking"></a>Ranking semântico

1. As legendas são avaliadas para relevância conceptual e semântica, em relação à consulta fornecida.

   O diagrama seguinte fornece uma ilustração do que significa "relevância semântica". Considere o termo "capital", que poderia ser usado no contexto financeiro, direito, geografia ou gramática. Se uma consulta inclui termos do mesmo espaço vetorial (por exemplo, "capital" e "investimento"), um documento que também inclui tokens no mesmo cluster irá pontuar mais do que um que não.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Representação vetorial para o contexto" border="true":::

1. O @search.rerankerScore documento é atribuído a cada documento com base na relevância semântica da legenda.

1. Depois de todos os documentos serem pontuados, são listados por ordem descendente por pontuação e incluídos na carga útil de resposta de consulta. A carga útil inclui respostas, texto simples e legendas realçadas, e quaisquer campos que tenha marcado como recuperável ou especificado numa cláusula selecionada.

## <a name="next-steps"></a>Passos seguintes

O ranking semântico é oferecido nos níveis Standard, em regiões específicas. Para mais informações sobre disponibilidade e inscrição, consulte [Disponibilidade e preços.](semantic-search-overview.md#availability-and-pricing) Um novo tipo de consulta permite o ranking e as estruturas de resposta da pesquisa semântica. Para começar, [crie uma consulta semântica.](semantic-how-to-query-request.md)

Em alternativa, reveja os seguintes artigos sobre o ranking predefinido. O ranking semântico depende da semelhança para devolver os resultados iniciais. Conhecer a execução e o ranking de consultas vai dar-lhe uma ampla compreensão de como todo o processo funciona.

+ [Pesquisa de texto completo em Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Semelhança e pontuação na Pesquisa Cognitiva Azure](index-similarity-and-scoring.md)
+ [Analisadores para processamento de texto em Pesquisa Cognitiva Azure](search-analyzers.md)