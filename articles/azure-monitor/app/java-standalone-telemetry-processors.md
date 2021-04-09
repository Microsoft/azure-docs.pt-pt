---
title: Processadores de telemetria (pré-visualização) - Azure Monitor Application Insights for Java
description: Aprenda a configurar processadores de telemetria em Azure Monitor Application Insights for Java.
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 991e52c13a5730b83552abb6b922d4d7a57c5429
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024120"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Processadores de telemetria (pré-visualização) - Azure Monitor Application Insights for Java

> [!NOTE]
> A funcionalidade de processadores de telemetria está em pré-visualização.

O agente Java 3.0 para Insights de Aplicação pode processar dados de telemetria antes de os dados serem exportados.

Aqui estão alguns casos de utilização para processadores de telemetria:
 * Mascarar dados sensíveis.
 * Adicione sem condições as dimensões personalizadas.
 * Atualize o nome de intervalo, que é usado para agregar telemetria semelhante no portal Azure.
 * Deixe cair os atributos de envergadura específicos para controlar os custos de ingestão.

> [!NOTE]
> Se pretende renunciar a períodos específicos (inteiros) para controlar o custo de ingestão, consulte [sobreposições de amostragem](./java-standalone-sampling-overrides.md).

## <a name="terminology"></a>Terminologia

Antes de aprender sobre processadores de telemetria, deve entender o termo *período*. Um período de tempo é um termo geral para:

* Um pedido de entrada.
* Uma dependência de saída (por exemplo, uma chamada remota para outro serviço).
* Uma dependência em processo (por exemplo, trabalho a ser feito por subcomponentes do serviço).

Para os processadores de telemetria, estes componentes de extensão são importantes:

* Name
* Atributos

O nome de extensão é o principal visor para pedidos e dependências no portal Azure. Os atributos de extensão representam propriedades padrão e personalizadas de um dado pedido ou dependência.

## <a name="telemetry-processor-types"></a>Tipos de processadores de telemetria

Atualmente, os dois tipos de processadores de telemetria são processadores de atributos e processadores de extensão.

Um processador de atributos pode inserir, atualizar, eliminar ou atributos de haxixe.
Também pode usar uma expressão regular para extrair um ou mais novos atributos de um atributo existente.

Um processador de intervalo pode atualizar o nome da telemetria.
Também pode usar uma expressão regular para extrair um ou mais novos atributos do nome de extensão.

> [!NOTE]
> Atualmente, os processadores de telemetria processam apenas atributos de cadeia tipo. Não processam atributos do tipo Boolean ou número.

## <a name="getting-started"></a>Introdução

Para começar, crie um ficheiro de configuração nomeado *applicationinsights.jsem*. Guarde-o no mesmo diretório que *o \* agente-agente de aplicações .jar*. Utilize o modelo seguinte.

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

## <a name="include-criteria-and-exclude-criteria"></a>Incluir critérios e excluir critérios

Tanto os processadores de atributos como os processadores de span suportam `include` critérios e `exclude` critérios opcionais.
Um processador é aplicado apenas a vãos que correspondam aos seus `include` critérios (se for fornecido) _e_ não correspondem aos seus `exclude` critérios (se for fornecido).

Para configurar esta opção, em `include` ou `exclude` em ambos, especifique pelo menos uma `matchType` e qualquer um ou `spanNames` `attributes` .
A configuração de excluir incluído permite mais de uma condição especificada.
Todas as condições especificadas devem avaliar-se de forma verdadeira para resultar em uma correspondência. 

* **Campo obrigatório**: `matchType` controla a interpretação dos itens em `spanNames` matrizes e `attributes` matrizes. Os valores possíveis são `regexp` `strict` e. 

* **Campos opcionais:** 
    * `spanNames` deve corresponder a pelo menos um dos itens. 
    * `attributes` especifica a lista de atributos a condizer. Todos estes atributos devem corresponder exatamente para resultar em uma partida.
    
> [!NOTE]
> Se ambos `include` e `exclude` forem especificados, as `include` propriedades são verificadas antes de as propriedades `exclude` serem verificadas.

### <a name="sample-usage"></a>Utilização de amostras

```json
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
```
Para obter mais informações, consulte [exemplos do processador de Telemetria](./java-standalone-telemetry-processors-examples.md).

## <a name="attribute-processor"></a>Processador de atributos

O processador de atributos modifica atributos de um vão. Pode suportar a capacidade de incluir ou excluir vãos. Requer uma lista de ações que são realizadas na ordem que o ficheiro de configuração especifica. O processador suporta estas ações:

- `insert`
- `update`
- `delete`
- `hash`
- `extract`
### `insert`

A `insert` ação insere um novo atributo em vãos onde a chave já não existe.   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      }
    ]
  }
]
```
A `insert` ação requer as seguintes definições:
* `key`
* Ou `value` ou `fromAttribute`
* `action`: `insert`

### `update`

A `update` ação atualiza um atributo em vãos onde a chave já existe.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      }
    ]
  }
]
```
A `update` ação requer as seguintes definições:
* `key`
* Ou `value` ou `fromAttribute`
* `action`: `update`


### `delete` 

A `delete` ação elimina um atributo de um período de tempo.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      }
    ]
  }
]
```
A `delete` ação requer as seguintes definições:
* `key`
* `action`: `delete`

### `hash`

O `hash` hashes de ação (SHA1) um valor de atributo existente.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      }
    ]
  }
]
```
A `hash` ação requer as seguintes definições:
* `key`
* `action`: `hash`

### `extract`

> [!NOTE]
> A `extract` funcionalidade encontra-se disponível apenas na versão 3.0.2 e posterior.

A `extract` ação extrai valores utilizando uma regra de expressão regular da chave de entrada para as teclas-alvo que a regra especifica. Se uma chave-alvo já existe, está ultrapassada. Esta ação comporta-se como a definição do [processador de envergadura,](#extract-attributes-from-the-span-name) `toAttributes` onde o atributo existente é a fonte.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      }
    ]
  }
]
```
A `extract` ação requer as seguintes definições:
* `key`
* `pattern`
* `action`: `extract`

Para obter mais informações, consulte [exemplos do processador de Telemetria](./java-standalone-telemetry-processors-examples.md).

## <a name="span-processor"></a>Processador de extensão

O processador de envergadura modifica o nome de extensão ou atributos de um vão baseado no nome da extensão. Pode suportar a capacidade de incluir ou excluir vãos.

### <a name="name-a-span"></a>Diga um vão

A `name` secção requer a `fromAttributes` definição. Os valores destes atributos são usados para criar um novo nome, concatenado na ordem que a configuração especifica. O processador só mudará o nome de intervalo se todos estes atributos estiverem presentes no intervalo.

A `separator` definição é opcional. Esta definição é uma corda. Está especificado para valores divididos.
> [!NOTE]
> Se a renomeação depender do processador de atributos para modificar atributos, certifique-se de que o processador de envergadura é especificado após o processador de atributos na especificação do pipeline.

```json
"processors": [
  {
    "type": "span",
    "name": {
      "fromAttributes": [
        "attributeKey1",
        "attributeKey2",
      ],
      "separator": "::"
    }
  }
] 
```

### <a name="extract-attributes-from-the-span-name"></a>Extrair atributos do nome de extensão

A `toAttributes` secção lista as expressões regulares para corresponder ao nome de intervalo. Extrai atributos baseados em subexpressões.

A `rules` definição é necessária. Esta definição lista as regras que são usadas para extrair valores de atributos do nome de intervalo. 

Os valores do nome de intervalo são substituídos por nomes de atributos extraídos. Cada regra da lista é uma expressão regular (regex) cadeia de padrão. 

Eis como os valores são substituídos por nomes de atributos extraídos:

1. O nome da span é verificado contra o regex. 
1. Se o regex corresponder, todas as subexpressões nomeadas do regex são extraídas como atributos. 
1. Os atributos extraídos são adicionados ao vão. 
1. Cada nome de subexpressão torna-se um nome de atributo. 
1. A parte de subexpressão igualada torna-se o valor do atributo. 
1. A parte combinada no nome da extensão é substituída pelo nome do atributo extraído. Se os atributos já existem no espaço, são substituídos. 
 
Este processo é repetido para todas as regras na ordem em que são especificados. Cada regra subsequente funciona no nome de extensão que é a saída da regra anterior.

```json
"processors": [
  {
    "type": "span",
    "name": {
      "toAttributes": {
        "rules": [
          "rule1",
          "rule2",
          "rule3"
        ]
      }
    }
  }
]

```

## <a name="common-span-attributes"></a>Atributos de extensão comum

Esta secção lista alguns atributos de intervalo comum que os processadores de telemetria podem usar.

### <a name="http-spans"></a>Extensões HTTP

| Atributo  | Tipo | Description | 
|---|---|---|
| `http.method` | cadeia (de carateres) | Método de pedido HTTP.|
| `http.url` | string | URL de pedido completo http no formulário `scheme://host[:port]/path?query[#fragment]` . O fragmento não é normalmente transmitido através de HTTP. Mas se o fragmento for conhecido, deve ser incluído.|
| `http.status_code` | número | [Código de estado de resposta HTTP](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | string | Tipo de protocolo HTTP. |
| `http.user_agent` | string | Valor do [cabeçalho http-user-agent](https://tools.ietf.org/html/rfc7231#section-5.5.3) enviado pelo cliente. |

### <a name="jdbc-spans"></a>Extensões JDBC

| Atributo  | Tipo | Description  |
|---|---|---|
| `db.system` | cadeia (de carateres) | Identificador para o sistema de gestão de bases de dados (DBMS) produto que está a ser utilizado. |
| `db.connection_string` | string | Cadeia de ligação usada para ligar à base de dados. É recomendado para remover credenciais embutidas.|
| `db.user` | string | Nome de utilizador para aceder à base de dados. |
| `db.name` | string | O String costumava reportar o nome da base de dados a ser acedida. Para comandos que comutem a base de dados, esta cadeia deve ser definida para a base de dados alvo, mesmo que o comando falhe.|
| `db.statement` | string | Declaração de base de dados que está a ser executada.|
