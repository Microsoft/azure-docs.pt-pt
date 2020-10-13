---
title: Chamar a API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Este artigo explica como pode chamar o Azure Cognitive Services Text Analytics API e o Carteiro.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: e17f2015ed4428cfd3c1a6c8a7bc4f92854a6b71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710605"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Como chamar a API de Sms Analytics REST

As chamadas para a **API de Análise de Texto** são chamadas HTTP POST/GET, que pode formular em qualquer idioma. Neste artigo, utilizamos REST e [Carteiro](https://www.postman.com/downloads/) para demonstrar conceitos-chave.

Cada pedido deve incluir a sua chave de acesso e um ponto final HTTP. O ponto final especifica a região que escolheu durante a inscrição, o URL de serviço e um recurso utilizado no pedido: `sentiment` `keyphrases` , , e `languages` `entities` . 

Lembre-se que o Text Analytics é apátrida para que não existam ativos de dados para gerir. O seu texto é carregado, analisado após a receção, e os resultados são devolvidos imediatamente ao pedido de chamada.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>Definição de esquema JSON

A entrada deve ser JSON em texto cru e não estruturado. XML não é suportado. O esquema é simples, consistindo dos elementos descritos na seguinte lista. 

Atualmente, pode submeter os mesmos documentos para todas as operações de Text Analytics: sentimento, frase-chave, deteção de idiomas e identificação de entidades. (O esquema é suscetível de variar para cada análise no futuro.)

| Elemento | Valores válidos | Necessário? | Utilização |
|---------|--------------|-----------|-------|
|`id` |O tipo de dados é string, mas na prática os IDs de documento tendem a ser inteiros. | Necessário | O sistema utiliza os IDs que fornece para estruturar a saída. Códigos linguísticos, frases-chave e pontuações de sentimento são gerados para cada ID no pedido.|
|`text` | Texto cru não estruturado, até 5.120 caracteres. | Necessário | Para a deteção de idiomas, o texto pode ser expresso em qualquer língua. Para análise de sentimentos, extração de frases-chave e identificação de entidades, o texto deve estar numa [língua suportada](../text-analytics-supported-languages.md). |
|`language` | Código [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) de 2 caracteres para uma [língua suportada](../text-analytics-supported-languages.md) | Varia | Necessário para a análise de sentimentos, extração de frases-chave e ligação de entidades; opcional para a deteção de linguagem. Não há erro se o excluirmos, mas a análise é enfraquecida sem ela. O código linguístico deve corresponder ao `text` que fornece. |

Para obter mais informações sobre limites, consulte [a visão geral > limites de dados](../overview.md#data-limits)de texto. 


```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    },
    {
      "language": "en",
      "id": "2",
      "text": "It's incredibly sunny outside! I'm so happy."
    },
    {
      "language": "en",
      "id": "3",
      "text": "Pike place market is my favorite Seattle attraction."
    }
  ]
}
```


## <a name="set-up-a-request-in-postman"></a>Configurar um pedido no Carteiro

O serviço aceita pedido até 1 MB de tamanho. Se estiver a utilizar o Carteiro (ou outra ferramenta de teste web API), configurar o ponto final para incluir o recurso que pretende utilizar e fornecer a chave de acesso num cabeçalho de pedido. Cada operação requer que apendam o recurso adequado ao ponto final. 

1. No Carteiro:

   + Escolha **o Post** como o tipo de pedido.
   + Cole no ponto final que copiou a partir da página do portal.
   + Anexar um recurso.

   Os pontos finais de recursos são os seguintes (a sua região pode variar):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v3.0/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v3.0/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v3.0/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v3.0/entities/recognition/general`

2. Desa estação os três cabeçalhos de pedido:

   + `Ocp-Apim-Subscription-Key`: a sua chave de acesso, obtida a partir do portal Azure.
   + `Content-Type`: aplicação/json.
   + `Accept`: aplicação/json.

   O seu pedido deve ser semelhante ao seguinte, assumindo um recurso **/keyPhrases.**

   ![Solicitar screenshot com ponto final e cabeçalhos](../media/postman-request-keyphrase-1.png)

4. Clique **em Corpo** e escolha **cru** para o formato.

   ![Solicitar screenshot com configurações do corpo](../media/postman-request-body-raw.png)

5. Cole em alguns documentos JSON num formato válido para a análise pretendida. Para obter mais informações sobre uma análise específica, consulte os tópicos abaixo:

  + [Deteção linguística](text-analytics-how-to-language-detection.md)  
  + [Extração de frase-chave](text-analytics-how-to-keyword-extraction.md)  
  + [Análise de sentimentos](text-analytics-how-to-sentiment-analysis.md)  
  + [Reconhecimento de entidades](text-analytics-how-to-entity-linking.md)  


6. Clique **em Enviar** para submeter o pedido. Consulte a secção [limite de dados](../overview.md#data-limits) na visão geral para obter informações sobre o número de pedidos que pode enviar por minuto e segundo.

   No Carteiro, a resposta é exibida na janela seguinte para baixo, como um único documento JSON, com um item para cada documento ID fornecido no pedido.

## <a name="see-also"></a>Consulte também 

 [Visão geral da análise de texto](../overview.md)  
 [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Detetar idioma](text-analytics-how-to-language-detection.md)
