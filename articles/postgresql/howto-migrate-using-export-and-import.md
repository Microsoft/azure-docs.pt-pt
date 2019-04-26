---
title: Migrar uma base de dados com a importação e exportação na base de dados do Azure para PostgreSQL
description: Descreve como extrair uma base de dados do PostgreSQL para um ficheiro de script e importar os dados para a base de dados de destino desse ficheiro.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/01/2018
ms.openlocfilehash: ecd7dc225379fc9d3eda6fb2e80e3c47a73db49b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422343"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migrar a sua base de dados do PostgreSQL com exportar e importar
Pode usar [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) para extrair um banco de dados do PostgreSQL para um ficheiro de script e [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) para importar os dados para a base de dados de destino desse ficheiro.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, terá de:
- Uma [base de dados do Azure para o servidor PostgreSQL](quickstart-create-server-database-portal.md) com regras de firewall para permitir o acesso e a base de dados sob a mesma.
- [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) ter instalado o utilitário da linha de comandos
- [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) ter instalado o utilitário da linha de comandos

Siga estes passos para exportar e importar a sua base de dados PostgreSQL.

## <a name="create-a-script-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Crie um ficheiro de script com pg_dump que contém os dados a ser carregado
Para exportar o PostgreSQL da base de dados no local ou numa VM para um ficheiro de script de sql, execute o seguinte comando no seu ambiente existente:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Por exemplo, se tiver um servidor local e uma base de dados chamado **testdb** no mesmo:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importar os dados no destino da base de dados do Azure para PostgreSQL
Pode usar a linha de comandos psql e o parâmetro – dbname (-1!d) para importar os dados para a base de dados do Azure para PostgreSQL server e carregar dados do ficheiro sql.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
Este exemplo utiliza o utilitário psql e um ficheiro de script denominado **testdb.sql** do passo anterior para importar dados para a base de dados **mypgsqldb** no servidor de destino  **mydemoserver.postgres.Database.Azure.com**.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>Passos Seguintes
- Para migrar uma base de dados do PostgreSQL com a captura e restauro, veja [migrar a sua base de dados do PostgreSQL com a captura e restauro](howto-migrate-using-dump-and-restore.md).
- Para obter mais informações sobre como migrar bases de dados para a base de dados do Azure para PostgreSQL, veja a [guia de migração de bases de dados](https://aka.ms/datamigration). 
