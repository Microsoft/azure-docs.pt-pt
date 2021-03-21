---
title: Método de quebra de tradutor
titleSuffix: Azure Cognitive Services
description: O método Desenteria do Tradutor identifica o posicionamento dos limites da frase num pedaço de texto.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/06/2020
ms.author: lajanuar
ms.openlocfilehash: 2da614fe829d0aa82bfa57337baf44491993c68f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895548"
---
# <a name="translator-30-breaksentence"></a>Tradutor 3.0: BreakSentence

Identifica o posicionamento dos limites da frase num pedaço de texto.

## <a name="request-url"></a>URL do Pedido

Envie um `POST` pedido para:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros de pedido

Os parâmetros de pedido passados na cadeia de consulta são:

| Parâmetro de consulta | Description |
| -------| ----------- |
| api-version <img width=200/>   | **Parâmetro de consulta exigido.**<br/>Versão da API solicitada pelo cliente. O valor deve `3.0` ser. |
| language | **Parâmetro de consulta opcional.**<br/>Etiqueta linguística que identifica a linguagem do texto de entrada. Se um código não for especificado, aplicar-se-á a deteção automática da linguagem. |
| script    | **Parâmetro de consulta opcional.**<br/>Etiqueta de script que identifica o script utilizado pelo texto de entrada. Se um script não for especificado, o script padrão da língua será assumido.  | 

Os cabeçalhos de pedido incluem:

| Cabeçalhos | Description |
| ------- | ----------- |
| Cabeçalho de autenticação <img width=200/>  | **Cabeçalho de pedido necessário**.<br/>Consulte <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">as opções disponíveis para autenticação.</a> |
| Content-Type | **Cabeçalho de pedido necessário**.<br/>Especifica o tipo de conteúdo da carga útil. Os valores possíveis são: `application/json` . |
| Comprimento do conteúdo    | **Cabeçalho de pedido necessário**.<br/>O comprimento do corpo do pedido.  | 
| X-ClientTraceId   | **Opcional.**<br/>Um GUID gerado pelo cliente para identificar exclusivamente o pedido. Note que pode omitir este cabeçalho se incluir o ID de traço na cadeia de consulta utilizando um parâmetro de consulta denominado `ClientTraceId` .  | 

## <a name="request-body"></a>Corpo do pedido

O corpo do pedido é uma matriz JSON. Cada elemento de matriz é um objeto JSON com uma propriedade de corda chamada `Text` . Os limites da sentença são calculados pelo valor do `Text` imóvel. Um corpo de pedido de amostra com um pedaço de texto é assim:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Aplicam-se as seguintes limitações:

* A matriz pode ter no máximo 100 elementos.
* O valor de texto de um elemento de matriz não pode exceder 50.000 caracteres, incluindo espaços.
* Todo o texto incluído no pedido não pode exceder 50.000 caracteres, incluindo espaços.
* Se o `language` parâmetro de consulta for especificado, todos os elementos de matriz devem estar na mesma língua. Caso contrário, a deteção automática da linguagem é aplicada a cada elemento de matriz de forma independente.

## <a name="response-body"></a>Corpo da resposta

Uma resposta bem sucedida é uma matriz JSON com um resultado para cada cadeia na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `sentLen`: Uma série de inteiros que representam os comprimentos das frases no elemento de texto. O comprimento da matriz é o número de frases, e os valores são o comprimento de cada frase. 

  * `detectedLanguage`: Um objeto que descreve a linguagem detetada através das seguintes propriedades:

     * `language`: Código da língua detetada.

     * `score`: Um valor flutuante que indique a confiança no resultado. A pontuação é entre zero e um e uma pontuação baixa indica uma baixa confiança.
     
    Note que a `detectedLanguage` propriedade só está presente no objeto de resultado quando é solicitada a deteção automática da língua.

Um exemplo de resposta JSON é:

```json
[
  {
    "sentLen": [ 13, 11, 22 ]
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
  <th>Description</th>
  <tr>
    <td>X-RequestId</td>
    <td>Valor gerado pelo serviço para identificar o pedido. É utilizado para resolução de problemas.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Códigos de estado de resposta

Seguem-se os possíveis códigos de estado HTTP que um pedido devolve. 

<table width="100%">
  <th width="20%">Código de Estado</th>
  <th>Description</th>
  <tr>
    <td>200</td>
    <td>Com êxito.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Um dos parâmetros de consulta está em falta ou não é válido. Corretos parâmetros de pedido antes de voltar a tentar.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>O pedido não pôde ser autenticado. Verifique se as credenciais são especificadas e válidas.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>O pedido não está autorizado. Verifique a mensagem de erro dos detalhes. Isto indica frequentemente que todas as traduções gratuitas fornecidas com uma subscrição experimental foram utilizadas.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>O servidor rejeitou o pedido porque o cliente excedeu os limites de pedido.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Ocorreu um erro inesperado. Se o erro persistir, informe-o com: data e hora da falha, solicite o identificador do cabeçalho de resposta `X-RequestId` e o identificador do cliente do cabeçalho do pedido `X-ClientTraceId` .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servidor temporariamente indisponível. Recandidutar o pedido. Se o erro persistir, informe-o com: data e hora da falha, solicite o identificador do cabeçalho de resposta `X-RequestId` e o identificador do cliente do cabeçalho do pedido `X-ClientTraceId` .</td>
  </tr>
</table> 

Se ocorrer um erro, o pedido também devolverá uma resposta de erro JSON. O código de erro é um número de 6 dígitos que combina o código de estado HTTP de 3 dígitos seguido de um número de 3 dígitos para categorizar ainda mais o erro. Os códigos de erro comuns podem ser encontrados na [página de referência do Tradutor V3](./v3-0-reference.md#errors). 

## <a name="examples"></a>Exemplos

O exemplo a seguir mostra como obter limites de sentença para uma única frase. A linguagem da frase é detetada automaticamente pelo serviço.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```