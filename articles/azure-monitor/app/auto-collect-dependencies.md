---
title: Azure Application Insights - a coleção automática de dependência | Documentos da Microsoft
description: O Application Insights automaticamente a recolha e visualize dependências
services: application-insights
documentationcenter: .net
author: nikmd23
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 08/13/2018
ms.reviewer: mbullwin
ms.author: nimolnar
ms.openlocfilehash: 7f152de89f5d374a5b97368eafe7287fc9377291
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011335"
---
# <a name="dependency-auto-collection"></a>Coleção automática da dependência

Segue-se a lista de chamadas de dependência que são detetados automaticamente como dependências sem a necessidade de quaisquer modificações adicionais para o código de seu aplicativo atualmente suportados. Isso consiste em chamadas de saída para as bibliotecas de comunicação, os clientes de armazenamento, registo e bibliotecas de métricas, bem como as chamadas de entrada em estruturas de aplicações e servidores. Estas dependências são visualizadas no Application Insights [mapa da aplicação](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) e [diagnósticos de transação](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) vistas. Se sua dependência não estiver na lista abaixo, pode ainda controlá-lo manualmente com um [controlar chamada de dependência](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="net"></a>.NET

| Estruturas de aplicações| Versões |
| ------------------------|----------|
| ASP.NET Webforms | 4.5+ |
| ASP.NET MVC | 4+ |
| ASP.NET WebAPI | 4.5+ |
| Núcleo de ASP.NET | 1.1+ |
| <b> Bibliotecas de comunicação</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5 +, .NET core 1.1 + |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET core 1.0 +, o NuGet versão 4.3.0 |
| [SDK de cliente de EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [SDK de cliente de ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Clientes de armazenamento</b>|  |
| ADO.NET | 4.5+ |
| <b>Bibliotecas de Registro em log</b> |  |
| ILogger | 1.1+ |
| System.Diagnostics.Trace | 4.5+ |
| [nLog](https://www.nuget.org/packages/NLog/) | 4.4.12+ |
| [log4net](https://www.nuget.org/packages/log4net/) | 2.0.8+ no NetStandard 1.3, 2.0.6+ no .NET 4.5 + |

## <a name="java"></a>Java
| Servidores de aplicações | Versões |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [O JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Estruturas de aplicações </b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9+<sup>*</sup> |
| Java Servlet | 3.1+ |
| <b>Bibliotecas de comunicação</b> |  |
| [Cliente de Http do Apache](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3+<sup>†</sup> |
| <b>Clientes de armazenamento</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1+<sup>†</sup> |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1+<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1+<sup>†</sup> |
| <b>Bibliotecas de Registro em log</b> | |
| [Logback](https://logback.qos.ch/) | 1+ |
| [Log4j](https://logging.apache.org/log4j/) | 1.2+ |
| <b>Bibliotecas de métricas</b> |  |
| JMX | 1.0+ |

> [!NOTE]
> * Exceto o suporte de programação reativa.
> <br>Instalação †requires [JVM agente](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java).

## <a name="nodejs"></a>Node.js

| Bibliotecas de comunicação | Versões |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html), [HTTPS](https://nodejs.org/api/https.html) | 0.10+ |
| <b>Clientes de armazenamento</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [Core de MongoDb](https://www.npmjs.com/package/mongodb-core) | 2.x - 3.x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0 - 2.16.x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6.x - 7.x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1.x - 2.x |
| <b>Bibliotecas de Registro em log</b> | |
| [console](https://nodejs.org/api/console.html) | 0.10+ |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x - 3.x |

## <a name="javascript"></a>JavaScript

| Bibliotecas de comunicação | Versões |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Todos |

## <a name="next-steps"></a>Passos Seguintes

- Configurar o controlo para dependência personalizadas [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Configurar o controlo para dependência personalizadas [Java](../../azure-monitor/app/java-agent.md).
- [Escrever telemetria de dependência personalizadas](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Ver [modelo de dados](../../azure-monitor/app/data-model.md) para o modelo de tipos e dados do Application Insights.
- Confira [plataformas](../../azure-monitor/app/platforms.md) suportada pelo Application Insights.
