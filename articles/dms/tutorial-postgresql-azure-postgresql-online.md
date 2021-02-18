---
title: 'Tutorial: Migrar postgreSQL para base de dados de Azure para postgreSQL on-line através do Azure CLI'
titleSuffix: Azure Database Migration Service
description: Aprenda a realizar uma migração on-line de PostgreSQL no local para a Base de Dados Azure para PostgreSQL utilizando o Serviço de Migração da Base de Dados Azure através do CLI.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurecli
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: e5efa30343afcda736946164eb271ad0bf420cab
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096139"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-cli"></a>Tutorial: Migrar PostgreSQL para Azure DB para PostgreSQL on-line usando DMS via Azure CLI

Pode utilizar o Azure Database Migration Service para migrar as bases de dados de uma instância postgreSQL no local para [a Azure Database para PostgreSQL](../postgresql/index.yml) com tempo de inatividade mínimo. Por outras palavras, a migração pode ser alcançada com um mínimo de tempo de inatividade para a aplicação. Neste tutorial, migra a base de dados de amostras **de DVD Rental** de um caso no local de PostgreSQL 9.6 para Azure Database for PostgreSQL utilizando a atividade de migração on-line no Azure Database Migration Service.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Migrar o esquema da amostra utilizando pg_dump utilidade.
> * Criar uma instância do Azure Database Migration Service.
> * Utilizar o Azure Database Migration Service para criar um projeto de migração.
> * Executar a migração.
> * Monitorizar a migração.

> [!NOTE]
> A utilização do Azure Database Migration Service para realizar uma migração online requer a criação de um caso baseado no nível de preços Premium. Encriptamos o disco para evitar o roubo de dados durante o processo de migração.

> [!IMPORTANT]
> Para uma experiência de migração ideal, a Microsoft recomenda a criação de um exemplo do Azure Database Migration Service na mesma região Azure que a base de dados-alvo. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Faça o download e instale [a edição comunitária postgreSQL](https://www.postgresql.org/download/) 9.5, 9.6 ou 10. A versão do Servidor PostgreSQL de origem deve ser 9.5.11, 9.6.7, 10 ou mais tarde. Para obter mais informações, consulte o artigo [Versões postgresQL suportadas na base de dados.](../postgresql/concepts-supported-versions.md)

    Note também que a base de dados Azure alvo para a versão PostgreSQL deve ser igual ou mais tarde à versão PostgreSQL no local. Por exemplo, o PostgreSQL 9.6 só pode migrar para a Base de Dados Azure para PostgreSQL 9.6, 10 ou 11, mas não para a Base de Dados Azure para PostgreSQL 9.5.

* [Crie uma instância na Base de Dados Azure para postgreSQL](../postgresql/quickstart-create-server-database-portal.md) ou [crie uma base de dados Azure para o servidor PostgreSQL - Hyperscale (Citus).](../postgresql/quickstart-create-hyperscale-portal.md)
* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-to-site aos seus servidores de origem no local, utilizando [expressRoute](../expressroute/expressroute-introduction.md) ou [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação da Rede Virtual,](../virtual-network/index.yml)e especialmente os artigos quickstart com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se utilizar o ExpressRoute com o olhar de rede para a Microsoft, adicione os [seguintes pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço à sub-rede em que o serviço será abastecado:
    >
    > * Ponto final da base de dados-alvo (por exemplo, ponto final SQL, ponto final cosmos DB, e assim por diante)
    > * Ponto final de armazenamento
    > * Ponto final do ônibus de serviço
    >
    > Esta configuração é necessária porque o Serviço de Migração da Base de Dados Azure carece de conectividade com a Internet.

* Certifique-se de que as regras do Grupo de Segurança da Rede virtual (NSG) não bloqueiam a porta de saída 443 do ServiceTag para ServiceBus, Storage e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego NSG da rede virtual, consulte o artigo Filtrar o [tráfego da rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra a firewall do Windows para permitir que o Serviço de Migração da Base de Dados de Azure aceda ao Servidor PostgreSQL de origem, que por padrão é a porta TCP 5432.
* Se estiver a utilizar uma aplicação de firewall à frente da base ou bases de dados, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base ou bases de dados de origem para migração.
* Crie uma regra de [firewall](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules) ao nível do servidor para a Base de Dados Azure para PostgreSQL para permitir que o Azure Database Migration Service aceda às bases de dados-alvo. Forneça a gama de sub-redes da rede virtual utilizada para o Serviço de Migração da Base de Dados Azure.
* Existem dois métodos para invocar a CLI:

  * No canto superior direito do portal Azure, selecione o botão Cloud Shell:

       ![Botão Cloud Shell no portal do Azure](media/tutorial-postgresql-to-azure-postgresql-online/cloud-shell-button.png)

  * Instale e execute a CLI localmente. A CLI 2.0 é a ferramenta de linha de comandos para gerir os recursos do Azure.

       Para transferir a CLI, siga as instruções no artigo [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). O artigo também indica as plataformas que suportam a CLI 2.0.

       Para configurar o Subsistema Windows para Linux (WSL), siga as instruções no [Windows 10 Installation Guide](/windows/wsl/install-win10) (Guia de Instalação do Windows 10)

* Ative a replicação lógica no ficheiro postgresql.config e defina os seguintes parâmetros:

  * wal_level = **logical**
  * max_replication_slots = [número de slots], recomendar a definição para **cinco slots**
  * max_wal_senders = [número de tarefas simultâneas] – O parâmetro max_wal_senders define o número de tarefas simultâneas que podem ser executadas, definição recomendada de **10 tarefas**

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo

Para concluir todos os objetos de base de dados, como esquemas de tabela, índices e procedimentos armazenados, é necessário extrair o esquema da base de dados de origem e aplicar à base de dados.

1. Utilize o comando pg_dump -s para criar um ficheiro de captura de esquema para uma base de dados. 

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Por exemplo, para capturar um ficheiro de esquema para uma base de dados dvdrental:
    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

    Para obter mais informações sobre como utilizar o utilitário pg_dump, veja os exemplos no tutorial [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Crie uma base de dados vazia no ambiente de destino, que é a Base de Dados do Azure para PostgreSQL.

    Para obter mais informações sobre como conectar e criar uma base de dados, consulte o artigo [Criar uma Base de Dados Azure para servidor PostgreSQL no portal Azure](../postgresql/quickstart-create-server-database-portal.md) ou Criar uma Base de [Dados Azure para postgresQL - Hiperescala (Citus) no portal Azure](../postgresql/quickstart-create-hyperscale-portal.md).

3. Importe o esquema para a base de dados de destino que criou ao restaurar o ficheiro de captura de esquema.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql 
    ```

    Por exemplo:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Se tiver chaves externas no seu esquema, o carregamento inicial e a sincronização contínua da migração irão falhar. Execute o seguinte script em PgAdmin ou em psql para extrair o script de remoção de chave de referência e adicionar o script de chave de referência no destino (Base de Dados do Azure para PostgreSQL).
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

    Execute o script de remoção de chave externa (que é a segunda coluna) no resultado da consulta.

5. Os acionadores nos dados (acionadores de inserção ou adição) irão impor a integridade dos dados no destino antes dos dados replicados da origem. Recomenda-se que desative os gatilhos em todas as tabelas **do alvo** durante a migração e, em seguida, reativar os gatilhos após a migração estar completa.

    Para desativar os acionadores na base de dados de destino, utilize o seguinte comando:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Se existirem dados ENUM em quaisquer tabelas, recomenda-se que o atualize temporariamente a um tipo de dados "variando o carácter" na tabela-alvo. Depois da conclusão da replicação de dados, reverta o tipo de dados para ENUM.

## <a name="provisioning-an-instance-of-dms-using-the-cli"></a>Aprovisionar uma instância do DMS com a CLI

1. Instale a extensão dms sync:
   * Inicie sessão no Azure ao executar o seguinte comando:
       ```azurecli
       az login
       ```

   * Quando lhe for pedido, abra um browser e introduza um código para autenticar o dispositivo. Siga as instruções tal como indicadas.
   * Adicione a extensão dms:
       * Para listar as extensões disponíveis, execute o seguinte comando:

           ```azurecli
           az extension list-available –otable
           ```

       * Para instalar a extensão, execute o seguinte comando:

           ```azurecli
           az extension add –n dms-preview
           ```

   * Para verificar se tem a extensão dms correta instalada, execute o seguinte comando:

       ```azurecli
       az extension list -otable
       ```
       Deverá ver o seguinte resultado:

       ```output
       ExtensionType    Name
       ---------------  ------
       whl              dms
       ```

      > [!IMPORTANT]
      > Certifique-se de que a sua versão de extensão está acima de 0.11.0.

   * Em qualquer altura, veja todos os comandos suportados no DMS ao executar:

       ```azurecli
       az dms -h
       ```

   * Se tiver várias subscrições do Azure, execute o comando seguinte para definir a subscrição com que pretende aprovisionar uma instância do serviço DMS.

        ```azurecli
       az account set -s 97181df2-909d-420b-ab93-1bff15acb6b7
        ```

2. Aprovisione uma instância do DMS ao executar o seguinte comando:

   ```azurecli
   az dms create -l [location] -n <newServiceName> -g <yourResourceGroupName> --sku-name Premium_4vCores --subnet/subscriptions/{vnet subscription id}/resourceGroups/{vnet resource group}/providers/Microsoft.Network/virtualNetworks/{vnet name}/subnets/{subnet name} –tags tagName1=tagValue1 tagWithNoValue
   ```

   Por exemplo, o seguinte comando vai criar um serviço em:

   * Localização: E.U.A. Leste 2
   * Subscrição: 97181df2-909d-420b-ab93-1bff15acb6b7
   * Nome de Grupo de Recursos: PostgresDemo
   * Nome do Serviço DMS: PostgresCLI

   ```azurecli
   az dms create -l eastus2 -g PostgresDemo -n PostgresCLI --subnet /subscriptions/97181df2-909d-420b-ab93-1bff15acb6b7/resourceGroups/ERNetwork/providers/Microsoft.Network/virtualNetworks/AzureDMS-CORP-USC-VNET-5044/subnets/Subnet-1 --sku-name Premium_4vCores
   ```

   Demora cerca de 10 a 12 minutos para criar a instância do serviço DMS.

3. Para identificar o endereço IP do agente do DMS, para que o possa adicionar ao ficheiro pg_hba.conf do Postgres, execute o seguinte comando:

    ```azurecli
    az network nic list -g <ResourceGroupName>--query '[].ipConfigurations | [].privateIpAddress'
    ```

    Por exemplo:

    ```azurecli
    az network nic list -g PostgresDemo --query '[].ipConfigurations | [].privateIpAddress'
    ```

    Deverá obter um resultado semelhante ao endereço seguinte: 

    ```output
    [
      "172.16.136.18"
    ]
    ```

4. Adicione o endereço IP do agente DMS ao ficheiro pg_hba.conf do Postgres.

    * Anote o endereço IP do DMS depois de concluir o aprovisionamento no DMS.
    * Adicione o endereço IP ao ficheiro pg_hba.conf na origem, semelhante à seguinte entrada:

        ```
        host     all            all        172.16.136.18/10    md5
        host     replication    postgres   172.16.136.18/10    md5
        ```

5. Em seguida, crie um projeto de migração do PostgreSQL ao executar o seguinte comando:
    
    ```azurecli
    az dms project create -l <location> -g <ResourceGroupName> --service-name <yourServiceName> --source-platform PostgreSQL --target-platform AzureDbforPostgreSQL -n <newProjectName>
    ```

    Por exemplo, o comando seguinte cria um projeto que utiliza estes parâmetros:

   * Localização: West Central US
   * Nome de Grupo de Recursos: PostgresDemo
   * Nome do Serviço: PostgresCLI
   * Nome do projeto: PGMigration
   * Plataforma de origem: PostgreSQL
   * Plataforma de destino: AzureDbForPostgreSql

     ```azurecli
     az dms project create -l westcentralus -n PGMigration -g PostgresDemo --service-name PostgresCLI --source-platform PostgreSQL --target-platform AzureDbForPostgreSql
     ```

6. Crie uma tarefa de migração do PostgreSQL com os seguintes passos.

    Este passo inclui a utilização do IP de origem, UserID e palavra-passe, IP de destino, UserID, palavra-passe e o tipo de tarefa para estabelecer conectividade.

   * Para ver uma lista completa de opções, execute o comando:

       ```azurecli
       az dms project task create -h
       ```

       Para a ligação de origem e de destino, o parâmetro de entrada refere-se a um ficheiro json que tem a lista de objetos.

       O formato do objeto JSON de ligação para ligações do PostgreSQL.
        
       ```json
       {
                   "userName": "user name",    // if this is missing or null, you will be prompted
                   "password": null,           // if this is missing or null (highly recommended) you will
               be prompted
                   "serverName": "server name",
                   "databaseName": "database name", // if this is missing, it will default to the 'postgres'
               server
                   "port": 5432                // if this is missing, it will default to 5432
               }
       ```

   * Há também uma opção de base de dados json file que lista os objetos json. Para o PostgreSQL, o formato do objeto JSON de opções de base de dados é mostrado abaixo:

       ```json
       [
           {
               "name": "source database",
               "target_database_name": "target database",
           },
           ...n
       ]
       ```

   * Crie um ficheiro json com o Bloco de notas, copie os seguintes comandos e cole-os no ficheiro e, em seguida, guarde o ficheiro em C:\DMS\source.json.

        ```json
       {
                   "userName": "postgres",    
                   "password": null,           
               be prompted
                   "serverName": "13.51.14.222",
                   "databaseName": "dvdrental", 
                   "port": 5432                
               }
        ```

   * Crie outro ficheiro com o nome target.json e guarde-o como C:\DMS\target.json. Inclua os seguintes comandos:

       ```json
       {
               "userName": " dms@builddemotarget",    
               "password": null,           
               "serverName": " builddemotarget.postgres.database.azure.com",
               "databaseName": "inventory", 
               "port": 5432                
           }
       ```

   * Crie um ficheiro json de opções de base de dados que liste o inventário como a base de dados a migrar:

       ```json
       [
           {
               "name": "dvdrental",
               "target_database_name": "dvdrental",
           }
       ]
       ```

   * Execute o seguinte comando, que aceita a origem, o destino e os ficheiros json de opções de BD.

       ```azurecli
       az dms project task create -g PostgresDemo --project-name PGMigration --source-platform postgresql --target-platform azuredbforpostgresql --source-connection-json c:\DMS\source.json --database-options-json C:\DMS\option.json --service-name PostgresCLI --target-connection-json c:\DMS\target.json –task-type OnlineMigration -n runnowtask    
       ```

     Chegado a este ponto, já submeteu com êxito uma tarefa de migração.

7. Para mostrar o progresso da tarefa, execute o seguinte comando:

   ```azurecli
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
   ```

   OR

    ```azurecli
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output
    ```

8. Pode também consultar o migrationState a na saída de expansão:

    ```azurecli
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output --query 'properties.output[].migrationState | [0]' "READY_TO_COMPLETE"
    ```

No ficheiro de saída, existem diversos parâmetros que indicam o progresso da migração. Por exemplo, veja o ficheiro de saída abaixo:

  ```output
    "output": [                                 // Database Level
          {
            "appliedChanges": 0,         // Total incremental sync applied after full load
            "cdcDeleteCounter": 0        // Total delete operation  applied after full load
            "cdcInsertCounter": 0,       // Total insert operation applied after full load
            "cdcUpdateCounter": 0,       // Total update operation applied after full load
            "databaseName": "inventory",
            "endedOn": null,
            "fullLoadCompletedTables": 2,   //Number of tables completed full load
            "fullLoadErroredTables": 0,     //Number of tables that contain migration error
            "fullLoadLoadingTables": 0,     //Number of tables that are in loading status
            "fullLoadQueuedTables": 0,      //Number of tables that are in queued status
            "id": "db|inventory",
            "incomingChanges": 0,           //Number of changes after full load
            "initializationCompleted": true,
            "latency": 0,
            "migrationState": "READY_TO_COMPLETE",    //Status of migration task. READY_TO_COMPLETE means the database is ready for cutover
            "resultType": "DatabaseLevelOutput",
            "startedOn": "2018-07-05T23:36:02.27839+00:00"
          },
          {
            "databaseCount": 1,
            "endedOn": null,
            "id": "dd27aa3a-ed71-4bff-ab34-77db4261101c",
            "resultType": "MigrationLevelOutput",
            "sourceServer": "138.91.123.10",
            "sourceVersion": "PostgreSQL",
            "startedOn": "2018-07-05T23:36:02.27839+00:00",
            "state": "PENDING",
            "targetServer": "builddemotarget.postgres.database.azure.com",
            "targetVersion": "Azure Database for PostgreSQL"
          },
          {                                        // Table 1
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 0,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:20.740701+00:00",    //Full load completed time
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:15.864552+00:00",    //Full load started time
            "fullLoadTotalRows": 10,                     //Number of rows loaded in full load
            "fullLoadTotalVolumeBytes": 7056,            //Volume in Bytes in full load
            "id": "or|inventory|public|actor",
            "lastModifiedTime": "2018-07-05T23:36:16.880174+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",                       //State of migration for this table
            "tableName": "public.catalog",              //Table name
            "totalChangesApplied": 0                    //Total sync changes that applied after full load
          },
          {                                            //Table 2
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 50,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:23.963138+00:00",
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:19.302013+00:00",
            "fullLoadTotalRows": 112,
            "fullLoadTotalVolumeBytes": 46592,
            "id": "or|inventory|public|address",
            "lastModifiedTime": "2018-07-05T23:36:20.308646+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",
            "tableName": "public.orders",
            "totalChangesApplied": 0
          }
        ],                                      // DMS migration task state
        "state": "Running",    //Migration task state – Running means it is still listening to any changes that might come in
        "taskType": null
      },
      "resourceGroup": "PostgresDemo",
      "type": "Microsoft.DataMigration/services/projects/tasks"
  ```

## <a name="cutover-migration-task"></a>Tarefa de migração de transferência

A base de dados está pronta para transferência quando o carregamento completo estiver concluído. Consoante o estado de disponibilidade do servidor de origem com novas transações a entrar, a tarefa DMS pode estar ainda a aplicar as alterações após a conclusão do carregamento completo.

Para garantir que todos os dados são capturados, valide as contagens de linhas entre as bases de dados de origem e de destino. Por exemplo, pode utilizar o seguinte comando:

```
"migrationState": "READY_TO_COMPLETE", //Status of migration task. READY_TO_COMPLETE means database is ready for cutover
 "incomingChanges": 0, //continue to check for a period of 5-10 minutes to make sure no new incoming changes that need to be applied to the target server
   "fullLoadTotalRows": 10, //full load for table 1
    "cdcDeleteCounter": 0, //delete, insert and update counter on incremental sync after full load
    "cdcInsertCounter": 50,
    "cdcUpdateCounter": 0,
     "fullLoadTotalRows": 112, //full load for table 2
```

1. Execute a tarefa de migração da base de dados de transferência com o seguinte comando:

    ```azurecli
    az dms project task cutover -h
    ```

    Por exemplo:

    ```azurecli
    az dms project task cutover --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask  --object-name Inventory
    ```

2. Para monitorizar o progresso da transferência, execute o seguinte comando:

    ```azurecli
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```
3. Quando o estado de migração da base de dados mostrar **concluído,** [recrie as sequências](https://wiki.postgresql.org/wiki/Fixing_Sequences) (se aplicável) e ligue as suas aplicações à nova instância-alvo da Base de Dados Azure para PostgreSQL.

## <a name="service-project-task-cleanup"></a>Limpeza de serviços, projetos, tarefas

Se precisar de cancelar ou eliminar qualquer tarefa, projeto ou serviço do DMS, execute o cancelamento na sequência seguinte:

* Cancele a tarefa em execução
* Elimine a tarefa
* Elimine o projeto
* Elimine o serviço DMS

1. Para cancelar uma tarefa em execução, utilize o seguinte comando:

    ```azurecli
    az dms project task cancel --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
     ```

2. Para eliminar uma tarefa em execução, utilize o seguinte comando:
    ```azurecli
    az dms project task delete --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

3. Para cancelar um projeto em execução, utilize o seguinte comando:
     ```azurecli
    az dms project task cancel -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
     ```

4. Para eliminar um projeto em execução, utilize o seguinte comando:
    ```azurecli
    az dms project task delete -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
    ```

5. Para eliminar o serviço DMS, utilize o seguinte comando:

     ```azurecli
    az dms delete -g ProgresDemo -n PostgresCLI
     ```

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre problemas conhecidos e limitações ao realizar migrações online para a Base de Dados do Azure para PostgreSQL, veja o artigo [Problemas conhecidos e soluções alternativas das migrações online da Base de Dados do Azure para PostgreSQL](known-issues-azure-postgresql-online.md).
* Para obter informações sobre o Azure Database Migration Service, leia o artigo [O que é o Azure Database Migration Service?](./dms-overview.md).
* Para obter informações sobre a Base de Dados Azure para PostgreSQL, consulte o artigo [O que é a Base de Dados Azure para PostgreSQL?](../postgresql/overview.md)
