---
title: Bibliotecas de ligação - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve várias bibliotecas e controladores que pode utilizar quando codifica aplicações para ligar e consultar a Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 2305a02e5b094ec9e98f39363ddbd0c39221ab0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768899"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Bibliotecas de ligação para base de dados Azure para PostgreSQL - Servidor Único
Este artigo lista bibliotecas e controladores que os desenvolvedores podem usar para desenvolver aplicações para ligar e consultar a Base de Dados Azure para PostgreSQL.

## <a name="client-interfaces"></a>Interfaces de cliente
A maioria das bibliotecas de clientes linguísticos usados para se conectar ao servidor PostgreSQL são projetos externos e são distribuídos de forma independente. As bibliotecas listadas são suportadas nas plataformas Windows, Linux e Mac, para a ligação à Base de Dados Azure para PostgreSQL. Vários exemplos de arranque rápido estão listados na secção Passos Seguintes.

| **Língua** | **Interface do cliente** | **Informações adicionais** | **Transferir** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2.0-compliant | [Transferir](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Extensão da base de dados | [Instalar](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pacote PG npm](https://www.npmjs.com/package/pg) | Cliente não bloqueado rêmeo do JavaScript puro | [Instalar](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Condutor JDBC tipo 4 | [Baixar](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gema](https://deveiate.org/code/pg/) | Interface Rubi | [Transferir](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Ir | [Pacote pq](https://godoc.org/github.com/lib/pq) | Motorista de postes Pure Go | [Instalar](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](https://www.npgsql.org/) | Fornecedor de dados ADO.NET | [Transferir](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Controlador ODBC | [Transferir](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Interface linguística c primária | Incluído |
| C++ | [libpqxx](http://pqxx.org/) | Interface C++ de estilo novo | [Transferir](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Passos seguintes
Leia estes quickstarts sobre como ligar e consultar a Base de Dados Azure para PostgreSQL utilizando a sua linguagem de eleição:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
