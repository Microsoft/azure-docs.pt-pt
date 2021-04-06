---
title: Criar uma consulta semântica
titleSuffix: Azure Cognitive Search
description: Desafie um tipo de consulta semântica para anexar os modelos de aprendizagem profunda ao processamento de consultas, inferindo a intenção e o contexto como parte do posto de pesquisa e relevância.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: c33739124092a17acf0590f00b2f9c3c09bf894e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654667"
---
# <a name="create-a-query-for-semantic-captions-in-cognitive-search"></a>Crie uma consulta para legendas semânticas na Pesquisa Cognitiva

> [!IMPORTANT]
> A pesquisa semântica está em pré-visualização pública, disponível através do portal DE PRÉ-VISUALIZAÇÃO REST API e Azure. As funcionalidades de pré-visualização são oferecidas como está, nos [Termos Complementares de Utilização.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Estas características são faturas. Para mais informações, consulte [Disponibilidade e preços.](semantic-search-overview.md#availability-and-pricing)

Neste artigo, aprenda a formular um pedido de pesquisa que utilize o ranking semântico e devolva legendas semânticas (e [respostas semânticas](semantic-answers.md)opcionalmente), com destaques sobre os termos e frases mais relevantes. Ambas as legendas e respostas são devolvidas em consultas formuladas utilizando o tipo de consulta "semântica".

As legendas e respostas são extraídas verbatim do texto no documento de pesquisa. O subsistema semântico determina que parte do seu conteúdo tem as características de uma legenda ou resposta, mas não compõe novas frases ou frases. Por esta razão, os conteúdos que incluem explicações ou definições funcionam melhor para pesquisa semântica.

## <a name="prerequisites"></a>Pré-requisitos

+ Um serviço de pesquisa em um nível Standard (S1, S2, S3), localizado em uma dessas regiões: North Central US, West US, West US 2, East US 2, North Europe, West Europe. Se tiver um serviço S1 ou maior existente numa destas regiões, pode solicitar acesso sem ter de criar um novo serviço.

+ Acesso à pré-visualização semântica: [inscreva-se](https://aka.ms/SemanticSearchPreviewSignup)

+ Um índice de pesquisa existente contendo conteúdo inglês

+ Um cliente de pesquisa para enviar consultas

  O cliente de pesquisa deve suportar pré-visualização REST APIs no pedido de consulta. Pode utilizar [o Carteiro](search-get-started-rest.md), [Código do Estúdio Visual](search-get-started-vs-code.md)ou código que faz chamadas REST para as APIs de pré-visualização. Também pode utilizar [o Explorador de Pesquisa](search-explorer.md) no portal Azure para submeter uma consulta semântica.

+ Um [pedido de consulta](/rest/api/searchservice/preview-api/search-documents) deve incluir a opção semântica e outros parâmetros descritos neste artigo.

## <a name="whats-a-semantic-query"></a>O que é uma consulta semântica?

Na Pesquisa Cognitiva, uma consulta é um pedido parametrizado que determina o processamento de consulta e a forma da resposta. Uma *consulta semântica* adiciona parâmetros que invocam o modelo semântico de reclasse que pode avaliar o contexto e significado de resultados correspondentes, promover jogos mais relevantes para o topo, e devolver respostas e legendas semânticas.

O seguinte pedido é representativo de uma consulta semântica mínima (sem respostas).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Tal como acontece com todas as consultas na Pesquisa Cognitiva, o pedido visa a recolha de documentos de um único índice. Além disso, uma consulta semântica sofre a mesma sequência de análise, análise, digitalização e pontuação como uma consulta não semântica. 

A diferença reside na relevância e na pontuação. Tal como definido nesta versão de pré-visualização, uma consulta semântica é aquela cujos *resultados* são reclassied usando um modelo de linguagem semântica, fornecendo uma forma de emergir os jogos considerados mais relevantes pelo ranker semântico, em vez das pontuações atribuídas pelo algoritmo de classificação de semelhança padrão.

Apenas os 50 melhores jogos dos resultados iniciais podem ser classificados semânticamente, e todos incluem legendas na resposta. Opcionalmente, pode especificar um **`answer`** parâmetro sobre o pedido para extrair uma resposta potencial. Para mais informações, consulte [as respostas semânticas.](semantic-answers.md)

## <a name="query-with-search-explorer"></a>Consultar com o Explorador de procura

[O explorador de pesquisa](search-explorer.md) foi atualizado para incluir opções para consultas semânticas. Estas opções tornam-se visíveis no portal após completar os seguintes passos:

1. [Inscreva-se](https://aka.ms/SemanticSearchPreviewSignup) e admita do seu serviço de pesquisa no programa de pré-visualização

1. Abra o portal com esta sintaxe: `https://portal.azure.com/?feature.semanticSearch=true`

As opções de consulta incluem comutadores para permitir consultas semânticas, searchFields e correção de feitiços. Também pode colar os parâmetros de consulta necessários na cadeia de consulta.

:::image type="content" source="./media/semantic-search-overview/search-explorer-semantic-query-options.png" alt-text="Opções de consulta no explorador de pesquisa" border="true":::

## <a name="query-using-rest"></a>Consulta usando REST

Utilize os [Documentos de Busca (pré-visualização REST)](/rest/api/searchservice/preview-api/search-documents) para formular o pedido programáticamente.

Uma resposta inclui legendas e destaque automaticamente. Se quiser que a resposta inclua correção ortográfica ou respostas, adicione um opcional **`speller`** ou **`answers`** parâmetro no pedido.

O exemplo a seguir utiliza o índice de amostras de hotéis para criar um pedido de consulta semântica com respostas e legendas semânticas:

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

A tabela seguinte resume os parâmetros de consulta utilizados numa consulta semântica para que possa vê-los holisticamente. Para obter uma lista de todos os parâmetros, consulte [Documentos de Pesquisa (pré-visualização REST)](/rest/api/searchservice/preview-api/search-documents)

| Parâmetro | Tipo | Description |
|-----------|-------|-------------|
| consultaType | String | Valores válidos incluem simples, completo e semântico. É necessário um valor de "semântica" para consultas semânticas. |
| queryLanguage | String | Necessário para consultas semânticas. Atualmente, apenas "en-us" é implementado. |
| searchFields | String | Uma lista delimitada por vírgulas de campos pesjáveis. Especifica os campos sobre os quais ocorre o ranking semântico, a partir dos quais são extraídas legendas e respostas. </br></br>Em contraste com os tipos de consulta simples e completa, a ordem em que os campos estão listados determina a precedência. Para obter mais instruções de utilização, consulte [o passo 2: Definir campos de pesquisa](#searchfields). |
| soletrador | String | Parâmetro opcional, não específico para consultas semânticas, que corrige os termos mal escritos antes de chegarem ao motor de busca. Para obter mais informações, consulte [adicionar correção de feitiços a consultas](speller-how-to-add.md). |
| respostas |String | Parâmetros opcionais que especificam se as respostas semânticas estão incluídas no resultado. Atualmente, apenas é implementada a "extração". As respostas podem ser configuradas para devolver um máximo de cinco. O padrão é um. Este exemplo mostra uma contagem de três respostas: \| "contagem extrativa3". Para obter mais informações, consulte [respostas semânticas de retorno.](semantic-answers.md)|

### <a name="formulate-the-request"></a>Formular o pedido

Esta secção percorre os parâmetros de consulta necessários para a procura semântica.

#### <a name="step-1-set-querytype-and-querylanguage"></a>Passo 1: Definir consultaType e queryLanguage

Adicione os seguintes parâmetros ao resto. Ambos os parâmetros são necessários.

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

A queryLanguage deve ser consistente com quaisquer [analisadores de linguagem](index-add-language-analyzers.md) atribuídos às definições de campo no esquema de índice. Se a queryLanguage for "en-us", então qualquer analisador de idiomas também deve ser uma variante inglesa ("en.microsoft" ou "en.lucene"). Quaisquer analisadores linguísticos agnósticos, tais como palavra-chave ou simples, não têm qualquer conflito com valores de dúvida.

Num pedido de consulta, se também estiver a utilizar [a correção ortográfica,](speller-how-to-add.md)a consultalanguage que definiu aplica-se igualmente a soletradores, respostas e legendas. Não há substituição de peças individuais. 

Embora o conteúdo de um índice de pesquisa possa ser composto em vários idiomas, a entrada de consulta é mais provável em um. O motor de busca não verifica a compatibilidade da queryLanguage, do analisador de línguas e do idioma em que o conteúdo é composto, por isso certifique-se de que as consultas de âmbito em conformidade para evitar produzir resultados incorretos.

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>Passo 2: Definir campos de busca

O parâmetro searchFields é usado para identificar passagens a avaliar para "semelhança semântica" com a consulta. Para a pré-visualização, não recomendamos deixar a searchFields em branco, uma vez que o modelo requer uma pista sobre quais os campos mais importantes para processar.

A ordem dos campos de busca é crítica. Se já utilizar searchFields no código existente para consultas simples ou completas de Lucene, reveja este parâmetro para verificar se há ordem de campo ao mudar para um tipo de consulta semântica.

Para dois ou mais campos de pesquisa:

+ Inclua apenas campos de cordas e campos de cordas de alto nível em coleções. Se por acaso incluir campos não-cordas ou campos de nível inferior numa coleção, não há erro, mas esses campos não serão usados no ranking semântico.

+ O primeiro campo deve ser sempre conciso (como um título ou nome), idealmente com menos de 25 palavras.

+ Se o índice tiver um campo URL que seja textual (legível humano como `www.domain.com/name-of-the-document-and-other-details` , e não máquina focada como ), `www.domain.com/?id=23463&param=eis` coloque-o em segundo lugar na lista (ou primeiro se não houver um campo de título conciso).

+ Siga esses campos por campos descritivos onde a resposta a consultas semânticas pode ser encontrada, como o conteúdo principal de um documento.

Se apenas um campo especificado, utilize um campo descritivo onde possa ser encontrada a resposta a consultas semânticas, como o conteúdo principal de um documento. 

#### <a name="step-3-remove-orderby-clauses"></a>Passo 3: Remover cláusulas de ordemBy

Remova quaisquer cláusulas de encomenda Por cláusulas, se existirem num pedido existente. A pontuação semântica é usada para encomendar resultados, e se incluir lógica de classificação explícita, um erro HTTP 400 é devolvido.

#### <a name="step-4-add-answers"></a>Passo 4: Adicionar respostas

Opcionalmente, adicione "respostas" se quiser incluir um processamento adicional que forneça uma resposta. As respostas (e legendas) são extraídas de passagens encontradas em campos listados em searchFields. Certifique-se de incluir campos ricos em conteúdo em searchFields para obter as melhores respostas numa resposta. Para mais informações, consulte [Como devolver respostas semânticas.](semantic-answers.md)

#### <a name="step-5-add-other-parameters"></a>Passo 5: Adicionar outros parâmetros

Desa estada quaisquer outros parâmetros que pretenda no pedido. Parâmetros como [soletrador](speller-how-to-add.md), [selecione](search-query-odata-select.md), e contam melhorar a qualidade do pedido e a legibilidade da resposta.

Opcionalmente, pode personalizar o estilo de destaque aplicado às legendas. As legendas aplicam-se a formatação de destaque sobre as passagens-chave no documento que resumem a resposta. A predefinição é `<em>`. Se quiser especificar o tipo de formatação (por exemplo, fundo amarelo), pode definir o destaquePreTag e realçar oPostTag.

## <a name="evaluate-the-response"></a>Avaliar a resposta

Como em todas as consultas, uma resposta é composta por todos os campos marcados como recuperáveis, ou apenas os campos listados no parâmetro selecionado. Inclui a pontuação original de relevância, e também pode incluir uma contagem, ou resultados em lotados, dependendo da forma como formulou o pedido.

Numa consulta semântica, a resposta tem elementos adicionais: uma nova pontuação de relevância semântica classificada, legendas em texto simples e com destaques, e opcionalmente uma resposta.

Numa aplicação de cliente, pode estruturar a página de pesquisa para incluir uma legenda como a descrição da partida, em vez de todo o conteúdo de um campo específico. Isto é útil quando os campos individuais são demasiado densos para a página de resultados da pesquisa.

A resposta para a consulta de exemplo acima devolve o seguinte jogo como a escolha principal. As legendas são devolvidas automaticamente, com texto simples e versões realçadas. As respostas são omitidas do exemplo porque não se pode ser determinado para esta consulta e corpus em particular.

```json
"@odata.count": 35,
"@search.answers": [],
"value": [
    {
        "@search.score": 1.8810667,
        "@search.rerankerScore": 1.1446577133610845,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Luxury. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Luxury. New Luxury Hotel. Be the first to stay.<strong> Bay</strong> views from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Luxury"
    },
```

## <a name="next-steps"></a>Passos seguintes

Lembre-se que o ranking semântico e as respostas são construídas sobre um conjunto de resultados iniciais. Qualquer lógica que melhore a qualidade dos resultados iniciais irá levar para a procura semântica. Como passo seguinte, reveja as funcionalidades que contribuem para os resultados iniciais, incluindo os analisadores que afetam a forma como as cordas são tokenizadas, perfis de pontuação que podem afinar resultados, e o algoritmo de relevância padrão.

+ [Analisadores para processamento de texto](search-analyzers.md)
+ [Algoritmo de classificação de semelhança](index-similarity-and-scoring.md)
+ [Perfis de classificação](index-add-scoring-profiles.md)
+ [Visão geral da pesquisa semântica](semantic-search-overview.md)
+ [Algoritmo de classificação semântica](semantic-ranking.md)
