---
title: Método de idiomas API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: O método Languages Obtém o conjunto de idiomas com suporte no momento por outras operações do API de Tradução de Texto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 37f70399e8125db559098869cdfffdf4533498d7
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835835"
---
# <a name="translator-text-api-30-languages"></a>API de Tradução de Texto 3,0: idiomas

Obtém o conjunto de idiomas com suporte no momento por outras operações do API de Tradução de Texto. 

## <a name="request-url"></a>URL do Pedido

Enviar uma solicitação de `GET` para:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros de solicitação

Os parâmetros de solicitação passados na cadeia de caracteres de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>versão da API</td>
    <td><em>Parâmetro obrigatório</em>.<br/>Versão da API solicitada pelo cliente. O valor deve ser `3.0`.</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Parâmetro opcional*.<br/>Uma lista separada por vírgulas de nomes que definem o grupo de idiomas a serem retornados. Os nomes de grupo permitidos são: `translation`, `transliteration` e `dictionary`. Se nenhum escopo for fornecido, todos os grupos serão retornados, o que equivale a passar `scope=translation,transliteration,dictionary`. Para decidir qual conjunto de idiomas com suporte é apropriado para seu cenário, consulte a descrição do [objeto de resposta](#response-body).</td>
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

Um cliente usa o parâmetro de consulta `scope` para definir em quais grupos de idiomas ele está interessado.

* o `scope=translation` fornece idiomas com suporte para traduzir o texto de um idioma para outro idioma;

* `scope=transliteration` fornece recursos para converter texto em um idioma de um script para outro;

* `scope=dictionary` fornece pares de idiomas para os quais as operações de `Dictionary` retornam dados.

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

* Propriedade `translation`

  O valor da propriedade `translation` é um dicionário de pares (chave, valor). Cada chave é uma marca de idioma BCP 47. Uma chave identifica um idioma para o qual o texto pode ser traduzido ou traduzido. O valor associado à chave é um objeto JSON com propriedades que descrevem o idioma:

  * `name`: exibir o nome do idioma na localidade solicitada por meio do cabeçalho `Accept-Language`.

  * `nativeName`: exibir o nome do idioma na localidade nativa para este idioma.

  * `dir`: direcionalidade, que é `rtl` para idiomas da direita para a esquerda ou `ltr` para idiomas da esquerda para a direita.

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

* Propriedade `transliteration`

  O valor da propriedade `transliteration` é um dicionário de pares (chave, valor). Cada chave é uma marca de idioma BCP 47. Uma chave identifica um idioma para o qual o texto pode ser convertido de um script para outro script. O valor associado à chave é um objeto JSON com propriedades que descrevem o idioma e seus scripts com suporte:

  * `name`: exibir o nome do idioma na localidade solicitada por meio do cabeçalho `Accept-Language`.

  * `nativeName`: exibir o nome do idioma na localidade nativa para este idioma.

  * `scripts`: lista de scripts dos quais converter. Cada elemento da lista de `scripts` tem propriedades:

    * `code`: código que identifica o script.

    * `name`: exibir o nome do script na localidade solicitada por meio do cabeçalho `Accept-Language`.

    * `nativeName`: exibir o nome do idioma na localidade nativa para o idioma.

    * `dir`: direcionalidade, que é `rtl` para idiomas da direita para a esquerda ou `ltr` para idiomas da esquerda para a direita.

    * `toScripts`: lista de scripts disponíveis para converter o texto. Cada elemento da lista de `toScripts` tem propriedades `code`, `name`, `nativeName`e `dir`, conforme descrito anteriormente.

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

* Propriedade `dictionary`

  O valor da propriedade `dictionary` é um dicionário de pares (chave, valor). Cada chave é uma marca de idioma BCP 47. A chave identifica um idioma para o qual as traduções alternativas e as traduções traseiras estão disponíveis. O valor é um objeto JSON que descreve o idioma de origem e os idiomas de destino com traduções disponíveis:

  * `name`: exibir o nome do idioma de origem na localidade solicitada por meio do cabeçalho `Accept-Language`.

  * `nativeName`: exibir o nome do idioma na localidade nativa para este idioma.

  * `dir`: direcionalidade, que é `rtl` para idiomas da direita para a esquerda ou `ltr` para idiomas da esquerda para a direita.

  * `translations`: lista de idiomas com traduções alternativa e exemplos para a consulta expressa no idioma de origem. Cada elemento da lista de `translations` tem propriedades:

    * `name`: nome de exibição do idioma de destino na localidade solicitada por meio do cabeçalho `Accept-Language`.

    * `nativeName`: exibir o nome do idioma de destino na localidade nativa para o idioma de destino.

    * `dir`: direcionalidade, que é `rtl` para idiomas da direita para a esquerda ou `ltr` para idiomas da esquerda para a direita.
    
    * `code`: código de idioma que identifica o idioma de destino.

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

A lista de idiomas com suporte não será alterada com frequência. Para economizar largura de banda de rede e melhorar a capacidade de resposta, um aplicativo cliente deve considerar armazenar em cache os recursos de idioma e a marca de entidade correspondente (`ETag`). Em seguida, o aplicativo cliente pode periodicamente (por exemplo, uma vez a cada 24 horas) para consultar o serviço para buscar o conjunto mais recente de idiomas com suporte. Passar o valor de `ETag` atual em um campo de cabeçalho de `If-None-Match` permitirá que o serviço Otimize a resposta. Se o recurso não tiver sido modificado, o serviço retornará o código de status 304 e um corpo de resposta vazio.

## <a name="response-headers"></a>Cabeçalhos de resposta

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>eTag</td>
    <td>Valor atual da marca de entidade para os grupos de idiomas com suporte solicitados. Para tornar as solicitações subsequentes mais eficientes, o cliente pode enviar o valor `ETag` em um campo de cabeçalho `If-None-Match`.
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
  <th width="20%">Código de status</th>
  <th>Descrição</th>
  <tr>
    <td>200</td>
    <td>Êxito.</td>
  </tr>
  <tr>
    <td>304</td>
    <td>O recurso não foi modificado desde a versão especificada pelos cabeçalhos de solicitação `If-None-Match`.</td>
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
    <td>Ocorreu um erro inesperado. Se o erro persistir, denuncie-o com: data e hora da falha, identificador de solicitação do cabeçalho de resposta `X-RequestId`e identificador de cliente do cabeçalho de solicitação `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servidor temporariamente indisponível. Repita a solicitação. Se o erro persistir, denuncie-o com: data e hora da falha, identificador de solicitação do cabeçalho de resposta `X-RequestId`e identificador de cliente do cabeçalho de solicitação `X-ClientTraceId`.</td>
  </tr>
</table> 

Se ocorrer um erro, a solicitação também retornará uma resposta de erro JSON. O código de erro é um número de 6 dígitos que combina o código de status HTTP de 3 dígitos seguido por um número de 3 dígitos para categorizar ainda mais o erro. Códigos de erro comuns podem ser encontrados na [página de referência do API de tradução de texto v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exemplos

O exemplo a seguir mostra como recuperar idiomas com suporte para tradução de texto.

```curl
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```
