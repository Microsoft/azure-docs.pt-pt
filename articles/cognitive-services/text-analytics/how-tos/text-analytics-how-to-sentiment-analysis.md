---
title: Realizar análise de sentimento com API DE REPOUSO TEXT Analytics
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como detetar sentimento em texto com o Azure Cognitive Services Analytics API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 08/25/2020
ms.author: aahi
ms.openlocfilehash: a0557c3ccf6510ab3ee2ae29cbef1fc754473345
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88933023"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Como: Detetar sentimento usando a API de Análise de Texto

A funcionalidade de Análise de Sentimento da API de Text Analytics avalia os resultados de texto e devolve pontuações e rótulos de sentimento para cada frase. Isto é útil para detetar sentimentos positivos e negativos nas redes sociais, avaliações de clientes, fóruns de discussão e muito mais. Os modelos de IA utilizados pela API são fornecidos pelo serviço, basta enviar conteúdo para análise.

Após o envio de um pedido de análise de sentimento, a API devolve rótulos de sentimento (tais como "negativo", "neutro" e "positivo") e pontuações de confiança a nível da frase e do documento.

A Análise de Sentimento suporta uma vasta gama de línguas, com mais na pré-visualização. Para mais informações, consulte [as línguas apoiadas.](../text-analytics-supported-languages.md)

## <a name="sentiment-analysis-versions-and-features"></a>Versões e funcionalidades de Análise de Sentimento

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Funcionalidade                                   | Análise de Sentimento v3 | Análise de Sentimento v3.1 (Pré-visualização) |
|-------------------------------------------|-----------------------|-----------------------------------|
| Métodos para pedidos individuais e de lote    | X                     | X                                 |
| Pontuações de sentimento e rotulagem             | X                     | X                                 |
| [Recipiente Docker](text-analytics-how-to-install-containers.md) baseado em Linux | X  |  |
| Mineração de opinião                            |                       | X                                 |

### <a name="sentiment-scoring-and-labeling"></a>Pontuação e rotulagem de sentimentos

A Análise de Sentimento na V3 aplica rótulos de sentimento ao texto, que são devolvidos a um nível de frase e documento, com uma pontuação de confiança para cada um. 

Os rótulos são *positivos,* *negativos*e *neutros.* Ao nível do documento, a etiqueta de sentimento *misto* também pode ser devolvida. O sentimento do documento é determinado a seguir:

| Sentimento da sentença                                                                            | Etiqueta de documento devolvido |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Pelo menos uma `positive` frase está no documento. O resto das frases `neutral` são. | `positive`              |
| Pelo menos uma `negative` frase está no documento. O resto das frases `neutral` são. | `negative`              |
| Pelo menos uma `negative` frase e pelo menos uma frase `positive` estão no documento.    | `mixed`                 |
| Todas as frases do documento `neutral` são.                                                  | `neutral`               |

Os resultados de confiança variam de 1 a 0. Pontuações mais próximas de 1 indicam uma maior confiança na classificação do rótulo, enquanto as pontuações mais baixas indicam menor confiança. Para cada documento ou para cada frase, as pontuações previstas associadas aos rótulos (positivos, negativos e neutros) somam até 1.

### <a name="opinion-mining"></a>Mineração de opinião

A mineração de opinião é uma característica da Análise do Sentimento, a partir da versão 3.1-preview.1. Também conhecida como Análise de Sentimento baseada em Aspetos no Processamento de Linguagem Natural (NLP), esta funcionalidade fornece mais informações granulares sobre as opiniões relacionadas com aspetos (como os atributos de produtos ou serviços) em texto.

Por exemplo, se um cliente deixar feedback sobre um hotel como "o quarto era ótimo, mas o pessoal não era amigável", a mineração de opinião localizará aspetos no texto, e as suas opiniões e sentimentos associados:

| Aspeto | Opinião    | Sentimento |
|--------|------------|-----------|
| sala   | Ótimo      | positivo  |
| pessoal  | hostil | negativo  |

Para obter a mineração de opinião nos seus resultados, deve incluir a `opinionMining=true` bandeira num pedido de análise de sentimento. Os resultados da mineração de opinião serão incluídos na resposta da análise do sentimento.

## <a name="sending-a-rest-api-request"></a>Envio de um pedido de API REST 

### <a name="preparation"></a>Preparação

A análise de sentimento produz um resultado de maior qualidade quando lhe dá pequenas quantidades de texto para trabalhar. O mesmo já não acontece com a extração de expressões-chave, que tem um melhor desempenho com blocos de texto maiores. Para obter os melhores resultados em ambas as operações, considere reestruturar as entradas em conformidade.

Você deve ter documentos JSON neste formato: ID, texto e idioma.

O tamanho do documento deve ser inferior a 5.120 caracteres por documento. Você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido.

## <a name="structure-the-request"></a>Estruturar o pedido

Crie um pedido POST. Pode utilizar o [Carteiro](text-analytics-how-to-call-api.md) ou a **consola de testes API** nas seguintes ligações de referência para estruturar e enviar rapidamente uma. 

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

[Referência v3 análise de sentimento](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

#### <a name="version-31-preview1"></a>[Versão 3.1-pré-visualização.1](#tab/version-3-1)

[Referência v3.1 análise de sentimento v3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Sentiment)

---

### <a name="request-endpoints"></a>Solicitar pontos finais

Deite o ponto final HTTPS para análise de sentimento utilizando um recurso text analytics no Azure ou um recipiente instantâneo [de Análise de Texto](text-analytics-how-to-install-containers.md). Tem de incluir o URL correto para a versão que pretende utilizar. Por exemplo:

> [!NOTE]
> Pode encontrar a sua chave e ponto final para o seu recurso Text Analytics no portal azul. Estarão localizados na página **de arranque rápido** do recurso, sob **gestão de recursos.** 

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

#### <a name="version-31-preview1"></a>[Versão 3.1-pré-visualização.1](#tab/version-3-1)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment`

Para obter resultados de mineração de opinião, você deve incluir o `opinionMining=true` parâmetro. Por exemplo:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment?opinionMining=true`

Este parâmetro é definido `false` por padrão. 

---

Desa estatua um cabeçalho de pedido para incluir a sua chave API API de Análise de Texto. No corpo do pedido, forneça a coleção de documentos JSON que preparou para esta análise.

### <a name="example-sentiment-analysis-request"></a>Exemplo Pedido de Análise de Sentimento 

Segue-se um exemplo de conteúdos que poderá enviar para a análise de sentimentos. O formato de pedido é o mesmo para ambas as versões.
    
```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "The restaurant had great food and our waiter was friendly."
    }
  ]
}
```

### <a name="post-the-request"></a>Postar o pedido

A análise é realizada aquando da receção do pedido. Para obter informações sobre o tamanho e o número de pedidos que pode enviar por minuto e segundo, consulte a secção [limite de dados](../overview.md#data-limits) na visão geral.

O Texto Analytics API é apátrida. Nenhum dado é armazenado na sua conta e os resultados são devolvidos imediatamente na resposta.


### <a name="view-the-results"></a>Ver os resultados

A análise do sentimento devolve um rótulo de sentimento e uma pontuação de confiança para todo o documento, e cada frase dentro dele. Pontuações mais próximas de 1 indicam uma maior confiança na classificação do rótulo, enquanto as pontuações mais baixas indicam menor confiança. Um documento pode ter várias frases, e as pontuações de confiança dentro de cada documento ou frase somam 1.

O resultado é devolvido imediatamente. Pode transmitir os resultados para uma aplicação que aceite o JSON ou guarde a saída para um ficheiro no sistema local. Em seguida, importe a saída para uma aplicação que pode usar para ordenar, pesquisar e manipular os dados. Devido ao suporte multilíngue e emoji, a resposta pode conter compensações de texto. Veja [como processar compensações](../concepts/text-offsets.md) para obter mais informações.

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

### <a name="sentiment-analysis-v30-example-response"></a>Análise de Sentimento v3.0 exemplo resposta

As respostas da Análise de Sentimento v3 contêm rótulos de sentimento e pontuações para cada frase e documento analisados.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

#### <a name="version-31-preview1"></a>[Versão 3.1-pré-visualização.1](#tab/version-3-1)

### <a name="sentiment-analysis-v31-example-response"></a>Análise de Sentimento v3.1 exemplo resposta

A Análise de Sentimento v3.1 oferece mineração de opinião para além do objeto de resposta no separador **Versão 3.0.** Na resposta abaixo, a frase *O restaurante tinha boa comida e o nosso empregado de mesa era amigável* tem dois aspetos: *comida* e *garçom.* A propriedade de cada aspeto `relations` contém um valor com a referência `ref` URI aos, `documents` e `sentences` `opinions` objetos associados.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly.",
                    "aspects": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 25,
                            "length": 4,
                            "text": "food",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/0"
                                }
                            ]
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 38,
                            "length": 6,
                            "text": "waiter",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/1"
                                }
                            ]
                        }
                    ],
                    "opinions": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 19,
                            "length": 5,
                            "text": "great",
                            "isNegated": false
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 49,
                            "length": 8,
                            "text": "friendly",
                            "isNegated": false
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

---

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para análise de sentimentos usando a API text Analytics. Em resumo:

+ A Análise de Sentimento está disponível para idiomas selecionados.
+ Os documentos JSON no órgão de pedido incluem um ID, texto e código linguístico.
+ O pedido do POST é para um `/sentiment` ponto final usando uma chave de acesso personalizada [e um ponto final](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) válido para a sua subscrição.
+ A saída de resposta, que consiste numa pontuação de sentimento para cada ID do documento, pode ser transmitida para qualquer app que aceite o JSON. Por exemplo, Excel e Power BI.

## <a name="see-also"></a>Consulte também

* [Descrição geral da Análise de Texto](../overview.md)
* [Utilização da biblioteca de clientes Text Analytics](../quickstarts/text-analytics-sdk.md)
* [Novidades](../whats-new.md)
