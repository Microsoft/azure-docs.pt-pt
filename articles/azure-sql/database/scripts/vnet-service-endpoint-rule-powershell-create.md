---
title: PowerShell para pontos finais vNet e regras para bases de dados individuais e agnetizadas
description: Fornece scripts PowerShell para criar e gerir pontos finais de Serviço Virtual para a sua Base de Dados Azure SQL e Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 04/17/2019
ms.custom: sqldbrb=1
tags: azure-synapse
ms.openlocfilehash: 76a1d3aaadcbd1b15966a84f5dd2fe876f82c43a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177628"
---
# <a name="powershell-create-a-virtual-service-endpoint-and-vnet-rule-for-azure-sql-database"></a>PowerShell: Criar um ponto final de serviço virtual e regra VNet para base de dados Azure SQL
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

*As regras de rede virtual* são uma funcionalidade de segurança de firewall que controla se o [servidor SQL lógico](../logical-servers.md) para as bases de dados [do Azure SQL Database,](../sql-database-paas-overview.md) piscinas elásticas ou bases de dados em [Azure Synapse](../../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) aceitam comunicações enviadas a partir de sub-redes específicas em redes virtuais.

> [!IMPORTANT]
> Este artigo aplica-se à Base de Dados Azure SQL, incluindo a Azure Synapse (anteriormente SQL DW). Para simplificar, o termo Azure SQL Database neste artigo aplica-se a bases de dados pertencentes à Base de Dados Azure SQL ou a Azure Synapse. Este artigo *não* se aplica à Azure SQL Managed Instance porque não tem um ponto final de serviço associado a ele.

Este artigo demonstra um script PowerShell que toma as seguintes ações:

1. Cria um ponto *de terminação do Serviço Virtual* Microsoft Azure na sua sub-rede.
2. Adiciona o ponto final à firewall do seu servidor, para criar uma *regra de rede virtual*.

Para mais informações, consulte [os pontos finais do Serviço Virtual para a Base de Dados Azure SQL][sql-db-vnet-service-endpoint-rule-overview-735r].

> [!TIP]
> Se tudo o que precisa é avaliar ou adicionar o *nome de ponto* final de Serviço Virtual para Base de Dados Azure SQL à sua sub-rede, pode antecipar-se ao nosso script [PowerShell](#a-verify-subnet-is-endpoint-ps-100)mais direto .

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para os [ `Az.Sql` Cmdlets](/powershell/module/az.sql). Para o módulo mais antigo, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

## <a name="major-cmdlets"></a>Grandes cmdlets

Este artigo enfatiza o [cmdlet **New-AzSqlServerVirtualNetworkRule**](/powershell/module/az.sql/new-azsqlservervirtualnetworkrule) que adiciona o ponto final da sub-rede à lista de controlo de acesso (ACL) do seu servidor, criando assim uma regra.

A lista a seguir mostra a sequência de outros cmdlets *principais* que deve executar para preparar a sua chamada para **New-AzSqlServerVirtualNetworkRule**. Neste artigo, estas chamadas ocorrem no [script 3 "Regra da rede virtual"](#a-script-30):

1. [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig): Cria um objeto de sub-rede.
2. [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork): Cria a sua rede virtual, dando-lhe a sub-rede.
3. [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig): Atribui um ponto final de serviço virtual à sua sub-rede.
4. [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork): Persiste nas atualizações feitas à sua rede virtual.
5. [Nova-AzSqlServerVirtualNetworkRule](/powershell/module/az.sql/new-azsqlservervirtualnetworkrule): Depois da sub-rede ser um ponto final, adiciona a sua sub-rede como regra de rede virtual, no ACL do seu servidor.
   - Este cmdlet Oferece o parâmetro **-IgnoreMissingVNetServiceEndpoint,** a partir da versão 5.1.1 do Módulo PowerShell Azure RM.

## <a name="prerequisites-for-running-powershell"></a>Pré-requisitos para executar PowerShell

- Já pode iniciar sessão no Azure, como por exemplo através do [portal Azure][http-azure-portal-link-ref-477t].
- Já pode executar scripts PowerShell.

> [!NOTE]
> Certifique-se de que os pontos finais de serviço estão ligados para o VNet/Subnet que pretende adicionar ao seu Servidor, caso contrário, a criação da Regra de Firewall VNet falhará.

## <a name="one-script-divided-into-four-chunks"></a>Um guião dividido em quatro pedaços

O nosso guião PowerShell de demonstração está dividido numa sequência de scripts menores. A divisão facilita a aprendizagem e proporciona flexibilidade. Os scripts devem ser executados na sequência indicada. Se não tiver tempo para executar os scripts, a nossa saída de teste real é exibida após o script 4.

<a name="a-script-10"></a>

### <a name="script-1-variables"></a>Script 1: Variáveis

Este primeiro script PowerShell atribui valores a variáveis. Os scripts subsequentes dependem destas variáveis.

> [!IMPORTANT]
> Antes de executar este script, pode editar os valores, se quiser. Por exemplo, se já tiver um grupo de recursos, talvez queira editar o nome do seu grupo de recursos como o valor atribuído.
>
> O nome da sua subscrição deve ser editado no script.

### <a name="powershell-script-1-source-code"></a>Código fonte do script PowerShell 1

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]'
if ('yes' -eq $yesno) { Connect-AzAccount }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.
$SubscriptionName = 'yourSubscriptionName'
Select-AzSubscription -SubscriptionName $SubscriptionName

$ResourceGroupName = 'RG-YourNameHere'
$Region = 'westcentralus'

$VNetName = 'myVNet'
$SubnetName = 'mySubnet'
$VNetAddressPrefix = '10.1.0.0/16'
$SubnetAddressPrefix = '10.1.1.0/24'
$VNetRuleName = 'myFirstVNetRule-ForAcl'

$SqlDbServerName = 'mysqldbserver-forvnet'
$SqlDbAdminLoginName = 'ServerAdmin'
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql'  # Official type name.

Write-Host 'Completed script 1, the "Variables".'
```

<a name="a-script-20"></a>

### <a name="script-2-prerequisites"></a>Script 2: Pré-requisitos

Este guião prepara-se para o próximo script, onde está a ação do ponto final. Este script cria para si os seguintes itens listados, mas apenas se eles já não existirem. Pode saltar o script 2 se tiver a certeza de que estes itens já existem:

- Grupo de recursos do Azure
- Servidor lógico SQL

### <a name="powershell-script-2-source-code"></a>Código fonte do script PowerShell 2

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists."

$gottenResourceGroup = $null
$gottenResourceGroup = Get-AzResourceGroup -Name $ResourceGroupName -ErrorAction SilentlyContinue

if ($null -eq $gottenResourceGroup) {
    Write-Host "Creating your missing Resource Group - $ResourceGroupName."
    New-AzResourceGroup -Name $ResourceGroupName -Location $Region
} else {
    Write-Host "Good, your Resource Group already exists - $ResourceGroupName."
}

$gottenResourceGroup = $null

###########################################################
## Ensure your server already exists. ##
###########################################################

Write-Host "Check whether your server already exists."

$sqlDbServer = $null
$azSqlParams = @{
    ResourceGroupName = $ResourceGroupName
    ServerName        = $SqlDbServerName
    ErrorAction       = 'SilentlyContinue'
}
$sqlDbServer = Get-AzSqlServer @azSqlParams

if ($null -eq $sqlDbServer) {
    Write-Host "Creating the missing server - $SqlDbServerName."
    Write-Host "Gather the credentials necessary to next create a server."

    $sqlAdministratorCredentials = [pscredential]::new($SqlDbAdminLoginName,(ConvertTo-SecureString -String $SqlDbAdminLoginPassword -AsPlainText -Force))

    if ($null -eq $sqlAdministratorCredentials) {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending."
        return
    }

    Write-Host "Create your server."

    $sqlSrvParams = @{
        ResourceGroupName           = $ResourceGroupName
        ServerName                  = $SqlDbServerName
        Location                    = $Region
        SqlAdministratorCredentials = $sqlAdministratorCredentials
    }
    New-AzSqlServer @sqlSrvParams
} else {
    Write-Host "Good, your server already exists - $SqlDbServerName."
}

$sqlAdministratorCredentials = $null
$sqlDbServer = $null

Write-Host 'Completed script 2, the "Prerequisites".'
```

<a name="a-script-30"></a>

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Script 3: Criar um ponto final e uma regra

Este script cria uma rede virtual com uma sub-rede. Em seguida, o script atribui o tipo de ponto final **Microsoft.Sql** à sua sub-rede. Finalmente, o script adiciona a sua sub-rede à lista de controlo de acesso (ACL), criando assim uma regra.

### <a name="powershell-script-3-source-code"></a>Código fonte do script PowerShell 3

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network."

$subnetParams = @{
    Name            = $SubnetName
    AddressPrefix   = $SubnetAddressPrefix
    ServiceEndpoint = $ServiceEndpointTypeName_SqlDb
}
$subnet = New-AzVirtualNetworkSubnetConfig @subnetParams

Write-Host "Create a virtual network '$VNetName'.`nGive the subnet to the virtual network that we created."

$vnetParams = @{
    Name              = $VNetName
    AddressPrefix     = $VNetAddressPrefix
    Subnet            = $subnet
    ResourceGroupName = $ResourceGroupName
    Location          = $Region
}
$vnet = New-AzVirtualNetwork @vnetParams

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet."

$vnetSubParams = @{
    Name            = $SubnetName
    AddressPrefix   = $SubnetAddressPrefix
    VirtualNetwork  = $vnet
    ServiceEndpoint = $ServiceEndpointTypeName_SqlDb
}
$vnet = Set-AzVirtualNetworkSubnetConfig @vnetSubParams

Write-Host "Persist the updates made to the virtual network > subnet."

$vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet

$vnet.Subnets[0].ServiceEndpoints  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object."

$vnet = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VNetName

$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vnet

Write-Host "Add the subnet .Id as a rule, into the ACLs for your server."

$ruleParams = @{
    ResourceGroupName      = $ResourceGroupName
    ServerName             = $SqlDbServerName
    VirtualNetworkRuleName = $VNetRuleName
    VirtualNetworkSubnetId = $subnet.Id
}
New-AzSqlServerVirtualNetworkRule @ruleParams 

Write-Host "Verify that the rule is in the SQL Database ACL."

$rule2Params = @{
    ResourceGroupName      = $ResourceGroupName
    ServerName             = $SqlDbServerName
    VirtualNetworkRuleName = $VNetRuleName
}
Get-AzSqlServerVirtualNetworkRule @rule2Params

Write-Host 'Completed script 3, the "Virtual-Network-Rule".'
```

<a name="a-script-40"></a>

## <a name="script-4-clean-up"></a>Script 4: Limpeza

Este script final elimina os recursos que os scripts anteriores criaram para a demonstração. No entanto, o script pede confirmação antes de eliminar o seguinte:

- Servidor lógico SQL
- Grupo de Recursos Azure

Pode executar o script 4 sempre que o script 1 estiver concluído.

### <a name="powershell-script-4-source-code"></a>Código fonte do script PowerShell 4

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL Database ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL Database ACL."

$removeParams = @{
    ResourceGroupName      = $ResourceGroupName
    ServerName             = $SqlDbServerName
    VirtualNetworkRuleName = $VNetRuleName
    ErrorAction            = 'SilentlyContinue'
}
Remove-AzSqlServerVirtualNetworkRule @removeParams

Write-Host "Delete the endpoint from the subnet."

$vnet = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VNetName

Remove-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vnet

Write-Host "Delete the virtual network (thus also deletes the subnet)."

$removeParams = @{
    Name              = $VNetName
    ResourceGroupName = $ResourceGroupName
    ErrorAction       = 'SilentlyContinue'
}
Remove-AzVirtualNetwork @removeParams

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Logical SQL server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your server AND your resource group?  [yes/no]'
if ('yes' -eq $yesno) {
    Write-Host "Remove the server."

    $removeParams = @{
        ServerName        = $SqlDbServerName
        ResourceGroupName = $ResourceGroupName
        ErrorAction       = 'SilentlyContinue'
    }
    Remove-AzSqlServer @removeParams

    Write-Host "Remove the Azure Resource Group."
    
    Remove-AzResourceGroup -Name $ResourceGroupName -ErrorAction SilentlyContinue
} else {
    Write-Host "Skipped over the DELETE of SQL Database and resource group."
}

Write-Host 'Completed script 4, the "Clean-Up".'
```

<a name="a-actual-output"></a>

<a name="a-verify-subnet-is-endpoint-ps-100"></a>

## <a name="verify-your-subnet-is-an-endpoint"></a>Verifique se a sua sub-rede é um ponto final

Pode ter uma sub-rede que já foi atribuída ao nome do tipo **Microsoft.Sql,** o que significa que já é um ponto final de Serviço Virtual. Pode utilizar o [portal Azure][http-azure-portal-link-ref-477t] para criar uma regra de rede virtual a partir do ponto final.

Ou, pode não ter a certeza se a sua sub-rede tem o nome do tipo **Microsoft.Sql.** Pode executar o seguinte script PowerShell para tomar estas ações:

1. Verificar se a sua sub-rede tem o nome do tipo **Microsoft.Sql.**
2. Opcionalmente, atribua o nome do tipo se estiver ausente.
    - O script pede-lhe que *confirme*, antes de aplicar o nome do tipo ausente.

### <a name="phases-of-the-script"></a>Fases do script

Aqui estão as fases do script PowerShell:

1. ACSSa na sua conta Azure, necessária apenas uma vez por sessão ps.  Atribuir variáveis.
2. Procure a sua rede virtual e, em seguida, a sua sub-rede.
3. A sua sub-rede está marcada como tipo de servidor de ponto final **Microsoft.Sql?**
4. Adicione um ponto final de serviço virtual de nome tipo **Microsoft.Sql,** na sua sub-rede.

> [!IMPORTANT]
> Antes de executar este script, deve editar os valores atribuídos às variáveis de $, perto do topo do script.

### <a name="direct-powershell-source-code"></a>Código fonte PowerShell direto

Este script PowerShell não atualiza nada, a menos que responda sim se lhe pedir confirmação. O script pode adicionar o nome tipo **Microsoft.Sql** à sua sub-rede. Mas o script tenta o add apenas se a sua sub-rede não tiver o nome do tipo.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.
$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]'
if ('yes' -eq $yesno) { Connect-AzAccount }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName = 'yourSubscriptionName'
Select-AzSubscription -SubscriptionName "$SubscriptionName"

$ResourceGroupName = 'yourRGName'
$VNetName = 'yourVNetName'
$SubnetName = 'yourSubnetName'
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.' # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql'  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.
# Search for the virtual network.
$vnet = $null
$vnet = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VNetName

if ($vnet -eq $null) {
    Write-Host "Caution: No virtual network found by the name '$VNetName'."
    return
}

$subnet = $null
for ($nn = 0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $subnet = $vnet.Subnets[$nn]
    if ($subnet.Name -eq $SubnetName) { break }
    $subnet = $null
}

if ($null -eq $subnet) {
    Write-Host "Caution: No subnet found by the name '$SubnetName'"
    Return
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?
$endpointMsSql = $null
for ($nn = 0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++) {
    $endpointMsSql = $subnet.ServiceEndpoints[$nn]
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb) {
        $endpointMsSql
        break
    }
    $endpointMsSql = $null
}

if ($null -eq $endpointMsSql) {
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'."
    return
} else {
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'."

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]'
    if ('no' -eq $yesno) { return }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.
$setParams = @{
    Name            = $SubnetName
    AddressPrefix   = $SubnetAddressPrefix
    VirtualNetwork  = $vnet
    ServiceEndpoint = $ServiceEndpointTypeName_SqlDb
}
$vnet = Set-AzVirtualNetworkSubnetConfig @setParams

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet

for ($nn = 0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $vnet.Subnets[0].ServiceEndpoints # Display.
}
```

<!-- Link references: -->
[sql-db-vnet-service-endpoint-rule-overview-735r]:../vnet-service-endpoint-rule-overview.md
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
