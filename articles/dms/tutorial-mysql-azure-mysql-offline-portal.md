---
title: 'Tutorial: Migrar o MySQL para a base de dados Azure para o MySQL offline usando DMS'
titleSuffix: Azure Database Migration Service
description: Aprenda a realizar uma migração offline do MySQL no local para a Azure Database for MySQL utilizando o Azure Database Migration Service.
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: 71363771359ffef0ff165bd4a6744d864ea1856c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819729"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-offline-using-dms"></a>Tutorial: Migrar o MySQL para a base de dados Azure para o MySQL offline usando DMS

Pode utilizar o Azure Database Migration Service para realizar uma migração de bases de dados completa no local, a minha instância MySQL para [Azure Database for MySQL](../mysql/index.yml) com capacidade de migração de dados de alta velocidade. Neste tutorial, migraremos uma base de dados de amostras de um caso no local do MySQL 5.7 para Azure Database for MySQL (v5.7) utilizando uma atividade de migração offline no Azure Database Migration Service. Embora os artigos assumam a origem como uma instância de base de dados MySQL e alvo para ser Azure Database para o MySQL, pode ser usado para migrar de uma Base de Dados Azure para MySQL apenas alterando o nome e credenciais do servidor de origem. Além disso, a migração de servidores MySQL de versão inferior (v5.6 e acima) para versões mais altas também é suportada.

> [!IMPORTANT]
> Para migrações on-line, você pode usar esta nova oferta juntamente com [a replicação de dados.](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) Em alternativa, utilize ferramentas de código aberto como [MyDumper/MyLoader](https://centminmod.com/mydumper.html) com replicação de dados para migrações online. 

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Migrar esquema de base de dados usando o utilitário mysqldump.
> * Crie uma instância do Azure Database Migration Service.
> * Crie um projeto de migração utilizando o Serviço de Migração da Base de Dados Azure.
> * Executar a migração.
> * Monitorizar a migração.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Tenha uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free)
* Tenha uma base de dados MySQL no local com a versão 5.7. Caso contrário, faça o download e instale a edição comunitária 5.7 [do MySQL.](https://dev.mysql.com/downloads/mysql/)
* [Criar uma instância na Base de Dados do Azure para MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Consulte o artigo Use a [bancada de trabalho MySQL para ligar e consultar dados](../mysql/connect-workbench.md) para obter detalhes sobre como conectar e criar uma base de dados utilizando a aplicação Workbench. A base de dados Azure para a versão MySQL deve ser igual ou superior à versão MySQL no local . Por exemplo, o MySQL 5.7 pode migrar para a Base de Dados Azure para o MySQL 5.7 ou atualizado para 8. 
* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-to-site aos servidores de origem no local, utilizando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou [o VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação da Rede Virtual,](../virtual-network/index.yml)e especialmente os artigos quickstart com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração virtual da redeNet, se utilizar o ExpressRoute com o olhar de rede para a Microsoft, adicione os [seguintes pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço à sub-rede em que o serviço será abastecado:
    >
    > * Ponto final da base de dados-alvo (por exemplo, ponto final SQL, ponto final cosmos DB, e assim por diante)
    > * Ponto final de armazenamento
    > * Ponto final do ônibus de serviço
    >
    > Esta configuração é necessária porque o Serviço de Migração da Base de Dados Azure carece de conectividade com a Internet.

* Certifique-se de que as regras do Grupo de Segurança da Rede de Rede Virtual não bloqueiam a porta de saída 443 do ServiceTag para ServiceBus, Storage e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego NSG da rede virtual, consulte o artigo Filtrar o [tráfego da rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Abra a firewall do Windows para permitir que as ligações da Rede Virtual para o Serviço de Migração da Base de Dados Azure acedam à fonte MySQL Server, que por padrão é a porta TCP 3306.
* Ao utilizar um aparelho de firewall em frente à sua base de dados de origem, poderá ter de adicionar regras de firewall para permitir que as ligações da Rede Virtual para o Serviço de Migração da Base de Dados de Azure acedam à base de dados de origem para migração.
* Crie uma regra de [firewall](../azure-sql/database/firewall-configure.md) ao nível do servidor ou [configuure pontos finais de serviço VNET](../mysql/howto-manage-vnet-using-portal.md) para a base de dados target Azure para o MySQL para permitir o acesso da Rede Virtual para o Serviço de Migração de Bases de Dados Azure às bases de dados-alvo.
* O MySQL de origem tem de estar na edição de comunidade do MySQL suportada. Para determinar a versão da instância do MySQL, no utilitário MySQL ou no MySQL Workbench, execute o seguinte comando:

    ```
    SELECT @@version;
    ```

* A Base de Dados do Azure para MySQL suporta apenas tabelas InnoDB. Para converter tabelas MyISAM para InnoDB, veja o artigo [Converting Tables from MyISAM to InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) (Converter Tabelas de MyISAM para InnoDB)
* O utilizador deve ter o privilégio de ler dados na base de dados de origem.

## <a name="migrate-database-schema"></a>Migrar esquema de base de dados

Para transferir todos os objetos da base de dados, como esquemas de mesa, índices e procedimentos armazenados, precisamos extrair esquemas da base de dados de origem e aplicar na base de dados-alvo. Para extrair o esquema, pode utilizar mysqldump com o parâmetro `--no-data`. Para isso, precisa de uma máquina que possa ligar-se tanto à base de dados MySQL de origem como à base de dados Azure target para o MySQL.

Para exportar o esquema utilizando o mysqldump, executar o seguinte comando:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Por exemplo:

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

Para importar esquemas para direcionar a Base de Dados Azure para o MySQL, executar o seguinte comando:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Por exemplo:

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

Se tiver chaves estrangeiras no seu esquema, a carga de dados paralela durante a migração será tratada pela tarefa de migração. Não há necessidade de largar chaves estrangeiras durante a migração do esquema.

Se tiver gatilhos na base de dados, irá impor a integridade dos dados no alvo antes da migração completa de dados da fonte. A recomendação é desativar os gatilhos em todas as tabelas do alvo durante a migração e, em seguida, ativar os gatilhos após a migração.

Execute o seguinte script na bancada mySQL workbench na base de dados alvo para extrair o script do gatilho de gota e adicionar script de gatilho.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

Executar a consulta gerada do gatilho de queda (coluna DropQuery) no resultado para lançar os gatilhos na base de dados alvo. A consulta de adição do gatilho pode ser guardada, para ser usada após a conclusão da migração de dados.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

O registo do fornecedor de recursos tem de ser feito apenas uma vez em cada subscrição da Azure. Sem o registo, não poderá criar uma instância do Serviço de **Migração da Base de Dados Azure.**

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-mysql-to-azure-mysql-offline-portal/01-dms-portal-select-subscription.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores de Recursos**.

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-mysql-to-azure-mysql-offline-portal/02-dms-portal-register-rp.png)

## <a name="create-a-database-migration-service-instance"></a>Criar uma instância do Serviço de Migração de Bases de Dados

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-offline-portal/03-dms-portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-offline-portal/04-dms-portal-marketplace-create.png)
  
3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione um nível de preços e mude para o ecrã de rede. A capacidade de migração offline está disponível tanto no nível de preços Standard como Premium.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Configurar configurações básicas do Serviço de Migração da Base de Dados Azure](media/tutorial-mysql-to-azure-mysql-offline-portal/05-dms-portal-create-basic.png)

5. Selecione uma rede virtual existente da lista ou forneça o nome da nova rede virtual a ser criada. Mova-se para a revisão + crie ecrã. Opcionalmente, pode adicionar tags ao serviço utilizando o ecrã das etiquetas.

    A rede virtual fornece ao Azure Database Migration Service acesso ao servidor SQL de origem e à instância de base de dados Azure SQL.

    ![Configurar configurar as definições da rede de serviços de migração da base de dados da Azure](media/tutorial-mysql-to-azure-mysql-offline-portal/06-dms-portal-create-networking.png)

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure](../virtual-network/quick-create-portal.md).

6. Reveja as configurações e **selecione Criar** para criar o serviço.
    
    ![Azure Database Migration Service criar](media/tutorial-mysql-to-azure-mysql-offline-portal/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.  

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

    ![Localizar todas as instâncias do Serviço de Migração da Base de Dados de Azure](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. Selecione a sua instância de serviço de migração a partir dos resultados da pesquisa e selecione + **Novo Projeto de Migração.**
    
    ![Criar um novo projeto de migração](media/tutorial-mysql-to-azure-mysql-offline-portal/08-02-dms-portal-new-project.png)

3. No novo ecrã do **projeto de migração,** especifique um nome para o projeto, na caixa de seleção **do tipo de servidor Fonte,** selecione **MySQL**, na caixa de seleção **do tipo de servidor Target,** selecione **Azure Database For MySQL** e na caixa de seleção **do tipo de atividade de migração,** selecione **\[ pré-visualização \] da migração de dados**. Selecione **Criar e executar atividade**.

    ![Criar Projeto do Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-offline-portal/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > Em alternativa, pode escolher **o projeto Criar apenas** para criar o projeto de migração agora e executar a migração mais tarde.

## <a name="configure-migration-project"></a>Projeto de migração de configuração

1. No ecrã **de origem Select,** especifique os detalhes da ligação para a origem da instância MySQL e selecione **Seguinte : Selecione o>>**

    ![Adicionar ecrã de detalhes de origem](media/tutorial-mysql-to-azure-mysql-offline-portal/10-dms-portal-project-mysql-source.png)

2. No ecrã **alvo Select,** especifique os detalhes da ligação para a base de dados Azure alvo para a instância MySQL e selecione **Seguinte : Selecione bases de dados>>**

    ![Adicionar ecrã de detalhes do alvo](media/tutorial-mysql-to-azure-mysql-offline-portal/11-dms-portal-project-mysql-target.png)

3. No ecrã de **bases de dados Select,** mapear a fonte e a base de dados-alvo para migração e selecionar **Seguinte : Configurar as definições de migração>>**. Pode selecionar a opção **Make Source Server Readonly** para tornar a fonte apenas para leitura, mas tenha cuidado com a definição do nível do servidor. Se selecionado, define todo o servidor apenas para leitura, e não apenas para as bases de dados selecionadas.
    
    Se a base de dados-alvo contiver o mesmo nome de base de dados que a base de dados de origem, o Azure Database Migration Service seleciona a base de dados-alvo por predefinição.
    ![Selecione o ecrã de detalhes da base de dados](media/tutorial-mysql-to-azure-mysql-offline-portal/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
    > Embora possa selecionar várias bases de dados neste passo, mas existem limites para quantos e quão rápido os DBs podem ser migrados desta forma, uma vez que cada base de dados partilhará o cálculo. Com a configuração padrão do SKU Premium, cada tarefa de migração tentará migrar duas tabelas em paralelo. Estas tabelas podem ser de qualquer uma das bases de dados selecionadas. Se isto não for rápido o suficiente, você pode dividir as atividades de migração de bases de dados em diferentes tarefas de migração e escalar em vários serviços. Além disso, existe um limite de 10 casos de Serviço de Migração de Bases de Dados Azure por subscrição por região.
    > Para obter um maior controlo granular sobre a produção e paralelização da migração, consulte o artigo [PowerShell: Executar migração offline da base de dados MySQL para Azure Database for MySQL usando DMS](./migrate-mysql-to-azure-mysql-powershell.md)

4. No ecrã de definições de **migração configurar,** selecione as tabelas para fazer parte da migração e selecione **Seguinte : Resumo>>**. Se as tabelas-alvo tiverem quaisquer dados, não são selecionadas por padrão, mas pode selecioná-los explicitamente e serão truncados antes de iniciar a migração.

    ![Selecione tela de tabelas](media/tutorial-mysql-to-azure-mysql-offline-portal/13-dms-portal-project-mysql-select-tbl.png)

5. No ecrã **Resumo,** na caixa de texto **'Nome de Atividade',** especifique um nome para a atividade de migração e reveja o resumo para garantir que os detalhes da origem e do alvo correspondem ao que especificou anteriormente.

    ![Resumo do projeto de migração](media/tutorial-mysql-to-azure-mysql-offline-portal/14-dms-portal-project-mysql-activity-summary.png)

6. Selecione **Migração iniciar**. É apresentada a janela de atividade da migração e o **Estado** da atividade é **A inicializar**. O **Estado** muda para **Correr** quando as migrações de mesa começam.
 
     ![Migração em execução](media/tutorial-mysql-to-azure-mysql-offline-portal/15-dms-portal-project-mysql-running.png)

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividades de migração, selecione **Refresh** para atualizar o ecrã e ver o progresso sobre o número de tabelas concluídas. 

2. Pode clicar no nome da base de dados no ecrã de atividade para ver o estado de cada tabela à medida que estão a ser migrados. Selecione **Refresh** para atualizar o visor. 

     ![Monitorização da migração](media/tutorial-mysql-to-azure-mysql-offline-portal/16-dms-portal-project-mysql-monitor.png)

## <a name="complete-the-migration"></a>Complete a migração

1. No ecrã de atividade de migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Concluída**.

    ![Concluir a migração](media/tutorial-mysql-to-azure-mysql-offline-portal/17-dms-portal-project-mysql-complete.png)

## <a name="post-migration-activities"></a>Atividades pós-migração

O corte migratório numa migração offline é um processo dependente de aplicação que está fora de alcance para este documento, mas são prescritas atividades pós-migração:

1. Crie logins, funções e permissões de acordo com os requisitos de aplicação.
2. Recrie todos os gatilhos na base de dados-alvo tal como extraídos durante o passo de pré-migração.
3. Realizar testes de sanidade da aplicação com base de dados-alvo para certificar a migração. 

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar o Serviço de Migração da Base de Dados, pode eliminar o serviço com os seguintes passos:

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

    ![Localizar todas as instâncias de DMS](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. Selecione a sua instância de serviço de migração a partir dos resultados da pesquisa e selecione **Eliminar o Serviço**.
    
    ![Apagar o serviço de migração](media/tutorial-mysql-to-azure-mysql-offline-portal/18-dms-portal-delete.png)

3. No diálogo de confirmação, digite o nome do serviço no TIPO A caixa de texto **DO SERVIÇO DE MIGRAÇÃO DE BASE DE DADOS** e selecione **Eliminar**

    ![Confirmar eliminação do serviço de migração](media/tutorial-mysql-to-azure-mysql-offline-portal/19-dms-portal-deleteconfirm.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre questões e limitações conhecidas ao realizar migrações utilizando DMS, consulte o artigo [Questões comuns - Azure Database Migration Service](./known-issues-troubleshooting-dms.md).
* Para resolver problemas de conectividade de base de dados de origem durante a utilização de DMS, consulte o artigo [Problemas que ligam bases de dados de origem](./known-issues-troubleshooting-dms-source-connectivity.md).
* Para obter informações sobre o Serviço de Migração da Base de Dados Azure, consulte o artigo [O que é o Serviço de Migração da Base de Dados Azure?](./dms-overview.md)
* Para obter informações sobre a Base de Dados Azure para o MySQL, consulte o artigo [O que é a Base de Dados Azure para o MySQL?](../mysql/overview.md)
* Para obter orientações sobre a utilização de DMS via PowerShell, consulte o artigo [PowerShell: Executar migração offline da base de dados MySQL para Azure Database for MySQL usando DMS](./migrate-mysql-to-azure-mysql-powershell.md)