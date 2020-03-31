---
title: 'Tutorial: Migrar o Servidor SQL para a Instância gerida pela SQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a migrar do SQL Server no local para uma base de dados Azure SQL gerida através do Serviço de Migração de Bases de Dados Azure.
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
ms.openlocfilehash: 416be7de4b3cef4fb6e1bcfd09d934937f8c96d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297734"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-offline-using-dms"></a>Tutorial: Migrar o Servidor SQL para uma Base de Dados Azure SQL gerida offline usando DMS

Pode utilizar o Serviço de Migração de Bases de Dados Azure para migrar as bases de dados de uma instância do Servidor SQL no local para uma instância gerida pela Base de [Dados Azure SQL](../sql-database/sql-database-managed-instance.md). Para obter métodos adicionais que possam exigir algum esforço manual, consulte o artigo [SQL Server instância migração para a base de dados Azure SQL gerida](../sql-database/sql-database-managed-instance-migrate.md).

Neste tutorial, migra a base de dados **Adventureworks2012** de uma instância no local do SQL Server para uma base de dados SQL gerida através do Serviço de Migração de Bases de Dados Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> - Crie uma instância do Azure Database Migration Service.
> - Crie um projeto de migração utilizando o Serviço de Migração de Bases de Dados Azure.
> - Executar a migração.
> - Monitorizar a migração.
> - Transferir um relatório da migração.

> [!IMPORTANT]
> Para migrações offline do SQL Server para a SQL Database gerida, o Serviço de Migração de Bases de Dados Azure pode criar os ficheiros de backup para si. Em alternativa, pode fornecer a mais recente cópia de dados completa na partilha da rede SMB que o serviço utilizará para migrar as suas bases de dados. Não anexar várias cópias de segurança num único meio de apoio; tome cada cópia de segurança num ficheiro de reserva separado. Note que também pode utilizar backups comprimidos para reduzir a probabilidade de experimentar potenciais problemas com grandes cópias de segurança migratórias.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração offline do SQL Server para uma instância gerida pela Base de Dados SQL. Para uma migração on-line, consulte [migrate SQL Server para uma Base de Dados Azure SQL gerida online usando DMS](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

- Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-a-site aos seus servidores de origem no local, utilizando [expressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Aprenda topoologias de rede para a Base de Dados Azure SQL gerida por migrações de instâncias utilizando o Serviço](https://aka.ms/dmsnetworkformi)de Migração de Bases de Dados Azure . Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação](https://docs.microsoft.com/azure/virtual-network/)da Rede Virtual , e especialmente os artigos quickstart com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se utilizar o ExpressRoute com o peering da rede para a Microsoft, adicione os [seguintes pontos finais](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à subnet na qual o serviço será aprovisionado:
    > - Ponto final da base de dados alvo (por exemplo, ponto final SQL, ponto final cosmos DB, e assim por diante)
    > - Ponto final de armazenamento
    > - Ponto final do ônibus de serviço
    >
    > Esta configuração é necessária porque o Serviço de Migração de Bases de Dados Azure carece de conectividade na Internet.

- Certifique-se de que as regras do Grupo de Segurança da Rede Virtual não bloqueiam as seguintes portas de comunicação de entrada para o Serviço de Migração de Bases de Dados Azure: 443, 53, 9354, 445, 12000. Para mais detalhes sobre a filtragem de tráfego da rede virtual NSG, consulte o artigo Filtrar o tráfego da [rede com grupos](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)de segurança da rede .
- Configurar a sua Firewall do Windows para acesso ao motor de bases de dados. Veja [Windows Firewall for source database engine access](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abra o seu Windows Firewall para permitir que o Serviço de Migração de Bases de Dados Azure aceda à fonte Do Servidor SQL, que por padrão é a porta TCP 1433.
- Se estiver a executar várias instâncias de Servidor SQL com portas dinâmicas, poderá desejar ativar o Serviço de Navegador SQL e permitir o acesso à porta UDP 1434 através das suas firewalls para que o Serviço de Migração da Base de Dados Azure possa ligar-se a uma instância nomeada na sua fonte servidor.
- Se estiver a utilizar um aparelho de firewall em frente às bases de dados de origem, poderá ter de adicionar regras de firewall para permitir que o Serviço de Migração de Bases de Dados Azure aceda à base de dados de origem para migração, bem como ficheiros através da porta SMB 445.
- Crie uma base de dados Azure SQL gerida seguindo os detalhes do artigo Criar uma base de [dados Azure SQL gerida no portal Azure](https://aka.ms/sqldbmi).
- Certifique-se de que os logins utilizados para ligar a fonte SQL Server e a instância gerida pelo alvo são membros da função do servidor de sysadmin.

    >[!NOTE]
    >Por predefinição, o Serviço de Migração de Bases de Dados Azure apenas suporta logins SQL migratórios. No entanto, pode permitir a capacidade de migrar os logins do Windows através de:
    >
    >- Garantir que a instância gerida pela Base de Dados SQL tem acesso de leitura AAD, que pode ser configurado através do portal Azure por um utilizador com o Administrador da **Empresa**ou um **Administrador Global**" função.
    >- Configurar a sua instância do Serviço de Migração de Bases de Dados Azure para permitir as migrações de login do utilizador/grupo windows, que é configurada através do portal Azure, na página de Configuração. Depois de permitir esta definição, reinicie o serviço para que as alterações entrem em vigor.
    >
    > Depois de reiniciar o serviço, os logins do utilizador/grupo windows aparecem na lista de logins disponíveis para migração. Para qualquer utilizador/grupo do Windows que emigra, é solicitado que forneça o nome de domínio associado. As contas de utilizador do serviço (conta com nome de domínio NT AUTHORITY) e contas de utilizador virtuais (nome de conta com nome de domínio NT SERVICE) não são suportadas.

- Crie uma partilha de rede que o Azure Database Migration Service pode usar para fazer o backup da base de dados de origem.
- Confirme que a conta de serviço em execução na instância do SQL Server de origem tem privilégios de escrita na partilha de rede que criou e que a conta do computador do servidor de origem tem acesso de leitura/escrita à mesma partilha.
- Tome nota de um utilizador do Windows (e da palavra-passe) que tenha privilégio de controlo total na partilha de rede que criou anteriormente. O Serviço de Migração da Base de Dados Azure personifica a credencial do utilizador para enviar os ficheiros de backup para o contentor de armazenamento Azure para restabelecer o funcionamento.
- Crie um contentor de blobs e obtenha o URI da SAS com os passos no artigo [Manage Azure Blob Storage resources with Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) (Gerir recursos do Armazenamento de Blobs do Azure com o Explorador de Armazenamento). Confirme que seleciona todas as permissões (Leitura, Escrita, Eliminação, Listagem) na janela da política quando criar o URI da SAS. Este detalhe fornece ao Azure Database Migration Service acesso ao seu recipiente de conta de armazenamento para o upload dos ficheiros de backup utilizados para bases de dados migratórias para a instância gerida pela Base de Dados Azure SQL.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

    ![Mostrar subscrições no portal](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores**de Recursos .

    ![Mostrar fornecedores de recursos](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Criar uma instância do Azure Database Migration Service

1. No portal Azure, selecione + **Crie um recurso,** procure o Serviço de Migração de Bases de **Dados Azure**e, em seguida, selecione O Serviço de Migração de Bases de **Dados Azure** da lista de drop-down.

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização em que pretende criar a instância do DMS.

5. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece ao Azure Database Migration Service acesso à fonte SQL Server e ao target Azure SQL Database gerido.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure](https://aka.ms/DMSVnet).

    Para mais detalhes, consulte o artigo [Topoologias de rede para migrações de instâncias geridas azure SQL DB utilizando o Serviço](https://aka.ms/dmsnetworkformi)de Migração de Bases de Dados Azure .

6. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Criar o serviço DMS](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação de uma instância do serviço, localize-a no portal do Azure, abra-a e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

    ![Localize todas as instâncias do Serviço de Migração da Base de Dados Azure](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. No ecrã do Serviço de Migração de Bases de **Dados Azure,** procure o nome da instância que criou e, em seguida, selecione a instância.

3. Selecione + **Novo Projeto de Migração**.

4. No ecrã **Novo projeto de migração**, indique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **SQL Server**, na caixa de texto **Tipo de servidor de destino**, selecione **Instância Gerida da Base de Dados SQL do Azure** e, em **Escolher tipo de atividade**, selecione **Migração de dados offline**.

   ![Criar o projeto do DMS](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Selecione **Create** (Criar) para criar o projeto.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã **Detalhes da origem da migração**, especifique os detalhes da ligação do SQL Server de origem.

2. Se não tiver um certificado fidedigno instalado no seu servidor, selecione a caixa de verificação **Confiar no certificado de servidor**.

    Se não tiver um certificado fidedigno instalado, o SQL Server gera um certificado autoassinado quando a instância é iniciada. Este certificado é utilizado para encriptar as credenciais para as ligações de cliente.

    > [!CAUTION]
    > As ligações TLS que são encriptadas utilizando um certificado auto-assinado não proporcionam uma segurança forte. São suscetíveis a ataques man-in-the-middle. Não deve confiar no TLS utilizando certificados auto-assinados num ambiente de produção ou em servidores ligados à internet.

   ![Detalhes da origem](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Selecione **Guardar**.

4. No ecrã **Selecionar bases de dados de origem**, selecione a base de dados **Adventureworks2012** para migração.

   ![Selecionar as bases de dados de origem](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Se utilizar os Serviços de Integração do Servidor SQL (SSIS), o DMS não suporta atualmente a migração da base de dados de catálogo para os seus projetos/pacotes SSIS (SSISDB) do SQL Server para o Azure SQL Database gerido. No entanto, pode fornecer SSIS na Azure Data Factory (ADF) e reimplantar os seus projetos/pacotes SSIS para o destino SSISDB hospedado pela Azure SQL Database gerida pela instância gerida. Para obter mais informações sobre pacotes SSIS migratórios, consulte o artigo Pacotes de Serviços de Integração de [Servidores SQL para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Selecione **Guardar**.

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. No ecrã de detalhes do **alvo da Migração,** especifique os detalhes da ligação para o alvo, que é a base de dados Azure SQL pré-disponibilizada para a qual está a migrar a base de dados **AdventureWorks2012.**

    Se ainda não disponibilizou a instância gerida pela Base de Dados SQL, selecione o [link](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) para o ajudar a fornecer a instância. Ainda pode continuar com a criação do projeto e, depois, quando a base de dados Azure SQL estiver pronta, volte a este projeto específico para executar a migração.

    ![Selecionar o Destino](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Selecione **Guardar**.

## <a name="select-source-databases"></a>Selecionar as bases de dados de origem

1. No ecrã **Selecionar as base de dados de origem**, selecione a base de dados de origem que quer migrar.

    ![Selecionar as bases de dados de origem](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Selecione **Guardar**.

## <a name="select-logins"></a>Selecionar os inícios de sessão

1. No ecrã **Selecionar os inícios de sessão**, selecione os inícios de sessão que quer migrar.

    >[!NOTE]
    >Por predefinição, o Serviço de Migração de Bases de Dados Azure apenas suporta logins SQL migratórios. Para permitir o suporte para logins do Windows migratório, consulte a secção **pré-requisitos** deste tutorial.

    ![Selecionar os inícios de sessão](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Selecione **Guardar**.

## <a name="configure-migration-settings"></a>Configurar as definições da migração

1. No ecrã **Configurar as definições da migração**, indique os detalhes seguintes:

    | | |
    |--------|---------|
    |**Escolher a opção de cópia de segurança de origem** | Escolha a opção **Vou disponibilizar os ficheiros de cópia de segurança mais recentes** se já tiver ficheiros de cópia de segurança completa disponíveis para o DMS utilizar para migrar a base de dados. Escolha a opção **O Azure Database Migration Service vai criar os ficheiros de cópia de segurança** se quiser que o DMS utilize a cópia de segurança completa da base de dados de origem e a utilize para a migração. |
    |**Partilha de localização na rede** | A rede SMB local partilha que o Serviço de Migração de Bases de Dados Azure pode levar as cópias de segurança da base de dados de origem. A conta de serviço que estiver a executar a instância do SQL Server de origem tem de ter privilégios de escrita nesta partilha de rede. Indique um FQDN ou um endereço IP do servidor na partilha de rede, como, por exemplo, “\\\servername.domainname.com\backupfolder” ou “\\\IP address\backupfolder”.|
    |**Nome de utilizador** | Certifique-se de que o utilizador do Windows tem privilégio de controlo total na partilha de rede que indicou acima. O Serviço de Migração da Base de Dados Azure personificará a credencial do utilizador para enviar os ficheiros de backup para o contentor de armazenamento Azure para restabelecer o funcionamento. Se estiverem selecionadas bases de dados ativadas para TDE para migração, o utilizador do Windows acima tem de ser a conta de administrador incorporada e o [Controlo de Conta de Utilizador](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview) tem de ser desativado, para que o Azure Database Migration Service carregue e elimine os ficheiros de certificado. |
    |**Palavra-passe** | A palavra-passe do utilizador. |
    |**Definições da conta de armazenamento** | O SAS URI que fornece ao Azure Database Migration Service acesso ao seu contentor de conta de armazenamento para o qual o serviço envia os ficheiros de backup e que é utilizado para migrar bases de dados para a base de dados Azure SQL gerida. Veja [Learn how to get the SAS URI for blob container](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) (Saiba como obter o URI da SAS para o contentor de blobs).|
    |**Definições de TDE** | Se estiver a migrar as bases de dados de origem com encriptação de dados transparentes (TDE) ativada, precisa de ter privilégios de escrita na instância gerida pela Base de Dados Azure SQL.  Selecione a subscrição na qual a Base de Dados Azure SQL geriu a instância disponibilizada a partir do menu suspenso.  Selecione a **Instância Gerida da Base de Dados SQL do Azure** de destino no menu pendente. |

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

  A janela da atividade migratória aparece, e o estado da atividade está **pendente.**

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade da migração, selecione **Atualizar**, para atualizar o ecrã.

   ![Atividade de migração em curso](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    Pode expandir ainda mais as categorias de bases de dados e inícios de sessão para monitorizar o estado da migração dos respetivos objetos de servidor.

   ![Atividade de migração em curso](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Após a conclusão da migração, selecione **Transferir relatório** para obter um relatório que liste os detalhes associados ao processo de migração.

3. Verifique se a base de dados de destino na base de dados Azure SQL gerida pelo target.

## <a name="next-steps"></a>Passos seguintes

- Para um tutorial que lhe mostre como migrar uma base de dados para uma instância gerida utilizando o comando RESTABELECIMENTO T-SQL, consulte Restaurar uma cópia de [segurança para uma instância gerida utilizando o comando de restauro](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Para obter informações sobre instância gerida, consulte [O que é um caso gerido](../sql-database/sql-database-managed-instance.md).
- Para obter informações sobre a ligação de apps a uma instância gerida, consulte [aplicações Connect](../sql-database/sql-database-managed-instance-connect-app.md).
