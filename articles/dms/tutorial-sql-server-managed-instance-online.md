---
title: 'Tutorial: usar o serviço de migração de banco de dados do Azure para executar uma migração online do SQL Server para uma instância gerenciada do banco de dados SQL do Azure | Microsoft Docs'
description: Saiba como executar uma migração online do SQL Server local para uma instância gerenciada do banco de dados SQL do Azure usando o serviço de migração de banco de dados do Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 12/04/2019
ms.openlocfilehash: d7a746c170d04ad17b86e8aca63384edffbe75ac
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806801"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Tutorial: migrar SQL Server para uma instância gerenciada do banco de dados SQL do Azure online usando DMS

Você pode usar o serviço de migração de banco de dados do Azure para migrar os bancos de dados de uma instância de SQL Server local para uma [instância gerenciada do banco de dados SQL do Azure](../sql-database/sql-database-managed-instance.md) com tempo de inatividade mínimo. Para métodos adicionais que podem exigir algum esforço manual, consulte o artigo [SQL Server migração da instância para a instância gerenciada do banco de dados SQL do Azure](../sql-database/sql-database-managed-instance-migrate.md).

Neste tutorial, você migra o banco de dados **Adventureworks2012** de uma instância local do SQL Server para uma instância gerenciada do banco de dados SQL com tempo de inatividade mínimo usando o serviço de migração de banco de dados do Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Crie uma instância do Azure Database Migration Service.
> * Crie um projeto de migração e inicie a migração online usando o serviço de migração de banco de dados do Azure.
> * Monitorizar a migração.
> * Execute a transferência de migração quando estiver pronto.

> [!IMPORTANT]
> Para migrações online de SQL Server para instância gerenciada do banco de dados SQL usando o serviço de migração de banco de dados do Azure, você deve fornecer o backup completo do banco de dados e os backups de log subsequentes no compartilhamento de rede SMB que o serviço pode usar para migrar seus bancos de dados. O serviço de migração de banco de dados do Azure não inicia nenhum backup e, em vez disso, usa backups existentes, que talvez você já tenha como parte do seu plano de recuperação de desastres, para a migração.
> Certifique-se de fazer [backups usando a opção with checksum](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017). Além disso, certifique-se de não acrescentar vários backups (ou seja, Full e t-log) em uma única mídia de backup; Faça cada backup em um arquivo de backup separado. Por fim, você pode usar backups compactados para reduzir a probabilidade de ocorrer problemas potenciais associados à migração de grandes backups.

> [!NOTE]
> Usar o serviço de migração de banco de dados do Azure para executar uma migração online requer a criação de uma instância com base no tipo de preço premium.

> [!IMPORTANT]
> Para uma experiência de migração ideal, a Microsoft recomenda a criação de uma instância do serviço de migração de banco de dados do Azure na mesma região do Azure que o banco de dados de destino. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração online do SQL Server para uma instância gerenciada do banco de dados SQL. Para uma migração offline, consulte [migrar SQL Server para uma instância gerenciada do banco de dados SQL offline usando DMS](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Crie uma rede virtual do Azure (VNet) para o serviço de migração de banco de dados do Azure usando o modelo de implantação Azure Resource Manager, que fornece conectividade site a site para seus servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Aprenda topologias de rede para migrações de instância gerenciada do banco de dados SQL do Azure usando o serviço de migração de banco de](https://aka.ms/dmsnetworkformi) Para obter mais informações sobre como criar uma VNet, consulte a [documentação da rede virtual](https://docs.microsoft.com/azure/virtual-network/)e especialmente os artigos de início rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da VNet, se você usar o ExpressRoute com emparelhamento de rede para a Microsoft, adicione os seguintes [pontos de extremidade](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à sub-rede na qual o serviço será provisionado:
    >
    > * Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade SQL, ponto de extremidade Cosmos DB e assim por diante)
    > * Ponto de extremidade de armazenamento
    > * Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o serviço de migração de banco de dados do Azure não tem conectividade com a Internet.
    >
    >Se você não tiver conectividade site a site entre a rede local e o Azure ou se houver uma largura de banda de conectividade site a site limitada, considere usar o serviço de migração de banco de dados do Azure no modo híbrido (versão prévia). O modo híbrido aproveita um trabalho de migração local junto com uma instância do serviço de migração de banco de dados do Azure em execução na nuvem. Para criar uma instância do serviço de migração de banco de dados do Azure no modo híbrido, consulte o artigo [criar uma instância do serviço de migração de banco de dados do Azure no modo híbrido usando o portal do Azure](https://aka.ms/dms-hybrid-create).

    > [!IMPORTANT]
    > Em relação à conta de armazenamento usada como parte da migração, você deve:
    > * Escolha permitir que toda a rede acesse a conta de armazenamento.
    > * Configure as ACLs para a VNet. Para obter mais informações, consulte o artigo [configurar redes virtuais e firewalls de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

* Certifique-se de que suas regras de grupo de segurança de rede VNet não bloqueiem as seguintes portas de comunicação de entrada para o serviço de migração de banco de dados do Azure: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego NSG VNet do Azure, consulte o artigo [filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Configurar a sua Firewall do Windows para acesso ao motor de bases de dados. Veja [Windows Firewall for source database engine access](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra o Firewall do Windows para permitir que o serviço de migração de banco de dados do Azure acesse o SQL Server de origem, que por padrão é a porta TCP 1433.
* Se você estiver executando várias instâncias de SQL Server nomeadas usando portas dinâmicas, talvez queira habilitar o serviço de SQL Browser e permitir o acesso à porta UDP 1434 por meio de firewalls para que o serviço de migração de banco de dados do Azure possa se conectar a uma instância nomeada em sua origem servidor.
* Se você estiver usando um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o serviço de migração de banco de dados do Azure acesse os bancos de dados de origem para migração, bem como arquivos por meio da porta SMB 445.
* Crie uma instância gerenciada do banco de dados SQL seguindo os detalhes no artigo [criar uma instância gerenciada do banco de dados SQL do Azure no portal do Azure](https://aka.ms/sqldbmi).
* Confirme que os inícios de sessão utilizados para ligar o SQL Server de origem e a instância gerida de destino são membros da função de servidor sysadmin.
* Forneça um compartilhamento de rede SMB que contenha todos os seus arquivos de backup de banco de dados completos e arquivos de backup de log de transações subsequentes, que o serviço de migração de banco de dados do Azure pode usar para migração
* Confirme que a conta de serviço em execução na instância do SQL Server de origem tem privilégios de escrita na partilha de rede que criou e que a conta do computador do servidor de origem tem acesso de leitura/escrita à mesma partilha.
* Tome nota de um utilizador do Windows (e da palavra-passe) que tenha privilégio de controlo total na partilha de rede que criou anteriormente. O serviço de migração de banco de dados do Azure representa a credencial do usuário para carregar os arquivos de backup no contêiner de armazenamento do Azure para a operação de restauração.
* Crie um Azure Active Directory ID do aplicativo que gera a chave de ID do aplicativo que o serviço de migração de banco de dados do Azure pode usar para se conectar à instância gerenciada do banco de dados do Azure e ao contêiner de armazenamento Para obter mais informações, veja o artigo [Utilizar o portal para criar uma aplicação e um principal de serviço do Azure Active Directory que possam aceder aos recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

  > [!NOTE]
  > O serviço de migração de banco de dados do Azure requer a permissão de colaborador na assinatura para a ID de aplicativo especificada. Como alternativa, você pode criar funções personalizadas que concedem as permissões específicas que o serviço de migração de banco de dados do Azure exige. Para obter orientações passo a passo sobre como usar funções personalizadas, consulte o artigo [funções personalizadas para SQL Server para migrações online de instância gerenciada do banco de dados SQL](https://docs.microsoft.com/azure/dms/resource-custom-roles-sql-db-managed-instance).

* Crie ou anote o **escalão de Desempenho Standard**, a Conta de Armazenamento do Azure, que permite ao serviço DMS carregar os ficheiros de cópia de segurança para a base de dados e utilizá-la para a migração de bases de dados.  Certifique-se de criar a conta de armazenamento do Azure na mesma região em que a instância do serviço de migração de banco de dados do Azure é criada.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

    ![Mostrar subscrições no portal](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Selecione a assinatura na qual você deseja criar a instância do serviço de migração de banco de dados do Azure e, em seguida, selecione **provedores de recursos**.

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

5. Selecione uma VNet existente ou crie uma.

    A VNet fornece ao serviço de migração de banco de dados do Azure acesso ao SQL Server de origem e à instância gerenciada do banco de dados SQL de destino.

    Para obter mais informações sobre como criar uma VNet no portal do Azure, consulte o artigo [criar uma rede virtual usando o portal do Azure](https://aka.ms/DMSVnet).

    Para obter detalhes adicionais, consulte o artigo [topologias de rede para migrações de instância gerenciada do banco de dados SQL do Azure usando o serviço de migração de banco de](https://aka.ms/dmsnetworkformi)

6. Selecione uma SKU do tipo de preço premium.

    > [!NOTE]
    > Há suporte para migrações online somente ao usar a camada Premium.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Criar o serviço DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação de uma instância do serviço, localize-a no portal do Azure, abra-a e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

    ![Localizar todas as instâncias do serviço de migração de banco de dados do Azure](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. No ecrã **Azure Database Migration Service**, procure o nome da instância que criou e selecione-a.

3. Selecione + **Novo Projeto de Migração**.

4. Na tela **novo projeto de migração** , especifique um nome para o projeto, na caixa de texto **tipo de servidor de origem** , selecione **SQL Server**, na caixa de texto tipo de **servidor de destino** , selecione **instância gerenciada do banco de dados SQL do Azure**e, para **escolher tipo de atividade**, selecione **migração de dados online**.

   ![Criar projeto de serviço de migração de banco de dados do Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

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
    > Se você usar SQL Server Integration Services (SSIS), o DMS atualmente não oferece suporte à migração do banco de dados de catálogo para seus projetos/pacotes SSIS (SSISDB) do SQL Server para a instância gerenciada do banco de dados SQL do Azure. No entanto, você pode provisionar o SSIS em Azure Data Factory (ADF) e reimplantar seus projetos/pacotes do SSIS no SSISDB de destino, hospedado pela instância gerenciada do banco de dados SQL do Azure. Para obter mais informações sobre como migrar pacotes SSIS, consulte o artigo [migrar pacotes de SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Selecione **Guardar**.

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Na tela **detalhes do destino da migração** , ESPECIFIQUE a ID e a **chave** do **aplicativo** que a instância DMS pode usar para se conectar à instância de destino da instância GERENCIAda do banco de dados SQL do Azure e à conta de armazenamento do Azure.

    Para obter mais informações, veja o artigo [Utilizar o portal para criar uma aplicação e um principal de serviço do Azure Active Directory que possam aceder aos recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Selecione a **assinatura** que contém a instância de destino da instância gerenciada do banco de dados SQL do Azure e, em seguida, selecione a instância de destino.

    Se você ainda não tiver provisionado a instância gerenciada do banco de dados SQL do Azure, selecione o [link](https://aka.ms/SQLDBMI) para ajudá-lo a provisionar a instância. Quando a instância gerenciada do banco de dados SQL do Azure estiver pronta, retorne a este projeto específico para executar a migração.

3. Forneça o usuário e a **senha** do **SQL** para se conectar à instância gerenciada do banco de dados SQL do Azure.

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
    |**Partilha de localização na Rede SMB** | O compartilhamento de rede SMB local ou o compartilhamento de arquivos do Azure que contém os arquivos completos de backup de banco de dados e de log de transações que o serviço de migração de banco de dados do Azure pode usar para a migração. A conta de serviço que estiver a executar a instância do SQL Server de origem tem de ter privilégios de leitura\escrita nesta partilha de rede. Indique um FQDN ou um endereço IP do servidor na partilha de rede, como, por exemplo, “\\\servername.domainname.com\backupfolder” ou “\\\IP address\backupfolder”.|
    |**Nome de utilizador** | Certifique-se de que o utilizador do Windows tem privilégio de controlo total na partilha de rede que indicou acima. O serviço de migração de banco de dados do Azure representará a credencial do usuário para carregar os arquivos de backup no contêiner de armazenamento do Azure para operação de restauração. Se você estiver usando o compartilhamento de arquivos do Azure, use o nome da conta de armazenamento previamente pendente com o AZURE \ como o nome de usuário. |
    |**Palavra-passe** | A palavra-passe do utilizador. Se estiver usando o compartilhamento de arquivos do Azure, use uma chave de conta de armazenamento como a senha. |
    |**Subscrição da Conta de Armazenamento do Azure** | Selecione a subscrição que contém a Conta de Armazenamento do Azure. |
    |**Conta de Armazenamento do Azure** | Selecione a Conta de Armazenamento do Azure para a qual o DMS pode carregar os ficheiros de cópia de segurança da partilha de rede SMB e utilizá-los para a migração de base de dados.  Recomendamos que selecione a Conta de Armazenamento na mesma região do serviço DMS para um desempenho de carregamento de ficheiros ideal. |

    ![Configurar as Definições da Migração](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)


> [!NOTE]
  > Se o serviço de migração de banco de dados do Azure mostrar o erro ' erro do sistema 53 ' ou ' erro do sistema 57 ', a causa poderá resultar em uma incapacidade do serviço de migração de banco de dados do Azure de acessar o compartilhamento de arquivos do Azure Se você encontrar um desses erros, conceda acesso ao armazenamento da conta da rede virtual usando as instruções [aqui](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).


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

Depois que o backup completo do banco de dados for restaurado na instância de destino da instância gerenciada do banco de dados SQL, o banco de dados estará disponível para executar uma transferência de migração.

1. Quando estiver pronto para concluir a migração da base de dados online, selecione **Iniciar Transferência**.

2. Pare todo o tráfego de entrada para as bases de dados de origem.

3. Com a [cópia de segurança de registo após erro], disponibilize o ficheiro de cópia de segurança na rede de partilha SMB e, em seguida, aguarde até que a cópia de segurança de registo de transações seja restaurada.

    Nesse momento, verá que as **Alterações pendentes** estão definidas como 0.

4. Selecione **Confirmar** e, em seguida, selecione **Aplicar**.

    ![Preparar para concluir transferência](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Quando o status de migração do banco de dados mostrar **concluído**, conecte seus aplicativos à nova instância de destino da instância gerenciada do banco de dados SQL do Azure.

    ![Transferência concluída](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter um tutorial mostrando como migrar um banco de dados para uma instância gerenciada usando o comando T-SQL RESTOre, consulte [restaurar um backup em uma instância gerenciada usando o comando restaurar](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
* Para obter informações sobre a instância gerenciada, consulte [o que é uma instância gerenciada](../sql-database/sql-database-managed-instance.md).
* Para obter informações sobre como conectar aplicativos a uma instância gerenciada, consulte [conectar aplicativos](../sql-database/sql-database-managed-instance-connect-app.md).
