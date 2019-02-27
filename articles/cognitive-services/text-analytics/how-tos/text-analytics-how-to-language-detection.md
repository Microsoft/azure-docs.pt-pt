---
title: Detetar idioma com a API de REST de análise de texto | Documentos da Microsoft
description: Como detetar o idioma a utilizar a API de REST de análise de texto dos serviços cognitivos do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 8197d091763709282c42379a7ca0ea802e5c6fdf
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886796"
---
# <a name="example-how-to-detect-language-with-text-analytics"></a>Exemplo: Como detectar o idioma com análise de texto

A [API de Deteção de Idioma](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) avalia o texto de entrada e para cada documento e devolve os identificadores de idioma com uma pontuação que indica o nível de confiança da análise. A Análise de Texto reconhece até 120 idiomas.

Esta funcionalidade é útil para arquivos de conteúdo que recolhem texto arbitrário quando o idioma é desconhecido. Pode analisar os resultados desta análise para determinar o idioma que é utilizado no documento de entrada. A resposta também devolve uma pontuação que reflete a confiança do modelo (um valor entre 0 e 1).

> [!TIP]
> Análise de texto também fornece um Docker baseado em Linux imagem de contentor para a deteção de idioma, para que possa [instalar e executar o contentor de análise de texto](text-analytics-how-to-install-containers.md) perto dos seus dados.

## <a name="preparation"></a>Preparação

Tem de ter documentos JSON neste formato: id, texto.

Tamanho do documento tem de ser em 5,120 carateres por documento, e pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo do pedido. Segue-se um exemplo de conteúdos que poderá enviar para a deteção do idioma.

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

Pode obter detalhes sobre a definição do pedido em [Como chamar a API de Análise de Texto](text-analytics-how-to-call-api.md). Os seguintes pontos são novamente apresentados para sua comodidade:

+ Crie um pedido **POST**. Reveja a documentação da API para este pedido: [API de deteção de idioma](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)

+ Definir o ponto final HTTP para deteção de idioma, usando um recurso de análise de texto no Azure ou um instanciadas [contentor de análise de texto](text-analytics-how-to-install-containers.md). Tem de incluir o recurso `/languages`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages`

+ Defina um cabeçalho de pedido para incluir a chave de acesso para operações de Análise de Texto. Para obter mais informações, veja [Como localizar pontos finais e chaves de acesso](text-analytics-how-to-access-key.md).

+ No corpo do pedido, forneça a coleção de documentos JSON que preparou para esta análise.

> [!Tip]
> Utilize o [Postman](text-analytics-how-to-call-api.md) ou abra a **consola de teste da API** na [documentação](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) para estruturar um pedido e publicá-lo no serviço.

## <a name="step-2-post-the-request"></a>Passo 2: O pedido de POST

A análise é realizada aquando da receção do pedido. O serviço aceita até 100 pedidos por minuto. Cada pedido pode ter um máximo de 1 MB.

Lembre-se de que o serviço não tem estado. Não são armazenados dados na sua conta. Os resultados são devolvidos imediatamente na resposta.


## <a name="step-3-view-results"></a>Passo 3: Ver resultados

Todos os pedidos POST devolvem uma resposta formatada JSON com os IDs e as propriedades detetadas.

O resultado é devolvido imediatamente. Pode transmitir os resultados para uma aplicação que aceite JSON ou guardar o resultado num ficheiro no sistema local e, em seguida, importá-lo para uma aplicação que lhe permita ordenar, procurar e manipular os dados.

Os resultados do pedido de exemplo deverão assemelhar-se ao seguinte JSON. Tenha em atenção que se trata de um documento com múltiplos itens. Os resultados são apresentados em inglês. Os identificadores de idioma incluem um nome amigável e um código de idioma no formato [ISO 639-1](https://www.iso.org/standard/22109.html).

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

Se o analisador não conseguir analisar a entrada (por exemplo, imagine que enviou um bloco de texto com apenas números árabes), irá devolver `(Unknown)`.

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
### <a name="mixed-language-content"></a>Conteúdo de idioma misto

O conteúdo de idioma misto dentro do mesmo documento devolve o idioma com maior presença no conteúdo, mas com uma classificação positiva inferior, o que reflete o nível de confiança marginal dessa avaliação. No seguinte exemplo, a entrada é uma mistura de inglês, espanhol e francês. O analisador conta carateres em cada segmento para determinar o idioma predominante.

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

O resultado é composto pelo idioma predominante com uma pontuação inferior a 1,0 que indica um nível de confiança mais baixo.

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

Neste artigo, aprendeu conceitos e fluxos de trabalho relativos à deteção de idioma com recurso à Análise de Texto nos Serviços Cognitivos. Segue-se um lembrete rápido dos principais pontos anteriormente explicados e demonstrados:

+ A [API de Deteção de Idioma](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) está disponível para 120 idiomas.
+ Os documentos JSON no corpo do pedido incluem um id e texto.
+ O pedido POST refere-se a um ponto final `/languages` com recurso a uma [chave de acesso personalizada e um ponto final](text-analytics-how-to-access-key.md) válido para a sua subscrição.
+ O resultado da resposta, que consiste em identificadores de idioma para cada ID de documento, pode ser transmitido para qualquer aplicação que aceite JSON, incluindo, por exemplo, o Excel e o Power BI.

## <a name="see-also"></a>Consulte também 

 [Descrição Geral da Análise de Texto](../overview.md)  
 [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Página de produto da Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Analisar sentimentos](text-analytics-how-to-sentiment-analysis.md)
