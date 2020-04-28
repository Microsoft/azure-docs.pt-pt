---
title: 'Tutorial: Migrar PostgreSQL para Azure DB para PostgreSQL online através do portal Azure'
titleSuffix: Azure Database Migration Service
description: Aprenda a realizar uma migração on-line de PostgreSQL no local para Azure Database for PostgreSQL utilizando o Serviço de Migração de Bases de Dados Azure através do portal Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 04/11/2020
ms.openlocfilehash: e01cc1c07d720c4743a03b5001e640f8b851dd5c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81114006"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Tutorial: Migrar PostgreSQL para Azure DB para PostgreSQL online usando DMS através do portal Azure

Pode utilizar o Serviço de Migração de Bases de Dados Azure para migrar as bases de dados de uma instância postgresql no local para a Base de [Dados Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/) com o mínimo de tempo de inatividade na aplicação. Neste tutorial, você migra a base de dados de amostras de aluguer de **DVD** de uma instância no local de PostgreSQL 9.6 para Azure Database for PostgreSQL, utilizando a atividade de migração on-line no Serviço de Migração de Bases de Dados Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Migrar o esquema da amostra utilizando o utilitário pg_dump.
> * Crie uma instância do Azure Database Migration Service.
> * Crie um projeto de migração no Serviço de Migração de Bases de Dados Azure.
> * Executar a migração.
> * Monitorizar a migração.
> * Executar a redução da migração.

> [!NOTE]
> A utilização do Serviço de Migração de Bases de Dados Azure para realizar uma migração online requer a criação de uma instância baseada no nível de preços Premium. Encriptamos o disco para evitar o roubo de dados durante o processo de migração

> [!IMPORTANT]
> Para uma experiência de migração ideal, a Microsoft recomenda a criação de uma instância do Serviço de Migração de Bases de Dados Azure na mesma região do Azure que a base de dados alvo. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Descarregue e instale a [edição comunitária PostgreSQL](https://www.postgresql.org/download/) 9.4, 9.5, 9.6 ou 10. A versão de origem PostgreSQL Server deve ser 9.4, 9.5, 9.6, 10 ou 11. Para mais informações, consulte o artigo [Versões de Base de Dados PostgreSQL suportadas](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Note também que o target Azure Database para a versão PostgreSQL deve ser igual ou posterior à versão postgresQL no local. Por exemplo, postgreSQL 9.6 pode migrar para a Base de Dados Azure para PostgreSQL 9.6, 10 ou 11, mas não para azure Database para PostgreSQL 9.5.

* [Crie uma Base de Dados Azure para servidor PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) ou [crie uma base de dados Azure para o servidor PostgreSQL - Hyperscale (Citus).](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)
* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-a-site aos seus servidores de origem no local, utilizando [expressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação](https://docs.microsoft.com/azure/virtual-network/)da Rede Virtual , e especialmente os artigos quickstart com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se utilizar o ExpressRoute com o peering da rede para a Microsoft, adicione os [seguintes pontos finais](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à subnet na qual o serviço será aprovisionado:
    >
    > * Ponto final da base de dados alvo (por exemplo, ponto final SQL, ponto final cosmos DB, e assim por diante)
    > * Ponto final de armazenamento
    > * Ponto final do ônibus de serviço
    >
    > Esta configuração é necessária porque o Serviço de Migração de Bases de Dados Azure carece de conectividade na Internet.

* Certifique-se de que as regras do Grupo de Segurança da Rede (NSG) para a sua rede virtual não bloqueiam as seguintes portas de comunicação de entrada para o Serviço de Migração de Bases de Dados Azure: 443, 53, 9354, 445, 12000. Para mais detalhes sobre a filtragem de tráfego da rede virtual NSG, consulte o artigo Filtrar o tráfego da [rede com grupos](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)de segurança da rede .
* Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra a sua firewall windows para permitir que o Serviço de Migração da Base de Dados Azure aceda à fonte PostgreSQL Server, que por padrão é a porta TCP 5432.
* Se estiver a utilizar uma aplicação de firewall à frente da base ou bases de dados, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base ou bases de dados de origem para migração.
* Crie uma [regra](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) de firewall ao nível do servidor para a Base de Dados Azure para postgreSQL para permitir que o Serviço de Migração de Bases de Dados Azure aceda às bases de dados-alvo. Forneça a gama de subredes da rede virtual utilizada para o Serviço de Migração de Bases de Dados Azure.
* Ative a replicação lógica no ficheiro postgresql.config e defina os seguintes parâmetros:

  * wal_level = **logical**
  * max_replication_slots = [número de slots], recomendar a definição de **cinco slots**
  * max_wal_senders = [número de tarefas simultâneas] – O parâmetro max_wal_senders define o número de tarefas simultâneas que podem ser executadas, definição recomendada de **10 tarefas**

> [!IMPORTANT]
> Todas as tabelas na sua base de dados existentes precisam de uma chave primária para garantir que as alterações podem ser sincronizadas na base de dados do alvo.

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo

Para concluir todos os objetos de base de dados, como esquemas de tabela, índices e procedimentos armazenados, é necessário extrair o esquema da base de dados de origem e aplicar à base de dados.

1. Utilize o comando pg_dump -s para criar um ficheiro de captura de esquema para uma base de dados.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Por exemplo, criar um ficheiro de despejo de esquemas para a base de dados de aluguer de **DVD:**

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Para obter mais informações sobre como utilizar o utilitário pg_dump, veja os exemplos no tutorial [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Crie uma base de dados vazia no ambiente de destino, que é a Base de Dados do Azure para PostgreSQL.

    Para mais detalhes sobre como conectar e criar uma base de dados, consulte o artigo Criar uma Base de [Dados Azure para servidor PostgreSQL no portal Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) ou Criar uma Base de [Dados Azure para servidor PostgreSQL - Hyperscale (Citus) no portal Azure.](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)

    > [!NOTE]
    > Uma instância de Base de Dados Azure para PostgreSQL - Hyperscale (Citus) tem apenas uma única base de dados: **citus**.

3. Importe o esquema para a base de dados de destino que criou ao restaurar o ficheiro de captura de esquema.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Por exemplo:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Para extrair o script de chave estrangeira drop e adicioná-lo no destino (Base de Dados Azure para PostgreSQL), em PgAdmin ou no psql, executar o seguinte script.

   > [!IMPORTANT]
   > As chaves estrangeiras no seu esquema farão com que a carga inicial e a sincronização contínua da migração falhem.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ', foreignkey), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ', foreignkey, ' FOREIGN KEY (', column_name, ')', ' REFERENCES ', foreign_table_schema, '.', foreign_table_name, '(', foreign_column_name, ')' ), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. Execute o script de remoção de chave externa (que é a segunda coluna) no resultado da consulta.

6. Para desativar os gatilhos na base de dados do alvo, execute o script abaixo.

   > [!IMPORTANT]
   > Os gatilhos (inserir ou atualizar) nos dados impõem a integridade dos dados no alvo antes de os dados serem replicados a partir da fonte. Como resultado, recomenda-se que desative os gatilhos em todas as tabelas **do alvo** durante a migração e, em seguida, reativar os gatilhos após a migração estar completa.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores**de Recursos .

    ![Mostrar fornecedores de recursos](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Criar uma instância do DMS

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. No ecrã **do Serviço de Migração Create,** especifique um nome, a subscrição, um novo ou existente grupo de recursos e a localização do serviço.

4. Selecione uma rede virtual existente ou crie uma nova.

    A rede virtual fornece ao Azure Database Migration Service acesso ao servidor de origem PostgreSQL e à base de dados azure-alvo para a instância PostgreSQL.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure.](https://aka.ms/DMSVnet)

5. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Selecione **Review + crie** para criar o serviço.

   A criação de serviço sairá dentro de cerca de 10 a 15 minutos.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

      ![Localize todas as instâncias do Serviço de Migração da Base de Dados Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. No ecrã **azure Database Migration Services,** procure o nome da instância do Serviço de Migração de Bases de Dados Azure que criou, selecione a instância e, em seguida, selecione + **Novo Projeto de Migração**.

3. No ecrã do **novo projeto de migração,** especifique um nome para o projeto, na caixa de texto **do tipo servidor Fonte,** selecione **PostgresSQL**, na caixa de texto **do tipo servidor Target,** selecione Base de **Dados Azure para PostgreSQL**.

4. Na secção **Escolha tipo de atividade,** selecione **migração de dados online**.

    ![Criar projeto de Serviço de Migração de Bases de Dados Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Alternadamente, pode escolher o **projeto Criar apenas** para criar o projeto de migração agora e executar a migração mais tarde.

5. Selecione **Guardar,** note os requisitos para utilizar com sucesso o Serviço de Migração de Bases de Dados Azure para migrar dados e, em seguida, selecione **Criar e executar atividade**.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã **Adicionar Detalhes de Origem,** especifique os detalhes de ligação para a instância de origem PostgreSQL.

    ![Ecrã Adicionar Detalhes da Origem](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. Selecione **Guardar**.

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. No ecrã de detalhes do **Target,** especifique os detalhes de ligação para o servidor target Hyperscale (Citus), que é a instância pré-provisionada de Hyperscale (Citus) para o qual o **DVD Rentals** foi implementado utilizando pg_dump.

    ![Ecrã Detalhes do destino](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se a base de dados-alvo contiver o mesmo nome de base de dados que a base de dados de origem, o Serviço de Migração da Base de Dados Azure seleciona a base de dados de destino por padrão.

    ![Mapa para o ecrã de bases de dados alvo](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Selecione **Guardar**, e depois no ecrã de definições de **Migração,** aceite os valores predefinidos.

    ![Tela de definições de migração](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e, em seguida, reveja o resumo para garantir que os detalhes de origem e de destino correspondem aos que foram anteriormente especificados.

    ![Tela sumária de migração](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

    A janela da atividade migratória aparece, e o **Estado** da atividade deve ser atualizado para mostrar como **Backup in Progress**.

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade de migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Concluída**.

     ![Monitorizar o processo de migração](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Quando a migração estiver concluída, sob o nome da base de **dados,** selecione uma base de dados específica para chegar ao estado de migração para a **carga completa** de dados e operações **incrementais de sincronização de dados.**

   > [!NOTE]
   > **A carga completa de dados** mostra o estado inicial de migração da carga, enquanto a **sincronização** de dados incremental mostra o estado de captura de dados de alteração (CDC).

     ![Detalhes completos da carga de dados](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Detalhes incrementais da sincronização de dados](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Realizar transferência de migração

Depois de concluída a Carga completa inicial, as bases de dados são marcadas como **Preparadas para transferência**.

1. Quando estiver pronto para concluir a migração da base de dados, selecione **Iniciar Transferência**.

2. Aguarde até que o contador **de alterações pendente** mostre **0** para garantir que todas as transações de entrada na base de dados de origem estão paradas, selecione a caixa de verificação **Confirmar** e, em seguida, selecione **Aplicar**.

    ![Tela completa de cutover](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Quando o estado de migração da base de dados mostrar **concluído,** ligue as suas aplicações à nova instância-alvo da Base de Dados Azure para PostgreSQL.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre problemas conhecidos e limitações ao realizar migrações online para a Base de Dados do Azure para PostgreSQL, veja o artigo [Problemas conhecidos e soluções alternativas das migrações online da Base de Dados do Azure para PostgreSQL](known-issues-azure-postgresql-online.md).
* Para obter informações sobre o Azure Database Migration Service, leia o artigo [O que é o Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Para obter informações sobre a Base de Dados Azure para PostgreSQL, consulte o artigo O que é a Base de [Dados Azure para PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview)
