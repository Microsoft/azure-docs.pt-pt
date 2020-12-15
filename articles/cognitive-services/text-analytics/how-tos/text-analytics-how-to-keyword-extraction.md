---
title: Extração de frases-chave usando a API de Repouso DE Texto
titleSuffix: Azure Cognitive Services
description: Como extrair frases-chave utilizando o Text Analytics REST API dos Serviços Cognitivos Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 39823792a438e533134f38c04e72f2c314c57678
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505193"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Exemplo: Como extrair frases-chave usando a Análise de Texto

A [API de Extração de Expressões-Chave](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) avalia o texto não estruturado e, para cada documento JSON, devolve uma lista de expressões-chave.

Esta funcionalidade é útil se precisar de identificar rapidamente os pontos principais numa coleção de documentos. Por exemplo, para o texto de entrada "The food was delicious and there were wonderful staff", o serviço devolve os pontos de conversa principais: "food" e "wonderful staff".

Para obter mais informações, veja os [Idiomas suportados](../language-support.md).

> [!TIP]
> * O Text Analytics também fornece uma imagem de recipiente Docker baseada em Linux para a extração de frases-chave, para que possa [instalar e executar o recipiente Text Analytics](text-analytics-how-to-install-containers.md) perto dos seus dados.
> * Também pode utilizar esta funcionalidade [com assincronizadousando](text-analytics-how-to-call-api.md) o `/analyze` ponto final.

## <a name="preparation"></a>Preparação

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

A extração de frases-chave funciona melhor quando lhe dá grandes quantidades de texto para trabalhar. Isto é oposto à análise do sentimento, que tem um melhor desempenho em quantidades menores de texto. Para obter os melhores resultados em ambas as operações, considere reestruturar as entradas em conformidade.

Você deve ter documentos JSON neste formato: ID, texto, idioma

O tamanho do documento deve ser de 5.120 caracteres ou menos por documento, e pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido. O seguinte exemplo é uma ilustração de conteúdos que pode enviar para extração de expressões-chave.

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

Para obter informações sobre a definição de pedido, consulte [Como ligar para a API de Análise de Texto](text-analytics-how-to-call-api.md). Os seguintes pontos são novamente apresentados para sua comodidade:

+ Crie um pedido **POST**. Reveja a documentação da API para este pedido: [Frases-chave API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases).

+ Deite o ponto final HTTP para a extração de frases-chave utilizando um recurso text analytics no Azure ou um recipiente de [análise de texto](text-analytics-how-to-install-containers.md)instantâneo . Deve incluir `/text/analytics/v3.0/keyPhrases` na URL. Por exemplo: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`.

+ Desa estade um cabeçalho de pedido para incluir a [chave de acesso](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) para operações de Análise de Texto.

+ No corpo do pedido, forneça a coleção de documentos JSON que preparou para esta análise.

> [!Tip]
> Utilize o [Postman](text-analytics-how-to-call-api.md) ou abra a **consola de teste da API** na [documentação](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) para estruturar um pedido e publicá-lo no serviço.

## <a name="step-2-post-the-request"></a>Passo 2: publicar o pedido

A análise é realizada aquando da receção do pedido. Para obter informações sobre o tamanho e o número de pedidos que pode enviar por minuto ou por segundo, consulte a secção [limite de dados](../overview.md#data-limits) na visão geral.

Lembre-se de que o serviço não tem estado. Não são armazenados dados na sua conta. Os resultados são devolvidos imediatamente na resposta.

## <a name="step-3-view-results"></a>Passo 3: ver resultados

Todos os pedidos POST devolvem uma resposta formatada JSON com os IDs e as propriedades detetadas. A ordem das frases-chave devolvidas é determinada internamente, pelo modelo.

O resultado é devolvido imediatamente. Pode transmitir os resultados para uma aplicação que aceite JSON ou guardar o resultado num ficheiro no sistema local e, em seguida, importá-lo para uma aplicação que lhe permita ordenar, procurar e manipular os dados.

É aqui apresentado um exemplo da saída para a extração da frase-chave do ponto final v3.1-preview.2:

```json
    {
       "documents":[
          {
             "id":"1",
             "keyPhrases":[
                "year",
                "trail",
                "trip",
                "views",
                "hike"
             ],
             "warnings":[]
          },
          {
             "id":"2",
             "keyPhrases":[
                "marked trails",
                "Worst hike",
                "goners"
             ],
             "warnings":[]
          },
          {
             "id":"3",
             "keyPhrases":[
                "trail",
                "small children",
                "family"
             ],
             "warnings":[]
          },
          {
             "id":"4",
             "keyPhrases":[
                "spectacular views",
                "trail",
                "Worth",
                "area"
             ],
             "warnings":[]
          },
          {
             "id":"5",
             "keyPhrases":[
                "places",
                "beautiful views",
                "favorite trail",
                "rest"
             ],
             "warnings":[]
          }
       ],
       "errors":[],
       "modelVersion":"2020-07-01"
    }

```
Como notado, o analisador encontra e descarta palavras não essenciais, e mantém termos ou frases simples que parecem ser o sujeito ou objeto de uma frase.

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para a extração de frases-chave utilizando o Text Analytics em Serviços Cognitivos. Em resumo:

+ A [API de extração de expressões-chave](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) está disponível para alguns idiomas.
+ Os documentos JSON no órgão de pedido incluem um ID, texto e código linguístico.
+ O pedido POST refere-se a um ponto final `/keyphrases` com recurso a uma [chave de acesso personalizada e um ponto final](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) válido para a sua subscrição.
+ A saída de resposta, que consiste em palavras-chave e frases para cada ID do documento, pode ser transmitida a qualquer aplicação que aceite o JSON, incluindo o Microsoft Office Excel e o Power BI, para citar alguns.

## <a name="see-also"></a>Veja também

 [Visão geral do Text Analytics](../overview.md) Perguntas [frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Página de produto da Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral da Análise de Texto](../overview.md)
* [Utilização da biblioteca de clientes Text Analytics](../quickstarts/client-libraries-rest-api.md)
* [Novidades](../whats-new.md)
