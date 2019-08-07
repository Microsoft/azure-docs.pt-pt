---
title: Como despejar e restaurar no banco de dados do Azure para PostgreSQL-servidor único
description: Descreve como extrair um banco de dados PostgreSQL em um arquivo de despejo e restaurá-lo a partir de um arquivo criado pelo pg_dump no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 43e6fe301cf28b7a342ba2e802c9fce19bfeec4d
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815866"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migrar seu banco de dados PostgreSQL usando despejo e restauração
Você pode usar [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) para extrair um banco de dados PostgreSQL em um arquivo de despejo e [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) para restaurar o banco de dados PostgreSQL de um arquivo morto criado pelo pg_dump.

## <a name="prerequisites"></a>Pré-requisitos
Para percorrer este guia de instruções, você precisa de:
- Um [banco de dados do Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md) com regras de firewall para permitir o acesso e o banco de dados sob ele.
- utilitários de linha de comando [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) instalados

Siga estas etapas para despejar e restaurar o banco de dados PostgreSQL:

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Criar um arquivo de despejo usando pg_dump que contém os dados a serem carregados
Para fazer backup de um banco de dados PostgreSQL existente localmente ou em uma VM, execute o seguinte comando:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
Por exemplo, se você tiver um servidor local e um banco de dados chamado **TestDB** nele
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pg_restore"></a>Restaurar os dados para o banco de dado de destino do Azure para PostrgeSQL usando o pg_restore
Depois de criar o banco de dados de destino, você pode usar o comando pg_restore e o parâmetro-d,--dbname para restaurar os dados no banco de dado de destino do arquivo de despejo.
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
A inclusão do parâmetro--no-Owner faz com que todos os objetos criados durante a restauração sejam de Propriedade do usuário especificado com--username. Para obter mais informações, consulte a documentação oficial do PostgreSQL em [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Se o seu servidor PostgreSQL exigir conexões SSL (ativadas por padrão no banco de dados do Azure para servidores PostgreSQL) `PGSSLMODE=require` , defina uma variável de ambiente para que a ferramenta pg_restore se conecte com SSL. Sem o SSL, o erro pode ser lido`FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> Na linha de comando do Windows, execute o `SET PGSSLMODE=require` comando antes de executar o comando pg_restore. No Linux ou bash, execute o `export PGSSLMODE=require` comando antes de executar o comando pg_restore.
>

Neste exemplo, restaure os dados do arquivo de despejo **TestDB. dump** no banco de dados **mypgsqldb** no servidor de destino **mydemoserver.Postgres.Database.Azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>Otimizando o processo de migração

Uma maneira de migrar seu banco de dados PostgreSQL existente para o serviço banco de dados do Azure para PostgreSQL é fazer backup do banco de dados na origem e restaurá-lo no Azure. Para minimizar o tempo necessário para concluir a migração, considere usar os parâmetros a seguir com os comandos backup e Restore.

> [!NOTE]
> Para obter informações detalhadas de sintaxe, consulte os artigos [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Para o backup
- Faça o backup com a opção-FC para que você possa executar a restauração em paralelo para acelerar a ti. Por exemplo:

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName > Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>Para a restauração
- Sugerimos que você mova o arquivo de backup para uma VM do Azure na mesma região que o banco de dados do Azure para o servidor PostgreSQL para o qual você está migrando e faça o pg_restore dessa VM para reduzir a latência de rede. Também recomendamos que a VM seja criada com a [rede acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md) habilitada.

- Ele já deve ser feito por padrão, mas abra o arquivo de despejo para verificar se as instruções CREATE INDEX são posteriores à inserção dos dados. Se não for o caso, mova as instruções CREATE INDEX depois que os dados forem inseridos.

- Restaure com switches-FC e-j *#* para paralelizar a restauração. *#* é o número de núcleos no servidor de destino. Você também pode tentar *#* definir como duas vezes o número de núcleos do servidor de destino para ver o impacto. Por exemplo:

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- Você também pode editar o arquivo de despejo adicionando o comando *set synchronous_commit = off;* no início e o comando *set synchronous_commit = on;* no final. Não ligá-lo no final, antes que os aplicativos alterem os dados, pode resultar em perda subsequente de dados.

- No banco de dados do Azure de destino para o servidor PostgreSQL, considere fazer o seguinte antes da restauração:
    - Desative o rastreamento de desempenho de consultas, já que essas estatísticas não são necessárias durante a migração. Você pode fazer isso definindo pg_stat_statements. Track, pg_qs. query_capture_mode e pgms_wait_sampling. query_capture_mode como NONE.

    - Use uma alta computação e um SKU de memória alta, como a memória vCore de 32, para acelerar a migração. Você pode dimensionar facilmente de volta para o SKU preferencial depois que a restauração for concluída. Quanto maior a SKU, mais paralelismo você pode obter aumentando o parâmetro correspondente `-j` no comando pg_restore. 

    - Mais IOPS no servidor de destino pode melhorar o desempenho de restauração. Você pode provisionar mais IOPS aumentando o tamanho de armazenamento do servidor. Essa configuração não é reversível, mas considere se um IOPS maior beneficiaria sua carga de trabalho real no futuro.

Lembre-se de testar e validar esses comandos em um ambiente de teste antes de usá-los na produção.

## <a name="next-steps"></a>Passos seguintes
- Para migrar um banco de dados PostgreSQL usando exportar e importar, consulte [migrar seu banco de dados PostgreSQL usando exportar e importar](howto-migrate-using-export-and-import.md).
- Para obter mais informações sobre como migrar bancos de dados do para o Azure para PostgreSQL, consulte o [Guia de migração de banco de dados](https://aka.ms/datamigration).
