---
title: Despejo e restauro - Base de Dados Azure para PostgreSQL - Servidor Único
description: Descreve como extrair uma base de dados PostgreSQL num ficheiro de despejo e restaurar a partir de um ficheiro criado por pg_dump na Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 4365338efa56593e80edcc19cba5944b213d2b72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770242"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migrar a base de dados de PostgreSQL através da captura e do restauro
Pode utilizar [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) para extrair uma base de dados PostgreSQL num ficheiro de despejo e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) restaurar a base de dados PostgreSQL a partir de um ficheiro de arquivo criado por pg_dump.

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia de como guiar, você precisa:
- Uma Base de [Dados Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md) com regras de firewall para permitir o acesso e a base de dados sob o mesmo.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) utilitários de linha de comando instalados

Siga estes passos para despejar e restaurar a sua base de dados PostgreSQL:

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Crie um ficheiro de despejo utilizando pg_dump que contenha os dados a serem carregados
Para fazer o apoio a uma base de dados postgresql existente no local ou num VM, executar o seguinte comando:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Por exemplo, se tiver um servidor local e uma base de dados chamada **testdb** nele
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postgresql-using-pg_restore"></a>Restaurar os dados na base de dados azure-alvo para postgreSQL utilizando pg_restore
Depois de criar a base de dados do alvo, pode utilizar o comando pg_restore e o parâmetro-d,-dbname para restaurar os dados na base de dados do alvo a partir do ficheiro de despejo.
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
Incluindo o parâmetro --sem proprietário faz com que todos os objetos criados durante a restauração sejam propriedade do utilizador especificado com o nome de utilizador. Para mais informações, consulte a documentação oficial do PostgreSQL sobre [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Se o seu servidor PostgreSQL necessitar de ligações SSL (por padrão na Base de `PGSSLMODE=require` Dados Azure para servidores PostgreSQL), delineie uma variável ambiental para que a ferramenta pg_restore se conectem com o SSL. Sem SSL, o erro pode ser lido`FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> Na linha de comando Windows, execute o comando `SET PGSSLMODE=require` antes de executar o comando pg_restore. Em Linux ou Bash `export PGSSLMODE=require` comande o comando antes de executar o comando pg_restore.
>

Neste exemplo, restaure os dados do **testdb.dump** do ficheiro de despejo na base de dados **mypgsqldb** no servidor **alvo mydemoserver.postgres.database.azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>Otimização do processo de migração

Uma forma de migrar a base de dados postgresql existente para a Base de Dados Azure para o serviço PostgreSQL é fazer o backup a base de dados da fonte e restaurá-la em Azure. Para minimizar o tempo necessário para completar a migração, considere utilizar os seguintes parâmetros com os comandos de backup e restaurar.

> [!NOTE]
> Para obter informações detalhadas sobre sintaxe, consulte os artigos [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Para o backup
- Pegue a cópia de segurança com o interruptor -Fc para que possa efetuar o restauro paralelamente para acelerar. Por exemplo:

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName -f Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>Para o restauro
- Sugerimos que mude o ficheiro de reserva para um VM Azure na mesma região que a Base de Dados Azure para o servidor PostgreSQL para onde está a migrar, e faça o pg_restore desse VM para reduzir a latência da rede. Recomendamos também que o VM seja criado com [rede acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md) ativada.

- Deve já ser feito por padrão, mas abra o ficheiro de despejo para verificar se as declarações de índice de criação estão após a inserção dos dados. Se não for o caso, mova as declarações do índice de criação após a inserção dos dados.

- Restaurar com os interruptores -Fc e -j *#* para paralelo restabelecimento. *#* é o número de núcleos no servidor alvo. Também pode tentar *#* com o dobro do número de núcleos do servidor alvo para ver o impacto. Por exemplo:

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- Também pode editar o ficheiro de despejo adicionando o conjunto de comando *synchronous_commit = desligado;* no início e o conjunto de comando *synchronous_commit = ligado;* no final. Não ligá-lo no final, antes que as aplicações alterem os dados, pode resultar na perda subsequente de dados.

- Na base de dados Azure-alvo para servidor PostgreSQL, considere fazer o seguinte antes da restauração:
    - Desligue o rastreio do desempenho da consulta, uma vez que estas estatísticas não são necessárias durante a migração. Pode fazê-lo definindo pg_stat_statements.track, pg_qs.query_capture_mode e pgms_wait_sampling.query_capture_mode para a NONE.

    - Use uma alta computação e sku de alta memória, como 32 vCore Memory Otimizada, para acelerar a migração. Pode facilmente reduzir a sua sku preferida depois de o restauro estar completo. Quanto maior for o sku, mais paralelismo `-j` se pode alcançar aumentando o parâmetro correspondente no comando pg_restore. 

    - Mais IOPS no servidor alvo poderia melhorar o desempenho de restauro. Pode fornecer mais IOPS aumentando o tamanho de armazenamento do servidor. Esta definição não é reversível, mas considere se um IOPS mais elevado beneficiaria a sua carga de trabalho real no futuro.

Lembre-se de testar e validar estes comandos num ambiente de teste antes de os utilizar na produção.

## <a name="next-steps"></a>Passos seguintes
- Para migrar uma base de dados PostgreSQL utilizando exportação e importação, consulte migrar a sua base de [dados PostgreSQL utilizando exportação e importação](howto-migrate-using-export-and-import.md).
- Para mais informações sobre bases de dados migratórias para a Base de Dados Azure para PostgreSQL, consulte o Guia de [Migração da Base de Dados](https://aka.ms/datamigration).
