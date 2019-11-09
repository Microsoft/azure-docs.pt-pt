---
title: Usar o reconhecimento de entidade com o API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Saiba como identificar e desambiguar a identidade de uma entidade encontrada em texto com a API REST do Análise de Texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 10/21/2019
ms.author: aahi
ms.openlocfilehash: b139f473bb20dbecde75c3b08dd92371849f096e
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835671"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Como usar o reconhecimento de entidade nomeada no Análise de Texto

A [API de reconhecimento de entidade nomeada](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) usa texto não estruturado e, para cada documento JSON, retorna uma lista de entidades desambiguadas com links para mais informações na Web (Wikipédia e Bing).

## <a name="entity-linking-and-named-entity-recognition"></a>Vinculação de entidade e reconhecimento de entidade nomeada

O ponto de extremidade do Análise de Texto ' `entities` dá suporte ao reconhecimento de entidade nomeada (NER) e à vinculação de entidade.

### <a name="entity-linking"></a>Ligar à Entidade
A vinculação de entidades é a capacidade de identificar e desambiguar a identidade de uma entidade encontrada em texto (por exemplo, determinar se o "Mars" está sendo usado como o planeta ou como o Deus romano de guerra). Esse processo requer a presença de uma base de dados de conhecimento para a qual as entidades reconhecidas são vinculadas-a Wikipédia é usada como a base de dados de conhecimento para o ponto de extremidade `entities` Análise de Texto.

### <a name="named-entity-recognition-ner"></a>Reconhecimento de entidade nomeada (NER)
O NER (reconhecimento de entidade nomeada) é a capacidade de identificar diferentes entidades no texto e categorizá-las em classes predefinidas ou tipos. 

## <a name="named-entity-recognition-v3-public-preview"></a>Visualização pública de reconhecimento de entidade nomeada v3

A [próxima versão do reconhecimento de entidade nomeada](https://cognitiveusw2ppe.portal.azure-api.net/docs/services/TextAnalytics-v3-0-Preview-1/operations/56f30ceeeda5650db055a3c7/console) agora está disponível para visualização pública. Ele fornece atualizações para vinculação de entidade e reconhecimento de entidade nomeada. 

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
        Tipos de entidade e subtipos expandidos
    :::column-end:::
    :::column span="":::
     Classificação e detecção expandidas para vários tipos de entidade nomeados.
    :::column-end:::
:::row-end:::
<!-- separate endpoints row-->
:::row:::
    :::column span="":::
        Pontos de extremidade de solicitação separados 
    :::column-end:::
    :::column span="":::
        Separe os pontos de extremidade para enviar solicitações de vinculação e NER de entidade.
    :::column-end:::
:::row-end:::
<!-- model-version row -->
:::row:::
    :::column span="":::
        `model-version` parâmetro
    :::column-end:::
    :::column span="":::
        Um parâmetro opcional para escolher uma versão do modelo de Análise de Texto. No momento, apenas o modelo padrão está disponível para uso.
    :::column-end:::
:::row-end:::

### <a name="entity-types"></a>Tipos de entidade

O reconhecimento de entidade nomeada v3 fornece detecção expandida entre vários tipos. Atualmente, o NER v3 pode reconhecer as seguintes categorias de entidades. Para obter uma lista detalhada de entidades e linguagens com suporte, consulte o artigo [tipos de entidade nomeada](../named-entity-types.md) .

* Geral
* Informações pessoais 

### <a name="request-endpoints"></a>Pontos de extremidade de solicitação

O reconhecimento de entidade nomeada v3 usa pontos de extremidade separados para solicitações de vinculação de NER e entidade. Use um formato de URL abaixo com base em sua solicitação:

NER
* Entidades gerais-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Entidades de informações pessoais-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Vinculação de entidade
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Controle de versão de modelo

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

## <a name="supported-types-for-named-entity-recognition-v2"></a>Tipos com suporte para reconhecimento de entidade nomeada v2

> [!NOTE]
> As entidades a seguir têm suporte pelo reconhecimento de entidade nomeada (NER) versão 2. O [Ner v3](#named-entity-recognition-v3-public-preview) está em visualização pública e expande muito o número e a profundidade das entidades reconhecidas no texto.   

| Tipo  | Subtipo | Exemplo |
|:-----------   |:------------- |:---------|
| Pessoa        | \* N/A         | "Jeff", "Bill Gates"     |
| Localização      | \* N/A         | "Redmond, Washington", "Paris"  |
| Organização  | \* N/A         | "Microsoft"   |
| Quantidade      | Number        | "6", "seis"     |
| Quantidade      | Percentagem    | "50%", "cinquenta por cento"|
| Quantidade      | Ordinal       | "2.º", "segundo"     |
| Quantidade      | Idade           | "dia de 90 dias antigos", "30 anos de idade"    |
| Quantidade      | Moeda      | "10,99 $"     |
| Quantidade      | Dimensão     | "10 milhas", "40 cm"     |
| Quantidade      | Temperatura   | "32 graus"    |
| DateTime      | \* N/A         | "18:30 4 de fevereiro de 2012"      |
| DateTime      | Date          | "2 de maio de 2017", "02/05/2017"   |
| DateTime      | Hora          | "8:00", "8:00"  |
| DateTime      | DateRange     | "2 a 5 de maio"    |
| DateTime      | TimeRange     | "18:00 às 19:00"     |
| DateTime      | Duração      | "1 minuto e 45 segundos"   |
| DateTime      | Definir           | "todas as terças"     |
| do IdP           | \* N/A         | "https:\//www.bing.com"    |
| Email         | \* N/A         | "support@contoso.com" |

\* dependendo das entidades de entrada e extraídas, determinadas entidades podem omitir a `SubType`.  Todos os tipos de entidade com suporte listados estão disponíveis apenas para idiomas inglês, chinês simplificado, francês, alemão e espanhol.

### <a name="language-support"></a>Suporte de idiomas

Usar a vinculação de entidades em várias linguagens requer o uso de uma base de dados de conhecimento correspondente em cada idioma. Para vinculação de entidade em Análise de Texto, isso significa que cada idioma com suporte pelo ponto de extremidade `entities` será vinculado ao corpus da Wikipédia correspondente nesse idioma. Como o tamanho de subinstrução varia entre os idiomas, é esperado que a Recall da funcionalidade de vinculação de entidades também varie. Consulte o artigo [suporte ao idioma](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) para obter mais informações.

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

## <a name="step-1-structure-the-request"></a>Passo 1: estruturar o pedido

Pode obter detalhes sobre a definição do pedido em [Como chamar a API de Análise de Texto](text-analytics-how-to-call-api.md). Os seguintes pontos são novamente apresentados para sua comodidade:

+ Crie um pedido **POST**. Examine a documentação da API para esta solicitação: [API de entidades](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Defina o ponto de extremidade HTTP para extração de frase-chave usando um recurso de Análise de Texto no Azure ou um [contêiner de análise de texto](text-analytics-how-to-install-containers.md)instanciado. Você deve incluir `/text/analytics/v2.1/entities`. Por exemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`.

+ Defina um cabeçalho de solicitação para incluir [a chave de acesso](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) para operações de análise de texto.

+ No corpo do pedido, forneça a coleção de documentos JSON que preparou para esta análise.

> [!Tip]
> Utilize o [Postman](text-analytics-how-to-call-api.md) ou abra a **consola de teste da API** na [documentação](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) para estruturar um pedido e publicá-lo no serviço.

## <a name="step-2-post-the-request"></a>Passo 2: publicar o pedido

A análise é realizada aquando da receção do pedido. Consulte a seção [limites de dados](../overview.md#data-limits) na visão geral para obter informações sobre o tamanho e o número de solicitações que você pode enviar por minuto e segundo.

Lembre-se de que o serviço não tem estado. Não são armazenados dados na sua conta. Os resultados são devolvidos imediatamente na resposta.

## <a name="step-3-view-results"></a>Passo 3: ver resultados

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [API de Análise de Texto](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [Descrição Geral da Análise de Texto](../overview.md)
* [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
* [Página de produto da Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759712)
