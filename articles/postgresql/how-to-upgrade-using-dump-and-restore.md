---
title: Upgrade usando despejo e restauro - Azure Database for PostgreSQL - Single Server
description: Descreve alguns métodos para despejar e restaurar bases de dados para migrar para uma versão mais alta Azure Database for PostgreSQL - Single Server.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/05/2020
ms.openlocfilehash: 6dfcf0b2ec1d46821007123908a8e7ba8df29744
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421775"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>Atualize a sua base de dados PostgreSQL utilizando o despejo e o restauro

Na Base de Dados Azure para PostgreSQL - Single Server, é recomendado atualizar o seu motor de base de dados PostgreSQL para uma versão maior, utilizando um destes métodos:
* Método offline utilizando [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)PostgreSQL . Neste método, primeiro executa a lixeira do seu servidor de origem e, em seguida, restaura-se a descarga no servidor alvo.
* Método online utilizando [**o Serviço de Migração de Bases de Dados**](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal) (DMS). Este método mantém a base de dados-alvo em sincronização com a fonte e pode escolher quando cortar. No entanto, existem alguns pré-requisitos e restrições a serem abordados. 

Pode utilizar a seguinte recomendação ao decidir entre métodos online e offline para realizar atualizações de versão importantes.

| **Base de dados** | **Despejo/restauro (Offline)** | **DMS (Online)** |
| ------ | :------: | :-----: |
| Você tem uma pequena base de dados e pode pagar tempo de inatividade para atualizar  | X | |
| Pequenas bases de dados (< 10 GB)  | X | X | 
| DBs de pequeno porção (10 GB – 100 GB) | X | X |
| Grandes bases de dados (> 100 GB) |  | X |
| Pode permitir tempo de inatividade para upgrade (independentemente do tamanho da base de dados) | X |  |
| Pode abordar [os pré-requisitos do](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal#prerequisites) DMS, incluindo um reboot? |  | X |
| Pode evitar DDLs e tabelas nãologadas durante o processo de atualização? | |  X |

Este guia de como fazer fornece dois métodos de exemplo sobre como atualizar as suas bases de dados utilizando comandos de pg_dump postgreSQL e pg_restore. O processo neste documento é referido como **upgrade,** embora a base de dados seja  **migrada** do servidor de origem para o servidor alvo. 

> [!NOTE]
> O despejo e restauro pós-SQL podem ser realizados de muitas maneiras. Pode optar por utilizar métodos diferentes dos referidos neste documento. Por exemplo, para fazer uma lixeira seguida de restauro de um cliente PostgreSQL, consulte a [documentação](./howto-migrate-using-dump-and-restore.md) para detalhes de procedimento passo a passo e boas práticas. Para obter despejos detalhados e restaurar a sintaxe com parâmetros adicionais, consulte os artigos [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-postgresql"></a>Pré-requisitos para o uso de despejo e restauro com Azure PostgreSQL
 
Para passar por este guia, você precisa:
- Uma base de dados de origem que funciona 9.5, 9.6 ou 10 (Base de Dados Azure para PostgreSQL – Servidor Único)
- Servidor de base de dados alvo com a versão principal desejada PostgreSQL [Azure Database para servidor PostgreSQL](quickstart-create-server-database-portal.md). 
- Um sistema de clientes (Linux) com postgreSQL instalado e tem [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) utilitários de linha de comando instalados. 
- Em alternativa, pode utilizar [a Azure Cloud Shell](https://shell.azure.com) ou clicando na Casca de Nuvem Azure na barra de menus no canto superior direito do portal [Azure](https://portal.azure.com). Terá de iniciar sessão na sua conta `az login` antes de executar os comandos de despejo e restauro.
- A localização do seu cliente PostgreSQL, como um VM de preferência a funcionar na mesma região que os servidores de origem e alvo. 

## <a name="additional-details-and-considerations"></a>Detalhes e considerações adicionais
- Pode encontrar o fio de ligação às bases de dados de origem e alvo clicando nas "Cordas de Ligação" a partir do portal. 
- Pode estar a executar mais de uma base de dados no seu servidor. Pode encontrar a lista de bases de dados ligando-se ao seu servidor de origem e executando `\l` .
- Criar bases de dados correspondentes no servidor de base de dados-alvo.
- Pode saltar bases de dados de upgrade `azure_maintenance` ou modelo.
- Consulte as tabelas acima para determinar se a base de dados é adequada para este modo de migração.
- Se quiser utilizar o Azure Cloud Shell, a sessão tem um horário de 20 minutos. Se o tamanho da sua base de dados for < 10 GB, poderá completar a atualização sem o tempo de saída. Caso contrário, poderá ter de manter a sessão aberta por outros meios, tais como premir <Enter> a tecla uma vez em 10-15 minutos. 

> [!TIP] 
> - Se estiver a usar a mesma palavra-passe para a origem e a base de dados-alvo, pode definir a `PGPASSWORD=yourPassword` variável ambiental.  Então não tens de fornecer palavra-passe sempre que tens de executar comandos como psql, pg_dump e pg_restore.  Da mesma forma, pode configurar variáveis adicionais `PGUSER` como, `PGSSLMODE` etc. ver [variáveis ambientais PostgreSQL](https://www.postgresql.org/docs/11/libpq-envars.html).
>  
> - Se o seu servidor PostgreSQL necessitar de ligações TLS/SSL (por padrão na Base de Dados Azure para servidores PostgreSQL), desagreja uma variável de ambiente `PGSSLMODE=require` de modo a que a ferramenta pg_restore se conecte com o TLS. Sem TLS, o erro pode ler  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> - Na linha de comando do Windows, executa o comando `SET PGSSLMODE=require` antes de executar o comando pg_restore. Em Linux ou Bash executar o comando `export PGSSLMODE=require` antes de executar o comando pg_restore.

## <a name="example-database-used-in-this-guide"></a>Base de dados de exemplos utilizada neste guia

 | **Descrição** | **Valor** |
 | ------- | ------- |
 | Servidor de origem (v9.5) | pg-95.postgres.database.azure.com |
 | Base de dados de origem | bench5gb |
 | Tamanho da base de dados de origem | 5 GB |
 | Nome do utilizador da fonte | pg@pg-95 |
 | Servidor-alvo (v11) | pg-11.postgres.database.azure.com |
 | Base de dados-alvo | bench5gb |
 | Nome do utilizador-alvo | pg@pg-11 |

## <a name="method-1-upgrade-with-streaming-backups-to-the-target"></a>Método 1: Atualizar com backups de streaming para o alvo 

Neste método, todo o despejo de base de dados é transmitido diretamente para o servidor de base de dados-alvo e não armazena a lixeira no cliente. Assim, isto pode ser usado com um cliente com armazenamento limitado e até pode ser executado a partir da Azure Cloud Shell. 

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

 A tabela a seguir ilustra o tempo que demorou a atualizar utilizando este método. Os dados são preenchidos [com recurso a pgbench.](https://www.postgresql.org/docs/10/pgbench.html) Como a sua base de dados pode ter um número diferente de objetos com tamanhos variados do que as tabelas e índices gerados por pgbench, é altamente recomendado testar o despejo e restaurar a sua base de dados para entender o tempo real que leva para atualizar a sua base de dados. 

| **Tamanho da base de dados** | **Aprox. tempo demorado** | 
| ----- | ------ |
| 1 GB  | 1-2 minutos |
| 5 GB | 8-10 minutos |
| 10 GB | 15-20 minutos |
| 50 GB | 1-1,5 horas |
| 100 GB | 2,5-3 horas|
   
## <a name="method-2-upgrade-with-parallel-dump-and-restore"></a>Método 2: Atualizar com despejo paralelo e restaurar 

Este método é útil se tiver poucas tabelas maiores na base de dados e quiser paralelizar o processo de despejo e restauro para essa base de dados. Precisa de armazenamento de disco local suficiente para acomodar depósitos de backup para as suas bases de dados. Este processo de despejo e restauro paralelo reduz o consumo de tempo para completar toda a migração/upgrade. Por exemplo, a base de dados pgbench de 50 GB que demorou 1-1,5 horas a migrar foi concluída em menos de 30 minutos.

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

## <a name="next-steps"></a>Passos Seguintes

- Uma vez satisfeito com a função de base de dados-alvo, pode deixar cair o seu antigo servidor de base de dados. 
- Se pretender utilizar o mesmo ponto final de base de dados que o servidor de origem, depois de ter eliminado o seu antigo servidor de base de dados de origem, pode criar uma réplica de leitura com o nome do servidor de base de dados antigo. Uma vez estabelecido o estado estável, pode parar a réplica, que irá promover o servidor de réplica para ser um servidor independente. Consulte [a Replicação](./concepts-read-replicas.md) para mais detalhes.
- Lembre-se de testar e validar estes comandos num ambiente de teste antes de os utilizar na produção.
