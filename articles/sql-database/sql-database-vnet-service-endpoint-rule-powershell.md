---
title: PowerShell para pontos finais VNet e regras para bases de dados individuais e reunidas
description: Fornece scripts PowerShell para criar e gerir pontos finais de Serviço Virtual para a sua Base de Dados Azure SQL e Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, vanto
ms.date: 03/12/2019
tags: azure-synapse
ms.openlocfilehash: 1e8ec394eab1df0aebe394b8acebc74c7ed49e9c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80124691"
---
# <a name="powershell--create-a-virtual-service-endpoint-and-vnet-rule-for-sql"></a>PowerShell: Criar um ponto final de serviço virtual e regra VNet para SQL

*As regras* de rede virtual são uma característica de segurança de firewall que controla se o servidor de base de dados para as suas bases de dados únicas e um pool elástico na Base de Dados Azure [SQL](sql-database-technical-overview.md) ou para as suas bases de dados em [Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) aceita comunicações enviadas a partir de determinadas redes virtuais.

> [!IMPORTANT]
> Este artigo aplica-se ao servidor Azure SQL, e tanto à Base de Dados SQL como ao armazém de dados em Azure Synapse que são criados no servidor Azure SQL. Para a simplicidade, a Base de Dados SQL é utilizada quando se refere tanto à Base de Dados SQL como ao Synapse Azure. Este artigo *não* se aplica a uma implantação de **instância gerida** na Base de Dados Azure SQL porque não tem um ponto final de serviço associado ao mesmo.

Este artigo fornece e explica um script PowerShell que toma as seguintes ações:

1. Cria um ponto final do *Serviço Virtual* Microsoft Azure na sua subnet.
2. Adiciona o ponto final à firewall do seu servidor de base de dados Azure SQL, para criar uma regra de *rede virtual*.

As suas motivações para criar uma regra são explicadas em: [Pontos finais do Serviço Virtual para base de dados Azure SQL][sql-db-vnet-service-endpoint-rule-overview-735r].

> [!TIP]
> Se tudo o que precisa é avaliar ou adicionar o nome do tipo de *ponto* final do Serviço Virtual para base de dados SQL à sua subnet, pode saltar para a frente para o nosso [script PowerShell](#a-verify-subnet-is-endpoint-ps-100)mais direto .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

## <a name="major-cmdlets"></a>Principais cmdlets

Este artigo enfatiza o **novo azSqlServerVirtualNetworkRule** cmdlet que adiciona o ponto final da subnet à lista de controlo de acesso (ACL) do seu servidor de base de dados Azure SQL, criando assim uma regra.

A lista que se segue mostra a sequência de outros cmdlets *principais* que deve executar para se preparar para a sua chamada para **New-AzSqlServerVirtualNetworkRule**. Neste artigo, estas chamadas ocorrem no script 3 "Regra da [rede virtual":](#a-script-30)

1. [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig): Cria um objeto de sub-rede.
2. [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork): Cria a sua rede virtual, dando-lhe a sub-rede.
3. [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig): Atribui um ponto final de serviço virtual à sua subnet.
4. [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetwork): Persiste atualizações feitas à sua rede virtual.
5. [New-AzSqlServerVirtualNetworkRule](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule): Depois da sua subnet ser um ponto final, adiciona a sua subnetcomo regra de rede virtual, no ACL do seu servidor de base de dados Azure SQL.
   - Este cmdlet oferece o parâmetro **-IgnoreMissingVNetServiceEndpoint**, a partir da versão 5.1.1 do Módulo PowerShell Azure RM.

## <a name="prerequisites-for-running-powershell"></a>Pré-requisitos para executar powerShell

- Já pode iniciar sessão no Azure, como por exemplo através do [portal Azure.][http-azure-portal-link-ref-477t]
- Já pode executar scripts PowerShell.

> [!NOTE]
> Certifique-se de que os pontos finais do serviço estão ligados para o VNet/Subnet que pretende adicionar ao seu Servidor caso contrário, a criação da Regra de Firewall VNet falhará.

## <a name="one-script-divided-into-four-chunks"></a>Um guião dividido em quatro pedaços

O nosso script powerShell de demonstração está dividido numa sequência de scripts menores. A divisão facilita a aprendizagem e proporciona flexibilidade. Os scripts devem ser executados na sua sequência indicada. Se não tiver tempo para executar os scripts, a nossa saída de teste real é exibida após o script 4.

<a name="a-script-10" />

### <a name="script-1-variables"></a>Script 1: Variáveis

Este primeiro script PowerShell atribui valores a variáveis. Os scripts subsequentes dependem destas variáveis.

> [!IMPORTANT]
> Antes de executar este guião, pode editar os valores, se quiser. Por exemplo, se já tem um grupo de recursos, talvez queira editar o nome do seu grupo de recursos como o valor atribuído.
>
> O seu nome de subscrição deve ser editado no script.

### <a name="powershell-script-1-source-code"></a>Código de origem do script PowerShell 1

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.
$SubscriptionName = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName $SubscriptionName;

$ResourceGroupName = 'RG-YourNameHere';
$Region            = 'westcentralus';

$VNetName            = 'myVNet';
$SubnetName          = 'mySubnet';
$VNetAddressPrefix   = '10.1.0.0/16';
$SubnetAddressPrefix = '10.1.1.0/24';
$VNetRuleName        = 'myFirstVNetRule-ForAcl';

$SqlDbServerName         = 'mysqldbserver-forvnet';
$SqlDbAdminLoginName     = 'ServerAdmin';
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1';

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Official type name.

Write-Host 'Completed script 1, the "Variables".';
```

<a name="a-script-20" />

### <a name="script-2-prerequisites"></a>Script 2: Pré-requisitos

Este guião prepara-se para o próximo guião, onde está a ação final. Este script cria para si os seguintes itens listados, mas apenas se eles já não existirem. Pode saltar o script 2 se tiver a certeza de que estes itens já existem:

- Grupo de recursos do Azure
- Servidor de base de dados Azure SQL

### <a name="powershell-script-2-source-code"></a>Código fonte do script PowerShell 2

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzResourceGroup `
      -Name $ResourceGroupName `
      -Location $Region;

    $gottenResourceGroup;
}
else { Write-Host "Good, your Resource Group already exists - $ResourceGroupName."; }

$gottenResourceGroup = $null;

###########################################################
## Ensure your Azure SQL Database server already exists. ##
###########################################################

Write-Host "Check whether your Azure SQL Database server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer) {
    Write-Host "Creating the missing Azure SQL Database server - $SqlDbServerName.";
    Write-Host "Gather the credentials necessary to next create an Azure SQL Database server.";

    $sqlAdministratorCredentials = New-Object `
      -TypeName     System.Management.Automation.PSCredential `
      -ArgumentList `
        $SqlDbAdminLoginName, `
        $(ConvertTo-SecureString `
            -String      $SqlDbAdminLoginPassword `
            -AsPlainText `
            -Force `
         );

    if ($null -eq $sqlAdministratorCredentials) {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your Azure SQL Database server.";

    $sqlDbServer = New-AzSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else {
    Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; 
}

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```

<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Script 3: Criar um ponto final e uma regra

Este script cria uma rede virtual com uma subnet. Em seguida, o script atribui o tipo de ponto **final Microsoft.Sql** à sua sub-rede. Finalmente, o script adiciona a sua subnet à lista de controlo de acesso (ACL) do seu servidor de base de dados SQL, criando assim uma regra.

### <a name="powershell-script-3-source-code"></a>Código fonte do script PowerShell 3

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Network-Rule".';
```

<a name="a-script-40" />

## <a name="script-4-clean-up"></a>Script 4: Limpeza

Este guião final elimina os recursos que os scripts anteriores criaram para a demonstração. No entanto, o script pede confirmação antes de eliminar o seguinte:

- Servidor de base de dados Azure SQL
- Grupo de Recursos Azure

Pode executar o script 4 a qualquer momento após o script 1 completar.

### <a name="powershell-script-4-source-code"></a>Código fonte do script PowerShell 4

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzVirtualNetwork `
  -Name              $VNetName `
  -ResourceGroupName $ResourceGroupName `
  -ErrorAction       SilentlyContinue;

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Azure SQL Database server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]';
if ('yes' -eq $yesno) {
    Write-Host "Remove the Azure SQL DB server.";

    Remove-AzSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;

    Write-Host "Remove the Azure Resource Group.";

    Remove-AzResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else {
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```

<a name="a-actual-output" />

<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>Verifique se a sua subnet é um ponto final

Pode ter uma sub-rede que já foi atribuída ao nome do tipo **Microsoft.Sql,** o que significa que já é um ponto final do Serviço Virtual. Você poderia usar o [portal Azure][http-azure-portal-link-ref-477t] para criar uma regra de rede virtual a partir do ponto final.

Ou, pode não ter a certeza se a sua subnet tem o nome do tipo **Microsoft.Sql.** Pode executar o seguinte script PowerShell para tomar estas ações:

1. Verifique se a sua sub-rede tem o nome do tipo **Microsoft.Sql.**
2. Opcionalmente, atribua o nome do tipo se estiver ausente.
    - O script pede-lhe que *confirme,* antes de aplicar o nome do tipo ausente.

### <a name="phases-of-the-script"></a>Fases do script

Aqui estão as fases do script PowerShell:

1. INICIE-SE na sua conta Azure, necessária apenas uma vez por sessão ps.  Atribuir variáveis.
2. Procure a sua rede virtual e, em seguida, a sua subnet.
3. A sua sub-rede está marcada como o tipo de servidor de ponto **final Microsoft.Sql?**
4. Adicione um ponto final de serviço virtual de nome de tipo **Microsoft.Sql,** na sua subnet.

> [!IMPORTANT]
> Antes de executar este script, deve editar os valores atribuídos às variáveis $, perto do topo do script.

### <a name="direct-powershell-source-code"></a>Código fonte PowerShell direto

Este script PowerShell não atualiza nada, a menos que responda sim se lhe for pedido confirmação. O script pode adicionar o nome de tipo **Microsoft.Sql** à sua sub-rede. Mas o script tenta o add apenas se a sua subnet não tiver o nome do tipo.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.
$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.
# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null) {
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null) {
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?
$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++) {
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb) {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null) {
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else {
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

<!-- Link references: -->
[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
