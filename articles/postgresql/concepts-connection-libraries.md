---
title: Bibliotecas de conexão - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve várias bibliotecas e controladores que pode utilizar ao codificar aplicações para ligar e consultar a Base de Dados Azure para PostgreSQL - Servidor Único.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 45081c6ba161686498398f2c4ccae8b4cff4c0d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91704315"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Bibliotecas de conexão para Base de Dados Azure para PostgreSQL - Servidor Único
Este artigo lista bibliotecas e controladores que os desenvolvedores podem usar para desenvolver aplicações para ligar e consultar a Base de Dados Azure para PostgreSQL.

## <a name="client-interfaces"></a>Interfaces de clientes
A maioria das bibliotecas de clientes de idiomas usadas para se conectar ao servidor PostgreSQL são projetos externos e são distribuídas de forma independente. As bibliotecas listadas são suportadas nas plataformas Windows, Linux e Mac, para ligação à Base de Dados Azure para PostgreSQL. Vários exemplos de arranque rápido estão listados na secção etapas seguintes.

| **Idioma** | **Interface de cliente** | **Informações adicionais** | **Transferência** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2.0 em conformidade | [Transferência](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Extensão da base de dados | [Instalar](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pacote pg npm](https://www.npmjs.com/package/pg) | Cliente javaScript puro que não bloqueia | [Instalar](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Condutor JDBC tipo 4 | [Baixar](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gema](https://deveiate.org/code/pg/) | Ruby Interface | [Transferência](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Ir | [Pacote pq](https://godoc.org/github.com/lib/pq) | Motorista de postgres Pure Go | [Instalar](https://github.com/lib/pq/blob/master/README.md) |
| C \# / .NET | [Npgsql](https://www.npgsql.org/) | Provedor de Dados ADO.NET | [Transferência](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Controlador ODBC | [Transferência](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Interface de idioma C primária | Incluída |
| C++ | [libpqxx](http://pqxx.org/) | Interface C++ de estilo novo | [Transferência](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Passos seguintes
Leia estes quickstarts sobre como ligar e consultar a Base de Dados Azure para PostgreSQL utilizando o seu idioma de eleição:

[Pitão](./connect-python.md)  |  [Node.JS](./connect-nodejs.md)  |  [Rio Java](./connect-java.md)  |  [Rio Ruby](./connect-ruby.md)  |  [PHP](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md)  |  [Vai](./connect-go.md)
