---
title: Exemplos de processadores de telemetria - Azure Monitor Application Insights for Java
description: Exemplos ilustrando processadores de telemetria em Azure Monitor Application Insights for Java
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: b9ad5347e146fc94b513180c591b00c4f449619f
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98146461"
---
# <a name="telemetry-processors-examples---azure-monitor-application-insights-for-java"></a>Exemplos de processadores de telemetria - Azure Monitor Application Insights for Java

## <a name="includeexclude-samples"></a>Incluir/Excluir amostras

### <a name="1-include-spans"></a>1. Incluir vãos

O seguinte demonstra incluir vãos para este processador de atributos. Todos os outros vãos que não correspondem às propriedades não são processados por este processador.

São reunidas as condições para uma partida:
* O nome do intervalo deve ser igual a "spanA" ou "spanB" 

Seguem-se os vãos que correspondem às propriedades incluídas e as ações do processador são aplicadas.
* Nome span1: 'spanA' Atributos: {env: dev, test_request: 123, credit_card: 1234}
* Nome Span2: 'spanB' Atributos: {env: dev, test_request: falso}
* Nome Span3: 'spanA' Atributos: {env: 1, test_request: dev, credit_card: 1234}

O intervalo seguinte não corresponde às propriedades incluídas e as ações do processador não são aplicadas.
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

### <a name="2-exclude-spans"></a>2. Excluir vãos

O seguinte demonstra a exclusão de períodos para este processador de atributos. Todos os períodos que correspondem às propriedades não são processados por este processador.

São reunidas as condições para uma partida:
* O nome do intervalo deve ser igual a "spanA" ou "spanB" 

Seguem-se os vãos que correspondem às propriedades de exclusão e as ações do processador não são aplicadas.
* Nome span1: 'spanA' Atributos: {env: dev, test_request: 123, credit_card: 1234}
* Nome Span2: 'spanB' Atributos: {env: dev, test_request: falso}
* Nome Span3: 'spanA' Atributos: {env: 1, test_request: dev, credit_card: 1234}

O intervalo a seguir não corresponde às propriedades de exclusão e aplicam-se as ações do processador.
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

### <a name="3-excludemulti-spans"></a>3. Excluir ExtensõesMulti

O seguinte demonstra a exclusão de períodos para este processador de atributos. Todos os períodos que correspondem às propriedades não são processados por este processador.

São reunidas as condições para uma partida:
* Um atributo ('env', 'dev') deve existir no intervalo para uma partida.
* Enquanto houver um atributo com chave 'test_request' no espaço, há um fósforo.

Seguem-se os vãos que correspondem às propriedades de exclusão e as ações do processador não são aplicadas.
* Nome span1: 'spanB' Atributos: {env: dev, test_request: 123, credit_card: 1234}
* Nome Span2: 'spanA' Atributos: {env: dev, test_request: falso}

O intervalo a seguir não corresponde às propriedades de exclusão e aplicam-se as ações do processador.
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

### <a name="4-selective-processing"></a>4. Processamento seletivo

O seguinte demonstra especificar o conjunto de propriedades de vão para indicar a que extensões este processador deve ser aplicado. As `include` propriedades dizem quais devem ser incluídas e as propriedades `exclude` filtram ainda mais os vãos que não devem ser processados.

Com a configuração abaixo, aplicam-se as seguintes extensões que correspondem às propriedades e às ações do processador:

* Span1 Nome: 'spanB' Atributos: {env: produção, test_request: 123, credit_card: 1234, redact_trace: "falso"}
* Nome span2: 'spanA' Atributos: {env: encenação, test_request: falso, redact_trace: verdadeiro}

Não são aplicadas as seguintes extensões que não correspondem às propriedades incluídas e as ações do processador não são aplicadas:
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
## <a name="attribute-processor-samples"></a>Amostras de processador de atributos

### <a name="insert"></a>Inserir

O seguinte insere um novo atributo {"atributo1": "atributoValue1"} para extensões onde a chave "atributo1" não existe.

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

O seguinte utiliza o valor do atributo "outra chave" para inserir um novo atributo {"newKey": "valor do atributo 'outra tecla'} para os intervalos onde a chave "newKey" não existe. Se o atributo 'outra chave' não existir, nenhum novo atributo é inserido em vãos.

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

As seguintes atualizações do atributo a { "db.secret": "redacted"} e atualiza o atributo 'boo' utilizando o valor do atributo 'foo'. Os vãos sem o atributo 'boo' não vão mudar.

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

O seguinte demonstra a eliminação do atributo com a chave 'credit_card'.

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

O seguinte demonstra os valores de atributos existentes.

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

O exemplo a seguir demonstra a utilização do Regex para criar novos atributos baseados no valor de outro atributo.
Por exemplo, dado http.url = http://example.com/path?queryParam1=value1 ,consultaParam2=valor2 serão inseridos os seguintes atributos:
* httpProtocol: http
* httpDomain: example.com
* httpPath: caminho
* httpQueryParams: consultaParam1=value1,queryParam2=value2
* http.url valor NÃO muda.

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


## <a name="span-processor-samples"></a>Amostras de processador de extensão

### <a name="name-a-span"></a>Diga um vão

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

### <a name="extract-attributes-from-span-name-with-include-and-exclude"></a>Extrair atributos do nome de intervalo com incluir e excluir

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