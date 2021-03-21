---
title: Despejo e restauro - Base de Dados Azure para PostgreSQL - Servidor Único
description: Descreve como extrair uma base de dados PostgreSQL num ficheiro de despejo e restaurar a partir de um ficheiro criado por pg_dump na Base de Dados Azure para PostgreSQL - Servidor Único.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 16166183b56b371fe8338894f83dbacf2e659c53
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563560"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migrar a base de dados de PostgreSQL através da captura e do restauro
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

Pode utilizar [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) para extrair uma base de dados PostgreSQL num ficheiro de despejo e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) restaurar a base de dados PostgreSQL a partir de um ficheiro de arquivo criado por pg_dump.

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia, precisa:
- Uma [Base de Dados Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md) com regras de firewall para permitir o acesso e base de dados sob o mesmo.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) serviços de linha de comando instalados

Siga estes passos para despejar e restaurar a sua base de dados PostgreSQL:

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Crie um ficheiro de despejo utilizando pg_dump que contenha os dados a serem carregados
Para fazer o apoio a uma base de dados postgreSQL existente no local ou num VM, executar o seguinte comando:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Por exemplo, se tiver um servidor local e uma base de dados chamada **testdb** nele
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postgresql-using-pg_restore"></a>Restaurar os dados na base de dados Azure alvo para postgreSQL usando pg_restore
Depois de criar a base de dados alvo, pode usar o comando pg_restore e o parâmetro -dbname para restaurar os dados na base de dados-alvo a partir do ficheiro de despejo.
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user-name> --dbname=<target database name> <database>.dump
```

A inclusão do parâmetro --no-proprietário faz com que todos os objetos criados durante a restauração sejam propriedade do utilizador especificado com nome de utilizador. Para mais informações, consulte a documentação oficial do PostgreSQL sobre [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Se o seu servidor PostgreSQL necessitar de ligações TLS/SSL (por padrão na Base de Dados Azure para servidores PostgreSQL), desagreja uma variável de ambiente `PGSSLMODE=require` de modo a que a ferramenta pg_restore se conecte com o TLS. Sem TLS, o erro pode ler  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> Na linha de comando do Windows, executa o comando `SET PGSSLMODE=require` antes de executar o comando pg_restore. Em Linux ou Bash executar o comando `export PGSSLMODE=require` antes de executar o comando pg_restore.
>

Neste exemplo, restaurar os dados do teste de ficheiro de **despejo.despeje** na base de **dados mypgsqldb** no servidor alvo **mydemoserver.postgres.database.azure.com**.

Aqui está um exemplo para como usar este **pg_restore** para **Servidor Único**:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```
Aqui está um exemplo para como usar este **pg_restore** para **Servidor Flexível**:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin --dbname=mypgsqldb testdb.dump
```
---

## <a name="optimizing-the-migration-process"></a>Otimização do processo de migração

Uma forma de migrar a sua base de dados postgreSQL existente para a Azure Database for PostgreSQL é fazer o back-up na fonte e restaurá-la em Azure. Para minimizar o tempo necessário para completar a migração, considere utilizar os seguintes parâmetros com os comandos de backup e restauro.

> [!NOTE]
> Para obter informações detalhadas sobre sintaxe, consulte os artigos [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Para o backup
- Pegue a cópia de segurança com o interruptor -Fc para que possa executar a restauração paralelamente para acelerá-la. Por exemplo:

    ```bash
    pg_dump -h my-source-server-name -U source-server-username -Fc -d source-databasename -f Z:\Data\Backups\my-database-backup.dump
    ```

### <a name="for-the-restore"></a>Para a restauração
- Sugerimos que mova o ficheiro de backup para um VM Azure na mesma região que a Base de Dados Azure para o servidor PostgreSQL para a que está a migrar, e faça o pg_restore desse VM para reduzir a latência da rede. Recomendamos também que o VM seja criado com [uma rede acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md) ativada.

- Já deve ser feito por padrão, mas abra o ficheiro de despejo para verificar se as declarações de índice de criação são após a inserção dos dados. Se não for o caso, mova as declarações de índice de criação após a inserção dos dados.

- Restaurar com os interruptores -Fc e -j *#* para paralelizar o restauro. *#* é o número de núcleos no servidor alvo. Também pode tentar com *#* o dobro do número de núcleos do servidor alvo para ver o impacto. Por exemplo:

Aqui está um exemplo para como usar este **pg_restore** para **Servidor Único**:
```bash
 pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
```
Aqui está um exemplo para como usar este **pg_restore** para **Servidor Flexível**:
```bash
 pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
 ```

- Também pode editar o ficheiro de despejo adicionando o conjunto de comando *synchronous_commit = desligado;* no início e o conjunto de comando *synchronous_commit = ligado; no* final. Não ligá-lo no final, antes que as aplicações alterem os dados, pode resultar na perda subsequente de dados.

- Na base de dados Azure alvo para o servidor PostgreSQL, considere fazer o seguinte antes da restauração:
    - Desligue o rastreio do desempenho da consulta, uma vez que estas estatísticas não são necessárias durante a migração. Pode fazê-lo definindo pg_stat_statements.track, pg_qs.query_capture_mode e pgms_wait_sampling.query_capture_mode a NONE.

    - Use um sku de alta computação e memória alta, como 32 vCore Memory Otimizado, para acelerar a migração. Pode facilmente escalar de volta para a sua sku preferida depois que o restauro estiver completo. Quanto maior for o sku, mais paralelismo poderá obter aumentando o parâmetro correspondente `-j` no comando pg_restore.

    - Mais IOPS no servidor alvo poderia melhorar o desempenho do restauro. Pode providenciar mais IOPS aumentando o tamanho de armazenamento do servidor. Esta definição não é reversível, mas considere se um IOPS mais elevado beneficiaria a sua carga de trabalho real no futuro.

Lembre-se de testar e validar estes comandos num ambiente de teste antes de os utilizar na produção.

## <a name="next-steps"></a>Passos seguintes
- Para migrar uma base de dados PostgreSQL utilizando exportação e importação, consulte [a Migração da sua base de dados PostgreSQL utilizando exportação e importação.](howto-migrate-using-export-and-import.md)
- Para obter mais informações sobre bases de dados migratórias para a Base de Dados Azure para PostgreSQL, consulte o [Guia de Migração da Base de Dados.](https://aka.ms/datamigration)
