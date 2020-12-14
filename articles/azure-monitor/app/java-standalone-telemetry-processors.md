---
title: Processadores de telemetria (pré-visualização) - Azure Monitor Application Insights for Java
description: Como configurar processadores de telemetria em Azure Monitor Application Insights for Java
ms.topic: conceptual
ms.date: 10/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 7fd53c77b64e028ffad25c8fa7a9eefd95439513
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387161"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Processadores de telemetria (pré-visualização) - Azure Monitor Application Insights for Java

> [!NOTE]
> Esta funcionalidade ainda se encontra em pré-visualização.

Java 3.0 Agente para Insights de Aplicação tem agora a capacidade de processar dados de telemetria antes de os dados serem exportados.

### <a name="some-use-cases"></a>Alguns usam casos:
 * Mascarar dados sensíveis
 * Adicionar dimensões personalizadas condicionalmente
 * Atualizar o nome da telemetria utilizado para agregação e exibição

### <a name="supported-processors"></a>Processadores suportados:
 * Processador de atributos
 * Processador de vão

## <a name="to-get-started"></a>Para começar

Crie um ficheiro de configuração chamado `applicationinsights.json` , e coloque-o no mesmo diretório `applicationinsights-agent-***.jar` que , com o seguinte modelo.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="includeexclude-spans"></a>Incluir/excluir vãos

O processador de atributos e o processador de envergadura expõem a opção de fornecer um conjunto de propriedades de um período de tempo a combinar, para determinar se o intervalo deve ser incluído ou excluído do processador. Para configurar esta opção, em `include` baixo e/ou `exclude` pelo menos um e um de ou é `matchType` `spanNames` `attributes` necessário. A configuração de incluir/excluir é suportada para ter mais de uma condição especificada. Todas as condições especificadas devem avaliar a verdade para que ocorra uma correspondência. 

**Campo obrigatório:** 
* `matchType` controla a forma como os itens `spanNames` e `attributes` matrizes são interpretados. Os valores possíveis são `regexp` ou `strict`. 

**Campos opcionais:** 
* `spanNames` deve corresponder a pelo menos um dos itens. 
* `attributes` especifica a lista de atributos a condizer. Todos estes atributos devem corresponder exatamente para que um jogo ocorra.

> [!NOTE]
> Se ambos `include` e `exclude` forem especificados, as `include` propriedades são verificadas antes das `exclude` propriedades.

#### <a name="sample-usage"></a>Utilização de amostras

O seguinte demonstra especificar o conjunto de propriedades de vão para indicar a que extensões este processador deve ser aplicado. As `include` propriedades dizem quais devem ser incluídas e as propriedades `exclude` filtram ainda mais os vãos que não devem ser processados.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "svcA",
            "svcB"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "attributes": [
            {
              "key": "redact_trace",
              "value": "false"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "duplicate_key",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

Com a configuração acima, aplicam-se as seguintes extensões que correspondem às propriedades e às ações do processador:

* Span1 Nome: 'svcB' Atributos: {env: produção, test_request: 123, credit_card: 1234, redact_trace: "falso"}

* Nome span2: 'svcA' Atributos: {env: encenação, test_request: falso, redact_trace: verdadeiro}

Não são aplicadas as seguintes extensões que não correspondem às propriedades incluídas e as ações do processador não são aplicadas:

* Nome Span3: 'svcB' Atributos: {env: produção, test_request: verdadeiro, credit_card: 1234, redact_trace: falso}

* Nome Span4: 'svcC' Atributos: {env: dev, test_request: falso}

## <a name="attribute-processor"></a>Processador de atributos 

O processador de atributos modifica atributos de um vão. Suporta opcionalmente a capacidade de incluir/excluir vãos.
Requer uma lista de ações que são realizadas por ordem especificada no ficheiro de configuração. As ações apoiadas são:

* `insert` : Insere um novo atributo em vãos onde a chave já não existe
* `update` : Atualiza um atributo em vãos onde a chave existe
* `delete` : Elimina um atributo de um vão
* `hash`   : Hashes (SHA1) um valor de atributo existente

Para as ações `insert` e `update`
* `key` é necessário
* um de `value` ou `fromAttribute` é necessário
* `action` é obrigatório.

Para a `delete` ação,
* `key` é necessário
* `action`: `delete` é necessário.

Para a `hash` ação,
* `key` é necessário
* `action` : `hash` é necessário.

A lista de ações pode ser composta para criar cenários ricos, como atributo de enchimento traseiro, copiando valores para uma nova chave, redirecionando informação sensível.

#### <a name="sample-usage"></a>Utilização de amostras

O exemplo a seguir demonstra a inserção de chaves/valores em vãos:

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "value1",
            "action": "insert"
          },
          {
            "key": "key1",
            "fromAttribute": "anotherkey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

O exemplo a seguir demonstra a configuração do processador para atualizar apenas as teclas existentes num atributo:

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "piiattribute",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

O exemplo a seguir demonstra como processar vãos que têm um nome de extensão que combina com padrões de regexp.
Este processador removerá o atributo "token" e obstaculizará o atributo "password" em vãos onde o nome de intervalo corresponde a \* "auth". e onde o nome de intervalo não corresponde a \* "login".

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

## <a name="span-processors"></a>Processadores de vãos

O processador de envergadura modifica o nome de extensão ou atributos de um vão baseado no nome da extensão. Suporta opcionalmente a capacidade de incluir/excluir vãos.

### <a name="name-a-span"></a>Diga um vão

É necessária a seguinte definição como parte da secção de nomes:

* `fromAttributes`: O valor de atributo para as teclas é utilizado para criar um novo nome na ordem especificada na configuração. Todas as teclas de atributos devem ser especificadas no intervalo para que o processador o rebatize.

A seguinte definição pode ser configurada opcionalmente:

* `separator`: Uma corda, que é especificada, será usada para dividir valores
> [!NOTE]
> Se o renomeamento depender da modificação dos atributos pelos atributos do processador, certifique-se de que o processador de envergadura é especificado após o processador de atributos na especificação do pipeline.

#### <a name="sample-usage"></a>Utilização de amostras

O exemplo a seguir especifica os valores do atributo "db.svc", "operação" e "id" formarão o novo nome do vão, por essa ordem, separados pelo valor "::".
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name"></a>Extrair atributos do nome de extensão

Pega numa lista de expressões regulares para combinar o nome de extensão com o do mesmo e extrair atributos do mesmo com base em subexpressões. Deve ser especificado na `toAttributes` secção.

São necessárias as seguintes definições:

`rules` : Uma lista de regras para extrair valores de atributos do nome de intervalo. Os valores do nome de intervalo são substituídos por nomes de atributos extraídos. Cada regra da lista é a cadeia de padrão regex. O nome do span é verificado contra o regex. Se o regex corresponder, todas as subexpressões nomeadas do regex são extraídas como atributos e adicionadas ao intervalo. Cada nome de subexpressão torna-se um nome de atributo e a porção de subexpressão corresponde ao valor do atributo. A parte combinada no nome da extensão é substituída pelo nome de atributo extraído. Se os atributos já existirem no espaço, serão substituídos. O processo é repetido para todas as regras na ordem em que são especificadas. Cada regra subsequente funciona com o nome de intervalo que é a saída após o processamento da regra anterior.

#### <a name="sample-usage"></a>Utilização de amostras

Vamos assumir que o nome da extensão de entrada é /api/v1/document/12345678/update. A aplicação dos seguintes resultados no nome de intervalo de saída /api/v1/document/{documentId}/atualização adicionará um novo atributo "documentId"="12345678" ao intervalo.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

O seguinte demonstra renomear o nome de intervalo para "{operation_website}" e adicionar o atributo {Key: operation_website, Valor: oldSpanName } quando o intervalo tem as seguintes propriedades:
- O nome da extensão contém '/' em qualquer lugar da corda.
- O nome da span não é "não não/mudança".
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```