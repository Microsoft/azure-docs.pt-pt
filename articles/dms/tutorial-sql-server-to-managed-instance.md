---
title: 'Tutorial: migrar SQL Server para instância gerenciada do SQL'
titleSuffix: Azure Database Migration Service
description: Saiba como migrar do SQL Server local para uma instância gerenciada do banco de dados SQL do Azure usando o serviço de migração de banco de dados do Azure.
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
ms.openlocfilehash: d8e5b531684e175e5b9423bbc302bbe0b3d36058
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75745281"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-offline-using-dms"></a>Tutorial: migrar SQL Server para uma instância gerenciada do banco de dados SQL do Azure offline usando DMS

Você pode usar o serviço de migração de banco de dados do Azure para migrar os bancos de dados de uma instância de SQL Server local para uma [instância gerenciada do banco de dados SQL do Azure](../sql-database/sql-database-managed-instance.md). Para métodos adicionais que podem exigir algum esforço manual, consulte o artigo [SQL Server migração da instância para a instância gerenciada do banco de dados SQL do Azure](../sql-database/sql-database-managed-instance-migrate.md).

Neste tutorial, você migra o banco de dados **Adventureworks2012** de uma instância local do SQL Server para uma instância gerenciada do banco de dados SQL usando o serviço de migração de banco de dados do Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> - Crie uma instância do Azure Database Migration Service.
> - Crie um projeto de migração usando o serviço de migração de banco de dados do Azure.
> - Executar a migração.
> - Monitorizar a migração.
> - Transferir um relatório da migração.

> [!IMPORTANT]
> Para migrações offline de SQL Server para a instância gerenciada do banco de dados SQL, o serviço de migração de banco de dados do Azure pode criar os arquivos de backup para você. Como alternativa, você pode fornecer o backup de banco de dados completo mais recente no compartilhamento de rede SMB que o serviço usará para migrar seus bancos. Não acrescente vários backups em uma única mídia de backup; Faça cada backup em um arquivo de backup separado. Observe que você também pode usar backups compactados para reduzir a probabilidade de ocorrer problemas potenciais com a migração de grandes backups.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração offline do SQL Server para uma instância gerenciada do banco de dados SQL. Para uma migração online, consulte [migrar SQL Server para uma instância gerenciada do banco de dados SQL do Azure online usando DMS](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

- Crie um Rede Virtual do Microsoft Azure para o serviço de migração de banco de dados do Azure usando o modelo de implantação Azure Resource Manager, que fornece conectividade site a site para seus servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Aprenda topologias de rede para migrações de instância gerenciada do banco de dados SQL do Azure usando o serviço de migração de banco de](https://aka.ms/dmsnetworkformi) Para obter mais informações sobre como criar uma rede virtual, consulte a [documentação da rede virtual](https://docs.microsoft.com/azure/virtual-network/)e especialmente os artigos de início rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração de rede virtual, se você usar o ExpressRoute com emparelhamento de rede para a Microsoft, adicione os seguintes [pontos de extremidade](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à sub-rede na qual o serviço será provisionado:
    > - Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade SQL, ponto de extremidade Cosmos DB e assim por diante)
    > - Ponto de extremidade de armazenamento
    > - Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o serviço de migração de banco de dados do Azure não tem conectividade com a Internet.

- Verifique se as regras do grupo de segurança de rede de rede virtual não bloqueiam as seguintes portas de comunicação de entrada para o serviço de migração de banco de dados do Azure: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego NSG de rede virtual, consulte o artigo [filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Configurar a sua Firewall do Windows para acesso ao motor de bases de dados. Veja [Windows Firewall for source database engine access](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abra o Firewall do Windows para permitir que o serviço de migração de banco de dados do Azure acesse o SQL Server de origem, que por padrão é a porta TCP 1433.
- Se você estiver executando várias instâncias de SQL Server nomeadas usando portas dinâmicas, talvez queira habilitar o serviço de SQL Browser e permitir o acesso à porta UDP 1434 por meio de firewalls para que o serviço de migração de banco de dados do Azure possa se conectar a uma instância nomeada em sua origem servidor.
- Se você estiver usando um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o serviço de migração de banco de dados do Azure acesse os bancos de dados de origem para migração, bem como arquivos por meio da porta SMB 445.
- Crie uma instância gerenciada do banco de dados SQL do Azure seguindo os detalhes no artigo [criar uma instância gerenciada do banco de dados SQL do Azure no portal do Azure](https://aka.ms/sqldbmi).
- Verifique se os logons usados para conectar o SQL Server de origem e a instância gerenciada de destino são membros da função de servidor sysadmin.

    >[!NOTE]
    >Por padrão, o serviço de migração de banco de dados do Azure dá suporte apenas à migração de logons SQL. No entanto, você pode habilitar a capacidade de migrar logons do Windows por:
    >
    >- Garantir que a instância gerenciada do banco de dados SQL de destino tenha acesso de leitura do AAD, que pode ser configurada por meio do portal do Azure por um usuário com a função **administrador da empresa**ou **administrador global**.
    >- Configurando sua instância do serviço de migração de banco de dados do Azure para habilitar migrações de logon de usuário/grupo do Windows, que é configurada por meio da portal do Azure, na página de configuração. Depois de habilitar essa configuração, reinicie o serviço para que as alterações entrem em vigor.
    >
    > Depois de reiniciar o serviço, os logons de usuário/grupo do Windows aparecem na lista de logons disponíveis para migração. Para qualquer logon de usuário/grupo do Windows que você migrar, será solicitado que você forneça o nome de domínio associado. Não há suporte para contas de usuário de serviço (conta com nome de domínio Autoridade NT) e contas de usuário virtual (nome de conta com o serviço NT de nome de domínio).

- Crie um compartilhamento de rede que o serviço de migração de banco de dados do Azure pode usar para fazer backup do banco de dados de origem.
- Confirme que a conta de serviço em execução na instância do SQL Server de origem tem privilégios de escrita na partilha de rede que criou e que a conta do computador do servidor de origem tem acesso de leitura/escrita à mesma partilha.
- Tome nota de um utilizador do Windows (e da palavra-passe) que tenha privilégio de controlo total na partilha de rede que criou anteriormente. O serviço de migração de banco de dados do Azure representa a credencial do usuário para carregar os arquivos de backup no contêiner de armazenamento do Azure para a operação de restauração.
- Crie um contentor de blobs e obtenha o URI da SAS com os passos no artigo [Manage Azure Blob Storage resources with Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) (Gerir recursos do Armazenamento de Blobs do Azure com o Explorador de Armazenamento). Confirme que seleciona todas as permissões (Leitura, Escrita, Eliminação, Listagem) na janela da política quando criar o URI da SAS. Esse detalhe fornece ao serviço de migração de banco de dados do Azure acesso ao contêiner de conta de armazenamento para carregar os arquivos de backup usados para migrar bancos de dados para a instância gerenciada do banco de dados SQL do Azure.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

    ![Mostrar subscrições no portal](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. Selecione a assinatura na qual você deseja criar a instância do serviço de migração de banco de dados do Azure e, em seguida, selecione **provedores de recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Criar uma instância do Azure Database Migration Service

1. No portal do Azure, selecione + **Criar um recurso**, procure o **Azure Database Migration Service** e selecione **Azure Database Migration Service**, na lista pendente.

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização em que pretende criar a instância do DMS.

5. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece ao serviço de migração de banco de dados do Azure acesso ao SQL Server de origem e à instância gerenciada do banco de dados SQL do Azure de destino.

    Para obter mais informações sobre como criar uma rede virtual no portal do Azure, consulte o artigo [criar uma rede virtual usando o portal do Azure](https://aka.ms/DMSVnet).

    Para obter detalhes adicionais, confira o artigo [topologias de rede para migrações de instância gerenciada do BD SQL do Azure usando o serviço de migração de banco de dados](https://aka.ms/dmsnetworkformi)

6. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Criar o serviço DMS](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação de uma instância do serviço, localize-a no portal do Azure, abra-a e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

    ![Localizar todas as instâncias do serviço de migração de banco de dados do Azure](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. No ecrã **Azure Database Migration Service**, procure o nome da instância que criou e selecione-a.

3. Selecione + **Novo Projeto de Migração**.

4. No ecrã **Novo projeto de migração**, indique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **SQL Server**, na caixa de texto **Tipo de servidor de destino**, selecione **Instância Gerida da Base de Dados SQL do Azure** e, em **Escolher tipo de atividade**, selecione **Migração de dados offline**.

   ![Criar o projeto do DMS](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Selecione **Create** (Criar) para criar o projeto.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã **Detalhes da origem da migração**, especifique os detalhes da ligação do SQL Server de origem.

2. Se não tiver um certificado fidedigno instalado no seu servidor, selecione a caixa de verificação **Confiar no certificado de servidor**.

    Se não tiver um certificado fidedigno instalado, o SQL Server gera um certificado autoassinado quando a instância é iniciada. Este certificado é utilizado para encriptar as credenciais para as ligações de cliente.

    > [!CAUTION]
    > As ligações SSL encriptadas que utilizem um certificado autoassinado não proporcionam segurança forte. São suscetíveis a ataques man-in-the-middle. Não deve confiar em SSL com certificados autoassinados num ambiente de produção ou em servidores que estejam ligados à Internet.

   ![Detalhes da origem](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Selecione **Guardar**.

4. No ecrã **Selecionar bases de dados de origem**, selecione a base de dados **Adventureworks2012** para migração.

   ![Selecionar as bases de dados de origem](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Se você usar SQL Server Integration Services (SSIS), o DMS atualmente não oferece suporte à migração do banco de dados de catálogo para seus projetos/pacotes SSIS (SSISDB) do SQL Server para a instância gerenciada do banco de dados SQL do Azure. No entanto, você pode provisionar o SSIS em Azure Data Factory (ADF) e reimplantar seus projetos/pacotes do SSIS no SSISDB de destino, hospedado pela instância gerenciada do banco de dados SQL do Azure. Para obter mais informações sobre como migrar pacotes SSIS, consulte o artigo [migrar pacotes de SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Selecione **Guardar**.

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Na tela **detalhes do destino de migração** , especifique os detalhes de conexão para o destino, que é a instância gerenciada pré-configurada do banco de dados SQL do Azure para a qual você está migrando o banco de dados **AdventureWorks2012** .

    Se você ainda não tiver provisionado a instância gerenciada do banco de dados SQL, selecione o [link](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) para ajudá-lo a provisionar a instância. Você ainda pode continuar com a criação do projeto e, em seguida, quando a instância gerenciada do banco de dados SQL do Azure estiver pronta, retorne a esse projeto específico para executar a migração.

    ![Selecionar o Destino](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Selecione **Guardar**.

## <a name="select-source-databases"></a>Selecionar as bases de dados de origem

1. No ecrã **Selecionar as base de dados de origem**, selecione a base de dados de origem que quer migrar.

    ![Selecionar as bases de dados de origem](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Selecione **Guardar**.

## <a name="select-logins"></a>Selecionar os inícios de sessão

1. No ecrã **Selecionar os inícios de sessão**, selecione os inícios de sessão que quer migrar.

    >[!NOTE]
    >Por padrão, o serviço de migração de banco de dados do Azure dá suporte apenas à migração de logons SQL. Para habilitar o suporte para migrar logons do Windows, consulte a seção **pré-requisitos** deste tutorial.

    ![Selecionar os inícios de sessão](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Selecione **Guardar**.

## <a name="configure-migration-settings"></a>Configurar as definições da migração

1. No ecrã **Configurar as definições da migração**, indique os detalhes seguintes:

    | | |
    |--------|---------|
    |**Escolher a opção de cópia de segurança de origem** | Escolha a opção **Vou disponibilizar os ficheiros de cópia de segurança mais recentes** se já tiver ficheiros de cópia de segurança completa disponíveis para o DMS utilizar para migrar a base de dados. Escolha a opção **O Azure Database Migration Service vai criar os ficheiros de cópia de segurança** se quiser que o DMS utilize a cópia de segurança completa da base de dados de origem e a utilize para a migração. |
    |**Partilha de localização na rede** | O compartilhamento de rede SMB local do qual o serviço de migração de banco de dados do Azure pode pegar os backups de banco de dados. A conta de serviço que estiver a executar a instância do SQL Server de origem tem de ter privilégios de escrita nesta partilha de rede. Indique um FQDN ou um endereço IP do servidor na partilha de rede, como, por exemplo, “\\\servername.domainname.com\backupfolder” ou “\\\IP address\backupfolder”.|
    |**Nome de utilizador** | Certifique-se de que o utilizador do Windows tem privilégio de controlo total na partilha de rede que indicou acima. O serviço de migração de banco de dados do Azure representará a credencial do usuário para carregar os arquivos de backup no contêiner de armazenamento do Azure para operação de restauração. Se estiverem selecionadas bases de dados ativadas para TDE para migração, o utilizador do Windows acima tem de ser a conta de administrador incorporada e o [Controlo de Conta de Utilizador](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview) tem de ser desativado, para que o Azure Database Migration Service carregue e elimine os ficheiros de certificado. |
    |**Palavra-passe** | A palavra-passe do utilizador. |
    |**Definições da conta de armazenamento** | O URI de SAS que fornece o serviço de migração de banco de dados do Azure com acesso ao seu contêiner de conta de armazenamento para o qual o serviço carrega os arquivos de backup e que é usado para migrar bancos de dados para a instância gerenciada do Azure SQL Database. Veja [Learn how to get the SAS URI for blob container](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) (Saiba como obter o URI da SAS para o contentor de blobs).|
    |**Definições de TDE** | Se estiver migrando os bancos de dados de origem com Transparent Data Encryption (TDE) habilitado, você precisará ter privilégios de gravação na instância gerenciada do banco de dados SQL do Azure de destino.  Selecione a assinatura na qual a instância gerenciada do banco de dados SQL do Azure é provisionada no menu suspenso.  Selecione a **Instância Gerida da Base de Dados SQL do Azure** de destino no menu pendente. |

    ![Configurar as Definições da Migração](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. Selecione **Guardar**.

## <a name="review-the-migration-summary"></a>Rever o resumo da migração

1. No ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, indique um nome para a atividade de migração.

2. Expanda a secção **Opção de validação** para mostrar o ecrã **Escolher opção de validação**, especifique se a base de dados deve ser verificada quanto à exatidão das consultas e selecione **Guardar**.

3. Reveja e verifique os detalhes associados ao projeto de migração.

    ![Resumo do projeto de migração](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. Selecione **Guardar**.

## <a name="run-the-migration"></a>Executar a migração

- Selecione **Executar a migração**.

  É apresentada a janela de atividade da migração e o estado da atividade é **Pendente**.

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade da migração, selecione **Atualizar**, para atualizar o ecrã.

   ![Atividade de migração em curso](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    Pode expandir ainda mais as categorias de bases de dados e inícios de sessão para monitorizar o estado da migração dos respetivos objetos de servidor.

   ![Atividade de migração em curso](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Após a conclusão da migração, selecione **Transferir relatório** para obter um relatório que liste os detalhes associados ao processo de migração.

3. Verifique se o banco de dados de destino no ambiente de instância gerenciada do banco de dados SQL do Azure de destino.

## <a name="next-steps"></a>Passos seguintes

- Para obter um tutorial mostrando como migrar um banco de dados para uma instância gerenciada usando o comando T-SQL RESTOre, consulte [restaurar um backup em uma instância gerenciada usando o comando restaurar](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Para obter informações sobre a instância gerenciada, consulte [o que é uma instância gerenciada](../sql-database/sql-database-managed-instance.md).
- Para obter informações sobre como conectar aplicativos a uma instância gerenciada, consulte [conectar aplicativos](../sql-database/sql-database-managed-instance-connect-app.md).
