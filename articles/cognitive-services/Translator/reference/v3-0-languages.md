---
title: Método das Línguas tradutoras
titleSuffix: Azure Cognitive Services
description: O método Das Línguas recebe o conjunto de línguas atualmente suportadas por outras operações do Tradutor.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 1f4dfc4b80aff01e4b7fe7ebae4850b28cd6a498
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588603"
---
# <a name="translator-30-languages"></a>Tradutor 3.0: Línguas

Obtém o conjunto de línguas atualmente apoiadas por outras operações do Tradutor. 

## <a name="request-url"></a>URL do Pedido

Envie um `GET` pedido para:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros de pedido

Os parâmetros de pedido transmitidos na corda de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>api-version</td>
    <td><em>Parâmetro necessário</em>.<br/>Versão da API solicitada pelo cliente. O valor deve `3.0` ser.</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Parâmetro opcional*.<br/>Uma lista separada de nomes que definem o grupo de línguas para regressar. Os nomes de grupo permitidos são: `translation` e `transliteration` `dictionary` . Se não for dado qualquer margem de manobra, todos os grupos são devolvidos, o que equivale a passagem `scope=translation,transliteration,dictionary` . Para decidir qual o conjunto de línguas suportadas adequada para o seu cenário, consulte a descrição do objeto de [resposta](#response-body).</td>
  </tr>
</table> 

Os cabeçalhos de pedido são:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>Accept-Language</td>
    <td>*Cabeçalho de pedido opcional*.<br/>O idioma a utilizar nas cadeias da interface de utilizador. Alguns dos campos na resposta são nomes de línguas ou nomes de regiões. Utilize este parâmetro para definir a linguagem em que estes nomes são devolvidos. A língua é especificada através da disponibilização de uma etiqueta de língua saneada DO BCP 47. Por exemplo, use o valor `fr` para solicitar nomes em francês ou use o valor `zh-Hant` para solicitar nomes em chinês tradicional.<br/>Os nomes são fornecidos na língua inglesa quando uma língua-alvo não é especificada ou quando a localização não está disponível.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceid</td>
    <td>*Cabeçalho de pedido opcional*.<br/>Um GUID gerado pelo cliente para identificar exclusivamente o pedido.</td>
  </tr>
</table> 

A autenticação não é necessária para obter recursos linguísticos.

## <a name="response-body"></a>Corpo da resposta

Um cliente usa o parâmetro de `scope` consulta para definir em que grupos de línguas está interessado.

* `scope=translation`fornece línguas suportadas para traduzir texto de uma língua para outra;

* `scope=transliteration`fornece capacidades para converter texto numa língua de um script para outro script;

* `scope=dictionary`fornece pares de idiomas para os quais `Dictionary` as operações devolvem dados.

Um cliente pode recuperar vários grupos simultaneamente especificando uma lista separada de nomes. Por exemplo, `scope=translation,transliteration,dictionary` devolveria as línguas apoiadas para todos os grupos.

Uma resposta bem sucedida é um objeto JSON com uma propriedade para cada grupo solicitado:

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

O valor para cada imóvel é o seguinte.

* `translation`propriedade

  O valor da `translation` propriedade é um dicionário de pares (chave, valor). Cada chave é uma etiqueta de língua BCP 47. Uma chave identifica uma língua para a qual o texto pode ser traduzido ou traduzido. O valor associado à chave é um objeto JSON com propriedades que descrevem a língua:

  * `name`: Mostrar o nome da língua no local solicitado através do `Accept-Language` cabeçalho.

  * `nativeName`: Mostrar o nome da língua no local nativo para esta língua.

  * `dir`: Direccionalidade, que é `rtl` para línguas da direita para a esquerda ou `ltr` para línguas da esquerda para a direita.

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

* `transliteration`propriedade

  O valor da `transliteration` propriedade é um dicionário de pares (chave, valor). Cada chave é uma etiqueta de língua BCP 47. Uma chave identifica um idioma para o qual o texto pode ser convertido de um script para outro script. O valor associado à chave é um objeto JSON com propriedades que descrevem a linguagem e os seus scripts suportados:

  * `name`: Mostrar o nome da língua no local solicitado através do `Accept-Language` cabeçalho.

  * `nativeName`: Mostrar o nome da língua no local nativo para esta língua.

  * `scripts`: Lista de scripts para converter. Cada elemento da `scripts` lista tem propriedades:

    * `code`: Código que identifica o guião.

    * `name`: Mostrar o nome do guião no local solicitado através do `Accept-Language` cabeçalho.

    * `nativeName`: Mostrar o nome da língua no local nativo para a língua.

    * `dir`: Direccionalidade, que é `rtl` para línguas da direita para a esquerda ou `ltr` para línguas da esquerda para a direita.

    * `toScripts`: Lista de scripts disponíveis para converter texto para. Cada elemento da `toScripts` lista tem `code` `name` propriedades, e como descrito `nativeName` `dir` anteriormente.

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

* `dictionary`propriedade

  O valor da `dictionary` propriedade é um dicionário de pares (chave, valor). Cada chave é uma etiqueta de língua BCP 47. A chave identifica um idioma para o qual existem traduções alternativas e traduções traseiras. O valor é um objeto JSON que descreve a linguagem fonte e as línguas-alvo com traduções disponíveis:

  * `name`: Mostrar o nome da língua de origem no local solicitado através do `Accept-Language` cabeçalho.

  * `nativeName`: Mostrar o nome da língua no local nativo para esta língua.

  * `dir`: Direccionalidade, que é `rtl` para línguas da direita para a esquerda ou `ltr` para línguas da esquerda para a direita.

  * `translations`: Lista de línguas com traduções alterativas e exemplos para a consulta expressa na língua-fonte. Cada elemento da `translations` lista tem propriedades:

    * `name`: Mostrar o nome da língua-alvo no local solicitado através do `Accept-Language` cabeçalho.

    * `nativeName`: Mostrar o nome da língua-alvo no local nativo para a língua-alvo.

    * `dir`: Direccionalidade, que é `rtl` para línguas da direita para a esquerda ou `ltr` para línguas da esquerda para a direita.
    
    * `code`: Código de idioma que identifica a linguagem alvo.

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

A estrutura do objeto de resposta não mudará sem uma alteração na versão da API. Para a mesma versão da API, a lista de idiomas disponíveis pode mudar ao longo do tempo, uma vez que o Microsoft Tradutor alarga continuamente a lista de idiomas suportados pelos seus serviços.

A lista de línguas apoiadas não mudará frequentemente. Para poupar a largura de banda da rede e melhorar a capacidade de resposta, uma aplicação do cliente deve considerar os recursos linguísticos de cache e a etiqueta correspondente da entidade `ETag` ( ). Em seguida, a aplicação do cliente pode periodicamente (por exemplo, uma vez a cada 24 horas) consultar o serviço para obter o mais recente conjunto de idiomas suportados. Passar o valor atual num campo de `ETag` `If-None-Match` cabeçalho permitirá ao serviço otimizar a resposta. Se o recurso não tiver sido modificado, o serviço devolverá o código de estado 304 e um organismo de resposta vazio.

## <a name="response-headers"></a>Cabeçalhos de resposta

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>ETag</td>
    <td>Valor atual da etiqueta da entidade para os grupos solicitados de línguas apoiadas. Para tornar os pedidos subsequentes mais eficientes, o cliente pode enviar o valor num campo de `ETag` `If-None-Match` cabeçalho.
    </td>
  </tr>
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
    <td>304</td>
    <td>O recurso não foi modificado desde a versão especificada por cabeçalhos de pedido `If-None-Match` .</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Um dos parâmetros de consulta está em falta ou não é válido. Corretos parâmetros de pedido antes de tentar novamente.</td>
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

O exemplo que se segue mostra como recuperar línguas suportadas para tradução de texto.

```curl
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```
