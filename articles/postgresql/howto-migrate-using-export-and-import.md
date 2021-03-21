---
title: Migrar uma base de dados - Base de Dados Azure para PostgreSQL - Servidor Único
description: Descreve como extrair uma base de dados PostgreSQL num ficheiro de script e importar os dados para a base de dados-alvo a partir desse ficheiro.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: d0f1824a1e4754dddea26484c836df79e95246d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580985"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migrar a sua base de dados PostgreSQL utilizando exportação e importação
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

Pode utilizar [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) para extrair uma base de dados PostgreSQL num ficheiro de script e [psql](https://www.postgresql.org/docs/current/static/app-psql.html) para importar os dados para a base de dados-alvo a partir desse ficheiro.

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia, precisa:
- Uma [Base de Dados Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md) com regras de firewall para permitir o acesso e base de dados sob o mesmo.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) utilitário de linha de comando instalado
- utilitário de linha de comando [psql](https://www.postgresql.org/docs/current/static/app-psql.html) instalado

Siga estes passos para exportar e importar a sua base de dados PostgreSQL.

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Crie um ficheiro de script usando pg_dump que contém os dados a serem carregados
Para exportar a sua base de dados PostgreSQL existente no local ou num VM para um ficheiro de script sql, execute o seguinte comando no seu ambiente existente:

```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Por exemplo, se tiver um servidor local e uma base de dados chamada **testdb:**
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importar os dados da base de dados target Azure para postgreSQL
Pode utilizar a linha de comando psql e o parâmetro --dbname (-d) para importar os dados para a Base de Dados Azure para o servidor PostgreSQL e carregar dados a partir do ficheiro sql.

```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user> --dbname=<target database name>
```
Este exemplo utiliza utilitário psql e um ficheiro de script nomeado **testdb.sql** do passo anterior para importar dados para a base de **dados mypgsqldb** no servidor alvo **mydemoserver.postgres.database.azure.com**.

Para **um servidor único,** utilize este comando 
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

Para **o Servidor Flexível,** utilize este comando  
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin --dbname=mypgsqldb
```



## <a name="next-steps"></a>Passos seguintes
- Para migrar uma base de dados PostgreSQL utilizando o despejo e restaurar, consulte [migrar a sua base de dados PostgreSQL utilizando o despejo e restaurar](howto-migrate-using-dump-and-restore.md).
- Para obter mais informações sobre bases de dados migratórias para a Base de Dados Azure para PostgreSQL, consulte o [Guia de Migração da Base de Dados.](https://aka.ms/datamigration)
