---
title: Descrição geral da pré-requisitos para utilizar o serviço de migração de base de dados do Azure | Documentos da Microsoft
description: Saiba mais sobre uma visão geral dos pré-requisitos para utilizar o serviço de migração de base de dados do Azure para efetuar as migrações de base de dados.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 05/29/2019
ms.openlocfilehash: 4e21014f7b4ed86846a100ed9a2b1cd4b0400974
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304265"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Descrição geral da pré-requisitos para utilizar o serviço de migração de base de dados do Azure

Existem vários pré-requisitos necessários para garantir que o serviço de migração de base de dados do Azure é executado sem problemas ao realizar migrações de base de dados. Alguns dos pré-requisitos aplicam-se em todos os cenários (pares origem-destino) suportados pelo serviço, enquanto outros pré-requisitos são exclusivos para um cenário específico.

Pré-requisitos associados a utilizar o serviço de migração de base de dados do Azure estão listados nas secções seguintes.

## <a name="prerequisites-common-across-migration-scenarios"></a>Pré-requisitos comuns em cenários de migração

Os pré-requisitos de serviço de migração de base de dados do Azure que são comuns em todos os cenários de migração suportados incluem a necessidade de:

* Criar uma rede Virtual do Azure (VNet) para o Azure Database Migration Service com o modelo de implementação Azure Resource Manager, que garante uma conectividade site a site aos seus servidores de origem no local através de um [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Certifique-se de que as regras do grupo de segurança de rede (NSG) do VNet não bloqueiam as seguintes portas de comunicação 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego do Azure VNet NSG, consulte o artigo [filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Quando utilizar uma aplicação de firewall na frente de suas bases de dados de origem, terá de adicionar regras de firewall para permitir que o serviço de migração de base de dados do Azure acessar os bancos de dados de origem para migração.
* Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Ativar o protocolo TCP/IP, que está desativado por predefinição durante a instalação do SQL Server Express, através das instruções no artigo [Enable or Disable a Server Network Protocol](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) (Ativar ou desativar um Protocolo de Rede de Servidor).

    > [!IMPORTANT]
    > Criando uma instância do serviço de migração de base de dados do Azure requer acesso a definições da VNet que normalmente não estão no mesmo grupo de recursos. Como resultado, o utilizador criar uma instância do DMS necessita de permissão em nível de assinatura. Para criar as funções necessárias, o que pode atribuir conforme necessário, execute o seguinte script:
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.DataMigration/services/*/read", `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action"
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

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Pré-requisitos para migração do SQL Server para a base de dados do Azure SQL

Além dos pré-requisitos do Azure Database Migration Service que são comuns a todos os cenários de migração, também existem pré-requisitos que se aplicam especificamente para um cenário ou de outra.

Ao utilizar o serviço de migração de base de dados do Azure para executar o SQL Server para migrações de base de dados do Azure SQL, além dos pré-requisitos que são comuns a todos os cenários de migração, certifique-se de que os seguintes pré-requisitos adicionais de endereços:

* Criar uma instância de instância de base de dados do Azure SQL, que pode fazer ao seguir o detalhe no artigo C[riar uma base de dados SQL do Azure no portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
* Transferir e instalar o [Assistente de Migração de Dados](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
* Abrir a Firewall do Windows para permitir ao Azure Database Migration Service aceder ao SQL Server de origem, que, por predefinição, é a porta TCP 1433.
* Se estiver a executar várias instâncias nomeadas do SQL Server em portas dinâmicas, poderá ser útil ativar o SQL Browser Service e permitir o acesso à porta UDP 1434 através das suas firewalls, de modo a que o Azure Database Migration Service se possa ligar a uma instância nomeada no servidor de origem.
* Criar uma [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) ao nível do servidor para o servidor da Base de Dados SQL do Azure para permitir que o Azure Database Migration Service aceda às bases de dados de destino. Fornecer o intervalo de sub-redes da VNET utilizada no Azure Database Migration Service.
* Confirmar que as credenciais utilizadas para ligar à instância de origem do SQL Server têm permissões [CONTROLAR SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
* Confirmar que as credenciais utilizadas para ligar à instância de destino da Base de Dados SQL do Azure têm permissões CONTROLAR BASE DE DADOS nas bases de dados SQL do Azure de destino.

   > [!NOTE]
   > Para obter uma lista completa dos pré-requisitos necessários para utilizar o serviço de migração de base de dados do Azure para efetuar migrações do SQL Server para a base de dados do Azure SQL, veja o tutorial [migrar o SQL Server para a base de dados do Azure SQL](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance"></a>Pré-requisitos para migrar o servidor SQL para uma instância gerida da base de dados do Azure SQL

* Criar uma instância gerida da base de dados do Azure SQL, seguindo o detalhe no artigo [criar uma instância de gerida de base de dados do Azure SQL no portal do Azure](https://aka.ms/sqldbmi).
* Abra as firewalls para permitir o tráfego SMB na porta 445 para o intervalo de sub-rede ou o endereço IP de serviço de migração de base de dados do Azure.
* Abrir a Firewall do Windows para permitir ao Azure Database Migration Service aceder ao SQL Server de origem, que, por predefinição, é a porta TCP 1433.
* Se estiver a executar várias instâncias nomeadas do SQL Server em portas dinâmicas, poderá ser útil ativar o SQL Browser Service e permitir o acesso à porta UDP 1434 através das suas firewalls, de modo a que o Azure Database Migration Service se possa ligar a uma instância nomeada no servidor de origem.
* Confirme que os inícios de sessão utilizados para ligar o SQL Server de origem e a Instância Gerida de destino são membros da função de servidor sysadmin.
* Crie uma partilha de rede que o Azure Database Migration Service possa utilizar para criar uma cópia de segurança da base de dados de origem.
* Confirme que a conta de serviço em execução na instância do SQL Server de origem tem privilégios de escrita na partilha de rede que criou e que a conta do computador do servidor de origem tem acesso de leitura/escrita à mesma partilha.
* Tome nota de um utilizador do Windows (e da palavra-passe) que tenha privilégio de controlo total na partilha de rede que criou anteriormente. O Azure Database Migration Service representa a credencial do utilizador para carregar os ficheiros de cópia de segurança para o contentor de armazenamento do Azure, para a operação de restauro.
* Criar um contentor de BLOBs e recuperar o seu URI de SAS, utilizando os passos no artigo [recursos de gerir o armazenamento de Blobs do Azure com o Explorador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Certifique-se de que selecionar todas as permissões (leitura, escrita, eliminação, lista) na janela de política ao criar o URI de SAS.

   > [!NOTE]
   > Para obter uma lista completa dos pré-requisitos necessários para utilizar o serviço de migração de base de dados do Azure para efetuar as migrações do SQL Server para instância gerida da base de dados SQL do Azure, veja o tutorial [migrar o SQL Server para instância gerida da base de dados SQL do Azure ](https://aka.ms/migratetomiusingdms).

## <a name="next-steps"></a>Passos Seguintes

Para uma descrição geral do serviço de migração de base de dados do Azure e a disponibilidade regional, consulte o artigo [o que é o Azure Database Migration Service](dms-overview.md).
