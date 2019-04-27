---
title: API de texto do Translator detetar método
titlesuffix: Azure Cognitive Services
description: Use o método detetar de API de texto do Translator.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: fa618c5c623a631e7a88f8235a0b7b16fcb2bc88
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605251"
---
# <a name="translator-text-api-30-detect"></a>Texto do Translator API 3.0: Detect

Identifica o idioma de um pedaço de texto.

## <a name="request-url"></a>URL do Pedido

Enviar um `POST` pedido para:

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros do pedido

Parâmetros de pedido passados na seqüência de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>versão de API</td>
    <td>*Parâmetro necessário*.<br/>Versão da API do pedido pelo cliente. Valor tem de ser `3.0`.</td>
  </tr>
</table> 

Cabeçalhos de pedido incluem:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>Excederam de autenticação</td>
    <td><em>Cabeçalho do pedido necessário</em>.<br/>Ver <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">as opções disponíveis para autenticação</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Cabeçalho do pedido necessário*.<br/>Especifica o tipo de conteúdo da carga. Os valores possíveis são: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Cabeçalho do pedido necessário*.<br/>O comprimento do corpo do pedido.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Opcional*.<br/>Um GUID gerado pelo cliente para identificar exclusivamente o pedido. Tenha em atenção que pode omitir este cabeçalho se incluir o ID de rastreio na cadeia de consulta com um parâmetro de consulta com o nome `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Corpo do pedido

O corpo do pedido é uma matriz JSON. Cada elemento da matriz é um objeto JSON com uma propriedade de cadeia de caracteres chamada `Text`. Deteção de idioma é aplicada ao valor da `Text` propriedade. Um corpo de pedido de exemplo fica assim:

```json
[
    { "Text": "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal." }
]
```

As seguintes limitações aplicam-se:

* A matriz pode ter no máximo de 100 elementos.
* O valor de texto de um elemento de matriz não pode ter mais de 10.000 carateres, incluindo espaços.
* Todo o texto incluído no pedido não pode ter mais de 50.000 carateres, incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta com êxito é uma matriz JSON com um resultado para cada cadeia de caracteres na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `language`: Código do idioma detetado.

  * `score`: Um valor de vírgula flutuante que indica a confiança no resultado. A classificação é entre zero e outro e uma pontuação baixa indica uma confiança de baixa.

  * `isTranslationSupported`: Um valor booleano que é verdadeiro se o idioma detetado é um dos idiomas suportados para a tradução de texto.

  * `isTransliterationSupported`: Um valor booleano que é verdadeiro se o idioma detetado é um dos idiomas suportados para a Transliteração.
  
  * `alternatives`: Uma matriz de outras linguagens possíveis. Cada elemento da matriz é outro objeto com as mesmas propriedades listadas acima: `language`, `score`, `isTranslationSupported` e `isTransliterationSupported`.

Um resposta JSON de exemplo é:

```json
[
  {
    "language": "de",
    "score": 0.92,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "sk",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="response-headers"></a>Cabeçalhos de resposta

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>X-RequestId</td>
    <td>Valor gerado pelo serviço para identificar o pedido. É utilizado para fins de resolução de problemas.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Códigos de estado de resposta

Seguem-se os possíveis códigos de estado HTTP que retorna um pedido. 

<table width="100%">
  <th width="20%">Código de Estado</th>
  <th>Descrição</th>
  <tr>
    <td>200</td>
    <td>Êxito.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Um dos parâmetros de consulta está em falta ou não é válido. Corrija os parâmetros de pedido antes de tentar novamente.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Não foi possível autenticar o pedido. Verifique se as credenciais estão especificado e válido.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>O pedido não está autorizado. Verifique os detalhes da mensagem de erro. Isso geralmente indica que foram utilizadas todas as conversões gratuitas fornecidas com uma subscrição de avaliação cópia de segurança.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>O chamador está enviando demasiados pedidos.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Ocorreu um erro inesperado. Se o o erro persistir, reporte-o com: data e hora da falha, o identificador de pedido do cabeçalho de resposta `X-RequestId`e o identificador de cliente a partir do cabeçalho de pedido `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servidor temporariamente indisponível. Repita o pedido. Se o o erro persistir, reporte-o com: data e hora da falha, o identificador de pedido do cabeçalho de resposta `X-RequestId`e o identificador de cliente a partir do cabeçalho de pedido `X-ClientTraceId`.</td>
  </tr>
</table> 

Se ocorrer um erro, o pedido também irá devolver uma resposta de erro do JSON. O código de erro é uma combinação de número de 6 dígitos o código de estado HTTP de 3 dígitos seguido por uma série de 3 dígitos para ainda mais categorizar o erro. Códigos de erro comuns que podem ser encontrados no [página de referência de API de texto do Microsoft Translator v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exemplos

O exemplo seguinte mostra como obter os idiomas suportados para a tradução de texto.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```

---
