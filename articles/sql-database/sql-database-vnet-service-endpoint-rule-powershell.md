---
title: PowerShell para pontos finais de VNet e regras para únicos e em pool bases de dados SQL do Azure | Documentos da Microsoft
description: Fornece os scripts do PowerShell para criar e gerir pontos finais de serviço Virtual para a sua base de dados do Azure SQL e SQL Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: genemi, vanto
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 6713182003a280c1d53e904209159b55b4ad01c6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57855576"
---
# <a name="powershell--create-a-virtual-service-endpoint-and-vnet-rule-for-sql"></a>PowerShell:  Criar um ponto final de serviço Virtual e uma regra de VNet para o SQL

*Regras de rede virtual* são um recurso de segurança de firewall que controla se o servidor de base de dados para as suas bases de dados individuais e o conjunto elástico no Azure [base de dados SQL](sql-database-technical-overview.md) ou nas bases de dados no [dados do SQL Armazém](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) aceita comunicações que são enviadas as sub-redes específicas nas redes virtuais.

> [!IMPORTANT]
> Este artigo aplica-se ao servidor SQL do Azure e a base de dados SQL e SQL Data Warehouse bases de dados que são criadas no servidor SQL do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse. Este artigo faz *não* aplicam-se a um **instância gerida** implementação na base de dados do Azure SQL porque não tem um ponto de extremidade de serviço associado ao mesmo.

Este artigo fornece e explica um script do PowerShell que executa as ações seguintes:

1. Cria um Microsoft Azure *ponto final de serviço Virtual* na sua sub-rede.
2. Adiciona o ponto final para o firewall do seu servidor de base de dados do Azure SQL, para criar uma *regra de rede virtual*.

Suas motivações para criar uma regra são explicadas em: [Pontos finais de serviço virtual para a base de dados do Azure SQL][sql-db-vnet-service-endpoint-rule-overview-735r].

> [!TIP]
> Se tudo o que precisa avaliar ou adicionar o ponto final de serviço Virtual *nome do tipo* para a base de dados SQL à sua sub-rede, pode avançar diretamente para o nosso mais [direcionar o script do PowerShell](#a-verify-subnet-is-endpoint-ps-100).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é suportado pelo SQL Database do Azure, mas todo o desenvolvimento futuro é para o módulo de Az.Sql. Para estes cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

## <a name="major-cmdlets"></a>Cmdlets de versão principal

Este artigo enfatiza a **New-AzSqlServerVirtualNetworkRule** cmdlet que adiciona o ponto de extremidade de sub-rede para a lista de controlo de acesso (ACL) do seu servidor de base de dados do Azure SQL, criando uma regra.

A lista seguinte mostra a sequência de outros *principais* cmdlets que tem de executar para se preparar para a chamada **New-AzSqlServerVirtualNetworkRule**. Neste artigo, essas chamadas ocorrem no [script 3 "regra de rede Virtual"](#a-script-30):

1. [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig): Cria um objeto de sub-rede.
2. [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork): Cria a rede virtual, dando a ele a sub-rede.
3. [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig): Atribui um ponto de extremidade de serviço Virtual à sua sub-rede.
4. [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetwork): Mantém as atualizações feitas à sua rede virtual.
5. [New-AzSqlServerVirtualNetworkRule](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule): Depois de sua sub-rede é um ponto de extremidade, adiciona a sub-rede como uma regra de rede virtual, a ACL do seu servidor de base de dados do Azure SQL.
   - Este cmdlet oferece o parâmetro **- IgnoreMissingVNetServiceEndpoint**, a partir do módulo do Azure RM PowerShell versão 5.1.1.

## <a name="prerequisites-for-running-powershell"></a>Pré-requisitos para executar o PowerShell

- Pode já iniciar sessão no Azure, tal como através da [portal do Azure][http-azure-portal-link-ref-477t].
- Já pode executar scripts do PowerShell.

> [!NOTE]
> Certifique-se de que pontos finais de serviço são ativados para a VNet/sub-rede que pretende adicionar ao seu servidor caso contrário, a criação da regra de Firewall VNet irá falhar.

## <a name="one-script-divided-into-four-chunks"></a>Dividido em quatro partes de um script

Nossa demonstração de script do PowerShell está dividida numa seqüência de scripts mais pequenos. A divisão facilita a aprendizagem e fornece a flexibilidade. Os scripts devem ser executados na sua sequência indicada. Se não tiver tempo para executar os scripts, nossa saída do teste real é apresentada depois do script 4.

<a name="a-script-10" />

### <a name="script-1-variables"></a>Script 1: Variáveis

Este script do PowerShell primeiro atribui valores às variáveis. Os scripts subsequentes dependem estas variáveis.

> [!IMPORTANT]
> Antes de executar este script, pode editar os valores, se assim o desejar. Por exemplo, se já tiver um grupo de recursos, pode querer editar seu nome de grupo de recursos como o valor atribuído.
>
> O nome da sua subscrição deve ser editado para o script.

### <a name="powershell-script-1-source-code"></a>Código de origem de 1 de script do PowerShell

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

### <a name="script-2-prerequisites"></a>Script de 2: Pré-requisitos

Este script prepara para o script seguinte, em que é a ação de ponto final. Este script cria para, o seguinte listado itens, mas apenas se eles ainda não existir. Pode ignorar script 2 se tiver a certeza de que estes itens ainda existam:

- Grupo de recursos do Azure
- Servidor de base de dados SQL do Azure

### <a name="powershell-script-2-source-code"></a>Código de origem de 2 de script do PowerShell

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

if ($null -eq $sqlDbServer)
{
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

    if ($null -eq $sqlAdministratorCredentials)
    {
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
else { Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; }

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```

<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Script de 3: Criar um ponto de extremidade e uma regra

Este script cria uma rede virtual com uma sub-rede. Em seguida, atribui o script a **Microsoft. SQL** tipo de ponto final à sua sub-rede. Por fim o script adiciona a sub-rede para a lista de controlo de acesso (ACL) do seu servidor de base de dados SQL, criando uma regra.

### <a name="powershell-script-3-source-code"></a>Código de origem de 3 de script do PowerShell

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

## <a name="script-4-clean-up"></a>Script de 4: Limpeza

Este script final elimina os recursos que os scripts anteriores criados para a demonstração. No entanto, o script pede-lhe confirmação antes de ele elimina o seguinte:

- Servidor de base de dados SQL do Azure
- Grupo de Recursos do Azure

Pode executar o script 4 a qualquer momento após a conclusão do script 1.

### <a name="powershell-script-4-source-code"></a>Código de origem de 4 de script do PowerShell

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
if ('yes' -eq $yesno)
{
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
else
{
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```

<a name="a-actual-output" />

## <a name="actual-output-from-scripts-1-through-4"></a>Saída real a partir de 1 a 4 de scripts

É apresentado em seguida, o resultado de nosso teste, execute num formato abreviado. O resultado poderá ser útil caso não deseje, na verdade, agora a executar os scripts do PowerShell.

```cmd
[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s1-variables.ps1
Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: yes


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount :



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s2-prerequisites.ps1
Check whether your Resource Group already exists.
Creating your missing Resource Group - RG-YourNameHere.


ResourceGroupName : RG-YourNameHere
Location          : westcentralus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/22222222-2222-2222-2222-222222222222/resourceGroups/RG-YourNameHere

Check whether your Azure SQL Database server already exists.
Creating the missing Azure SQL Database server - mysqldbserver-forvnet.
Gather the credentials necessary to next create an Azure SQL Database server.
Create your Azure SQL Database server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword :
ServerVersion            : 12.0
Tags                     :
Identity                 :

Completed script 2, the "Prerequisites".

[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s3-vnet-rule.ps1
Define a subnet 'mySubnet', to be given soon to a virtual network.
Create a virtual network 'myVNet'.   Give the subnet to the virtual network that we created.
WARNING: The output object type of this cmdlet will be modified in a future release.
Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
Persist the updates made to the virtual network > subnet.

Get the subnet object.
Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.
ProvisioningState Service       Locations
----------------- -------       ---------
Succeeded         Microsoft.Sql {westcentralus}

Verify that the rule is in the SQL DB ACL.

Completed script 3, the "Virtual-Network-Rule".

[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s4-clean-up.ps1
Delete the rule from the SQL DB ACL.

Delete the endpoint from the subnet.


Delete the virtual network (thus also deletes the subnet).
CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]: yes
Remove the Azure SQL DB server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword :
ServerVersion            : 12.0
Tags                     :
Identity                 :

Remove the Azure Resource Group.
True
Completed script 4, the "Clean-Up".
```

Esse é o fim de nosso script de PowerShell principal.

<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>Certifique-se de que a sub-rede é um ponto final

Pode ter uma sub-rede que já foi atribuída a **Microsoft. SQL** nome do tipo, que significa que já é um ponto de extremidade de serviço Virtual. Pode utilizar o [portal do Azure] [ http-azure-portal-link-ref-477t] para criar uma regra de rede virtual a partir do ponto final.

Também pode ser não sabe se a sub-rede tem o **Microsoft. SQL** nome do tipo. Pode executar o seguinte script do PowerShell para realizar estas ações:

1. Determinar se a sub-rede tem o **Microsoft. SQL** nome do tipo.
2. Opcionalmente, atribua o nome do tipo se ele está ausente.
    - O script pede para *confirmar*, antes de aplicar o tipo de ausente nome.

### <a name="phases-of-the-script"></a>Fases do script

Aqui estão as fases do script do PowerShell:

1. Inicie sessão na sua conta do Azure, necessário apenas uma vez por sessão de PS.  Atribua variáveis.
2. Procurar para a rede virtual e, em seguida, para a sua sub-rede.
3. A sub-rede é marcada como **Microsoft. SQL** tipo de servidor de ponto final?
4. Adicionar um ponto de extremidade de serviço Virtual do nome do tipo **Microsoft. SQL**, na sua sub-rede.

> [!IMPORTANT]
> Antes de executar este script, tem de editar os valores atribuídos para as variáveis $, junto à parte superior do script.

### <a name="direct-powershell-source-code"></a>Direcionar o código-fonte do PowerShell

Este script não atualiza qualquer coisa, exceto se responder Sim, se o PowerShell é solicita confirmação. O script pode adicionar o nome do tipo **Microsoft. SQL** à sua sub-rede. Mas o script tenta adicionar apenas se a sub-rede não tem o nome do tipo.

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

if ($vnet -eq $null)
{
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null)
{
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?

$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++)
{
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb)
    {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null)
{
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else
{
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

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{ $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

### <a name="actual-output"></a>Saída real

O bloco seguinte mostra nossos comentários real (com as edições cosméticas).

```powershell
<# Our output example (with cosmetic edits), when the subnet was already tagged:

Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: no


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount :


ProvisioningState : Succeeded
Service           : Microsoft.Sql
Locations         : {westcentralus}

Good: Subnet found, and is already tagged as an endpoint of type 'Microsoft.Sql'.
#>
```

<!-- Link references: -->

[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md

[http-azure-portal-link-ref-477t]: https://portal.azure.com/
