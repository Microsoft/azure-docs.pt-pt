---
title: Método de BreakSentence de API de texto do tradutor
titlesuffix: Azure Cognitive Services
description: Use o método BreakSentence de API de texto do Translator.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: 3a9c2ae0eee3e282dddff812da2fed07787328b7
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916296"
---
# <a name="translator-text-api-30-breaksentence"></a>Texto do Translator API 3.0: BreakSentence

Identifica o posicionamento dos limites da sentença num pedaço de texto.

## <a name="request-url"></a>URL do Pedido

Enviar um `POST` pedido para:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros do pedido

Parâmetros de pedido passados na seqüência de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>versão de API</td>
    <td>*Parâmetro de consulta necessário*.<br/>Versão da API do pedido pelo cliente. Valor tem de ser `3.0`.</td>
  </tr>
  <tr>
    <td>language</td>
    <td>*Parâmetro de consulta opcionais*.<br/>Etiqueta de idioma, identificando o idioma do texto de entrada. Se não for especificado um código, deteção de idioma automática será aplicada.</td>
  </tr>
  <tr>
    <td>Script</td>
    <td>*Parâmetro de consulta opcionais*.<br/>Marca de script, identificando o script utilizado pelo texto de entrada. Se não for especificado um script, será assumido o script de predefinição da linguagem.</td>
  </tr>
</table> 

Cabeçalhos de pedido incluem:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>_Uma autorização_<br/>_cabeçalho_</td>
    <td>*Cabeçalho do pedido necessário*.<br/>Ver [as opções disponíveis para autenticação](./v3-0-reference.md#authentication).</td>
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

O corpo do pedido é uma matriz JSON. Cada elemento da matriz é um objeto JSON com uma propriedade de cadeia de caracteres chamada `Text`. Limites de sentença são calculados para o valor do `Text` propriedade. Um corpo de pedido de exemplo com um elemento de texto terá um aspeto parecido:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

As seguintes limitações aplicam-se:

* A matriz pode ter no máximo de 100 elementos.
* O valor de texto de um elemento de matriz não pode ter mais de 10.000 carateres, incluindo espaços.
* Todo o texto incluído no pedido não pode ter mais de 50.000 carateres, incluindo espaços.
* Se o `language` é especificado o parâmetro de consulta, em seguida, todos os elementos de matriz tem de estar no mesmo idioma. Caso contrário, a deteção automática de idioma é aplicada a cada elemento de matriz independentemente.

## <a name="response-body"></a>Corpo da resposta

Uma resposta com êxito é uma matriz JSON com um resultado para cada cadeia de caracteres na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `sentLen`: Uma matriz de inteiros que representam os comprimentos das sentenças do elemento de texto. O comprimento da matriz é o número de frases e os valores são o comprimento de cada sentença. 

  * `detectedLanguage`: Um objeto que descreve o idioma detetado através das seguintes propriedades:

     * `language`: Código do idioma detetado.

     * `score`: Um valor de vírgula flutuante que indica a confiança no resultado. A classificação é entre zero e outro e uma pontuação baixa indica uma confiança de baixa.
     
    Tenha em atenção que o `detectedLanguage` propriedade só está presente no objeto de resultado quando é solicitada a deteção automática de idioma.

Um resposta JSON de exemplo é:

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

O exemplo seguinte mostra como obter os limites de frase para uma única frase. O idioma da sentença é detetado automaticamente pelo serviço.

# [<a name="curl"></a>Curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

---

