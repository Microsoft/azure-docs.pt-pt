---
title: Upgrade a partir de 2.x - Azure Monitor Application Insights Java
description: Upgrade a partir de Azure Monitor Application Insights Java 2.x
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: d1d09c09afbabd40a32cbb80f1901112c37ac3da
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355138"
---
# <a name="upgrading-from-application-insights-java-sdk-2x"></a>Upgrade a partir de Application Insights Java SDK 2.x

Se já estiver a utilizar o Application Insights Java SDK 2.x na sua aplicação, não há necessidade de a remover.
O agente Java 3.0 irá detetá-lo e capturar e correlacionar qualquer telemetria personalizada que está a enviar através do Java SDK 2.x, enquanto suprimiu qualquer auto-recolha realizada pelo Java SDK 2.x para evitar a telemetria duplicada.

Se estava a usar o agente Application Insights 2.x, tem de remover o `-javaagent:` arg JVM que estava a apontar para o agente 2.x.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetriaInitializadores e TelemetriaProcessadores

Java SDK 2.x TelemetriaInitializadores e TelemetriaProcessadores não serão executados quando utilizar o agente 3.0.
Muitos dos casos de utilização anteriormente necessários podem ser resolvidos em 3.0 configurando [dimensões personalizadas](./java-standalone-config.md#custom-dimensions) ou configurando processadores de [telemetria](./java-standalone-telemetry-processors.md).

## <a name="multiple-applications-in-a-single-jvm"></a>Múltiplas aplicações num único JVM

Atualmente, 3.0 suporta apenas uma única [cadeia de ligação e nome de função](./java-standalone-config.md#connection-string-and-role-name) por processo de execução. Em particular, não é possível ter várias aplicações web tomcat na mesma implementação de tomcat usando diferentes cadeias de conexão ou diferentes nomes de papéis ainda.

## <a name="http-request-telemetry-names"></a>HTTP solicitar nomes de telemetria

HTTP Solicitam nomes de telemetria em 3.0 alterados para geralmente fornecer uma melhor visão agregada no Portal de Insights de Aplicação U/X.

No entanto, para algumas aplicações, ainda pode preferir a vista agregada no U/X que foi fornecida pelos nomes anteriores da telemetria, caso em que pode utilizar a funcionalidade de pré-visualização dos processadores de telemetria em 3.0 para voltar aos nomes anteriores.

### <a name="to-prefix-the-telemetry-name-with-the-http-method-get-post-etc"></a>Para pré-fixo o nome da telemetria com o método http `GET` (, `POST` , etc.):

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "attributes": [
            { "key": "http.method", "value": "" }
          ],
          "spanNames": [ "^/" ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "^(?<tempName>.*)$" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempName" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "actions": [
          { "key": "tempName", "action": "delete" }
        ]
      }
    ]
  }
}
```

### <a name="to-set-the-telemetry-name-to-the-full-url-path"></a>Para definir o nome da telemetria para o caminho completo do URL

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
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
            { "key": "http.method" },
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
