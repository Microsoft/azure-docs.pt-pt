---
title: Método de transcrição de API de texto do tradutor
titlesuffix: Azure Cognitive Services
description: Use o método de Transliteração de API de texto do Translator.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-pawal
ms.openlocfilehash: 138a04cca1bbbaf7b59f628f491a5f13d73fb6f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387398"
---
# <a name="translator-text-api-30-transliterate"></a>Texto do Translator API 3.0: Transliterate

Converte texto num idioma de um script para outro script.

## <a name="request-url"></a>URL do Pedido

Enviar um `POST` pedido para:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
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
  <tr>
    <td>language</td>
    <td>*Parâmetro necessário*.<br/>Especifica o idioma do texto para converter a partir de um script para outro. Idiomas possíveis estão listados na `transliteration` âmbito de obteve consultando o serviço para o seu [idiomas suportados](./v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Parâmetro necessário*.<br/>Especifica o script utilizado pelo texto de entrada. Procure [idiomas suportados](./v3-0-languages.md) usando o `transliteration` escopo, para localizar a entrada scripts disponíveis para o idioma seleccionado.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Parâmetro necessário*.<br/>Especifica o script de saída. Procure [idiomas suportados](./v3-0-languages.md) usando o `transliteration` escopo, para encontrar scripts de saída disponíveis para a combinação selecionada de idioma de entrada e de entrada de script.</td>
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

O corpo do pedido é uma matriz JSON. Cada elemento da matriz é um objeto JSON com uma propriedade de cadeia de caracteres chamada `Text`, que representa a cadeia a converter.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

As seguintes limitações aplicam-se:

* A matriz pode ter no máximo 10 elementos.
* O valor de texto de um elemento de matriz não pode ter mais de 1000 carateres, incluindo espaços.
* Todo o texto incluído no pedido não pode exceder os 5000 carateres, incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta com êxito é uma matriz JSON com um resultado para cada elemento na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `text`: Uma cadeia de caracteres que é o resultado da conversão de cadeia de entrada para o script de saída.
  
  * `script`: Uma cadeia que especifica o script utilizado na saída.

Um resposta JSON de exemplo é:

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
    <td>Valor gerado pelo serviço para identificar o pedido. É utilizado para fins de resolução de problemas.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Códigos de estado de resposta

Seguem-se os possíveis códigos de estado HTTP que retorna um pedido. 

<table width="100%">
  <th width="20%">Código de estado</th>
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
    <td>O servidor rejeitou o pedido porque o cliente excedeu os limites de pedido.</td>
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

O exemplo seguinte mostra como converter duas cadeias de caracteres japoneses em Romanized japonês.

# <a name="curltabcurl"></a>[curl](#tab/curl)

O payload JSON para o pedido neste exemplo:

```
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Se estiver a utilizar o cURL numa janela da linha de comandos que não suporta carateres Unicode, pegar o payload JSON seguinte e guarde-o para um ficheiro denominado `request.txt`. Certifique-se de que guarde o ficheiro com `UTF-8` codificação.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```

---
