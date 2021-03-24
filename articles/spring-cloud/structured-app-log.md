---
title: Registo de aplicações estruturado para Azure Spring Cloud | Microsoft Docs
description: Este artigo explica como gerar e recolher dados estruturados de registo de aplicações em Azure Spring Cloud.
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 844530c6d1650b5fddd27b10c775c4364a3f5147
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878359"
---
# <a name="structured-application-log-for-azure-spring-cloud"></a>Registo de aplicações estruturado para Azure Spring Cloud

Este artigo explica como gerar e recolher dados estruturados de registo de aplicações em Azure Spring Cloud. Com uma configuração adequada, o Azure Spring Cloud fornece consulta e análise útil de registo de aplicações através do Log Analytics.

## <a name="log-schema-requirements"></a>Requisitos de esquema de registo
Para melhorar a experiência de consulta de registo, é necessário um registo de aplicação no formato JSON e conforme a um esquema. A Azure Spring Cloud utiliza este esquema para analisar a sua aplicação e transmitir para Log Analytics. 

**Requisitos de esquema JSON:**

| Chave Json      | Tipo de valor Json|  Necessário | Coluna em Log Analytics| Descrição |
| --------------| ------------|-----------|-----------------|--------------------------|
| carimbo de data/hora     | string      |     Sim   | AppTimestamp    | timetamp em formato UTC  |
| madeireiro        | cadeia (de carateres)      |     No    | Madeir          | madeireiro                   |
| nível         | cadeia (de carateres)      |     No    | PersonalizadoSLevel     | nível de log                |
| fio        | cadeia (de carateres)      |     No    | Fio          | fio                   |
| message       | cadeia (de carateres)      |     No    | Mensagem         | mensagem de log              |
| stackTrace    | cadeia (de carateres)      |     No    | StackTrace      | traço de pilha de exceção    |
| exceçãoClass| cadeia (de carateres)      |     No    | Classe de Exceções  | nome de classe exceção     |
| mdc           | JSON aninhado |     No    |                 | contexto de diagnóstico mapeado|
| mdc.traceId   | cadeia (de carateres)      |     No    | TraceId         |trace Id para rastreador distribuído|
| mdc.spanId    | cadeia (de carateres)      |     No    | SpanId          |id de extensão para rastreador distribuído |
|               |             |           |                 |                          |

* O campo "timetamp" é necessário, e deve estar em formato UTC, todos os outros campos são opcionais.
* "traceId" e "spanId" no campo "mdc" são usados para fins de rastreio.
* Regista cada registo JSON numa única linha. 

**Amostra de registo de registo de registo** 
 ```
{"timestamp":"2021-01-08T09:23:51.280Z","logger":"com.example.demo.HelloController","level":"ERROR","thread":"http-nio-1456-exec-4","mdc":{"traceId":"c84f8a897041f634","spanId":"c84f8a897041f634"},"stackTrace":"java.lang.RuntimeException: get an exception\r\n\tat com.example.demo.HelloController.throwEx(HelloController.java:54)\r\n\","message":"Got an exception","exceptionClass":"RuntimeException"}
```

## <a name="generate-schema-compliant-json-log"></a>Gerar registo JSON compatível com esquemas  
Para aplicações primavera, você pode gerar o formato de registo JSON esperado usando [quadros comuns de registo](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-configuration), tais como [logback](http://logback.qos.ch/) e [log4j2](https://logging.apache.org/log4j/2.x/). 

### <a name="log-with-logback"></a>Registar com logback 
Ao utilizar entradas de Arranque de Mola, o logback é utilizado por padrão. Para aplicações de logback, utilize [o codificadora de logstash](https://github.com/logstash/logstash-logback-encoder) para gerar registos formatados JSON. Este método é suportado na versão 2.1+. 

O procedimento:

1. Adicione a dependência de logstash no seu ficheiro pom.xml. 

    ```json
    <dependency>
        <groupId>net.logstash.logback</groupId>
        <artifactId>logstash-logback-encoder</artifactId>
        <version>6.5</version>
    </dependency>
    ```
1. Atualize o seu ficheiro config logback.xml para definir o formato JSON.
    ```json
    <configuration>
        <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
            <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
            <providers>
                <timestamp>
                    <fieldName>timestamp</fieldName>
                    <timeZone>UTC</timeZone>
                </timestamp>
                <loggerName>
                    <fieldName>logger</fieldName>
                </loggerName>
                <logLevel>
                    <fieldName>level</fieldName>
                </logLevel>
                <threadName>
                    <fieldName>thread</fieldName>
                </threadName>
                <nestedField>
                    <fieldName>mdc</fieldName>
                    <providers>
                        <mdc/>
                    </providers>
                </nestedField>
                <stackTrace>
                    <fieldName>stackTrace</fieldName>
                </stackTrace>
                <message/>
                <throwableClassName>
                    <fieldName>exceptionClass</fieldName>
                </throwableClassName>
            </providers>
            </encoder>
        </appender>
        <root level="info">
            <appender-ref ref="stdout"/>
        </root>
    </configuration>
    ```

### <a name="log-with-log4j2"></a>Faça login com log4j2 

Para aplicações log4j2, use [json-modelo-layout](https://logging.apache.org/log4j/2.x/manual/json-template-layout.html) para gerar registos formatados JSON. Este método é suportado na versão 2.1+.

O procedimento:

1. Exclua `spring-boot-starter-logging` de `spring-boot-starter` , adicionar `spring-boot-starter-log4j2` dependências, no seu ficheiro `log4j-layout-template-json` pom.xml.

    ```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                    <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-layout-template-json</artifactId>
            <version>2.14.0</version>
        </dependency>
    ```

2. Prepare um ficheiro de modelo de layout JSON jsonTemplate.jsno seu caminho de classe.

    ```json
        {
            "mdc": {
                "$resolver": "mdc"
            },
            "exceptionClass": {
                "$resolver": "exception",
                "field": "className"
            },
            "stackTrace": {
                "$resolver": "exception",
                "field": "stackTrace",
                "stringified": true
            },
            "message": {
                "$resolver": "message",
                "stringified": true
            },
            "thread": {
                "$resolver": "thread",
                "field": "name"
            },
            "timestamp": {
                "$resolver": "timestamp",
                "pattern": {
                    "format": "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'",
                    "timeZone": "UTC"
                }
            },
            "level": {
                "$resolver": "level",
                "field": "name"
            },
            "logger": {
                "$resolver": "logger",
                "field": "name"
            }
        }
    ```

3. Utilize este modelo de layout JSON no seu ficheiro config log4j2.xml. 

    ```json
        <configuration>
            <appenders>
                <console name="Console" target="SYSTEM_OUT">
                <JsonTemplateLayout eventTemplateUri="classpath:jsonTemplate.json"/>
                </console>
            </appenders>
            <loggers>
                <root level="info">
                <appender-ref ref="Console"/>
                </root>
            </loggers>
        </configuration>
    ```

## <a name="analyze-the-logs-in-log-analytics"></a>Analise os registos em Log Analytics

Após a configuração correta da sua aplicação, o registo da consola de aplicação será transmitido para Log Analytics. A estrutura permite uma consulta eficiente no Log Analytics.

### <a name="check-log-structure-in-log-analytics"></a>Verifique a estrutura do registo em Log Analytics
Utilize o seguinte procedimento:
1. Aceda à página geral do serviço da sua instância de serviço.
2. Clique `Logs` na entrada na `Monitoring` secção.
3. Executar esta consulta.

   ```
   AppPlatformLogsforSpring
   | where TimeGenerated > ago(1h)
   | project AppTimestamp, Logger, CustomLevel, Thread, Message, ExceptionClass, StackTrace, TraceId, SpanId
   ```

4. Os registos de aplicações devolvem-se como mostrado na seguinte imagem:

![Json Log show](media/spring-cloud-structured-app-log/json-log-query.png)

### <a name="show-log-entries-containing-errors"></a>Mostrar entradas de registo que contenham erros

Para rever as entradas de registo que tenham um erro, execute a seguinte consulta:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h) and CustomLevel == "ERROR" 
| project AppTimestamp, Logger, ExceptionClass, StackTrace, Message, AppName 
| sort by AppTimestamp
```

Utilize esta consulta para encontrar erros ou modificar os termos de consulta para encontrar uma classe de exceção específica ou código de erro. 

### <a name="show-log-entries-for-a-specific-traceid"></a>Mostrar entradas de registo para um traceId específico
Para rever as entradas de registo de um ID de rastreio específico "trace_id", executar a seguinte consulta:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where TraceId == "trace_id" 
| project AppTimestamp, Logger, TraceId, SpanId, StackTrace, Message, AppName 
| sort by AppTimestamp
```

## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre a Consulta de Log, consulte [Começar com consultas de log no Azure Monitor](../azure-monitor/logs/get-started-queries.md)