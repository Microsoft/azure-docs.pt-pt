---
title: Pré-requisitos para o Serviço de Migração de Bases de Dados Azure
description: Conheça uma visão geral dos pré-requisitos para a utilização do Serviço de Migração da Base de Dados Azure para realizar migrações de bases de dados.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 55d5594229bccb5fcb6a406e671ed104c1e12378
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101094054"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Descrição geral dos pré-requisitos para utilizar o Azure Database Migration Service

Existem vários pré-requisitos necessários para garantir que o Serviço de Migração da Base de Dados de Azure funciona sem problemas ao realizar migrações de bases de dados. Alguns dos pré-requisitos aplicam-se em todos os cenários (pares de alvos de origem) suportados pelo serviço, enquanto outros pré-requisitos são exclusivos de um cenário específico.

Os pré-requisitos associados à utilização do Serviço de Migração da Base de Dados Azure estão listados nas seguintes secções.

## <a name="prerequisites-common-across-migration-scenarios"></a>Pré-requisitos comuns em todos os cenários de migração

Os pré-requisitos do Serviço de Migração da Base de Dados Azure que são comuns em todos os cenários de migração suportados incluem a necessidade de:

* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-to-site aos seus servidores de origem no local, utilizando [expressRoute](../expressroute/expressroute-introduction.md) ou [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Certifique-se de que as regras do Grupo de Segurança da Rede virtual (NSG) não bloqueiam a porta de saída 443 do ServiceTag para ServiceBus, Storage e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego NSG da rede virtual, consulte o artigo Filtrar o [tráfego da rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Ao utilizar um aparelho de firewall em frente à sua base de dados de origem, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base de dados de origem para migração.
* Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Ativar o protocolo TCP/IP, que está desativado por predefinição durante a instalação do SQL Server Express, através das instruções no artigo [Enable or Disable a Server Network Protocol](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) (Ativar ou desativar um Protocolo de Rede de Servidor).

    > [!IMPORTANT]
    > A criação de um caso de Serviço de Migração de Bases de Dados Azure requer acesso a configurações de rede virtuais que normalmente não estão dentro do mesmo grupo de recursos. Como resultado, o utilizador que cria uma instância de DMS requer permissão a nível de subscrição. Para criar as funções necessárias, que pode atribuir conforme necessário, execute o seguinte script:
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Pré-requisitos para migrar o SQL Server para a Base de Dados Azure SQL

Além dos pré-requisitos do Serviço de Migração da Base de Dados Azure que são comuns a todos os cenários de migração, existem também pré-requisitos que se aplicam especificamente a um ou outro cenário.

Ao utilizar o Serviço de Migração da Base de Dados Azure para realizar migrações de bases de dados SQL para Azure SQL Database, para além dos pré-requisitos comuns a todos os cenários de migração, certifique-se de que aborda os seguintes pré-requisitos adicionais:

* Crie uma instância de exemplo da caixa de dados Azure SQL Database, o que faz seguindo os detalhes do artigo [Criar uma base de dados na Base de Dados Azure SQL no portal Azure](../azure-sql/database/single-database-create-quickstart.md).
* Transferir e instalar o [Assistente de Migração de Dados](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
* Abrir a Firewall do Windows para permitir ao Azure Database Migration Service aceder ao SQL Server de origem, que, por predefinição, é a porta TCP 1433.
* Se estiver a executar várias instâncias nomeadas do SQL Server em portas dinâmicas, poderá ser útil ativar o SQL Browser Service e permitir o acesso à porta UDP 1434 através das suas firewalls, de modo a que o Azure Database Migration Service se possa ligar a uma instância nomeada no servidor de origem.
* Crie uma regra de [firewall](../azure-sql/database/firewall-configure.md) ao nível do servidor para a Base de Dados SQL para permitir o acesso do Serviço de Migração da Base de Dados Azure às bases de dados-alvo. Forneça a gama de sub-redes da rede virtual utilizada para o Serviço de Migração da Base de Dados Azure.
* Confirmar que as credenciais utilizadas para ligar à instância de origem do SQL Server têm permissões [CONTROLAR SERVIDOR](/sql/t-sql/statements/grant-server-permissions-transact-sql).
* Certifique-se de que as credenciais utilizadas para ligar à base de dados-alvo têm permissão de BASE DE DADOS DE CONTROLO na base de dados-alvo.

   > [!NOTE]
   > Para obter uma listagem completa dos pré-requisitos necessários para utilizar o Serviço de Migração da Base de Dados Azure para efetuar migrações do SQL Server para a Base de Dados Azure SQL, consulte o [tutorial Migrate SQL Server para Azure SQL Database](./tutorial-sql-server-to-azure-sql.md).
   >

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance"></a>Pré-requisitos para migrar o SQL Server para Azure SQL Gerenciado Instância

* Crie uma SqL Managed Instance seguindo os detalhes do artigo [Crie uma Instância Gerida Azure SQL no portal Azure](../azure-sql/managed-instance/instance-create-quickstart.md).
* Abra as suas firewalls para permitir o tráfego SMB na porta 445 para o endereço IP do Serviço de Migração da Base de Dados Azure ou para a gama de sub-redes.
* Abrir a Firewall do Windows para permitir ao Azure Database Migration Service aceder ao SQL Server de origem, que, por predefinição, é a porta TCP 1433.
* Se estiver a executar várias instâncias nomeadas do SQL Server em portas dinâmicas, poderá ser útil ativar o SQL Browser Service e permitir o acesso à porta UDP 1434 através das suas firewalls, de modo a que o Azure Database Migration Service se possa ligar a uma instância nomeada no servidor de origem.
* Confirme que os inícios de sessão utilizados para ligar o SQL Server de origem e a Instância Gerida de destino são membros da função de servidor sysadmin.
* Crie uma partilha de rede que o Azure Database Migration Service possa utilizar para criar uma cópia de segurança da base de dados de origem.
* Confirme que a conta de serviço em execução na instância do SQL Server de origem tem privilégios de escrita na partilha de rede que criou e que a conta do computador do servidor de origem tem acesso de leitura/escrita à mesma partilha.
* Tome nota de um utilizador do Windows (e da palavra-passe) que tenha privilégio de controlo total na partilha de rede que criou anteriormente. O Serviço de Migração da Base de Dados Azure personifica a credencial do utilizador para enviar os ficheiros de cópia de segurança para o contentor de armazenamento Azure para restaurar o funcionamento.
* Crie um recipiente blob e recupere o seu SAS URI utilizando os passos do artigo [Gerencie os recursos de armazenamento Azure Blob com o Storage Explorer](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container). Certifique-se de que seleciona todas as permissões (Ler, Escrever, Eliminar, Lista) na janela de política enquanto cria o SAS URI.

   > [!NOTE]
   > Para obter uma listagem completa dos pré-requisitos necessários para utilizar o Serviço de Migração da Base de Dados Azure para efetuar migrações do SQL Server para SQL Managed Instance, consulte o [tutorial Migrate SQL Server para SQL Managed Instance](./tutorial-sql-server-to-managed-instance.md).

## <a name="next-steps"></a>Passos seguintes

Para uma visão geral do Serviço de Migração da Base de Dados Azure e disponibilidade regional, consulte o artigo [O que é o Serviço de Migração de Bases de Dados Azure.](dms-overview.md)