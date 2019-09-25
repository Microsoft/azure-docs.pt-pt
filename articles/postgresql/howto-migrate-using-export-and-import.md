---
title: Migrar um banco de dados usando importação e exportação no banco de dados do Azure para PostgreSQL-servidor único
description: Descreve como extrair um banco de dados PostgreSQL em um arquivo de script e importá-los para o banco de dado de destino desse arquivo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 0803f56312ca9b650987c2203c4271cff21df9f8
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260366"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migrar seu banco de dados PostgreSQL usando exportar e importar
Você pode usar [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) para extrair um banco de dados PostgreSQL em um arquivo de script e [psql](https://www.postgresql.org/docs/current/static/app-psql.html) para importar os dados para o banco de dado de destino desse arquivo.

## <a name="prerequisites"></a>Pré-requisitos
Para percorrer este guia de instruções, você precisa de:
- Um [banco de dados do Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md) com regras de firewall para permitir o acesso e o banco de dados sob ele.
- utilitário de linha de comando [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) instalado
- utilitário de linha de comando [psql](https://www.postgresql.org/docs/current/static/app-psql.html) instalado

Siga estas etapas para exportar e importar seu banco de dados PostgreSQL.

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Criar um arquivo de script usando pg_dump que contém os dados a serem carregados
Para exportar o banco de dados PostgreSQL existente localmente ou em uma VM para um arquivo de script SQL, execute o seguinte comando em seu ambiente existente:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Por exemplo, se você tiver um servidor local e um banco de dados chamado **TestDB** nele:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importar os dados no banco de dado de destino do Azure para PostgreSQL
Você pode usar a linha de comando psql e o parâmetro--dbname (-d) para importar os dados para o servidor do banco de dado do Azure para PostgreSQL e carregar dados do arquivo SQL.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
Este exemplo usa o utilitário psql e um arquivo de script chamado **TestDB. SQL** da etapa anterior para importar dados para o banco de **mypgsqldb** no servidor de destino **mydemoserver.Postgres.Database.Azure.com**.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>Passos seguintes
- Para migrar um banco de dados PostgreSQL usando despejo e restauração, consulte [migrar seu banco de dados PostgreSQL usando despejo e restauração](howto-migrate-using-dump-and-restore.md).
- Para obter mais informações sobre como migrar bancos de dados do para o Azure para PostgreSQL, consulte o [Guia de migração de banco de dados](https://aka.ms/datamigration). 
