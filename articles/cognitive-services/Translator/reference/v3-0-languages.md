---
title: Método de idiomas de API de texto do tradutor
titlesuffix: Azure Cognitive Services
description: Use o método de idiomas de API de texto do Translator.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: 45baf915ffbc2723d3ed5b4c4dcd96b583a12802
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58122408"
---
# <a name="translator-text-api-30-languages"></a>Texto do Translator API 3.0: Languages

Obtém o conjunto de idiomas atualmente suportados pelo outras operações de API de texto do Translator. 

## <a name="request-url"></a>URL do Pedido

Enviar um `GET` pedido para:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros do pedido

Parâmetros de pedido passados na seqüência de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>versão de API</td>
    <td><em>Parâmetro necessário</em>.<br/>Versão da API do pedido pelo cliente. Valor tem de ser `3.0`.</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*O parâmetro opcional*.<br/>Uma lista separada por vírgulas de nomes de definir o grupo de idiomas para retornar. Permitidos os nomes de grupos são: `translation`, `transliteration` e `dictionary`. Se nenhum âmbito é fornecido, em seguida, todos os grupos são devolvidos, que é equivalente a passagem `scope=translation,transliteration,dictionary`. Para decidir qual conjunto de idiomas suportados é adequado para o seu cenário, consulte a descrição dos [objeto response](#response-body).</td>
  </tr>
</table> 

Cabeçalhos de pedido são:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>Accept-Language</td>
    <td>*Cabeçalho do pedido opcional*.<br/>O idioma a utilizar nas cadeias da interface de utilizador. Alguns dos campos na resposta são nomes de idiomas ou nomes de regiões. Utilize este parâmetro para definir o idioma em que esses nomes são devolvidos. O idioma é especificado ao fornecer uma etiqueta de idioma do BCP 47 bem formada. Por exemplo, utilize o valor `fr` para pedir nomes em francês ou utilizar o valor `zh-Hant` para nomes do pedido em chinês tradicional.<br/>Os nomes são fornecidos em inglês quando não for especificado um idioma de destino ou quando a localização não está disponível.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Cabeçalho do pedido opcional*.<br/>Um GUID gerado pelo cliente para identificar exclusivamente o pedido.</td>
  </tr>
</table> 

Autenticação não é necessário para obter recursos de idioma.

## <a name="response-body"></a>Corpo da resposta

Um cliente utiliza o `scope` parâmetro para definir a quais grupos de idiomas está interessada na consulta.

* `scope=translation` Fornece os idiomas suportados para traduzir texto de um idioma para outro idioma;

* `scope=transliteration` Fornece capacidades para a conversão de texto num idioma para outro script; a partir de um script

* `scope=dictionary` Fornece os pares de idiomas para os quais `Dictionary` operações retornam dados.

Um cliente pode obter vários grupos em simultâneo ao especificar uma lista separada por vírgulas de nomes. Por exemplo, `scope=translation,transliteration,dictionary` retornaria idiomas suportados para todos os grupos.

Uma resposta com êxito é um objeto JSON com uma propriedade para cada grupo pedida:

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

Segue-se o valor para cada propriedade.

* `translation` Propriedade

  O valor da `translation` propriedade é um dicionário de valor (chave) pares. Cada chave é uma etiqueta de idioma de BCP 47. Uma chave identifica um idioma para o qual texto pode ser convertido para ou convertido do. O valor associado à chave é um objeto JSON com propriedades que descrevem o idioma:

  * `name`: Nome a apresentar da linguagem na localidade solicitada via `Accept-Language` cabeçalho.

  * `nativeName`: Nome a apresentar da linguagem no idioma nativo para este idioma.

  * `dir`: Direcionalidade, que é `rtl` para idiomas da direita para esquerda ou `ltr` para idiomas do esquerda para a direita.

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

* `transliteration` Propriedade

  O valor da `transliteration` propriedade é um dicionário de valor (chave) pares. Cada chave é uma etiqueta de idioma de BCP 47. Uma chave identifica um idioma para o qual pode ser convertido texto a partir de um script para outro script. O valor associado à chave é um objeto JSON com propriedades que descrevem a linguagem e seus scripts com suporte:

  * `name`: Nome a apresentar da linguagem na localidade solicitada via `Accept-Language` cabeçalho.

  * `nativeName`: Nome a apresentar da linguagem no idioma nativo para este idioma.

  * `scripts`: Lista de scripts para converter. Cada elemento do `scripts` lista tem propriedades:

    * `code`: Identificar o script de código.

    * `name`: Nome a apresentar do script na localidade solicitada via `Accept-Language` cabeçalho.

    * `nativeName`: Nome do idioma a apresentar no idioma nativo do idioma.

    * `dir`: Direcionalidade, que é `rtl` para idiomas da direita para esquerda ou `ltr` para idiomas do esquerda para a direita.

    * `toScripts`: Lista de scripts disponíveis para converter o texto a. Cada elemento dos `toScripts` lista tem propriedades `code`, `name`, `nativeName`, e `dir` conforme descrito anteriormente.

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

* `dictionary` Propriedade

  O valor da `dictionary` propriedade é um dicionário de valor (chave) pares. Cada chave é uma etiqueta de idioma de BCP 47. A chave identifica um idioma para o qual traduções alternativas e back-traduções estão disponíveis. O valor é um objeto JSON que descreve a linguagem de código e os idiomas de destino com traduções disponíveis:

  * `name`: Nome a apresentar do idioma de origem na localidade solicitada via `Accept-Language` cabeçalho.

  * `nativeName`: Nome a apresentar da linguagem no idioma nativo para este idioma.

  * `dir`: Direcionalidade, que é `rtl` para idiomas da direita para esquerda ou `ltr` para idiomas do esquerda para a direita.

  * `translations`: Lista de idiomas com traduções alterative e exemplos para a consulta expressado no idioma de origem. Cada elemento do `translations` lista tem propriedades:

    * `name`: Nome a apresentar do idioma de destino na localidade solicitada via `Accept-Language` cabeçalho.

    * `nativeName`: Nome a apresentar do idioma de destino no idioma nativo para o idioma de destino.

    * `dir`: Direcionalidade, que é `rtl` para idiomas da direita para esquerda ou `ltr` para idiomas do esquerda para a direita.
    
    * `code`: Código de idioma, identificando o idioma de destino.

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

A estrutura do objeto de resposta não será alterado sem uma alteração na versão da API. Para a mesma versão da API, a lista de idiomas de disponíveis pode mudar ao longo do tempo, como Microsoft Translator continuamente estende a lista de idiomas suportados pelo seus serviços.

A lista de idiomas suportados não serão alterados com frequência. Para poupar largura de banda de rede e melhorar a capacidade de resposta, um aplicativo cliente deve considerar a colocação em cache recursos de idioma e da etiqueta de entidade correspondente (`ETag`). Em seguida, a aplicação cliente pode periodicamente (por exemplo, uma vez a cada 24 horas) consulta o serviço para obter o mais recente conjunto de idiomas suportados. Passando o atual `ETag` valor numa `If-None-Match` campo de cabeçalho permitirá que o serviço otimizar a resposta. Se o recurso não foi modificado, o serviço irá devolver o código de estado 304 e um corpo de resposta vazia.

## <a name="response-headers"></a>Cabeçalhos de resposta

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>ETag</td>
    <td>Valor atual da etiqueta de entidade para os grupos de pedido de idiomas suportados. Para tornar as solicitações subseqüentes mais eficiente, o cliente pode enviar os `ETag` valor numa `If-None-Match` campo de cabeçalho.
    </td>
  </tr>
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
    <td>304</td>
    <td>O recurso não foi modificado desde a versão especificada por cabeçalhos de pedido `If-None-Match`.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Um dos parâmetros de consulta está em falta ou não é válido. Corrija os parâmetros de pedido antes de tentar novamente.</td>
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

O exemplo seguinte mostra como obter os idiomas suportados para a tradução de texto.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```

---
