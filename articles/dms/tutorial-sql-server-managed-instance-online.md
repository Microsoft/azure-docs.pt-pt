---
title: 'Tutorial: Migrar o Servidor SQL on-line para uma instância gerida pela SQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a realizar uma migração on-line do SQL Server no local para uma base de dados Azure SQL gerida através do Serviço de Migração de Bases de Dados Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/10/2020
ms.openlocfilehash: 236c68b3c26049073d3e6e942ce2a6be8b7f4fde
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80298893"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Tutorial: Migrar o Servidor SQL para uma base de dados Azure SQL gerida online usando DMS

Pode utilizar o Serviço de Migração de Bases de Dados Azure para migrar as bases de dados de uma instância do SQL Server no local para uma base de [dados Azure SQL gerida](../sql-database/sql-database-managed-instance.md) com o mínimo de tempo de inatividade. Para obter métodos adicionais que possam exigir algum esforço manual, consulte o artigo [SQL Server instância migração para a base de dados Azure SQL gerida](../sql-database/sql-database-managed-instance-migrate.md).

Neste tutorial, migra a base de dados **Adventureworks2012** de uma instância no local do SQL Server para uma Base de Dados SQL gerida com o mínimo de tempo de inatividade utilizando o Serviço de Migração de Bases de Dados Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Crie uma instância do Azure Database Migration Service.
> * Crie um projeto de migração e inicie a migração online utilizando o Serviço de Migração de Bases de Dados Azure.
> * Monitorizar a migração.
> * Execute o corte de migração quando estiver pronto.

> [!IMPORTANT]
> Para migrações on-line do SQL Server para a SQL Database gerida sem recurso ao Serviço de Migração de Bases de Dados Azure, deve fornecer a cópia de segurança completa da base de dados e posteriores cópias de segurança de registo na partilha da rede SMB que o serviço pode usar para migrar as suas bases de dados. O Serviço de Migração de Bases de Dados Azure não inicia quaisquer backups e, em vez disso, utiliza cópias de segurança existentes, que pode já ter como parte do seu plano de recuperação de desastres, para a migração.
> Certifique-se de que utiliza cópias de [segurança utilizando a opção COM CHECKSUM](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017). Além disso, certifique-se de não anexar várias cópias de segurança (isto é, full e t-log) num único meio de backup; tome cada cópia de segurança num ficheiro de reserva separado. Finalmente, pode utilizar backups comprimidos para reduzir a probabilidade de experimentar potenciais problemas associados à migração de grandes cópias de segurança.

> [!NOTE]
> A utilização do Serviço de Migração de Bases de Dados Azure para realizar uma migração online requer a criação de uma instância baseada no nível de preços Premium.

> [!IMPORTANT]
> Para uma experiência de migração ideal, a Microsoft recomenda a criação de uma instância do Serviço de Migração de Bases de Dados Azure na mesma região do Azure que a base de dados alvo. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

> [!IMPORTANT]
> Reduza ao máximo a duração do processo de migração online para minimizar o risco de interrupção causado pela reconfiguração ou manutenção planeada. Em caso de tal acontecimento, o processo de migração começará desde o início. Em caso de manutenção planeada, há um período de carência de 36 horas antes do reinício do processo de migração.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração on-line do SQL Server para uma instância gerida pela Base de Dados SQL. Para uma migração offline, consulte [migrate SQL Server para uma Base de Dados SQL gerida por exemplo offline usando DMS](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-a-site aos seus servidores de origem no local, utilizando [expressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Aprenda topoologias de rede para a Base de Dados Azure SQL gerida por migrações de instâncias utilizando o Serviço](https://aka.ms/dmsnetworkformi)de Migração de Bases de Dados Azure . Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação](https://docs.microsoft.com/azure/virtual-network/)da Rede Virtual , e especialmente os artigos quickstart com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se utilizar o ExpressRoute com o peering da rede para a Microsoft, adicione os [seguintes pontos finais](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à subnet na qual o serviço será aprovisionado:
    >
    > * Ponto final da base de dados alvo (por exemplo, ponto final SQL, ponto final cosmos DB, e assim por diante)
    > * Ponto final de armazenamento
    > * Ponto final do ônibus de serviço
    >
    > Esta configuração é necessária porque o Serviço de Migração de Bases de Dados Azure carece de conectividade na Internet.
    >
    >Se não tiver conectividade site-a-site entre a rede no local e o Azure ou se houver uma largura de banda de conectividade de ligação local-local limitada, considere utilizar o Serviço de Migração de Bases de Dados Azure em modo híbrido (Pré-visualização). O modo híbrido aproveita um trabalhador migratório no local juntamente com uma instância do Serviço de Migração de Bases de Dados Azure que funciona na nuvem. Para criar uma instância do Serviço de Migração de Bases de Dados Azure em modo híbrido, consulte o artigo Criar uma instância do Serviço de Migração de Bases [de Dados Azure em modo híbrido utilizando o portal Azure](https://aka.ms/dms-hybrid-create).

    > [!IMPORTANT]
    > No que diz respeito à conta de armazenamento utilizada como parte da migração, deve:
    > * Opte por permitir que toda a rede aceda à conta de armazenamento.
    > * Ligue a delegação de [sub-rede](https://docs.microsoft.com/azure/virtual-network/manage-subnet-delegation) na sub-rede MI e atualize as regras de firewall da Conta de Armazenamento para permitir esta sub-rede.

* Certifique-se de que as regras do Grupo de Segurança da Rede Virtual não bloqueiam as seguintes portas de comunicação de entrada para o Serviço de Migração de Bases de Dados Azure: 443, 53, 9354, 445, 12000. Para mais detalhes sobre a filtragem de tráfego da rede virtual NSG, consulte o artigo Filtrar o tráfego da [rede com grupos](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)de segurança da rede .
* Configurar a sua Firewall do Windows para acesso ao motor de bases de dados. Veja [Windows Firewall for source database engine access](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra o seu Windows Firewall para permitir que o Serviço de Migração de Bases de Dados Azure aceda à fonte Do Servidor SQL, que por padrão é a porta TCP 1433.
* Se estiver a executar várias instâncias de Servidor SQL com portas dinâmicas, poderá desejar ativar o Serviço de Navegador SQL e permitir o acesso à porta UDP 1434 através das suas firewalls para que o Serviço de Migração da Base de Dados Azure possa ligar-se a uma instância nomeada no seu servidor de origem.
* Se estiver a utilizar um aparelho de firewall em frente às bases de dados de origem, poderá ter de adicionar regras de firewall para permitir que o Serviço de Migração de Bases de Dados Azure aceda à base de dados de origem para migração, bem como ficheiros através da porta SMB 445.
* Crie uma instância gerida pela Base de Dados SQL seguindo os detalhes do artigo Criar uma base de [dados Azure SQL gerida no portal Azure](https://aka.ms/sqldbmi).
* Confirme que os inícios de sessão utilizados para ligar o SQL Server de origem e a instância gerida de destino são membros da função de servidor sysadmin.
* Forneça uma partilha de rede SMB que contenha todos os ficheiros de backup completos da base de dados da base de dados e ficheiros de cópia de segurança subsequentes do registo de transações, que o Serviço de Migração de Bases de Dados Azure pode utilizar para a migração da base de dados.
* Confirme que a conta de serviço em execução na instância do SQL Server de origem tem privilégios de escrita na partilha de rede que criou e que a conta do computador do servidor de origem tem acesso de leitura/escrita à mesma partilha.
* Tome nota de um utilizador do Windows (e da palavra-passe) que tenha privilégio de controlo total na partilha de rede que criou anteriormente. O Serviço de Migração da Base de Dados Azure personifica a credencial do utilizador para enviar os ficheiros de backup para o contentor de armazenamento Azure para restabelecer o funcionamento.
* Crie um ID de aplicação de diretório ativo Azure que gere a chave de ID de aplicação que o Serviço de Migração de Bases de Dados Azure pode usar para se ligar à instância gerida pela Azure Database e ao Contentor de Armazenamento Azure. Para obter mais informações, veja o artigo [Utilizar o portal para criar uma aplicação e um principal de serviço do Azure Active Directory que possam aceder aos recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

  > [!NOTE]
  > O Serviço de Migração da Base de Dados Azure requer a autorização do Contribuinte na subscrição do ID de aplicação especificado. Em alternativa, pode criar funções personalizadas que concedam as permissões específicas que o Serviço de Migração de Bases de Dados Azure necessita. Para obter orientações passo a passo sobre a utilização de papéis personalizados, consulte o artigo [Papéis personalizados para o SQL Server para a Base de Dados SQL gerida por migrações online](https://docs.microsoft.com/azure/dms/resource-custom-roles-sql-db-managed-instance).

* Crie ou anote o **escalão de Desempenho Standard**, a Conta de Armazenamento do Azure, que permite ao serviço DMS carregar os ficheiros de cópia de segurança para a base de dados e utilizá-la para a migração de bases de dados.  Certifique-se de criar a Conta de Armazenamento Azure na mesma região que a instância do Serviço de Migração de Bases de Dados Azure.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

    ![Mostrar subscrições no portal](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores**de Recursos .

    ![Mostrar fornecedores de recursos](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Criar uma instância do Azure Database Migration Service

1. No portal Azure, selecione + **Crie um recurso,** procure o Serviço de Migração de Bases de **Dados Azure**e, em seguida, selecione O Serviço de Migração de Bases de **Dados Azure** da lista de drop-down.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização em que pretende criar a instância do DMS.

5. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece ao Azure Database Migration Service acesso à fonte SQL Server e ao target SQL Database gerido.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure](https://aka.ms/DMSVnet).

    Para mais detalhes, consulte o artigo [Topoologias de rede para base de dados Azure SQL geridas por migrações de instânciautilizando o Serviço](https://aka.ms/dmsnetworkformi)de Migração de Bases de Dados Azure .

6. Selecione um SKU do nível de preços Premium.

    > [!NOTE]
    > As migrações online só são suportadas quando se utiliza o nível Premium.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Criar o serviço DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação de uma instância do serviço, localize-a no portal do Azure, abra-a e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

    ![Localize todas as instâncias do Serviço de Migração da Base de Dados Azure](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. No ecrã do Serviço de Migração de Bases de **Dados Azure,** procure o nome da instância que criou e, em seguida, selecione a instância.

3. Selecione + **Novo Projeto de Migração**.

4. No ecrã do **novo projeto de migração,** especifique um nome para o projeto, na caixa de texto **tipo servidor Fonte,** selecione **SQL Server**, na caixa de texto do tipo servidor **Target,** selecione **Azure SQL Database Managed Instance**, e depois para Escolher tipo de **atividade,** selecione **migração**de dados online .

   ![Criar projeto de serviço de migração de bases de dados Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Selecione **Criar e executar atividade** para criar o projeto.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã **Detalhes da origem da migração**, especifique os detalhes da ligação do SQL Server de origem.

2. Se não tiver um certificado fidedigno instalado no seu servidor, selecione a caixa de verificação **Confiar no certificado de servidor**.

    Se não tiver um certificado fidedigno instalado, o SQL Server gera um certificado autoassinado quando a instância é iniciada. Este certificado é utilizado para encriptar as credenciais para as ligações de cliente.

    > [!CAUTION]
    > As ligações TLS que são encriptadas utilizando um certificado auto-assinado não proporcionam uma segurança forte. São suscetíveis a ataques man-in-the-middle. Não deve confiar no TLS utilizando certificados auto-assinados num ambiente de produção ou em servidores ligados à internet.

   ![Detalhes da origem](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Selecione **Guardar**.

4. No ecrã **Selecionar bases de dados de origem**, selecione a base de dados **Adventureworks2012** para migração.

   ![Selecionar as bases de dados de origem](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Se utilizar os Serviços de Integração do Servidor SQL (SSIS), o DMS não suporta atualmente a migração da base de dados de catálogo para os seus projetos/pacotes SSIS (SSISDB) do SQL Server para o Azure SQL Database gerido. No entanto, pode fornecer SSIS na Azure Data Factory (ADF) e reimplantar os seus projetos/pacotes SSIS para o destino SSISDB hospedado pela Azure SQL Database gerida pela instância gerida. Para obter mais informações sobre pacotes SSIS migratórios, consulte o artigo Pacotes de Serviços de Integração de [Servidores SQL para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Selecione **Guardar**.

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. No ecrã de dados do **alvo da Migração,** especifique o ID de **aplicação** e **a chave** que a instância DMS pode usar para se ligar à instância-alvo da instância gerida pela Base de Dados Azure SQL e à Conta de Armazenamento Azure.

    Para obter mais informações, veja o artigo [Utilizar o portal para criar uma aplicação e um principal de serviço do Azure Active Directory que possam aceder aos recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Selecione a **subscrição** contendo a instância-alvo da base de dados Azure SQL gerida e, em seguida, selecione a instância-alvo.

    Se ainda não disponibilizou a instância gerida pela Base de Dados Azure SQL, selecione o [link](https://aka.ms/SQLDBMI) para o ajudar a fornecer a instância. Quando a base de dados Azure SQL estiver pronta, volte a este projeto específico para executar a migração.

3. Forneça ao Utilizador E **Palavra-Passe** **SQL** para se ligar à instância gerida pela Base de Dados SQL do Azure.

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
    |**Partilha de localização na Rede SMB** | A partilha de rede SMB local ou a partilha de ficheiros Azure que contém os ficheiros de backup da base de dados completas e ficheiros de cópia de segurança de registo de transações que o Azure Database Migration Service pode usar para a migração. A conta de serviço que estiver a executar a instância do SQL Server de origem tem de ter privilégios de leitura\escrita nesta partilha de rede. Indique um FQDN ou um endereço IP do servidor na partilha de rede, como, por exemplo, “\\\servername.domainname.com\backupfolder” ou “\\\IP address\backupfolder”. Para um melhor desempenho, recomenda-se a utilização de pasta separada para que cada base de dados seja migrada. Pode fornecer o caminho de partilha de ficheiros de nível de base de dados utilizando a opção **Definições Avançadas.** |
    |**Nome de utilizador** | Certifique-se de que o utilizador do Windows tem privilégio de controlo total na partilha de rede que indicou acima. O Serviço de Migração da Base de Dados Azure personificará a credencial do utilizador para enviar os ficheiros de backup para o contentor de armazenamento Azure para restabelecer o funcionamento. Se utilizar a parte do Ficheiro Azure, utilize o nome da conta de armazenamento pré-pregado com o AZURE\ como nome de utilizador. |
    |**Palavra-passe** | A palavra-passe do utilizador. Se utilizar a partilha de ficheiros Azure, utilize uma chave de conta de armazenamento como palavra-passe. |
    |**Subscrição da Conta de Armazenamento do Azure** | Selecione a subscrição que contém a Conta de Armazenamento do Azure. |
    |**Conta de Armazenamento Azure** | Selecione a Conta de Armazenamento do Azure para a qual o DMS pode carregar os ficheiros de cópia de segurança da partilha de rede SMB e utilizá-los para a migração de base de dados.  Recomendamos que selecione a Conta de Armazenamento na mesma região do serviço DMS para um desempenho de carregamento de ficheiros ideal. |

    ![Configurar as Definições da Migração](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Se o Serviço de Migração da Base de Dados Azure mostrar erro 'Erro de sistema 53' ou 'Erro de sistema 57', a causa pode resultar de uma incapacidade do Serviço de Migração de Bases de Dados Azure para aceder à partilha de ficheiros Azure. Se encontrar um destes erros, por favor, conceda acesso à conta de armazenamento da rede virtual utilizando as instruções [aqui .](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)

    > [!IMPORTANT]
    > Se a funcionalidade de verificação de loopback estiver ativada e a fonte Do Servidor SQL e a partilha de ficheiros estiverem no mesmo computador, então a fonte não poderá aceder à lebre de ficheiros utilizando o FQDN. Para corrigir este problema, desative a funcionalidade de verificação de volta utilizando as instruções [aqui .](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd)

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

Após a cópia de segurança da base de dados completa ser restaurada na instância-alvo da instância gerida pela Base de Dados SQL, a base de dados está disponível para a realização de um corte de migração.

1. Quando estiver pronto para concluir a migração da base de dados online, selecione **Iniciar Transferência**.

2. Pare todo o tráfego de entrada para as bases de dados de origem.

3. Com a [cópia de segurança de registo após erro], disponibilize o ficheiro de cópia de segurança na rede de partilha SMB e, em seguida, aguarde até que a cópia de segurança de registo de transações seja restaurada.

    Nesse momento, verá que as **Alterações pendentes** estão definidas como 0.

4. Selecione **Confirmar** e, em seguida, selecione **Aplicar**.

    ![Preparar para concluir transferência](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Quando o estado de migração da base de dados mostrar **concluído,** ligue as suas aplicações à nova instância-alvo gerida pela Base de Dados Azure SQL.

    ![Transferência concluída](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Passos seguintes

* Para um tutorial que lhe mostre como migrar uma base de dados para uma instância gerida utilizando o comando RESTABELECIMENTO T-SQL, consulte Restaurar uma cópia de [segurança para uma instância gerida utilizando o comando de restauro](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
* Para obter informações sobre instância gerida, consulte [O que é um caso gerido](../sql-database/sql-database-managed-instance.md).
* Para obter informações sobre a ligação de apps a uma instância gerida, consulte [aplicações Connect](../sql-database/sql-database-managed-instance-connect-app.md).
