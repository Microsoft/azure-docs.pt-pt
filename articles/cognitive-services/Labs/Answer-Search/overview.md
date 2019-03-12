---
title: O que é a Pesquisa de Respostas do Projeto?
titlesuffix: Azure Cognitive Services
description: Introdução à Pesquisa de Respostas do Projeto.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: overview
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: ac1717a8e8a08fcfedc3bc21bb0f03b3e3ca2511
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548086"
---
# <a name="what-is-project-answer-search"></a>O que é a Pesquisa de Respostas do Projeto?
A API de Pesquisa de Respostas do Projeto utiliza o ponto final do Bing v7 para obter respostas para consultas interrogativas. Uma pergunta como “Qual é a circunferência da terra?” devolve uma resposta com informações factuais.  Uma consulta de uma pessoa, local ou coisa devolve informações sobre a entidade identificada pela consulta. Estes cenários podem ser úteis em aplicações, como bots de conversação, aplicações de mensagens, leitores, etc.  

As consultas devolvem as respostas que dependem do cenário de consulta: são sempre devolvidas páginas Web, enquanto os [factos](fact-queries.md) e/ou as [entidades](entity-queries.md) só são devolvidos se forem relevantes.

## <a name="endpoint"></a>Ponto Final
Para obter respostas a uma pergunta ou informações sobre uma pessoa, local ou coisa, envie um pedido para o ponto final da API de Pesquisa de Respostas. Utilize os cabeçalhos e os parâmetros de URL para várias especificações.  Inclua o cabeçalho *Ocp-Apim-Subscription-Key* com um token válido.  O parâmetro de mercado é obrigatório. Apenas o mercado de `en-us` é atualmente suportada.

A seguinte consulta obtém respostas à pergunta: "O que é a circunferência da terra?"

GET:
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

```

O parâmetro de URL `q=` é necessário para especificar o objeto de pesquisa.

## <a name="response-object"></a>Objeto de resposta

A resposta inclui cabeçalhos HTTP, páginas Web, factos e/ou entidades.

```
BingAPIs-TraceId: AB2E75C998614ADB8EBF5110DF648298
X-MSEdge-ClientID: 1E48FC4F7B8768C80B14F7997A106906
BingAPIs-SessionId: 0504DDD6DAE84861A4842306F8DA7A58
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: AB2E75C998614ADB8EBF5110DF648298 Ref B: CO1EDGE0322 Ref C: 2018-04-19T19:57:13Z

JSON Response:

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "what is the circumference of earth"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q\u003dwhat+is+the+circumference+of+earth",
    "totalEstimatedMatches": 217000,
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.0",
        "name": "Circumference of the Earth - Universe Today",
        "url": "https://www.universetoday.com/26461/circumference-of-the-earth/",
        "isFamilyFriendly": true,
        "displayUrl": "https://www.universetoday.com/26461/circumference-of-the-earth",
        "snippet": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "deepLinks": [
          {
            "name": "About Earth",
            "url": "https://www.universetoday.com/14382/10-interesting-facts-about-planet-earth/"
          }
        ],
        "dateLastCrawled": "2018-04-12T14:13:00.0000000Z",
        "language": "en"
      },
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.1",
        "name": "Earth - Wikipedia",
        "url": "https://en.wikipedia.org/wiki/Earth",
        "about": [
          {
            "name": "Earth"
          },
          {
            "name": "Earth"
          }
        ],
        "isFamilyFriendly": true,
        "displayUrl": "https://en.wikipedia.org/wiki/Earth",
        "snippet": "Circumference: 40 075.017 km equatorial (24 901.461 mi) ... Earth is the third planet from the Sun and the only object in the Universe known to harbor life.",
        "deepLinks": [
          {
            "name": "Moon",
            "url": "https://en.wikipedia.org/wiki/Moon"
          },
          {
            "name": "Planet",
            "url": "https://en.wikipedia.org/wiki/Planet"
          },
          {
            "name": "Quasi-Satellites",
            "url": "https://en.wikipedia.org/wiki/Quasi-satellite"
          },
          {
            "name": "World Population",
            "url": "https://en.wikipedia.org/wiki/World_population"
          },
   . . .

    ]
  },
  "entities": {
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#Entities.0",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
              "name": "CC-BY-SA",
              "url": "https://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "https://en.wikipedia.org/wiki/Earth"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "https://en.wikipedia.org/wiki/Earth"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dEarth\u0026filters\u003dsid:%226ddb3372-4801-5567-321e-e8a53bd774a4%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Earth",
        "image": {
          "name": "Earth",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA3ab623665ab412f386c162bd29f0683a\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "https://en.wikipedia.org/wiki/Earth"
            }
          ],
          "hostPageUrl": "https://upload.wikimedia.org/wikipedia/commons/9/97/The_Earth_seen_from_Apollo_17.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 799,
          "sourceHeight": 800
        },
        "description": "Earth is the third planet from the Sun and the only object in the Universe known to harbor life. According to radiometric dating and other sources of evidence, Earth formed over 4.5 billion years ago. Earth\u0027s gravity interacts with other objects in space, especially the Sun and the Moon, Earth\u0027s only natural satellite. Earth revolves around the Sun in 365.26 days, a period known as an Earth year. During this time, Earth rotates about its axis about 366.26 times.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Generic"
          ]
        },
        "bingId": "6ddb3372-4801-5567-321e-e8a53bd774a4"
      }
    ]
  },
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.universetoday.com/26461/circumference-of-the-earth/",
        "url": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.universetoday.com/26461/circumference-of-the-earth/",
        "seeMoreUrl": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "value": [
      {
        "description": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "subjectName": ""
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "Facts",
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Facts"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.0"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.1"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 2,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.2"
          }
        },
        
        . . . 
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        }
      ]
    }
  }
}

```

## <a name="terms-of-use"></a>Termos de utilização
A Pesquisa de Respostas do Projeto e as Tendências de Vídeo do Projeto estão sujeitas aos [Requisitos de Utilização e Apresentação de Pesquisa do Bing](use-display-requirements.md).

O Utilizador ou terceiros em seu nome não poderão utilizar, reter, armazenar, colocar em cache, partilhar nem distribuir quaisquer dados da API de Pré-visualização de URL para fins de testes, desenvolvimento, formação, distribuição ou disponibilização de qualquer serviço ou funcionalidade que não seja da Microsoft. 

## <a name="throttling-requests"></a>Limitar pedidos

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>Atribuição de dados  

As respostas da Pesquisa de Respostas do Projeto contêm informações pertencentes a terceiros. É da sua responsável garantir a respetiva utilização adequada, por exemplo, ao respeitar o cumprimento de qualquer licença Creative Commons na qual a sua experiência de utilizador se baseie.  
  
Se uma resposta ou resultado incluir os campos `contractualRules`, `attributions` ou `provider`, tem de atribuir os dados. Se a resposta não incluir nenhum desses campos, a atribuição não é necessária. Se a resposta incluir o campo `contractualRules` e os campos `attributions` e/ou `provider`, tem de utilizar as regras contratuais para atribuir os dados.  
  
O exemplo seguinte mostra uma entidade que inclui uma regra contratual MediaAttribution e uma imagem que inclui um campo `provider`. A regra MediaAttribution identifica a imagem como o destino da regra, pelo que poderia ignorar o campo `provider` da imagem e utilizar, em alternativa, a regra MediaAttribution para fornecer a atribuição.  
  
```  
        "value" : [{
            "contractualRules" : [
                . . .
                {
                    "_type" : "ContractualRules\/MediaAttribution",
                    "targetPropertyName" : "image",
                    "mustBeCloseToContent" : true,
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }
            ],
            . . .
            "image" : {
                "name" : "Space Needle",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A46378861201...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }],
                "hostPageUrl" : "http:\/\/www.citydictionary.com\/Uploaded...",
                "width" : 110,
                "height" : 110
            },
            . . .
        }]
```  
  
Se uma regra contratual incluir o campo `targetPropertyName`, a regra aplica-se apenas ao campo visado. Caso contrário, a regra aplica-se ao objeto principal que contém o campo `contractualRules`.  
  
  
No exemplo seguinte, a regra `LinkAttribution` inclui o campo `targetPropertyName`, pelo que a regra aplica-se ao campo `description`. Para regras que se aplicam a campos específicos, tem de incluir uma linha imediatamente após os dados visados que contenha uma hiperligação para o site do fornecedor. Por exemplo, para atribuir a descrição, inclua uma linha imediatamente após o texto de descrição, que contenha uma hiperligação para os dados no site do fornecedor, neste caso, crie uma ligação para en.wikipedia.org.  
  
```  
"entities" : {  
    "value" : [{  
            . . .  
            "description" : "Peyton Williams Manning is a former American....",  
            . . .  
            "contractualRules" : [{  
                    "_type" : "ContractualRules\/LinkAttribution",  
                    "targetPropertyName" : "description",  
                    "mustBeCloseToContent" : true,  
                    "text" : "en.wikipedia.org",  
                    "url" : "http:\/\/www.bing.com\/cr?IG=B8AD73..."  
                 },  
            . . .  
  
```  

### <a name="license-attribution"></a>Atribuição de Licença  

Se a lista de regras contratuais incluir uma regra [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution), tem de apresentar o aviso na linha imediatamente após o conteúdo ao qual a licença se aplica. A regra `LicenseAttribution` utiliza o campo `targetPropertyName` para identificar a propriedade à qual a licença se aplica.  
  
O seguinte mostra um exemplo que inclui uma regra `LicenseAttribution`.  
  
![Atribuição de licença](./media/licenseattribution.png)  
  
O aviso de licença que apresentar tem de incluir uma hiperligação para o site que contenha informações sobre a licença. Normalmente, o nome da licença é utilizado como hiperligação. Por exemplo, se o aviso for **Texto ao abrigo da licença CC-BY-SA** e CC-BY-SA for o nome da licença, tornaria CC-BY-SA numa hiperligação.  
  
### <a name="link-and-text-attribution"></a>Atribuição de Ligação e Texto  

As regras [LinkAttribution](reference.md#linkattribution) e [TextAttribution](reference.md#textattribution) são normalmente utilizadas para identificar o fornecedor dos dados. O campo `targetPropertyName` identifica o campo ao qual a regra se aplica.  
  
Para atribuir os fornecedores, inclua uma linha imediatamente após o conteúdo ao qual as atribuições se aplicam (por exemplo, o ficheiro visado). A linha deve estar claramente identificada para indicar que os fornecedores são a origem dos dados. Por exemplo, “Dados de: en.wikipedia.org”. Para as regras `LinkAttribution`, tem de criar uma hiperligação para o site do fornecedor.  
  
O seguinte mostra um exemplo que inclui as regras `LinkAttribution` e `TextAttribution`.  
  
![Atribuição de texto de ligação](./media/linktextattribution.png)  

### <a name="media-attribution"></a>Atribuição de Suporte de Dados  

Se a entidade incluir uma imagem e a apresentar, tem de fornecer uma ligação clicável para o site do fornecedor. Se a entidade incluir uma regra [MediaAttribution](reference.md#mediaattribution), utilize o URL da regra para criar a ligação clicável. Caso contrário, utilize o URL incluído no campo `provider` da imagem para criar a ligação clicável.  
  
O seguinte mostra um exemplo que inclui um campo `provider` e as regras contratuais da imagem. Uma vez que o exemplo inclui a regra contratual, irá ignorar o campo `provider` da imagem e aplicar a regra `MediaAttribution`.  
  
![Atribuição de suporte de dados](./media/mediaattribution.png)  

## <a name="next-steps"></a>Passos Seguintes
- [Início rápido do C#](c-sharp-quickstart.md)
- [Início Rápido do Java](java-quickstart.md)
- [Início rápido de Node](node-quickstart.md)
- [Início Rápido do Python](python-quickstart.md)
