---
title: 'Powershell: Migrar o Servidor SQL para a Base de Dados SQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a migrar do SQL Server para a Base de Dados Azure SQL utilizando o Azure PowerShell com o Serviço de Migração de Bases de Dados Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: f63f79402b457017257f1762c6ddc7e04c0ee1af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650695"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-database-using-azure-powershell"></a>Migrar o Servidor SQL no local para a Base de Dados Azure SQL utilizando o Azure PowerShell

Neste artigo, migra a base de dados **Adventureworks2012** restaurada para uma instância no local do SQL Server 2016 ou superior a uma Base de Dados Azure SQL utilizando o Microsoft Azure PowerShell. Pode migrar bases de dados de uma instância do SQL Server no local `Az.DataMigration` para a Base de Dados Azure SQL utilizando o módulo no Microsoft Azure PowerShell.

Neste artigo, vai aprender a:
> [!div class="checklist"]
>
> * Crie um grupo de recursos.
> * Criar uma instância do Azure Database Migration Service.
> * Crie um projeto de migração numa instância do Serviço de Migração de Bases de Dados Azure.
> * Executar a migração.

## <a name="prerequisites"></a>Pré-requisitos

Para completar estes passos, precisa de:

* [SQL Server 2016 ou superior](https://www.microsoft.com/sql-server/sql-server-downloads) (qualquer edição)
* Para ativar o protocolo TCP/IP, que é desativado por padrão com a instalação Do SQL Server Express. Ative o protocolo TCP/IP seguindo o artigo [Ativar ou Desativar um Protocolo](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)de Rede de Servidores .
* Para configurar o [seu Windows Firewall para acesso](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)ao motor de base de dados .
* Uma instância de Base de Dados SQL do Azure. Pode criar uma instância de base de dados Azure SQL seguindo os detalhes do artigo [Criar uma base de dados Azure SQL no portal Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
* Assistente de [Migração](https://www.microsoft.com/download/details.aspx?id=53595) de Dados v3.3 ou posterior.
* Ter criado uma Rede Virtual Microsoft Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece ao Serviço de Migração de Bases de Dados Azure conectividade site-a-site para os seus servidores de origem no local, utilizando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [o VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)
* Ter concluído a avaliação da sua base de dados no local e migração de esquemas utilizando o Assistente de Migração de Dados, conforme descrito no [artigo, realizando uma avaliação de migração do Servidor SQL](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
* Para descarregar e instalar o módulo Az.DataMigration a partir da Galeria PowerShell utilizando o [cmdlet de PowerShell de instalação;](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1) certifique-se de abrir a janela de comando powershell usando o executar como administrador.
* Para garantir que as credenciais utilizadas para ligar à instância de origem Do Servidor SQL têm a permissão [CONTROL SERVER.](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)
* Para garantir que as credenciais utilizadas para se ligar em direção à instância De QQL DB tem a permissão de BASE DE DADOS DE CONTROLO nas bases de dados de base de dados Azure SQL alvo.
* Uma subscrição do Azure. Se não tiver uma, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Inicie sessão na subscrição do Microsoft Azure

Utilize as instruções do artigo Faça login com o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps) para iniciar sessão na subscrição do Azure utilizando o PowerShell.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos antes de criar uma máquina virtual.

Crie um grupo de recursos utilizando o comando [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)

O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* na região *de EastUS.*

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do Serviço de Migração de Bases de Dados Azure

Pode criar uma nova instância do Serviço `New-AzDataMigrationService` de Migração de Bases de Dados Azure utilizando o cmdlet. Este cmdlet espera os seguintes parâmetros necessários:

* *Nome do Grupo de Recursos Azure*. Pode utilizar o comando [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) para criar o grupo Azure Resource, como anteriormente mostrado e fornecer o seu nome como parâmetro.
* *Nome de serviço*. Corda que corresponde ao nome de serviço único desejado para o Serviço de Migração de Bases de Dados Azure 
* *Localização*. Especifica a localização do serviço. Especifique uma localização do centro de dados Azure, como os EUA Ocidentais ou o Sudeste Asiático
* *Sku.* Este parâmetro corresponde ao nome DMS Sku. O nome Sku atualmente apoiado é *GeneralPurpose_4vCores*.
* *Identificador de sub-rede virtual.* Pode utilizar cmdlet [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) para criar uma sub-rede. 

O exemplo seguinte cria um serviço chamado *MyDMS* no grupo de recursos *MyDMSResourceGroup* localizado na região *leste dos EUA* usando uma rede virtual chamada *MyVNET* e subnet chamada *MySubnet*.

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

Depois de criar uma instância azure Database Migration Service, crie um projeto de migração. Um projeto do Serviço de Migração de Bases de Dados Azure requer informações de ligação tanto para as instâncias de origem como para as ocorrências-alvo, bem como uma lista de bases de dados que pretende migrar como parte do projeto.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Criar um objeto de informação de ligação de base de dados para a origem e ligações-alvo

Pode criar um objeto informação `New-AzDmsConnInfo` de ligação à base de dados utilizando o cmdlet. Este cmdlet espera os seguintes parâmetros:

* *Tipo de servidor*. O tipo de ligação de base de dados solicitada, por exemplo, SQL, Oracle ou MySQL. Utilize O SQL para O Servidor SQL e o Azure SQL.
* *DataSource*. O nome ou IP de uma instância do Servidor SQL ou base de dados Azure SQL.
* *AuthType*. O tipo de autenticação para ligação, que pode ser SqlAuthentication ou WindowsAuthentication.
* O parâmetro *TrustServerCertificate* define um valor que indica se o canal está encriptado enquanto contorna a cadeia de certificados para validar a confiança. O valor pode ser verdadeiro ou falso.

O exemplo seguinte cria o objeto De Ligação Info para fonte SQL Server chamado MySourceSQLServer utilizando a autenticação sql:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

O exemplo seguinte mostra a criação de Info de Ligação para um servidor de base de dados Azure SQL chamado SQLAzureTarget utilizando a autenticação sql:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Fornecer bases de dados para o projeto de migração

Crie uma `AzDataMigrationDatabaseInfo` lista de objetos que especifiquem bases de dados como parte do projeto de migração de bases de dados Azure que pode ser fornecido como parâmetro para a criação do projeto. O Cmdlet `New-AzDataMigrationDatabaseInfo` pode ser usado para criar AzDataMigrationDatabaseInfo. 

O exemplo `AzDataMigrationDatabaseInfo` seguinte cria projeto para a base de dados **AdventureWorks2016** e adiciona-o à lista a ser fornecida como parâmetro para a criação de projetos.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Criar um objeto de projeto

Finalmente, pode criar o projeto Demigração de Bases de `New-AzDataMigrationProject` Dados Azure chamado *MyDMSProject* localizado no Leste dos *EUA* utilizando e adicionando as ligações de origem e alvo previamente criadas e a lista de bases de dados para migrar.

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

Finalmente, crie e inicie a tarefa de migração da Base de Dados Azure. A tarefa de migração da Base de Dados Azure requer informações credenciais de ligação para a origem e o alvo e lista de tabelas de bases de dados a migrar, para além das informações já fornecidas com o projeto criado como pré-requisito.

### <a name="create-credential-parameters-for-source-and-target"></a>Criar parâmetros credenciais para fonte e alvo

As credenciais de segurança de ligação podem ser criadas como um objeto [PSCredential.](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0)

O exemplo seguinte mostra a criação de objetos *PSCredential* para ligações de origem e alvo que fornecem palavras-passe como variáveis de cadeia *$sourcePassword* e *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Crie um mapa de tabela seleção e selecione os parâmetros de origem e alvo para a migração

Outro parâmetro necessário para a migração é o mapeamento de tabelas de origem para destino a migrar. Crie dicionário de tabelas que fornece um mapeamento entre as tabelas de origem e alvo para migração. O exemplo que se segue ilustra o mapeamento entre as tabelas de origem e alvo dos Recursos Humanos para a base de dados AdventureWorks 2016.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

O próximo passo é selecionar as bases de dados de origem e alvo `New-AzDmsSelectedDB` e fornecer mapeamento de tabela para migrar como parâmetro utilizando o cmdlet, como mostra o seguinte exemplo:

```powershell
$selectedDbs = New-AzDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-the-migration-task-and-start-it"></a>Criar a tarefa de migração e iniciá-la

Utilize `New-AzDataMigrationTask` o cmdlet para criar e iniciar uma tarefa de migração. Este cmdlet espera os seguintes parâmetros:

* *Tipo de tarefa*. Tipo de tarefa de migração para criar para o SQL Server para azure SQL Base de dados de migração tipo *migração MigrateSqlServerSqlDb* é esperado. 
* *Nome do grupo de recursos*. Nome do grupo de recursos Azure para criar a tarefa.
* *Nome de serviço*. Instância do Serviço de Migração da Base de Dados Azure para criar a tarefa.
* *Nome do projeto.* Nome do projeto Azure Database Migration Service para criar a tarefa. 
* *Nome de tarefa*. Nome da tarefa a ser criado. 
* *Ligação de origem*. Objeto AzDmsConnInfo representando a ligação fonte Do Servidor SQL.
* *Ligação de alvos*. Objeto AzDmsConnInfo que representa a ligação de base de dados Azure SQL.
* *FonteCred*. [Objeto PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) para ligar ao servidor de origem.
* *TargetCred*. [Objeto PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) para ligar ao servidor alvo.
* *Base de Dados Selecionado*. Objeto AzDataMigrationSelectedDB que representa o mapeamento da base de dados de origem e alvo.
* *SchemaValidação*. (parâmetro opcional e de comutação) Após a migração, eexecuta uma comparação da informação do esquema entre fonte e alvo.
* *DataIntegrityValidação*. (parâmetro opcional e de comutação) Após a migração, efetua uma validação da integridade dos dados baseada em verificação entre fonte e alvo.
* *ConsultaAnáliseValidação*. (parâmetro opcional e de comutação) Após a migração, realiza uma análise rápida e inteligente da consulta, recuperando consultas da base de dados de origem e executando-as no alvo.

O exemplo seguinte cria e inicia uma tarefa de migração chamada myDMSTask:

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

O exemplo seguinte cria e inicia a mesma tarefa de migração que acima, mas também executa as três validações:

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

Pode monitorizar a tarefa de migração em execução consultando a propriedade do Estado da tarefa, como mostra o seguinte exemplo:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>Apagar a instância DMS

Após a migração estar concluída, pode eliminar a instância DeMS Azure:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-step"></a>Passo seguinte

* Reveja a orientação de migração no Guia de [Migração](https://datamigration.microsoft.com/)da Microsoft Database .
