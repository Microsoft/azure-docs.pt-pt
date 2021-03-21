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
ms.date: 12/17/2020
ms.author: aahi
ms.openlocfilehash: 91e10c25d2c3bef9c1ca20e3e5737a326d45997c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654783"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Exemplo: Como extrair frases-chave usando a Análise de Texto

A [API de Extração de Expressões-Chave](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) avalia o texto não estruturado e, para cada documento JSON, devolve uma lista de expressões-chave.

Esta funcionalidade é útil se precisar de identificar rapidamente os pontos principais numa coleção de documentos. Por exemplo, para o texto de entrada "The food was delicious and there were wonderful staff", o serviço devolve os pontos de conversa principais: "food" e "wonderful staff".

Para obter mais informações, veja os [Idiomas suportados](../language-support.md).

> [!TIP]
> * O Text Analytics também fornece uma imagem de recipiente Docker baseada em Linux para a extração de frases-chave, para que possa [instalar e executar o recipiente Text Analytics](text-analytics-how-to-install-containers.md) perto dos seus dados.
> * Também pode utilizar esta funcionalidade [com assincronizadousando](text-analytics-how-to-call-api.md) o `/analyze` ponto final.

## <a name="preparation"></a>Preparação

A extração de frases-chave funciona melhor quando lhe dá grandes quantidades de texto para trabalhar. Isto é oposto à análise do sentimento, que tem um melhor desempenho em quantidades menores de texto. Para obter os melhores resultados em ambas as operações, considere reestruturar as entradas em conformidade.

Você deve ter documentos JSON neste formato: ID, texto, idioma

O tamanho do documento deve ser de 5.120 caracteres ou menos por documento, e pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido. O seguinte exemplo é uma ilustração de conteúdos que pode enviar para extração de expressões-chave. 

Consulte [como ligar para a API text Analytics](text-analytics-how-to-call-api.md) para obter mais informações sobre objetos de pedido e resposta.  

### <a name="example-synchronous-request-object"></a>Exemplo objeto de pedido sincronizado


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

### <a name="example-asynchronous-request-object"></a>Exemplo objeto de pedido assíncronos

A partir de `v3.1-preview.3` , pode enviar pedidos NER assíncronos utilizando o `/analyze` ponto final.


```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
    }
}
```

## <a name="step-1-structure-the-request"></a>Passo 1: estruturar o pedido

Para obter informações sobre a definição de pedido, consulte [Como ligar para a API de Análise de Texto](text-analytics-how-to-call-api.md). Os seguintes pontos são novamente apresentados para sua comodidade:

+ Crie um pedido **POST**. Reveja a documentação da API para este pedido: [Frases-chave API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases).

+ Deite o ponto final HTTP para a extração de frases-chave utilizando um recurso text analytics no Azure ou um recipiente de [análise de texto](text-analytics-how-to-install-containers.md)instantâneo . se estiver a utilizar a API de forma sincronizada, deve incluir `/text/analytics/v3.0/keyPhrases` no URL. Por exemplo: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`.

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

### <a name="synchronous-result"></a>Resultado sincronizado

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

### <a name="asynchronous-result"></a>Resultado assíncronos

Se utilizar o `/analyze` ponto final para uma operação assíncrono, obterá uma resposta contendo as tarefas que enviou para a API.

```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```


## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para a extração de frases-chave utilizando o Text Analytics em Serviços Cognitivos. Em resumo:

+ A [API de extração de expressões-chave](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) está disponível para alguns idiomas.
+ Os documentos JSON no órgão de pedido incluem um ID, texto e código linguístico.
+ O pedido de CORREIO é para um `/keyphrases` ou `/analyze` ponto final, usando uma chave de acesso personalizada [e um ponto final](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) que é válido para a sua subscrição.
+ A saída de resposta, que consiste em palavras-chave e frases para cada ID do documento, pode ser transmitida a qualquer aplicação que aceite o JSON, incluindo o Microsoft Office Excel e o Power BI, para citar alguns.

## <a name="see-also"></a>Ver também

 [Visão geral do Text Analytics](../overview.md) Perguntas [frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Página de produto da Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral da Análise de Texto](../overview.md)
* [Utilização da biblioteca de clientes Text Analytics](../quickstarts/client-libraries-rest-api.md)
* [Novidades](../whats-new.md)
