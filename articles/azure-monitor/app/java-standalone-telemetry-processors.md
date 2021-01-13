---
title: Processadores de telemetria (pré-visualização) - Azure Monitor Application Insights for Java
description: Como configurar processadores de telemetria em Azure Monitor Application Insights for Java
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: ba4e6b8b5e9db494ab4c0c372c2086087a2d58cb
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133179"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Processadores de telemetria (pré-visualização) - Azure Monitor Application Insights for Java

> [!NOTE]
> Esta funcionalidade ainda se encontra em pré-visualização.

Java 3.0 Agente para Insights de Aplicação tem agora a capacidade de processar dados de telemetria antes de os dados serem exportados.

Seguem-se alguns casos de utilização de processadores de telemetria:
 * Mascarar dados sensíveis
 * Adicionar dimensões personalizadas condicionalmente
 * Atualizar o nome da telemetria utilizado para agregação e exibição
 * Atributos de intervalo ou de filtro para controlar o custo de ingestão

## <a name="terminology"></a>Terminologia

Antes de saltarmos para processadores de telemetria, é importante entender o que são vestígios e vãos.

### <a name="traces"></a>Rastreios

Os vestígios acompanham a progressão de um único pedido, chamado `trace` de , como é tratado por serviços que compõem uma aplicação. O pedido pode ser iniciado por um utilizador ou por uma aplicação. Cada unidade de trabalho num `trace` é chamada de `span` a; a é uma árvore de `trace` vãos. A `trace` é composta pelo período de raiz única e por qualquer número de extensões infantis.

### <a name="span"></a>Vão

Os vãos são objetos que representam o trabalho que está a ser feito por serviços individuais ou componentes envolvidos num pedido à medida que flui através de um sistema. A `span` contém um conjunto de `span context` identificadores globalmente únicos que representam o pedido único de que cada vão faz parte. 

Os vãos encapsulam:

* O nome da extensão
* Um imutável `SpanContext` que identifica exclusivamente o Vão
* Um período de pais na forma de `Span` `SpanContext` um, ou nulo
* Uma `SpanKind`
* Uma estamp de tempo de início
* Um fim da hora
* [`Attributes`](#attributes)
* Uma lista de eventos com tempos
* A `Status` .

Geralmente, o ciclo de vida de um vão assemelha-se ao seguinte:

* Um pedido é recebido por um serviço. O contexto de extensão é extraído dos cabeçalhos de pedido, se existir.
* Uma nova extensão é criada como uma criança do contexto de extensão extraída; se nenhuma existe, uma nova extensão de raiz é criada.
* O serviço trata do pedido. São adicionados atributos e eventos adicionais ao intervalo que são úteis para compreender o contexto do pedido, como o nome de anfitrião da máquina que manuseia o pedido, ou identificadores de clientes.
* Podem ser criados novos vãos para representar o trabalho que está a ser feito por subcomponentes do serviço.
* Quando o serviço faz uma chamada remota para outro serviço, o contexto de envergadura atual é serializado e encaminhado para o serviço seguinte, injetando o contexto de envergadura nos cabeçalhos ou envelope de mensagens.
* O trabalho que está a ser feito pelo serviço completa, com ou sem sucesso. O estado de envergadura está devidamente definido e o intervalo está marcado.

### <a name="attributes"></a>Atributos

`Attributes` são uma lista de pares de valor-chave zero ou mais que são encapsulados num `span` . Um Atributo DEVE ter as seguintes propriedades:

A chave de atributo, que DEVE ser uma corda não-nula e não vazia.
O valor do atributo, que é:
* Um tipo primitivo: corda, booleano, ponto flutuante de dupla precisão (IEEE 754-1985) ou assinado 64 bit inteiro.
* Uma matriz de valores primitivos do tipo. A matriz DEVE ser homogénea, ou seja, NÃO deve conter valores de diferentes tipos. Para protocolos que não suportam de forma nativa valores de matriz tais devem ser representados como cordas JSON.

## <a name="supported-processors"></a>Processadores suportados:
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

## <a name="includeexclude-spans"></a>Incluir/Excluir vãos

O processador de atributos e o processador de envergadura expõem a opção de fornecer um conjunto de propriedades de um período de tempo a combinar, para determinar se o espaço deve ser incluído ou excluído do processador de telemetria. Para configurar esta opção, em `include` baixo e/ou `exclude` pelo menos um e um de ou é `matchType` `spanNames` `attributes` necessário. A configuração de incluir/excluir é suportada para ter mais de uma condição especificada. Todas as condições especificadas devem avaliar a verdade para que ocorra uma correspondência. 

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
      },
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
      },
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
      },
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
      },
    ]
  }
]
```
Para a `hash` ação, são necessários seguintes
* `key`
* `action` : `hash`

### `extract`

> [!NOTE]
> Esta funcionalidade é apenas em 3.0.1 e mais tarde

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
      },
    ]
  }
]
```
Para a `extract` ação, são necessários seguintes
* `key`
* `pattern`
* `action` : `extract`

Para obter mais compreensão, consulte a documentação do [processador de telemetria exemplos.](./java-standalone-telemetry-processors-examples.md)

## <a name="span-processors"></a>Processadores de vãos

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