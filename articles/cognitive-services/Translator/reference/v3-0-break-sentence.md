---
title: Método de rutura de tradutor
titleSuffix: Azure Cognitive Services
description: O método De Rutura do Tradutor identifica o posicionamento dos limites da frase num pedaço de texto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: 01a5404100da6c669da4513ac9fd08c959df220e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588637"
---
# <a name="translator-30-breaksentence"></a>Tradutor 3.0: BreakSentence

Identifica o posicionamento dos limites da frase num pedaço de texto.

## <a name="request-url"></a>URL do Pedido

Envie um `POST` pedido para:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros de pedido

Os parâmetros de pedido transmitidos na corda de consulta são:

| Parâmetro de consulta | Descrição |
| -------| ----------- |
| api-version <img width=200/>   | **Parâmetro**de consulta necessário .<br/>Versão da API solicitada pelo cliente. O valor deve `3.0` ser. |
| language | **Parâmetro de consulta opcional.**<br/>Etiqueta linguística identificando a linguagem do texto de entrada. Se um código não for especificado, será aplicada a deteção automática de idiomas. |
| .    | **Parâmetro de consulta opcional.**<br/>Etiqueta script identificando o script utilizado pelo texto de entrada. Se um script não for especificado, o script padrão da língua será assumido.  | 

Os cabeçalhos de pedido incluem:

| Cabeçalhos | Descrição |
| ------- | ----------- |
| Cabeçalho de autenticação <img width=200/>  | **Cabeçalho de pedido exigido**.<br/>Consulte <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">as opções disponíveis para autenticação.</a> |
| Content-Type | **Cabeçalho de pedido exigido**.<br/>Especifica o tipo de conteúdo da carga útil. Os valores possíveis são: `application/json` . |
| Comprimento do conteúdo    | **Cabeçalho de pedido exigido**.<br/>O comprimento do corpo de pedido.  | 
| X-ClientTraceid   | **Opcional.**<br/>Um GUID gerado pelo cliente para identificar exclusivamente o pedido. Note que pode omitir este cabeçalho se incluir o id de traço na corda de consulta usando um parâmetro de consulta chamado `ClientTraceId` .  | 

## <a name="request-body"></a>Corpo do pedido

O corpo do pedido é uma matriz JSON. Cada elemento matriz é um objeto JSON com uma propriedade de cordas chamada `Text` . Os limites da sentença são calculados pelo valor da `Text` propriedade. Um corpo de pedido de amostra com um pedaço de texto é assim:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Aplicam-se as seguintes limitações:

* A matriz pode ter no máximo 100 elementos.
* O valor de texto de um elemento matriz não pode exceder 10.000 caracteres, incluindo espaços.
* Todo o texto incluído no pedido não pode exceder 50.000 caracteres, incluindo espaços.
* Se o parâmetro de `language` consulta for especificado, todos os elementos da matriz devem estar na mesma língua. Caso contrário, a deteção automática da linguagem é aplicada a cada elemento matriz de forma independente.

## <a name="response-body"></a>Corpo da resposta

Uma resposta bem sucedida é um array JSON com um resultado para cada corda na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `sentLen`: Um conjunto de inteiros que representam os comprimentos das frases no elemento texto. O comprimento da matriz é o número de frases, e os valores são o comprimento de cada frase. 

  * `detectedLanguage`: Um objeto que descreve a linguagem detetada através das seguintes propriedades:

     * `language`: Código da linguagem detetada.

     * `score`: Um valor flutuante que indique a confiança no resultado. A pontuação é entre zero e um e uma pontuação baixa indica uma baixa confiança.
     
    Note que a `detectedLanguage` propriedade só está presente no objeto de resultados quando é solicitada a deteção automática da linguagem.

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
  <th>Descrição</th>
  <tr>
    <td>X-Requestid</td>
    <td>Valor gerado pelo serviço para identificar o pedido. É usado para fins de resolução de problemas.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Códigos de estado de resposta

Seguem-se os possíveis códigos de estado http que um pedido devolve. 

<table width="100%">
  <th width="20%">Código de Estado</th>
  <th>Descrição</th>
  <tr>
    <td>200</td>
    <td>Com êxito.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Um dos parâmetros de consulta está em falta ou não é válido. Corretos parâmetros de pedido antes de tentar novamente.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>O pedido não pôde ser autenticado. Verifique se as credenciais são especificadas e válidas.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>O pedido não está autorizado. Verifique a mensagem de erro dos detalhes. Isto indica frequentemente que todas as traduções gratuitas fornecidas com uma subscrição experimental foram usadas.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>O servidor rejeitou o pedido porque o cliente excedeu os limites de pedido.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Ocorreu um erro inesperado. Se o erro persistir, informe-o com: data e hora da falha, solicite o identificador do cabeçalho de resposta `X-RequestId` e identificador do cliente do cabeçalho do pedido `X-ClientTraceId` .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servidor temporariamente indisponível. Tente o pedido. Se o erro persistir, informe-o com: data e hora da falha, solicite o identificador do cabeçalho de resposta `X-RequestId` e identificador do cliente do cabeçalho do pedido `X-ClientTraceId` .</td>
  </tr>
</table> 

Se ocorrer um erro, o pedido também devolverá uma resposta de erro da JSON. O código de erro é um número de 6 dígitos que combina o código de estado HTTP de 3 dígitos seguido de um número de 3 dígitos para categorizar ainda mais o erro. Códigos de erro comuns podem ser encontrados na página de referência do [tradutor v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exemplos

O exemplo que se segue mostra como obter limites de sentença para uma única frase. A linguagem da frase é automaticamente detetada pelo serviço.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

