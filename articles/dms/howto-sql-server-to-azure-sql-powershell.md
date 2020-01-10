---
title: 'PowerShell: migrar SQL Server para o banco de dados SQL'
titleSuffix: Azure Database Migration Service
description: Saiba como migrar do SQL Server local para o banco de dados SQL do Azure usando Azure PowerShell com o serviço de migração de banco de dados do Azure.
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
ms.openlocfilehash: f67572adc3b40115b2c6d4618718867eacf8c95e
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75746297"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-database-using-azure-powershell"></a>Migre SQL Server no local para o banco de dados SQL do Azure usando Azure PowerShell

Neste artigo, você migra o banco de dados **Adventureworks2012** restaurado para uma instância local do SQL Server 2016 ou superior para um banco de dados SQL do Azure usando Microsoft Azure PowerShell. Você pode migrar bancos de dados de uma instância de SQL Server do local para o Azure SQL usando o módulo `Az.DataMigration` no Microsoft Azure PowerShell.

Neste artigo, vai aprender a:
> [!div class="checklist"]
>
> * Crie um grupo de recursos.
> * Criar uma instância do Azure Database Migration Service.
> * Crie um projeto de migração em uma instância do serviço de migração de banco de dados do Azure.
> * Executar a migração.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir essas etapas, você precisará de:

* [SQL Server 2016 ou superior](https://www.microsoft.com/sql-server/sql-server-downloads) (qualquer edição)
* Para habilitar o protocolo TCP/IP, que é desabilitado por padrão com a instalação do SQL Server Express. Habilite o protocolo TCP/IP seguindo o artigo [habilitar ou desabilitar um protocolo de rede do servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Para configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Uma instância de Base de Dados SQL do Azure. Você pode criar uma instância do banco de dados SQL do Azure seguindo os detalhes no artigo [criar um banco de dados SQL do Azure no portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
* [Assistente de migração de dados](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 ou posterior.
* Para ter criado um Rede Virtual do Microsoft Azure usando o modelo de implantação Azure Resource Manager, que fornece o serviço de migração de banco de dados do Azure com conectividade site a site para seus servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Para concluir a avaliação de seu banco de dados local e a migração de esquema usando Assistente de Migração de Dados, conforme descrito no artigo [executando uma avaliação de migração de SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
* Para baixar e instalar o módulo AZ. datamigration por meio do Galeria do PowerShell usando o [cmdlet Install-Module PowerShell](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1); Certifique-se de abrir a janela de comando do PowerShell usando executar como administrador.
* Para garantir que as credenciais usadas para se conectar à instância de SQL Server de origem tenham a permissão [Control Server](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) .
* Para garantir que as credenciais usadas para se conectar à instância de banco de dados SQL do Azure de destino tenham a permissão CONTROL DATABASE nos bancos de dados de destino SQL do Azure.
* Uma subscrição do Azure. Se você não tiver uma, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Faça logon na sua assinatura do Microsoft Azure

Use as instruções no artigo [fazer logon com Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps) para entrar em sua assinatura do Azure usando o PowerShell.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos para poder criar uma máquina virtual.

Crie um grupo de recursos usando o comando [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) .

O exemplo a seguir cria um grupo de recursos chamado *MyResource* Group na região *eastus* .

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do serviço de migração de banco de dados do Azure

Você pode criar uma nova instância do serviço de migração de banco de dados do Azure usando o cmdlet `New-AzDataMigrationService`. Esse cmdlet espera os seguintes parâmetros obrigatórios:

* *Nome do grupo de recursos do Azure*. Você pode usar o comando [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) para criar o grupo de recursos do Azure como mostrado anteriormente e fornecer seu nome como um parâmetro.
* *Nome do serviço*. Cadeia de caracteres que corresponde ao nome de serviço exclusivo desejado para o serviço de migração de banco de dados do Azure 
* *Localização*. Especifica o local do serviço. Especificar um local de data center do Azure, como oeste dos EUA ou sudeste asiático
* *Sku*. Esse parâmetro corresponde ao nome do SKU do DMS. O nome de SKU com suporte no momento é *GeneralPurpose_4vCores*.
* *Identificador de sub-rede virtual*. Você pode usar o cmdlet [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) para criar uma sub-rede. 

O exemplo a seguir cria um serviço chamado *mydms* no grupo de recursos *MyDMSResourceGroup* localizado na região *leste dos EUA* usando uma rede virtual chamada *MyVNET* e sub-rede chamada *mysubnet*.

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

Depois de criar uma instância do serviço de migração de banco de dados do Azure, crie um projeto de migração. Um projeto de serviço de migração de banco de dados do Azure requer informações de conexão para as instâncias de origem e de destino, bem como uma lista de bancos de dados que você deseja migrar como parte do projeto.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Criar um objeto de informações de conexão de banco de dados para as conexões de origem e de destino

Você pode criar um objeto de informações de conexão de banco de dados usando o cmdlet `New-AzDmsConnInfo`. Esse cmdlet espera os seguintes parâmetros:

* *ServerType*. O tipo de conexão de banco de dados solicitada, por exemplo, SQL, Oracle ou MySQL. Use o SQL para SQL Server e o SQL do Azure.
* *Fonte de fontes*. O nome ou IP de uma instância de SQL Server ou banco de dados SQL do Azure.
* *AuthType*. O tipo de autenticação para conexão, que pode ser sqlauthentication ou WindowsAuthentication.
* O parâmetro *TrustServerCertificate* define um valor que indica se o canal é criptografado ao ignorar a cadeia de certificados para validar a confiança. O valor pode ser true ou false.

O exemplo a seguir cria um objeto de informações de conexão para Source SQL Server chamado MySourceSQLServer usando a autenticação do SQL:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

O exemplo a seguir mostra a criação de informações de conexão para um servidor de banco de dados SQL do Azure chamado SQLAzureTarget usando a autenticação do SQL:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Fornecer bancos de dados para o projeto de migração

Crie uma lista de objetos de `AzDataMigrationDatabaseInfo` que especificam bancos de dados como parte do projeto de migração de banco de dados do Azure que pode ser fornecido como parâmetro para a criação do projeto. O cmdlet `New-AzDataMigrationDatabaseInfo` pode ser usado para criar AzDataMigrationDatabaseInfo. 

O exemplo a seguir cria `AzDataMigrationDatabaseInfo` projeto para o banco de dados **AdventureWorks2016** e o adiciona à lista a ser fornecida como parâmetro para a criação do projeto.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Criar um objeto de projeto

Por fim, você pode criar um projeto de migração de banco de dados do Azure chamado *MyDMSProject* localizado no *leste dos EUA* usando `New-AzDataMigrationProject` e adicionando as conexões de origem e de destino criadas anteriormente e a lista de bancos de dados a serem migrados.

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

Por fim, crie e inicie a tarefa de migração de banco de dados do Azure. A tarefa de migração de banco de dados do Azure requer informações de credenciais de conexão para a origem e o destino e a lista de tabelas de banco de dados a serem migradas, além das informações já fornecidas com o projeto criado como um pré-requisito.

### <a name="create-credential-parameters-for-source-and-target"></a>Criar parâmetros de credencial para origem e destino

As credenciais de segurança de conexão podem ser criadas como um objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) .

O exemplo a seguir mostra a criação de objetos *PSCredential* para conexões de origem e de destino que fornecem senhas como variáveis de cadeia de caracteres *$sourcePassword* e *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Criar um mapa de tabela e selecionar os parâmetros de origem e destino para migração

Outro parâmetro necessário para a migração é o mapeamento de tabelas da origem para o destino a ser migrado. Crie um dicionário de tabelas que fornece um mapeamento entre tabelas de origem e de destino para migração. O exemplo a seguir ilustra o mapeamento entre o esquema de recursos humanos de tabelas de origem e de destino para o banco de dados AdventureWorks 2016.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

A próxima etapa é selecionar os bancos de dados de origem e de destino e fornecer mapeamento de tabela para migrar como um parâmetro usando o cmdlet `New-AzDmsSelectedDB`, conforme mostrado no exemplo a seguir:

```powershell
$selectedDbs = New-AzDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-the-migration-task-and-start-it"></a>Criar a tarefa de migração e iniciá-la

Use o cmdlet `New-AzDataMigrationTask` para criar e iniciar uma tarefa de migração. Esse cmdlet espera os seguintes parâmetros:

* *TaskType*. Tipo de tarefa de migração a ser criada para SQL Server para o tipo de migração de banco de dados SQL do Azure *MigrateSqlServerSqlDb* é esperado. 
* *Nome do grupo de recursos*. Nome do grupo de recursos do Azure no qual criar a tarefa.
* *ServiceName*. Instância do serviço de migração de banco de dados do Azure na qual criar a tarefa.
* *ProjectName*. Nome do projeto do serviço de migração de banco de dados do Azure no qual criar a tarefa. 
* *Nome_Tarefa*. Nome da tarefa a ser criada. 
* *SourceConnection*. Objeto AzDmsConnInfo que representa a conexão SQL Server de origem.
* *TargetConnection*. Objeto AzDmsConnInfo que representa a conexão de banco de dados SQL do Azure de destino.
* *SourceCred*. Objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) para se conectar ao servidor de origem.
* *TargetCred*. Objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) para se conectar ao servidor de destino.
* *SelectedDatabase*. Objeto AzDataMigrationSelectedDB que representa o mapeamento de banco de dados de origem e destino.
* *SchemaValidation*. (opcional, parâmetro de opção) Após a migração, o executa uma comparação das informações de esquema entre a origem e o destino.
* *DataIntegrityValidation*. (opcional, parâmetro de opção) Após a migração, o executa uma validação de integridade de dados baseada em soma de verificação entre a origem e o destino.
* *QueryAnalysisValidation*. (opcional, parâmetro de opção) Após a migração, o executa uma análise de consulta rápida e inteligente recuperando consultas do banco de dados de origem e as executa no destino.

O exemplo a seguir cria e inicia uma tarefa de migração chamada myDMSTask:

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

O exemplo a seguir cria e inicia a mesma tarefa de migração acima, mas também executa todas as três validações:

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
  -SchemaValidation `
  -DataIntegrityValidation `
  -QueryAnalysisValidation `
```

## <a name="monitor-the-migration"></a>Monitorizar a migração

Você pode monitorar a tarefa de migração em execução consultando a propriedade State da tarefa, conforme mostrado no exemplo a seguir:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>Excluindo a instância DMS

Depois que a migração for concluída, você poderá excluir a instância DMS do Azure:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-step"></a>Passo seguinte

* Examine as diretrizes de migração no [Guia de migração de banco de dados](https://datamigration.microsoft.com/)da Microsoft.
