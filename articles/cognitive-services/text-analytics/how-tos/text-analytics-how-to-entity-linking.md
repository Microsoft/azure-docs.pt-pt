---
title: Use entity recognition with the Text Analytics API
titleSuffix: Azure Cognitive Services
description: Learn how to identify and disambiguate the identity of an entity found in text with the Text Analytics REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 11/21/2019
ms.author: aahi
ms.openlocfilehash: ae5222dcd05740ecb9747037b315c4e920b3eabd
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326631"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>How to use Named Entity Recognition in Text Analytics

The [Named Entity Recognition API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) takes unstructured text, and for each JSON document, returns a list of disambiguated entities with links to more information on the web (Wikipedia and Bing).

## <a name="entity-linking-and-named-entity-recognition"></a>Entity Linking and Named Entity Recognition

The Text Analytics' `entities` endpoint supports both named entity recognition (NER) and entity linking.

### <a name="entity-linking"></a>Ligação de Entidades
Entity linking is the ability to identify and disambiguate the identity of an entity found in text (for example, determining whether the "Mars" is being used as the planet or as the Roman god of war). This process requires the presence of a knowledge base to which recognized entities are linked - Wikipedia is used as the knowledge base for the `entities` endpoint Text Analytics.

### <a name="named-entity-recognition-ner"></a>Named Entity Recognition (NER)
Named entity recognition (NER) is the ability to identify different entities in text and categorize them into pre-defined classes, or types. 

## <a name="named-entity-recognition-v3-public-preview"></a>Named Entity Recognition v3 public preview

The next version of Named Entity Recognition is now available for public preview. It provides updates to both entity linking and Named Entity Recognition. Try it using the [API test console](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral).

:::row:::
    :::column span="":::
        **Funcionalidade**
    :::column-end:::
    ::: column span="":::
        **Descrição** 
    :::column-end:::
:::row-end:::
<!-- expanded types and subtypes row-->
:::row:::
    :::column span="":::
        Expanded entity types and subtypes
    :::column-end:::
    :::column span="":::
     Expanded classification and detection for several named entity types.
    :::column-end:::
:::row-end:::
<!-- separate endpoints row-->
:::row:::
    :::column span="":::
        Separate request endpoints 
    :::column-end:::
    :::column span="":::
        Separate endpoints for sending entity linking and NER requests.
    :::column-end:::
:::row-end:::
<!-- model-version row -->
:::row:::
    :::column span="":::
        `model-version` parameter
    :::column-end:::
    :::column span="":::
        An optional parameter for choosing a version of the Text Analytics model. Currently only the default model is available for use.
    :::column-end:::
:::row-end:::

### <a name="entity-types"></a>Entity types

Named Entity Recognition v3 provides expanded detection across multiple types. Currently, NER v3 can recognize the following categories of entities. For a detailed list of supported entities and languages, see the [Named entity types](../named-entity-types.md) article.

* Geral
* Personal Information 

### <a name="request-endpoints"></a>Request endpoints

Named Entity Recognition v3 uses separate endpoints for NER and entity linking requests. Use a URL format below based on your request:

NER
* General entities - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Personal information entities - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Entity linking
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Model versioning

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

## <a name="supported-types-for-named-entity-recognition-v2"></a>Supported Types for Named Entity Recognition v2

> [!NOTE]
> The following entities are supported by Named Entity Recognition(NER) version 2. [NER v3](#named-entity-recognition-v3-public-preview) is in public preview, and greatly expands the number and depth of the entities recognized in text.   

| Tipo  | SubType | Exemplo |
|:-----------   |:------------- |:---------|
| Pessoa        | N/A\*         | "Jeff", "Bill Gates"     |
| Localização      | N/A\*         | "Redmond, Washington", "Paris"  |
| Organização  | N/A\*         | "Microsoft"   |
| Quantidade      | Number        | "6", "seis"     |
| Quantidade      | Percentagem    | "50%", "cinquenta por cento"|
| Quantidade      | Ordinal       | "2.º", "segundo"     |
| Quantidade      | Idade           | "90 day old", "30 years old"    |
| Quantidade      | Moeda      | "10,99 $"     |
| Quantidade      | Dimensão     | "10 milhas", "40 cm"     |
| Quantidade      | Temperatura   | "32 graus"    |
| DateTime      | N/A\*         | "18:30 4 de fevereiro de 2012"      |
| DateTime      | Date          | "2 de maio de 2017", "02/05/2017"   |
| DateTime      | Tempo          | "8am", "8:00"  |
| DateTime      | DateRange     | "2 a 5 de maio"    |
| DateTime      | TimeRange     | "18:00 às 19:00"     |
| DateTime      | Duração      | "1 minuto e 45 segundos"   |
| DateTime      | Definir           | "todas as terças"     |
| URL           | N/A\*         | "https:\//www.bing.com"    |
| E-mail         | N/A\*         | "support@contoso.com" |
| US Phone Number  | N/A\*         | (US phone numbers only) "(312) 555-0176" |
| Endereço IP    | N/A\*         | "10.0.0.100" |

\* Depending on the input and extracted entities, certain entities may omit the `SubType`.  All the supported entity types listed are available only for the English, Chinese-Simplified, French, German, and Spanish languages.

### <a name="language-support"></a>Suporte de idiomas

Using entity linking in various languages requires using a corresponding knowledge base in each language. For entity linking in Text Analytics, this means each language that is supported by the `entities` endpoint will link to the corresponding Wikipedia corpus in that language. Since the size of corpora varies between languages, it is expected that the entity linking functionality's recall will also vary. See the [language support](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) article for more information.

## <a name="preparation"></a>Preparação

You must have JSON documents in this format: ID, text, language

For currently supported languages, see [this list](../text-analytics-supported-languages.md).

Document size must be under 5,120 characters per document, and you can have up to 1,000 items (IDs) per collection. A coleção é enviada no corpo do pedido. The following example is an illustration of content you might submit to the entity linking end.

```json
    {
        "documents": [
            {
                "id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
            },
            {
                "id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Passo 1: estruturar o pedido

Pode obter detalhes sobre a definição do pedido em [Como chamar a API de Análise de Texto](text-analytics-how-to-call-api.md). Os seguintes pontos são novamente apresentados para sua comodidade:

+ Crie um pedido **POST**. Review the API documentation for this request: [Entities API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Set the HTTP endpoint for key phrase extraction by using either a Text Analytics resource on Azure or an instantiated [Text Analytics container](text-analytics-how-to-install-containers.md). You must include `/text/analytics/v2.1/entities`. Por exemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`.

+ Set a request header to include [the access key](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) for Text Analytics operations.

+ No corpo do pedido, forneça a coleção de documentos JSON que preparou para esta análise.

> [!Tip]
> Utilize o [Postman](text-analytics-how-to-call-api.md) ou abra a **consola de teste da API** na [documentação](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) para estruturar um pedido e publicá-lo no serviço.

## <a name="step-2-post-the-request"></a>Passo 2: publicar o pedido

A análise é realizada aquando da receção do pedido. See the [data limits](../overview.md#data-limits) section in the overview for information on the size and number of requests you can send per minute and second.

Lembre-se de que o serviço não tem estado. Não são armazenados dados na sua conta. Os resultados são devolvidos imediatamente na resposta.

## <a name="step-3-view-results"></a>Passo 3: ver resultados

Todos os pedidos POST devolvem uma resposta formatada JSON com os IDs e as propriedades detetadas.

O resultado é devolvido imediatamente. Pode transmitir os resultados para uma aplicação que aceite JSON ou guardar o resultado num ficheiro no sistema local e, em seguida, importá-lo para uma aplicação que lhe permita ordenar, procurar e manipular os dados.

An example of the output for entity linking is shown next:

```json
    {
        "Documents": [
            {
                "Id": "1",
                "Entities": [
                    {
                        "Name": "Jeff",
                        "Matches": [
                            {
                                "Text": "Jeff",
                                "Offset": 0,
                                "Length": 4
                            }
                        ],
                        "Type": "Person"
                    },
                    {
                        "Name": "three dozen",
                        "Matches": [
                            {
                                "Text": "three dozen",
                                "Offset": 12,
                                "Length": 11
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50",
                        "Matches": [
                            {
                                "Text": "50",
                                "Offset": 49,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50%",
                        "Matches": [
                            {
                                "Text": "50%",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            },
            {
                "Id": "2",
                "Entities": [
                    {
                        "Name": "Great Depression",
                        "Matches": [
                            {
                                "Text": "The Great Depression",
                                "Offset": 0,
                                "Length": 20
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Great Depression",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                        "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                    },
                    {
                        "Name": "1929",
                        "Matches": [
                            {
                                "Text": "1929",
                                "Offset": 30,
                                "Length": 4
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "By 1933",
                        "Matches": [
                            {
                                "Text": "By 1933",
                                "Offset": 36,
                                "Length": 7
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "Gross domestic product",
                        "Matches": [
                            {
                                "Text": "GDP",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Gross domestic product",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                        "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                    },
                    {
                        "Name": "United States",
                        "Matches": [
                            {
                                "Text": "America",
                                "Offset": 56,
                                "Length": 7
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "United States",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                        "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                        "Type": "Location"
                    },
                    {
                        "Name": "25",
                        "Matches": [
                            {
                                "Text": "25",
                                "Offset": 72,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "25%",
                        "Matches": [
                            {
                                "Text": "25%",
                                "Offset": 72,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            }
        ],
        "Errors": []
    }
```

## <a name="summary"></a>Resumo

In this article, you learned concepts and workflow for entity linking using Text Analytics in Cognitive Services. Em resumo:

+ [Entities API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) is available for selected languages.
+ JSON documents in the request body include an ID, text, and language code.
+ O pedido POST refere-se a um ponto final `/entities` com recurso a uma [chave de acesso personalizada e um ponto final](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) válido para a sua subscrição.
+ Response output, which consists of linked entities (including confidence scores, offsets, and web links, for each document ID) can be used in any application

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [API de Análise de Texto](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [Descrição Geral da Análise de Texto](../overview.md)
* [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
* [Página de produto da Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759712)
