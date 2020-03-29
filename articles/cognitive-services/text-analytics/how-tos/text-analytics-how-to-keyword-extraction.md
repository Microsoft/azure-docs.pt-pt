---
title: Extração de frase-chave utilizando a API REST DE TEXTO Analytics
titleSuffix: Azure Cognitive Services
description: Como extrair frases-chave utilizando a API REST REST text Analytics dos Serviços Cognitivos Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/29/2019
ms.author: raymondl
ms.openlocfilehash: ec5ff756d7e732430675676868bc754627a2a4a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72429020"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Exemplo: Como extrair frases-chave usando o Text Analytics

A [API de Extração de Expressões-Chave](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) avalia o texto não estruturado e, para cada documento JSON, devolve uma lista de expressões-chave.

Esta funcionalidade é útil se precisar de identificar rapidamente os pontos principais numa coleção de documentos. Por exemplo, para o texto de entrada "The food was delicious and there were wonderful staff", o serviço devolve os pontos de conversa principais: "food" e "wonderful staff".

Para obter mais informações, veja [Idiomas suportados](../text-analytics-supported-languages.md).

> [!TIP]
> O Text Analytics também fornece uma imagem de recipiente Docker baseada em Linux para a extração de frases-chave, para que possa [instalar e executar o recipiente Text Analytics](text-analytics-how-to-install-containers.md) perto dos seus dados.

## <a name="preparation"></a>Preparação

A extração de frases-chave funciona melhor quando lhe dás maiores quantidades de texto para trabalhar. Isto é oposto à análise de sentimentos, que tem um melhor desempenho em quantidades menores de texto. Para obter os melhores resultados com as duas operações, pondere reestruturar as entradas em conformidade.

Você deve ter documentos JSON neste formato: ID, texto, idioma

O tamanho do documento deve ser de 5.120 ou menos caracteres por documento, e pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido. O seguinte exemplo é uma ilustração de conteúdos que pode enviar para extração de expressões-chave.

```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Passo 1: estruturar o pedido

Para obter informações sobre a definição de pedido, consulte [Como ligar para a API](text-analytics-how-to-call-api.md)de Análise de Texto . Os seguintes pontos são novamente apresentados para sua comodidade:

+ Crie um pedido **POST**. Reveja a documentação da API para este pedido: [Frases-chave API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6).

+ Detete o ponto final http para a extração de frases-chave utilizando um recurso De SMAnalytics no Azure ou um recipiente instantâneo de Análise de [Texto](text-analytics-how-to-install-containers.md). Deve incluir `/text/analytics/v2.1/keyPhrases` na URL. Por exemplo: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v2.1/keyPhrases`.

+ Detete um cabeçalho de pedido para incluir a [chave de acesso](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) para operações de Análise de Texto.

+ No corpo do pedido, forneça a coleção de documentos JSON que preparou para esta análise.

> [!Tip]
> Utilize o [Postman](text-analytics-how-to-call-api.md) ou abra a **consola de teste da API** na [documentação](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) para estruturar um pedido e publicá-lo no serviço.

## <a name="step-2-post-the-request"></a>Passo 2: publicar o pedido

A análise é realizada aquando da receção do pedido. Para obter informações sobre o tamanho e número de pedidos que pode enviar por minuto ou por segundo, consulte a secção de [limites](../overview.md#data-limits) de dados na visão geral .

Lembre-se de que o serviço não tem estado. Não são armazenados dados na sua conta. Os resultados são devolvidos imediatamente na resposta.

## <a name="step-3-view-results"></a>Passo 3: ver resultados

Todos os pedidos POST devolvem uma resposta formatada JSON com os IDs e as propriedades detetadas. A ordem das frases-chave devolvidas é determinada internamente pelo modelo.

O resultado é devolvido imediatamente. Pode transmitir os resultados para uma aplicação que aceite JSON ou guardar o resultado num ficheiro no sistema local e, em seguida, importá-lo para uma aplicação que lhe permita ordenar, procurar e manipular os dados.

Um exemplo da saída para a extração de frases-chave é mostrado aqui:

```json
    {
        "documents": [
            {
                "keyPhrases": [
                    "year",
                    "trail",
                    "trip",
                    "views"
                ],
                "id": "1"
            },
            {
                "keyPhrases": [
                    "marked trails",
                    "Worst hike",
                    "goners"
                ],
                "id": "2"
            },
            {
                "keyPhrases": [
                    "trail",
                    "small children",
                    "family"
                ],
                "id": "3"
            },
            {
                "keyPhrases": [
                    "spectacular views",
                    "trail",
                    "area"
                ],
                "id": "4"
            },
            {
                "keyPhrases": [
                    "places",
                    "beautiful views",
                    "favorite trail"
                ],
                "id": "5"
            }
        ],
        "errors": []
    }
```

Como notado, o analisador encontra e descarta palavras não essenciais, e mantém termos ou frases individuais que parecem ser o sujeito ou objeto de uma frase.

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para a extração de frases-chave utilizando o Text Analytics em Serviços Cognitivos. Em resumo:

+ A [API de extração de expressões-chave](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) está disponível para alguns idiomas.
+ Os documentos da JSON no organismo de pedido incluem um código de identificação, texto e idioma.
+ O pedido POST refere-se a um ponto final `/keyphrases` com recurso a uma [chave de acesso personalizada e um ponto final](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) válido para a sua subscrição.
+ A saída de resposta, que consiste em palavras-chave e frases para cada ID de documento, pode ser transmitida para qualquer aplicação que aceite jSON, incluindo Microsoft Office Excel e Power BI, para citar alguns.

## <a name="see-also"></a>Consulte também

 [Visão geral do Text Analytics](../overview.md) [frequentemente feita sperguntas (FAQ)](../text-analytics-resource-faq.md)</br>
 [Página de produto da Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [API de Análise de Texto](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)
