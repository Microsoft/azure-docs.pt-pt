---
title: Utilizar a API de Verificação de Ortografia do Bing
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre os modos de Verificação Ortográfica do Bing, configurações e outras informações relacionadas à API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: c5c9ad8be8bd4cd834b01a0c67e0bbc81b8cdd4a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881892"
---
# <a name="using-the-bing-spell-check-api"></a>Utilizar a API de Verificação de Ortografia do Bing

Use este artigo para saber mais sobre como usar a API de Verificação Ortográfica do Bing para executar a gramática contextual e a verificação ortográfica. Embora a maioria dos verificadores ortográficos dependam de conjuntos de regras baseados em dicionário, o verificador ortográfico do Bing aproveita o aprendizado de máquina e a tradução automática do computador para fornecer correções precisas e contextuais. 

## <a name="spell-check-modes"></a>Modos de verificação ortográfica

A API suporta dois modos de revisão de texto, `Proof` e `Spell`.  Experimente [estes](https://azure.microsoft.com/services/cognitive-services/spell-check/) exemplos.

### <a name="proof---for-documents"></a>Prova de documentos 

O modo predefinido é `Proof`. O modo de ortografia `Proof` oferece as verificações mais abrangentes, a adição de capitalização, pontuação básica e outras funcionalidades para ajudar na criação de documentos. no entanto, está disponível apenas nos mercados en-US (inglês dos Estados Unidos), es-ES (espanhol), pt-BR (português do Brasil) (Nota: apenas na versão beta para espanhol e português). Para todos os outros mercados, defina o parâmetro de consulta de modo para Ortografia. 

> [!NOTE]
> Se o comprimento do texto da consulta exceder 4096, ele será truncado para 4096 caracteres e, em seguida, ser processado. 

### <a name="spell----for-web-searchesqueries"></a>Ortografia-para pesquisas/consultas na Web

A `Spell` é mais agressiva para devolver resultados melhores de pesquisa. O modo `Spell` encontra a maioria dos erros de ortografia, mas não encontra alguns dos erros de gramática que o `Proof` encontra, por exemplo, a capitalização e as palavras repetidas.

> [!NOTE]
> * O comprimento máximo de consulta com suporte está abaixo. Se a consulta exceder o comprimento máximo, a consulta e seus resultados não serão alterados.
>    * 130 caracteres para os seguintes códigos de idioma: en, de, es, FR, pl, pt, VA, ru, NL, NB, TR-TR, ti, zh, Ko. 
>    * 65 caracteres para todos os outros.
> * O modo de ortografia não dá suporte a caracteres de`[` colchetes (e `]`) em consultas e pode causar resultados inconsistentes. É recomendável removê-los de suas consultas ao usar o modo de verificação ortográfica.

## <a name="market-setting"></a>Definição do mercado

Um [código de mercado](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#market-codes) deve ser especificado com `mkt` o parâmetro de consulta em sua solicitação. Caso contrário, a API usará um mercado padrão com base no endereço IP da solicitação.


## <a name="http-post-and-get-support"></a>Suporte HTTP POST e GET

A API suporta HTTP POST ou HTTP GET. O que utiliza depende do tamanho do texto que pretende verificar. Se as cadeias de carateres tiverem sempre menos de 1.500 carateres, utilizaria um GET. Mas se quiser suportar cadeias de carateres até 10.000 carateres, utilize o POST. A cadeia de texto pode incluir qualquer caráter válido UTF-8.

O exemplo seguinte mostra um pedido POST para verificar a ortografia e a gramática de uma cadeia de texto. O exemplo inclui o parâmetro de consulta de [modo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#mode) para estar completo (poderia ter sido deixado de fora desde `mode` predefinições para Verificação). O parâmetro de consulta de [texto](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) contém a cadeia a ser verificada.
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

Se utilizar o HTTP GET, iria incluir o parâmetro de consulta `text` na cadeia de consulta do URL
  
O código a seguir mostra a resposta ao pedido anterior. A resposta contém um objeto [SpellCheck](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#spellcheck). 
  
```json
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
O campo [flaggedTokens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#flaggedtokens) apresenta uma lista de erros ortográficos e gramaticais que a API encontrou na cadeia de [texto](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text). O campo `token` contém a palavra a ser substituída. Utilizaria o deslocamento baseado em zero no campo `offset` para encontrar o token na cadeia `text`. Teria de substituir a palavra naquela localização pela palavra no campo `suggestion`. 

Se o campo `type` é RepeatedToken, ainda deveria substituir o token por `suggestion`, mas provavelmente também teria de remover o espaço à direita.

## <a name="throttling-requests"></a>Limitar pedidos

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Passos seguintes

- [O que é a API de Verificação Ortográfica do Bing?](../overview.md)
- [Bing Spell Check API v7 Reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference) (Referência da API de Verificação de Ortografia do Bing v7)
