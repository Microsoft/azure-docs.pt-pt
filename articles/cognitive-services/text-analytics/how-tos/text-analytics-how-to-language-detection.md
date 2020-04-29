---
title: Detete a linguagem com a API REST DE TEXTO Analytics
titleSuffix: Azure Cognitive Services
description: Detete a linguagem utilizando a API REST de Texto Analytics dos Serviços Cognitivos Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: d34f3a03e1bcd35c270d13c4dda57d0394a36e4b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70387797"
---
# <a name="example-detect-language-with-text-analytics"></a>Exemplo: Detetar linguagem com Análise de Texto

A funcionalidade de deteção de [idiomas](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) da API do Rest API do Texto Azure avalia a entrada de texto para cada documento e devolve identificadores linguísticos com uma pontuação que indica a força da análise.

Esta funcionalidade é útil para arquivos de conteúdo que recolhem texto arbitrário quando o idioma é desconhecido. Pode analisar os resultados desta análise para determinar o idioma que é utilizado no documento de entrada. A resposta também devolve uma pontuação que reflete a confiança do modelo. O valor da pontuação é entre 0 e 1.

A funcionalidade de Deteção de Línguas pode detetar uma vasta gama de línguas, variantes, dialetos e algumas línguas regionais ou culturais. A lista exata de idiomas para esta funcionalidade não é publicada.

Se tiver conteúdo expresso num idioma menos utilizado, pode experimentar a funcionalidade deteção de idiomas para ver se devolve um código. A resposta para línguas que não `unknown`podem ser detetadas é.

> [!TIP]
> O Text Analytics também fornece uma imagem de recipiente Docker baseada em Linux para deteção de idiomas, para que possa [instalar e executar o recipiente Text Analytics](text-analytics-how-to-install-containers.md) perto dos seus dados.

## <a name="preparation"></a>Preparação

Deve ter documentos JSON neste formato: ID e texto.

O tamanho do documento deve ser inferior a 5.120 caracteres por documento. Pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido. A amostra que se segue é um exemplo de conteúdo que pode submeter para deteção de idiomas:

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "This document is in English."
            },
            {
                "id": "2",
                "text": "Este documento está en inglés."
            },
            {
                "id": "3",
                "text": "Ce document est en anglais."
            },
            {
                "id": "4",
                "text": "本文件为英文"
            },
            {
                "id": "5",
                "text": "Этот документ на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Passo 1: estruturar o pedido

Para obter mais informações sobre a definição de pedido, consulte [a API](text-analytics-how-to-call-api.md)de Análise de Texto . Os seguintes pontos são novamente apresentados para sua comodidade:

+ Crie um pedido POST. Para rever a documentação da API para este pedido, consulte a API de [Deteção de Idiomas](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

+ Defina o ponto de final HTTP para a deteção de idioma. Utilize um recurso De Análise de Texto no Azure ou num recipiente instantâneo de Análise de [Texto](text-analytics-how-to-install-containers.md). Deve incluir `/text/analytics/v2.1/languages` na URL. Por exemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/languages`.

+ Detete um cabeçalho de pedido para incluir a [chave de acesso](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) para operações de Análise de Texto.

+ No corpo do pedido, forneça a coleção de documentos JSON que preparou para esta análise.

> [!Tip]
> Utilize o [Postman](text-analytics-how-to-call-api.md) ou abra a **consola de teste da API** na [documentação](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) para estruturar um pedido e publicá-lo no serviço.

## <a name="step-2-post-the-request"></a>Passo 2: POST o pedido

A análise é realizada aquando da receção do pedido. Para obter informações sobre o tamanho e número de pedidos que pode enviar por minuto e segundo, consulte a secção de [limites](../overview.md#data-limits) de dados na visão geral.

Lembre-se de que o serviço não tem estado. Não são armazenados dados na sua conta. Os resultados são devolvidos imediatamente na resposta.


## <a name="step-3-view-the-results"></a>Passo 3: Ver os resultados

Todos os pedidos post devolvem uma resposta formatada jSON com os IDs e propriedades detetadas.

O resultado é devolvido imediatamente. Pode transmitir os resultados para uma aplicação que aceite a JSON ou guardar a saída para um ficheiro no sistema local. Em seguida, importe a produção para uma aplicação que pode usar para classificar, pesquisar e manipular os dados.

Os resultados do pedido de exemplo deverão assemelhar-se ao seguinte JSON. Note que é um documento com vários itens. Os resultados são apresentados em inglês. Os identificadores de idioma incluem um nome amigável e um código de idioma no formato [ISO 639-1](https://www.iso.org/standard/22109.html).

Uma pontuação positiva igual a 1,0 expressa o nível de confiança mais elevado possível da análise.

```json
    {
        "documents": [
            {
                "id": "1",
                "detectedLanguages": [
                    {
                        "name": "English",
                        "iso6391Name": "en",
                        "score": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "Spanish",
                        "iso6391Name": "es",
                        "score": 1
                    }
                ]
            },
            {
                "id": "3",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "score": 1
                    }
                ]
            },
            {
                "id": "4",
                "detectedLanguages": [
                    {
                        "name": "Chinese_Simplified",
                        "iso6391Name": "zh_chs",
                        "score": 1
                    }
                ]
            },
            {
                "id": "5",
                "detectedLanguages": [
                    {
                        "name": "Russian",
                        "iso6391Name": "ru",
                        "score": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

### <a name="ambiguous-content"></a>Conteúdo ambíguo

Em alguns casos, pode ser difícil desambiguar línguas com base na entrada. Pode utilizar `countryHint` o parâmetro para especificar um código de país de 2 letras. Por padrão, a API está a usar os "EUA" como país padrãoHint, para remover este `countryHint = ""` comportamento pode redefinir este parâmetro definindo este valor para uma cadeia vazia .

Por exemplo, "Impossível" é comum tanto ao inglês como ao francês e, se for dado com um contexto limitado, a resposta basear-se-á na sugestão do país "EUA". Se o texto tiver origem em França, este país poderá ser dado como sugestão.

**Input**

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "impossible"
            },
            {
                "id": "2",
                "text": "impossible",
                "countryHint": "fr"
            }
        ]
    }
```

O serviço tem agora um contexto adicional para fazer um melhor julgamento: 

**Saída**

```json
    {
        "documents": [
            {
                "id": "1",
                "detectedLanguages": [
                    {
                        "name": "English",
                        "iso6391Name": "en",
                        "score": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "score": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

Se o analisador não conseguir analisar a `(Unknown)`entrada, ela devolve . Um exemplo é se submeter um bloco de texto que consiste exclusivamente em algarismos árabes.

```json
    {
        "id": "5",
        "detectedLanguages": [
            {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "score": "NaN"
            }
        ]
    }
```

### <a name="mixed-language-content"></a>Conteúdo de linguagem mista

O conteúdo de linguagem mista dentro do mesmo documento devolve a língua com a maior representação no conteúdo, mas com uma classificação positiva mais baixa. O rating reflete a força marginal da avaliação. No seguinte exemplo, a entrada é uma mistura de inglês, espanhol e francês. O analisador conta carateres em cada segmento para determinar o idioma predominante.

**Input**

```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
        }
      ]
    }
```

**Saída**

A produção resultante consiste na língua predominante, com uma pontuação inferior a 1.0, o que indica um nível de confiança mais fraco.

```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 0.9375
            }
          ]
        }
      ],
      "errors": []
    }
```

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para deteção de linguagem utilizando text Analytics em Serviços Cognitivos Azure. Os seguintes pontos foram explicados e demonstrados:

+ [A deteção](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) de idiomas está disponível para uma vasta gama de línguas, variantes, dialetos e algumas línguas regionais ou culturais.
+ Os documentos da JSON no organismo de pedido incluem uma identificação e texto.
+ O pedido do `/languages` POST é para um ponto final usando uma chave de acesso personalizada [e um ponto final](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) que é válido para a sua subscrição.
+ A saída de resposta consiste em identificadores linguísticos para cada id de documento. A saída pode ser transmitida para qualquer aplicação que aceite a JSON. As aplicações exemplo incluem Excel e Power BI, para citar alguns.

## <a name="see-also"></a>Consulte também

 [Visão geral do Text Analytics](../overview.md) [frequentemente feita sperguntas (FAQ)](../text-analytics-resource-faq.md)</br>
 [Página de produto da Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analisar sentimento](text-analytics-how-to-sentiment-analysis.md)
