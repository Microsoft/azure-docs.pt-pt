---
title: 'Tutorial: Utilizar o serviço de migração de base de dados do Azure para efetuar uma migração online do SQL Server para uma instância gerida da base de dados do Azure SQL | Documentos da Microsoft'
description: Aprenda a realizar uma migração online do SQL Server no local para uma instância gerida da base de dados do Azure SQL utilizando o serviço de migração de base de dados do Azure.
services: dms
author: HJToland3
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 790501aff42bec483862b735a0c5de7f8107ed46
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792315"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Tutorial: Migrar o SQL Server para uma instância gerida da base de dados do Azure SQL online com o DMS

Pode utilizar o serviço de migração de base de dados do Azure para migrar as bases de dados a partir de uma instância do SQL Server no local para um [instância gerida de base de dados do Azure SQL](../sql-database/sql-database-managed-instance.md) com período de indisponibilidade mínimo. Para métodos adicionais que podem exigir algum esforço manual, consulte o artigo [instância gerida de migração de instância do SQL Server para a base de dados do Azure SQL](../sql-database/sql-database-managed-instance-migrate.md).

Neste tutorial, migra os **Adventureworks2012** base de dados de uma instância no local do SQL Server para uma instância gerida da base de dados do Azure SQL com o período de indisponibilidade mínimo ao utilizar o serviço de migração de base de dados do Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Criar uma instância do Azure Database Migration Service.
> - Crie um projeto de migração e inicie a migração online com o Azure Database Migration Service.
> - Monitorizar a migração.
> - Transfira a migração quando estiver pronto.

> [!NOTE]
> Utilizar o serviço de migração de base de dados do Azure para efetuar uma migração online requer a criação de uma instância com base no escalão de preço Premium.
> [!IMPORTANT]
> Para uma experiência de migração otimizada, a Microsoft recomenda a criação de uma instância do Azure Database Migration Service na mesma região do Azure que a base de dados de destino. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração online do SQL Server para uma instância gerida da base de dados do Azure SQL. Para uma migração offline, consulte [instância gerida de migrar o SQL Server para uma base de dados do SQL do Azure com o DMS offline](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

- Criar uma rede Virtual do Azure (VNET) para o serviço de migração de base de dados do Azure com o modelo de implementação Azure Resource Manager, que garante uma conectividade site a site aos seus servidores de origem no local, utilizando um [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Saiba as topologias de rede para migrações de instância de base de dados do SQL do Azure geridos com o Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

    > [!NOTE]
    > Durante a configuração VNET, se utilizar o ExpressRoute com peering de rede para a Microsoft, adicione o seguinte serviço [pontos de extremidade](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) à sub-rede na qual o serviço será aprovisionado:
    > - Ponto de extremidade de destino da base de dados (por exemplo, ponto de extremidade do SQL, ponto final do Cosmos DB etc.)
    > - Ponto final de armazenamento
    > - Ponto final de barramento de serviço
    >
    > Esta configuração é necessária porque o serviço de migração de base de dados do Azure não tem conectividade à internet.

- Certifique-se de que as regras do grupo de segurança de rede de VNET não bloqueiam as seguintes portas de comunicação 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego dos NSGs das VNETs do Azure, veja o artigo [Filter network traffic with network security groups](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (Filtrar tráfego de rede com grupos de segurança de rede).
- Configurar a sua Firewall do Windows para acesso ao motor de bases de dados. Veja [Windows Firewall for source database engine access](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abrir a Firewall do Windows para permitir ao Azure Database Migration Service aceder ao SQL Server de origem, que, por predefinição, é a porta TCP 1433.
- Se estiver a executar várias instâncias nomeadas do SQL Server em portas dinâmicas, poderá ser útil ativar o SQL Browser Service e permitir o acesso à porta UDP 1434 através das suas firewalls, de modo a que o Azure Database Migration Service se possa ligar a uma instância nomeada no servidor de origem.
- Se estiver a utilizar uma aplicação de firewall à frente da base ou bases de dados, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base ou bases de dados de origem para migração, bem como ficheiros através da porta SMB 445.
- Criar uma instância gerida da base de dados do Azure SQL, seguindo o detalhe no artigo [criar uma instância gerida da base de dados do Azure SQL no portal do Azure](https://aka.ms/sqldbmi).
- Certifique-se de que os inícios de sessão utilizado para ligar a origem do SQL Server e a instância gerida de destino são membros da função de servidor sysadmin.
- Forneça uma partilha de rede SMB que contenha os ficheiros completos de cópia de segurança de base de dados e os ficheiros subsequentes de cópia de segurança de registo de transações, que o Azure Database Migration Service pode utilizar para a migração da base de dados.
- Confirme que a conta de serviço em execução na instância do SQL Server de origem tem privilégios de escrita na partilha de rede que criou e que a conta do computador do servidor de origem tem acesso de leitura/escrita à mesma partilha.
- Tome nota de um utilizador do Windows (e da palavra-passe) que tenha privilégio de controlo total na partilha de rede que criou anteriormente. O Azure Database Migration Service representa a credencial do utilizador para carregar os ficheiros de cópia de segurança para o contentor de armazenamento do Azure, para a operação de restauro.
- Crie um Azure Active Directory ID de aplicação que gera a chave de ID da aplicação que pode utilizar o serviço DMS para ligar à instância de base de dados do Azure geridos de destino e o contentor de armazenamento do Azure. Para obter mais informações, veja o artigo [Utilizar o portal para criar uma aplicação e um principal de serviço do Azure Active Directory que possam aceder aos recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).
- Crie ou anote o **escalão de Desempenho Standard**, a Conta de Armazenamento do Azure, que permite ao serviço DMS carregar os ficheiros de cópia de segurança para a base de dados e utilizá-la para a migração de bases de dados.  Certifique-se de que cria a Conta de Armazenamento do Azure na mesma região do serviço DMS criado.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

    ![Mostrar subscrições no portal](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)        
2. Selecione a subscrição na qual pretende criar a instância do Database Migration Service do Azure e, em seguida, selecione **Fornecedores de recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Criar uma instância do Azure Database Migration Service

1. No portal do Azure, selecione + **Criar um recurso**, procure o **Azure Database Migration Service** e selecione **Azure Database Migration Service**, na lista pendente.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização em que pretende criar a instância do DMS.

5. Selecione uma rede virtual (VNET) já existente ou crie uma.

    A VNET fornece o serviço de migração de base de dados do Azure com o acesso a origem do SQL Server e a instância gerida de base de dados do Azure SQL de destino.

    Para obter mais informações sobre como criar uma VNET no portal do Azure, veja o artigo [Criar uma rede virtual com o portal do Azure](https://aka.ms/DMSVnet).

    Para obter detalhes adicionais, consulte o artigo [topologias de rede para a base de dados do Azure SQL gerida migrações de instância com o Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Selecione um SKU de escalão de preços Premium.

    > [!NOTE]
    > Migrações online são suportadas apenas quando utiliza o escalão Premium. 

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Criar o serviço DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação de uma instância do serviço, localize-a no portal do Azure, abra-a e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

    ![Localizar todas as instâncias do Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. No ecrã **Azure Database Migration Service**, procure o nome da instância que criou e selecione-a.

3. Selecione + **Novo Projeto de Migração**.

4. Na **novo projeto de migração** ecrã, especifique um nome para o projeto, no **tipo de servidor de origem** caixa de texto, selecione **do SQL Server**, no **servidor de destino tipo** caixa de texto, selecione **instância gerida da base de dados SQL do Azure**e, em seguida, para **Escolher tipo de atividade**, selecione **migração de dados Online** .

   ![Criar o projeto do DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Selecione **Criar e executar atividade** para criar o projeto.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã **Detalhes da origem da migração**, especifique os detalhes da ligação do SQL Server de origem.

2. Se não tiver um certificado fidedigno instalado no seu servidor, selecione a caixa de verificação **Confiar no certificado de servidor**.

    Se não tiver um certificado fidedigno instalado, o SQL Server gera um certificado autoassinado quando a instância é iniciada. Este certificado é utilizado para encriptar as credenciais para as ligações de cliente.

    > [!CAUTION]
    > As ligações SSL encriptadas que utilizem um certificado autoassinado não proporcionam segurança forte. São suscetíveis a ataques man-in-the-middle. Não deve confiar em SSL com certificados autoassinados num ambiente de produção ou em servidores que estejam ligados à Internet.

   ![Detalhes da origem](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Selecione **Guardar**.

4. No ecrã **Selecionar bases de dados de origem**, selecione a base de dados **Adventureworks2012** para migração.

   ![Selecionar as bases de dados de origem](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Se utilizar o SQL Server Integration Services (SSIS), DMS não suporta atualmente migrar a base de dados de catálogo dos projetos/pacotes do SSIS (SSISDB) do SQL Server para instância gerida da base de dados do Azure SQL. No entanto, pode aprovisionar o SSIS no Azure Data Factory (ADF) e voltar a implementar dos projetos/pacotes do SSIS para o SSISDB alojado por instância gerida da base de dados do Azure SQL de destino. Para obter mais informações sobre a migração de pacotes de SSIS, veja o artigo [pacotes de migrar o SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Selecione **Guardar**.

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Sobre o **detalhes do destino de migração** ecrã, especifique a **ID da aplicação** e **chave** que a instância DMS pode utilizar para ligar à instância de destino da base de dados do Azure SQL instância gerida e a conta de armazenamento do Azure.

    Para obter mais informações, veja o artigo [Utilizar o portal para criar uma aplicação e um principal de serviço do Azure Active Directory que possam aceder aos recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Selecione o **subscrição** que contém a instância de base de dados do SQL do Azure geridos destino de instância e, em seguida, selecione a instância de destino.

    Se ainda não aprovisionou a instância gerida da base de dados do Azure SQL, selecione o [link](https://aka.ms/SQLDBMI) para o ajudar a aprovisionar a instância. Quando a instância gerida da base de dados do Azure SQL estiver pronta, regresse a este projeto específico para executar a migração.

3. Fornecer **utilizador de SQL** e **palavra-passe** ligar à base de dados SQL do Azure de instância gerida.

    ![Selecionar o Destino](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Selecione **Guardar**.

## <a name="select-source-databases"></a>Selecionar as bases de dados de origem

1. No ecrã **Selecionar as base de dados de origem**, selecione a base de dados de origem que quer migrar.

    ![Selecionar as bases de dados de origem](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. Selecione **Guardar**.

## <a name="configure-migration-settings"></a>Configurar as definições da migração

1. No ecrã **Configurar as definições da migração**, indique os detalhes seguintes:

    | | |
    |--------|---------|
    |**Partilha de localização na Rede SMB** | A partilha de rede SMB local que contém os ficheiros Completos de cópia de segurança de base de dados e os ficheiros de cópia de segurança de registo de transações, que o Azure Database Migration Service pode utilizar para a migração. A conta de serviço que estiver a executar a instância do SQL Server de origem tem de ter privilégios de leitura\escrita nesta partilha de rede. Indique um FQDN ou um endereço IP do servidor na partilha de rede, como, por exemplo, “\\\servername.domainname.com\backupfolder” ou “\\\IP address\backupfolder”.|
    |**Nome de utilizador** | Certifique-se de que o utilizador do Windows tem privilégio de controlo total na partilha de rede que indicou acima. O Azure Database Migration Service vai representar a credencial do utilizador para carregar os ficheiros de cópia de segurança para o contentor de armazenamento do Azure, para a operação de restauro. |
    |**Palavra-passe** | A palavra-passe do utilizador. |
    |**Subscrição da Conta de Armazenamento do Azure** | Selecione a subscrição que contém a Conta de Armazenamento do Azure. |
    |**Conta de Armazenamento do Azure** | Selecione a Conta de Armazenamento do Azure para a qual o DMS pode carregar os ficheiros de cópia de segurança da partilha de rede SMB e utilizá-los para a migração de base de dados.  Recomendamos que selecione a Conta de Armazenamento na mesma região do serviço DMS para um desempenho de carregamento de ficheiros ideal. |

    ![Configurar as Definições da Migração](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

2. Selecione **Guardar**.

## <a name="review-the-migration-summary"></a>Rever o resumo da migração

1. No ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, indique um nome para a atividade de migração.

2. Reveja e verifique os detalhes associados ao projeto de migração.

    ![Resumo do projeto de migração](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>Executar e monitorizar a migração

1. Selecione **Executar a migração**.

2. No ecrã de atividade da migração, selecione **Atualizar** para atualizar o ecrã.

   ![Atividade de migração em curso](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    Pode expandir ainda mais as categorias de bases de dados e inícios de sessão para monitorizar o estado da migração dos respetivos objetos de servidor.

   ![Atividade de migração em curso](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Efetuar a transferência da migração

Depois de restaurar a cópia de segurança completa da base de dados na instância do destino de instância gerida da base de dados do Azure SQL, a base de dados está disponível para executar uma migração de transferência.

1. Quando estiver pronto para concluir a migração da base de dados online, selecione **Iniciar Transferência**.

2. Pare todo o tráfego de entrada para as bases de dados de origem.

3. Com a [cópia de segurança de registo após erro], disponibilize o ficheiro de cópia de segurança na rede de partilha SMB e, em seguida, aguarde até que a cópia de segurança de registo de transações seja restaurada.

    Nesse momento, verá que as **Alterações pendentes** estão definidas como 0.

4. Selecione **Confirmar** e, em seguida, selecione **Aplicar**.

    ![Preparar para concluir transferência](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Quando o estado de migração de base de dados é apresentado **concluído**, conectar seus aplicativos para a nova instância de destino de instância gerida da base de dados do Azure SQL.

    ![Transferência concluída](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Passos Seguintes

- Para obter um tutorial que mostra como migrar uma base de dados para uma instância gerida com o comando T-SQL RESTORE, consulte [restaurar uma cópia de segurança para uma instância gerida com o comando restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Para obter informações sobre a instância gerida, veja [o que é uma instância gerida](../sql-database/sql-database-managed-instance.md).
- Para obter informações sobre como ligar aplicações para uma instância gerida, veja [ligar aplicações](../sql-database/sql-database-managed-instance-connect-app.md).
