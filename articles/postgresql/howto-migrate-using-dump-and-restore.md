---
title: Como capturar e restaurar na base de dados do Azure para PostgreSQL
description: Descreve como extrair uma base de dados do PostgreSQL para um arquivo de despejo e restaurar a partir de um arquivo criado por pg_dump na base de dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: d406132c4e359c78567ae47a3acba5b73aa39820
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60420347"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migrar a sua base de dados do PostgreSQL com a captura e restauro
Pode usar [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) para extrair um banco de dados do PostgreSQL para um arquivo de despejo e [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) para restaurar a base de dados do PostgreSQL a partir de um ficheiro de arquivo criado pelo pg_dump.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, terá de:
- Uma [base de dados do Azure para o servidor PostgreSQL](quickstart-create-server-database-portal.md) com regras de firewall para permitir o acesso e a base de dados sob a mesma.
- [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) utilitários da linha de comandos instalados

Siga estes passos para cópia de segurança e restaurar a base de dados do PostgreSQL:

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Criar um arquivo de despejo com pg_dump que contém os dados a ser carregado
Para fazer uma cópia de segurança uma existente PostgreSQL da base de dados no local ou numa VM, execute o seguinte comando:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
Por exemplo, se tiver um servidor local e uma base de dados chamado **testdb** nela
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>Restaurar os dados para o base de dados do Azure de destino para PostrgeSQL usando pg_restore
Depois de criar a base de dados de destino, pode utilizar o comando pg_restore e a -d, o parâmetro – dbname para restaurar os dados no banco de dados de destino do ficheiro de informação.
```bash
pg_restore -v --no-owner –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
Incluindo o-- parâmetro de proprietário não faz com que, todos os objetos criados durante o restauro que é detido pelo utilizador especificado com – o nome de utilizador. Para obter mais informações, consulte a documentação oficial do PostgreSQL no [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Se o servidor PostgreSQL exigir ligações SSL (por predefinição na base de dados do Azure para servidores PostgreSQL), defina uma variável de ambiente `PGSSLMODE=require` para que a ferramenta de pg_restore liga-se com SSL. Sem SSL, pode ler o erro  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> Na linha de comando do Windows, execute o comando `SET PGSSLMODE=require` antes de executar o comando pg_restore. No Linux ou Bash, execute o comando `export PGSSLMODE=require` antes de executar o comando pg_restore.
>

Neste exemplo, restaurar os dados a partir do arquivo de despejo **testdb.dump** na base de dados **mypgsqldb** no servidor de destino **mydemoserver.postgres.database.azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>Otimizar o processo de migração

Uma forma de migrar a base de dados existente do PostgreSQL para base de dados do Azure para o serviço PostgreSQL é criar cópias de segurança da base de dados na origem e restaurá-lo no Azure. Para minimizar o tempo necessário para concluir a migração, considere utilizar os seguintes parâmetros com a cópia de segurança e restaurar os comandos.

> [!NOTE]
> Para informações detalhadas de sintaxe, consulte os artigos [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Para a cópia de segurança
- Efetuar a cópia de segurança com o comutador -Fc, de forma a que pode efetuar o restauro em paralelo para acelerar. Por exemplo:

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName > Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>Para o restauro
- Sugerimos que mover o ficheiro de cópia de segurança para uma VM do Azure na mesma região que a base de dados do Azure para o servidor PostgreSQL está a migrar para e fazer o pg_restore dessa VM para reduzir a latência de rede. Recomendamos também que a VM é criada com [accelerated networking](../virtual-network/create-vm-accelerated-networking-powershell.md) ativada.

- Ele deve ser feito já por predefinição, mas abrir o ficheiro de informação para verificar que as declarações de índice de criar após a inserção dos dados. Se não for o caso, mova as declarações de índice de criar depois dos dados são inseridos.

- Restaurar com os comutadores de -Fc e -j *#* para paralelizar o restauro. *#* é o número de núcleos no servidor de destino. Também pode tentar com *#* definido como duas vezes o número de núcleos de servidor de destino para ver o impacto. Por exemplo:

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- Também pode editar o arquivo de despejo ao adicionar o comando *definir synchronous_commit = desativar;* no início e o comando *definir synchronous_commit = on;* no final. Não ativá-la no final, antes das aplicações, alterar os dados, poderá resultar na perda subsequente de dados.

- No destino da base de dados do Azure para o servidor PostgreSQL, considere efetuar o seguinte antes do restauro:
    - Desative a consulta desempenho de controlo, uma vez que estas estatísticas não são necessários durante a migração. Pode fazê-lo ao definir pg_stat_statements.track pg_qs.query_capture_mode e pgms_wait_sampling.query_capture_mode como NONE.

    - Utilize um sku de memória elevada, como 32 vCore com otimização de memória e de computação elevada para acelerar a migração. Pode dimensionar facilmente volta para o sku preferencial após o restauro está concluído. Maior o sku, mais paralelismo pode conseguir aumentando o correspondente `-j` parâmetro no comando pg_restore. 

    - Melhora o desempenho de restauro mais IOPS no servidor de destino. Pode aprovisionar mais IOPS por aumentar o tamanho do armazenamento do servidor. Esta definição não é reversível, mas considere se um IOPS superior irá se beneficiar com a carga de trabalho real no futuro.

Lembre-se de testar e validar estes comandos num ambiente de teste antes de usá-los em produção.

## <a name="next-steps"></a>Passos Seguintes
- Para migrar uma base de dados do PostgreSQL com exportar e importar, consulte [migrar a sua base de dados do PostgreSQL com exportar e importar](howto-migrate-using-export-and-import.md).
- Para obter mais informações sobre como migrar bases de dados para a base de dados do Azure para PostgreSQL, veja a [guia de migração de bases de dados](https://aka.ms/datamigration).
