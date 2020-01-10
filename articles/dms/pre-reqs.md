---
title: Pré-requisitos para o serviço de migração de banco de dados do Azure
description: Saiba mais sobre uma visão geral dos pré-requisitos para usar o serviço de migração de banco de dados do Azure para executar migrações de banco de dados.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 7ba317da9524c322d47fe57a866d429ff8f7e952
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748736"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Visão geral dos pré-requisitos para usar o serviço de migração de banco de dados do Azure

Há vários pré-requisitos necessários para garantir que o serviço de migração de banco de dados do Azure funcione sem problemas ao executar migrações de banco de dados. Alguns dos pré-requisitos se aplicam em todos os cenários (pares de destino de origem) com suporte do serviço, enquanto outros pré-requisitos são exclusivos para um cenário específico.

Os pré-requisitos associados ao uso do serviço de migração de banco de dados do Azure são listados nas seções a seguir.

## <a name="prerequisites-common-across-migration-scenarios"></a>Pré-requisitos comuns em cenários de migração

Os pré-requisitos do serviço de migração de banco de dados do Azure que são comuns em todos os cenários de migração com suporte incluem a necessidade de:

* Crie um Rede Virtual do Microsoft Azure para o serviço de migração de banco de dados do Azure usando o modelo de implantação Azure Resource Manager, que fornece conectividade site a site para seus servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Verifique se suas regras de NSG (grupo de segurança de rede) de rede virtual não bloqueiam as seguintes portas de comunicação 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego NSG de rede virtual, consulte o artigo [filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o serviço de migração de banco de dados do Azure acesse os bancos de dados de origem para migração.
* Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Ativar o protocolo TCP/IP, que está desativado por predefinição durante a instalação do SQL Server Express, através das instruções no artigo [Enable or Disable a Server Network Protocol](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) (Ativar ou desativar um Protocolo de Rede de Servidor).

    > [!IMPORTANT]
    > A criação de uma instância do serviço de migração de banco de dados do Azure requer acesso às configurações de networt virtual que normalmente não estão dentro do mesmo grupo de recursos. Como resultado, o usuário que está criando uma instância do DMS requer permissão no nível da assinatura. Para criar as funções necessárias, que você pode atribuir conforme necessário, execute o seguinte script:
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

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Pré-requisitos para migrar SQL Server para o banco de dados SQL do Azure

Além dos pré-requisitos do serviço de migração de banco de dados do Azure que são comuns a todos os cenários de migração, também há pré-requisitos que se aplicam especificamente a um cenário ou a outro.

Ao usar o serviço de migração de banco de dados do Azure para executar SQL Server para migrações de banco de dados SQL do Azure, além dos pré-requisitos que são comuns a todos os cenários de migração, certifique-se de abordar os seguintes pré-requisitos adicionais:

* Crie uma instância da instância do banco de dados SQL do Azure, que você faz seguindo os detalhes no artigo C[RIAR um banco de dados SQL do Azure no portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
* Transferir e instalar o [Assistente de Migração de Dados](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
* Abrir a Firewall do Windows para permitir ao Azure Database Migration Service aceder ao SQL Server de origem, que, por predefinição, é a porta TCP 1433.
* Se estiver a executar várias instâncias nomeadas do SQL Server em portas dinâmicas, poderá ser útil ativar o SQL Browser Service e permitir o acesso à porta UDP 1434 através das suas firewalls, de modo a que o Azure Database Migration Service se possa ligar a uma instância nomeada no servidor de origem.
* Criar uma [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) ao nível do servidor para o servidor da Base de Dados SQL do Azure para permitir que o Azure Database Migration Service aceda às bases de dados de destino. Forneça o intervalo de sub-rede da rede virtual usada para o serviço de migração de banco de dados do Azure.
* Confirmar que as credenciais utilizadas para ligar à instância de origem do SQL Server têm permissões [CONTROLAR SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
* Confirmar que as credenciais utilizadas para ligar à instância de destino da Base de Dados SQL do Azure têm permissões CONTROLAR BASE DE DADOS nas bases de dados SQL do Azure de destino.

   > [!NOTE]
   > Para obter uma lista completa dos pré-requisitos necessários para usar o serviço de migração de banco de dados do Azure para executar migrações de SQL Server para o banco de dados SQL do Azure, consulte o tutorial [migrar SQL Server para o banco de dados SQL do Azure](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance"></a>Pré-requisitos para migrar SQL Server para uma instância gerenciada do banco de dados SQL do Azure

* Crie uma instância gerenciada do banco de dados SQL do Azure seguindo os detalhes no artigo [criar um instância gerenciada do banco de dados SQL do Azure no portal do Azure](https://aka.ms/sqldbmi).
* Abra seus firewalls para permitir o tráfego SMB na porta 445 para o endereço IP ou o intervalo de sub-rede do serviço de migração de banco de dados do Azure.
* Abrir a Firewall do Windows para permitir ao Azure Database Migration Service aceder ao SQL Server de origem, que, por predefinição, é a porta TCP 1433.
* Se estiver a executar várias instâncias nomeadas do SQL Server em portas dinâmicas, poderá ser útil ativar o SQL Browser Service e permitir o acesso à porta UDP 1434 através das suas firewalls, de modo a que o Azure Database Migration Service se possa ligar a uma instância nomeada no servidor de origem.
* Confirme que os inícios de sessão utilizados para ligar o SQL Server de origem e a Instância Gerida de destino são membros da função de servidor sysadmin.
* Crie uma partilha de rede que o Azure Database Migration Service possa utilizar para criar uma cópia de segurança da base de dados de origem.
* Confirme que a conta de serviço em execução na instância do SQL Server de origem tem privilégios de escrita na partilha de rede que criou e que a conta do computador do servidor de origem tem acesso de leitura/escrita à mesma partilha.
* Tome nota de um utilizador do Windows (e da palavra-passe) que tenha privilégio de controlo total na partilha de rede que criou anteriormente. O serviço de migração de banco de dados do Azure representa a credencial do usuário para carregar os arquivos de backup no contêiner de armazenamento do Azure para a operação de restauração.
* Crie um contêiner de BLOB e recupere seu URI SAS usando as etapas no artigo [gerenciar recursos de armazenamento de BLOBs do Azure com Gerenciador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Certifique-se de selecionar todas as permissões (leitura, gravação, exclusão, lista) na janela de política ao criar o URI de SAS.

   > [!NOTE]
   > Para obter uma lista completa dos pré-requisitos necessários para usar o serviço de migração de banco de dados do Azure para executar migrações de SQL Server para Instância Gerenciada do Banco de Dados SQL do Azure, consulte o tutorial [migrar SQL Server para instância gerenciada do banco de dados SQL do Azure](https://aka.ms/migratetomiusingdms).

## <a name="next-steps"></a>Passos seguintes

Para obter uma visão geral do serviço de migração de banco de dados do Azure e disponibilidade regional, consulte o artigo o [que é o serviço de migração de banco de dados do Azure](dms-overview.md).
