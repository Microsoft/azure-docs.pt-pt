---
title: API de Tradução de Texto método transliterate
titleSuffix: Azure Cognitive Services
description: Converta o texto em um idioma de um script para outro com o método transliterate API de Tradução de Texto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: e6bb1541b2b668796b352bebc68d59b4ade143e3
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837283"
---
# <a name="translator-text-api-30-transliterate"></a>API de Tradução de Texto 3,0: transliteração

Converte o texto em um idioma de um script para outro.

## <a name="request-url"></a>URL do Pedido

Enviar uma solicitação de `POST` para:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros de solicitação

Os parâmetros de solicitação passados na cadeia de caracteres de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>versão da API</td>
    <td>*Parâmetro obrigatório*.<br/>Versão da API solicitada pelo cliente. O valor deve ser `3.0`.</td>
  </tr>
  <tr>
    <td>language</td>
    <td>*Parâmetro obrigatório*.<br/>Especifica o idioma do texto a ser convertido de um script para outro. As linguagens possíveis são listadas no escopo de `transliteration` obtido pela consulta do serviço para seus [idiomas com suporte](./v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Parâmetro obrigatório*.<br/>Especifica o script usado pelo texto de entrada. Pesquise os [idiomas com suporte](./v3-0-languages.md) usando o escopo de `transliteration`, para localizar os scripts de entrada disponíveis para o idioma selecionado.</td>
  </tr>
  <tr>
    <td>toscript</td>
    <td>*Parâmetro obrigatório*.<br/>Especifica o script de saída. Pesquise os [idiomas com suporte](./v3-0-languages.md) usando o escopo de `transliteration` para localizar os scripts de saída disponíveis para a combinação selecionada de idioma de entrada e script de entrada.</td>
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
    <td>Tipo de conteúdo</td>
    <td>*Cabeçalho de solicitação necessário*.<br/>Especifica o tipo de conteúdo da carga. Os valores possíveis são: `application/json`.</td>
  </tr>
  <tr>
    <td>Comprimento do conteúdo</td>
    <td>*Cabeçalho de solicitação necessário*.<br/>O comprimento do corpo da solicitação.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Opcional*.<br/>Um GUID gerado pelo cliente para identificar exclusivamente a solicitação. Observe que você pode omitir esse cabeçalho se incluir a ID de rastreamento na cadeia de caracteres de consulta usando um parâmetro de consulta chamado `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Corpo do pedido

O corpo da solicitação é uma matriz JSON. Cada elemento da matriz é um objeto JSON com uma propriedade de cadeia de caracteres chamada `Text`, que representa a cadeia de caracteres a ser convertida.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

As seguintes limitações se aplicam:

* A matriz pode ter no máximo 10 elementos.
* O valor de texto de um elemento de matriz não pode exceder 1.000 caracteres, incluindo espaços.
* O texto inteiro incluído na solicitação não pode exceder 5.000 caracteres, incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta bem-sucedida é uma matriz JSON com um resultado para cada elemento na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `text`: uma cadeia de caracteres que é o resultado da conversão da cadeia de caracteres de entrada para o script de saída.
  
  * `script`: uma cadeia de caracteres que especifica o script usado na saída.

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
    <td>Valor gerado pelo serviço para identificar a solicitação. Ele é usado para fins de solução de problemas.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Códigos de status de resposta

A seguir estão os códigos de status HTTP possíveis que uma solicitação retorna. 

<table width="100%">
  <th width="20%">Código de status</th>
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
    <td>Não foi possível autenticar a solicitação. Verifique se as credenciais estão especificadas e válidas.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>A solicitação não é autorizada. Verifique a mensagem de erro detalhes. Isso geralmente indica que todas as traduções gratuitas fornecidas com uma assinatura de avaliação foram usadas.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>O servidor rejeitou a solicitação porque o cliente excedeu os limites de solicitação.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Ocorreu um erro inesperado. Se o erro persistir, denuncie-o com: data e hora da falha, identificador de solicitação do cabeçalho de resposta `X-RequestId`e identificador de cliente do cabeçalho de solicitação `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servidor temporariamente indisponível. Repita a solicitação. Se o erro persistir, denuncie-o com: data e hora da falha, identificador de solicitação do cabeçalho de resposta `X-RequestId`e identificador de cliente do cabeçalho de solicitação `X-ClientTraceId`.</td>
  </tr>
</table> 

Se ocorrer um erro, a solicitação também retornará uma resposta de erro JSON. O código de erro é um número de 6 dígitos que combina o código de status HTTP de 3 dígitos seguido por um número de 3 dígitos para categorizar ainda mais o erro. Códigos de erro comuns podem ser encontrados na [página de referência do API de tradução de texto v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exemplos

O exemplo a seguir mostra como converter duas cadeias de caracteres japonesas em Japonês romano.

O conteúdo JSON para a solicitação neste exemplo:

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Se você estiver usando uma ondulação em uma janela de linha de comando que não ofereça suporte a caracteres Unicode, use a seguinte carga JSON e salve-a em um arquivo chamado `request.txt`. Certifique-se de salvar o arquivo com a codificação `UTF-8`.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```
