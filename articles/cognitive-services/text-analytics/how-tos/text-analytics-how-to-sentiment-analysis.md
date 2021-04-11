---
title: Realizar análise de sentimento e mineração de opinião com Text Analytics REST API
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como detetar sentimentos, e o meu para opiniões em texto com a Azure Cognitive Services Analytics API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 03/29/2021
ms.author: aahi
ms.openlocfilehash: 7cd2b0a6b943ceb32420ef119a7fc5eddefa2e19
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276999"
---
# <a name="how-to-sentiment-analysis-and-opinion-mining"></a>Como: Análise de sentimento e mineração de opinião

A funcionalidade de Análise de Sentimento da API de Análise de Texto analytics fornece duas formas de detetar sentimentos positivos e negativos. Se enviar um pedido de Análise de Sentimento, a API devolverá rótulos de sentimento (tais como "negativo", "neutro" e "positivo") e pontuações de confiança a nível da frase e do documento. Também pode enviar pedidos de Mineração de Opinião utilizando o ponto final de Análise de Sentimento, que fornece informações granulares sobre as opiniões relacionadas com palavras (como os atributos de produtos ou serviços) no texto.

Os modelos de IA utilizados pela API são fornecidos pelo serviço, basta enviar conteúdo para análise.

## <a name="sentiment-analysis-versions-and-features"></a>Versões e funcionalidades de Análise de Sentimento

| Funcionalidade                                   | Análise de Sentimento v3 | Análise de Sentimento v3.1 (Pré-visualização) |
|-------------------------------------------|-----------------------|-----------------------------------|
| Métodos para pedidos individuais e de lote    | X                     | X                                 |
| Pontuações e rotulagem da Análise de Sentimento             | X                     | X                                 |
| [Recipiente Docker](text-analytics-how-to-install-containers.md) baseado em Linux | X  |  |
| Mineração de Opinião                            |                       | X                                 |

## <a name="sentiment-analysis"></a>Análise de Sentimentos

Análise de Sentimento na versão 3.x aplica rótulos de sentimento ao texto, que são devolvidos a um nível de frase e documento, com uma pontuação de confiança para cada um. 

Os rótulos são *positivos,* *negativos* e *neutros.* Ao nível do documento, a etiqueta de sentimento *misto* também pode ser devolvida. O sentimento do documento é determinado a seguir:

| Sentimento da sentença                                                                            | Etiqueta de documento devolvido |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Pelo menos uma `positive` frase está no documento. O resto das frases `neutral` são. | `positive`              |
| Pelo menos uma `negative` frase está no documento. O resto das frases `neutral` são. | `negative`              |
| Pelo menos uma `negative` frase e pelo menos uma frase `positive` estão no documento.    | `mixed`                 |
| Todas as frases do documento `neutral` são.                                                  | `neutral`               |

Os resultados de confiança variam de 1 a 0. Pontuações mais próximas de 1 indicam uma maior confiança na classificação do rótulo, enquanto as pontuações mais baixas indicam menor confiança. Para cada documento ou para cada frase, as pontuações previstas associadas aos rótulos (positivos, negativos e neutros) somam até 1. Para mais informações, consulte a [nota de transparência text Analytics.](/legal/cognitive-services/text-analytics/transparency-note?context=/azure/cognitive-services/text-analytics/context/context) 

## <a name="opinion-mining"></a>Mineração de Opinião

Opinion Mining é uma característica da Análise do Sentimento, a começar na pré-visualização da versão 3.1. Também conhecida como Análise de Sentimento baseada em Aspeto no Processamento de Linguagem Natural (NLP), esta funcionalidade fornece mais informações granulares sobre as opiniões relacionadas com atributos de produtos ou serviços em texto. A API surge como alvo (substantivo ou verbo) e avaliação (adjetivo).

Por exemplo, se um cliente deixar feedback sobre um hotel como "O quarto era ótimo, mas o pessoal não era amigável.", a Opinion Mining localizará alvos (aspetos) no texto, e as suas avaliações associadas (opiniões) e sentimentos. A Análise de Sentimento só pode relatar um sentimento negativo.

:::image type="content" source="../media/how-tos/opinion-mining.png" alt-text="Um diagrama do exemplo de Mineração de Opinião" lightbox="../media/how-tos/opinion-mining.png":::

Para obter a Opinion Mining nos seus resultados, deve incluir a `opinionMining=true` bandeira num pedido de análise de sentimento. Os resultados da Mineração de Opinião serão incluídos na resposta da análise do sentimento. A mineração de opinião é uma extensão da Análise do Sentimento e está incluída no seu [nível atual de preços.](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)


## <a name="sending-a-rest-api-request"></a>Envio de um pedido de API REST 

### <a name="preparation"></a>Preparação

A análise de sentimento produz um resultado de maior qualidade quando lhe dá pequenas quantidades de texto para trabalhar. O mesmo já não acontece com a extração de expressões-chave, que tem um melhor desempenho com blocos de texto maiores. Para obter os melhores resultados em ambas as operações, considere reestruturar as entradas em conformidade.

Você deve ter documentos JSON neste formato: ID, texto e idioma. A Análise de Sentimento suporta uma vasta gama de línguas, com mais na pré-visualização. Para obter mais informações, veja os [Idiomas suportados](../language-support.md).

O tamanho do documento deve ser inferior a 5.120 caracteres por documento. Para o número máximo de documentos permitidos numa recolha, consulte o artigo [limite de dados](../concepts/data-limits.md?tabs=version-3) nos Termos. A coleção é enviada no corpo do pedido.

## <a name="structure-the-request"></a>Estruturar o pedido

Crie um pedido POST. Pode utilizar o [Carteiro](text-analytics-how-to-call-api.md) ou a **consola de testes API** nas seguintes ligações de referência para estruturar e enviar rapidamente uma. 

#### <a name="version-31-preview"></a>[Versão 3.1-pré-visualização](#tab/version-3-1)

[Referência v3.1 análise de sentimento v3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Sentiment)

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

[Referência v3 análise de sentimento](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

---

### <a name="request-endpoints"></a>Solicitar pontos finais

Deite o ponto final HTTPS para análise de sentimento utilizando um recurso text analytics no Azure ou um recipiente instantâneo [de Análise de Texto](text-analytics-how-to-install-containers.md). Tem de incluir o URL correto para a versão que pretende utilizar. Por exemplo:

> [!NOTE]
> Pode encontrar a sua chave e ponto final para o seu recurso Text Analytics no portal Azure. Estarão localizados na página **de arranque rápido** do recurso, sob **gestão de recursos.** 

#### <a name="version-31-preview"></a>[Versão 3.1-pré-visualização](#tab/version-3-1)

**Análise de Sentimentos**

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/sentiment`

**Mineração de Opinião**

Para obter os resultados da Opinion Mining, deve incluir o `opinionMining=true` parâmetro. Por exemplo:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/sentiment?opinionMining=true`

Este parâmetro é definido `false` por padrão. 

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

**Análise de Sentimentos**

Em v3.0, o único ponto final disponível é para análise de sentimento.
 
`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

---

Desa estatua um cabeçalho de pedido para incluir a sua chave API API de Análise de Texto. No corpo do pedido, forneça a coleção de documentos JSON que preparou para esta análise.

### <a name="example-request-for-sentiment-analysis-and-opinion-mining"></a>Pedido de exemplo para Análise de Sentimento e Mineração de Opinião  

Segue-se um exemplo de conteúdos que poderá enviar para a análise de sentimentos. O formato de pedido é o mesmo para ambos `v3.0` e `v3.1-preview` .
    
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

O resultado é devolvido imediatamente. Pode transmitir os resultados para uma aplicação que aceite o JSON ou guarde a saída para um ficheiro no sistema local. Em seguida, importe a saída para uma aplicação que pode usar para ordenar, pesquisar e manipular os dados. Devido ao suporte multilíngue e emoji, a resposta pode conter compensações de texto. Veja [como processar compensações](../concepts/text-offsets.md) para obter mais informações.

#### <a name="version-31-preview"></a>[Versão 3.1-pré-visualização](#tab/version-3-1)

### <a name="sentiment-analysis-and-opinion-mining-example-response"></a>Análise de Sentimento e Resposta de exemplo de Mineração de Opinião

> [!IMPORTANT]
> Segue-se um exemplo JSON para a utilização da Opinion Mining with Sentiment Analysis, oferecida em v3.1 da API. Se não solicitar a mineração de opinião, a resposta da API será a mesma que o separador **Versão 3.0.**  

A Análise de Sentimento v3.1 pode devolver objetos de resposta tanto para análise de sentimento como para mineração de opinião.
  
A análise do sentimento devolve um rótulo de sentimento e uma pontuação de confiança para todo o documento, e cada frase dentro dele. Pontuações mais próximas de 1 indicam uma maior confiança na classificação do rótulo, enquanto as pontuações mais baixas indicam menor confiança. Um documento pode ter várias frases, e as pontuações de confiança dentro de cada documento ou frase somam 1.

A Mineração de Opinião localizará alvos (substantivos ou verbos) no texto e a sua avaliação associada (adjetivo). Na resposta abaixo, a frase *O restaurante tinha boa comida e o nosso empregado de mesa era amigável* tem dois alvos: *comida* e *garçom.* A propriedade de cada alvo `relations` contém um valor com a referência `ref` URI `documents` `sentences` aos, e `assessments` objetos associados.

A API devolve opiniões como um alvo (substantivo ou verbo) e uma avaliação (adjetivo).

```json
{
  "documents": [
    {
      "id": "1",
      "sentiment": "positive",
      "confidenceScores": {
        "positive": 1,
        "neutral": 0,
        "negative": 0
      },
      "sentences": [
        {
          "sentiment": "positive",
          "confidenceScores": {
            "positive": 1,
            "neutral": 0,
            "negative": 0
          },
          "offset": 0,
          "length": 58,
          "text": "The restaurant had great food and our waiter was friendly.",
          "targets": [
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 25,
              "length": 4,
              "text": "food",
              "relations": [
                {
                  "relationType": "assessment",
                  "ref": "#/documents/0/sentences/0/assessments/0"
                }
              ]
            },
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 38,
              "length": 6,
              "text": "waiter",
              "relations": [
                {
                  "relationType": "assessment",
                  "ref": "#/documents/0/sentences/0/assessments/1"
                }
              ]
            }
          ],
          "assessments": [
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 19,
              "length": 5,
              "text": "great",
              "isNegated": false
            },
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
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

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

### <a name="sentiment-analysis-example-response"></a>Resposta exemplo de análise de sentimento

A análise do sentimento devolve um rótulo de sentimento e uma pontuação de confiança para todo o documento, e cada frase dentro dele. Pontuações mais próximas de 1 indicam uma maior confiança na classificação do rótulo, enquanto as pontuações mais baixas indicam menor confiança. Um documento pode ter várias frases, e as pontuações de confiança dentro de cada documento ou frase somam 1.

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

---

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para análise de sentimentos usando a API text Analytics. Em resumo:

+ A Análise de Sentimentos e a Mineração de Opinião estão disponíveis para idiomas selecionados.
+ Os documentos JSON no órgão de pedido incluem um ID, texto e código linguístico.
+ O pedido do POST é para um `/sentiment` ponto final usando uma chave de acesso personalizada [e um ponto final](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) válido para a sua subscrição.
+ Use `opinionMining=true` em pedidos de Análise Consciente para obter resultados de Mineração de Opinião.
+ A saída de resposta, que consiste numa pontuação de sentimento para cada ID do documento, pode ser transmitida para qualquer app que aceite o JSON. Por exemplo, Excel e Power BI.

## <a name="see-also"></a>Ver também

* [Descrição geral da Análise de Texto](../overview.md)
* [Utilização da biblioteca de clientes Text Analytics](../quickstarts/client-libraries-rest-api.md)
* [Novidades](../whats-new.md)
