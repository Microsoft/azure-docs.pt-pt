---
title: Despejo e restauro - Base de Dados Azure para PostgreSQL - Servidor Único
description: Pode extrair uma base de dados PostgreSQL num ficheiro de despejo. Em seguida, pode restaurar a partir de um ficheiro criado por pg_dump na Base de Dados Azure para o Servidor Único Pós-SQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 809ff06fe460a06a689d7bbc11cdbd5ee247f585
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450060"
---
# <a name="migrate-your-postgresql-database-by-using-dump-and-restore"></a>Migrar a sua base de dados PostgreSQL utilizando o despejo e restaurar
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

Pode utilizar [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) para extrair uma base de dados PostgreSQL num ficheiro de despejo. Em seguida, utilize [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) para restaurar a base de dados PostgreSQL a partir de um ficheiro de arquivo criado por `pg_dump` .

## <a name="prerequisites"></a>Pré-requisitos

Para passar por este guia, precisa:
- Uma [base de dados Azure para servidor PostgreSQL,](quickstart-create-server-database-portal.md)incluindo regras de firewall para permitir o acesso.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) serviços de linha de comando instalados.

## <a name="create-a-dump-file-that-contains-the-data-to-be-loaded"></a>Crie um ficheiro de despejo que contenha os dados a serem carregados

Para fazer o apoio a uma base de dados postgreSQL existente no local ou num VM, executar o seguinte comando:

```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Por exemplo, se tiver um servidor local e uma base de dados chamada **testdb** nele, corra:

```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```

## <a name="restore-the-data-into-the-target-database"></a>Restaurar os dados na base de dados-alvo

Depois de criar a base de dados-alvo, pode usar o `pg_restore` comando e o parâmetro para restaurar os  `--dbname` dados na base de dados-alvo a partir do ficheiro de despejo.

```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user-name> --dbname=<target database name> <database>.dump
```

A inclusão do `--no-owner` parâmetro faz com que todos os objetos criados durante a restauração sejam propriedade do utilizador especificado com `--username` . Para mais informações, consulte a [documentação PostgreSQL.](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html)

> [!NOTE]
> Na Base de Dados Azure para servidores PostgreSQL, as ligações TLS/SSL estão ligadas por predefinição. Se o seu servidor PostgreSQL necessitar de ligações TLS/SSL, mas não as tiver, desagre uma variável ambiental `PGSSLMODE=require` para que a ferramenta pg_restore se conecte com o TLS. Sem TLS, o erro pode ler-se: "FATAL: É necessária uma ligação SSL. Por favor, especifique as opções SSL e redaça." Na linha de comando do Windows, executa o comando `SET PGSSLMODE=require` antes de executar o `pg_restore` comando. Em Linux ou Bash, executar o comando `export PGSSLMODE=require` antes de executar o `pg_restore` comando. 
>

Neste exemplo, restaurar os dados do teste de ficheiro de **despejo.despeje** na base de **dados mypgsqldb**, no servidor alvo **mydemoserver.postgres.database.azure.com**.

Aqui está um exemplo para como usar isto `pg_restore` para Single Server:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

Aqui está um exemplo para como usar isto `pg_restore` para o Servidor Flexível:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin --dbname=mypgsqldb testdb.dump
```

## <a name="optimize-the-migration-process"></a>Otimizar o processo de migração

Uma forma de migrar a sua base de dados postgreSQL existente para a Base de Dados Azure para PostgreSQL é fazer o back-up na fonte e restaurá-la em Azure. Para minimizar o tempo necessário para completar a migração, considere utilizar os seguintes parâmetros com os comandos de backup e restauro.

> [!NOTE]
> Para obter informações detalhadas sobre sintaxe, consulte [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Para o backup

Pegue a cópia de segurança com o `-Fc` interruptor, para que possa executar a restauração paralelamente para a acelerar. Por exemplo:

```bash
pg_dump -h my-source-server-name -U source-server-username -Fc -d source-databasename -f Z:\Data\Backups\my-database-backup.dump
```

### <a name="for-the-restore"></a>Para a restauração

- Mova o ficheiro de backup para um Azure VM na mesma região que a Base de Dados Azure para o servidor PostgreSQL para o que está a migrar. Execute o `pg_restore` a partir desse VM para reduzir a latência da rede. Crie o VM com [rede acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md) ativada.

- Abra o ficheiro de despejo para verificar se as declarações de índice de criação são após a inserção dos dados. Se não for o caso, mova as declarações de índice de criação após a inserção dos dados. Isto já deve ser feito por defeito, mas é uma boa ideia confirmar.

- Restaurar com os interruptores `-Fc` e `-j` (com um número) para paralelizar o restauro. O número especificado é o número de núcleos no servidor alvo. Também pode definir para o dobro do número de núcleos do servidor alvo para ver o impacto.

    Aqui está um exemplo para como usar isto `pg_restore` para Single Server:

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

    Aqui está um exemplo para como usar isto `pg_restore` para o Servidor Flexível:

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

- Também pode editar o ficheiro de despejo adicionando o comando `set synchronous_commit = off;` no início e o comando no `set synchronous_commit = on;` final. Não ligá-lo no final, antes que as aplicações alterem os dados, pode resultar na perda subsequente de dados.

- Na base de dados Azure alvo para o servidor PostgreSQL, considere fazer o seguinte antes da restauração:
    
  - Desligue o rastreio de desempenho da consulta. Estas estatísticas não são necessárias durante a migração. Pode fazê-lo definindo `pg_stat_statements.track` `pg_qs.query_capture_mode` , e para `pgms_wait_sampling.query_capture_mode` `NONE` .

  - Use um SKU de alta computação e alta memória, como 32 vCore Memory Otimizado, para acelerar a migração. Pode facilmente escalar de volta para o seu SKU preferido depois que o restauro estiver completo. Quanto maior for o SKU, mais paralelismo poderá obter aumentando o parâmetro correspondente `-j` no `pg_restore` comando.

  - Mais IOPS no servidor alvo pode melhorar o desempenho do restauro. Pode providenciar mais IOPS aumentando o tamanho de armazenamento do servidor. Esta definição não é reversível, mas considere se um IOPS mais elevado beneficiaria a sua carga de trabalho real no futuro.

Lembre-se de testar e validar estes comandos num ambiente de teste antes de os utilizar na produção.

## <a name="next-steps"></a>Passos seguintes

- Para migrar uma base de dados PostgreSQL utilizando a exportação e a importação, consulte [a Migração da sua base de dados PostgreSQL utilizando a exportação e a importação.](howto-migrate-using-export-and-import.md)
- Para obter mais informações sobre bases de dados migratórias para a Base de Dados Azure para PostgreSQL, consulte o [Guia de Migração da Base de Dados.](https://aka.ms/datamigration)


