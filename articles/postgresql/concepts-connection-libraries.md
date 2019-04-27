---
title: Bibliotecas de ligação para base de dados do Azure para PostgreSQL
description: Este artigo descreve várias bibliotecas e controladores que os desenvolvedores podem usar quando codificar aplicativos para se ligar e consultar a base de dados do Azure para PostgreSQL.
services: postgresql
author: WenJason
ms.author: v-jay
manager: digimobile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
origin.date: 02/28/2018
ms.date: 12/03/2018
ms.openlocfilehash: 0e762a2d7cf82e2957fb276fcea0a20553f719e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60559777"
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Bibliotecas de ligação para base de dados do Azure para PostgreSQL
Este artigo apresenta uma lista de bibliotecas e controladores que os desenvolvedores podem usar para desenvolver aplicativos para ligar e consultar a base de dados do Azure para PostgreSQL.

## <a name="client-interfaces"></a>Interfaces de cliente
A maioria das bibliotecas de cliente de linguagem utilizada para ligar ao servidor PostgreSQL são projetos de externos e são distribuídos de forma independente. As bibliotecas listadas são suportadas nas plataformas Windows, Linux e Mac, para ligar à base de dados do Azure para PostgreSQL. Vários exemplos de início rápido estão listados na secção passos seguintes.

| **Language** (Idioma) | **Interface do cliente** | **Informações adicionais** | **Transferência** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | API de DB 2.0 compatível | [Transferência](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Extensão de base de dados | [Instalar](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pacote de npm do PG](https://www.npmjs.com/package/pg) | Cliente de sem bloqueio de JavaScript pura | [Instalar](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Driver JDBC de tipo 4 | [Transferência](https://jdbc.postgresql.org/download.html)  |
| Ruby | [PG gem](https://deveiate.org/code/pg/) | Ruby Interface | [Transferência](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Ir | [Pacote pq](https://godoc.org/github.com/lib/pq) | Pura Go postgres driver | [Instalar](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](https://www.npgsql.org/) | Fornecedor de dados ADO.NET | [Transferência](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Controlador ODBC | [Transferência](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Interface de idioma primário do C | Incluída |
| C++ | [libpqxx](http://pqxx.org/) | Interface de C++ novo estilo | [Transferência](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Passos Seguintes
Leia estes guias de introdução sobre como ligar e consultar a base de dados do Azure para PostgreSQL, utilizando a linguagem de sua escolha:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
