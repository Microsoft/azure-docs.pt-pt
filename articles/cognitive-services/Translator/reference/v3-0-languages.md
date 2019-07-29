---
title: Método de idiomas API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Use o método de idiomas API de Tradução de Texto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: c5298c038f056942ddc754482cbf4ec8cd77b48b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595017"
---
# <a name="translator-text-api-30-languages"></a>API de Tradução de Texto 3,0: Languages

Obtém o conjunto de idiomas com suporte no momento por outras operações do API de Tradução de Texto. 

## <a name="request-url"></a>URL do Pedido

Enviar uma `GET` solicitação para:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros do pedido

Os parâmetros de solicitação passados na cadeia de caracteres de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>versão de API</td>
    <td><em>Parâmetro obrigatório</em>.<br/>Versão da API solicitada pelo cliente. O valor deve `3.0`ser.</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Parâmetro opcional*.<br/>Uma lista separada por vírgulas de nomes que definem o grupo de idiomas a serem retornados. Os nomes de grupo permitidos `translation`são `transliteration` : `dictionary`, e. Se nenhum escopo for fornecido, todos os grupos serão retornados, o que equivale a passar `scope=translation,transliteration,dictionary`. Para decidir qual conjunto de idiomas com suporte é apropriado para seu cenário, consulte a descrição do [objeto de resposta](#response-body).</td>
  </tr>
</table> 

Os cabeçalhos de solicitação são:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>Aceitar idioma</td>
    <td>*Cabeçalho de solicitação opcional*.<br/>O idioma a utilizar nas cadeias da interface de utilizador. Alguns dos campos na resposta são nomes de idiomas ou nomes de regiões. Use esse parâmetro para definir o idioma no qual esses nomes são retornados. O idioma é especificado fornecendo uma marca de linguagem BCP 47 bem formada. Por exemplo, use o valor `fr` para solicitar nomes em francês ou use o valor `zh-Hant` para solicitar nomes em Chinês tradicional.<br/>Os nomes são fornecidos no idioma inglês quando um idioma de destino não é especificado ou quando a localização não está disponível.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Cabeçalho de solicitação opcional*.<br/>Um GUID gerado pelo cliente para identificar exclusivamente a solicitação.</td>
  </tr>
</table> 

A autenticação não é necessária para obter recursos de idioma.

## <a name="response-body"></a>Corpo da resposta

Um cliente usa o `scope` parâmetro de consulta para definir em quais grupos de idiomas ele está interessado.

* `scope=translation`fornece idiomas com suporte para traduzir o texto de um idioma para outro idioma;

* `scope=transliteration`fornece recursos para converter texto em um idioma de um script para outro;

* `scope=dictionary`fornece pares de idiomas para `Dictionary` os quais as operações retornam dados.

Um cliente pode recuperar vários grupos simultaneamente, especificando uma lista de nomes separados por vírgulas. Por exemplo, `scope=translation,transliteration,dictionary` retornaria idiomas com suporte para todos os grupos.

Uma resposta bem-sucedida é um objeto JSON com uma propriedade para cada grupo solicitado:

```json
{
    "translation": {
        //... set of languages supported to translate text (scope=translation)
    },
    "transliteration": {
        //... set of languages supported to convert between scripts (scope=transliteration)
    },
    "dictionary": {
        //... set of languages supported for alternative translations and examples (scope=dictionary)
    }
}
```

O valor de cada propriedade é o seguinte.

* `translation`Propriedade

  O valor da `translation` propriedade é um dicionário de pares (chave, valor). Cada chave é uma marca de idioma BCP 47. Uma chave identifica um idioma para o qual o texto pode ser traduzido ou traduzido. O valor associado à chave é um objeto JSON com propriedades que descrevem o idioma:

  * `name`: Nome de exibição do idioma na localidade solicitada por `Accept-Language` meio do cabeçalho.

  * `nativeName`: Nome de exibição do idioma na localidade nativa para este idioma.

  * `dir`: A direcionalidade, que `rtl` é para idiomas da direita para a esquerda `ltr` ou para idiomas da esquerda para a direita.

  Um exemplo é:
          
  ```json
  {
    "translation": {
      ...
      "fr": {
        "name": "French",
        "nativeName": "Français",
        "dir": "ltr"
      },
      ...
    }
  }
  ```

* `transliteration`Propriedade

  O valor da `transliteration` propriedade é um dicionário de pares (chave, valor). Cada chave é uma marca de idioma BCP 47. Uma chave identifica um idioma para o qual o texto pode ser convertido de um script para outro script. O valor associado à chave é um objeto JSON com propriedades que descrevem o idioma e seus scripts com suporte:

  * `name`: Nome de exibição do idioma na localidade solicitada por `Accept-Language` meio do cabeçalho.

  * `nativeName`: Nome de exibição do idioma na localidade nativa para este idioma.

  * `scripts`: Lista de scripts dos quais converter. Cada elemento da `scripts` lista tem propriedades:

    * `code`: Código que identifica o script.

    * `name`: Nome de exibição do script na localidade solicitada por `Accept-Language` meio do cabeçalho.

    * `nativeName`: Nome de exibição do idioma na localidade nativa para o idioma.

    * `dir`: A direcionalidade, que `rtl` é para idiomas da direita para a esquerda `ltr` ou para idiomas da esquerda para a direita.

    * `toScripts`: Lista de scripts disponíveis para converter o texto. `toScripts` Cada elemento da lista tem propriedades `name` `code`,, `nativeName`e `dir` conforme descrito anteriormente.

  Um exemplo é:

  ```json
  {
    "transliteration": {
      ...
      "ja": {
        "name": "Japanese",
        "nativeName": "日本語",
        "scripts": [
          {
            "code": "Jpan",
            "name": "Japanese",
            "nativeName": "日本語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Latn",
                "name": "Latin",
                "nativeName": "ラテン語",
                "dir": "ltr"
              }
            ]
          },
          {
            "code": "Latn",
            "name": "Latin",
            "nativeName": "ラテン語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Jpan",
                "name": "Japanese",
                "nativeName": "日本語",
                "dir": "ltr"
              }
            ]
          }
        ]
      },
      ...
    }
  }
  ```

* `dictionary`Propriedade

  O valor da `dictionary` propriedade é um dicionário de pares (chave, valor). Cada chave é uma marca de idioma BCP 47. A chave identifica um idioma para o qual as traduções alternativas e as traduções traseiras estão disponíveis. O valor é um objeto JSON que descreve o idioma de origem e os idiomas de destino com traduções disponíveis:

  * `name`: Nome de exibição do idioma de origem na localidade solicitada `Accept-Language` por meio do cabeçalho.

  * `nativeName`: Nome de exibição do idioma na localidade nativa para este idioma.

  * `dir`: A direcionalidade, que `rtl` é para idiomas da direita para a esquerda `ltr` ou para idiomas da esquerda para a direita.

  * `translations`: Lista de idiomas com traduções alternativa e exemplos para a consulta expressa no idioma de origem. Cada elemento da `translations` lista tem propriedades:

    * `name`: Nome de exibição do idioma de destino na localidade solicitada `Accept-Language` por meio do cabeçalho.

    * `nativeName`: Nome de exibição do idioma de destino na localidade nativa para o idioma de destino.

    * `dir`: A direcionalidade, que `rtl` é para idiomas da direita para a esquerda `ltr` ou para idiomas da esquerda para a direita.
    
    * `code`: Código de idioma que identifica o idioma de destino.

  Um exemplo é:

  ```json
  "es": {
    "name": "Spanish",
    "nativeName": "Español",
    "dir": "ltr",
    "translations": [
      {
        "name": "English",
        "nativeName": "English",
        "dir": "ltr",
        "code": "en"
      }
    ]
  },
  ```

A estrutura do objeto de resposta não será alterada sem uma alteração na versão da API. Para a mesma versão da API, a lista de idiomas disponíveis pode mudar ao longo do tempo, pois o Microsoft Translator estende continuamente a lista de idiomas com suporte por seus serviços.

A lista de idiomas com suporte não será alterada com frequência. Para economizar largura de banda de rede e melhorar a capacidade de resposta, um aplicativo cliente deve considerar armazenar em cache os`ETag`recursos de idioma e a marca de entidade correspondente (). Em seguida, o aplicativo cliente pode periodicamente (por exemplo, uma vez a cada 24 horas) para consultar o serviço para buscar o conjunto mais recente de idiomas com suporte. Passar o valor `ETag` atual em um `If-None-Match` campo de cabeçalho permitirá que o serviço Otimize a resposta. Se o recurso não tiver sido modificado, o serviço retornará o código de status 304 e um corpo de resposta vazio.

## <a name="response-headers"></a>Cabeçalhos de resposta

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>eTag</td>
    <td>Valor atual da marca de entidade para os grupos de idiomas com suporte solicitados. Para tornar as solicitações subsequentes mais eficientes, o cliente pode `ETag` enviar o valor `If-None-Match` em um campo de cabeçalho.
    </td>
  </tr>
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
    <td>304</td>
    <td>O recurso não foi modificado desde a versão especificada pelos cabeçalhos `If-None-Match`de solicitação.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Um dos parâmetros de consulta está ausente ou não é válido. Corrija os parâmetros de solicitação antes de tentar novamente.</td>
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

O exemplo a seguir mostra como recuperar idiomas com suporte para tradução de texto.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```

---
