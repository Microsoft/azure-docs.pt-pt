---
title: Insights de Aplicação Azure - Dependência Auto-Collection [ Dependência Auto-Coleção ] Microsoft Docs
description: Insights de aplicação recolhem e visualizam automaticamente dependências
ms.topic: reference
author: nikmd23
ms.author: nimolnar
ms.date: 04/29/2019
ms.reviewer: mbullwin
ms.openlocfilehash: eaafe19f5112b433d50a34aa551aa84d196726a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77665821"
---
# <a name="dependency-auto-collection"></a>Coleção automática da dependência

Abaixo está a lista de chamadas de dependência suportadas atualmente que são automaticamente detetadas como dependências sem exigir qualquer modificação adicional ao código da sua aplicação. Estas dependências são visualizadas no mapa de aplicação de insights de [aplicação](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) e vistas de [diagnóstico de transações.](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) Se a sua dependência não estiver na lista abaixo, ainda pode rastreá-la manualmente com uma chamada de dependência da [via.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)

## <a name="net"></a>.NET

| Quadros de aplicativos| Versões |
| ------------------------|----------|
| ASP.NET Webforms | 4.5+ |
| ASP.NET MVC | 4+ |
| ASP.NET WebAPI | 4.5+ |
| Núcleo de ASP.NET | 1.1+ |
| <b>Bibliotecas de comunicação</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5+, .NET Core 1.1+ |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0+, NuGet 4.3.0 |
| [EventHubs Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus Client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Clientes de armazenamento</b>|  |
| ADO.NET | 4.5+ |

## <a name="java"></a>Java
| Servidores de aplicações | Versões |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Cais](https://www.eclipse.org/jetty/) | 9 |
| <b>Quadros de aplicativos</b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9+<sup>*</sup> |
| Java Servlet | 3.1+ |
| <b>Bibliotecas de comunicação</b> |  |
| [Cliente Apache Http](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3+<sup>†</sup> |
| <b>Clientes de armazenamento</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1+<sup>†</sup> |
| [PostgreSQL (Suporte Beta)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1+<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1+<sup>†</sup> |
| <b>Bibliotecas de exploração madeireira</b> | |
| [Logback](https://logback.qos.ch/) | 1+ |
| [Log4j](https://logging.apache.org/log4j/) | 1.2+ |
| <b>Bibliotecas métricas</b> |  |
| JMX | 1.0+ |

> [!NOTE]
> *Exceto suporte de programação reativo.
> <br>†Requer a instalação do [Agente JVM](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java).

## <a name="nodejs"></a>Node.js

| Bibliotecas de comunicação | Versões |
| ------------------------|----------|
| [HTTP,](https://nodejs.org/api/http.html) [HTTPS](https://nodejs.org/api/https.html) | 0,10+ |
| <b>Clientes de armazenamento</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb;](https://www.npmjs.com/package/mongodb) [Núcleo MongoDb](https://www.npmjs.com/package/mongodb-core) | 2.x - 3.x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0 - 2.16.x |
| [PostgreSql;](https://www.npmjs.com/package/pg) | 6.x - 7.x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1.x - 2.x |
| <b>Bibliotecas de exploração madeireira</b> | |
| [consola](https://nodejs.org/api/console.html) | 0,10+ |
| [Rio Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x - 3.x |

## <a name="javascript"></a>JavaScript

| Bibliotecas de comunicação | Versões |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Todos |

## <a name="next-steps"></a>Passos seguintes

- Configurar o rastreio de dependência personalizada para [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Configurar o rastreio de dependência personalizada para [Java.](../../azure-monitor/app/java-agent.md)
- Configurar o rastreio de dependência personalizada para [OpenCensus Python](../../azure-monitor/app/opencensus-python-dependency.md).
- [Escreva telemetria de dependência personalizada](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Consulte o modelo de [dados](../../azure-monitor/app/data-model.md) para tipos de Insights de Aplicação e modelo de dados.
- Confira [as plataformas](../../azure-monitor/app/platforms.md) suportadas por Application Insights.
