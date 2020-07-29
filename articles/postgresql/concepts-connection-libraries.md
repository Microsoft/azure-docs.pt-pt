---
title: Bibliotecas de conexão - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve várias bibliotecas e controladores que pode utilizar ao codificar aplicações para ligar e consultar a Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 2305a02e5b094ec9e98f39363ddbd0c39221ab0b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74768899"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Bibliotecas de conexão para Base de Dados Azure para PostgreSQL - Servidor Único
Este artigo lista bibliotecas e controladores que os desenvolvedores podem usar para desenvolver aplicações para ligar e consultar a Base de Dados Azure para PostgreSQL.

## <a name="client-interfaces"></a>Interfaces de clientes
A maioria das bibliotecas de clientes de idiomas usadas para se conectar ao servidor PostgreSQL são projetos externos e são distribuídas de forma independente. As bibliotecas listadas são suportadas nas plataformas Windows, Linux e Mac, para ligação à Base de Dados Azure para PostgreSQL. Vários exemplos de arranque rápido estão listados na secção etapas seguintes.

| **Linguagem** | **Interface de cliente** | **Informações adicionais** | **Transferência** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2.0 em conformidade | [Transferência](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Extensão da base de dados | [Instalar](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pacote pg npm](https://www.npmjs.com/package/pg) | Cliente javaScript puro que não bloqueia | [Instalar](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Condutor JDBC tipo 4 | [Baixar](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gema](https://deveiate.org/code/pg/) | Ruby Interface | [Transferência](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Ir | [Pacote pq](https://godoc.org/github.com/lib/pq) | Motorista de postgres Pure Go | [Instalar](https://github.com/lib/pq/blob/master/README.md) |
| C \# / .NET | [Npgsql](https://www.npgsql.org/) | Provedor de Dados ADO.NET | [Transferência](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Controlador ODBC | [Transferência](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Interface de idioma C primária | Incluídos |
| C++ | [libpqxx](http://pqxx.org/) | Interface C++ de estilo novo | [Transferência](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Próximos passos
Leia estes quickstarts sobre como ligar e consultar a Base de Dados Azure para PostgreSQL utilizando o seu idioma de eleição:

[Pitão](./connect-python.md)  |  [Node.JS](./connect-nodejs.md)  |  [Rio Java](./connect-java.md)  |  [Rio Ruby](./connect-ruby.md)  |  [PHP](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md)  |  [Vai](./connect-go.md)
