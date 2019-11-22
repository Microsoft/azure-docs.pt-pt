---
title: Chamar a API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Este artigo explica como você pode chamar os serviços cognitivas do Azure Análise de Texto a API REST e o postmaster.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: afb576c265ccdd4a014ed678331f030a0442a197
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286611"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Como chamar a API REST do Análise de Texto

Chamadas para as **API de análise de texto** são chamadas http post/Get, que você pode formular em qualquer idioma. Neste artigo, usamos REST e [postmaster](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) para demonstrar os principais conceitos.

Cada solicitação deve incluir sua chave de acesso e um ponto de extremidade HTTP. O ponto de extremidade especifica a região que você escolheu durante a inscrição, a URL do serviço e um recurso usado na solicitação: `sentiment`, `keyphrases`, `languages`e `entities`. 

Lembre-se de que Análise de Texto é sem estado, portanto, não há ativos de dados a serem gerenciados. O texto é carregado, analisado após o recebimento, e os resultados são retornados imediatamente para o aplicativo de chamada.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>Definição de esquema JSON

A entrada deve ser JSON em texto não estruturado bruto. Não há suporte para XML. O esquema é simples, consistindo nos elementos descritos na lista a seguir. 

Atualmente, você pode enviar os mesmos documentos para todas as operações de Análise de Texto: opiniões, frases-chave, detecção de idioma e identificação de entidade. (É provável que o esquema varie para cada análise no futuro.)

| Elemento | Valores válidos | Necessário? | Utilização |
|---------|--------------|-----------|-------|
|`id` |O tipo de dados é String, mas, nas práticas, as IDs de documento tendem a ser inteiros. | Necessário | O sistema usa as IDs que você fornece para estruturar a saída. Códigos de idioma, frases-chave e pontuações de opiniões são gerados para cada ID na solicitação.|
|`text` | Texto bruto não estruturado, até 5.120 caracteres. | Necessário | Para detecção de idioma, o texto pode ser expresso em qualquer idioma. Para análise de sentimentos, extração de frases-chave e identificação de entidade, o texto deve estar em um [idioma com suporte](../text-analytics-supported-languages.md). |
|`language` | código [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) de 2 caracteres para um [idioma com suporte](../text-analytics-supported-languages.md) | Varia | Necessário para análise de sentimentos, extração de frases-chave e vinculação de entidade; opcional para detecção de idioma. Não há erro se você excluí-lo, mas a análise é diminuída sem ele. O código de idioma deve corresponder à `text` que você fornece. |

Para obter mais informações sobre limites, consulte [análise de texto visão geral > limites de dados](../overview.md#data-limits). 

## <a name="set-up-a-request-in-postman"></a>Configurar uma solicitação no postmaster

O serviço aceita a solicitação de até 1 MB de tamanho. Se você estiver usando o postmaster (ou outra ferramenta de teste da API Web), configure o ponto de extremidade para incluir o recurso que você deseja usar e forneça a chave de acesso em um cabeçalho de solicitação. Cada operação requer que você acrescente o recurso apropriado ao ponto de extremidade. 

1. No postmaster:

   + Escolha **post** como o tipo de solicitação.
   + Cole no ponto de extremidade que você copiou da página do Portal.
   + Acrescentar um recurso.

   Os pontos de extremidade de recurso são os seguintes (sua região pode variar):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`

2. Defina os três cabeçalhos de solicitação:

   + `Ocp-Apim-Subscription-Key`: sua chave de acesso, obtida do portal do Azure.
   + `Content-Type`: Application/JSON.
   + `Accept`: Application/JSON.

   Sua solicitação deve ser semelhante à captura de tela a seguir, supondo um recurso **/keyPhrases** .

   ![Solicitar captura de tela com o ponto de extremidade e os cabeçalhos](../media/postman-request-keyphrase-1.png)

4. Clique em **corpo** e escolha **RAW** para o formato.

   ![Solicitar captura de tela com configurações de corpo](../media/postman-request-body-raw.png)

5. Cole alguns documentos JSON em um formato válido para a análise pretendida. Para obter mais informações sobre uma determinada análise, consulte os tópicos abaixo:

  + [Detecção de idioma](text-analytics-how-to-language-detection.md)  
  + [Extração de frases-chave](text-analytics-how-to-keyword-extraction.md)  
  + [Análise de sentimentos](text-analytics-how-to-sentiment-analysis.md)  
  + [Reconhecimento de entidade](text-analytics-how-to-entity-linking.md)  


6. Clique em **Enviar** para enviar a solicitação. Consulte a seção [limites de dados](../overview.md#data-limits) na visão geral para obter informações sobre o número de solicitações que você pode enviar por minuto e segundo.

   No postmaster, a resposta é exibida na próxima janela abaixo, como um único documento JSON, com um item para cada ID de documento fornecida na solicitação.

## <a name="see-also"></a>Consulte também 

 [Text Analytics Overview](../overview.md) (Descrição Geral da Análise de Texto)  
 [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Detectar idioma](text-analytics-how-to-language-detection.md)
