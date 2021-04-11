---
title: Upgrade a partir de 2.x - Azure Monitor Application Insights Java
description: Upgrade a partir de Azure Monitor Application Insights Java 2.x
ms.topic: conceptual
ms.date: 11/25/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 342c535cadb1a2d3f2d18478d8941d9ea61bdf72
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448972"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>Upgrade a partir de Application Insights Java 2.x SDK

Se já estiver a utilizar o Application Insights Java 2.x SDK na sua aplicação, não há necessidade de a remover.
O agente Java 3.0 irá detetá-lo e capturar e correlacionar qualquer telemetria personalizada que está a enviar através do 2.x SDK, enquanto suprimi qualquer auto-coleção realizada pelo 2.x SDK para evitar a telemetria duplicada.

Se estava a usar o agente Application Insights 2.x, tem de remover o `-javaagent:` arg JVM que estava a apontar para o agente 2.x.

O resto deste documento descreve limitações e alterações que poderá encontrar ao atualizar de 2.x para 3.0, bem como algumas soluções alternativas que poderá achar úteis.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetriaInitializadores e TelemetriaProcessadores

Os 2.x TelemetriaInitializadores e Processos de Telemetria não serão executados quando utilizar o agente 3.0.
Muitos dos casos de utilização anteriormente necessários podem ser resolvidos em 3.0 configurando [dimensões personalizadas](./java-standalone-config.md#custom-dimensions) ou configurando processadores de [telemetria](./java-standalone-telemetry-processors.md).

## <a name="multiple-applications-in-a-single-jvm"></a>Múltiplas aplicações num único JVM

Atualmente, 3.0 suporta apenas uma única [cadeia de ligação e nome de função](./java-standalone-config.md#connection-string-and-role-name) por processo de execução. Em particular, não é possível ter várias aplicações web tomcat na mesma implementação de tomcat usando diferentes cadeias de conexão ou diferentes nomes de papéis ainda.

## <a name="operation-names"></a>Nomes de operação

Os nomes de operação em 3.0 mudaram para geralmente fornecer uma melhor visão agregada no Portal de Insights de Aplicação U/X.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-3-0.png" alt-text="Nomes de operação em 3.0":::

No entanto, para algumas aplicações, ainda pode preferir a vista agregada no U/X que foi fornecida pelos nomes de operação anteriores, caso em que pode utilizar a funcionalidade de processadores de [telemetria](./java-standalone-telemetry-processors.md) (pré-visualização) em 3.0 para replicar o comportamento anterior.

### <a name="prefix-the-operation-name-with-the-http-method-get-post-etc"></a>Prefixar o nome da operação com o método http `GET` `POST` (, , etc.)

No 2.x SDK, os nomes da operação foram prefixados pelo método http `GET` `POST` (, etc.), por exemplo

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-prefixed-by-http-method.png" alt-text="Nomes de operação prefixados por método http":::

A partir de 3.0.3, você pode trazer de volta este comportamento 2.x usando

```json
{
  "preview": {
    "httpMethodInOperationName": true
  }
}
```

### <a name="set-the-operation-name-to-the-full-path"></a>Desacorda o nome da operação no caminho completo

Além disso, no 2.x SDK, em alguns casos, os nomes da operação continham o caminho completo, por exemplo.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="Nomes de operação com percurso completo":::

O snippet abaixo configura 4 processadores de telemetria que combinam para replicar o comportamento anterior.
Os processadores de telemetria executam as seguintes ações (por ordem):

1. O primeiro processador de telemetria é um processador de envergadura (tem `span` tipo), o que significa que se aplica a `requests` e `dependencies` .

   Corresponderá a qualquer extensão que tenha um atributo chamado `http.url` .

   Em seguida, atualizará o nome de intervalo com o `http.url` valor do atributo.

   Este seria o fim, exceto que `http.url` se parece com algo `http://host:port/path` como, e é provável que só queiras o `/path` papel.

2. O segundo processador de telemetria é também um processador de envergadura.

   Corresponderá a qualquer extensão que tenha um atributo nomeado `http.url` (por outras palavras, qualquer extensão que o primeiro processador corresponda).

   Em seguida, extrairá a parte do caminho do nome da extensão num atributo denominado `tempName` .

3. O terceiro processador de telemetria é também um processador de envergadura.

   Corresponderá a qualquer extensão que tenha um atributo chamado `tempPath` .

   Em seguida, atualizará o nome de intervalo a partir do atributo `tempPath` .

4. O último processador de telemetria é um processador de atributos (tem `attribute` tipo), o que significa que se aplica a toda a telemetria que tem atributos (atualmente `requests` , e `dependencies` `traces` ).

   Corresponderá a qualquer telemetria que tenha um atributo chamado `tempPath` .

   Em seguida, eliminará o atributo denominado `tempPath` , para que não seja reportado como uma dimensão personalizada.

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "tempPath" ]
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```

## <a name="dependency-names"></a>Nomes de dependência

Os nomes de dependência em 3.0 também mudaram, novamente para fornecer uma melhor visão agregada no Portal de Insights de Aplicação U/X.

Mais uma vez, para algumas aplicações, pode ainda preferir a vista agregada no U/X que foi fornecida pelos nomes anteriores da dependência, caso em que pode usar técnicas semelhantes às anteriores para replicar o comportamento anterior.

## <a name="operation-name-on-dependencies"></a>Nome da operação sobre dependências

Anteriormente no 2.x SDK, o nome de operação da telemetria de pedido também foi definido na telemetria de dependência.
Application Insights Java 3.0 já não povoa o nome da operação na telemetria de dependência.
Se quiser ver o nome da operação para o pedido que é o pai da telemetria de dependência, pode escrever uma consulta de Logs (Kusto) para se juntar da tabela de dependência à tabela de pedidos, por exemplo.

```
let start = datetime('...');
let end = datetime('...');
dependencies
| where timestamp between (start .. end)
| project timestamp, type, name, operation_Id
| join (requests
    | where timestamp between (start .. end)
    | project operation_Name, operation_Id)
    on $left.operation_Id == $right.operation_Id
| summarize count() by operation_Name, type, name
```

## <a name="2x-sdk-logging-appenders"></a>2.x Aplicativos de registo SDK

O agente 3.0 recolhe automaticamente o [registo registado sem](./java-standalone-config.md#auto-collected-logging) a necessidade de configurar quaisquer appenders de registo.
Se estiver a utilizar 2.x appenders de registo SDK, estes podem ser removidos, uma vez que serão suprimidos pelo agente 3.0 de qualquer forma.

## <a name="2x-sdk-spring-boot-starter"></a>Arranque de arranque de mola SDK de 2.x

Não há arranque de mola 3.0.
A configuração e configuração do agente 3.0 segue os mesmos [passos simples,](./java-in-process-agent.md#quickstart) quer esteja a usar ou não bota de mola.

Ao atualizar a partir do arranque de mola de 2.x SDK, note que o nome da função da nuvem deixará de ser padrão para `spring.application.name` .
Consulte os [docs de configuração 3.0](./java-standalone-config.md#cloud-role-name) para definir o nome da função de nuvem em 3.0 via json config ou variável ambiente.
