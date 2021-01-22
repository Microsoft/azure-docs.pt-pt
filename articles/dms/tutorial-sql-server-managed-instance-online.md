---
title: 'Tutorial: Migrar o SQL Server online para a SQL Managed Instance'
titleSuffix: Azure Database Migration Service
description: Aprenda a realizar uma migração on-line do SQL Server para um Azure SQL Managed Instance utilizando o Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 08/04/2020
ms.openlocfilehash: 02adb6f47b907fea402f8b312b3f4e8e117927ed
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98693685"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-online-using-dms"></a>Tutorial: Migrar o SQL Server para um Azure SQL Managed Instance on-line usando DMS

Pode utilizar o Azure Database Migration Service para migrar as bases de dados de uma instância do SQL Server para uma [Instância Gerida Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) com tempo de inatividade mínimo. Para métodos adicionais que possam requerer algum esforço manual, consulte o artigo [SQL Server migração para Azure SQL Managed Instance](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md).

Neste tutorial, migra a base de dados **Adventureworks2012** de uma instância no local do SQL Server para uma SQL Managed Instance com tempo mínimo de inatividade utilizando o Azure Database Migration Service.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Crie uma instância do Azure Database Migration Service.
> * Crie um projeto de migração e inicie a migração online utilizando o Azure Database Migration Service.
> * Monitorizar a migração.
> * Execute o corte de migração quando estiver pronto.

> [!IMPORTANT]
> Para migrações on-line do SQL Server para SQL Managed Instance utilizando o Azure Database Migration Service, deve fornecer a cópia de segurança completa da base de dados e subsequentes cópias de segurança na rede SMB que o serviço pode usar para migrar as suas bases de dados. O Azure Database Migration Service não inicia quaisquer backups e, em vez disso, utiliza cópias de segurança existentes, que já pode ter como parte do seu plano de recuperação de desastres, para a migração.
> Certifique-se de que aceita [cópias de segurança utilizando a opção COM CHECKSUM](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?preserve-view=true&view=sql-server-2017). Além disso, certifique-se de não anexar várias cópias de segurança (isto é, completas e t-log) num único meio de cópia de segurança; tome cada cópia de segurança num ficheiro de reserva separado. Finalmente, pode utilizar backups comprimidos para reduzir a probabilidade de experimentar potenciais problemas associados à migração de grandes backups.

> [!NOTE]
> A utilização do Azure Database Migration Service para realizar uma migração online requer a criação de um caso baseado no nível de preços Premium.

> [!IMPORTANT]
> Para uma experiência de migração ideal, a Microsoft recomenda a criação de um exemplo do Azure Database Migration Service na mesma região Azure que a base de dados-alvo. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

> [!IMPORTANT]
> Reduza ao máximo a duração do processo de migração online para minimizar o risco de interrupção causado por reconfiguração por exemplo ou manutenção planeada. Em caso de tal acontecimento, o processo de migração começará desde o início. Em caso de manutenção planeada, há um período de carência de 36 horas antes do reinício do processo de migração.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração on-line do SQL Server para uma SQL Managed Instance. Para uma migração offline, consulte [o Servidor SQL migrar para um SQL Managed Instance offline utilizando DMS](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-to-site aos seus servidores de origem no local, utilizando [expressRoute](../expressroute/expressroute-introduction.md) ou [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). [Aprenda as topologias de rede para migrações de instância gerida sql usando o Serviço de Migração da Base de Dados Azure.](./resource-network-topologies.md) Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação da Rede Virtual,](../virtual-network/index.yml)e especialmente os artigos quickstart com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se utilizar o ExpressRoute com o olhar de rede para a Microsoft, adicione os [seguintes pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço à sub-rede em que o serviço será abastecado:
    >
    > * Ponto final da base de dados-alvo (por exemplo, ponto final SQL, ponto final cosmos DB, e assim por diante)
    > * Ponto final de armazenamento
    > * Ponto final do ônibus de serviço
    >
    > Esta configuração é necessária porque o Serviço de Migração da Base de Dados Azure carece de conectividade com a Internet.
    >
    >Se não tiver conectividade site-to-site entre a rede no local e a Azure ou se houver uma largura de banda de conectividade local-local limitada, considere a utilização do Serviço de Migração da Base de Dados Azure em modo híbrido (Preview). O modo híbrido aproveita um trabalhador de migração no local, juntamente com um exemplo do Serviço de Migração da Base de Dados Azure a funcionar na nuvem. Para criar um exemplo do Serviço de Migração da Base de Dados Azure em modo híbrido, consulte o artigo [Criar uma instância do Serviço de Migração da Base de Dados Azure em modo híbrido utilizando o portal Azure](./quickstart-create-data-migration-service-portal.md).

    > [!IMPORTANT]
    > No que diz respeito à conta de armazenamento utilizada como parte da migração, deve:
    > * Opte por permitir que toda a rede aceda à conta de armazenamento.
    > * Ligue a [delegação de sub-redes](../virtual-network/manage-subnet-delegation.md) na sub-rede MI e atualize as regras de firewall da Conta de Armazenamento para permitir esta sub-rede.

* Certifique-se de que as regras do grupo de segurança da rede virtual não bloqueiam as seguintes portas de comunicação de saída para o Serviço de Migração da Base de Dados Azure: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego NSG da rede virtual, consulte o artigo Filtrar o [tráfego da rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Configurar a sua Firewall do Windows para acesso ao motor de bases de dados. Veja [Windows Firewall for source database engine access](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra o Windows Firewall para permitir que o Serviço de Migração da Base de Dados de Azure aceda ao servidor SQL de origem, que por padrão é a porta TCP 1433. Se o seu caso padrão estiver a ouvir noutra porta, adicione-o à firewall.
* Se estiver a executar várias instâncias chamadas SQL Server utilizando portas dinâmicas, pode desejar ativar o Serviço de Navegador SQL e permitir o acesso à porta UDP 1434 através das suas firewalls para que o Serviço de Migração da Base de Dados Azure possa ligar-se a uma instância nomeada no seu servidor de origem.
* Se estiver a utilizar um aparelho de firewall em frente às suas bases de dados de origem, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base de dados de origem para migração, bem como ficheiros através da porta SMB 445.
* Criar uma SQL Managed Instance seguindo os detalhes do artigo [Criar uma SqL Managed Instance no portal Azure](../azure-sql/managed-instance/instance-create-quickstart.md).
* Certifique-se de que os logins utilizados para ligar o SqL Server de origem e o alvo SQL Managed Instance são membros da função do servidor sysadmin.
* Forneça uma partilha de rede SMB que contenha todos os ficheiros de backup completos da sua base de dados e ficheiros de cópias de segurança subsequentes do registo de transações, que o Azure Database Migration Service pode utilizar para a migração de bases de dados.
* Confirme que a conta de serviço em execução na instância do SQL Server de origem tem privilégios de escrita na partilha de rede que criou e que a conta do computador do servidor de origem tem acesso de leitura/escrita à mesma partilha.
* Tome nota de um utilizador do Windows (e da palavra-passe) que tenha privilégio de controlo total na partilha de rede que criou anteriormente. O Serviço de Migração da Base de Dados Azure personifica a credencial do utilizador para enviar os ficheiros de cópia de segurança para o contentor de armazenamento Azure para restaurar o funcionamento.
* Crie um ID de aplicação de diretório ativo Azure que gere a chave de ID de aplicação que o Azure Database Migration Service pode usar para ligar para atingir o target Azure Database Managed Instance e O Azure Storage Container. Para obter mais informações, veja o artigo [Utilizar o portal para criar uma aplicação e um principal de serviço do Azure Active Directory que possam aceder aos recursos](../active-directory/develop/howto-create-service-principal-portal.md).

  > [!NOTE]
  > O Serviço de Migração da Base de Dados Azure requer a permissão do Contribuinte na subscrição do ID de aplicação especificado. Em alternativa, pode criar funções personalizadas que concedam as permissões específicas que o Serviço de Migração da Base de Dados Azure necessita. Para obter orientações passo a passo sobre a utilização de funções personalizadas, consulte o artigo [Funções personalizadas para SQL Server para SQL Managed Instance migrações on-line](./resource-custom-roles-sql-db-managed-instance.md).

* Crie ou anote o **escalão de Desempenho Standard**, a Conta de Armazenamento do Azure, que permite ao serviço DMS carregar os ficheiros de cópia de segurança para a base de dados e utilizá-la para a migração de bases de dados.  Certifique-se de criar a Conta de Armazenamento Azure na mesma região que o caso do Serviço de Migração da Base de Dados Azure.

  > [!NOTE]
  > Quando migrar uma base de dados protegida pela [Encriptação de Dados Transparente](../azure-sql/database/transparent-data-encryption-tde-overview.md) para um caso gerido através da migração online, o certificado correspondente a partir do local ou da instância do Servidor SQL Azure VM deve ser migrado antes da restauração da base de dados. Para obter etapas detalhadas, consulte [Migrar um certificado TDE para um caso gerido](../azure-sql/database/transparent-data-encryption-tde-overview.md).

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

    ![Mostrar subscrições no portal](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores de Recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Criar uma instância do Azure Database Migration Service

1. No portal Azure, selecione + **Crie um recurso,** procure o **Serviço de Migração da Base de Dados Azure** e, em seguida, selecione O Serviço de **Migração da Base de Dados Azure** a partir da lista de espera.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização em que pretende criar a instância do DMS.

5. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece ao Azure Database Migration Service acesso ao servidor SQL de origem e ao alvo SQL Managed Instance.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure](../virtual-network/quick-create-portal.md).

    Para mais detalhes, consulte as [topologias da rede para migrações de casos geridos SQL utilizando o Serviço de Migração da Base de Dados Azure](./resource-network-topologies.md).

6. Selecione um SKU a partir do nível de preços Premium.

    > [!NOTE]
    > As migrações online só são suportadas quando se utiliza o nível Premium.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Criar o serviço DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação de uma instância do serviço, localize-a no portal do Azure, abra-a e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

    ![Localizar todas as instâncias do Serviço de Migração da Base de Dados de Azure](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. No ecrã do **Serviço de Migração da Base de Dados Azure,** procure o nome do caso que criou e, em seguida, selecione o caso.

3. Selecione + **Novo Projeto de Migração**.

4. No novo ecrã do **projeto de migração,** especifique um nome para o projeto, na caixa de texto **do tipo servidor Fonte,** selecione **SQL Server**, na caixa de texto do tipo do servidor **Target,** selecione **Azure SQL Managed Instance**, e, em seguida, para Escolher tipo de atividade , selecione migração **de** **dados online**.

   ![Criar projeto de serviço de migração de bases de dados Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Selecione **Criar e executar atividade** para criar o projeto.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã **Detalhes da origem da migração**, especifique os detalhes da ligação do SQL Server de origem.

2. Se não tiver um certificado fidedigno instalado no seu servidor, selecione a caixa de verificação **Confiar no certificado de servidor**.

    Se não tiver um certificado fidedigno instalado, o SQL Server gera um certificado autoassinado quando a instância é iniciada. Este certificado é utilizado para encriptar as credenciais para as ligações de cliente.

    > [!CAUTION]
    > As ligações TLS que são encriptadas usando um certificado auto-assinado não proporcionam uma segurança forte. São suscetíveis a ataques man-in-the-middle. Não deve confiar no TLS utilizando certificados auto-assinados num ambiente de produção ou em servidores ligados à internet.

   ![Detalhes da origem](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Selecione **Guardar**.

4. No ecrã **Selecionar bases de dados de origem**, selecione a base de dados **Adventureworks2012** para migração.

   ![Selecionar as bases de dados de origem](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Se utilizar os Serviços de Integração de Servidores SQL (SSIS), o DMS não suporta atualmente a migração da base de dados do catálogo para os seus projetos/pacotes SSIS (SSISDB) do SQL Server para o SQL Managed Instance. No entanto, pode prospeccionar o SSIS na Azure Data Factory (ADF) e recolocar os seus projetos/pacotes SSIS para o destino SSISDB hospedado pela SQL Managed Instance. Para obter mais informações sobre os pacotes SSIS migratórios, consulte o artigo [Pacotes de Serviços de Integração de Servidores SQL migratórios para o Azure](./how-to-migrate-ssis-packages.md).

5. Selecione **Guardar**.

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. No ecrã de **detalhes do alvo da migração,** especifique o ID e a **Chave** de **Aplicação** que a instância DMS pode usar para ligar à instância-alvo da SQL Managed Instance e da Conta de Armazenamento Azure.

    Para obter mais informações, veja o artigo [Utilizar o portal para criar uma aplicação e um principal de serviço do Azure Active Directory que possam aceder aos recursos](../active-directory/develop/howto-create-service-principal-portal.md).

2. Selecione a **Assinatura** contendo a instância-alvo de SQL Managed Instance e, em seguida, selecione a instância-alvo.

    Se ainda não a provisionou a SQL Managed Instance, selecione o [link](../azure-sql/managed-instance/instance-create-quickstart.md) para o ajudar a providenciar o caso. Quando o SQL Managed Instance estiver pronto, volte a este projeto específico para executar a migração.

3. Forneça o Utilizador e **a Palavra-Passe** **SQL** para ligar à SqL Managed Instance.

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
    |**Partilha de localização na Rede SMB** | A partilha de rede SMB local ou a partilha de ficheiros Azure que contém os ficheiros de backup de bases de dados completos e ficheiros de cópia de segurança de registo de transações que o Azure Database Migration Service pode usar para migração. A conta de serviço que estiver a executar a instância do SQL Server de origem tem de ter privilégios de leitura\escrita nesta partilha de rede. Indique um FQDN ou um endereço IP do servidor na partilha de rede, como, por exemplo, “\\\servername.domainname.com\backupfolder” ou “\\\IP address\backupfolder”. Para um melhor desempenho, recomenda-se a utilização de pastas separadas para cada base de dados a ser migrada. Pode fornecer o caminho de partilha de ficheiros de nível de base de dados utilizando a opção **Definições Avançadas.** Se estiver a detear problemas ligados à participação do SMB, consulte [a partilha do SMB](known-issues-azure-sql-db-managed-instance-online.md#smb-file-share-connectivity). |
    |**Nome de utilizador** | Certifique-se de que o utilizador do Windows tem privilégio de controlo total na partilha de rede que indicou acima. O Serviço de Migração da Base de Dados Azure personificará a credencial do utilizador para enviar os ficheiros de cópia de segurança para o contentor de armazenamento Azure para restaurar o funcionamento. Se utilizar a partilha do Ficheiro Azure, utilize o nome da conta de armazenamento pré-pendido com AZURE\ como nome de utilizador. |
    |**Palavra-passe** | A palavra-passe do utilizador. Se utilizar a partilha de ficheiros Azure, utilize uma chave de conta de armazenamento como palavra-passe. |
    |**Subscrição da Conta de Armazenamento do Azure** | Selecione a subscrição que contém a Conta de Armazenamento do Azure. |
    |**Conta de Armazenamento Azure** | Selecione a Conta de Armazenamento do Azure para a qual o DMS pode carregar os ficheiros de cópia de segurança da partilha de rede SMB e utilizá-los para a migração de base de dados.  Recomendamos que selecione a Conta de Armazenamento na mesma região do serviço DMS para um desempenho de carregamento de ficheiros ideal. |

    ![Configurar as Definições da Migração](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Se o Serviço de Migração da Base de Dados Azure apresentar erro 'Erro do Sistema 53' ou 'Erro do Sistema 57', a causa pode resultar de uma incapacidade do Serviço de Migração de Bases de Dados Azure para aceder à partilha de ficheiros Azure. Se encontrar um destes erros, por favor, conceda acesso à conta de armazenamento a partir da rede virtual utilizando as instruções [aqui](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

    > [!IMPORTANT]
    > Se a funcionalidade de verificação de loopback estiver ativada e o sql server de origem e a partilha de ficheiros estiverem no mesmo computador, então a fonte não será capaz de aceder à lebre de ficheiros usando fQDN. Para corrigir este problema, desative a funcionalidade de verificação do loopback utilizando as instruções [aqui](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd).

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

   ![Estado da atividade migratória](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Efetuar a transferência da migração

Após a recuperação completa da base de dados ser restaurada no caso-alvo de SQL Managed Instance, a base de dados está disponível para a realização de um corte de migração.

1. Quando estiver pronto para concluir a migração da base de dados online, selecione **Iniciar Transferência**.

2. Pare todo o tráfego de entrada para as bases de dados de origem.

3. Com a [cópia de segurança de registo após erro], disponibilize o ficheiro de cópia de segurança na rede de partilha SMB e, em seguida, aguarde até que a cópia de segurança de registo de transações seja restaurada.

    Nesse momento, verá que as **Alterações pendentes** estão definidas como 0.

4. Selecione **Confirmar** e, em seguida, selecione **Aplicar**.

    ![Preparar para concluir transferência](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

    > [!IMPORTANT]
    > Após o corte, a disponibilidade de SQL Managed Instance com o nível de serviço Business Critical só pode demorar significativamente mais tempo do que a Finalidade Geral, uma vez que três réplicas secundárias têm de ser semeadas para o grupo AlwaysOn High Availability. Esta duração da operação depende da dimensão dos dados, para mais informações ver a duração das [operações de gestão](../azure-sql/managed-instance/management-operations-overview.md#duration).

5. Quando o estado de migração da base de dados mostrar **concluído,** ligue as suas aplicações à nova instância-alvo do SQL Managed Instance.

    ![Transferência concluída](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Próximos passos

* Para um tutorial que lhe mostre como migrar uma base de dados para SQL Managed Instance utilizando o comando T-SQL RESTORE, consulte [Restaurar uma cópia de segurança para SQL Managed Instance utilizando o comando de restauro](../azure-sql/managed-instance/restore-sample-database-quickstart.md).
* Para obter informações sobre a SQL Managed Instance, consulte [o que é a sql Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).
* Para obter informações sobre a ligação de aplicações a SQL Managed Instance, consulte [aplicações Connect](../azure-sql/managed-instance/connect-application-instance.md).