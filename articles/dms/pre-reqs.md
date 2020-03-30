---
title: Pré-requisitos para o Serviço de Migração de Bases de Dados Azure
description: Conheça uma visão geral dos pré-requisitos para a utilização do Serviço de Migração de Bases de Dados Azure para realizar migrações de bases de dados.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 89cb63630e3dbe953ed3f4fd8796d01ba0d36067
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651496"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Descrição geral dos pré-requisitos para utilizar o Azure Database Migration Service

Existem vários pré-requisitos necessários para garantir que o Serviço de Migração de Bases de Dados Azure funciona sem problemas ao realizar migrações de bases de dados. Alguns dos pré-requisitos aplicam-se em todos os cenários (pares de alvo de origem) suportados pelo serviço, enquanto outros pré-requisitos são exclusivos de um cenário específico.

Os pré-requisitos associados à utilização do Serviço de Migração de Bases de Dados Azure estão listados nas seguintes secções.

## <a name="prerequisites-common-across-migration-scenarios"></a>Pré-requisitos comuns entre cenários de migração

Os pré-requisitos do Serviço de Migração da Base de Dados Azure que são comuns em todos os cenários de migração suportados incluem a necessidade de:

* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-a-site aos seus servidores de origem no local, utilizando [expressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Certifique-se de que as regras do Grupo de Segurança da Rede Virtual (NSG) não bloqueiam as seguintes portas de comunicação 443, 53, 9354, 445, 12000. Para mais detalhes sobre a filtragem de tráfego da rede virtual NSG, consulte o artigo Filtrar o tráfego da [rede com grupos](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)de segurança da rede .
* Ao utilizar um aparelho de firewall em frente à sua base de dados de origem, poderá ter de adicionar regras de firewall para permitir que o Serviço de Migração de Bases de Dados Azure aceda à base de dados de origem para migração.
* Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Ativar o protocolo TCP/IP, que está desativado por predefinição durante a instalação do SQL Server Express, através das instruções no artigo [Enable or Disable a Server Network Protocol](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) (Ativar ou desativar um Protocolo de Rede de Servidor).

    > [!IMPORTANT]
    > A criação de uma instância do Serviço de Migração de Bases de Dados Azure requer acesso a configurações de rede virtuais que normalmente não estão dentro do mesmo grupo de recursos. Como resultado, o utilizador que cria uma instância de DMS requer permissão ao nível da subscrição. Para criar as funções necessárias, que pode atribuir conforme necessário, executar o seguinte script:
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

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Pré-requisitos para migrar o Servidor SQL para a Base de Dados SQL Azure

Além dos pré-requisitos do Serviço de Migração de Bases de Dados Azure que são comuns a todos os cenários de migração, existem também pré-requisitos que se aplicam especificamente a um ou outro cenário.

Ao utilizar o Serviço de Migração de Bases de Dados Azure para realizar as migrações de Base de Dados SQL SQL para Azure, para além dos pré-requisitos que são comuns a todos os cenários de migração, certifique-se de que abordará os seguintes pré-requisitos adicionais:

* Criar uma instância da Base de Dados SQL do Azure; para tal, siga o detalhe descrito no artigo [Criar uma base de Dados SQL do Azure no portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
* Transferir e instalar o [Assistente de Migração de Dados](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
* Abrir a Firewall do Windows para permitir ao Azure Database Migration Service aceder ao SQL Server de origem, que, por predefinição, é a porta TCP 1433.
* Se estiver a executar várias instâncias nomeadas do SQL Server em portas dinâmicas, poderá ser útil ativar o SQL Browser Service e permitir o acesso à porta UDP 1434 através das suas firewalls, de modo a que o Azure Database Migration Service se possa ligar a uma instância nomeada no servidor de origem.
* Criar uma [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) ao nível do servidor para o servidor da Base de Dados SQL do Azure para permitir que o Azure Database Migration Service aceda às bases de dados de destino. Forneça a gama de subredes da rede virtual utilizada para o Serviço de Migração de Bases de Dados Azure.
* Confirmar que as credenciais utilizadas para ligar à instância de origem do SQL Server têm permissões [CONTROLAR SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
* Confirmar que as credenciais utilizadas para ligar à instância de destino da Base de Dados SQL do Azure têm permissões CONTROLAR BASE DE DADOS nas bases de dados SQL do Azure de destino.

   > [!NOTE]
   > Para obter uma lista completa dos pré-requisitos necessários para utilizar o Serviço de Migração de Bases de Dados Azure para efetuar migrações do SQL Server para a Base de Dados SQL Azure, consulte o tutorial [Migrate SQL Server para a Base de Dados Azure SQL](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).
   >

## <a name="prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance"></a>Pré-requisitos para migrar o Servidor SQL para uma base de dados Azure SQL gerida

* Crie uma base de dados Azure SQL gerida seguindo os detalhes do artigo Criar uma instância gerida por base de [dados Azure SQL no portal Azure](https://aka.ms/sqldbmi).
* Abra as suas firewalls para permitir o tráfego sMB na porta 445 para o endereço IP do Serviço de Migração de Bases de Dados Azure ou gama de sub-redes.
* Abrir a Firewall do Windows para permitir ao Azure Database Migration Service aceder ao SQL Server de origem, que, por predefinição, é a porta TCP 1433.
* Se estiver a executar várias instâncias nomeadas do SQL Server em portas dinâmicas, poderá ser útil ativar o SQL Browser Service e permitir o acesso à porta UDP 1434 através das suas firewalls, de modo a que o Azure Database Migration Service se possa ligar a uma instância nomeada no servidor de origem.
* Confirme que os inícios de sessão utilizados para ligar o SQL Server de origem e a Instância Gerida de destino são membros da função de servidor sysadmin.
* Crie uma partilha de rede que o Azure Database Migration Service possa utilizar para criar uma cópia de segurança da base de dados de origem.
* Confirme que a conta de serviço em execução na instância do SQL Server de origem tem privilégios de escrita na partilha de rede que criou e que a conta do computador do servidor de origem tem acesso de leitura/escrita à mesma partilha.
* Tome nota de um utilizador do Windows (e da palavra-passe) que tenha privilégio de controlo total na partilha de rede que criou anteriormente. O Serviço de Migração de Bases de Dados Azure personifica a credencial do utilizador para enviar os ficheiros de backup para o contentor de armazenamento Azure para restabelecer o funcionamento.
* Crie um recipiente de bolha e recupere o seu SAS URI utilizando os passos do artigo Gerir os recursos de [armazenamento da Blob Azure com o Storage Explorer.](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) Certifique-se de selecionar todas as permissões (Ler, Escrever, Excluir, Lista) na janela de política enquanto cria o SAS URI.

   > [!NOTE]
   > Para obter uma lista completa dos pré-requisitos necessários para utilizar o Serviço de Migração de Bases de Dados Azure para efetuar migrações do SQL Server para a Instância Gerida pela Base de Dados Azure SQL, consulte o tutorial [Migrate SQL Server para a Instância Gerida pela Base de Dados Azure SQL.](https://aka.ms/migratetomiusingdms)

## <a name="next-steps"></a>Passos seguintes

Para uma visão geral do Serviço de Migração da Base de Dados Azure e da disponibilidade regional, consulte o artigo O que é o Serviço de Migração de Bases de [Dados Azure.](dms-overview.md)
