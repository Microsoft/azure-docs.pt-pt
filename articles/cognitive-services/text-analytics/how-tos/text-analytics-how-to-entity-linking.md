---
title: Utilizar o reconhecimento de entidades com a API de análise de texto
titleSuffix: Azure Cognitive Services
description: Aprenda a reconhecer entidades usando a API de REST de análise de texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 1ea34d69c867d2d14496320f497df2ece5280e0c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58009352"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics-preview"></a>Como utilizar o reconhecimento de entidades com o nome na análise de texto (pré-visualização)

O [a API de reconhecimento de entidades](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) usa texto não estruturado e, para cada documento JSON, retorna uma lista de entidades a ambigüidade removidas com ligações para obter mais informações na web (Wikipédia e Bing). 

## <a name="entity-linking-and-named-entity-recognition"></a>Ligação de entidades e o reconhecimento de entidades

Análise de texto `entities` suporta de ponto final, ambos com o nome o reconhecimento de entidades (NER) e a entidade de ligação.

### <a name="entity-linking"></a>Ligar à Entidade
Ligação de entidades é a capacidade de identificar e desambiguar a identidade de uma entidade encontrada no texto (por exemplo, que determina se o "Mars" está a ser utilizado como o planeta ou como o Deus Roman da war). Este processo requer a presença de um conhecimento base para a qual reconhecido entidades são vinculadas - Wikipedia é utilizada como a base de dados de conhecimento para o `entities` ponto final de análise de texto.

Na análise de texto [versão 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634), apenas a ligação de entidades está disponível.

### <a name="named-entity-recognition-ner"></a>O reconhecimento de entidades (NER)
Com o nome de entidades (NER) é a capacidade de identificar as diferentes entidades em texto e categorize-as em classes predefinidas. As classes de entidades de suportados estão listadas abaixo.

Na análise de texto [versão 2.1-Preview](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634), ligação de entidades e o reconhecimento de entidades (NER) estão disponíveis.

### <a name="language-support"></a>Suporte de idiomas

Através do entity linking em várias linguagens requer a utilização de uma base de dados de conhecimento correspondente em cada linguagem. Para a entidade de ligação na análise de texto, isso significa que todos os idiomas que é suportado pelo `entities` ponto final de ligações para o corpo da Wikipedia correspondente nesse idioma. Uma vez que o tamanho do conjunto de documentos de varia entre as linguagens, espera-se que a entidade de recolhimento da funcionalidade de ligação também podem variar.

## <a name="supported-types-for-named-entity-recognition"></a>Tipos suportados para o reconhecimento de entidades

| Type  | Subtipo | Exemplo |
|:-----------   |:------------- |:---------|
| Pessoa        | N/A\*         | "Jeff", "Bill Gates"     |
| Localização      | N/A\*         | "Redmond, Washington", "Paris"  |
| Organização  | N/A\*         | "Microsoft"   |
| Quantidade      | Number        | "6", "six"     | 
| Quantidade      | Percentagem    | "50%", "cinquenta por cento"| 
| Quantidade      | Ordinal       | "2nd", "second"     | 
| Quantidade      | NumberRange   | "4 a 8"     | 
| Quantidade      | Idade           | 90 dias antigos"", "30 anos de idade"    | 
| Quantidade      | Moeda      | "$10.99"     | 
| Quantidade      | Dimensão     | "milhas 10", "40 cm"     | 
| Quantidade      | Temperatura   | "32 graus"    |
| DateTime      | N/A\*         | "6 17:30, 4 de Fevereiro de 2012"      | 
| DateTime      | Date          | "2nd de Maio de 2017", "05/02/2017"   | 
| Data e Hora     | Hora          | "8 am", "8:00"  | 
| DateTime      | DateRange     | "2nd de Maio para 5 de Maio"    | 
| DateTime      | TimeRange     | "pm de 6 a 7 pm"     | 
| DateTime      | Duração      | "1 minuto e 45 segundos"   | 
| DateTime      | Definir           | "cada terça-feira"     | 
| DateTime      | TimeZone      |    | 
| do IdP           | N/A\*         | "<https://www.bing.com>"    |
| Email         | N/A\*         | "support@contoso.com" |

\* Consoante as entidades de entrada e extraídas, determinadas entidades podem omitir o `SubType`.



## <a name="preparation"></a>Preparação

Tem de ter documentos JSON neste formato: id, texto, idioma

Para idiomas atualmente suportados, consulte [esta lista](../text-analytics-supported-languages.md).

Tamanho do documento tem de ser em 5,120 carateres por documento, e pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido. O exemplo seguinte é uma ilustração de conteúdo que pode enviar para o final de ligação de entidade.

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
                },
               {"id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>Passo 1: O pedido de estrutura

Pode obter detalhes sobre a definição do pedido em [Como chamar a API de Análise de Texto](text-analytics-how-to-call-api.md). Os seguintes pontos são novamente apresentados para sua comodidade:

+ Crie um pedido **POST**. Reveja a documentação da API para este pedido: [API do Entity Linking](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ Defina o ponto de final HTTP para extração de entidades. Tem de incluir o recurso `/entities`: `https://[your-region].api.cognitive.microsoft.com/text/analytics/v2.1-preview/entities`

+ Defina um cabeçalho de pedido para incluir a chave de acesso para operações de Análise de Texto. Para obter mais informações, veja [Como localizar pontos finais e chaves de acesso](text-analytics-how-to-access-key.md).

+ No corpo do pedido, forneça a coleção de documentos JSON que preparou para esta análise.

> [!Tip]
> Utilize o [Postman](text-analytics-how-to-call-api.md) ou abra a **consola de teste da API** na [documentação](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) para estruturar um pedido e publicá-lo no serviço.

## <a name="step-2-post-the-request"></a>Passo 2: O pedido de POST

A análise é realizada aquando da receção do pedido. O serviço aceita até 100 pedidos por minuto. Cada pedido pode ter um máximo de 1 MB.

Lembre-se de que o serviço não tem estado. Não são armazenados dados na sua conta. Os resultados são devolvidos imediatamente na resposta.

## <a name="step-3-view-results"></a>Passo 3: Ver resultados

Todos os pedidos POST devolvem uma resposta formatada JSON com os IDs e as propriedades detetadas.

O resultado é devolvido imediatamente. Pode transmitir os resultados para uma aplicação que aceite JSON ou guardar o resultado num ficheiro no sistema local e, em seguida, importá-lo para uma aplicação que lhe permita ordenar, procurar e manipular os dados.

Um exemplo da saída para a entidade de ligação é mostrado a seguir:

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

Neste artigo, aprendeu conceitos e fluxo de trabalho para a ligação de entidades com a análise de texto nos serviços cognitivos. Em resumo:

+ [Entidades API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) está disponível para os idiomas selecionados.
+ Os documentos JSON no corpo do pedido incluem um id, texto e código de idioma.
+ O pedido POST refere-se a um ponto final `/entities` com recurso a uma [chave de acesso personalizada e um ponto final](text-analytics-how-to-access-key.md) válido para a sua subscrição.
+ Saída de resposta, que consiste em entidades associadas (incluindo confiança pontuações, deslocamentos e ligações web, para cada ID de documento) pode ser utilizada em qualquer aplicação

## <a name="see-also"></a>Consulte também 

 [Descrição Geral da Análise de Texto](../overview.md)  
 [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Página de produto da Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [API de Análise de Texto](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634)
