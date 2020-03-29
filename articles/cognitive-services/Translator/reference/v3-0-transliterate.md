---
title: Método transliterado de texto tradutor API
titleSuffix: Azure Cognitive Services
description: Converta o texto numa língua de um guião para outro com o método transliterado de Texto Tradutor API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: e6bb1541b2b668796b352bebc68d59b4ade143e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837283"
---
# <a name="translator-text-api-30-transliterate"></a>Texto tradutor API 3.0: Transliterado

Converte texto numa língua de um script para outro.

## <a name="request-url"></a>URL do Pedido

Envie `POST` um pedido para:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros de pedido

Os parâmetros de pedido transmitidos na corda de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>api-version</td>
    <td>*Parâmetro necessário*.<br/>Versão da API solicitada pelo cliente. O valor `3.0`deve ser.</td>
  </tr>
  <tr>
    <td>language</td>
    <td>*Parâmetro necessário*.<br/>Especifica a linguagem do texto para converter de um script para outro. As línguas possíveis `transliteration` estão listadas no âmbito obtido consultando o serviço para as suas [línguas apoiadas.](./v3-0-languages.md)</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Parâmetro necessário*.<br/>Especifica o script utilizado pelo texto de entrada. Procure [idiomas suportados](./v3-0-languages.md) `transliteration` usando o âmbito, para encontrar scripts de entrada disponíveis para o idioma selecionado.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Parâmetro necessário*.<br/>Especifica o guião de saída. Procure [idiomas suportados](./v3-0-languages.md) `transliteration` usando o âmbito, para encontrar scripts de saída disponíveis para a combinação selecionada de idioma de entrada e script de entrada.</td>
  </tr>
</table> 

Os cabeçalhos de pedido incluem:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>Cabeçalho de autenticação</td>
    <td><em>Cabeçalho de pedido exigido</em>.<br/>Consulte <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">as opções disponíveis para autenticação.</a></td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Cabeçalho de pedido exigido*.<br/>Especifica o tipo de conteúdo da carga útil. Os valores `application/json`possíveis são: .</td>
  </tr>
  <tr>
    <td>Comprimento do conteúdo</td>
    <td>*Cabeçalho de pedido exigido*.<br/>O comprimento do corpo de pedido.</td>
  </tr>
  <tr>
    <td>X-ClientTraceid</td>
    <td>*Opcional.*<br/>Um GUID gerado pelo cliente para identificar exclusivamente o pedido. Note que pode omitir este cabeçalho se incluir o id de traço `ClientTraceId`na corda de consulta usando um parâmetro de consulta chamado .</td>
  </tr>
</table> 

## <a name="request-body"></a>Corpo do pedido

O corpo do pedido é uma matriz JSON. Cada elemento matriz é um objeto JSON com uma propriedade de corda chamada, `Text`que representa a cadeia para converter.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Aplicam-se as seguintes limitações:

* A matriz pode ter no máximo 10 elementos.
* O valor de texto de um elemento matriz não pode exceder 1.000 caracteres, incluindo espaços.
* Todo o texto incluído no pedido não pode exceder 5.000 caracteres, incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta bem sucedida é um array JSON com um resultado para cada elemento na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `text`: Uma corda que é o resultado da conversão da cadeia de entrada para o script de saída.
  
  * `script`: Uma cadeia que especifica o guião utilizado na saída.

Um exemplo de resposta JSON é:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
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
    <td>Êxito.</td>
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
    <td>Ocorreu um erro inesperado. Se o erro persistir, informe-o com: data e hora `X-RequestId`da falha, solicite `X-ClientTraceId`o identificador do cabeçalho de resposta e identificador do cliente do cabeçalho do pedido .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servidor temporariamente indisponível. Tente o pedido. Se o erro persistir, informe-o com: data e hora `X-RequestId`da falha, solicite `X-ClientTraceId`o identificador do cabeçalho de resposta e identificador do cliente do cabeçalho do pedido .</td>
  </tr>
</table> 

Se ocorrer um erro, o pedido também devolverá uma resposta de erro da JSON. O código de erro é um número de 6 dígitos que combina o código de estado HTTP de 3 dígitos seguido de um número de 3 dígitos para categorizar ainda mais o erro. Códigos de erro comuns podem ser encontrados na página de referência da API do [texto do tradutor v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exemplos

O exemplo que se segue mostra como converter duas cordas japonesas em japonês romanizado.

A carga útil da JSON para o pedido neste exemplo:

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Se estiver a utilizar cURL numa janela de linha de comando que não suporta caracteres Unicode, `request.txt`pegue na seguinte carga útil jSON e guarde-a num ficheiro chamado . Certifique-se de guardar `UTF-8` o ficheiro com codificação.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```
