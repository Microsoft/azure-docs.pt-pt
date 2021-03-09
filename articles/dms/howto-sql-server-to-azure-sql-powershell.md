---
title: 'PowerShell: Migrar o Servidor SQL para a Base de Dados SQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a migrar uma base de dados do SQL Server para a Azure SQL Database utilizando a Azure PowerShell com o Serviço de Migração da Base de Dados Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: a8f7e14500fb377b46f651b53e2704d8477aea7a
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520664"
---
# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-azure-powershell"></a>Migrar uma base de dados do SQL Server para Azure SQL Database usando a Azure PowerShell

Neste artigo, migra a base de dados **Adventureworks2012** restaurada para uma instância no local do SQL Server 2016 ou superior à Base de Dados Azure SQL utilizando o Microsoft Azure PowerShell. Pode migrar bases de dados de uma instância sql server para Azure SQL Database utilizando o `Az.DataMigration` módulo no Microsoft Azure PowerShell.

Neste artigo, vai aprender a:
> [!div class="checklist"]
>
> * Crie um grupo de recursos.
> * Criar uma instância do Azure Database Migration Service.
> * Crie um projeto de migração num caso do Serviço de Migração da Base de Dados Azure.
> * Executar a migração.

## <a name="prerequisites"></a>Pré-requisitos

Para completar estes passos, você precisa:

* [SQL Server 2016 ou superior](https://www.microsoft.com/sql-server/sql-server-downloads) (qualquer edição)
* Para ativar o protocolo TCP/IP, que é desativado por predefinição com a instalação SQL Server Express. Ativar o protocolo TCP/IP seguindo o artigo [Ative ou desative um Protocolo de Rede de Servidor](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Para configurar o seu [Windows Firewall para acesso ao motor de base de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Uma instância de Base de Dados SQL do Azure. Pode criar uma instância de base de dados Azure SQL seguindo os detalhes do artigo [Criar uma base de dados na Base de Dados Azure SQL no portal Azure](../azure-sql/database/single-database-create-quickstart.md).
* [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
* Ter criado uma Rede Virtual Microsoft Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece ao Serviço de Migração da Base de Dados Azure conectividade site-to-site aos seus servidores de origem no local, utilizando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou [o VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Para ter concluído a avaliação da sua base de dados no local e migração de esquemas usando o Data Migration Assistant, conforme descrito no artigo [Realizando uma avaliação de migração do SQL Server](/sql/dma/dma-assesssqlonprem)
* Para descarregar e instalar o módulo Az.DataMigration a partir da Galeria PowerShell utilizando o [cmdlet Install-Module PowerShell](/powershell/module/powershellget/Install-Module); Certifique-se de que abre a janela de comando PowerShell utilizando o funcionamento como administrador.
* Para garantir que as credenciais utilizadas para ligar à origem SQL Server têm a permissão [do SERVIDOR DE CONTROLO.](/sql/t-sql/statements/grant-server-permissions-transact-sql)
* Para garantir que as credenciais utilizadas para se ligar ao exemplo DB do Azure SQL têm a permissão de BASE DE DADOS DE CONTROLO nas bases de dados target Azure SQL Database.
* Uma subscrição do Azure. Se não tiver uma, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Faça login na subscrição do Microsoft Azure

Utilize as instruções no artigo [Faça login com a Azure PowerShell](/powershell/azure/authenticate-azureps) para iniciar sessão na sua subscrição Azure utilizando o PowerShell.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos antes de poder criar uma máquina virtual.

Crie um grupo de recursos utilizando o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup)

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na região *eastus.*

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do Azure Database Migration Service

Pode criar uma nova instância do Serviço de Migração da Base de Dados Azure utilizando o `New-AzDataMigrationService` cmdlet. Este cmdlet espera os seguintes parâmetros necessários:

* *Nome do Grupo de Recursos Azure*. Pode utilizar o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) para criar o grupo de Recursos Azure, como anteriormente mostrado e fornecer o seu nome como parâmetro.
* *Nome de serviço*. Cadeia que corresponde ao nome de serviço único desejado para O Serviço de Migração de Bases de Dados Azure 
* *Localização*. Especifica a localização do serviço. Especificar uma localização do centro de dados Azure, como o Oeste dos EUA ou o Sudeste Asiático
* *Sku.* Este parâmetro corresponde ao nome DMS Sku. O nome Sku atualmente suportado é *GeneralPurpose_4vCores*.
* *Identificador de sub-rede virtual.* Pode utilizar a cmdlet [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) para criar uma sub-rede. 

O exemplo a seguir cria um serviço chamado *MyDMS* no grupo de recursos *MyDMSResourceGroup* localizado na região *leste dos EUA* usando uma rede virtual chamada *MyVNET* e sub-rede chamada *MySubnet*.

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

Depois de criar um serviço de migração de bases de dados Azure, crie um projeto de migração. Um projeto do Azure Database Migration Service requer informações de ligação tanto para as instâncias de origem como para alvos, bem como uma lista de bases de dados que pretende migrar como parte do projeto.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Criar um objeto de informação de ligação de base de dados para as ligações de origem e alvo

Pode criar um objeto info de ligação de base de dados utilizando o `New-AzDmsConnInfo` cmdlet. Este cmdlet espera os seguintes parâmetros:

* *ServerType*. O tipo de ligação de base de dados solicitado, por exemplo, SQL, Oracle ou MySQL. Utilize SQL para SQL Server e Azure SQL.
* *DataSource*. O nome ou IP de uma instância sql server ou Azure SQL Database.
* *AuthType.* O tipo de autenticação para ligação, que pode ser ou a Autorização de SqlAuthentication ou a Versão Desafetação do Windows.
* O parâmetro *TrustServerCertificate* define um valor que indica se o canal está encriptado enquanto contorna a cadeia de certificados para validar a confiança. Valor pode ser verdadeiro ou falso.

O exemplo a seguir cria o objeto Connection Info para o servidor SQL de origem chamado MySourceSQLServer utilizando a autenticação sql:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

O exemplo seguinte mostra a criação de Informação de Conexão para um servidor chamado SQLAzureTarget usando a autenticação sql:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Fornecer bases de dados para o projeto de migração

Crie uma lista de `AzDataMigrationDatabaseInfo` objetos que especifique bases de dados como parte do projeto Azure Database Migration que pode ser fornecido como parâmetro para a criação do projeto. O Cmdlet `New-AzDataMigrationDatabaseInfo` pode ser utilizado para criar AzDataMigrationDatabaseInfo. 

O exemplo a seguir cria `AzDataMigrationDatabaseInfo` projeto para a base de dados **AdventureWorks2016** e adiciona-o à lista a fornecer como parâmetro para a criação de projetos.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Criar um objeto de projeto

Finalmente, pode criar o projeto Azure Database Migration chamado *MyDMSProject* localizado nos *EUA orientais* usando `New-AzDataMigrationProject` e adicionando as ligações de origem e alvo previamente criadas e a lista de bases de dados para migrar.

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

Por fim, crie e inicie a tarefa de migração da Base de Dados Azure. A tarefa de migração da Base de Dados Azure requer informações credenciais de ligação tanto para a origem como para o alvo e lista de tabelas de bases de dados a serem migradas para além das informações já fornecidas com o projeto criado como pré-requisito.

### <a name="create-credential-parameters-for-source-and-target"></a>Criar parâmetros de credencial para a origem e o alvo

As credenciais de segurança de ligação podem ser criadas como um objeto [PSCredential.](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0)

O exemplo a seguir mostra a criação de objetos *PSCredential* para ligações de origem e alvo que fornecem senhas como variáveis de cordas *$sourcePassword* e *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Crie um mapa de tabelas e selecione os parâmetros de origem e alvo para a migração

Outro parâmetro necessário para a migração é o mapeamento de tabelas de origem para alvo a migrar. Crie um dicionário de tabelas que forneça um mapeamento entre as tabelas de origem e alvo para a migração. O exemplo a seguir ilustra o mapeamento entre as tabelas de origem e as tabelas-alvo Esquema de Recursos Humanos para a base de dados AdventureWorks 2016.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

O próximo passo é selecionar as bases de dados de origem e alvo e fornecer mapeamento de mesa para migrar como parâmetro utilizando o `New-AzDmsSelectedDB` cmdlet, como mostra o exemplo seguinte:

```powershell
$selectedDbs = New-AzDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-the-migration-task-and-start-it"></a>Crie a tarefa de migração e inicie-a

Utilize o `New-AzDataMigrationTask` cmdlet para criar e iniciar uma tarefa de migração. Este cmdlet espera os seguintes parâmetros:

* *TaskType*. É esperado o tipo de tarefa de migração para o SQL Server para Azure SQL Database tipo *migração MigrateSqlServerSqlDb.* 
* *Nome do grupo de recursos*. Nome do grupo de recursos Azure para criar a tarefa.
* *Nome de serviço*. Exemplo do Serviço de Migração da Base de Dados Azure para criar a tarefa.
* *Nome do Projeto*. Nome do projeto Azure Database Migration Service para criar a tarefa. 
* *Nome de tarefas*. Nome da tarefa a ser criada. 
* *FonteConnection*. Objeto AzDmsConnInfo que representa a ligação do Servidor SQL de origem.
* *TargetConnection*. Objeto AzDmsConnInfo que representa a ligação de base de dados Azure SQL.
* *SourceCred*. [Objeto PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0) para ligar ao servidor de origem.
* *TargetCred*. [Objeto PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0) para ligar ao servidor alvo.
* *Database selecionada*. AzDataMigrationSelectedDB objeto que representa o mapeamento da base de dados de origem e alvo.
* *SchemaValidation*. (opcional, parâmetro de comutação) Após a migração, realiza-se uma comparação da informação do esquema entre a fonte e o alvo.
* *DataIntegrityValidation*. (opcional, parâmetro de comutação) Após a migração, realiza uma validação de integridade de dados baseada em dados entre a fonte e o alvo.
* *ConsultaAnalysisValidation*. (opcional, parâmetro de comutação) Após a migração, realiza uma análise de consulta rápida e inteligente, recuperando consultas a partir da base de dados de origem e executa-as no alvo.

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

O exemplo a seguir cria e inicia a mesma tarefa de migração que acima, mas também executa as três validações:

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

Pode monitorizar a tarefa de migração em execução consultando a propriedade do Estado da tarefa, como mostrado no exemplo seguinte:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>Apagar a instância do DMS

Após a conclusão da migração, pode eliminar a instância Azure DMS:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-step"></a>Passo seguinte

* Reveja a orientação de migração no [Guia de Migração da Base de Dados da](https://datamigration.microsoft.com/)Microsoft.