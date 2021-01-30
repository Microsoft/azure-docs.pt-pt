---
title: 'Tutorial: Migrar o SqL Server para a SQL Managed Instance'
titleSuffix: Azure Database Migration Service
description: Aprenda a migrar do SQL Server para um Azure SQL Managed Instance utilizando o Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 995fe7bc81b0fae75ebf93338722c21bd12bde49
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99095578"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-offline-using-dms"></a>Tutorial: Migrar o SQL Server para um Azure SQL Managed Instance offline usando DMS

Pode utilizar o Azure Database Migration Service para migrar as bases de dados de uma instância do SQL Server para uma [Instância Gerida Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md). Para métodos adicionais que possam requerer algum esforço manual, consulte o artigo [SQL Server migração para SQL Managed Instance](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md).

Neste tutorial, migra a base de **dados Adventureworks2012** de uma instância no local do SQL Server para um SQL Managed Instance utilizando o Azure Database Migration Service.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> - Crie uma instância do Azure Database Migration Service.
> - Crie um projeto de migração utilizando o Serviço de Migração da Base de Dados Azure.
> - Executar a migração.
> - Monitorizar a migração.
> - Transferir um relatório da migração.

> [!IMPORTANT]
> Para migrações offline do SQL Server para SQL Managed Instance, o Azure Database Migration Service pode criar os ficheiros de backup para si. Em alternativa, pode fornecer a mais recente cópia de segurança completa da base de dados na rede SMB que o serviço utilizará para migrar as suas bases de dados. Não apromtesse várias cópias de segurança num único meio de cópia de segurança; tome cada cópia de segurança num ficheiro de reserva separado. Note que também pode usar backups comprimidos, para reduzir a probabilidade de experimentar potenciais problemas com grandes backups migratórios.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração offline do SQL Server para uma SQL Managed Instance. Para uma migração on-line, consulte [o Migrate SQL Server para uma SQL Managed Instance on-line utilizando DMS](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

- Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-to-site aos seus servidores de origem no local, utilizando [expressRoute](../expressroute/expressroute-introduction.md) ou [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). [Aprenda as topologias de rede para migrações de instância gerida sql usando o Serviço de Migração da Base de Dados Azure.](./resource-network-topologies.md) Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação da Rede Virtual,](../virtual-network/index.yml)e especialmente os artigos quickstart com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se utilizar o ExpressRoute com o olhar de rede para a Microsoft, adicione os [seguintes pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço à sub-rede em que o serviço será abastecado:
    > - Ponto final da base de dados-alvo (por exemplo, ponto final SQL, ponto final cosmos DB, e assim por diante)
    > - Ponto final de armazenamento
    > - Ponto final do ônibus de serviço
    >
    > Esta configuração é necessária porque o Serviço de Migração da Base de Dados Azure carece de conectividade com a Internet.

- Certifique-se de que as regras do grupo de segurança da rede virtual não bloqueiam as seguintes portas de comunicação de entrada para o Serviço de Migração da Base de Dados Azure: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego NSG da rede virtual, consulte o artigo Filtrar o [tráfego da rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
- Configurar a sua Firewall do Windows para acesso ao motor de bases de dados. Veja [Windows Firewall for source database engine access](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abra o Windows Firewall para permitir que o Serviço de Migração da Base de Dados de Azure aceda ao servidor SQL de origem, que por padrão é a porta TCP 1433. Se o seu caso padrão estiver a ouvir noutra porta, adicione-o à firewall.
- Se estiver a executar várias instâncias chamadas SQL Server utilizando portas dinâmicas, pode desejar ativar o Serviço de Navegador SQL e permitir o acesso à porta UDP 1434 através das suas firewalls para que o Serviço de Migração da Base de Dados Azure possa ligar-se a uma instância nomeada no seu servidor de origem.
- Se estiver a utilizar um aparelho de firewall em frente às suas bases de dados de origem, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base de dados de origem para migração, bem como ficheiros através da porta SMB 445.
- Criar uma SQL Managed Instance seguindo os detalhes do artigo [Criar uma SqL Managed Instance no portal Azure](../azure-sql/managed-instance/instance-create-quickstart.md).
- Certifique-se de que os logins utilizados para ligar o sql server de origem e o alvo SQL Managed Instance são membros da função do servidor sysadmin.

    >[!NOTE]
    >Por padrão, o Azure Database Migration Service suporta apenas logins SQL migratórios. No entanto, pode ativar a capacidade de migrar os logins do Windows através de:
    >
    >- Garantindo que o target SQL Managed Instance tem acesso de leitura AAD, que pode ser configurado através do portal Azure por um utilizador com a função **de Administrador Global.**
    >- Configurar a sua instância do Serviço de Migração de Bases de Dados Azure para permitir migrações de login de utilizador/grupo do Windows, que é configurada através do portal Azure, na página De Configuração. Depois de ativar esta definição, reinicie o serviço para que as alterações entrem em vigor.
    >
    > Depois de reiniciar o serviço, os logins de utilizador/grupo do Windows aparecem na lista de logins disponíveis para migração. Para quaisquer logins de utilizador/grupo do Windows que migrar, é solicitado que forneça o nome de domínio associado. As contas de utilizadores de serviço (conta com nome de domínio NT AUTHORITY) e contas de utilizador virtuais (nome de conta com nome de domínio NT SERVICE) não são suportadas.

- Crie uma partilha de rede que o Azure Database Migration Service pode usar para fazer o back-up da base de dados de origem.
- Confirme que a conta de serviço em execução na instância do SQL Server de origem tem privilégios de escrita na partilha de rede que criou e que a conta do computador do servidor de origem tem acesso de leitura/escrita à mesma partilha.
- Tome nota de um utilizador do Windows (e da palavra-passe) que tenha privilégio de controlo total na partilha de rede que criou anteriormente. O Serviço de Migração da Base de Dados Azure personifica a credencial do utilizador para enviar os ficheiros de cópia de segurança para o contentor de armazenamento Azure para restaurar o funcionamento.
- Crie um contentor de blobs e obtenha o URI da SAS com os passos no artigo [Manage Azure Blob Storage resources with Storage Explorer](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container) (Gerir recursos do Armazenamento de Blobs do Azure com o Explorador de Armazenamento). Confirme que seleciona todas as permissões (Leitura, Escrita, Eliminação, Listagem) na janela da política quando criar o URI da SAS. Este detalhe fornece ao Serviço de Migração da Base de Dados de Azure acesso ao seu contentor de conta de armazenamento para o upload dos ficheiros de backup utilizados para bases de dados migratórias para a SQL Managed Instance.

    > [!NOTE]
    > O Serviço de Migração de Bases de Dados Azure não suporta a utilização de um token SAS de nível de conta ao configurar as definições da Conta de Armazenamento durante a etapa [configurações de configuração de configurações de configuração.](#configure-migration-settings)
    
## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

    ![Mostrar subscrições no portal](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores de Recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Criar uma instância do Azure Database Migration Service

1. No portal Azure, selecione + **Crie um recurso,** procure o **Serviço de Migração da Base de Dados Azure** e, em seguida, selecione O Serviço de **Migração da Base de Dados Azure** a partir da lista de espera.

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização em que pretende criar a instância do DMS.

5. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece ao Azure Database Migration Service acesso ao servidor SQL de origem e ao alvo SQL Managed Instance.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure](../virtual-network/quick-create-portal.md).

    Para mais detalhes, consulte as topologias da rede para as migrações de [instância gerida do Azure SQL utilizando o Serviço de Migração da Base de Dados Azure](./resource-network-topologies.md).

6. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Criar o serviço DMS](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação de uma instância do serviço, localize-a no portal do Azure, abra-a e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

    ![Localizar todas as instâncias do Serviço de Migração da Base de Dados de Azure](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. No ecrã do **Serviço de Migração da Base de Dados Azure,** procure o nome do caso que criou e, em seguida, selecione o caso.

3. Selecione + **Novo Projeto de Migração**.

4. No novo ecrã do **projeto de migração,** especifique um nome para o projeto, na caixa de texto **do tipo do servidor Fonte,** selecione **SQL Server**, na caixa de texto do tipo do servidor **Target,** selecione **Azure SQL Managed Instance**, e, em seguida, para Escolher tipo de atividade , selecione migração **de** **dados offline**.

   ![Criar o projeto do DMS](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Selecione **Create** (Criar) para criar o projeto.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã **Detalhes da origem da migração**, especifique os detalhes da ligação do SQL Server de origem.

2. Se não tiver um certificado fidedigno instalado no seu servidor, selecione a caixa de verificação **Confiar no certificado de servidor**.

    Se não tiver um certificado fidedigno instalado, o SQL Server gera um certificado autoassinado quando a instância é iniciada. Este certificado é utilizado para encriptar as credenciais para as ligações de cliente.

    > [!CAUTION]
    > As ligações TLS que são encriptadas usando um certificado auto-assinado não proporcionam uma segurança forte. São suscetíveis a ataques man-in-the-middle. Não deve confiar no TLS utilizando certificados auto-assinados num ambiente de produção ou em servidores ligados à internet.

   ![Detalhes da origem](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Selecione **Guardar**.

4. No ecrã **Selecionar bases de dados de origem**, selecione a base de dados **Adventureworks2012** para migração.

   ![Selecionar as bases de dados de origem](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Se utilizar os Serviços de Integração de Servidores SQL (SSIS), o DMS não suporta atualmente a migração da base de dados do catálogo para os seus projetos/pacotes SSIS (SSISDB) do SQL Server para o SQL Managed Instance. No entanto, pode prospeccionar o SSIS na Azure Data Factory (ADF) e recolocar os seus projetos/pacotes SSIS para o destino SSISDB hospedado pela SQL Managed Instance. Para obter mais informações sobre os pacotes SSIS migratórios, consulte o artigo [Pacotes de Serviços de Integração de Servidores SQL migratórios para o Azure](./how-to-migrate-ssis-packages.md).

5. Selecione **Guardar**.

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. No ecrã de **detalhes do destino migratório,** especifique os detalhes de ligação para o alvo, que é a primeira instância gerida do SQL para a qual está a migrar a base de dados **AdventureWorks2012.**

    Se ainda não a provisionou a SQL Managed Instance, selecione o [link](../azure-sql/managed-instance/instance-create-quickstart.md) para o ajudar a providenciar o caso. Você ainda pode continuar com a criação de projetos e, em seguida, quando o SQL Managed Instance está pronto, voltar a este projeto específico para executar a migração.

    ![Selecionar o Destino](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Selecione **Guardar**.

## <a name="select-source-databases"></a>Selecionar as bases de dados de origem

1. No ecrã **Selecionar as base de dados de origem**, selecione a base de dados de origem que quer migrar.

    ![Selecionar as bases de dados de origem](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Selecione **Guardar**.

## <a name="select-logins"></a>Selecionar os inícios de sessão

1. No ecrã **Selecionar os inícios de sessão**, selecione os inícios de sessão que quer migrar.

    >[!NOTE]
    >Por padrão, o Azure Database Migration Service suporta apenas logins SQL migratórios. Para ativar o suporte para a migração de logins do Windows, consulte a secção **Pré-Requisitos** deste tutorial.

    ![Selecionar os inícios de sessão](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Selecione **Guardar**.

## <a name="configure-migration-settings"></a>Configurar as definições da migração

1. No ecrã **Configurar as definições da migração**, indique os detalhes seguintes:

    | | |
    |--------|---------|
    |**Escolher a opção de cópia de segurança de origem** | Escolha a opção **Vou disponibilizar os ficheiros de cópia de segurança mais recentes** se já tiver ficheiros de cópia de segurança completa disponíveis para o DMS utilizar para migrar a base de dados. Escolha a opção **O Azure Database Migration Service vai criar os ficheiros de cópia de segurança** se quiser que o DMS utilize a cópia de segurança completa da base de dados de origem e a utilize para a migração. |
    |**Partilha de localização na rede** | A rede SMB local partilha que o Azure Database Migration Service pode levar as cópias de dados de base de dados de origem para. A conta de serviço que estiver a executar a instância do SQL Server de origem tem de ter privilégios de escrita nesta partilha de rede. Indique um FQDN ou um endereço IP do servidor na partilha de rede, como, por exemplo, “\\\servername.domainname.com\backupfolder” ou “\\\IP address\backupfolder”.|
    |**Nome de utilizador** | Certifique-se de que o utilizador do Windows tem privilégio de controlo total na partilha de rede que indicou acima. O Serviço de Migração da Base de Dados Azure personificará a credencial do utilizador para enviar os ficheiros de cópia de segurança para o contentor de armazenamento Azure para restaurar o funcionamento. Se estiverem selecionadas bases de dados ativadas para TDE para migração, o utilizador do Windows acima tem de ser a conta de administrador incorporada e o [Controlo de Conta de Utilizador](/windows/security/identity-protection/user-account-control/user-account-control-overview) tem de ser desativado, para que o Azure Database Migration Service carregue e elimine os ficheiros de certificado. |
    |**Palavra-passe** | A palavra-passe do utilizador. |
    |**Definições da conta de armazenamento** | O SAS URI que fornece ao Serviço de Migração da Base de Dados de Azure acesso ao seu contentor de conta de armazenamento para o qual o serviço envia os ficheiros de backup e que é utilizado para migrar bases de dados para SQL Managed Instance. Veja [Learn how to get the SAS URI for blob container](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container) (Saiba como obter o URI da SAS para o contentor de blobs). Este SAS URI deve ser para o recipiente blob, não para a conta de armazenamento.|
    |**Definições de TDE** | Se estiver a migrar as bases de dados de origem com encriptação de dados transparente (TDE) ativada, precisa de ter privilégios de escrita no exemplo de gestão sql alvo.  Selecione a subscrição na qual o SQL Managed Instance adestamos no menu suspenso.  Selecione a **Instância Gerida da Base de Dados SQL do Azure** de destino no menu pendente. |

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

  A janela de atividade de migração aparece, e o estado da atividade está **pendente**.

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade da migração, selecione **Atualizar**, para atualizar o ecrã.

   ![Screenshot que mostra o ecrã de atividade de migração e o botão Refresh.](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    Pode expandir ainda mais as categorias de bases de dados e inícios de sessão para monitorizar o estado da migração dos respetivos objetos de servidor.

   ![Atividade de migração em curso](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Após a conclusão da migração, selecione **Transferir relatório** para obter um relatório que liste os detalhes associados ao processo de migração.

3. Verifique se a base de dados-alvo no ambiente de exemplo gerido sql alvo.

## <a name="next-steps"></a>Passos seguintes

- Para um tutorial que lhe mostre como migrar uma base de dados para SQL Managed Instance utilizando o comando T-SQL RESTORE, consulte [Restaurar uma cópia de segurança para SQL Managed Instance utilizando o comando de restauro](../azure-sql/managed-instance/restore-sample-database-quickstart.md).
- Para obter informações sobre a SQL Managed Instance, consulte [o que é a sql Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).
- Para obter informações sobre a ligação de aplicações a SQL Managed Instance, consulte [aplicações Connect](../azure-sql/managed-instance/connect-application-instance.md).