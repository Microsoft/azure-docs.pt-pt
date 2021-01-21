---
title: Processadores de telemetria (pré-visualização) - Azure Monitor Application Insights for Java
description: Como configurar processadores de telemetria em Azure Monitor Application Insights for Java
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: c0745dd4069c64292fbcaef666d843ae2d25f7b3
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632585"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Processadores de telemetria (pré-visualização) - Azure Monitor Application Insights for Java

> [!NOTE]
> Esta funcionalidade ainda se encontra em pré-visualização.

Java 3.0 Agente para Insights de Aplicação tem agora a capacidade de processar dados de telemetria antes de os dados serem exportados.

Seguem-se alguns casos de utilização de processadores de telemetria:
 * Mascarar dados sensíveis
 * Adicionar dimensões personalizadas condicionalmente
 * Atualize o nome que é usado para agregação e exibição no portal Azure
 * Desatenção de atributos de extensão para controlar o custo de ingestão

## <a name="terminology"></a>Terminologia

Antes de entrarmos em processadores de telemetria, é importante entender a que se refere o termo.

Um período é um termo geral para qualquer uma destas três coisas:

* Um pedido de entrada
* Uma dependência de saída (por exemplo, uma chamada remota para outro serviço)
* Uma dependência em curso (por exemplo, trabalho a ser feito por subcomponentes do serviço)

Para efeitos de transformadores de telemetria, os componentes importantes de um vão são:

* Nome
* Atributos

O nome de envergadura é o visor principal utilizado para pedidos e dependências no portal Azure.

Os atributos de envergadura representam propriedades padrão e personalizadas de um dado pedido ou dependência.

## <a name="telemetry-processor-types"></a>Tipos de processadores de telemetria

Existem atualmente dois tipos de processadores de telemetria.

#### <a name="attribute-processor"></a>Processador de atributos

Um processador de atributos tem a capacidade de inserir, atualizar, eliminar ou atributos de haxixe.
Também pode extrair (através de uma expressão regular) um ou mais novos atributos de um atributo existente.

#### <a name="span-processor"></a>Processador de extensão

Um processador de intervalo tem a capacidade de atualizar o nome da telemetria.
Também pode extrair (através de uma expressão regular) um ou mais novos atributos do nome de extensão.

> [!NOTE]
> Note que atualmente os processadores de telemetria apenas processam atributos de tipo de cadeia, e não processam atributos do tipo boolean ou número.

## <a name="getting-started"></a>Introdução

Crie um ficheiro de configuração chamado `applicationinsights.json` , e coloque-o no mesmo diretório `applicationinsights-agent-*.jar` que , com o seguinte modelo.

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

## <a name="includeexclude-criteria"></a>Incluir/excluir critérios

Tanto os processadores de atributos como os processadores de span suportam `include` critérios e `exclude` critérios opcionais.
Um transformador só será aplicado aos períodos que correspondam aos seus `include` critérios (se fornecidos) _e_ não correspondam aos seus `exclude` critérios (se fornecidos).

Para configurar esta opção, em `include` baixo e/ou `exclude` pelo menos um e um de ou é `matchType` `spanNames` `attributes` necessário.
A configuração de incluir/excluir é suportada para ter mais de uma condição especificada.
Todas as condições especificadas devem avaliar a verdade para que ocorra uma correspondência. 

**Campo obrigatório:** 
* `matchType` controla a forma como os itens `spanNames` e `attributes` matrizes são interpretados. Os valores possíveis são `regexp` ou `strict`. 

**Campos opcionais:** 
* `spanNames` deve corresponder a pelo menos um dos itens. 
* `attributes` especifica a lista de atributos a condizer. Todos estes atributos devem corresponder exatamente para que um jogo ocorra.

> [!NOTE]
> Se ambos `include` e `exclude` forem especificados, as `include` propriedades são verificadas antes das `exclude` propriedades.

#### <a name="sample-usage"></a>Utilização de Exemplo

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
Para obter mais compreensão, consulte a documentação do [processador de telemetria exemplos.](./java-standalone-telemetry-processors-examples.md)

## <a name="attribute-processor"></a>Processador de atributos

O processador de atributos modifica atributos de um vão. Suporta opcionalmente a capacidade de incluir/excluir vãos. Requer uma lista de ações que são realizadas por ordem especificada no ficheiro de configuração. As ações apoiadas são:

### `insert`

Insere um novo atributo em vãos onde a chave já não existe.   

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
Para a `insert` ação, são necessários seguintes
  * `key`
  * um dos `value` ou `fromAttribute`
  * `action`:`insert`

### `update`

Atualiza um atributo em vãos onde a chave existe

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
Para a `update` ação, são necessários seguintes
  * `key`
  * um dos `value` ou `fromAttribute`
  * `action`:`update`


### `delete` 

Elimina um atributo de um vão

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
Para a `delete` ação, são necessários seguintes
  * `key`
  * `action`: `delete`

### `hash`

Hashes (SHA1) um valor de atributo existente

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
Para a `hash` ação, são necessários seguintes
* `key`
* `action` : `hash`

### `extract`

> [!NOTE]
> Esta característica é apenas em 3.0.2 e mais tarde

Extrai valores utilizando uma regra de expressão regular da chave de entrada para as teclas-alvo especificadas na regra. Se já existe uma chave-alvo, será ultrapassada. Comporta-se de forma semelhante à definição [do Processador de Esversar](#extract-attributes-from-span-name) `toAttributes` com o atributo existente como fonte.

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
Para a `extract` ação, são necessários seguintes
* `key`
* `pattern`
* `action` : `extract`

Para obter mais compreensão, consulte a documentação do [processador de telemetria exemplos.](./java-standalone-telemetry-processors-examples.md)

## <a name="span-processor"></a>Processador de extensão

O processador de envergadura modifica o nome de extensão ou atributos de um vão baseado no nome da extensão. Suporta opcionalmente a capacidade de incluir/excluir vãos.

### <a name="name-a-span"></a>Diga um vão

É necessária a seguinte definição como parte da secção de nomes:

* `fromAttributes`: O valor de atributo para as teclas é utilizado para criar um novo nome na ordem especificada na configuração. Todas as teclas de atributos devem ser especificadas no intervalo para que o processador o rebatize.

A seguinte definição pode ser configurada opcionalmente:

* `separator`: Uma corda, que é especificada, será usada para dividir valores
> [!NOTE]
> Se o renomeamento depender da modificação dos atributos pelos atributos do processador, certifique-se de que o processador de envergadura é especificado após o processador de atributos na especificação do pipeline.

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

### <a name="extract-attributes-from-span-name"></a>Extrair atributos do nome de extensão

Pega numa lista de expressões regulares para combinar o nome de extensão com o do mesmo e extrair atributos do mesmo com base em subexpressões. Deve ser especificado na `toAttributes` secção.

São necessárias as seguintes definições:

`rules` : Uma lista de regras para extrair valores de atributos do nome de intervalo. Os valores do nome de intervalo são substituídos por nomes de atributos extraídos. Cada regra da lista é a cadeia de padrão regex. O nome do span é verificado contra o regex. Se o regex corresponder, todas as subexpressões nomeadas do regex são extraídas como atributos e adicionadas ao intervalo. Cada nome de subexpressão torna-se um nome de atributo e a porção de subexpressão corresponde ao valor do atributo. A parte combinada no nome da extensão é substituída pelo nome de atributo extraído. Se os atributos já existirem no espaço, serão substituídos. O processo é repetido para todas as regras na ordem em que são especificadas. Cada regra subsequente funciona com o nome de intervalo que é a saída após o processamento da regra anterior.

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

## <a name="list-of-attributes"></a>Lista de Atributos

Seguem-se a lista de alguns atributos de envergadura comuns que podem ser utilizados nos processadores de telemetria.

### <a name="http-spans"></a>PERÍODOS HTTP

| Atributo  | Tipo | Description | 
|---|---|---|
| `http.method` | cadeia (de carateres) | Método de pedido HTTP.|
| `http.url` | string | URL de pedido completo http no formulário `scheme://host[:port]/path?query[#fragment]` . Normalmente, o fragmento não é transmitido em HTTP, mas se for conhecido, deve ser incluído no entanto.|
| `http.status_code` | número | [Código de estado de resposta HTTP](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | string | Tipo de protocolo HTTP usado |
| `http.user_agent` | string | Valor do [cabeçalho http-user-agent](https://tools.ietf.org/html/rfc7231#section-5.5.3) enviado pelo cliente. |

### <a name="jdbc-spans"></a>JDBC Spans

| Atributo  | Tipo | Description  |
|---|---|---|
| `db.system` | cadeia (de carateres) | Está a ser utilizado um identificador para o sistema de gestão da base de dados (DBMS). |
| `db.connection_string` | string | A cadeia de ligação usada para ligar à base de dados. Recomenda-se a remoção de credenciais embutidas.|
| `db.user` | string | Nome de utilizador para aceder à base de dados. |
| `db.name` | string | Este atributo é utilizado para reportar o nome da base de dados que está a ser acedida. Para comandos que alterem a base de dados, este deve ser definido para a base de dados alvo (mesmo que o comando falhe).|
| `db.statement` | string | A declaração da base de dados está a ser executada.|
