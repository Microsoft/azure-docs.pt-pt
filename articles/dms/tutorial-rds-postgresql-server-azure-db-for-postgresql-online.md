---
title: 'Tutorial: Migrar RDS PostgreSQL on-line para Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a realizar uma migração on-line de RDS PostgreSQL para Azure Database for PostgreSQL utilizando o Serviço de Migração da Base de Dados Azure.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: c2a6a71365b48fa4349306ce632f5762c38dacf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597475"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-db-for-postgresql-online-using-dms"></a>Tutorial: Migrar RDS PostgreSQL para Azure DB para PostgreSQL on-line usando DMS

Pode utilizar o Azure Database Migration Service para migrar bases de dados de um caso RDS PostgreSQL para [Azure Database for PostgreSQL](../postgresql/index.yml) enquanto a base de dados de origem permanece on-line durante a migração. Por outras palavras, a migração pode ser alcançada com um mínimo de tempo de inatividade para a aplicação. Neste tutorial, migra a base de dados de amostras **de DVD Rental** de um exemplo de RDS PostgreSQL 9.6 para Azure Database for PostgreSQL utilizando a atividade de migração on-line no Azure Database Migration Service.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Migrar o esquema da amostra utilizando o utilitário pg_dump.
> * Crie uma instância do Azure Database Migration Service.
> * Crie um projeto de migração utilizando o Serviço de Migração da Base de Dados Azure.
> * Executar a migração.
> * Monitorizar a migração.
> * Executar corte de migração.

> [!NOTE]
> A utilização do Serviço de Migração da Base de Dados Azure para realizar uma migração online requer a criação de um caso baseado no nível de preços Premium. Para mais informações, consulte a página de preços do Serviço [de Migração](https://azure.microsoft.com/pricing/details/database-migration/) da Base de Dados Azure. Encriptamos o disco para evitar o roubo de dados durante o processo de migração.

> [!IMPORTANT]
> Para uma experiência de migração otimizada, a Microsoft recomenda a criação de uma instância do Azure Database Migration Service na mesma região do Azure que a base de dados de destino. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve como realizar uma migração on-line de uma instância no local de PostgreSQL para Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Faça o download e instale [a edição comunitária postgreSQL](https://www.postgresql.org/download/) 9.5, 9.6 ou 10. A versão do Servidor PostgreSQL de origem deve ser 9.5.11, 9.6.7, 10 ou mais tarde. Para obter mais informações, consulte o artigo [Versões postgresQL suportadas na base de dados.](../postgresql/concepts-supported-versions.md)

   Note também que a base de dados Azure alvo para a versão PostgreSQL deve ser igual ou mais tarde à versão RDS PostgreSQL. Por exemplo, RDS PostgreSQL 9.6 só pode migrar para a Base de Dados Azure para PostgreSQL 9.6, 10 ou 11, mas não para Azure Database para PostgreSQL 9.5.

* Criar uma instância da Base de [Dados Azure para PostgreSQL](../postgresql/quickstart-create-server-database-portal.md) ou [Azure Database para PostgreSQL - Hyperscale (Citus)](../postgresql/quickstart-create-hyperscale-portal.md). Consulte esta [secção](../postgresql/quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) do documento para obter detalhes sobre como ligar-se ao Servidor PostgreSQL utilizando pgAdmin.
* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-to-site aos seus servidores de origem no local, utilizando [expressRoute](../expressroute/expressroute-introduction.md) ou [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação da Rede Virtual,](../virtual-network/index.yml)e especialmente os artigos quickstart com detalhes passo a passo.
* Certifique-se de que as regras do Grupo de Segurança da Rede de Rede Virtual não bloqueiam a porta de saída 443 do ServiceTag para ServiceBus, Storage e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego NSG da rede virtual, consulte o artigo Filtrar o [tráfego da rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra a firewall do Windows para permitir que o Serviço de Migração da Base de Dados de Azure aceda ao servidor PostgreSQL de origem, que por padrão é a porta TCP 5432.
* Se estiver a utilizar uma aplicação de firewall à frente da base ou bases de dados, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base ou bases de dados de origem para migração.
* Crie uma regra de [firewall](../postgresql/concepts-firewall-rules.md) ao nível do servidor Azure para o servidor PostgreSQL para permitir o acesso do Serviço de Migração da Base de Dados Azure às bases de dados-alvo. Forneça a gama de sub-redes da rede virtual utilizada para o Serviço de Migração da Base de Dados Azure.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>Configurar AWS RDS PostgreSQL para replicação

1. Para criar um novo grupo de parâmetros, siga as instruções fornecidas pela AWS no artigo [Trabalhando com grupos de parâmetros DB](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html).
2. Utilize o nome principal do utilizador para ligar à fonte do Serviço de Migração da Base de Dados Azure. Se utilizar uma conta diferente da conta principal do utilizador, a conta deve ter a função rds_superuser e a função rds_replication. A função rds_replication concede permissões para gerir slots lógicos e para transmitir dados usando slots lógicos.
3. Criar um novo grupo de parâmetros com a seguinte configuração:

    a. Descreva o parâmetro rds.logical_replication no seu grupo de parâmetros DB para 1.

    b. max_wal_senders =[número de tarefas simultâneas] - O parâmetro max_wal_senders define o número de tarefas simultâneas que podem ser executadas, recomenda 10 tarefas.

    c. max_replication_slots – = [número de slots], recomenda-se definir para cinco slots.

4. Associe o grupo de parâmetros que criou para a instância RDS PostgreSQL.

## <a name="migrate-the-schema"></a>Migrar o esquema

1. Extrair o esquema da base de dados de origem e aplicar na base de dados-alvo para completar a migração de todos os objetos de base de dados, tais como esquemas de tabelas, índices e procedimentos armazenados.

    A maneira mais fácil de migrar apenas o esquema é usá pg_dump com a opção -s. Para mais informações, consulte os [exemplos](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) no tutorial de pg_dump Postgres.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Por exemplo, para despejar um ficheiro de esquema para a base **de dados dvdrental,** utilize o seguinte comando:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Crie uma base de dados vazia no serviço alvo, que é a Base de Dados Azure para PostgreSQL. Para ligar e criar uma base de dados, consulte um dos seguintes artigos:

    * [Criar uma base de dados Azure para servidor PostgreSQL utilizando o portal Azure](../postgresql/quickstart-create-server-database-portal.md)
    * [Criar uma base de dados Azure para o servidor PostgreSQL - Hiperescala (Citus) utilizando o portal Azure](../postgresql/quickstart-create-hyperscale-portal.md)

3. Importe o esquema para o serviço de destino, que é Azure Database for PostgreSQL. Para restaurar o ficheiro de despejo de esquema, execute o seguinte comando:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Por exemplo:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Se tiver chaves externas no seu esquema, o carregamento inicial e a sincronização contínua da migração irão falhar. Para extrair o script de chave estrangeira gota e adicionar script de chave estrangeira no destino (Azure Database for PostgreSQL), executar o seguinte script em PgAdmin ou em psql:
  
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

5. Passe a chave estrangeira de queda (que é a segunda coluna) no resultado da consulta para deixar cair a tecla estrangeira.

6. Se tiver gatilhos (inserir ou atualizar o gatilho) nos dados, irá impor a integridade dos dados no alvo antes de replicar dados da fonte. A recomendação é desativar os gatilhos em todas as tabelas *do alvo* durante a migração e, em seguida, ativar os gatilhos após a conclusão da migração.

    Para desativar os gatilhos na base de dados-alvo:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name,' ON ', event_object_table, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores de Recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do Azure Database Migration Service

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização em que pretende criar a instância do Serviço de Migração da Base de Dados Azure.

5. Selecione uma rede virtual existente ou crie uma nova.

    A rede virtual fornece ao Azure Database Migration Service o acesso à origem postgreSQL e à base de dados Azure alvo para a instância PostgreSQL.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure](../virtual-network/quick-create-portal.md).

6. Selecione um nível de preços; para esta migração on-line, não se esqueça de selecionar o nível de preços Premium: 4vCores.

    ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings5.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

      ![Localizar todas as instâncias do Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. No ecrã dos **Serviços de Migração da Base de Dados Azure,** procure o nome da instância do Serviço de Migração da Base de Dados Azure que criou, selecione o caso e, em seguida, selecione + **Novo Projeto de Migração**.
3. No novo ecrã do **projeto de migração,** especifique um nome para o projeto, na caixa de texto **do tipo do servidor Fonte,** selecione **AWS RDS para PostgreSQL** e, em seguida, na caixa de texto **do tipo do servidor Target,** selecione **Azure Database for PostgreSQL**.
4. Na secção Escolha o **tipo de atividade,** selecione Migração de **dados Online.**

    > [!IMPORTANT]
    > Certifique-se de selecionar **migração de dados online;** as migrações offline não são apoiadas para este cenário.

    ![Criar Projeto do Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > Em alternativa, pode escolher **o projeto Criar apenas** para criar o projeto de migração agora e executar a migração mais tarde.

5. Selecione **Guardar**.

6. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

    > [!NOTE]
    > Por favor, tome nota dos pré-requisitos necessários para a criação de migração online na lâmina de criação de projetos.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

* No ecrã **'Adicionar Detalhes fonte',** especifique os detalhes da ligação para a origem da instância PostgreSQL.

   ![Detalhes da origem](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Selecione **Guardar**, e, em seguida, no ecrã **de detalhes do Target,** especifique os detalhes de ligação para o target Azure Database para o servidor PostgreSQL, que é pré-a provisionado e tem o esquema **de Aluguer de DVD** implantado usando pg_dump.

    ![Detalhes do destino](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-target-details.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se a base de dados-alvo contiver o mesmo nome de base de dados que a base de dados de origem, o Azure Database Migration Service seleciona a base de dados-alvo por predefinição.

    ![Mapear para as bases de dados de destino](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-target-databases.png)

3. Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e, em seguida, reveja o resumo para garantir que os detalhes de origem e de destino correspondem aos que foram anteriormente especificados.

    ![Resumo da migração](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

    É apresentada a janela de atividade da migração e o **Estado** da atividade é **A inicializar**.

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Em execução**.

    ![Estado de Atividade - execução](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. Em **NOME BASE DE DADOS**, selecione uma base de dados específica para chegar ao estado de migração para **operações de sincronização de dados completos** e **de sincronização de dados incrementais.**

    **A carga completa de dados** mostra o estado inicial da migração da carga, enquanto o sync incremental de **dados** mostra o estado da captura de dados de alteração (CDC).

    ![Ecrã de inventário - carga completa de dados](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Ecrã de inventário - sincronização incremental de dados](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Realizar transferência de migração

Depois de concluída a carga inicial full, as bases de dados estão marcadas **Pronto para Cortar**.

1. Quando estiver pronto para concluir a migração da base de dados, selecione **Iniciar Transferência**.

2. Aguarde até que o contador **de alterações pendentes** mostre **0** para garantir que todas as transações recebidas na base de dados de origem sejam interrompidas, selecione a caixa de verificação **Confirmar** e, em seguida, selecione **Apply**.

    ![Tela de corte completa](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-complete-cutover.png)

3. Quando o estado de migração da base de dados mostrar **concluído,** ligue as suas aplicações à nova base de dados Azure Database para a base de dados PostgreSQL.

A sua migração on-line de um caso no local de RDS PostgreSQL para Azure Database for PostgreSQL está agora completa.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre o Azure Database Migration Service, leia o artigo [O que é o Azure Database Migration Service?](./dms-overview.md).
* Para obter informações sobre a Base de Dados Azure para PostgreSQL, consulte o artigo [O que é a Base de Dados Azure para PostgreSQL?](../postgresql/overview.md)
* Para outras questões, envie um e-mail para o [pseudónimo Ask Azure Database Migrations.](mailto:AskAzureDatabaseMigrations@service.microsoft.com)
