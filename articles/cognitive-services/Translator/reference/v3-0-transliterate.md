---
title: Método Transliterado tradutor
titleSuffix: Azure Cognitive Services
description: Converter texto numa língua de um script para outro script com o método de Transliteto de Tradutor.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: 0d5a7f8df0c722ca52780ba254e9af9608f26b54
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895429"
---
# <a name="translator-30-transliterate"></a>Tradutor 3.0: Transliterado

Converte texto numa língua de um script para outro script.

## <a name="request-url"></a>URL do Pedido

Envie um `POST` pedido para:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros de pedido

Os parâmetros de pedido passados na cadeia de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>api-version</td>
    <td>*Parâmetro requerido*.<br/>Versão da API solicitada pelo cliente. O valor deve `3.0` ser.</td>
  </tr>
  <tr>
    <td>language</td>
    <td>*Parâmetro requerido*.<br/>Especifica a linguagem do texto para converter de um script para outro. As línguas possíveis são listadas no `transliteration` âmbito obtido consultando o serviço para as suas [línguas suportadas.](./v3-0-languages.md)</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Parâmetro requerido*.<br/>Especifica o script utilizado pelo texto de entrada. Procure [idiomas suportados](./v3-0-languages.md) usando o `transliteration` âmbito, para encontrar scripts de entrada disponíveis para o idioma selecionado.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Parâmetro requerido*.<br/>Especifica o script de saída. Procure [idiomas suportados](./v3-0-languages.md) usando o `transliteration` âmbito, para encontrar scripts de saída disponíveis para a combinação selecionada de linguagem de entrada e script de entrada.</td>
  </tr>
</table> 

Os cabeçalhos de pedido incluem:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>Cabeçalho de autenticação</td>
    <td><em>Cabeçalho de pedido necessário</em>.<br/>Consulte <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">as opções disponíveis para autenticação.</a></td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Cabeçalho de pedido necessário*.<br/>Especifica o tipo de conteúdo da carga útil. Os valores possíveis são: `application/json` .</td>
  </tr>
  <tr>
    <td>Comprimento do conteúdo</td>
    <td>*Cabeçalho de pedido necessário*.<br/>O comprimento do corpo do pedido.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Opcional.*<br/>Um GUID gerado pelo cliente para identificar exclusivamente o pedido. Note que pode omitir este cabeçalho se incluir o ID de traço na cadeia de consulta utilizando um parâmetro de consulta denominado `ClientTraceId` .</td>
  </tr>
</table> 

## <a name="request-body"></a>Corpo do pedido

O corpo do pedido é uma matriz JSON. Cada elemento de matriz é um objeto JSON com uma propriedade de corda chamada `Text` , que representa a corda para converter.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Aplicam-se as seguintes limitações:

* A matriz pode ter no máximo 10 elementos.
* O valor de texto de um elemento de matriz não pode exceder 1.000 caracteres, incluindo espaços.
* Todo o texto incluído no pedido não pode exceder 5.000 caracteres, incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta bem sucedida é uma matriz JSON com um resultado para cada elemento na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `text`: Uma corda que é o resultado da conversão da cadeia de entrada para o script de saída.
  
  * `script`: Uma cadeia especificando o script utilizado na saída.

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
    <td>X-RequestId</td>
    <td>Valor gerado pelo serviço para identificar o pedido. É utilizado para resolução de problemas.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Códigos de estado de resposta

Seguem-se os possíveis códigos de estado HTTP que um pedido devolve. 

<table width="100%">
  <th width="20%">Código de Estado</th>
  <th>Descrição</th>
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

Se ocorrer um erro, o pedido também retorna uma resposta de erro JSON. O código de erro é um número de 6 dígitos que combina o código de estado HTTP de 3 dígitos seguido de um número de 3 dígitos para categorizar ainda mais o erro. Os códigos de erro comuns podem ser encontrados na [página de referência do Tradutor V3](./v3-0-reference.md#errors). 

## <a name="examples"></a>Exemplos

O exemplo a seguir mostra como converter duas cordas japonesas em japonês romanizado.

A carga útil do JSON para o pedido neste exemplo:

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Se estiver a utilizar o cURL numa janela de linha de comando que não suporta caracteres Unicode, pegue na carga útil JSON seguinte e guarde-a num ficheiro denominado `request.txt` . Certifique-se de guardar o ficheiro com `UTF-8` codificação.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```