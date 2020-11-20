---
title: 'Tutorial: Migrar RDS MySQL on-line para Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a realizar uma migração on-line de RDS MySQL para Azure Database for MySQL utilizando o Serviço de Migração da Base de Dados Azure.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 06/09/2020
ms.openlocfilehash: b8d5c763b68a9f69add14ab8430c117e5705a515
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955094"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Tutorial: Migrar o RDS MySQL para a Base de Dados do Azure para MySQL online com o DMS

Pode utilizar o Azure Database Migration Service para migrar bases de dados de um caso RDS MySQL para [Azure Database for MySQL](../mysql/index.yml) enquanto a base de dados de origem permanece on-line durante a migração. Por outras palavras, a migração pode ser alcançada com um mínimo de tempo de inatividade para a aplicação. Neste tutorial, migra a base de dados de amostras de **Empregados** de um caso de RDS MySQL para Azure Database for MySQL utilizando a atividade de migração online no Azure Database Migration Service.

Neste tutorial, vai aprender a:
> [!div class="checklist"]
>
> * Migrar o esquema da amostra usando os utilitários mysqldump e mysql.
> * Crie uma instância do Azure Database Migration Service.
> * Crie um projeto de migração utilizando o Serviço de Migração da Base de Dados Azure.
> * Executar a migração.
> * Monitorizar a migração.

> [!NOTE]
> A utilização do Azure Database Migration Service para realizar uma migração online requer a criação de um caso baseado no nível de preços Premium. Para mais informações, consulte a página de preços do Serviço [de Migração](https://azure.microsoft.com/pricing/details/database-migration/) da Base de Dados Azure.

> [!IMPORTANT]
> Para uma experiência de migração otimizada, a Microsoft recomenda a criação de uma instância do Azure Database Migration Service na mesma região do Azure que a base de dados de destino. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve como realizar uma migração on-line de um caso de RDS MySQL para Azure Database para o MySQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Certifique-se de que o servidor MySQL de origem está a executar uma edição comunitária suportada do MySQL. Para determinar a versão do seu exemplo MySQL, no utilitário mysql ou na bancada MySQL, executar o comando:

    ```
    SELECT @@version;
    ```

    Para obter mais informações, consulte o artigo [Base de Dados Azure suportado para versões MySQL](../mysql/concepts-supported-versions.md).

* Descarregue e instale a [base de dados de **amostras** mySQL Employees](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* Crie uma instância da Base de [Dados Azure para o MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md).
* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-to-site aos seus servidores de origem no local, utilizando [expressRoute](../expressroute/expressroute-introduction.md) ou [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação da Rede Virtual,](../virtual-network/index.yml)e especialmente os artigos quickstart com detalhes passo a passo.
* Certifique-se de que as regras do grupo de segurança da rede virtual não bloqueiam as seguintes portas de comunicação de entrada para o Serviço de Migração da Base de Dados Azure: 443, 53, 9354, 445 e 12000. Para obter mais detalhes sobre a filtragem de tráfego NSG da rede virtual, consulte o artigo Filtrar o [tráfego da rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Configure o [Windows Firewall](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) (ou a firewall Do Linux) para permitir o acesso ao motor da base de dados. Para o servidor MySQL, permita a porta 3306 para conectividade.

> [!NOTE]
> A Azure Database for MySQL suporta apenas as tabelas InnoDB. Para converter as tabelas MyISAM para InnoDB, consulte o artigo [Convertendo tabelas do MyISAM para o InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) .

### <a name="set-up-aws-rds-mysql-for-replication"></a>Configurar AWS RDS MySQL para replicação

1. Para criar um novo grupo de parâmetros, siga as instruções fornecidas pela AWS no artigo [MySQL Database Log Files](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html), na secção **Formato de Registo de Registos Binários.**
2. Criar um novo grupo de parâmetros com a seguinte configuração:
    * log_bin = ON
    * binlog_format = row
    * binlog_checksum = NENHUMA
3. Salve o novo grupo de parâmetros.
4. Associe o novo grupo de parâmetros à instância RDS MySQL. Pode ser necessário um reboot.
5. Uma vez colocado o grupo de parâmetros, ligue-se à instância MySQL e [desembarate a retenção do binlog](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_set_configuration.html#mysql_rds_set_configuration-usage-notes.binlog-retention-hours) para pelo menos 5 dias.
```
call mysql.rds_set_configuration('binlog retention hours', 120);
```

## <a name="migrate-the-schema"></a>Migrar o esquema

1. Extrair o esquema da base de dados de origem e aplicar na base de dados-alvo para completar a migração de todos os objetos de base de dados, tais como esquemas de tabelas, índices e procedimentos armazenados.

    A maneira mais fácil de migrar apenas o esquema é usar o mysqldump com o parâmetro sem dados. O comando para migrar o esquema é:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Por exemplo, para despejar um ficheiro de esquema para a base de **dados dos Empregados,** utilize o seguinte comando:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Importe o esquema para o serviço de destino, que é Azure Database para mySQL. Para restaurar o ficheiro de despejo de esquema, execute o seguinte comando:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Por exemplo, importar o esquema para a base **de dados dos empregados:**

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Se tiver chaves externas no seu esquema, o carregamento inicial e a sincronização contínua da migração irão falhar. Para extrair o script de chave estrangeira gota e adicionar script de chave estrangeira no destino (Azure Database for MySQL), executar o seguinte script na bancada mySQL Workbench:

    ```
    SET group_concat_max_len = 8192;
        SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
        FROM
        (SELECT
        KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
                    KCU.TABLE_NAME,
                    KCU.COLUMN_NAME,
                    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
                    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
                    WHERE
                      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
                      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
      GROUP BY SchemaName;
    ```

4. Passe a chave estrangeira de queda (que é a segunda coluna) no resultado da consulta para deixar cair a tecla estrangeira.

> [!NOTE]
> O Azure DMS não suporta a ação referencial CASCADE, que ajuda a eliminar ou atualizar automaticamente uma linha de correspondência na tabela infantil quando uma linha é eliminada ou atualizada na tabela dos pais. Para obter mais informações, na documentação do MySQL, consulte a secção Ações Referenciais do artigo [Restrições DE CHAVE ESTRANGEIRA](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html).
> O Azure DMS requer que deixe cair os constrangimentos de chaves estrangeiras no servidor de base de dados-alvo durante a carga inicial de dados, e não pode utilizar ações referenciais. Se a sua carga de trabalho depender da atualização de uma tabela de crianças relacionada através desta ação referencial, recomendamos que efetue uma [lixeira e restaure.](../mysql/concepts-migrate-dump-restore.md) 

5. Se tiver gatilhos (inserir ou atualizar o gatilho) nos dados, irá impor a integridade dos dados no alvo antes de replicar dados da fonte. A recomendação é desativar os gatilhos em todas as tabelas *do alvo* durante a migração e, em seguida, ativar os gatilhos após a conclusão da migração.

    Para desativar os gatilhos na base de dados-alvo:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Se existirem casos do tipo de dados ENUM em quaisquer tabelas, recomendamos uma atualização temporária do tipo de dados "variando o carácter" na tabela-alvo. A replicação de dados WHen está completa e, em seguida, reverte o tipo de dados para ENUM.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores de Recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do Azure Database Migration Service

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização em que pretende criar a instância do Serviço de Migração da Base de Dados Azure.

5. Selecione uma rede virtual existente ou crie uma nova.

    A rede virtual fornece ao Azure Database Migration Service o acesso à origem mySQL instância e à base de dados Azure alvo para o exemplo mySQL.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure](../virtual-network/quick-create-portal.md).

6. Selecione um nível de preços; para esta migração on-line, não se esqueça de selecionar o nível de preços Premium: 4vCores.

    ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

      ![Localizar todas as instâncias do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. No ecrã **Azure Database Migration Services**, procure o nome da instância do Azure Database Migration Service que criou e selecione-a.

     ![Localizar a instância do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Selecione + **Novo Projeto de Migração**.
4. No novo ecrã do **projeto de migração,** especifique um nome para o projeto, na caixa de texto **do tipo servidor Source,** selecione **MySQL** e, em seguida, na caixa de texto do tipo alvo do **servidor,** selecione **AzureDbForMySQL**.
5. Na secção Escolha o **tipo de atividade,** selecione Migração de **dados Online.**

    > [!IMPORTANT]
    > Certifique-se de selecionar **migração de dados online;** as migrações offline não são apoiadas para este cenário.

    ![Criar Projeto do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Em alternativa, pode escolher **o projeto Criar apenas** para criar o projeto de migração agora e executar a migração mais tarde.

6. Selecione **Guardar**.

7. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

    > [!NOTE]
    > Por favor, tome nota dos pré-requisitos necessários para a criação de migração online na lâmina de criação de projetos.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

* No ecrã de detalhes da **fonte de migração,** especifique os detalhes da ligação para a origem da ocorrência MySQL.

   ![Detalhes da origem](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Selecione **Save**, e, em seguida, no ecrã **de detalhes do Destino,** especifique os detalhes de conexão para o target Azure Database para o servidor MySQL, que é pré-a provisionado e tem o esquema **de Empregados** implantado usando o MySQLDump.

    ![Selecionar o Destino](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se a base de dados-alvo contiver o mesmo nome de base de dados que a base de dados de origem, o Azure Database Migration Service seleciona a base de dados-alvo por predefinição.

    ![Mapear para as bases de dados de destino](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e, em seguida, reveja o resumo para garantir que os detalhes de origem e de destino correspondem aos que foram anteriormente especificados.

    ![Resumo da migração](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

    É apresentada a janela de atividade da migração e o **Estado** da atividade é **A inicializar**.

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Em execução**.

    ![Estado de Atividade - execução](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. Em **NOME BASE DE DADOS**, selecione uma base de dados específica para chegar ao estado de migração para **operações de sincronização de dados completos** e **de sincronização de dados incrementais.**

    **A carga completa de dados** mostra o estado inicial da migração da carga, enquanto o sync incremental de **dados** mostra o estado da captura de dados de alteração (CDC).

    ![Ecrã de inventário - carga completa de dados](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Ecrã de inventário - sincronização incremental de dados](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Realizar transferência de migração

Depois de concluída a carga inicial full, as bases de dados estão marcadas **Pronto para Cortar**.

1. Quando estiver pronto para concluir a migração da base de dados, selecione **Iniciar Transferência**.

    ![Começar a cortar](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Certifique-se de que para todas as transações de entrada para a base de dados de origem; aguarde até que o contador **Alterações pendentes** mostre **0**.
3. Selecione **Confirmar** e depois **Aplicar**.
4. Quando o estado de migração da base de dados mostrar **concluído,** ligue as suas aplicações à nova base de dados Azure Para o novo alvo para a base de dados MySQL.

A sua migração on-line de um caso no local do MySQL para a Azure Database para o MySQL está agora concluída.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre o Azure Database Migration Service, leia o artigo [O que é o Azure Database Migration Service?](./dms-overview.md).
* Para obter informações sobre a Base de Dados Azure para o MySQL, consulte o artigo [O que é a Base de Dados Azure para o MySQL?](../mysql/overview.md)
* Para outras questões, envie um e-mail para o [pseudónimo Ask Azure Database Migrations.](mailto:AskAzureDatabaseMigrations@service.microsoft.com)