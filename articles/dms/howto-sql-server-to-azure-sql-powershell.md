---
title: Migrar o SQL Server à base de dados SQL do Azure com o serviço de migração de base de dados e o PowerShell | Documentos da Microsoft
description: Aprenda a migrar do SQL Server no local para a base de dados do Azure SQL com o Azure PowerShell.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 599fc7e1eb021e3c519047a14145c292623d7508
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336206"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-database-using-azure-powershell"></a>Migrar o SQL Server no local para a base de dados do SQL Azure com o Azure PowerShell
Neste artigo, migra os **Adventureworks2012** base de dados restaurada para uma instância no local do SQL Server 2016 ou superior para uma base de dados do SQL do Azure com o Microsoft Azure PowerShell. Pode migrar bases de dados de uma instância do SQL Server no local para a base de dados do Azure SQL usando o `Az.DataMigration` módulo no Microsoft Azure PowerShell.

Neste artigo, vai aprender a:
> [!div class="checklist"]
> * Crie um grupo de recursos.
> * Criar uma instância do Azure Database Migration Service.
> * Crie um projeto de migração numa instância de serviço de migração de base de dados do Azure.
> * Executar a migração.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir estes passos, terá de:

- [SQL Server 2016 ou superior](https://www.microsoft.com/sql-server/sql-server-downloads) (qualquer edição)
- Para ativar o protocolo de TCP/IP, o que está desabilitado por predefinição com a instalação do SQL Server Express. Ativar o protocolo de TCP/IP, seguindo o artigo [ativar ou desativar um protocolo de rede do servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Para configurar seus [Firewall do Windows para acesso ao motor de base de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Uma instância de base de dados do Azure SQL. Pode criar uma instância de SQL Database do Azure ao seguir o detalhe no artigo [criar uma base de dados SQL do Azure no portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Assistente de migração de dados](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
- Para criar uma VNET ao utilizar o modelo de implementação Azure Resource Manager, que fornece o serviço de migração de base de dados do Azure com a conectividade de site a site aos seus servidores de origem no local através de um [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Para concluir a avaliação da sua migração de base de dados e esquema de no local com o Assistente de migração de dados, tal como descrito no artigo [realizar uma avaliação de migração do SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
- Para transferir e instalar o módulo de Az.DataMigration da galeria do PowerShell, utilizando [cmdlet do PowerShell de Install-Module](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1); Certifique-se de que abrir a janela de comando do powershell com a execução como administrador.
- Para garantir que as credenciais utilizadas para ligar à instância do SQL Server de origem tem o [servidor de CONTROLE](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) permissão.
- Para garantir que as credenciais utilizadas para estabelecer ligação ao destino do Azure SQL DB instância tem a permissão controlar base de dados nas bases de dados de base de dados do Azure SQL de destino.
- Uma subscrição do Azure. Se não tiver uma, crie uma [gratuita](https://azure.microsoft.com/free/) conta antes de começar.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Inicie sessão na sua subscrição do Microsoft Azure
Utilize as instruções no artigo [iniciar sessão com o Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps) para iniciar sessão sua subscrição do Azure com o PowerShell.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos antes de poder criar uma máquina virtual.

Criar um grupo de recursos com o [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) comando. 

O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* no *EastUS* região.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-instance-of-the-azure-database-migration-service"></a>Criar uma instância do serviço de migração de base de dados do Azure 
Pode criar nova instância do serviço de migração de base de dados do Azure utilizando o `New-AzDataMigrationService` cmdlet. Este cmdlet espera que os parâmetros necessários seguintes:
- *Nome do grupo de recursos do Azure*. Pode usar [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) comando para criar o grupo de recursos do Azure, conforme mostrado anteriormente e forneça o nome como um parâmetro.
- *Nome do serviço*. Cadeia de caracteres que corresponde ao nome do serviço exclusivo desejado para o Azure Database Migration Service 
- *Localização*. Especifica a localização do serviço. Especifique uma localização de centro de dados do Azure, como EUA ocidental ou Sudeste asiático
- *Sku*. Este parâmetro corresponde ao nome de Sku de DMS. O nome de Sku suportado atualmente é *GeneralPurpose_4vCores*.
- *Identificador da sub-rede virtual*. Pode utilizar o cmdlet [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) para criar uma sub-rede. 

O exemplo seguinte cria um serviço com o nome *MyDMS* no grupo de recursos *MyDMSResourceGroup* localizado no *E.U.A. Leste* região com uma rede virtual denominada  *MyVNET* e uma sub-rede denominada *MySubnet*.

```powershell
 $vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Criar um projeto de migração
Depois de criar uma instância de serviço de migração de base de dados do Azure, crie um projeto de migração. Um projeto de serviço de migração de base de dados do Azure requer informações de ligação para as instâncias de origem e destino, bem como uma lista de bases de dados que pretende migrar como parte do projeto.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Criar um objeto de informações de ligação de base de dados para as ligações de origem e destino
Pode criar um objeto de informações de ligação de base de dados utilizando o `New-AzDmsConnInfo` cmdlet. Este cmdlet espera os seguintes parâmetros:
- *ServerType*. O tipo de ligação de base de dados solicitada, por exemplo, MySQL, Oracle ou SQL. Utilize o SQL para o SQL Server e o Azure SQL.
- *DataSource*. O nome ou o IP de uma instância do SQL Server ou a base de dados SQL do Azure.
- *AuthType*. O tipo de autenticação para a ligação, que pode ser SqlAuthentication ou WindowsAuthentication.
- *TrustServerCertificate* parâmetro define um valor que indica se o canal é encriptado ao ignorar a movimentação de cadeia de certificados para validar a confiança. Valor pode ser true ou false.

O exemplo seguinte cria o objeto de informações de ligação para a origem do SQL Server chamado MySourceSQLServer utilizando a autenticação sql: 

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

O exemplo seguinte mostra a criação de informações de ligação para um servidor de base de dados SQL do Azure chamado SQLAzureTarget utilizando a autenticação sql:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Fornecer as bases de dados para o projeto de migração
Criar uma lista de `AzDataMigrationDatabaseInfo` objetos que especifica a bases de dados como parte da migração de base de dados do Azure de projeto que podem ser fornecidos como parâmetro para a criação do projeto. O Cmdlet `New-AzDataMigrationDatabaseInfo` pode ser utilizado para criar AzDataMigrationDatabaseInfo. 

O exemplo seguinte cria `AzDataMigrationDatabaseInfo` do projeto para o **AdventureWorks2016** de base de dados e adiciona-o à lista de ser fornecido como parâmetro para a criação do projeto.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Criar um objeto de projeto
Por fim pode criar o projeto de migração de base de dados do Azure chamado *MyDMSProject* localizado na *E.U.A. Leste* usando `New-AzDataMigrationProject` e adicionar as ligações de origem e destino criadas anteriormente e a lista de a migração de bases de dados.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Criar e iniciar uma tarefa de migração
Por fim, crie e inicie a tarefa de migração de base de dados do Azure. Tarefa de migração de base de dados do Azure requer informações de credenciais de ligação para a origem e destino e lista de tabelas de base de dados a serem migrados além das informações já é fornecidas com o projeto criado como um pré-requisito. 

### <a name="create-credential-parameters-for-source-and-target"></a>Criar parâmetros de credencial para a origem e destino
As credenciais de segurança da ligação podem ser criadas como um [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objeto. 

O exemplo seguinte mostra a criação de *PSCredential* objetos para ligações de origem e destino fornecer palavras-passe como variáveis de cadeia de caracteres *$sourcePassword* e *$ targetPassword*. 

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Criar um mapa de tabela e selecionados parâmetros de origem e de destino para migração
Outro parâmetro necessário para a migração é mapeamento das tabelas de origem para destino a serem migrados. Crie dicionário de tabelas que fornece um mapeamento entre as tabelas de origem e de destino para migração. O exemplo a seguir ilustra o mapeamento entre as tabelas de origem e destino do esquema Human Resources para a base de dados AdventureWorks 2016.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

A próxima etapa é selecionar as bases de dados de origem e de destino e fornecer o mapeamento de tabelas a migrar como um parâmetro com o `New-AzDmsSelectedDB` cmdlet, conforme mostrado no exemplo a seguir:

```powershell
$selectedDbs = New-AzDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-and-start-a-migration-task"></a>Criar e iniciar uma tarefa de migração

Utilize o `New-AzDataMigrationTask` cmdlet para criar e iniciar uma tarefa de migração. Este cmdlet espera os seguintes parâmetros:
- *TaskType*. Tipo de tarefa de migração para criar para o SQL Server para o tipo de migração de base de dados do Azure SQL *MigrateSqlServerSqlDb* é esperado. 
- *Nome do grupo de recursos*. Nome do grupo de recursos do Azure na qual pretende criar a tarefa.
- *ServiceName*. Instância de serviço de migração de base de dados do Azure na qual pretende criar a tarefa.
- *ProjectName*. Nome do projeto de serviço de migração de base de dados do Azure no qual pretende criar a tarefa. 
- *TaskName*. Nome da tarefa a ser criada. 
- *SourceConnection*. Objeto de AzDmsConnInfo que representa a ligação do SQL Server de origem.
- *TargetConnection*. Objeto de AzDmsConnInfo que representa a ligação de base de dados do Azure SQL de destino.
- *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objeto para ligar ao servidor de origem.
- *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objeto para ligar ao servidor de destino.
- *SelectedDatabase*. Objeto de AzDataMigrationSelectedDB que representa o mapeamento de banco de dados de origem e de destino.

O exemplo seguinte cria e inicia uma tarefa de migração com o nome myDMSTask:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
```

## <a name="monitor-the-migration"></a>Monitorizar a migração
Pode monitorizar a tarefa de migração está em execução, consultando a propriedade de estado da tarefa, conforme mostrado no exemplo a seguir:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>A eliminar a instância DMS
Após a migração estiver concluída, pode eliminar a instância do Azure DMS:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-steps"></a>Passos Seguintes
- Reveja as orientações de migração no Microsoft [guia de migração de bases de dados](https://datamigration.microsoft.com/).
