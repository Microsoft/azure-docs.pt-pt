---
title: Usar o reconhecimento de entidade com o API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer entidades usando a API REST do Análise de Texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 642b21624ce3ffc993d5f29a413845044d703fd7
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984271"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Como usar o reconhecimento de entidade nomeada no Análise de Texto

A [API de reconhecimento de entidade nomeada](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) usa texto não estruturado e, para cada documento JSON, retorna uma lista de entidades desambiguadas com links para mais informações na Web (Wikipédia e Bing).

## <a name="entity-linking-and-named-entity-recognition"></a>Vinculação de entidade e reconhecimento de entidade nomeada

O ponto de `entities` extremidade análise de texto ' dá suporte ao reconhecimento de entidade nomeada (Ner) e à vinculação de entidade.

### <a name="entity-linking"></a>Ligar à Entidade
A vinculação de entidades é a capacidade de identificar e desambiguar a identidade de uma entidade encontrada em texto (por exemplo, determinar se o "Mars" está sendo usado como o planeta ou como o Deus romano de guerra). Esse processo requer a presença de uma base de dados de conhecimento na qual as entidades reconhecidas são vinculadas-a Wikipédia `entities` é usada como a base de dados de conhecimento para o ponto de extremidade análise de texto.

### <a name="named-entity-recognition-ner"></a>Reconhecimento de entidade nomeada (NER)
O NER (reconhecimento de entidade nomeada) é a capacidade de identificar diferentes entidades no texto e categorizá-las em classes predefinidas. As classes de entidades com suporte estão listadas abaixo.

No Análise de Texto [versão 2,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634), a vinculação de entidade e o reconhecimento de entidade nomeada (Ner) estão disponíveis para vários idiomas. Consulte o artigo [suporte ao idioma](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) para obter mais informações.

### <a name="language-support"></a>Suporte de idiomas

Usar a vinculação de entidades em várias linguagens requer o uso de uma base de dados de conhecimento correspondente em cada idioma. Para vinculação de entidade em análise de texto, isso significa que cada idioma com suporte no `entities` ponto de extremidade será vinculado ao corpus da Wikipédia correspondente nesse idioma. Como o tamanho de subinstrução varia entre os idiomas, é esperado que a Recall da funcionalidade de vinculação de entidades também varie.

## <a name="supported-types-for-named-entity-recognition"></a>Tipos com suporte para reconhecimento de entidade nomeada

| Type  | Subtipo | Exemplo |
|:-----------   |:------------- |:---------|
| Pessoa        | N/A\*         | "Jeff", "Bill Gates"     |
| Location      | N/A\*         | "Redmond, Washington", "Paris"  |
| Organização  | N/A\*         | "Microsoft"   |
| Quantidade      | Número        | "6", "six"     |
| Quantidade      | Percentagem    | "50%", "cinquenta por cento"|
| Quantidade      | Ordinal       | "2.º", "segundo"     |
| Quantidade      | Idade           | "dia de 90 dias antigos", "30 anos de idade"    |
| Quantidade      | Currency      | "10,99 $"     |
| Quantidade      | Dimensão     | "10 milhas", "40 cm"     |
| Quantidade      | Temperatura   | "32 graus"    |
| DateTime      | N/A\*         | "18:30 4 de fevereiro de 2012"      |
| DateTime      | Date          | "2 de maio de 2017", "02/05/2017"   |
| DateTime      | Time          | "8:00", "8:00"  |
| DateTime      | DateRange     | "2 a 5 de maio"    |
| DateTime      | TimeRange     | "18:00 às 19:00"     |
| DateTime      | Duration      | "1 minuto e 45 segundos"   |
| DateTime      | Definir           | "todas as terças"     |
| URL           | N/A\*         | "https:\//www.bing.com"    |
| Email         | N/A\*         | "support@contoso.com" |

\*Dependendo das entidades de entrada e extraídas, determinadas entidades podem omitir `SubType`o.  Todos os tipos de entidade com suporte listados estão disponíveis apenas para os idiomas inglês, chinês simplificado, francês, alemão e espanhol.



## <a name="preparation"></a>Preparação

Você deve ter documentos JSON neste formato: ID, texto, idioma

Para os idiomas com suporte no momento, consulte [esta lista](../text-analytics-supported-languages.md).

O tamanho do documento deve ter menos de 5.120 caracteres por documento e você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido. O exemplo a seguir é uma ilustração do conteúdo que você pode enviar para a extremidade de vinculação de entidade.

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

## <a name="step-1-structure-the-request"></a>Passo 1: Estruturar a solicitação

Pode obter detalhes sobre a definição do pedido em [Como chamar a API de Análise de Texto](text-analytics-how-to-call-api.md). Os seguintes pontos são novamente apresentados para sua comodidade:

+ Crie um pedido **POST**. Examine a documentação da API para esta solicitação: [API de entidades](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Defina o ponto de extremidade HTTP para extração de frase-chave usando um recurso de Análise de Texto no Azure ou um [contêiner de análise de texto](text-analytics-how-to-install-containers.md)instanciado. Você deve incluir `/text/analytics/v2.1/entities`. Por exemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`.

+ Defina um cabeçalho de solicitação para incluir [a chave de acesso](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) para operações de análise de texto.

+ No corpo do pedido, forneça a coleção de documentos JSON que preparou para esta análise.

> [!Tip]
> Utilize o [Postman](text-analytics-how-to-call-api.md) ou abra a **consola de teste da API** na [documentação](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) para estruturar um pedido e publicá-lo no serviço.

## <a name="step-2-post-the-request"></a>Passo 2: Postar a solicitação

A análise é realizada aquando da receção do pedido. Consulte a seção [limites de dados](../overview.md#data-limits) na visão geral para obter informações sobre o tamanho e o número de solicitações que você pode enviar por minuto e segundo.

Lembre-se de que o serviço não tem estado. Não são armazenados dados na sua conta. Os resultados são devolvidos imediatamente na resposta.

## <a name="step-3-view-results"></a>Passo 3: Ver resultados

Todos os pedidos POST devolvem uma resposta formatada JSON com os IDs e as propriedades detetadas.

O resultado é devolvido imediatamente. Pode transmitir os resultados para uma aplicação que aceite JSON ou guardar o resultado num ficheiro no sistema local e, em seguida, importá-lo para uma aplicação que lhe permita ordenar, procurar e manipular os dados.

Veja a seguir um exemplo de saída para vinculação de entidade:

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

Neste artigo, você aprendeu os conceitos e o fluxo de trabalho para vinculação de entidade usando Análise de Texto em serviços cognitivas. Em resumo:

+ A [API de entidades](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) está disponível para os idiomas selecionados.
+ Os documentos JSON no corpo da solicitação incluem uma ID, um texto e um código de idioma.
+ O pedido POST refere-se a um ponto final `/entities` com recurso a uma [chave de acesso personalizada e um ponto final](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) válido para a sua subscrição.
+ A saída de resposta, que consiste em entidades vinculadas (incluindo pontuações de confiança, deslocamentos e links da Web, para cada ID de documento) pode ser usada em qualquer aplicativo

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [API de Análise de Texto](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [Descrição Geral da Análise de Texto](../overview.md)
* [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
* [Página de produto da Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759712)
