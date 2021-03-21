---
title: Ranking semântico
titleSuffix: Azure Cognitive Search
description: Saiba como funciona o algoritmo semântico do ranking na Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: bb65a53f1ba6e97a39bd0c0170c5c41da38aee8b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720513"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Ranking semântico em Pesquisa Cognitiva Azure

> [!IMPORTANT]
> As funcionalidades de pesquisa semântica estão em pré-visualização pública, disponíveis apenas através da pré-visualização REST API. As funcionalidades de pré-visualização são oferecidas como está, nos [Termos Complementares de Utilização,](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)e não estão garantidas a mesma implementação em disponibilidade geral. Estas características são faturas. Para mais informações, consulte [Disponibilidade e preços.](semantic-search-overview.md#availability-and-pricing)

O ranking semântico é uma extensão do pipeline de execução de consultas que melhora a precisão e a recuperação, relembr o topo de um conjunto de resultados iniciais. O ranking semântico é apoiado por modelos de compreensão de leitura de máquinas de última geração, treinados para consultas expressas em linguagem natural em oposição à correspondência linguística em palavras-chave. Em contraste com o [algoritmo de classificação de semelhança padrão,](index-ranking-similarity.md)o ranker semântico usa o contexto e significado das palavras para determinar a relevância.

O ranking semântico é tanto de recursos como de tempo intensivo. A fim de completar o processamento dentro da latência esperada de uma operação de consulta, as entradas são consolidadas e simplificadas de modo a que a resumo e a análise possam ser concluídas o mais rapidamente possível.

## <a name="preparation-for-semantic-ranking"></a>Preparação para o ranking semântico

Antes de marcar por relevância, o conteúdo deve ser reduzido a uma quantidade de parâmetros que podem ser manuseados de forma eficiente pelo ranker semântico. A redução de conteúdo inclui a seguinte sequência de passos.

1. A redução de conteúdo começa por utilizar os resultados iniciais devolvidos pelo algoritmo de [classificação](index-ranking-similarity.md) de semelhança padrão utilizado para a pesquisa de palavras-chave. Os resultados da pesquisa podem incluir até 1.000 partidas, mas o ranking semântico só irá processar o top 50. 

   Dada a consulta, os resultados iniciais podem ser muito menos de 50, dependendo de quantos fósforos foram encontrados. Qualquer que seja a contagem do documento, o conjunto de resultados iniciais é o corpus documental para o ranking semântico.

1. Em todo o corpus de documento, o conteúdo de cada campo em "searchFields" é extraído e combinado numa longa cadeia.

1. As cordas excessivamente compridas são aparadas para garantir que o comprimento total satisfaz os requisitos de entrada do modelo de resumo. Este exercício de aparar é o motivo pelo qual é importante posicionar os campos concisos primeiro em "searchFields", para garantir que estão incluídos na cadeia. Se tiver documentos muito grandes com campos pesados de texto, qualquer coisa após o limite máximo é ignorada.

Cada documento é agora representado por uma única corda longa.

> [!NOTE]
> As entradas de parâmetros para os modelos são símbolos não caracteres ou palavras. A tokenização é determinada em parte pela atribuição do analisador em campos pes pesjáveis. Para obter informações sobre como as cordas são tokenizadas, pode rever a saída simbólica de um analisador utilizando a API do [Analisador de Teste](/rest/api/searchservice/test-analyzer)REST .
>
> Atualmente nesta pré-visualização, as cordas longas podem ser um máximo de 8.000 fichas de tamanho. Se a pesquisa não fornecer uma resposta esperada a partir de um documento profundo, saber sobre aparar conteúdos ajuda-o a entender o porquê. 

## <a name="summarization"></a>Resumo

Após a redução das cordas, é agora possível passar os parâmetros através da compreensão da leitura da máquina e da representação linguística para determinar quais as frases e frases que melhor resumem o modelo, em relação à consulta.

As entradas para a resumo são a longa cadeia da fase de preparação. A partir dessa entrada, o modelo de resumo avalia o conteúdo para encontrar passagens que sejam mais representativas.

A saída é uma [legenda semântica,](semantic-how-to-query-request.md)em texto simples e com destaques. A legenda é menor do que a corda longa, geralmente menos de 200 palavras por documento, e é considerada a mais representativa do documento. 

Uma [resposta semântica](semantic-answers.md) também será devolvida se especificar o parâmetro "respostas", se a consulta foi colocada como uma pergunta, e se uma passagem pode ser encontrada na corda longa que parece uma resposta plausível à pergunta.

## <a name="scoring-and-ranking"></a>Pontuação e classificação

Neste momento, tem agora legendas para cada documento. As legendas são avaliadas para relevância para a consulta.

1. A pontuação é determinada avaliando cada legenda para relevância conceptual e semântica, em relação à consulta fornecida.

   O diagrama seguinte fornece uma ilustração do que significa "relevância semântica". Considere o termo "capital", que poderia ser usado no contexto financeiro, direito, geografia ou gramática. Se uma consulta inclui termos do mesmo espaço vetorial (por exemplo, "capital" e "investimento"), um documento que também inclui tokens no mesmo cluster irá pontuar mais do que um que não.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Representação vetorial para o contexto" border="true":::

1. A saída desta fase é @search.rerankerScore atribuída a cada documento. Uma vez que todos os documentos são marcados, eles são listados em ordem descendente e incluídos na carga útil de resposta de consulta. A carga útil inclui respostas, texto simples e legendas realçadas, e quaisquer campos que tenha marcado como recuperável ou especificado numa cláusula selecionada.

## <a name="next-steps"></a>Passos seguintes

O ranking semântico é oferecido nos níveis Standard, em regiões específicas. Para mais informações sobre a disponibilidade e inscreva-se, consulte [Disponibilidade e preços.](semantic-search-overview.md#availability-and-pricing) Um novo tipo de consulta permite o ranking de relevância e as estruturas de resposta da pesquisa semântica. Para começar, [crie uma consulta semântica.](semantic-how-to-query-request.md)

Em alternativa, reveja os seguintes artigos sobre o ranking predefinido. O ranking semântico depende da semelhança para devolver os resultados iniciais. Conhecer a execução e o ranking de consultas vai dar-lhe uma ampla compreensão de como todo o processo funciona.

+ [Pesquisa de texto completo em Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Semelhança e pontuação na Pesquisa Cognitiva Azure](index-similarity-and-scoring.md)
+ [Analisadores para processamento de texto em Pesquisa Cognitiva Azure](search-analyzers.md)