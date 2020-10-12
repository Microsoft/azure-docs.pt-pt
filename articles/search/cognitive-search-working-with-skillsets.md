---
title: Conceitos de skillset e fluxo de trabalho
titleSuffix: Azure Cognitive Search
description: Skillsets são onde você é autor de um oleoduto de enriquecimento de IA em Azure Cognitive Search. Aprenda conceitos e detalhes importantes sobre a composição skillset.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: f1d8715fcadeda5ccd1a98192a70939b0c359c88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84976681"
---
# <a name="skillset-concepts-in-azure-cognitive-search"></a>Conceitos de Skillset em Pesquisa Cognitiva Azure

Este artigo destina-se a desenvolvedores que precisam de uma compreensão mais profunda dos conceitos e composição de skillset, e assume familiaridade com o processo de enriquecimento de IA. Se é novo neste conceito, comece com [o enriquecimento de IA na Azure Cognitive Search.](cognitive-search-concept-intro.md)

## <a name="introducing-skillsets"></a>Introdução de skillsets

Um skillset é um recurso reutilizável na Pesquisa Cognitiva Azure que está ligado a um indexante, e especifica uma coleção de habilidades usadas para analisar, transformar e enriquecer o conteúdo de texto ou imagem durante a indexação. As competências têm entradas e saídas, e muitas vezes a saída de uma habilidade torna-se a entrada de outra numa cadeia ou sequência de processos.

Um skillset tem três propriedades principais:

+ `skills`, uma recolha não ordenado de competências para as quais a plataforma determina a sequência de execução com base nas entradas necessárias para cada habilidade.
+ `cognitiveServices`, a chave de um recurso de Serviços Cognitivos que executa o processamento de imagem e texto para habilidades que incluem habilidades incorporadas.
+ `knowledgeStore`, (opcional) uma conta de Armazenamento Azure onde serão projetados os seus documentos enriquecidos. Os documentos enriquecidos também são consumidos por índices de pesquisa.

Skillsets são da autoria em JSON. Segue-se um exemplo ligeiramente simplificado deste [skillset de avaliação hoteleira,](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json)usado para ilustrar conceitos neste artigo. 

As duas primeiras competências são mostradas abaixo:

+ Skill #1 é uma [habilidade de Divisão](cognitive-search-skill-textsplit.md) de Texto que aceita o conteúdo do campo "reviews_text" como entrada, e divide esse conteúdo em "páginas" de 5000 caracteres como saída.
+ Skill #2 é uma [habilidade de Deteção de Sentimento](cognitive-search-skill-sentiment.md) aceita "páginas" como entrada, e produz um novo campo chamado "Sentimento" como saída que contém os resultados da análise de sentimento.


```json
{
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "#1",
            "description": null,
            "context": "/document/reviews_text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "name": "#2",
            "description": null,
            "context": "/document/reviews_text/pages/*",
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text/pages/*",
                }
            ],
            "outputs": [
                {
                    "name": "score",
                    "targetName": "Sentiment"
                }
            ]
        },
  "cognitiveServices": null,
  "knowledgeStore": {  }
}
```
> [!NOTE]
> Você pode construir habilidades complexas com looping e ramificação, usando a [habilidade condicional](cognitive-search-skill-conditional.md) para criar as expressões. A sintaxe baseia-se na notação do caminho do [ponteiro JSON,](https://tools.ietf.org/html/rfc6901) com algumas modificações para identificar nós na árvore de enriquecimento. A `"/"` atravessa um nível mais baixo na árvore e  `"*"` atua como um operador para cada operador no contexto. Numerosos exemplos neste artigo ilustram a sintaxe. 

### <a name="enrichment-tree"></a>Árvore de enriquecimento

Na progressão de etapas num gasoduto de [enriquecimento,](cognitive-search-concept-intro.md#enrichment-steps)o processamento de conteúdos segue a fase *de rachamento* do documento onde o texto e as imagens são extraídos da fonte. O conteúdo da imagem pode então ser encaminhado para competências que especificam o processamento de imagem, enquanto o conteúdo de texto é feito na fila para processamento de texto. Para documentos de origem que contenham grandes quantidades de texto, pode definir um modo de *análise* no indexante para segmentar texto em pedaços menores para um processamento mais ideal. 

![Loja de conhecimento em diagrama de pipeline](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Loja de conhecimento em diagrama de pipeline")

Uma vez que um documento está no oleoduto de enriquecimento, é representado como uma árvore de conteúdo e enriquecimentos associados. Esta árvore é instantânea como a saída do documento rachando.  O formato da árvore de enriquecimento permite que o gasoduto de enriquecimento anexe metadados até tipos de dados primitivos, não é um objeto JSON válido, mas pode ser projetado num formato JSON válido. O quadro que se segue mostra o estado de um documento que entra no gasoduto de enriquecimento:

|Fonte de dados\Modo de análise|Predefinição|JSON, JSON Lines & CSV|
|---|---|---|
|Armazenamento de Blobs|/documento/conteúdo<br>/documento/normalized_images/*<br>…|/documento/{key1}<br>/documento/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|N/D |
|BD do Cosmos|/documento/{key1}<br>/documento/{key2}<br>…|N/D|

 À medida que as habilidades executam, adicionam novos nós à árvore do enriquecimento. Estes novos nós podem então ser usados como entradas para habilidades a jusante, projetando para a loja de conhecimentos, ou mapeando para campos de indexação. Os enriquecimentos não são mutáveis: uma vez criados, os nós não podem ser editados. À medida que as suas habilidades se tornam mais complexas, também a sua árvore de enriquecimento, mas nem todos os nós na árvore do enriquecimento precisam de chegar ao índice ou à loja de conhecimento. 

Pode persistir seletivamente apenas um subconjunto dos enriquecimentos para o índice ou para a loja de conhecimentos.

### <a name="context"></a>Contexto

Cada habilidade requer um contexto. Um contexto determina:

+ O número de vezes que a habilidade executa, com base nos nós selecionados. Para valores de contexto de coleção de tipo, a adição de um `/*` no final resultará na invocação da habilidade uma vez por cada instância na coleção. 

+ Onde na árvore de enriquecimento são adicionadas as saídas de habilidades. As saídas são sempre adicionadas à árvore como crianças do nó de contexto. 

+ Forma das entradas. Para coleções de vários níveis, definir o contexto para a coleção dos pais irá afetar a forma da entrada para a habilidade. Por exemplo, se tiver uma árvore de enriquecimento com uma lista de países/regiões, cada uma enriquecida com uma lista de estados contendo uma lista de códigos ZIP.

|Contexto|Input|Forma de Entrada|Invocação de Habilidades|
|-------|-----|--------------|----------------|
|`/document/countries/*` |`/document/countries/*/states/*/zipcodes/*` |Uma lista de todos os códigos ZIP no país/região |Uma vez por país/região |
|`/document/countries/*/states/*` |«/documento/países/*/estados/*/zipcodes/*». |Uma lista de códigos ZIP no estado | Uma vez por combinação de país/região e estado|

## <a name="generate-enriched-data"></a>Gerar dados enriquecidos 

Usando o [skillset de avaliações](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json) de hotéis como um ponto de referência, vamos olhar:

+ Como a árvore de enriquecimento evolui com a execução de cada habilidade
+ Como o contexto e as entradas funcionam para determinar quantas vezes uma habilidade executa
+ Qual a forma da entrada é baseada no contexto

Um "documento" dentro do processo de enriquecimento representa uma única linha (uma revisão hoteleira) dentro do ficheiro de origem hotel_reviews.csv.

### <a name="skill-1-split-skill"></a>Skill #1: Habilidade dividida

Quando o conteúdo da origem consiste em grandes pedaços de texto, é útil que o parta em componentes menores para uma maior precisão da linguagem, sentimento e deteção de frases-chave. Há dois grãos disponíveis: páginas e frases. Uma página é composta por aproximadamente 5000 caracteres.

Uma habilidade dividida em texto é tipicamente a primeira numa habilidade.

```json
      "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
      "name": "#1",
      "description": null,
      "context": "/document/reviews_text",
      "defaultLanguageCode": "en",
      "textSplitMode": "pages",
      "maximumPageLength": 5000,
      "inputs": [
        {
          "name": "text",
          "source": "/document/reviews_text"
        }
      ],
      "outputs": [
        {
          "name": "textItems",
          "targetName": "pages"
        }
```

Com o contexto de habilidade `"/document/reviews_text"` de, a habilidade dividida executará uma vez para o `reviews_text` . A produção de habilidades é uma lista onde o `reviews_text` é dividido em 5000 segmentos de caracteres. A saída da habilidade dividida é nomeada `pages` e é adicionada à árvore de enriquecimento. A `targetName` funcionalidade permite-lhe renomear uma produção de habilidades antes de ser adicionada à árvore de enriquecimento.

A árvore de enriquecimento tem agora um novo nó colocado no contexto da habilidade. Este nó está disponível para qualquer habilidade, projeção ou mapeamento de campo de saída. Conceptualmente, a árvore parece o seguinte:

![árvore de enriquecimento após rachadura de documento](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Árvore de enriquecimento após rachadura de documento e antes da execução de habilidades")

O nó de raiz para todos os enriquecimentos é `"/document"` . Ao trabalhar com indexadores blob, o `"/document"` nó terá nódulos infantis de `"/document/content"` e `"/document/normalized_images"` . Ao trabalhar com dados de CSV, como estamos neste exemplo, os nomes das colunas mapeiam para nós abaixo `"/document"` . 

Para aceder a qualquer um dos enriquecimentos adicionados a um nó por uma habilidade, é necessário todo o caminho para o enriquecimento. Por exemplo, se pretender utilizar o texto do ```pages``` nó como entrada para outra habilidade, terá de especirá-lo como ```"/document/reviews_text/pages/*"``` .
 
 ![árvore de enriquecimento após habilidade #1](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Árvore de enriquecimento após habilidade #1 executa")

### <a name="skill-2-language-detection"></a>Deteção de linguagens #2 habilidade

Os documentos de revisão do hotel incluem feedback do cliente expresso em vários idiomas. A habilidade de deteção de linguagem determina qual a língua utilizada. O resultado é então passado para a extração de frases-chave e deteção de sentimentos, tendo em conta a linguagem ao detetar sentimentos e frases.

Embora a habilidade de deteção de linguagem seja a terceira habilidade (habilidade #3) definida no skillset, é a próxima habilidade a executar. Uma vez que não é bloqueado exigindo quaisquer entradas, executará em paralelo com a habilidade anterior. Tal como a habilidade dividida que a precedeu, a habilidade de deteção de linguagem também é invocada uma vez para cada documento. A árvore do enriquecimento tem agora um novo nó para a linguagem.

 ![árvore de enriquecimento após habilidade #2](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Árvore de enriquecimento após habilidade #2 executa")
 
 ### <a name="skill-3-key-phrases-skill"></a>Habilidade #3: Habilidade de frases-chave 

Dado o contexto das `/document/reviews_text/pages/*` frases-chave, a habilidade será invocada uma vez para cada um dos itens da `pages` coleção. A saída da habilidade será um nó sob o elemento de página associado. 

 Agora deve ser capaz de olhar para o resto das habilidades na habilidade e visualizar como a árvore dos enriquecimentos continuará a crescer com a execução de cada habilidade. Algumas habilidades, como a habilidade de fusão e a habilidade do shaper, também criam novos nós, mas apenas usam dados de nós existentes e não criam novos enriquecimentos líquidos.

![árvore de enriquecimento depois de todas as habilidades](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Árvore de enriquecimento depois de todas as habilidades")

As cores dos conectores na árvore acima indicam que os enriquecimentos foram criados por diferentes habilidades e os nós terão de ser abordados individualmente e não farão parte do objeto devolvido ao selecionar o nó-mãe.

## <a name="save-enrichments"></a>Salvar enriquecimentos

Na Pesquisa Cognitiva Azure, um indexante salva a saída que cria. Uma das saídas é sempre um [índice pesmável.](search-what-is-an-index.md) Especificar um índice é um requisito, e quando se anexa um skillset, os dados ingeridos por um índice incluem a substância dos enriquecimentos. Normalmente, as saídas de habilidades específicas, tais como frases-chave ou pontuações de sentimento, são ingeridas no índice num campo criado para o efeito.

Opcionalmente, um indexante também pode enviar a saída para uma [loja de conhecimento](knowledge-store-concept-intro.md) para consumo em outras ferramentas ou processos. Uma loja de conhecimento é definida como parte do skillset. A sua definição determina se os seus documentos enriquecidos são projetados como tabelas ou objetos (ficheiros ou bolhas). As projeções tabulares são adequadas para análises interativas em ferramentas como o Power BI, enquanto ficheiros e bolhas são normalmente usados em ciência de dados ou processos semelhantes. Nesta secção, você vai aprender como a composição skillset pode moldar as tabelas ou objetos que você deseja projetar.

### <a name="projections"></a>Projeções

Para conteúdos direcionados para uma loja de conhecimento, irá querer considerar como o conteúdo é estruturado. *Projeção* é o processo de seleção dos nós da árvore do enriquecimento e criação de uma expressão física deles na loja de conhecimento. As projeções são formas personalizadas do documento (conteúdo e enriquecimentos) que podem ser saídas como projeções de tabela ou objeto. Para saber mais sobre trabalhar com projeções, consulte [trabalhar com projeções.](knowledge-store-projection-overview.md)

![Opções de mapeamento de campo](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Opções de mapeamento de campo para o gasoduto de enriquecimento")

### <a name="sourcecontext"></a>Texto de Origem

O `sourceContext` elemento é utilizado apenas em entradas e projeções de habilidades. É usado para construir objetos aninhados de vários níveis. Pode ser necessário criar um novo objeto para passá-lo como entrada para uma habilidade ou projeto na loja de conhecimento. Como os nós de enriquecimento podem não ser um objeto JSON válido na árvore de enriquecimento e fazer referência a um nó na árvore apenas devolve esse estado do nó quando foi criado, usando os enriquecimentos como entradas ou projeções de habilidades requer que você crie um objeto JSON bem formado. `sourceContext`Permite-lhe construir um objeto hierárquico e anónimo, que exigiria múltiplas habilidades se estivesse apenas a usar o contexto. 

A utilização `sourceContext` é mostrada nos seguintes exemplos. Veja a produção de habilidades que gerou um enriquecimento para determinar se é um objeto JSON válido e não um tipo primitivo.

### <a name="slicing-projections"></a>Projeções de corte

Ao definir um grupo de projeção de mesa, um único nó na árvore de enriquecimento pode ser cortado em várias tabelas relacionadas. Se adicionar uma tabela com um caminho de origem que é filho de uma projeção de mesa existente, o nó de criança resultante não será uma criança da projeção da tabela existente, mas será projetado para a nova tabela, relacionada. Esta técnica de corte permite-lhe definir um único nó numa habilidade de formador que pode ser a fonte de todas as suas projeções de mesa. 

### <a name="shaping-projections"></a>Modelar projeções

Há duas formas de definir uma projeção:

+ Utilize a habilidade do Modelador de Texto para criar um novo nó que é o nó de raiz para todos os enriquecimentos que está a projetar. Então, nas suas projeções, só referenciaria a saída da habilidade do shaper.

+ Utilize uma forma inline uma projeção dentro da própria definição de projeção.

A abordagem do shaper é mais verbosa do que a formação inline, mas garante que todas as mutações da árvore de enriquecimento estão contidas nas habilidades e que a saída é um objeto que pode ser reutilizado. Em contraste, a formação inline permite-lhe criar a forma de que necessita, mas é um objeto anónimo e só está disponível para a projeção para a qual é definido. As abordagens podem ser usadas em conjunto ou separadamente. O skillset criado para si no fluxo de trabalho do portal contém ambos. Usa uma habilidade de formador para as projeções da mesa, mas também usa a formação inline para projetar a tabela de frases-chave.

Para estender o exemplo, pode optar por remover a modelação inline e usar uma habilidade de modelador para criar um novo nó para as frases-chave. Para criar uma forma projetada em três tabelas, ou seja, `hotelReviewsDocument` `hotelReviewsPages` as `hotelReviewsKeyPhrases` duas opções são descritas nas seguintes secções.

#### <a name="shaper-skill-and-projection"></a>Habilidade e projeção do shaper

Este 

> [!Note]
> Algumas colunas da tabela de documentos foram removidas deste exemplo por brevidade.
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

Com o `tableprojection` nó definido na secção `outputs` acima, podemos agora usar a função de corte para projetar partes do `tableprojection` nó em diferentes tabelas:

> [!Note]
> Isto é apenas um corte da projeção dentro da configuração da loja de conhecimento.
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>Projeções de formação inline

A abordagem de formação inline não requer uma habilidade de formador, uma vez que todas as formas necessárias para as projeções são criadas no momento em que são necessárias. Para projetar os mesmos dados que o exemplo anterior, a opção de projeção em linha seria assim:

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
Uma observação de ambas as abordagens é a forma como os valores `"Keyphrases"` são projetados utilizando o `"sourceContext"` . O `"Keyphrases"` nó, que contém uma coleção de cordas, é em si uma criança do texto da página. No entanto, como as projeções requerem um objeto JSON e a página é uma primitiva (corda), `"sourceContext"` é usada para envolver a frase chave num objeto com uma propriedade nomeada. Esta técnica permite que até os primitivos sejam projetados de forma independente.

## <a name="next-steps"></a>Passos seguintes

Como próximo passo, crie o seu primeiro skillset com habilidades cognitivas.

> [!div class="nextstepaction"]
> [Crie o seu primeiro skillset](cognitive-search-defining-skillset.md).
