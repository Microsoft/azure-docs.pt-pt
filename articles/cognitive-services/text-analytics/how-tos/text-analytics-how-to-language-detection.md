---
title: Detectar linguagem com a API REST do Análise de Texto
titleSuffix: Azure Cognitive Services
description: Detectar linguagem usando a API REST do Análise de Texto dos serviços cognitivas do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: d34f3a03e1bcd35c270d13c4dda57d0394a36e4b
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70387797"
---
# <a name="example-detect-language-with-text-analytics"></a>Exemplo: Detectar idioma com Análise de Texto

O recurso [detecção de idioma](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) da API REST do Azure análise de texto avalia a entrada de texto de cada documento e retorna identificadores de idioma com uma pontuação que indica a força da análise.

Esta funcionalidade é útil para arquivos de conteúdo que recolhem texto arbitrário quando o idioma é desconhecido. Pode analisar os resultados desta análise para determinar o idioma que é utilizado no documento de entrada. A resposta também retorna uma pontuação que reflete a confiança do modelo. O valor de pontuação está entre 0 e 1.

O recurso Detecção de Idioma pode detectar uma ampla gama de linguagens, variantes, dialetos e algumas linguagens regionais ou culturais. A lista exata de idiomas para esse recurso não está publicada.

Se você tiver conteúdo expresso em uma linguagem usada com menos frequência, poderá tentar o Detecção de Idioma recurso para ver se ele retorna um código. A resposta para idiomas que não podem ser detectados é `unknown`.

> [!TIP]
> O Análise de Texto também fornece uma imagem de contêiner do Docker baseada em Linux para detecção de idioma, para que você possa [instalar e executar o contêiner de análise de texto](text-analytics-how-to-install-containers.md) perto dos seus dados.

## <a name="preparation"></a>Preparação

Você deve ter documentos JSON neste formato: ID e texto.

O tamanho do documento deve ter menos de 5.120 caracteres por documento. Você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido. O exemplo a seguir é um exemplo de conteúdo que você pode enviar para detecção de idioma:

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

## <a name="step-1-structure-the-request"></a>Passo 1: Estruturar a solicitação

Para obter mais informações sobre a definição de solicitação, consulte [chamar o API de análise de texto](text-analytics-how-to-call-api.md). Os seguintes pontos são novamente apresentados para sua comodidade:

+ Crie uma solicitação POST. Para examinar a documentação da API para essa solicitação, consulte a [api detecção de idioma](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

+ Defina o ponto de final HTTP para a deteção de idioma. Use um recurso de Análise de Texto no Azure ou um contêiner de [análise de texto](text-analytics-how-to-install-containers.md)instanciado. Você deve incluir `/text/analytics/v2.1/languages` na URL. Por exemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/languages`.

+ Defina um cabeçalho de solicitação para incluir a [chave de acesso](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) para operações de análise de texto.

+ No corpo do pedido, forneça a coleção de documentos JSON que preparou para esta análise.

> [!Tip]
> Utilize o [Postman](text-analytics-how-to-call-api.md) ou abra a **consola de teste da API** na [documentação](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) para estruturar um pedido e publicá-lo no serviço.

## <a name="step-2-post-the-request"></a>Passo 2: POSTAR a solicitação

A análise é realizada aquando da receção do pedido. Para obter informações sobre o tamanho e o número de solicitações que você pode enviar por minuto e segundo, consulte a seção [limites de dados](../overview.md#data-limits) na visão geral.

Lembre-se de que o serviço não tem estado. Não são armazenados dados na sua conta. Os resultados são devolvidos imediatamente na resposta.


## <a name="step-3-view-the-results"></a>Passo 3: Ver os resultados

Todas as solicitações POST retornam uma resposta formatada em JSON com as IDs e as propriedades detectadas.

O resultado é devolvido imediatamente. Você pode transmitir os resultados para um aplicativo que aceita JSON ou salva a saída em um arquivo no sistema local. Em seguida, importe a saída para um aplicativo que você pode usar para classificar, Pesquisar e manipular os dados.

Os resultados do pedido de exemplo deverão assemelhar-se ao seguinte JSON. Observe que ele é um documento com vários itens. Os resultados são apresentados em inglês. Os identificadores de idioma incluem um nome amigável e um código de idioma no formato [ISO 639-1](https://www.iso.org/standard/22109.html).

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

Em alguns casos, pode ser difícil eliminar a ambiguidade de idiomas com base na entrada. Você pode usar o `countryHint` parâmetro para especificar um código de país de duas letras. Por padrão, a API está usando o "US" como o countryHint padrão, para remover esse comportamento, você pode redefinir esse parâmetro definindo esse valor como cadeia `countryHint = ""` de caracteres vazia.

Por exemplo, "impossível" é comum tanto em inglês quanto em francês e, se for fornecido com contexto limitado, a resposta será baseada na dica de país "EUA". Se a origem do texto for conhecida por ser proveniente da França que pode ser fornecida como uma dica.

**Input (Entrada)**

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

O serviço agora tem contexto adicional para fazer um melhor julgamento: 

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

Se o analisador não conseguir analisar a entrada, `(Unknown)`ele retornará. Um exemplo é se você enviar um bloco de texto que consiste apenas em numerais árabes.

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

### <a name="mixed-language-content"></a>Conteúdo de idioma misto

O conteúdo de idioma misto no mesmo documento retorna o idioma com a maior representação no conteúdo, mas com uma classificação positiva mais baixa. A classificação reflete a força marginal da avaliação. No seguinte exemplo, a entrada é uma mistura de inglês, espanhol e francês. O analisador conta carateres em cada segmento para determinar o idioma predominante.

**Input (Entrada)**

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

A saída resultante consiste no idioma predominante, com uma pontuação inferior a 1,0, que indica um nível mais fraco de confiança.

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

Neste artigo, você aprendeu os conceitos e o fluxo de trabalho para detecção de idioma usando Análise de Texto nos serviços cognitivas do Azure. Os seguintes pontos foram explicados e demonstrados:

+ A [detecção de idioma](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) está disponível para uma ampla gama de linguagens, variantes, dialetos e algumas linguagens regionais ou culturais.
+ Os documentos JSON no corpo da solicitação incluem uma ID e um texto.
+ A solicitação post é para um `/languages` ponto de extremidade usando uma [chave de acesso personalizada e um ponto de extremidade](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) válido para sua assinatura.
+ A saída de resposta consiste em identificadores de idioma para cada ID de documento. A saída pode ser transmitida para qualquer aplicativo que aceite JSON. Aplicativos de exemplo incluem Excel e Power BI, para citar alguns.

## <a name="see-also"></a>Consulte também

 [Visão geral de análise de texto](../overview.md) [Perguntas frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Página de produto da Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Analisar sentimentos](text-analytics-how-to-sentiment-analysis.md)
