---
title: Bibliotecas de conexões-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve várias bibliotecas e drivers que você pode usar ao codificar aplicativos para se conectar e consultar o banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 2305a02e5b094ec9e98f39363ddbd0c39221ab0b
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768899"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Bibliotecas de conexões do banco de dados do Azure para PostgreSQL-servidor único
Este artigo lista as bibliotecas e os drivers que os desenvolvedores podem usar para desenvolver aplicativos para se conectar e consultar o banco de dados do Azure para PostgreSQL.

## <a name="client-interfaces"></a>Interfaces de cliente
A maioria das bibliotecas de cliente de idioma usadas para se conectar ao servidor PostgreSQL são projetos externos e são distribuídas de forma independente. As bibliotecas listadas têm suporte nas plataformas Windows, Linux e Mac, para se conectar ao banco de dados do Azure para PostgreSQL. Vários exemplos de início rápido estão listados na seção próximas etapas.

| **Language** (Idioma) | **Interface do cliente** | **Informações adicionais** | **Transferência** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Compatível com o DB API 2,0 | [Transferência](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Extensão do banco de dados | [Instalar](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pacote NPM PG](https://www.npmjs.com/package/pg) | Cliente puro de não bloqueio de JavaScript | [Instalar](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Driver JDBC do tipo 4 | [Transferência](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Gem PG](https://deveiate.org/code/pg/) | Interface Ruby | [Transferência](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Pacote pq](https://godoc.org/github.com/lib/pq) | Driver go postgres puro | [Instalar](https://github.com/lib/pq/blob/master/README.md) |
| C\#/.NET | [Npgsql](https://www.npgsql.org/) | Provedor de Dados ADO.NET | [Transferência](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Controlador ODBC | [Transferência](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Interface de linguagem C primária | Incluído |
| C++ | [libpqxx](http://pqxx.org/) | Nova interface de C++ estilo | [Transferência](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Passos seguintes
Leia estes guias de início rápido sobre como se conectar e consultar o banco de dados do Azure para PostgreSQL usando a linguagem de sua escolha:

[Python](./connect-python.md) | [node. js](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [php](./connect-php.md) | o [.NETC#()](./connect-csharp.md) | [go](./connect-go.md)
