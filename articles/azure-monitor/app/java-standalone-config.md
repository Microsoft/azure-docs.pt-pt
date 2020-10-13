---
title: Monitorize aplicações java em qualquer lugar - Azure Monitor Application Insights
description: Monitorização de desempenho de aplicações sem código para aplicações Java em qualquer ambiente sem instrumentar a aplicação. Encontre a causa principal dos problemas d usando o mapa de rastreio e aplicação distribuídos.
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9b90f8b9336111438b4b832d557d448470959255
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537662"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>Opções de configuração - Java agente autónomo para Azure Monitor Application Insights



## <a name="connection-string-and-role-name"></a>Cadeia de conexão e nome de função

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

O fio de ligação é necessário, e o nome da função é importante sempre que estiver a enviar dados de diferentes aplicações para o mesmo recurso Application Insights.

Encontrará mais detalhes e opções de configuração adicionais abaixo para mais detalhes.

## <a name="configuration-file-path"></a>Caminho do arquivo de configuração

Por predefinição, a Pré-visualização do Application Insights Java 3.0 espera que o ficheiro de configuração seja nomeado `ApplicationInsights.json` , e que esteja localizado no mesmo diretório que `applicationinsights-agent-3.0.0-PREVIEW.5.jar` .

Pode especificar o seu próprio caminho de ficheiro de configuração usando qualquer um

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` variável de ambiente, ou
* `applicationinsights.configurationFile` Propriedade do sistema Java

Se especificar um caminho relativo, será resolvido em relação ao diretório onde `applicationinsights-agent-3.0.0-PREVIEW.5.jar` está localizado.

## <a name="connection-string"></a>Cadeia de ligação

Isto é necessário. Pode encontrar a sua cadeia de ligação no seu recurso Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Cadeia de conexão de insights de aplicação":::


```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

Também pode definir a cadeia de ligação utilizando a variável `APPLICATIONINSIGHTS_CONNECTION_STRING` ambiente.

Não definir a cadeia de ligação irá desativar o agente Java.

## <a name="cloud-role-name"></a>Nome do papel da nuvem

O nome da função cloud é usado para rotular o componente no mapa de aplicações.

Se quiser definir o nome da função de nuvem:

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

Se o nome da função de nuvem não estiver definido, o nome do recurso Application Insights será utilizado para rotular o componente no mapa de aplicações.

Também pode definir o nome da função de nuvem usando a variável `APPLICATIONINSIGHTS_ROLE_NAME` ambiente.

## <a name="cloud-role-instance"></a>Instância de papel em nuvem

A instância de função em nuvem é padrão para o nome da máquina.

Se quiser definir a instância de papel em nuvem para algo diferente do nome da máquina:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

Também pode definir a instância de função em nuvem usando a variável `APPLICATIONINSIGHTS_ROLE_INSTANCE` ambiente.

## <a name="application-log-capture"></a>Captura de registo de aplicações

A pré-visualização de Informações Java 3.0 captura automaticamente a marcação de aplicações através de Log4j, Logback e java.util.logging.

Por predefinição, capturará todos os registos realizados ao `INFO` nível ou acima.

Se quiser alterar este limiar:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "WARN"
        }
      }
    }
  }
}
```

Também pode definir o limiar de registo utilizando a variável `APPLICATIONINSIGHTS_LOGGING_THRESHOLD` ambiente.

Estes são os `threshold` valores válidos que pode especificar no `ApplicationInsights.json` ficheiro e como correspondem aos níveis de registo em diferentes quadros de registo:

| valor limiar   | Log4j  | Logback | JUL     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| FATAL             | FATAL  | ERROR   | Grave  |
| ERRO (ou GRAVE) | ERROR  | ERROR   | Grave  |
| WARN (ou AVISO) | ATENÇÃO   | ATENÇÃO    | AVISO |
| INFO              | INFO   | INFO    | INFO    |
| CONFIG            | DEPURAR  | DEPURAR   | CONFIG  |
| DEBUG (ou FINE)   | DEPURAR  | DEPURAR   | TUDO BEM.    |
| FINER             | DEPURAR  | DEPURAR   | FINER   |
| TRACE (ou FINEST) | VESTÍGIOS  | VESTÍGIOS   | O MELHOR  |
| ALL               | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>Métricas JMX

Se tiver algumas métricas JMX que esteja interessado em capturar:

```json
{
  "instrumentationSettings": {
    "preview": {
      "jmxMetrics": [
        {
          "objectName": "java.lang:type=Runtime",
          "attribute": "Uptime",
          "display": "JVM uptime (millis)"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Metaspace",
          "attribute": "Usage.used",
          "display": "MetaSpace Used"
        }
      ]
    }
  }
}
```

Também pode definir as métricas JMX utilizando a variável `APPLICATIONINSIGHTS_JMX_METRICS` ambiente.

Este conteúdo variável do ambiente deve ser um dado json correspondente à estrutura acima referida, por exemplo. `[{"objectName": "java.lang:type=Runtime", "attribute": "Uptime", "display": "JVM uptime (millis)"}, {"objectName": "java.lang:type=MemoryPool,name=Metaspace", "attribute": "Usage.used", "display": "MetaSpace Used"}]`

## <a name="micrometer-including-metrics-from-spring-boot-actuator"></a>Micrometro (incluindo métricas do Actuador de Bota de Mola)

Se a sua aplicação utilizar [o Micrometro](https://micrometer.io), o Application Insights 3.0 (a partir da Preview.2) captura agora as métricas enviadas para o registo global do Micrometro.

Se a sua aplicação utilizar [o Actuador de Bota de Mola](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html), o Application Insights 3.0 (a partir da Pré-visualização.4) captura agora métricas configuradas pelo Actuador de Bota de Mola (que utiliza o Micrometro, mas não utiliza o registo global do Micrometro).

Se quiser desativar estas funcionalidades:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "micrometer": {
          "enabled": false
        }
      }
    }
  }
}
```

## <a name="heartbeat"></a>Heartbeat

Por predefinição, a pré-visualização De Insights de Aplicação Java 3.0 envia uma métrica de batimento cardíaco a cada 15 minutos. Se estiver a usar a métrica do batimento cardíaco para desencadear alertas, pode aumentar a frequência deste batimento cardíaco:

```json
{
  "instrumentationSettings": {
    "preview": {
      "heartbeat": {
        "intervalSeconds": 60
      }
    }
  }
}
```

> [!NOTE]
> Não é possível diminuir a frequência deste batimento cardíaco, uma vez que os dados do batimento cardíaco também são utilizados para rastrear o uso de Insights de Aplicação.

## <a name="sampling"></a>Amostragem

A amostragem é útil se precisar de reduzir o custo.
A amostragem é realizada como uma função no ID de funcionamento (também conhecido como identificação de vestígios), de modo que o mesmo ID de operação resultará sempre na mesma decisão de amostragem. Isto garante que não obterá partes de uma transação distribuída enquanto outras partes são amostradas.

Por exemplo, se definir a amostragem para 10%, só verá 10% das suas transações, mas cada uma dessas 10% terá detalhes de transações finais completas.

Aqui está um exemplo de como definir a amostragem para **10% de todas as transações** - certifique-se de definir a taxa de amostragem que está correta para o seu caso de uso:

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10
        }
      }
    }
  }
}
```

Também pode definir a percentagem de amostragem utilizando a variável `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` ambiente.

## <a name="http-proxy"></a>HTTP Proxy

Se a sua aplicação estiver por detrás de uma firewall e não puder ligar-se diretamente aos Insights de Aplicação (ver [endereços IP utilizados pelo Application Insights),](./ip-addresses.md)pode configurar a pré-visualização do Pedido Desconsejamento Java 3.0 para utilizar um proxy HTTP:

```json
{
  "instrumentationSettings": {
    "preview": {
      "httpProxy": {
        "host": "myproxy",
        "port": 8080
      }
    }
  }
}
```

## <a name="self-diagnostics"></a>Autodiagnóssia

"Autodiagnóstro" refere-se à sessão interna a partir de Pedidos de Informação Java 3.0 Preview.

Isto pode ser útil para detetar e diagnosticar problemas com a própria Application Insights.

Por predefinição, regista-se para consolar com nível `warn` , correspondente a esta configuração:

```json
{
  "instrumentationSettings": {
    "preview": {
      "selfDiagnostics": {
        "destination": "console",
        "level": "WARN"
      }
    }
  }
}
```

Os níveis válidos `OFF` são, `ERROR` , , , e `WARN` `INFO` `DEBUG` `TRACE` .

Se pretender iniciar sessão num ficheiro em vez de iniciar sessão na consola:

```json
{
  "instrumentationSettings": {
    "preview": {
      "selfDiagnostics": {
        "destination": "file",
        "directory": "/var/log/applicationinsights",
        "level": "WARN",
        "maxSizeMB": 10
      }
    }
  }
}
```

Ao utilizar o registo de ficheiros, uma vez que o ficheiro `maxSizeMB` atinge, irá capotar, mantendo apenas o ficheiro de registo mais recentemente preenchido, para além do ficheiro de registo atual.
