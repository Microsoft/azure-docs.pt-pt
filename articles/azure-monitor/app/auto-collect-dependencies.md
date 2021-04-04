---
title: Azure Application Insights - Dependency Auto-Collection | Microsoft Docs
description: Application Insights recolhem e visualizam automaticamente dependências
ms.topic: reference
ms.custom: devx-track-dotnet
ms.date: 05/06/2020
ms.openlocfilehash: 8a4d79e52465e93fb4db2625217cb37a06917218
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91930871"
---
# <a name="dependency-auto-collection"></a>Coleção automática da dependência

Abaixo está a lista de chamadas de dependência atualmente suportadas que são automaticamente detetadas como dependências sem exigir qualquer modificação adicional ao código da sua aplicação. Estas dependências são visualizadas no mapa da [Aplicação](./app-map.md) insights e pontos de vista [de diagnóstico de transação.](./transaction-diagnostics.md) Se a sua dependência não estiver na lista abaixo, ainda pode rastreá-la manualmente com uma [chamada de dependência da faixa](./api-custom-events-metrics.md#trackdependency).

## <a name="net"></a>.NET

| Quadros de aplicativos| Versões |
| ------------------------|----------|
| formas web ASP.NET | 4.5+ |
| ASP.NET MVC | 4+ |
| ASP.NET WebAPI | 4.5+ |
| ASP.NET Core | 1.1+ |
| <b> Bibliotecas de comunicação</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5+, .NET Core 1.1+ |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0+, NuGet 4.3.0 |
| [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/1.1.2)| 1.1.0 - última libertação estável. (Ver nota abaixo.)
| [EventHubs Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus Client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Clientes de armazenamento</b>|  |
| ADO.NET | 4.5+ |

> [!NOTE]
> Existe um [problema conhecido](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1347) com versões mais antigas do Microsoft.Data.SqlClient. Recomendamos a utilização de 1.1.0 ou posterior para atenuar este problema. O Núcleo Quadro da Entidade não envia necessariamente com a mais recente versão estável do Microsoft.Data.SqlClient, pelo que aconselhamos a confirmar que está em pelo menos 1.1.0 para evitar este problema.   


## <a name="java"></a>Java
| Servidores de aplicações | Versões |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Quadros de aplicativos </b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9+<sup>*</sup> |
| Java Servlet | 3.1+ |
| <b>Bibliotecas de comunicação</b> |  |
| [Apache Http Cliente](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3+<sup>†</sup> |
| <b>Clientes de armazenamento</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1+<sup>†</sup> |
| [Postgresql (Suporte Beta)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1+<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1+<sup>†</sup> |
| <b>Bibliotecas de exploração madeireira</b> | |
| [Logback](https://logback.qos.ch/) | 1+ |
| [Log4j](https://logging.apache.org/log4j/) | 1.2+ |
| <b>Bibliotecas métricas</b> |  |
| JMX | 1.0+ |

> [!NOTE]
> *Exceto suporte de programação reativo.
> <br>†Requires instalação do [Agente JVM](./java-agent.md#install-the-application-insights-agent-for-java).

## <a name="nodejs"></a>Node.js

| Bibliotecas de comunicação | Versões |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html), [HTTPS](https://nodejs.org/api/https.html) | 0.10+ |
| <b>Clientes de armazenamento</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb;](https://www.npmjs.com/package/mongodb) [Núcleo mongoDb](https://www.npmjs.com/package/mongodb-core) | 2.x - 3.x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0 - 2.16.x |
| [PostgreSql;](https://www.npmjs.com/package/pg) | 6.x - 7.x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1.x - 2.x |
| <b>Bibliotecas de exploração madeireira</b> | |
| [consola](https://nodejs.org/api/console.html) | 0.10+ |
| [Rio Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x - 3.x |

## <a name="javascript"></a>JavaScript

| Bibliotecas de comunicação | Versões |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Todos |

## <a name="next-steps"></a>Passos seguintes

- Configurar o rastreio de dependência personalizada para [.NET](./asp-net-dependencies.md).
- Configurar o rastreio de dependência personalizada para [Java.](./java-agent.md)
- Configurar rastreio de dependência personalizada para [o OpenCensus Python](./opencensus-python-dependency.md).
- [Escrever telemetria de dependência personalizada](./api-custom-events-metrics.md#trackdependency)
- Consulte [o modelo de dados](./data-model.md) para os tipos de Insights de Aplicação e modelo de dados.
- Confira as [plataformas](./platforms.md) suportadas pela Application Insights.

