---
title: Detetar linguagem com o Texto Analytics REST API
titleSuffix: Azure Cognitive Services
description: Detetar linguagem utilizando o Text Analytics REST API dos Serviços Cognitivos Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 04/02/2021
ms.author: aahi
ms.openlocfilehash: e2148f56c216795c5022b86b6a1d90b476a4672e
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277288"
---
# <a name="example-detect-language-with-text-analytics"></a>Exemplo: Detetar linguagem com análise de texto

A funcionalidade de Deteção de [Idiomas](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) do Azure Text Analytics REST API avalia a entrada de texto de cada documento e devolve os identificadores linguísticos com uma pontuação que indique a força da análise.

Esta funcionalidade é útil para arquivos de conteúdo que recolhem texto arbitrário quando o idioma é desconhecido. Pode analisar os resultados desta análise para determinar o idioma que é utilizado no documento de entrada. A resposta também devolve uma pontuação que reflete a confiança do modelo. O valor da pontuação é entre 0 e 1.

A funcionalidade de Deteção de Idiomas pode detetar uma vasta gama de línguas, variantes, dialetos e algumas línguas regionais ou culturais. A lista exata de idiomas para esta funcionalidade não é publicada.

Se tiver conteúdo expresso num idioma menos utilizado, pode experimentar a funcionalidade de Deteção de Idiomas para ver se devolve um código. A resposta para línguas que não podem ser detetadas `unknown` é.

> [!TIP]
> O Text Analytics também fornece uma imagem de recipiente Docker baseada em Linux para deteção de idiomas, para que possa [instalar e executar o recipiente Text Analytics](text-analytics-how-to-install-containers.md) perto dos seus dados.

## <a name="preparation"></a>Preparação

Você deve ter documentos JSON neste formato: ID e texto.

O tamanho do documento deve ser inferior a 5.120 caracteres por documento. Você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido. A amostra a seguir é um exemplo de conteúdo que pode submeter para deteção de idiomas:

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

Para obter mais informações sobre a definição de pedido, consulte [a API de Análise de Texto](text-analytics-how-to-call-api.md). Os seguintes pontos são novamente apresentados para sua comodidade:

+ Crie um pedido POST. Para rever a documentação da API para este pedido, consulte a [API de Deteção de Idiomas.](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

+ Defina o ponto de final HTTP para a deteção de idioma. Utilize um recurso text analytics no Azure ou um recipiente de análise de [texto](text-analytics-how-to-install-containers.md)instantâneo . Deve incluir `/text/analytics/v3.0/languages` na URL. Por exemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`.

+ Desa estade um cabeçalho de pedido para incluir a [chave de acesso](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) para operações de Análise de Texto.

+ No corpo do pedido, forneça a coleção de documentos JSON que preparou para esta análise.

> [!Tip]
> Utilize o [Postman](text-analytics-how-to-call-api.md) ou abra a **consola de teste da API** na [documentação](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) para estruturar um pedido e publicá-lo no serviço.

## <a name="step-2-post-the-request"></a>Passo 2: POST o pedido

A análise é realizada aquando da receção do pedido. Para obter informações sobre o tamanho e número de pedidos que pode enviar por minuto e segundo, consulte o artigo [limites de dados.](../concepts/data-limits.md)

Lembre-se de que o serviço não tem estado. Não são armazenados dados na sua conta. Os resultados são devolvidos imediatamente na resposta.


## <a name="step-3-view-the-results"></a>Passo 3: Ver os resultados

Todos os pedidos post retornam uma resposta formatada por JSON com os IDs e propriedades detetadas.

O resultado é devolvido imediatamente. Pode transmitir os resultados para uma aplicação que aceite o JSON ou guarde a saída para um ficheiro no sistema local. Em seguida, importe a saída para uma aplicação que pode usar para ordenar, pesquisar e manipular os dados.

Os resultados do pedido de exemplo deverão assemelhar-se ao seguinte JSON. Note que é um documento com vários itens. Os resultados são apresentados em inglês. Os identificadores de idioma incluem um nome amigável e um código de idioma no formato [ISO 639-1](https://www.iso.org/standard/22109.html).

Uma pontuação positiva igual a 1,0 expressa o nível de confiança mais elevado possível da análise.

```json
{
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.99,
                "iso6391Name":"en",
                "name":"English"
            },
            "id":"1",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"es",
                "name":"Spanish"
            },
            "id":"2",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"fr",
                "name":"French"
            },
            "id":"3",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"zh_chs",
                "name":"Chinese_Simplified"
            },
            "id":"4",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"ru",
                "name":"Russian"
            },
            "id":"5",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

### <a name="ambiguous-content"></a>Conteúdo ambíguo

Em alguns casos, pode ser difícil desambiguar línguas com base na entrada. Pode utilizar o `countryHint` parâmetro para especificar um código país/região de 2 letras. Por predefinição, a API está a usar os "EUA" como país padrãoHint, para remover este comportamento pode redefinir este parâmetro definindo este valor para cadeia vazia `countryHint = ""` .

Por exemplo, "Impossível" é comum tanto ao inglês como ao francês e, se for dado com um contexto limitado, a resposta basear-se-á na sugestão do país/região dos EUA. Se o texto tiver origem em França, este país poderá ser dado como sugestão.

**Entrada**

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
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.62,
                "iso6391Name":"en",
                "name":"English"
            },
            "id":"1",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"fr",
                "name":"French"
            },
            "id":"2",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

Se o analisador não puder analisar a entrada, retorna `(Unknown)` . Um exemplo é se submeter um bloco de texto que consiste apenas em algarismos árabes.

```json
{
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.0,
                "iso6391Name":"(Unknown)",
                "name":"(Unknown)"
            },
            "id":"1",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

### <a name="mixed-language-content"></a>Conteúdo em língua mista

O conteúdo em linguagem mista dentro do mesmo documento devolve a língua com a maior representação do conteúdo, mas com uma classificação positiva mais baixa. O rating reflete a força marginal da avaliação. No seguinte exemplo, a entrada é uma mistura de inglês, espanhol e francês. O analisador conta carateres em cada segmento para determinar o idioma predominante.

**Entrada**

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
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.94,
                "iso6391Name":"es",
                "name":"Spanish"
            },
            "id":"1",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para deteção de linguagem utilizando Text Analytics em Azure Cognitive Services. Foram explicados e demonstrados os seguintes pontos:

+ [A deteção de linguagens](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) está disponível para uma vasta gama de línguas, variantes, dialetos e algumas línguas regionais ou culturais.
+ Os documentos JSON no órgão de pedido incluem uma identificação e texto.
+ O pedido do POST é para um `/languages` ponto final usando uma chave de acesso personalizada [e um ponto final](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) válido para a sua subscrição.
+ A saída de resposta consiste em identificadores de linguagem para cada documento ID. A saída pode ser transmitida para qualquer app que aceite o JSON. As aplicações de exemplo incluem Excel e Power BI, para citar alguns.

## <a name="see-also"></a>Ver também

* [Descrição geral da Análise de Texto](../overview.md)
* [Utilização da biblioteca de clientes Text Analytics](../quickstarts/client-libraries-rest-api.md)
* [Novidades](../whats-new.md)
