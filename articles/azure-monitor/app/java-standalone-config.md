---
title: Opções de configuração - Azure Monitor Application Insights for Java
description: Como configurar insights de aplicação do monitor Azure para Java
ms.topic: conceptual
ms.date: 11/04/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: e58d69634712a9cc640ba9e4785a7bf1effaf88c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103224661"
---
# <a name="configuration-options---azure-monitor-application-insights-for-java"></a>Opções de configuração - Azure Monitor Application Insights for Java

> [!WARNING]
> **Se está a atualizar a partir de 3.0 Preview**
>
> Por favor, reveja cuidadosamente todas as opções de configuração abaixo, uma vez que a estrutura json mudou completamente, além do próprio nome de ficheiro que foi tudo minúsculo.

## <a name="connection-string-and-role-name"></a>Cadeia de conexão e nome de função

A cadeia de ligação e o nome da função são as definições mais comuns necessárias para começar:

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

O fio de ligação é necessário, e o nome da função é importante sempre que estiver a enviar dados de diferentes aplicações para o mesmo recurso Application Insights.

Encontrará mais detalhes e opções de configuração adicionais abaixo.

## <a name="configuration-file-path"></a>Caminho do arquivo de configuração

Por predefinição, a Application Insights Java 3.0 espera que o ficheiro de configuração seja `applicationinsights.json` nomeado, e que esteja localizado no mesmo diretório que `applicationinsights-agent-3.0.2.jar` .

Pode especificar o seu próprio caminho de ficheiro de configuração usando qualquer um

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` variável de ambiente, ou
* `applicationinsights.configuration.file` Propriedade do sistema Java

Se especificar um caminho relativo, será resolvido em relação ao diretório onde `applicationinsights-agent-3.0.2.jar` está localizado.

## <a name="connection-string"></a>Cadeia de ligação

É necessária uma ligação de ligação. Pode encontrar a sua cadeia de ligação no seu recurso Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Cadeia de conexão de insights de aplicação":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Também pode definir a cadeia de ligação utilizando a variável ambiente `APPLICATIONINSIGHTS_CONNECTION_STRING` (que terá precedência se a cadeia de ligação também for especificada na configuração json).

Não definir a cadeia de ligação irá desativar o agente Java.

## <a name="cloud-role-name"></a>Nome do papel da nuvem

O nome da função cloud é usado para rotular o componente no mapa de aplicações.

Se quiser definir o nome da função de nuvem:

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

Se o nome da função de nuvem não estiver definido, o nome do recurso Application Insights será utilizado para rotular o componente no mapa de aplicações.

Também pode definir o nome da função de nuvem usando a variável ambiente `APPLICATIONINSIGHTS_ROLE_NAME` (que terá precedência se o nome da função de nuvem também for especificado na configuração json).

## <a name="cloud-role-instance"></a>Instância de papel em nuvem

A instância de função em nuvem é padrão para o nome da máquina.

Se quiser definir a instância de papel em nuvem para algo diferente do nome da máquina:

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

Também pode definir a instância de função em nuvem usando a variável ambiente `APPLICATIONINSIGHTS_ROLE_INSTANCE` (que terá precedência se a instância de função da nuvem também for especificada na configuração json).

## <a name="sampling"></a>Amostragem

A amostragem é útil se precisar de reduzir o custo.
A amostragem é realizada como uma função no ID de funcionamento (também conhecido como identificação de vestígios), de modo que o mesmo ID de operação resultará sempre na mesma decisão de amostragem. Isto garante que não obterá partes de uma transação distribuída enquanto outras partes são amostradas.

Por exemplo, se definir a amostragem para 10%, só verá 10% das suas transações, mas cada uma dessas 10% terá detalhes de transações finais completas.

Aqui está um exemplo de como definir a amostragem para capturar aproximadamente **1/3 de todas as transações** - certifique-se de que define a taxa de amostragem que está correta para o seu caso de utilização:

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

Também pode definir a percentagem de amostragem utilizando a variável ambiente `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` (que terá precedência se a percentagem de amostragem também for especificada na configuração json).

> [!NOTE]
> Para a percentagem de amostragem, escolha uma percentagem próxima de 100/N onde N é um inteiro. Atualmente, a amostragem não suporta outros valores.

## <a name="jmx-metrics"></a>Métricas JMX

Se quiser recolher algumas métricas JMX adicionais:

```json
{
  "jmxMetrics": [
    {
      "name": "JVM uptime (millis)",
      "objectName": "java.lang:type=Runtime",
      "attribute": "Uptime"
    },
    {
      "name": "MetaSpace Used",
      "objectName": "java.lang:type=MemoryPool,name=Metaspace",
      "attribute": "Usage.used"
    }
  ]
}
```

`name` é o nome métrico que será atribuído a esta métrica JMX (pode ser qualquer coisa).

`objectName` é o [Nome objeto](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) do JMX MBean que pretende recolher.

`attribute` é o nome de atributo dentro do JMX MBean que pretende recolher.

Os valores métricos JMX numéricos e boolean são suportados. As métricas boolean jMX são mapeadas `0` para falso, e `1` para verdade.

## <a name="custom-dimensions"></a>Dimensões personalizadas

Se quiser adicionar dimensões personalizadas a toda a sua telemetria:

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

`${...}` pode ser usado para ler o valor da variável ambiente especificado no arranque.

> [!NOTE]
> A partir da versão 3.0.2, se adicionar uma dimensão personalizada `service.version` nomeada, o valor será armazenado na `application_Version` coluna na tabela 'Registos de Insights de Aplicação' em vez de como uma dimensão personalizada.

## <a name="telemetry-processors-preview"></a>Processadores de telemetria (pré-visualização)

Esta funcionalidade está em pré-visualização.

Permite-lhe configurar regras que serão aplicadas ao pedido, dependência e telemetria de traços, por exemplo:
 * Mascarar dados sensíveis
 * Adicionar dimensões personalizadas condicionalmente
 * Atualizar o nome da telemetria utilizado para agregação e exibição

Para mais informações, consulte a documentação do [processador de telemetria.](./java-standalone-telemetry-processors.md)

## <a name="auto-collected-logging"></a>Registos auto-recolhidos

Log4j, Logback e java.util.logging são instrumentados automaticamente, e o registo realizado através destas estruturas de registo é recolhido automaticamente.

A exploração madeireira só é capturada se cumprir primeiro o nível configurado para a estrutura de registo e, em segundo lugar, também cumpre o nível configurado para Insights de Aplicação.

Por exemplo, se a sua estrutura de registo estiver configurada para registar `WARN` (e acima) a partir do pacote `com.example` , e o Application Insights estiver configurado para capturar `INFO` (e acima), então o Application Insights apenas capturará `WARN` (e acima) do pacote `com.example` .

O nível padrão configurado para Insights de Aplicação é `INFO` . Se quiser alterar este nível:

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

Também pode definir o nível utilizando a variável ambiente `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` (que terá precedência se o nível também for especificado na configuração json).

Estes são os `level` valores válidos que pode especificar no `applicationinsights.json` ficheiro e como correspondem aos níveis de registo em diferentes quadros de registo:

| nível             | Log4j  | Logback | JUL     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| FATAL             | FATAL  | ERRO   | Grave  |
| ERRO (ou GRAVE) | ERRO  | ERRO   | Grave  |
| WARN (ou AVISO) | ATENÇÃO   | ATENÇÃO    | AVISO |
| INFO              | INFO   | INFO    | INFO    |
| CONFIG            | DEPURAR  | DEPURAR   | CONFIG  |
| DEBUG (ou FINE)   | DEPURAR  | DEPURAR   | TUDO BEM.    |
| FINER             | DEPURAR  | DEPURAR   | FINER   |
| TRACE (ou FINEST) | VESTÍGIOS  | VESTÍGIOS   | O MELHOR  |
| ALL               | ALL    | ALL     | ALL     |

> [!NOTE]
> Se um objeto de exceção for passado para o madeirão, então a mensagem de registo (e detalhes do objeto de exceção) aparecerá no portal Azure debaixo da `exceptions` tabela em vez da `traces` tabela.

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>Métricas de micrometros auto-recolhidas (incluindo métricas do actuador de bota de mola)

Se a sua aplicação utilizar [o Micrometro,](https://micrometer.io)as métricas enviadas para o registo global do Micrometro são recolhidas automaticamente.

Além disso, se a sua aplicação utilizar [o Actuador de Bota de Mola,](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)as métricas configuradas pelo Actuador de Bota de Mola também são recolhidas automaticamente.

Para desativar a recolha automática de métricas do Micrometro (incluindo métricas do Actuador de Arranque de Mola):

> [!NOTE]
> As métricas personalizadas são faturadas separadamente e podem gerar custos adicionais. Certifique-se de verificar as [informações detalhadas sobre os preços](https://azure.microsoft.com/pricing/details/monitor/). Para desativar as métricas do Micrometro e do Actuador de Mola, adicione a configuração abaixo ao seu ficheiro config.

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="suppressing-specific-auto-collected-telemetry"></a>Suprimir a telemetria auto-recolhida específica

A partir da versão 3.0.2, a telemetria auto-recolhida específica pode ser suprimida utilizando estas opções de configuração:

```json
{
  "instrumentation": {
    "cassandra": {
      "enabled": false
    },
    "jdbc": {
      "enabled": false
    },
    "kafka": {
      "enabled": false
    },
    "micrometer": {
      "enabled": false
    },
    "mongo": {
      "enabled": false
    },
    "redis": {
      "enabled": false
    }
  }
}
```

## <a name="heartbeat"></a>Heartbeat

Por predefinição, a Application Insights Java 3.0 envia uma métrica de batimento cardíaco a cada 15 minutos. Se estiver a usar a métrica do batimento cardíaco para desencadear alertas, pode aumentar a frequência deste batimento cardíaco:

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> Não é possível aumentar o intervalo para mais de 15 minutos, porque os dados do batimento cardíaco também são utilizados para rastrear o uso de Insights de Aplicação.

## <a name="http-proxy"></a>HTTP Proxy

Se a sua aplicação estiver por detrás de uma firewall e não puder ligar-se diretamente aos Insights de Aplicação (ver [endereços IP utilizados pelo Application Insights),](./ip-addresses.md)pode configurar a Aplicação Insights Java 3.0 para utilizar um proxy HTTP:

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

Application Insights Java 3.0 também respeita o global `-Dhttps.proxyHost` e se estes são `-Dhttps.proxyPort` definidos.

## <a name="metric-interval"></a>Intervalo métrico

Esta funcionalidade está em pré-visualização.

Por predefinição, as métricas são capturadas a cada 60 segundos.

A partir da versão 3.0.3-BETA, pode alterar este intervalo:

```json
{
  "preview": {
    "metricIntervalSeconds": 300
  }
}
```

A definição aplica-se a todas estas métricas:

* Contadores de desempenho predefinidos, por exemplo CPU e Memória
* Métricas personalizadas padrão, por exemplo, tempo de recolha de lixo
* Métricas JMX configuradas[(ver acima)](#jmx-metrics)
* Métricas de micrómetros[(ver acima)](#auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics)


[//]: # "NOTA O suporte openTelemetry está em pré-visualização privada até que a API openTelemetry atinja 1.0"

[//]: # "## Suporte para lançamentos pré-1.0 da API da OpenTelemetry"

[//]: # "Suporte para versões pré-1.0 da OpenTelemetry API é opt-in, porque a API OpenTelemetry ainda não está estável"
[//]: # "e assim cada versão do agente só suporta uma versão específica pré-1.0 da API OpenTelemetry"
[//]: # "(esta limitação não se aplicará uma vez lançada a API 1.0 da OpenTelemetry)."

[//]: # "''json'"
[//]: # "{"
[//]: # "  \"pré-visualização \" : {"
[//]: # "    \"openTelemetryApiSupport: \" verdadeiro"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>Autodiagnóssia

"Autodiagnóstro" refere-se à sessão interna de Insights java 3.0.

Esta funcionalidade pode ser útil para detetar e diagnosticar problemas com a própria Application Insights.

Por predefinição, os registos De Aplicação Java 3.0 estão ao nível `INFO` do ficheiro e da `applicationinsights.log` consola, correspondentes a esta configuração:

```json
{
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

`destination` pode ser um `file` dos, `console` ou `file+console` .

`level` pode ser um `OFF` `ERROR` dos, `WARN` , , , , ou `INFO` `DEBUG` `TRACE` .

`path` pode ser um caminho absoluto ou relativo. Caminhos relativos são resolvidos contra o diretório onde `applicationinsights-agent-3.0.2.jar` está localizado.

`maxSizeMb` é o tamanho máximo do ficheiro de registo antes de rolar.

`maxHistory` é o número de ficheiros de registo retorlados que são retidos (além do ficheiro de registo atual).

A partir da versão 3.0.2, também pode definir os autodiagnóscos `level` utilizando a variável ambiente `APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL` (que terá precedência se o autodiagnósto `level` também for especificado na configuração json).

## <a name="an-example"></a>Um exemplo

Este é apenas um exemplo para mostrar como é um ficheiro de configuração com vários componentes.
Por favor, configuure opções específicas com base nas suas necessidades.

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  },
  "sampling": {
    "percentage": 100
  },
  "jmxMetrics": [
  ],
  "customDimensions": {
  },
  "instrumentation": {
    "logging": {
      "level": "INFO"
    },
    "micrometer": {
      "enabled": true
    }
  },
  "proxy": {
  },
  "preview": {
    "processors": [
    ]
  },
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```
