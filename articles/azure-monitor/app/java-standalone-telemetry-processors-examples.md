---
title: Exemplos de processadores de telemetria - Azure Monitor Application Insights for Java
description: Explore exemplos que mostram processadores de telemetria em Azure Monitor Application Insights for Java.
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 0978bd669855d264ed6dfa5eeddc45ad499aa2a5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734592"
---
# <a name="telemetry-processor-examples---azure-monitor-application-insights-for-java"></a>Exemplos de processadores de telemetria - Azure Monitor Application Insights for Java

Este artigo fornece exemplos de processadores de telemetria em Application Insights for Java. Encontrará amostras para incluir e excluir configurações. Também encontrará amostras para processadores de atributos e processadores de extensão.
## <a name="include-and-exclude-samples"></a>Incluir e excluir amostras

Nesta secção, você verá como incluir e excluir vãos. Você também verá como excluir vários vãos e aplicar processamento seletivo.
### <a name="include-spans"></a>Incluir vãos

Esta secção mostra como incluir os vãos para um processador de atributos. Os vãos que não correspondem às propriedades não são processados pelo processador.

Uma correspondência requer que o nome de intervalo seja igual a `spanA` ou `spanB` . 

Estes vãos correspondem às propriedades incluídas, e as ações do processador são aplicadas:
* Nome span1: 'spanA' Atributos: {env: dev, test_request: 123, credit_card: 1234}
* Nome Span2: 'spanB' Atributos: {env: dev, test_request: falso}
* Nome Span3: 'spanA' Atributos: {env: 1, test_request: dev, credit_card: 1234}

Este vão não corresponde às propriedades incluídas, e as ações do processador não são aplicadas:
* Nome Span4: 'spanC' Atributos: {env: dev, test_request: falso}

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
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="exclude-spans"></a>Excluir vãos

Esta secção demonstra como excluir os intervalos para um processador de atributos. Os vãos que correspondem às propriedades não são processados por este processador.

Uma correspondência requer que o nome de intervalo seja igual a `spanA` ou `spanB` .

Os períodos seguintes correspondem às propriedades de exclusão, e as ações do processador não são aplicadas:
* Nome span1: 'spanA' Atributos: {env: dev, test_request: 123, credit_card: 1234}
* Nome Span2: 'spanB' Atributos: {env: dev, test_request: falso}
* Nome Span3: 'spanA' Atributos: {env: 1, test_request: dev, credit_card: 1234}

Este vão não corresponde às propriedades de exclusão, e as ações do processador são aplicadas:
* Nome Span4: 'spanC' Atributos: {env: dev, test_request: falso}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="exclude-spans-by-using-multiple-criteria"></a>Excluir os vãos utilizando vários critérios

Esta secção demonstra como excluir os intervalos para um processador de atributos. Os vãos que correspondem às propriedades não são processados por este processador.

Uma partida requer que sejam cumpridas as seguintes condições:
* Um atributo (por exemplo, `env` ou ) deve existir no `dev` espaço.
* O vão deve ter um atributo que tenha chave `test_request` .

Os períodos seguintes correspondem às propriedades de exclusão, e as ações do processador não são aplicadas.
* Nome span1: 'spanB' Atributos: {env: dev, test_request: 123, credit_card: 1234}
* Nome Span2: 'spanA' Atributos: {env: dev, test_request: falso}

O período seguinte não corresponde às propriedades de exclusão, e as ações do processador são aplicadas:
* Nome Span3: 'spanB' Atributos: {env: 1, test_request: dev, credit_card: 1234}
* Nome Span4: 'spanC' Atributos: {env: dev, dev_request: falso}


```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ],
          "attributes": [
            {
              "key": "env",
              "value": "dev"
            },
            {
              "key": "test_request"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="selective-processing"></a>Processamento seletivo

Esta secção mostra como especificar o conjunto de propriedades de envergadura que indicam quais os intervalos a que este processador deve ser aplicado. As propriedades incluem indicam quais os vãos que devem ser processados. As propriedades de exclusão filtram períodos que não devem ser processados.

Na seguinte configuração, estes vãos correspondem às propriedades e aplicam-se as ações do processador:

* Span1 Nome: 'spanB' Atributos: {env: produção, test_request: 123, credit_card: 1234, redact_trace: "falso"}
* Nome span2: 'spanA' Atributos: {env: encenação, test_request: falso, redact_trace: verdadeiro}

Estes vãos não correspondem às propriedades incluídas, e as ações do processador não são aplicadas:
* Nome Span3: 'spanB' Atributos: {env: produção, test_request: verdadeiro, credit_card: 1234, redact_trace: falso}
* Nome Span4: 'spanC' Atributos: {env: dev, test_request: falso}

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
            "spanA",
            "spanB"
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
## <a name="attribute-processor-samples"></a>Atribuir amostras de processador

### <a name="insert"></a>Inserir

A amostra seguinte insere o novo atributo `{"attribute1": "attributeValue1"}` em vãos onde a chave `attribute1` não existe.

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
            "value": "attributeValue1",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="insert-from-another-key"></a>Inserir a partir de outra chave

A amostra a seguir utiliza o valor do atributo `anotherkey` para inserir o novo atributo em `{"newKey": "<value from attribute anotherkey>"}` vãos onde a chave não `newKey` existe. Se o atributo `anotherkey` não existir, nenhum novo atributo é inserido em vãos.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "newKey",
            "fromAttribute": "anotherKey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="update"></a>Atualizar

A amostra a seguir atualiza o atributo a `{"db.secret": "redacted"}` . Atualiza o atributo `boo` utilizando o valor do atributo `foo` . Os vãos que não têm o atributo `boo` não mudam.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "db.secret",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "boo",
            "fromAttribute": "foo",
            "action": "update" 
          }
        ]
      }
    ]
  }
}
```

### <a name="delete"></a>Eliminar

A amostra que se segue mostra como eliminar um atributo que tem a chave `credit_card` .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="hash"></a>Hash

A amostra que se segue mostra como hash valores de atributos existentes.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
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

### <a name="extract"></a>Extrair

A amostra que se segue mostra como usar uma expressão regular (regex) para criar novos atributos com base no valor de outro atributo.
Por exemplo, `http.url = http://example.com/path?queryParam1=value1,queryParam2=value2` dado, os seguintes atributos são inseridos:
* httpProtocol: `http`
* httpDomain: `example.com`
* httpPata: `path`
* httpQueryParams: `queryParam1=value1,queryParam2=value2`
* http.url: *sem* alteração

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "http.url",
            "pattern": "^(?<httpProtocol>.*):\\/\\/(?<httpDomain>.*)\\/(?<httpPath>.*)(\\?|\\&)(?<httpQueryParams>.*)",
            "action": "extract"
          }
        ]
      }
    ]
  }
}
```

A amostra que se segue mostra como processar vãos que têm um nome de extensão que corresponde aos padrões regex.
Este processador remove o `token` atributo. Obstace o `password` atributo em vãos onde o nome de intervalo corresponde `auth.*` e onde o nome do intervalo não corresponde `login.*` .

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


## <a name="span-processor-samples"></a>Amostras de processador de esverda

### <a name="name-a-span"></a>Diga um vão

A amostra que se segue especifica os valores dos `db.svc` `operation` atributos, e `id` . Forma o novo nome do vão utilizando esses atributos, por essa ordem, separados pelo valor `::` .
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

### <a name="extract-attributes-from-a-span-name"></a>Atributos de extrato de um nome de extensão

Vamos supor que o nome da extensão de entrada `/api/v1/document/12345678/update` é. A amostra que se segue resulta no nome do intervalo de saída `/api/v1/document/{documentId}/update` . Adiciona o novo atributo `documentId=12345678` ao vão.
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

### <a name="extract-attributes-from-a-span-name-by-using-include-and-exclude"></a>Extrair atributos de um nome de intervalo usando incluir e excluir

A amostra que se segue mostra como alterar o nome de intervalo para `{operation_website}` . Adiciona um atributo com chave `operation_website` e valor quando o vão tem as `{oldSpanName}` seguintes propriedades:
- O nome da extensão contém `/` qualquer lugar na corda.
- O nome do span não `donot/change` é.
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
