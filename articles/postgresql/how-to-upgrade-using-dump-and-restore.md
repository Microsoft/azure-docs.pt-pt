---
title: Upgrade usando despejo e restauro - Azure Database for PostgreSQL - Single Server
description: Descreve métodos de atualização offline usando bases de dados de despejo e restauro para migrar para uma versão mais alta Azure Database for PostgreSQL - Single Server.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 42bbe1c9f4056ae0dae0ccd59b452db90a7c63c5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493666"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>Atualize a sua base de dados PostgreSQL utilizando o despejo e o restauro

Pode atualizar o seu servidor PostgreSQL implantado na Base de Dados Azure para PostgreSQL - Servidor Único migrando as suas bases de dados para um servidor de versão maior, utilizando métodos seguidos.
* **Método offline** utilizando [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) postgreSQL e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) que incorre em tempo de inatividade para migrar os dados. Este documento aborda este método de atualização/migração.
* **Método online** utilizando [o Serviço de Migração de Bases de Dados](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) (DMS). Este método fornece uma migração reduzida de tempo de inatividade e mantém a base de dados-alvo em sintonia com a fonte e pode escolher quando cortar. No entanto, existem poucos pré-requisitos e restrições a serem abordados para a utilização de DMS. Para mais informações, consulte a documentação do [DMS.](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) 

 A tabela seguinte fornece algumas recomendações baseadas em tamanhos e cenários de base de dados.

| **Base de dados/Cenário** | **Despejo/restauro (Offline)** | **DMS (Online)** |
| ------ | :------: | :-----: |
| Você tem uma pequena base de dados e pode pagar tempo de inatividade para atualizar  | X | |
| Pequenas bases de dados (< 10 GB)  | X | X | 
| DBs de pequeno porção (10 GB – 100 GB) | X | X |
| Grandes bases de dados (> 100 GB) |  | X |
| Pode permitir tempo de inatividade para upgrade (independentemente do tamanho da base de dados) | X |  |
| Pode abordar [os pré-requisitos do](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md#prerequisites)DMS, incluindo um reboot? |  | X |
| Pode evitar DDLs e tabelas nãologadas durante o processo de atualização? | |  X |

Este guia fornece poucas metodologias e exemplos de migração offline para mostrar como pode migrar do seu servidor de origem para o servidor-alvo que executa uma versão mais alta do PostgreSQL.

> [!NOTE]
> O despejo e restauro pós-SQL podem ser realizados de muitas maneiras. Pode optar por migrar utilizando um dos métodos fornecidos neste guia ou escolher quaisquer formas alternativas de atender às suas necessidades. Para obter despejos detalhados e restaurar a sintaxe com parâmetros adicionais, consulte os artigos [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-database-for-postgresql"></a>Pré-requisitos para a utilização de despejo e restauro com Base de Dados Azure para PostgreSQL
 
Para passar por este guia, você precisa:

- Uma base de dados postgreSQL **de origem** que executa 9.5, 9.6 ou 10 que pretende atualizar
- Um **target** servidor de base de dados postgreSQL alvo com a versão principal desejada [Azure Database para servidor PostgreSQL](quickstart-create-server-database-portal.md). 
- Um sistema de clientes PostgreSQL para executar os comandos de despejo e restauro.
  - Pode ser um cliente Linux ou Windows com postgreSQL instalado e tem [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) utilitários de linha de comando instalados. 
  - Em alternativa, pode utilizar [a Azure Cloud Shell](https://shell.azure.com) ou clicando na Casca de Nuvem Azure na barra de menus no canto superior direito do portal [Azure](https://portal.azure.com). Terá de iniciar sessão na sua conta `az login` antes de executar os comandos de despejo e restauro.
- O seu cliente PostgreSQL funciona de preferência na mesma região que os servidores de origem e alvo. 


## <a name="additional-details-and-considerations"></a>Detalhes e considerações adicionais
- Pode encontrar o fio de ligação às bases de dados de origem e alvo clicando nas "Cordas de Ligação" a partir do portal. 
- Pode estar a executar mais de uma base de dados no seu servidor. Pode encontrar a lista de bases de dados ligando-se ao seu servidor de origem e executando `\l` .
- Criar bases de dados correspondentes no servidor de base de dados-alvo.
- Pode saltar bases de dados de upgrade `azure_maintenance` ou modelo.
- Consulte as tabelas acima para determinar se a base de dados é adequada para este modo de migração.
- Se quiser utilizar a Azure Cloud Shell, tenha em atenção que a sessão tem um tempo de saída após 20 minutos. Se o tamanho da sua base de dados for < 10 GB, poderá concluir a atualização sem o tempo de sessão. Caso contrário, poderá ter de manter a sessão aberta por outros meios, tais como premir <Enter> a tecla uma vez em 10-15 minutos. 


## <a name="example-database-used-in-this-guide"></a>Base de dados de exemplos utilizada neste guia

Neste guia, os seguintes servidores de origem e alvo e nomes de bases de dados são utilizados para ilustrar com exemplos.

 | **Descrição** | **Valor** |
 | ------- | ------- |
 | Servidor de origem (v9.5) | pg-95.postgres.database.azure.com |
 | Base de dados de origem | bench5gb |
 | Tamanho da base de dados de origem | 5 GB |
 | Nome do utilizador da fonte | pg@pg-95 |
 | Servidor-alvo (v11) | pg-11.postgres.database.azure.com |
 | Base de dados-alvo | bench5gb |
 | Nome do utilizador-alvo | pg@pg-11 |

## <a name="upgrade-your-databases-using-offline-migration-methods"></a>Atualize as suas bases de dados utilizando métodos de migração offline
Pode optar por utilizar um dos métodos descritos nesta secção para as suas atualizações. Pode utilizar as seguintes dicas durante a realização das tarefas.

- Se estiver a usar a mesma palavra-passe para a origem e a base de dados-alvo, pode definir a `PGPASSWORD=yourPassword` variável ambiental.  Então não tens de fornecer palavra-passe sempre que corres comandos como psql, pg_dump e pg_restore.  Da mesma forma, pode configurar variáveis adicionais `PGUSER` como, `PGSSLMODE` etc. ver [variáveis ambientais PostgreSQL](https://www.postgresql.org/docs/11/libpq-envars.html).
  
- Se o seu servidor PostgreSQL necessitar de ligações TLS/SSL (por padrão na Base de Dados Azure para servidores PostgreSQL), desagreja uma variável de ambiente `PGSSLMODE=require` de modo a que a ferramenta pg_restore se conecte com o TLS. Sem TLS, o erro pode ler  `FATAL:  SSL connection is required. Please specify SSL options and retry.`

- Na linha de comando do Windows, executa o comando `SET PGSSLMODE=require` antes de executar o comando pg_restore. Em Linux ou Bash executar o comando `export PGSSLMODE=require` antes de executar o comando pg_restore.

### <a name="method-1-migrate-using-dump-file"></a>Método 1: Migrar utilizando o ficheiro de despejo

Este método envolve dois passos. Primeiro é criar uma lixeira a partir do servidor de origem. O segundo passo é restaurar o ficheiro de despejo para o servidor alvo. Mais detalhes, por favor, consulte o [Migrate usando a](howto-migrate-using-dump-and-restore.md) documentação de despejo e restauro. Este é o método recomendado se tiver grandes bases de dados e o seu sistema de clientes tiver armazenamento suficiente para armazenar o ficheiro de despejo.

### <a name="method-2-migrate-using-streaming-the-dump-data-to-the-target-database"></a>Método 2: Migrar utilizando o streaming dos dados de despejo para a base de dados-alvo

Se não tiver um cliente PostgreSQL ou quiser utilizar a Azure Cloud Shell, então pode utilizar este método. O depósito de dados é transmitido diretamente para o servidor de base de dados-alvo e não armazena a lixeira no cliente. Assim, isto pode ser usado com um cliente com armazenamento limitado e até pode ser executado a partir da Azure Cloud Shell. 

1. Certifique-se de que a base de dados existe no servidor alvo utilizando o `\l` comando. Se a base de dados não existir, então crie a base de dados.
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```SQL
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. Executar a lixeira e restaurar como uma única linha de comando usando um tubo. 
    ```azurecli-interactive
    pg_dump -Fc -v --mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    Por exemplo,

    ```azurecli-interactive
    pg_dump -Fc -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. Uma vez concluído o processo de atualização (migração), pode testar a sua aplicação com o servidor alvo. 
4. Repita este processo para todas as bases de dados dentro do servidor.

 Como exemplo, a tabela que se segue ilustra o tempo que demorou a migrar utilizando o método de despejo de streaming. Os dados da amostra são preenchidos [com recurso a pgbench](https://www.postgresql.org/docs/10/pgbench.html). Como a sua base de dados pode ter um número diferente de objetos com tamanhos variados do que as tabelas e índices gerados por pgbench, é altamente recomendado testar o despejo e restaurar a sua base de dados para entender o tempo real que leva para atualizar a sua base de dados. 

| **Tamanho da base de dados** | **Aprox. tempo demorado** | 
| ----- | ------ |
| 1 GB  | 1-2 minutos |
| 5 GB | 8-10 minutos |
| 10 GB | 15-20 minutos |
| 50 GB | 1-1,5 horas |
| 100 GB | 2,5-3 horas|
   
### <a name="method-3-migrate-using-parallel-dump-and-restore"></a>Método 3: Migrar utilizando despejo paralelo e restaurar 

Pode considerar este método se tiver poucas tabelas maiores na sua base de dados e pretender paralelizar o processo de despejo e restauro para essa base de dados. Também precisa de armazenamento suficiente no seu sistema de clientes para acomodar depósitos de backup. Este processo de despejo e restauro paralelo reduz o consumo de tempo para completar toda a migração. Por exemplo, a base de dados pgbench de 50 GB que demorou 1-1,5 horas a migrar foi concluída usando o Método 1 e 2 demorou menos de 30 minutos usando este método.

1. Para cada base de dados no seu servidor de origem, crie uma base de dados correspondente no servidor alvo.

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   Por exemplo,
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. Executar o comando pg_dump em formato de diretório com número de empregos = 4 (número de tabelas na base de dados). Com um nível de computação maior e com mais tabelas, pode aumentá-lo para um número mais elevado. Essa pg_dump criará um diretório para armazenar ficheiros comprimidos para cada trabalho.

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    Por exemplo,
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. Em seguida, restaurar a cópia de segurança no servidor alvo.
    ```bash
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    Por exemplo,
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> O processo mencionado neste documento também pode ser usado para atualizar a sua Base de Dados Azure para o servidor Pós-SQL - Flexível, que está em Pré-visualização. A principal diferença é que a cadeia de ligação para o alvo do servidor flexível é sem o `@dbName` .  Por exemplo, se o nome de utilizador for `pg` , o nome de utilizador do único servidor na cadeia de ligação será , enquanto com o `pg@pg-95` servidor flexível, pode simplesmente usar `pg` .

## <a name="next-steps"></a>Passos seguintes

- Depois de estar satisfeito com a função de base de dados-alvo, pode deixar cair o seu antigo servidor de base de dados. 
- Se pretender utilizar o mesmo ponto final de base de dados que o servidor de origem, depois de ter eliminado o seu antigo servidor de base de dados de origem, pode criar uma réplica de leitura com o nome do servidor de base de dados antigo. Uma vez estabelecido o estado estável, pode parar a réplica, que irá promover o servidor de réplica para ser um servidor independente. Consulte [a Replicação](./concepts-read-replicas.md) para mais detalhes.
- Lembre-se de testar e validar estes comandos num ambiente de teste antes de os utilizar na produção.