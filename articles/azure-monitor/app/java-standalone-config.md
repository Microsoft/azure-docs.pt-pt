---
title: Monitor aplicativos Java em qualquer lugar - Azure Monitor Application Insights
description: Monitorização do desempenho da aplicação sem código para aplicações Java em qualquer ambiente sem instrumentar a aplicação. Encontre a causa principal dos problemas d utilizando o rastreio distribuído e o mapa de aplicações.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 478e42669339ac015076c89da103d91080090685
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509215"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>Opções de configuração - Java agente autónomo para Insights de Aplicação do Monitor Azure



## <a name="connection-string-and-role-name"></a>Fio de ligação e nome de papel

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

A cadeia de ligação é necessária e o nome do papel é importante sempre que estiver a enviar dados de diferentes aplicações para o mesmo recurso Desinformação de Aplicação.

Encontrará mais detalhes e opções de configuração adicionais abaixo para mais detalhes.

## <a name="configuration-file-path"></a>Caminho de ficheiro de configuração

Por predefinição, a Pré-visualização De Insights `ApplicationInsights.json`de Aplicação Java 3.0 `applicationinsights-agent-3.0.0-PREVIEW.4.jar`espera que o ficheiro de configuração seja nomeado , e que esteja localizado no mesmo diretório que .

Pode especificar o seu próprio caminho de ficheiro de configuração usando qualquer um dos dois

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE`variável ambiente, ou
* `applicationinsights.configurationFile`Propriedade do sistema Java

Se especificar um caminho relativo, será resolvido em `applicationinsights-agent-3.0.0-PREVIEW.4.jar` relação ao diretório onde se encontra.

## <a name="connection-string"></a>Cadeia de ligação

Isto é necessário. Pode encontrar a sua cadeia de ligação no seu recurso Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Linha de ligação insights de aplicação":::

Também pode definir a cadeia de `APPLICATIONINSIGHTS_CONNECTION_STRING`ligação utilizando a variável ambiente .

## <a name="cloud-role-name"></a>Nome de papel de nuvem

O nome da função cloud é usado para rotular o componente no mapa de aplicações.

Se quiser definir o nome do papel da nuvem:

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

Se o nome da função cloud não estiver definido, o nome do recurso Application Insights será usado para rotular o componente no mapa de aplicações.

Também pode definir o nome da `APPLICATIONINSIGHTS_ROLE_NAME`função da nuvem utilizando a variável ambiental .

## <a name="cloud-role-instance"></a>Instância de papel de nuvem

A função cloud exemplo de predefinição do nome da máquina.

Se quiser definir a função de nuvem para algo diferente do nome da máquina:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

Também pode definir a função de `APPLICATIONINSIGHTS_ROLE_INSTANCE`nuvem utilizando a variável ambiental .

## <a name="application-log-capture"></a>Captura de registo de aplicações

Informações de aplicação Java 3.0 Preview captura automaticamente o registo de aplicações através de Log4j, Logback e java.util.logging.

Por padrão, capturará todo `WARN` o registo realizado ao nível ou superior.

Se quiser alterar este limiar:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "ERROR"
        }
      }
    }
  }
}
```

Estes são `threshold` os valores válidos `ApplicationInsights.json` que pode especificar no ficheiro, e como correspondem aos níveis de registo em diferentes quadros de exploração madeireira:

| `threshold`  | Log4j  | Logback | JUL     |
|--------------|--------|---------|---------|
| OFF          | OFF    | OFF     | OFF     |
| FATAL        | FATAL  | ERROR   | SEVERO  |
| ERRO/GRAVE | ERROR  | ERROR   | SEVERO  |
| AVISO/AVISO | ATENÇÃO   | ATENÇÃO    | AVISO |
| INFORMAÇÃO         | INFORMAÇÃO   | INFORMAÇÃO    | INFORMAÇÃO    |
| CONFIG       | DEPURAR  | DEPURAR   | CONFIG  |
| DEPURAÇÃO/MULTA   | DEPURAR  | DEPURAR   | MULTA    |
| Mais fino        | DEPURAR  | DEPURAR   | Mais fino   |
| TRAÇO/MELHOR | VESTÍGIOS  | VESTÍGIOS   | O MELHOR  |
| TUDO          | TUDO    | TUDO     | TUDO     |

## <a name="jmx-metrics"></a>Métricas JMX

Se tiver algumas métricas JMX que está interessado em capturar:

```json
{
  "instrumentationSettings": {
    "preview": {
        "jmxMetrics": [
        {
          "objectName": "java.lang:type=ClassLoading",
          "attribute": "LoadedClassCount",
          "display": "Loaded Class Count"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Code Cache",
          "attribute": "Usage.used",
          "display": "Code Cache Used"
        }
      ]
    }
  }
}
```

## <a name="micrometer-including-metrics-from-spring-boot-actuator"></a>Micrómetro (incluindo métricas do Actuador de Botas de primavera)

Se a sua aplicação utilizar [o Micrometro](https://micrometer.io), Application Insights 3.0 (começando com Preview.2) captura agora as métricas enviadas para o registo global do Micrometro.

Se a sua aplicação utilizar o Acionador de Arranque de [Mola](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html), application Insights 3.0 (começando com Preview.4) captura agora métricas configuradas pelo Acionador de Arranque de Mola (que utiliza o Micrometro, mas não utiliza o registo global do Micrometro).

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

Por predefinição, a pré-visualização de Insights de Aplicação Java 3.0 envia uma métrica de batimento cardíaco uma vez a cada 15 minutos. Se estiver a usar a métrica do batimento cardíaco para desencadear alertas, pode aumentar a frequência deste batimento cardíaco:

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
> Não é possível diminuir a frequência deste batimento cardíaco, uma vez que os dados do batimento cardíaco também são usados para rastrear o uso de Insights de Aplicação.

## <a name="sampling"></a>Amostragem

A amostragem é útil se precisar de reduzir o custo.
A amostragem é realizada em função do ID de funcionamento (também conhecido como identificação do rastreio), de modo que o mesmo ID de operação resultará sempre na mesma decisão de amostragem. Isto garante que não obterá partes de uma transação distribuída enquanto outras partes são amostradas.

Por exemplo, se definir amostragem para 10%, só verá 10% das suas transações, mas cada uma dessas 10% terá detalhes completos de transação de ponta a ponta.

Aqui está um exemplo de como definir a amostragem para **10% de todas as transações** - certifique-se de definir a taxa de amostragem que está correta para o seu caso de utilização:

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

## <a name="http-proxy"></a>HTTP Proxy

Se a sua aplicação estiver por detrás de uma firewall e não conseguir ligar-se diretamente aos Insights de Aplicação (ver [endereços IP utilizados por Insights de Aplicação),](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses)pode configurar os Insights de Aplicação Java 3.0 Preview para utilizar um proxy HTTP:

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

## <a name="self-diagnostics"></a>Autodiagnóstico

"Auto-diagnóstico" refere-se ao registo interno a partir de Insights de Aplicação Java 3.0 Pré-visualização.

Isto pode ser útil para detetar e diagnosticar problemas com a própria Application Insights.

Por predefinição, inicia-se `warn`para consolar com nível , correspondente a esta configuração:

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

Os níveis `OFF`válidos são, `ERROR`, `WARN`, `INFO`e `DEBUG`. `TRACE`

Se quiser iniciar sessão num ficheiro em vez de iniciar sessão na consola:

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

Ao utilizar o registo de `maxSizeMB`ficheiros, uma vez que o ficheiro seja introduzido, será reversão, mantendo apenas o ficheiro de registo mais recentemente concluído, para além do ficheiro de registo atual.
