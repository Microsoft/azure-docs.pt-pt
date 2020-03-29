---
title: Migrar uma base de dados - Base de Dados Azure para PostgreSQL - Servidor Único
description: Descreve como extrai uma base de dados PostgreSQL num ficheiro de script e importa os dados para a base de dados alvo desse ficheiro.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: f7cf5d245383b8a58f03e2e3610750866a2f4b5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770208"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migrar a sua base de dados PostgreSQL utilizando exportação e importação
Pode utilizar [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) para extrair uma base de dados PostgreSQL num ficheiro de script e [psql](https://www.postgresql.org/docs/current/static/app-psql.html) para importar os dados para a base de dados alvo desse ficheiro.

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia de como guiar, você precisa:
- Uma Base de [Dados Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md) com regras de firewall para permitir o acesso e a base de dados sob o mesmo.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) utilitário de linha de comando instalado
- utilitário de linha de comando [psql](https://www.postgresql.org/docs/current/static/app-psql.html) instalado

Siga estes passos para exportar e importar a sua base de dados PostgreSQL.

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Crie um ficheiro script usando pg_dump que contenha os dados a serem carregados
Para exportar a sua base de dados PostgreSQL existente no local ou num VM para um ficheiro de script sql, execute o seguinte comando no seu ambiente existente:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Por exemplo, se tiver um servidor local e uma base de dados chamada **testdb** no mesmo:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importar os dados sobre a base de dados azure-alvo para postgreSQL
Pode utilizar a linha de comando psql e o parâmetro --dbname (-d) para importar os dados para a Base de Dados Azure para o servidor PostgreSQL e carregar dados a partir do ficheiro sql.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
Este exemplo utiliza utilitário psql e um ficheiro script chamado **testdb.sql** de passo anterior para importar dados para a base de dados **mypgsqldb** no servidor alvo **mydemoserver.postgres.database.azure.com**.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>Passos seguintes
- Para migrar uma base de dados PostgreSQL utilizando despejo e restauro, consulte migrar a sua base de [dados PostgreSQL utilizando lixeira e restauro](howto-migrate-using-dump-and-restore.md).
- Para mais informações sobre bases de dados migratórias para a Base de Dados Azure para PostgreSQL, consulte o Guia de [Migração da Base de Dados](https://aka.ms/datamigration). 
