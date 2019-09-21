---
title: 'Tutorial: Usar o serviço de migração de banco de dados do Azure para uma migração online do RDS MySQL para o banco de dados do Azure para MySQL | Microsoft Docs'
description: Saiba como executar uma migração online do RDS MySQL para o banco de dados do Azure para MySQL usando o serviço de migração de banco de dados do Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 09/21/2019
ms.openlocfilehash: 9bd620ef9664e921aa88792017585b02e44387f8
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172701"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Tutorial: Migrar o RDS MySQL para o banco de dados do Azure para MySQL online usando DMS

Você pode usar o serviço de migração de banco de dados do Azure para migrar bancos de dados de uma instância do RDS MySQL para o [banco de dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/) enquanto o banco de dados de origem permanece online durante a migração Em outras palavras, a migração pode ser obtida com o mínimo de tempo de inatividade para o aplicativo. Neste tutorial, você migra o banco de dados de exemplo **Employees** de uma instância do RDS MySQL para o banco de dados do Azure para MySQL usando a atividade de migração online no serviço de migração de banco de dados do Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Migre o esquema de exemplo usando os utilitários mysqldump e MySQL.
> * Crie uma instância do serviço de migração de banco de dados do Azure.
> * Crie um projeto de migração usando o serviço de migração de banco de dados do Azure.
> * Executar a migração.
> * Monitorizar a migração.

> [!NOTE]
> Usar o serviço de migração de banco de dados do Azure para executar uma migração online requer a criação de uma instância com base no tipo de preço premium. Para obter mais informações, consulte a página [preços](https://azure.microsoft.com/pricing/details/database-migration/) do serviço de migração de banco de dados do Azure.

> [!IMPORTANT]
> Para uma experiência de migração otimizada, a Microsoft recomenda a criação de uma instância do Azure Database Migration Service na mesma região do Azure que a base de dados de destino. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve como executar uma migração online de uma instância do RDS MySQL para o banco de dados do Azure para MySQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Verifique se o servidor MySQL de origem está executando uma edição de comunidade MySQL com suporte. Para determinar a versão da instância do MySQL, no utilitário MySQL ou no MySQL Workbench, execute o comando:

    ```
    SELECT @@version;
    ```

    Para obter mais informações, consulte o artigo [suporte para versões do banco de dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/concepts-supported-versions).

* Baixe e instale o [banco de dados de exemplo de **funcionários** do MySQL](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* Crie uma instância do [banco de dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).
* Criar uma VNet (rede virtual) do Azure para o serviço de migração de banco de dados do Azure usando o modelo de implantação Azure Resource Manager, que fornece conectividade site a site para seus servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou a [VPN ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações sobre como criar uma VNet, consulte a [documentação da rede virtual](https://docs.microsoft.com/azure/virtual-network/)e especialmente os artigos de início rápido com detalhes passo a passo.
* Confirme que as regras de Grupos de Segurança de Rede da VNet não bloqueiam as seguintes portas de comunicação de entrada para o Azure Database Migration Service: 443, 53, 9354, 445 e 12000. Para obter mais detalhes sobre a filtragem de tráfego NSG VNet do Azure, consulte o artigo [filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Configure o [Firewall do Windows](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) (ou o Firewall do Linux) para permitir o acesso ao mecanismo de banco de dados. Para o servidor MySQL, permita a porta 3306 para conectividade.
* Abra o Firewall do Windows para permitir que o serviço de migração de banco de dados do Azure acesse o servidor MySQL de origem (a porta TCP padrão é 3306).
* Se estiver a utilizar uma aplicação de firewall à frente da base ou bases de dados, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base ou bases de dados de origem para migração.
* Crie uma [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) no nível de servidor para o servidor de banco de dados do Azure para MySQL a fim de permitir o acesso ao serviço de migração de banco de dados do Azure aos bancos dos dados. Forneça o intervalo de sub-rede da VNet usada para o serviço de migração de banco de dados do Azure.

> [!NOTE]
> O banco de dados do Azure para MySQL dá suporte apenas a tabelas InnoDB. Para converter tabelas MyISAM em InnoDB, consulte o artigo [convertendo tabelas de MyISAM para InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) .

### <a name="set-up-aws-rds-mysql-for-replication"></a>Configurar o AWS RDS MySQL para replicação

1. Para criar um novo grupo de parâmetros, siga as instruções fornecidas por AWS no artigo [arquivos de log do banco de dados MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html), na seção **formato de log binário** .
2. Crie um novo grupo de parâmetros com a seguinte configuração:
    * binlog_format = linha
    * binlog_checksum = NONE
3. Salve o novo grupo de parâmetros.

## <a name="migrate-the-schema"></a>Migrar o esquema

1. Extraia o esquema do banco de dados de origem e aplique-o ao banco de dados de destino para concluir a migração de todos os objetos de banco de dados, como esquemas de tabela, índices e procedimentos armazenados.

    A maneira mais fácil de migrar apenas o esquema é usar mysqldump com o parâmetro--no-data. O comando para migrar o esquema é:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Por exemplo, para despejar um arquivo de esquema para o banco de dados **Employees** , use o seguinte comando:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Importe o esquema para o serviço de destino, que é o banco de dados do Azure para MySQL. Para restaurar o arquivo de despejo de esquema, execute o seguinte comando:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Por exemplo, para importar o esquema para o banco de dados **Employees** :

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Se tiver chaves externas no seu esquema, o carregamento inicial e a sincronização contínua da migração irão falhar. Para extrair o script drop Foreign Key e adicionar o script Foreign Key no destino (banco de dados do Azure para MySQL), execute o seguinte script no MySQL Workbench:

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

4. Execute a chave de remoção estrangeira (que é a segunda coluna) no resultado da consulta para descartar a chave estrangeira.

5. Se você tiver gatilhos (inserir ou atualizar gatilho) nos dados, ele imporá a integridade dos dados no destino antes de replicar os dados da origem. A recomendação é desabilitar os gatilhos em todas as tabelas *no destino durante a* migração e, em seguida, habilitar os gatilhos após a conclusão da migração.

    Para desabilitar gatilhos no banco de dados de destino:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Se houver instâncias do tipo de dados ENUM em qualquer tabela, recomendamos que a atualização seja temporária para o DataType ' character varying ' na tabela de destino. Quando a replicação de dados for concluída, reverta o tipo de dados para ENUM.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Selecione a assinatura na qual você deseja criar a instância do serviço de migração de banco de dados do Azure e, em seguida, selecione **provedores de recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar fornecedor de recursos](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do serviço de migração de banco de dados do Azure

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione o local no qual você deseja criar a instância do serviço de migração de banco de dados do Azure.

5. Selecione uma VNet existente ou crie uma nova.

    A VNet fornece ao serviço de migração de banco de dados do Azure acesso à instância do MySQL de origem e à instância de destino do banco de dados do Azure para MySQL.

    Para obter mais informações sobre como criar uma VNet no portal do Azure, consulte o artigo [criar uma rede virtual usando o portal do Azure](https://aka.ms/DMSVnet).

6. Selecione um tipo de preço; para esta migração online, certifique-se de selecionar o prêmio Premium: tipo de preço 4vCores.

    ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

      ![Localizar todas as instâncias do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. No ecrã **Azure Database Migration Services**, procure o nome da instância do Azure Database Migration Service que criou e selecione-a.

     ![Localizar a instância do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Selecione + **Novo Projeto de Migração**.
4. Na tela **novo projeto de migração** , especifique um nome para o projeto, na caixa de texto **tipo de servidor de origem** , selecione **MySQL**e, em seguida, na caixa de texto tipo de **servidor de destino** , selecione **AzureDbForMySQL**.
5. Na seção **escolher tipo de atividade** , selecione **migração de dados online**.

    > [!IMPORTANT]
    > Certifique-se de selecionar **migração de dados online**; Não há suporte para migrações offline para este cenário.

    ![Criar Projeto do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Como alternativa, você pode escolher **criar projeto somente** para criar o projeto de migração agora e executar a migração mais tarde.

6. Selecione **Guardar**.

7. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

    > [!NOTE]
    > Anote os pré-requisitos necessários para configurar a migração online na folha de criação do projeto.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

* Na tela **detalhes da fonte de migração** , especifique os detalhes de conexão para a instância do MySQL de origem.

   ![Detalhes da origem](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Selecione **salvar**e, em seguida, na tela **detalhes de destino** , especifique os detalhes de conexão do banco de dados do Azure de destino para o servidor MySQL, que é previamente provisionado e tem o esquema **funcionários** implantado usando MySQLDump.

    ![Selecionar o Destino](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se o banco de dados de destino contém o mesmo nome do banco de dados de origem, o serviço de migração de banco de dados do Azure seleciona o banco de dados de destino por padrão

    ![Mapa para as bases de dados de destino](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e, em seguida, reveja o resumo para garantir que os detalhes de origem e de destino correspondem aos que foram anteriormente especificados.

    ![Resumo da migração](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

    É apresentada a janela de atividade da migração e o **Estado** da atividade é **A inicializar**.

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Em execução**.

    ![Status da atividade-em execução](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. Em **nome do banco**de dados, selecione um banco de dado específico para obter o status de migração para operações de sincronização de dados **incrementais** e **carregamento completo** .

    O **carregamento de dados completo** mostra o status de migração de carga inicial, enquanto a **sincronização de dados incremental** mostra o status da captura de dados de alteração (CDC).

    ![Tela de inventário-carregamento de dados completo](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Tela de inventário-sincronização de dados incremental](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Realizar transferência de migração

Após a conclusão da carga inicial completa, os bancos de dados são marcados **como prontos para a transferência**.

1. Quando estiver pronto para concluir a migração da base de dados, selecione **Iniciar Transferência**.

    ![Começar a cortar](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Certifique-se de que para todas as transações de entrada para a base de dados de origem; aguarde até que o contador **Alterações pendentes** mostre **0**.
3. Selecione **Confirmar** e depois **Aplicar**.
4. Quando o status de migração do banco de dados mostrar **concluído**, conecte seus aplicativos ao novo banco de dados de destino do banco de dados do Azure para MySQL.

A migração online de uma instância local do MySQL para o banco de dados do Azure para MySQL já está concluída.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre o Azure Database Migration Service, leia o artigo [O que é o Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Para obter informações sobre o banco de dados do Azure para MySQL, consulte o artigo [o que é o banco de dados do Azure para MySQL?](https://docs.microsoft.com/azure/mysql/overview).
* Para outras perguntas, envie um email para o alias [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) .
