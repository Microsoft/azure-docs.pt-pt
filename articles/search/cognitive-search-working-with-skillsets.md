---
title: Conceitos de skillset e fluxo de trabalho
titleSuffix: Azure Cognitive Search
description: Skillsets é onde você é autor de um oleoduto de enriquecimento de IA em Pesquisa Cognitiva Azure. Aprenda conceitos e detalhes importantes sobre a composição de skillset.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0637e160454897af774c3bac48fc02866cb71835
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760798"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Conceitos de skillset e composição em Pesquisa Cognitiva Azure

Este artigo é para desenvolvedores que precisam de uma compreensão mais profunda de como o gasoduto de enriquecimento funciona e assume que você tem uma compreensão conceptual do processo de enriquecimento de IA. Se você é novo este conceito, comece com:
+ [Enriquecimento de IA em Pesquisa Cognitiva Azure](cognitive-search-concept-intro.md)
+ [Loja de conhecimento (visualização)](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>Especificar o Skillset
Um skillset é um recurso reutilizável na Pesquisa Cognitiva Azure que especifica uma coleção de habilidades cognitivas usadas para analisar, transformar e enriquecer o conteúdo de texto ou imagem durante a indexação. A criação de um skillset permite-lhe anexar enriquecimentos de texto e imagem na fase de ingestão de dados, extraindo e criando novas informações e estruturas a partir de conteúdo bruto.

Um skillset tem três propriedades:

+   ```skills```, uma coleção de competências não ordenadapara as quais a plataforma determina a sequência de execução com base nas inputs necessárias para cada habilidade
+   ```cognitiveServices```, a chave dos serviços cognitivos necessária seleções para a faturação das capacidades cognitivas invocadas
+   ```knowledgeStore```, a conta de armazenamento onde os seus documentos enriquecidos serão projetados



As habilidades são da autoria da JSON. Pode construir habilidades complexas com looping e [ramificação](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional) utilizando a linguagem de [expressão](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional). A linguagem de expressão usa a notação do caminho [do Ponteiro JSON](https://tools.ietf.org/html/rfc6901) com algumas modificações para identificar os nódosos na árvore de enriquecimento. Um ```"/"``` atravessa um nível mais baixo na árvore e ```"*"``` atua como um operador para cada um no contexto. Estes conceitos são melhor descritos com um exemplo. Para ilustrar alguns dos conceitos e capacidades, vamos percorrer as avaliações do [hotel.](knowledge-store-connect-powerbi.md) Para ver a habilidade depois de ter seguido o fluxo de trabalho de dados de importação, terá de usar um cliente rest API para [obter a habilidade.](https://docs.microsoft.com/rest/api/searchservice/get-skillset)

### <a name="enrichment-tree"></a>Árvore de enriquecimento

Para visualizar como uma habilidade enriquece progressivamente o seu documento, vamos começar com o que o documento parece antes de qualquer enriquecimento. A saída de quebra de documentos depende da fonte de dados e do modo específico de análise selecionado. Este é também o estado do documento que os [mapeamentos](search-indexer-field-mappings.md) de campo podem obter conteúdo a partir da adição de dados ao índice de pesquisa.
![Repositório de conhecimento no diagrama de pipeline](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Repositório de conhecimento no diagrama de pipeline")

Uma vez que um documento está no oleoduto de enriquecimento, é representado como uma árvore de conteúdo e enriquecimentoassociado. Esta árvore é instantaneamente instantânea como a saída de documentos rachando. O formato de árvore de enriquecimento permite que o gasoduto de enriquecimento fixe metadados a tipos de dados até primitivos, não é um objeto JSON válido, mas pode ser projetado num formato JSON válido. A tabela que se segue mostra o estado de um documento que entra no gasoduto de enriquecimento:

|Modo Fonte de Dados\Parsing|Predefinição|JSON, JSON Lines & CSV|
|---|---|---|
|Blob Storage|/documento/conteúdo<br>/documento/normalized_images/*<br>…|/documento/{key1}<br>/documento/{key2}<br>…|
|SQL|/documento/{column1}<br>/documento/{column2}<br>…|N/A |
|Cosmos DB|/documento/{key1}<br>/documento/{key2}<br>…|N/A|

 Como as habilidades executam, adicionam novos nós à árvore de enriquecimento. Estes novos nós podem então ser usados como entradas para competências a jusante, projetando para a loja de conhecimento, ou mapeando para campos indexados. Os enriquecimentos não são mutáveis: uma vez criados, os nós não podem ser editados. À medida que as suas habilidades se tornam mais complexas, também a sua árvore de enriquecimento, mas nem todos os nós na árvore de enriquecimento precisam chegar ao índice ou à loja de conhecimento. Pode persistir seletivamente apenas um subconjunto dos enriquecimentos ao índice ou à loja de conhecimento.

Pode persistir seletivamente apenas um subconjunto dos enriquecimentos ao índice ou à loja de conhecimento.
Para o resto deste documento, vamos assumir que estamos a trabalhar com o exemplo de avaliações de [hotéis,](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi)mas os mesmos conceitos aplicam-se ao enriquecimento de documentos de todas as outras fontes de dados.

### <a name="context"></a>Contexto
Cada habilidade requer um contexto. Um contexto determina:
+   O número de vezes que a habilidade executa, com base nos nós selecionados. Para valores de contexto de recolha de tipo, adicionar um ```/*``` no final resultará na invodesão invocada uma vez por cada instância da coleção. 
+   Onde na árvore de enriquecimento as saídas de habilidade são adicionadas. As saídas são sempre adicionadas à árvore como crianças do nó de contexto. 
+   Forma das inputs. Para coleções de vários níveis, definir o contexto para a coleção dos pais afetará a forma da entrada para a habilidade. Por exemplo, se tiver uma árvore de enriquecimento com uma lista de países, cada um enriquecido com uma lista de estados que contêm uma lista de códigos postal.

|Contexto|Input|Forma de entrada|Invocação de Habilidades|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |Uma lista de todos os códigos postal do país |Uma vez por país |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |Uma lista de códigos postais no estado | Uma vez por combinação de país e estado|

### <a name="sourcecontext"></a>Contexto de Origem

O `sourceContext` é utilizado apenas em inputs e projeções de [habilidades.](knowledge-store-projection-overview.md) É usado para construir objetos aninhadas de vários níveis. Você pode precisar de criar um novo objeto para passá-lo como uma entrada para uma habilidade ou projeto para a loja de conhecimento. Como os nós de enriquecimento podem não ser um objeto JSON válido na árvore de enriquecimento e referenciar um nó na árvore apenas devolve esse estado do nó quando foi criado, usando os enriquecimentos como inputs de habilidade ou projeções requer que você crie um objeto JSON bem formado. O `sourceContext` permite-lhe construir um objeto hierárquico e anónimo, o que exigiria múltiplas habilidades se estivesse apenas a usar o contexto. A utilização `sourceContext` é mostrada na secção seguinte. Veja a saída de habilidade que gerou um enriquecimento para determinar se é um objeto JSON válido e não um tipo primitivo.

### <a name="projections"></a>Projeções

Projeção é o processo de seleção dos nódosos da árvore de enriquecimento a serem guardados na loja de conhecimento. As projeções são formas personalizadas do documento (conteúdo e enriquecimento) que podem ser saídas como projeções de tabela ou objeto. Para saber mais sobre trabalhar com projeções, veja [trabalhar com projeções.](knowledge-store-projection-overview.md)

![Opções de mapeamento de campo](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Opções de mapeamento de campo para o gasoduto de enriquecimento")

O diagrama acima descreve o seletor com o qual trabalha com base no local onde está no gasoduto de enriquecimento.

## <a name="generate-enriched-data"></a>Gerar dados enriquecidos 

Vamos agora analisar as competências do hotel, pode seguir o [tutorial](knowledge-store-connect-powerbi.md) para criar a habilidade ou [ver](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/samples/skillset.json) o skillset. Vamos ver:

* como a árvore de enriquecimento evolui com a execução de cada habilidade 
* como o contexto e as inputs trabalham para determinar quantas vezes uma habilidade executa 
* qual a forma da entrada é baseada no contexto. 

Uma vez que estamos a usar o modo de análise de texto delimitado para o indexante, um documento dentro do processo de enriquecimento representa uma única linha dentro do ficheiro CSV.

### <a name="skill-1-split-skill"></a>Habilidade #1: Habilidade dividida 

![árvore de enriquecimento após rachadura de documento](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Árvore de enriquecimento após rachadura de documento e antes da execução de habilidades")

Com o contexto de habilidade de ```"/document/reviews_text"```, esta habilidade executará uma vez para o `reviews_text`. A saída de habilidades é uma lista onde o `reviews_text` é dividido em 5000 segmentos de caracteres. A saída da habilidade dividida é chamada `pages` e adicionada à árvore de enriquecimento. A funcionalidade `targetName` permite-lhe renomear uma saída de habilidade antes de ser adicionado à árvore de enriquecimento.

A árvore de enriquecimento tem agora um novo nó colocado no contexto da habilidade. Este nó está disponível para qualquer habilidade, projeção ou mapeamento de campo de saída.


O nó de raiz para todos os enriquecimentos é `"/document"`. Ao trabalhar com os indexantes blob, o nó `"/document"` terá nódeos infantis de `"/document/content"` e `"/document/normalized_images"`. Ao trabalhar com dados csv, como estamos neste exemplo, os nomes das colunas irão mapear para nós abaixo `"/document"`. Para aceder a qualquer um dos enriquecimentos adicionados a um nó por uma habilidade, é necessário todo o caminho para o enriquecimento. Por exemplo, se quiser utilizar o texto do nó ```pages``` como entrada para outra habilidade, terá de especificá-lo como ```"/document/reviews_text/pages/*"```.
 
 ![árvore de enriquecimento após habilidade #1](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Árvore de enriquecimento após habilidade #1 executa")

### <a name="skill-2-language-detection"></a>Habilidade #2 Deteção de Linguagem
 Embora a habilidade de deteção de linguagem seja a terceira habilidade (habilidade #3) definida no skillset, é a próxima habilidade a executar. Uma vez que não é bloqueada por exigir quaisquer inputs, executará em paralelo com a habilidade anterior. Tal como a habilidade dividida que a precedeu, a habilidade de deteção de linguagem também é invocada uma vez para cada documento. A árvore de enriquecimento agora tem um novo nó para a linguagem.
 ![árvore de enriquecimento após habilidade #2](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Árvore de enriquecimento após habilidade #2 executa")
 
 ### <a name="skill-3-key-phrases-skill"></a>Habilidade #3: Habilidade de frases-chave 

Dado o contexto de ```/document/reviews_text/pages/*``` as frases-chave serão invocadas uma vez para cada um dos itens da coleção `pages`. A saída da habilidade será um nó sob o elemento de página associado. 

 Agora deve ser capaz de olhar para o resto das habilidades no skillset e visualizar como a árvore dos enriquecimentos continuará a crescer com a execução de cada habilidade. Algumas habilidades, como a habilidade de fusão e a habilidade do shaper, também criam novos nós, mas apenas usam dados de nós existentes e não criam novos enriquecimentos líquidos.

![árvore de enriquecimento depois de todas as habilidades](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Árvore de enriquecimento afinal de todas as habilidades")

As cores dos conectores na árvore acima indicam que os enriquecimentos foram criados por diferentes habilidades e os nódosos terão de ser abordados individualmente e não farão parte do objeto devolvido ao selecionar o nó principal.

## <a name="save-enrichments-in-a-knowledge-store"></a>Poupe enriquecimentos numa loja de conhecimento 

As habilidades também definem uma loja de conhecimentos onde os seus documentos enriquecidos podem ser projetados como mesas ou objetos. Para guardar os seus dados enriquecidos na loja de conhecimento, define um conjunto de projeções para o seu documento enriquecido. Para saber mais sobre a loja de conhecimento ver [visão geral da loja](knowledge-store-concept-intro.md) de conhecimento

### <a name="slicing-projections"></a>Projeções de corte

Ao definir um grupo de projeção de mesa, um único nó na árvore de enriquecimento pode ser cortado em várias tabelas relacionadas. Se adicionar uma tabela com um caminho de origem que é filho de uma projeção de mesa existente, o nó infantil resultante não será uma criança da projeção da tabela existente, mas será projetado para a nova tabela, relacionada. Esta técnica de corte permite definir um único nó numa habilidade de formador que pode ser a fonte para todas as suas projeções de mesa. 

### <a name="shaping-projections"></a>Projeções de modelação

Há duas maneiras de definir uma projeção. Você poderia usar uma habilidade de shaper para criar um novo nó que é o nó raiz para todos os enriquecimentos que você está projetando. Então, nas suas projeções, só referenciaria a saída da habilidade do shaper. Também pode formar uma projeção dentro da própria definição de projeção.

A abordagem do shaper é mais verbosa do que a formação linear, mas garante que todas as mutações da árvore de enriquecimento estão contidas dentro das habilidades e que a saída é um objeto que pode ser reutilizado. A formação inline permite-lhe criar a forma de que necessita, mas é um objeto anónimo e só está disponível para a projeção para a qual é definida. As abordagens podem ser usadas em conjunto ou separadamente. A habilidade criada para si no fluxo de trabalho do portal contém ambos. Usa uma habilidade de shaper para as projeções da tabela, mas também usa a formação linear para projetar a tabela de frases-chave.

Para estender o exemplo, pode optar por remover a formação inline e utilizar uma habilidade de shaper para criar um novo nó para as frases-chave. Para criar uma forma projetada em três tabelas, nomeadamente, `hotelReviewsDocument`, `hotelReviewsPages`, e `hotelReviewsKeyPhrases`, as duas opções são descritas nas seguintes secções.


#### <a name="shaper-skill-and-projection"></a>Habilidade e projeção do shaper 

> [!Note]
> Algumas das colunas da tabela de documentos foram removidas deste exemplo para a brevidade.
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

Com o nó `tableprojection` definido na secção `outputs` acima, podemos agora usar a função de corte para projetar partes do nó `tableprojection` em diferentes tabelas:

> [!Note]
> Este é apenas um corte da projeção dentro da configuração da loja de conhecimento.
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

#### <a name="inline-shaping-projections"></a>Projeções de modelação inline

A abordagem de formação inline não requer uma habilidade de shaper, uma vez que todas as formas necessárias para as projeções são criadas no momento em que são necessárias. Para projetar os mesmos dados que o exemplo anterior, a opção de projeção inline seria assim:

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
  
Uma observação de ambas as abordagens é a forma como os valores das `"Keyphrases"` são projetados utilizando o `"sourceContext"`. O nó `"Keyphrases"`, que contém uma coleção de cordas, é em si mesmo uma criança do texto da página. No entanto, como as projeções requerem um objeto JSON e a página é uma primitiva (corda), o `"sourceContext"` é usado para embrulhar a frase chave em um objeto com uma propriedade nomeada. Esta técnica permite que mesmo os primitivos sejam projetados de forma independente.

## <a name="next-steps"></a>Passos seguintes

Como próximo passo, crie a sua primeira habilidade com habilidades cognitivas.

> [!div class="nextstepaction"]
> [Crie a sua primeira habilidade.](cognitive-search-defining-skillset.md)
