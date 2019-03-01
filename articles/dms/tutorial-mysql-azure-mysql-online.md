---
title: 'Tutorial: Utilizar o Azure Database Migration Service para realizar uma migração online do MySQL para a Base de Dados do Azure para MySQL | Microsoft Docs'
description: Aprenda a realizar uma migração do MySQL no local para a Base de Dados do Azure para MySQL com o Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: scphang
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: c41258501b476d1edaf483e08874d2fc83bd00c9
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991551"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Tutorial: Migrar o MySQL para a Base de Dados do Azure para MySQL online com o DMS
Pode utilizar o Azure Database Migration Service para migrar as bases de dados de uma instância do MySQL no local para a [Base de Dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/) com um período de indisponibilidade mínimo. Por outras palavras, a migração pode ser feita com um período de indisponibilidade mínimo para a aplicação. Neste tutorial, vai migrar a base de dados de exemplo **Colaboradores** de uma instância no local do MySQL 5.7 para a Base de Dados do Azure para MySQL através de uma atividade de migração online no Azure Database Migration Service.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Migre o esquema de exemplo com o utilitário mysqldump.
> * Criar uma instância do Azure Database Migration Service.
> * Utilizar o Azure Database Migration Service para criar um projeto de migração.
> * Executar a migração.
> * Monitorizar a migração.

> [!NOTE]
> Utilizar o serviço de migração de base de dados do Azure para efetuar uma migração online requer a criação de uma instância com base no escalão de preço Premium.

> [!IMPORTANT]
> Para uma experiência de migração otimizada, a Microsoft recomenda a criação de uma instância do Azure Database Migration Service na mesma região do Azure que a base de dados de destino. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa de:

- Transferir e instalar a [edição de comunidade do MySQL](https://dev.mysql.com/downloads/mysql/) 5.6 ou 5.7. A versão do MySQL no local tem de corresponder à versão da Base de Dados do Azure para MySQL. Por exemplo, o MySQL 5.6 só pode ser migrado para a Base de Dados do Azure para MySQL 5.6 e não pode ser atualizado para a versão 5.7.
- [Criar uma instância na Base de Dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal). Veja o artigo [Utilizar o MySQL Workbench para se ligar e consultar dados](https://docs.microsoft.com/azure/mysql/connect-workbench) para obter detalhes sobre como ligar e criar uma base de dados com o portal do Azure.  
- Criar uma rede Virtual do Azure (VNET) para o serviço de migração de base de dados do Azure com o modelo de implementação Azure Resource Manager, que garante uma conectividade site a site aos seus servidores de origem no local, utilizando um [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

    > [!NOTE]
    > Durante a configuração VNET, se utilizar o ExpressRoute com peering de rede para a Microsoft, adicione o seguinte serviço [pontos de extremidade](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) à sub-rede na qual o serviço será aprovisionado:
    > - Ponto de extremidade de destino da base de dados (por exemplo, ponto de extremidade do SQL, ponto final do Cosmos DB etc.)
    > - Ponto final de armazenamento
    > - Ponto final de barramento de serviço
    >
    > Esta configuração é necessária porque o serviço de migração de base de dados do Azure não tem conectividade à internet.
 
- Certifique-se de que as regras do grupo de segurança de rede de VNET não bloqueiam as seguintes portas de comunicação 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego dos NSGs das VNETs do Azure, veja o artigo [Filter network traffic with network security groups](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) (Filtrar tráfego de rede com grupos de segurança de rede).
- Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abrir a firewall do Windows para permitir que o Azure Database Migration Service aceda ao Servidor MySQL de origem, que, por predefinição, é a porta TCP 3306.
- Se estiver a utilizar uma aplicação de firewall à frente da base ou bases de dados, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base ou bases de dados de origem para migração.
- Criar uma [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) ao nível do servidor para a Base de Dados do Azure para MySQL para permitir que o Azure Database Migration Service aceda às bases de dados de destino. Fornecer o intervalo de sub-redes da VNET utilizada no Azure Database Migration Service.
- O MySQL de origem tem de estar na edição de comunidade do MySQL suportada. Para determinar a versão da instância do MySQL, no utilitário MySQL ou no MySQL Workbench, execute o seguinte comando:
    ```
    SELECT @@version;
    ```
- A Base de Dados do Azure para MySQL suporta apenas tabelas InnoDB. Para converter tabelas MyISAM para InnoDB, veja o artigo [Converting Tables from MyISAM to InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) (Converter Tabelas de MyISAM para InnoDB) 

- Ativar o registo binário no ficheiro my.ini (Windows) ou my.cnf (Unix) na base de dados de origem, utilizando a seguinte configuração:

    - **server_id** = 1 ou superior (relevante apenas para o MySQL 5.6)
    - **log-bin**  = <path> (relevante apenas para o MySQL 5.6)

        Por exemplo: log-bin = E:\MySQL_logs\BinLog
    - **binlog_format** = row
    - **Expire_logs_days** = 5 (é recomendado que não utilize zero; relevante apenas para o MySQL 5.6)
    - **Binlog_row_image** = full (relevante apenas para o MySQL 5.6)
    - **log_slave_updates** = 1
 
- O utilizador tem de ter a função ReplicationAdmin com os seguintes privilégios:
    - **CLIENTE DE REPLICAÇÃO** - necessário apenas para tarefas de Processamento de Alterações. Por outras palavras, as tarefas de apenas Carregamento completo não precisam deste privilégio.
    - **RÉPLICA DE REPLICAÇÃO** - necessário apenas para tarefas de Processamento de Alterações. Por outras palavras, as tarefas de apenas Carregamento completo não precisam deste privilégio.
    - **SUPER** - necessário apenas em versões anteriores ao MySQL 5.6.6.

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo
Para concluir todos os objetos de base de dados, como esquemas de tabela, índices e procedimentos armazenados, é necessário extrair o esquema da base de dados de origem e aplicar à base de dados. Para extrair o esquema, pode utilizar mysqldump com o parâmetro `--no-data`.
 
Partindo do princípio de que tem a base de dados de exemplo de colaboradores de MySQL no sistema local, o comando para executar a migração do esquema com mysqldump é:
```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```
Por exemplo:
```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

Para importar o esquema para a Base de Dados do Azure para MySQL de destino, execute o seguinte comando:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Por exemplo:
```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

Se tiver chaves externas no seu esquema, o carregamento inicial e a sincronização contínua da migração irão falhar.  Execute o seguinte script no MySQL Workbench para extrair o script de remoção de chave externa e o script de adição de chave externa.
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
        
Execute o script de remoção de chave externa (que é a segunda coluna) no resultado da consulta para remover a chave externa.

Se tiver o acionador nos dados (acionador de inserção ou adição), irá impor a integridade dos dados no destino antes dos dados replicados da origem. A recomendação é desativar os acionadores em todas as tabelas no destino durante a migração e, em seguida, ativar os acionadores após a conclusão da migração.

Para desativar os acionadores na base de dados de destino, utilize o seguinte comando:
```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```
## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration
1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.
 
   ![Mostrar subscrições no portal](media/tutorial-mysql-to-azure-mysql-online/portal-select-subscriptions.png)
       
2. Selecione a subscrição na qual pretende criar a instância do Database Migration Service do Azure e, em seguida, selecione **Fornecedores de recursos**.
 
    ![Mostrar fornecedores de recursos](media/tutorial-mysql-to-azure-mysql-online/portal-select-resource-provider.png)
    
3.  Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.
 
    ![Registar o fornecedor de recursos](media/tutorial-mysql-to-azure-mysql-online/portal-register-resource-provider.png)    

## <a name="create-a-dms-instance"></a>Criar uma instância do DMS
1.  No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/portal-marketplace.png)

2.  No ecrã **Azure Database Migration Service**, selecione **Criar**.
 
    ![Criar instância do Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-create1.png)
  
3.  No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione uma rede virtual (VNET) já existente ou crie uma nova.

    A VNET fornece ao Azure Database Migration Service acesso à instância do SQL Server de origem e à instância da Base de Dados SQL do Azure de destino.

    Para obter mais informações sobre como criar uma VNET no portal do Azure, veja o artigo [Criar uma rede virtual com o portal do Azure](https://aka.ms/DMSVnet).

5. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    Se precisar de ajuda para escolher o escalão certo do serviço de migração de base de dados do Azure, consulte as recomendações apresentadas na postagem de blog [escolher um escalão de serviço de migração de base de dados do Azure (Azure DMS)](https://go.microsoft.com/fwlink/?linkid=861067). 

     ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-settings3.png)

7.  Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração
Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.
 
      ![Localizar todas as instâncias do Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-search.png)

2. No ecrã **Azure Database Migration Services**, procure o nome da instância do Azure Database Migration Service que criou e selecione-a.
 
     ![Localizar a instância do Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-instance-search.png)
 
3. Selecione + **Novo Projeto de Migração**.
4. No ecrã **Novo projeto de migração**, indique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **MySQL**, na caixa de texto **Tipo de servidor de destino**, selecione **AzureDbForMySQL**.
5. Na secção **Escolher tipo de atividade**, selecione **Migração de dados online**

    ![Criar Projeto do Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-create-project4.png)

    > [!NOTE]
    > Em alternativa, pode escolher **Apenas criar projeto** para criar o projeto de migração agora e executar a migração mais tarde.

6. Selecione **Guardar**, tenha em atenção os requisitos para utilizar com êxito o DMS para migrar dados e, em seguida, selecione **Criar e executar atividade**.

## <a name="specify-source-details"></a>Especificar os detalhes da origem
1. No ecrã **Adicionar Detalhes da Origem**, especifique os detalhes da ligação para a instância do MySQL de origem.
 
    ![Ecrã Adicionar Detalhes da Origem](media/tutorial-mysql-to-azure-mysql-online/dms-add-source-details.png)   

## <a name="specify-target-details"></a>Especificar os detalhes do destino
1. Selecione **Guardar** e, no ecrã **Detalhes do destino**, especifique os detalhes de ligação do servidor da Base de Dados do Azure para MySQL de destino, que é a instância pré-aprovisionada da Base de Dados do Azure para MySQL na qual o esquema de **Colaboradores** foi implementado através do mysqldump.

    ![Ecrã Detalhes do destino](media/tutorial-mysql-to-azure-mysql-online/dms-add-target-details.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se o nome da base de dados de destino for o mesmo da de origem, o Azure Database Migration Service seleciona a base de dados de destino por predefinição.

    ![Mapear para as bases de dados de destino](media/tutorial-mysql-to-azure-mysql-online/dms-map-target-details.png)

3.  Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e, em seguida, reveja o resumo para garantir que os detalhes de origem e de destino correspondem aos que foram anteriormente especificados.

    ![Resumo da migração](media/tutorial-mysql-to-azure-mysql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Executar a migração
- Selecione **Executar a migração**.

    É apresentada a janela da atividade de migração e o **Estado** da atividade é **a inicializar**.

## <a name="monitor-the-migration"></a>Monitorizar a migração
1. No ecrã de atividade de migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Concluída**.

     ![Estado da Atividade - concluída](media/tutorial-mysql-to-azure-mysql-online/dms-activity-completed.png)

2. Em **Nome da Base de Dados**, selecione uma base de dados específica para obter o estado da migração para as operações **Carregamento de dados completo** e **Sincronização de dados incremental**.

    O Carregamento de dados completo irá mostrar o estado de migração de carregamento inicial, enquanto a Sincronização de dados incremental irá mostrar o estado de captura de dados alterados (CDC).
   
     ![Estado da Atividade - Carregamento completo concluído](media/tutorial-mysql-to-azure-mysql-online/dms-activity-full-load-completed.png)

     ![Estado da Atividade - Sincronização de dados incremental](media/tutorial-mysql-to-azure-mysql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Realizar transferência de migração
Depois de concluída a Carga completa inicial, as bases de dados são marcadas como **Preparadas para transferência**.

1. Quando estiver pronto para concluir a migração da base de dados, selecione **Iniciar Transferência**.

    ![Iniciar transferência](media/tutorial-mysql-to-azure-mysql-online/dms-start-cutover.png)
 
2.  Certifique-se de que para todas as transações de entrada para a base de dados de origem; aguarde até que o contador **Alterações pendentes** mostre **0**.
3.  Selecione **Confirmar** e depois **Aplicar**.
4. Quando o estado da migração de base de dados apresentar **Concluído**, ligue as suas aplicações à nova Base de Dados SQL do Azure de destino.
 
## <a name="next-steps"></a>Passos Seguintes
- Para obter informações sobre problemas conhecidos e limitações ao realizar migrações online para a Base de Dados do Azure para MySQL, veja o artigo [Problemas conhecidos e soluções alternativas com migrações online da Base de Dados do Azure para MySQL](known-issues-azure-mysql-online.md).
- Para obter informações sobre o Azure Database Migration Service, leia o artigo [O que é o Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
- Para obter informações sobre a Base de Dados do Azure para MySQL, veja o artigo [O que é Base de Dados do Azure para MySQL?](https://docs.microsoft.com/azure/mysql/overview).
