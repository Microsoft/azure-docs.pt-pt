---
title: Chamar a API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Este artigo explica como pode chamar o Texto de Análise de Texto dos Serviços Cognitivos Azure REST API e Carteiro.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: afb576c265ccdd4a014ed678331f030a0442a197
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79219304"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Como chamar a API REST de Análise de Texto

As chamadas para a **API** de Análise de Texto são chamadas HTTP POST/GET, que pode formular em qualquer idioma. Neste artigo, usamos REST e [Carteiro](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) para demonstrar conceitos-chave.

Cada pedido deve incluir a sua chave de acesso e um ponto final HTTP. O ponto final especifica a região que escolheu durante a inscrição, `sentiment` `keyphrases`o `languages`URL `entities`de serviço e um recurso utilizado no pedido: , , e . 

Recorde-se que o Text Analytics é apátrida, pelo que não existem ativos de dados para gerir. O seu texto é enviado, analisado após a receção, e os resultados são devolvidos imediatamente ao pedido de chamada.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>Definição de esquema JSON

A entrada deve ser JSON em texto cru não estruturado. XML não é suportado. O esquema é simples, consistindo dos elementos descritos na lista seguinte. 

Atualmente pode submeter os mesmos documentos para todas as operações de Análise de Texto: sentimento, frase-chave, deteção de idiomas e identificação de entidades. (É provável que o esquema varie para cada análise no futuro.)

| Elemento | Valores válidos | Necessário? | Utilização |
|---------|--------------|-----------|-------|
|`id` |O tipo de dados é string, mas na prática os IDs de documentotendem a ser inteiros. | Necessário | O sistema usa as iDs que fornece para estruturar a saída. Códigos linguísticos, frases-chave e pontuações de sentimento são gerados para cada ID no pedido.|
|`text` | Texto cru não estruturado, até 5.120 caracteres. | Necessário | Para deteção de idiomas, o texto pode ser expresso em qualquer idioma. Para análise de sentimentos, extração de frases-chave e identificação da entidade, o texto deve estar numa [língua apoiada](../text-analytics-supported-languages.md). |
|`language` | Código [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) de 2 caracteres para uma [língua apoiada](../text-analytics-supported-languages.md) | Varia | Necessária para análise de sentimentos, extração de frases-chave e ligação de entidades; opcional para deteção de linguagem. Não há erro se o excluirmos, mas a análise é enfraquecida sem ela. O código linguístico `text` deve corresponder ao que fornece. |

Para obter mais informações sobre limites, consulte a [visão geral do Texto > limites de dados](../overview.md#data-limits). 

## <a name="set-up-a-request-in-postman"></a>Configurar um pedido no Carteiro

O serviço aceita pedido até 1 MB de tamanho. Se estiver a utilizar o Carteiro (ou outra ferramenta de teste Web API), crie o ponto final para incluir o recurso que pretende utilizar e forneça a chave de acesso num cabeçalho de pedido. Cada operação requer que acomode o recurso adequado ao ponto final. 

1. No Carteiro:

   + Escolha **o Post** como o tipo de pedido.
   + Pasta no ponto final que copiou da página do portal.
   + Anexar um recurso.

   Os pontos finais dos recursos são os seguintes (a sua região pode variar):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`

2. Detete os três cabeçalhos de pedido:

   + `Ocp-Apim-Subscription-Key`: a sua chave de acesso, obtida a partir do portal Azure.
   + `Content-Type`: aplicação/json.
   + `Accept`: aplicação/json.

   O seu pedido deve ser semelhante ao seguinte screenshot, assumindo um recurso **/keyPhrases.**

   ![Solicitar screenshot com ponto final e cabeçalhos](../media/postman-request-keyphrase-1.png)

4. Clique em **Body** e escolha **cru** para o formato.

   ![Solicitar screenshot com configurações do corpo](../media/postman-request-body-raw.png)

5. Colá-lo em alguns documentos JSON num formato válido para a análise pretendida. Para obter mais informações sobre uma determinada análise, consulte os tópicos abaixo:

  + [Deteção de linguagem](text-analytics-how-to-language-detection.md)  
  + [Extração de frase-chave](text-analytics-how-to-keyword-extraction.md)  
  + [Análise de sentimentos](text-analytics-how-to-sentiment-analysis.md)  
  + [Reconhecimento de entidades](text-analytics-how-to-entity-linking.md)  


6. Clique **em Enviar** para submeter o pedido. Consulte a secção de [limites](../overview.md#data-limits) de dados na visão geral para obter informações sobre o número de pedidos que pode enviar por minuto e segundo.

   No Carteiro, a resposta é exibida na janela seguinte para baixo, como um único documento JSON, com um item para cada id de documento fornecido no pedido.

## <a name="see-also"></a>Consulte também 

 [Visão geral da análise de texto](../overview.md)  
 [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Detetar idioma](text-analytics-how-to-language-detection.md)
