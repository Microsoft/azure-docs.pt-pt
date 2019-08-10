---
title: API de Tradução de Texto método BreakSentence
titleSuffix: Azure Cognitive Services
description: Use o método API de Tradução de Texto BreakSentence.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 184677589b3aa777ec556215455f8018e0d71f3f
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934043"
---
# <a name="translator-text-api-30-breaksentence"></a>API de Tradução de Texto 3,0: BreakSentence

Identifica o posicionamento de limites de sentença em um pedaço de texto.

## <a name="request-url"></a>URL do Pedido

Enviar uma `POST` solicitação para:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros do pedido

Os parâmetros de solicitação passados na cadeia de caracteres de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>versão de API</td>
    <td>*Parâmetro de consulta necessário*.<br/>Versão da API solicitada pelo cliente. O valor deve `3.0`ser.</td>
  </tr>
  <tr>
    <td>language</td>
    <td>*Parâmetro de consulta opcional*.<br/>Marca de idioma que identifica o idioma do texto de entrada. Se um código não for especificado, a detecção automática de idioma será aplicada.</td>
  </tr>
  <tr>
    <td>script</td>
    <td>*Parâmetro de consulta opcional*.<br/>Marca de script que identifica o script usado pelo texto de entrada. Se um script não for especificado, o script padrão do idioma será assumido.</td>
  </tr>
</table> 

Os cabeçalhos de solicitação incluem:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>Cabeçalho (s) de autenticação</td>
    <td><em>Cabeçalho de solicitação necessário</em>.<br/>Consulte <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">as opções disponíveis para autenticação</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Cabeçalho de solicitação necessário*.<br/>Especifica o tipo de conteúdo da carga. Os valores possíveis são `application/json`:.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Cabeçalho de solicitação necessário*.<br/>O comprimento do corpo da solicitação.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Opcional*.<br/>Um GUID gerado pelo cliente para identificar exclusivamente a solicitação. Observe que você pode omitir esse cabeçalho se incluir a ID de rastreamento na cadeia de caracteres de consulta usando um `ClientTraceId`parâmetro de consulta denominado.</td>
  </tr>
</table> 

## <a name="request-body"></a>Corpo do pedido

O corpo da solicitação é uma matriz JSON. Cada elemento da matriz é um objeto JSON com uma propriedade de `Text`cadeia de caracteres chamada. Os limites de frase são calculados para o valor `Text` da propriedade. Um corpo de solicitação de exemplo com uma parte do texto tem a seguinte aparência:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

As seguintes limitações se aplicam:

* A matriz pode ter no máximo 100 elementos.
* O valor de texto de um elemento de matriz não pode exceder 10.000 caracteres, incluindo espaços.
* O texto inteiro incluído na solicitação não pode exceder 50.000 caracteres, incluindo espaços.
* Se o `language` parâmetro de consulta for especificado, todos os elementos de matriz deverão estar no mesmo idioma. Caso contrário, a detecção automática de idioma será aplicada a cada elemento de matriz de forma independente.

## <a name="response-body"></a>Corpo da resposta

Uma resposta bem-sucedida é uma matriz JSON com um resultado para cada cadeia de caracteres na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `sentLen`: Uma matriz de inteiros que representa os comprimentos das frases no elemento de texto. O comprimento da matriz é o número de sentenças e os valores são o comprimento de cada frase. 

  * `detectedLanguage`: Um objeto que descreve a linguagem detectada por meio das seguintes propriedades:

     * `language`: Código do idioma detectado.

     * `score`: Um valor float que indica a confiança no resultado. A pontuação é entre zero e uma e uma pontuação baixa indica uma baixa confiança.
     
    Observe que a `detectedLanguage` Propriedade está presente apenas no objeto de resultado quando a detecção automática de idioma é solicitada.

Um exemplo de resposta JSON é:

```json
[
  {
    "sentenceLengths": [ 13, 11, 22 ]
    "detectedLanguage": {
      "language": "en",
      "score": 401
    },
  }
]
```

## <a name="response-headers"></a>Cabeçalhos de resposta

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>X-RequestId</td>
    <td>Valor gerado pelo serviço para identificar a solicitação. Ele é usado para fins de solução de problemas.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Códigos de status de resposta

A seguir estão os códigos de status HTTP possíveis que uma solicitação retorna. 

<table width="100%">
  <th width="20%">Código de Estado</th>
  <th>Descrição</th>
  <tr>
    <td>200</td>
    <td>Êxito.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Um dos parâmetros de consulta está ausente ou não é válido. Corrija os parâmetros de solicitação antes de tentar novamente.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Não foi possível autenticar o pedido. Verifique se as credenciais estão especificadas e válidas.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>O pedido não está autorizado. Verifique a mensagem de erro detalhes. Isso geralmente indica que todas as traduções gratuitas fornecidas com uma assinatura de avaliação foram usadas.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>O servidor rejeitou a solicitação porque o cliente excedeu os limites de solicitação.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Ocorreu um erro inesperado. Se o erro persistir, denuncie-o com: data e hora da falha, identificador de solicitação do cabeçalho `X-RequestId`de resposta e identificador de cliente do `X-ClientTraceId`cabeçalho da solicitação.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servidor temporariamente indisponível. Repita a solicitação. Se o erro persistir, denuncie-o com: data e hora da falha, identificador de solicitação do cabeçalho `X-RequestId`de resposta e identificador de cliente do `X-ClientTraceId`cabeçalho da solicitação.</td>
  </tr>
</table> 

Se ocorrer um erro, a solicitação também retornará uma resposta de erro JSON. O código de erro é um número de 6 dígitos que combina o código de status HTTP de 3 dígitos seguido por um número de 3 dígitos para categorizar ainda mais o erro. Códigos de erro comuns podem ser encontrados na [página de referência do API de tradução de texto v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exemplos

O exemplo a seguir mostra como obter limites de frase para uma única frase. O idioma da sentença é detectado automaticamente pelo serviço.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

