---
title: 'Tutorial: Migrar RDS MySQL on-line para Azure Database para MySQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a realizar uma migração on-line de RDS MySQL para Azure Database for MySQL utilizando o Serviço de Migração de Bases de Dados Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c34de48d0184057f42d1b779abee56e1fa9ac169
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78255156"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Tutorial: Migrar RDS MySQL para Azure Database para MySQL on-line usando DMS

Pode utilizar o Serviço de Migração de Bases de Dados Azure para migrar bases de dados de uma instância RDS MySQL para a Base de [Dados Azure para mySQL](https://docs.microsoft.com/azure/mysql/) enquanto a base de dados de origem permanece online durante a migração. Por outras palavras, a migração pode ser alcançada com o mínimo de tempo de paragem para a aplicação. Neste tutorial, migra a base de dados de amostras **dos Colaboradores** de uma instância de RDS MySQL para a Base de Dados Azure para mySQL utilizando a atividade de migração online no Serviço de Migração de Bases de Dados Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Migrar o esquema da amostra usando os serviços de misqldump e mysql.
> * Crie uma instância do Azure Database Migration Service.
> * Crie um projeto de migração utilizando o Serviço de Migração de Bases de Dados Azure.
> * Executar a migração.
> * Monitorizar a migração.

> [!NOTE]
> A utilização do Serviço de Migração de Bases de Dados Azure para realizar uma migração online requer a criação de uma instância baseada no nível de preços Premium. Para mais informações, consulte a página de preços do Serviço de [Migração](https://azure.microsoft.com/pricing/details/database-migration/) da Base de Dados Azure.

> [!IMPORTANT]
> Para uma experiência de migração otimizada, a Microsoft recomenda a criação de uma instância do Azure Database Migration Service na mesma região do Azure que a base de dados de destino. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve como realizar uma migração on-line de um caso de RDS MySQL para Azure Database para MySQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Certifique-se de que a fonte do servidor MySQL está a executar uma edição comunitária MySQL suportada. Para determinar a versão da sua instância MySQL, na utilidade mysql ou na bancada MySQL, execute o comando:

    ```
    SELECT @@version;
    ```

    Para mais informações, consulte o artigo Suportado Base de [Dados Azure para versões MySQL](https://docs.microsoft.com/azure/mysql/concepts-supported-versions).

* Descarregue e instale a base de dados de [amostras mySQL **Employees** ](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* Crie uma instância de Base de [Dados Azure para MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).
* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-a-site aos seus servidores de origem no local, utilizando [expressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação](https://docs.microsoft.com/azure/virtual-network/)da Rede Virtual , e especialmente os artigos quickstart com detalhes passo a passo.
* Certifique-se de que as regras do Grupo de Segurança da Rede Virtual não bloqueiam as seguintes portas de comunicação de entrada para o Serviço de Migração de Bases de Dados Azure: 443, 53, 9354, 445 e 12000. Para mais detalhes sobre a filtragem de tráfego da rede virtual NSG, consulte o artigo Filtrar o tráfego da [rede com grupos](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)de segurança da rede .
* Configure o seu [Firewall do Windows](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) (ou a sua firewall Linux) para permitir o acesso ao motor de base de dados. Para o servidor MySQL, permita a porta 3306 para conectividade.

> [!NOTE]
> A Base de Dados Azure para MySQL apenas suporta tabelas InnoDB. Para converter as tabelas MyISAM para InnoDB, consulte o artigo [Converter Tabelas do MyISAM para InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) .

### <a name="set-up-aws-rds-mysql-for-replication"></a>Configurar AWS RDS MySQL para replicação

1. Para criar um novo grupo de parâmetros, siga as instruções fornecidas pela AWS no artigo [MySQL Database Log Files,](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)na secção **Formato de Registo Binário.**
2. Criar um novo grupo de parâmetros com a seguinte configuração:
    * binlog_format = row
    * binlog_checksum = NENHUM
3. Salve mato novo grupo de parâmetros.
4. Associe o novo grupo de parâmetros com a instância RDS MySQL. Pode ser necessário reiniciar.

## <a name="migrate-the-schema"></a>Migrar o esquema

1. Extrair o esquema da base de dados de origem e aplicar-se à base de dados-alvo para completar a migração de todos os objetos de base de dados, tais como squemas de mesa, índices e procedimentos armazenados.

    A maneira mais fácil de migrar apenas o esquema é usar o mysqldump com o parâmetro sem dados. O comando para migrar o esquema é:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Por exemplo, para despejar um ficheiro schema para a base de dados **dos Empregados,** utilize o seguinte comando:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Importar o esquema para o serviço alvo, que é a Base de Dados Azure para o MySQL. Para restaurar o ficheiro de despejo de esquemas, execute o seguinte comando:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Por exemplo, importar o esquema para a base de dados **dos Empregados:**

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Se tiver chaves externas no seu esquema, o carregamento inicial e a sincronização contínua da migração irão falhar. Para extrair o script de chave estrangeira drop e adicionar script chave estrangeira no destino (Base de Dados Azure para MySQL), executar o seguinte script na bancada de trabalho MySQL:

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

4. Executar a chave estrangeira drop (que é a segunda coluna) no resultado da consulta para deixar cair a chave estrangeira.

5. Se tiver gatilhos (inserção ou gatilho de atualização) nos dados, irá impor a integridade dos dados no alvo antes de replicar dados a partir da fonte. A recomendação é desativar os gatilhos em todas as tabelas *do alvo* durante a migração e, em seguida, ativar os gatilhos após a migração estar completa.

    Para desativar os gatilhos na base de dados do alvo:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Se houver casos do tipo de dados ENUM em quaisquer tabelas, recomendamos a atualização temporária do tipo de dados "variável de caracteres" na tabela-alvo. A replicação de dados wHen está completa e, em seguida, reverter o tipo de dados para ENUM.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores**de Recursos .

    ![Mostrar fornecedores de recursos](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do Serviço de Migração de Bases de Dados Azure

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização em que pretende criar a instância do Serviço de Migração de Bases de Dados Azure.

5. Selecione uma rede virtual existente ou crie uma nova.

    A rede virtual fornece ao Azure Database Migration Service acesso à fonte a instância MySQL e à base de dados azure-alvo para a instância MySQL.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure.](https://aka.ms/DMSVnet)

6. Selecione um nível de preços; para esta migração online, certifique-se de selecionar o nível de preços Premium: 4vCores.

    ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

      ![Localizar todas as instâncias do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. No ecrã **Azure Database Migration Services**, procure o nome da instância do Azure Database Migration Service que criou e selecione-a.

     ![Localizar a instância do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Selecione + **Novo Projeto de Migração**.
4. No ecrã do **novo projeto de migração,** especifique um nome para o projeto, na caixa de texto **tipo servidor Fonte,** selecione **MySQL**, e depois na caixa de texto **do tipo servidor Target,** selecione **AzureDbForMySQL**.
5. Na secção **Escolha tipo de atividade,** selecione **migração de dados online**.

    > [!IMPORTANT]
    > Certifique-se de selecionar a **migração de dados online;** as migrações offline não são suportadas para este cenário.

    ![Criar Projeto do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Alternadamente, pode escolher o **projeto Criar apenas** para criar o projeto de migração agora e executar a migração mais tarde.

6. Selecione **Guardar**.

7. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

    > [!NOTE]
    > Por favor, tome nota dos pré-requisitos necessários para a criação de migração on-line na lâmina de criação do projeto.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

* No ecrã de detalhes da **fonte da migração,** especifique os detalhes de ligação para a instância MySQL de origem.

   ![Detalhes da origem](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Selecione **Guardar**, e depois no ecrã de dados do **Target,** especificar os detalhes de ligação para o target Azure Database para o servidor MySQL, que é pré-provisionado e tem o esquema **dos Empregados** implementado usando o MySQLDump.

    ![Selecionar o Destino](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se a base de dados-alvo contiver o mesmo nome de base de dados que a base de dados de origem, o Serviço de Migração da Base de Dados Azure seleciona a base de dados de destino por padrão.

    ![Mapear para as bases de dados de destino](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e, em seguida, reveja o resumo para garantir que os detalhes de origem e de destino correspondem aos que foram anteriormente especificados.

    ![Resumo da migração](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

    É apresentada a janela de atividade da migração e o **Estado** da atividade é **A inicializar**.

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Em execução**.

    ![Estado de Atividade - execução](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. Em NOME DE BASE DE **DADOS,** selecione uma base de dados específica para chegar ao estado de migração para **operações completas** de carga de dados e **sincronização de dados incrementais.**

    **A carga completa de dados** mostra o estado inicial de migração da carga, enquanto a **sincronização** de dados incremental mostra o estado de captura de dados de alteração (CDC).

    ![Tela de inventário - carga completa de dados](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Tela de inventário - sincronização incremental de dados](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Realizar transferência de migração

Após a carga completa inicial ser concluída, as bases de dados estão marcadas **Pronta seleção pronta a cortar**.

1. Quando estiver pronto para concluir a migração da base de dados, selecione **Iniciar Transferência**.

    ![Começar a cortar](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Certifique-se de que para todas as transações de entrada para a base de dados de origem; aguarde até que o contador **Alterações pendentes** mostre **0**.
3. Selecione **Confirmar** e depois **Aplicar**.
4. Quando o estado de migração da base de dados mostrar **concluído,** ligue as suas aplicações à nova base de dados azure-alvo para base de dados MySQL.

A sua migração online de uma instância no local do MySQL para a Base de Dados Azure para MySQL está agora completa.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre o Azure Database Migration Service, leia o artigo [O que é o Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Para obter informações sobre a Base de Dados Azure para mySQL, consulte o artigo O que é a Base de [Dados Azure para mySQL?](https://docs.microsoft.com/azure/mysql/overview)
* Para outras questões, envie um e-mail para o pseudónimo [Ask Azure Database Migrations.](mailto:AskAzureDatabaseMigrations@service.microsoft.com)
