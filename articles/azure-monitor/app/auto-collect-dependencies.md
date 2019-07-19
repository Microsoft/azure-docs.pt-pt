---
title: Aplicativo Azure insights – coleção de dependências automática | Microsoft Docs
description: Application Insights coletar e Visualizar automaticamente as dependências
services: application-insights
documentationcenter: .net
author: nikmd23
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 04/29/2019
ms.reviewer: mbullwin
ms.author: nimolnar
ms.openlocfilehash: 839ab291a99de646053b638520ce43f459d5c41f
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297020"
---
# <a name="dependency-auto-collection"></a>Coleção automática da dependência

Abaixo está a lista de chamadas de dependência com suporte no momento que são detectadas automaticamente como dependências sem a necessidade de qualquer modificação adicional no código do aplicativo. Essas dependências são visualizadas no [mapa do aplicativo](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) Application insights e exibições de [diagnóstico de transação](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) . Se sua dependência não estiver na lista abaixo, você ainda poderá rastreá-la manualmente com uma [chamada de dependência de faixa](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="net"></a>.NET

| Estruturas de aplicativo| Versões |
| ------------------------|----------|
| WebForms do ASP.NET | 4.5 + |
| ASP.NET MVC | 4 + |
| ASP.NET WebAPI | 4.5 + |
| Núcleo de ASP.NET | 1.1 + |
| <b>Bibliotecas de comunicação</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5 +, .NET Core 1.1 + |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0 +, NuGet 4.3.0 |
| [SDK do cliente do EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [SDK do cliente do ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Clientes de armazenamento</b>|  |
| ADO.NET | 4.5 + |

## <a name="java"></a>Java
| Servidores de aplicativos | Versões |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [EAP JBoss](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Estruturas de aplicativo</b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring boot](https://spring.io/projects/spring-boot) | 1.5.9 +<sup>*</sup> |
| Servlet Java | 3.1 + |
| <b>Bibliotecas de comunicação</b> |  |
| [Cliente http do Apache](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3 +<sup>†</sup> |
| <b>Clientes de armazenamento</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1 +<sup>†</sup> |
| [PostgreSQL (suporte beta)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1 +<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1 +<sup>†</sup> |
| <b>Bibliotecas de log</b> | |
| [Logback](https://logback.qos.ch/) | 1 + |
| [Log4j](https://logging.apache.org/log4j/) | 1.2 + |
| <b>Bibliotecas de métricas</b> |  |
| JMX | 1.0 + |

> [!NOTE]
> \* Exceto o suporte a programas reativos.
> <br>† Requer a instalação do [agente JVM](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java).

## <a name="nodejs"></a>Node.js

| Bibliotecas de comunicação | Versões |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html), [HTTPS](https://nodejs.org/api/https.html) | 0,10 + |
| <b>Clientes de armazenamento</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDB](https://www.npmjs.com/package/mongodb); [Núcleo do MongoDB](https://www.npmjs.com/package/mongodb-core) | 2. x-3. x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0-2,16. x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6. x-7. x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1. x-2. x |
| <b>Bibliotecas de log</b> | |
| [console](https://nodejs.org/api/console.html) | 0,10 + |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2. x-3. x |

## <a name="javascript"></a>JavaScript

| Bibliotecas de comunicação | Versões |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Todos |

## <a name="next-steps"></a>Passos Seguintes

- Configure o rastreamento de dependência personalizado para [.net](../../azure-monitor/app/asp-net-dependencies.md).
- Configure o acompanhamento de dependência personalizado para [Java](../../azure-monitor/app/java-agent.md).
- [Gravar telemetria de dependência personalizada](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Consulte [modelo de dados](../../azure-monitor/app/data-model.md) para tipos de Application insights e modelo de dados.
- Confira as [plataformas](../../azure-monitor/app/platforms.md) com suporte pelo Application insights.
