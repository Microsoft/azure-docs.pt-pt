---
title: Detetar idioma com a API de REST de análise de texto | Documentos da Microsoft
description: Detete idioma ao utilizar a API de REST de análise de texto dos serviços cognitivos do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: e1adeb34cf999f471bb183e4d7de9c65427252bb
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67986514"
---
# <a name="example-detect-language-with-text-analytics"></a>Exemplo: Detetar idioma com análise de texto

O [deteção de idioma](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) recurso da API REST de análise de texto do Azure avalia a introdução de texto para cada documento e devolve os identificadores de idioma com uma pontuação que indica a força da análise.

Esta funcionalidade é útil para arquivos de conteúdo que recolhem texto arbitrário quando o idioma é desconhecido. Pode analisar os resultados desta análise para determinar o idioma que é utilizado no documento de entrada. A resposta também devolve uma pontuação que reflete a confiança do modelo. O valor de pontuação é entre 0 e 1.

A funcionalidade de deteção de idioma pode detectar uma grande variedade de linguagens, variantes, dialetos e alguns idiomas regionais ou culturais. A lista exata de idiomas para esta funcionalidade não está publicada.

Se tiver conteúdo expresso numa linguagem utilizada com menos frequência, pode experimentar a funcionalidade de deteção de idioma para ver se ele retorna um código. A resposta para idiomas que não puderem ser detetadas está `unknown`.

> [!TIP]
> Análise de texto também fornece um Docker baseado em Linux imagem de contentor para a deteção de idioma, para que possa [instalar e executar o contentor de análise de texto](text-analytics-how-to-install-containers.md) perto dos seus dados.

## <a name="preparation"></a>Preparação

Tem de ter documentos JSON no seguinte formato: ID e o texto.

O tamanho do documento tem de ser em 5,120 carateres por documento. Pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido. O exemplo a seguir é um exemplo de conteúdo, que pode enviar para a deteção de idioma:

   ```
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

## <a name="step-1-structure-the-request"></a>Passo 1: O pedido de estrutura

Para obter mais informações sobre a definição de pedido, consulte [chamar a API de análise de texto](text-analytics-how-to-call-api.md). Os seguintes pontos são novamente apresentados para sua comodidade:

+ Crie um pedido POST. Para rever a documentação da API para este pedido, consulte a [API de deteção de idioma](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

+ Defina o ponto de final HTTP para a deteção de idioma. Utilizar um recurso de análise de texto no Azure ou um instanciadas [contentor de análise de texto](text-analytics-how-to-install-containers.md). Tem de incluir o `/languages` recurso: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`.

+ Defina um cabeçalho de pedido para incluir a chave de acesso para operações de Análise de Texto. Para obter mais informações, consulte [localizar pontos de extremidade e chaves de acesso](text-analytics-how-to-access-key.md).

+ No corpo do pedido, forneça a coleção de documentos JSON que preparou para esta análise.

> [!Tip]
> Utilize o [Postman](text-analytics-how-to-call-api.md) ou abra a **consola de teste da API** na [documentação](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) para estruturar um pedido e publicá-lo no serviço.

## <a name="step-2-post-the-request"></a>Passo 2: O pedido POST

A análise é realizada aquando da receção do pedido. Para obter informações sobre o tamanho e número de pedidos pode enviar por minuto e segundo lugar, consulte a [limites de dados](../overview.md#data-limits) secção na descrição geral.

Lembre-se de que o serviço não tem estado. Não são armazenados dados na sua conta. Os resultados são devolvidos imediatamente na resposta.


## <a name="step-3-view-the-results"></a>Passo 3: Ver os resultados

Todos os pedidos POST devolverem uma resposta JSON formatado com os IDs e detetado propriedades.

O resultado é devolvido imediatamente. Pode transmitir os resultados para uma aplicação que aceite JSON ou salvar a saída num arquivo no sistema local. Em seguida, importe a saída num aplicativo que pode utilizar para ordenar, procure e manipular os dados.

Os resultados do pedido de exemplo deverão assemelhar-se ao seguinte JSON. Tenha em atenção que é um documento com vários itens. Os resultados são apresentados em inglês. Os identificadores de idioma incluem um nome amigável e um código de idioma no formato [ISO 639-1](https://www.iso.org/standard/22109.html).

Uma pontuação positiva igual a 1,0 expressa o nível de confiança mais elevado possível da análise.



```
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
```

### <a name="ambiguous-content"></a>Conteúdo ambíguo

Se o analisador não é possível analisar a entrada, ele retorna `(Unknown)`. Um exemplo é se enviar um bloco de texto que consiste somente em numerais árabes.

```
    {
      "id": "5",
      "detectedLanguages": [
        {
          "name": "(Unknown)",
          "iso6391Name": "(Unknown)",
          "score": "NaN"
        }
      ]
```
### <a name="mixed-language-content"></a>Conteúdo de linguagens mistas

Conteúdo de linguagens mistas dentro do mesmo documento devolve o idioma com a representação maior no conteúdo, mas com uma classificação positiva inferior. A classificação reflete a força marginal da avaliação. No seguinte exemplo, a entrada é uma mistura de inglês, espanhol e francês. O analisador conta carateres em cada segmento para determinar o idioma predominante.

**Input (Entrada)**

```
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

A saída resultante é composta por idioma predominante, com uma classificação de inferior a 1.0, que indica um nível mais fraco de confiança.

```
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

Neste artigo, aprendeu conceitos e fluxo de trabalho para deteção de idioma ao utilizar a análise de texto nos serviços cognitivos do Azure. Os seguintes pontos foram explicados e demonstrei:

+ [Deteção de idioma](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) está disponível para uma ampla variedade de linguagens, variantes, dialetos e alguns idiomas regionais ou culturais.
+ Documentos JSON no corpo do pedido incluem um ID e o texto.
+ O pedido POST é um `/languages` ponto final utilizando personalizado [aceder a chave e um ponto de extremidade](text-analytics-how-to-access-key.md) que é válido para a sua subscrição.
+ Saída de resposta consiste em identificadores de idioma para cada ID de documento. A saída pode ser transmitida para qualquer aplicação que aceita JSON. As aplicações de exemplo incluem o Excel e o Power BI, para citar alguns.

## <a name="see-also"></a>Consulte também 

 [Descrição Geral da Análise de Texto](../overview.md)  
 [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Página de produto da Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Analisar sentimentos](text-analytics-how-to-sentiment-analysis.md)
