---
title: Configure um grupo de disponibilidade (PowerShell & Az CLI)
description: Utilize o PowerShell ou o Azure CLI para criar o cluster de failover do Windows, o ouvinte do grupo de disponibilidade e o balançador de carga interno num SQL Server VM em Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 865ee3a5aeb8a2dd06d8759ba04d02259d2b4bee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359970"
---
# <a name="use-powershell-or-az-cli-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>Utilize o PowerShell ou o Az CLI para configurar um grupo de disponibilidade para o SQL Server em Azure VM 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo descreve como utilizar o [PowerShell](/powershell/scripting/install/installing-powershell) ou o [Azure CLI](/cli/azure/sql/vm) para implementar um cluster de failover do Windows, adicionar VMs sql server ao cluster e criar o equilibrador de carga interno e o ouvinte para um grupo de disponibilidade Always On. 

A implementação do grupo de disponibilidade ainda é feita manualmente através do SQL Server Management Studio (SSMS) ou da Transact-SQL (T-SQL). 

Embora este artigo utilize o PowerShell e o CLI Az para configurar o ambiente de grupo de disponibilidade, também é possível fazê-lo a partir do [portal Azure](availability-group-azure-portal-configure.md), utilizando [modelos Azure Quickstart](availability-group-quickstart-template-configure.md), ou [Manualmente](availability-group-manually-configure-tutorial.md) também. 

## <a name="prerequisites"></a>Pré-requisitos

Para configurar um grupo de disponibilidade Always On, tem de ter os seguintes pré-requisitos: 

- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- Um grupo de recursos com um controlador de domínio. 
- Um ou mais VMs unidos ao domínio [em Azure executando SQL Server 2016 (ou posteriormente) Edição Empresarial](./create-sql-vm-portal.md) no *mesmo* conjunto de disponibilidade ou *diferentes* zonas de disponibilidade que tenham sido [registadas com a extensão do Agente IAAS SQL](sql-agent-extension-manually-register-single-vm.md).  
- A versão mais recente do [PowerShell](/powershell/scripting/install/installing-powershell) ou do [Azure CLI](/cli/azure/install-azure-cli). 
- Dois endereços IP disponíveis (não utilizados por nenhuma entidade). Um é para o equilibrador de carga interno. O outro é para o ouvinte do grupo de disponibilidade dentro da mesma sub-rede que o grupo de disponibilidade. Se estiver a utilizar um equilibrador de carga existente, só precisa de um endereço IP disponível para o ouvinte do grupo de disponibilidade. 

## <a name="permissions"></a>Permissões

Necessita das seguintes permissões de conta para configurar o grupo always on disponibilidade utilizando o Azure CLI: 

- Uma conta de utilizador de domínio existente que tem a permissão **de Objeto de Computador** criar no domínio. Por exemplo, uma conta de administração de domínio normalmente tem permissão suficiente (por exemplo: account@domain.com ). _Esta conta também deve fazer parte do grupo de administradores locais em cada VM para criar o cluster._
- A conta de utilizador de domínio que controla o SQL Server. 
 
## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento 

O aglomerado precisa de uma conta de armazenamento para agir como testemunha da nuvem. Pode utilizar qualquer conta de armazenamento existente, ou pode criar uma nova conta de armazenamento. Se quiser utilizar uma conta de armazenamento existente, avance para a secção seguinte. 

O seguinte código corta-códigos cria a conta de armazenamento: 

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Pode ver o erro `az sql: 'vm' is not in the 'az sql' command group` se estiver a usar uma versão desatualizada do Azure CLI. Descarregue a [versão mais recente do Azure CLI](/cli/azure/install-azure-cli-windows) para ultrapassar este erro.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the storage account
# example: New-AzStorageAccount -ResourceGroupName SQLVM-RG -Name cloudwitness `
#    -SkuName Standard_LRS -Location West US -Kind StorageV2 `
#    -AccessTier Hot -EnableHttpsTrafficOnly

New-AzStorageAccount -ResourceGroupName <resource group name> -Name <name> `
    -SkuName Standard_LRS -Location <region> -Kind StorageV2 `
    -AccessTier Hot -EnableHttpsTrafficOnly
```

---

## <a name="define-cluster-metadata"></a>Definir metadados de cluster

O grupo de comando Azure CLI [az sql vm](/cli/azure/sql/vm/group) gere os metadados do serviço Windows Server Failover Cluster (WSFC) que acolhe o grupo de disponibilidade. Os metadados de cluster incluem o domínio do Diretório Ativo, contas de cluster, contas de armazenamento a serem usadas como testemunha em nuvem e a versão SQL Server. Utilize [o grupo az sql vm criar](/cli/azure/sql/vm/group#az-sql-vm-group-create) para definir os metadados para WSFC de modo que quando o primeiro SQL Server VM é adicionado, o cluster é criado como definido. 

O seguinte código de corte define os metadados para o cluster:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Define the cluster metadata
# example: $group = New-AzSqlVMGroup -Name Cluster -Location West US ' 
#  -ResourceGroupName SQLVM-RG -Offer SQL2017-WS2016
#  -Sku Enterprise -DomainFqdn domain.com -ClusterOperatorAccount vmadmin@domain.com
#  -ClusterBootstrapAccount vmadmin@domain.com  -SqlServiceAccount sqlservice@domain.com 
#  -StorageAccountUrl '<ex:https://cloudwitness.blob.core.windows.net/>' `
#  -StorageAccountPrimaryKey '4Z4/i1Dn8/bpbseyWX'

$group = New-AzSqlVMGroup -Name <name> -Location <regio> 
  -ResourceGroupName <resource group name> -Offer <SQL201?-WS201?> 
  -Sku Enterprise -DomainFqdn <FQDN> -ClusterOperatorAccount <domain account> 
  -ClusterBootstrapAccount <domain account>  -SqlServiceAccount <service account> 
  -StorageAccountUrl '<ex:StorageAccountUrl>' `
  -StorageAccountPrimaryKey '<PublicKey>'
```

---

## <a name="add-vms-to-the-cluster"></a>Adicione VMs ao cluster

Adicionar o primeiro SQL Server VM ao cluster cria o cluster. O comando [az sql vm add-to-group](/cli/azure/sql/vm#az-sql-vm-add-to-group) cria o cluster com o nome anteriormente dado, instala a função de cluster nos VMs do SQL Server e adiciona-os ao cluster. As utilizações subsequentes do `az sql vm add-to-group` comando adicionam mais VMs do Servidor SQL ao cluster recém-criado. 

O seguinte corte de código cria o cluster e adiciona-lhe o primeiro SQL Server VM: 

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
Utilize este comando para adicionar quaisquer outros VMs do SqL Server ao cluster. Modifique apenas o `-n` parâmetro para o nome VM do servidor SQL. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Add SQL Server VMs to cluster
# example: $sqlvm1 = Get-AzSqlVM -Name SQLVM1 -ResourceGroupName SQLVM-RG
# $sqlvm2 = Get-AzSqlVM -Name SQLVM2  -ResourceGroupName SQLVM-RG

# $sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

# $sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  - ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

$sqlvm1 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
$sqlvm2 = Get-AzSqlVM -Name <VM2 Name> -ResourceGroupName <Resource Group Name>

$sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm1.ResourceId -SqlVM $sqlvmconfig1

$sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm2.ResourceId -SqlVM $sqlvmconfig2
```

---


## <a name="validate-cluster"></a>Validar o cluster 

Para que um cluster de failover seja suportado pela Microsoft, tem de passar na validação do cluster. Ligue-se ao VM utilizando o seu método preferido, como o Remote Desktop Protocol (RDP) e valide que o seu cluster passa a validação antes de prosseguir. Se não o fizer, deixa o seu aglomerado num estado não apoiado. 

Pode validar o cluster utilizando o Failover Cluster Manager (FCM) ou o seguinte comando PowerShell:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

## <a name="create-availability-group"></a>Criar grupo de disponibilidade

Crie manualmente o grupo de disponibilidade como normalmente faria, utilizando [o SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)ou [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Não* crie um ouvinte neste momento porque isto é feito através do CLI Azure nas seguintes secções.  

## <a name="create-internal-load-balancer"></a>Criar equilibrador de carga interno

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

O ouvinte do grupo Always On está disponível requer uma instância interna do Balançador de Carga Azure. O equilibrador de carga interno fornece um endereço IP "flutuante" para o ouvinte do grupo de disponibilidade que permite uma maior falha e reconexão. Se os VMs do Servidor SQL de um grupo de disponibilidade fizerem parte do mesmo conjunto de disponibilidade, pode utilizar um equilibrador de carga Básico. Caso contrário, é necessário utilizar um equilibrador de carga Standard.  

> [!NOTE]
> O balançador de carga interno deve estar na mesma rede virtual que as instâncias VM do SQL Server. 

O seguinte corte de código cria o equilibrador de carga interno:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the internal load balancer
# example: New-AzLoadBalancer -name sqlILB -ResourceGroupName SQLVM-RG  `
#  -Sku Standard -Location West US

New-AzLoadBalancer -name sqlILB -ResourceGroupName <resource group name> `
   -Sku Standard -Location <region>
```

---

>[!IMPORTANT]
> O recurso IP público para cada SQL Server VM deve ter um SKU Standard para ser compatível com o balanceador de carga Standard. Para determinar o SKU do recurso IP público do seu VM, vá ao **Grupo de Recursos,** selecione o seu recurso **de endereço IP público** para o VM do servidor SQL desejado e localize o valor sob **SKU** no painel **de visão geral.**  

## <a name="create-listener"></a>Criar ouvinte

Depois de criar manualmente o grupo de disponibilidade, pode criar o ouvinte utilizando [um az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener#az-sql-vm-group-ag-listener-create). 

O *ID do recurso sub-rede* é o valor de `/subnets/<subnetname>` anexado ao ID de recursos do recurso de rede virtual. Para identificar o ID do recurso sub-rede:
   1. Vá ao seu grupo de recursos no [portal Azure](https://portal.azure.com). 
   1. Selecione o recurso de rede virtual. 
   1. Selecione **Propriedades** no painel **de definições.** 
   1. Identifique o ID de recursos para a rede virtual e `/subnets/<subnetname>` apense-o até ao fim para criar o ID de recursos da sub-rede. Por exemplo:
      - O seu ID de recursos de rede virtual é: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - O nome da sua sub-rede é: `default`
      - Portanto, o seu ID de recurso de sub-rede é: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


O seguinte corte de código cria o ouvinte do grupo de disponibilidade:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive

# example: New-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
#    -AvailabilityGroupName SQLAG  -GroupName Cluster `
#    -IpAddress 10.0.0.27 -LoadBalancerResourceId sqlilb `
#    -ProbePort 59999 `
#    -SubnetId /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#    -SqlVirtualMachineId sqlvm1 sqlvm2


New-AzAvailabilityGroupListener -Name <listener name> -ResourceGroupName <resource group name> `
   -AvailabilityGroupName <availability group name> -GroupName <cluster name> `
   -IpAddress <ag listener IP address> -LoadBalancerResourceId <lbid> `
   -ProbePort <Load Balancer probe port, default 59999> `
   -SubnetId <subnet resource id> `
   -SqlVirtualMachineId <names of SQL VM's hosting AG replicas>
```

---

## <a name="modify-number-of-replicas"></a>Modificar o número de réplicas 
Há uma camada adicional de complexidade quando você está implantando um grupo de disponibilidade para SQL Server VMs hospedados em Azure. O fornecedor de recursos e o grupo de máquinas virtuais gerem agora os recursos. Como tal, quando está a adicionar ou a remover réplicas no grupo de disponibilidade, há um passo adicional para atualizar os metadados do ouvinte com informações sobre os VMs do Servidor SQL. Ao modificar o número de réplicas no grupo de disponibilidade, também deve utilizar o comando [de atualização ag-listener do grupo az sql vm](/cli/azure/sql/vm/group/ag-listener#az-sql-vm-group-ag-listener-update) para atualizar o ouvinte com os metadados dos VMs do SqL Server. 


### <a name="add-a-replica"></a>Adicione uma réplica

Para adicionar uma nova réplica ao grupo de disponibilidade:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Adicione o SQL Server VM ao grupo de cluster:
   ```azurecli-interactive

   # Add the SQL Server VM to the cluster group
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```

1. Utilize o SQL Server Management Studio para adicionar a instância do SQL Server como uma réplica dentro do grupo de disponibilidade.
1. Adicione os metadados VM do servidor SQL ao ouvinte:

   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Adicione o SQL Server VM ao grupo de cluster:

   ```powershell-interactive
   # Add the SQL Server VM to the cluster group
   # example: $sqlvm3 = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG 
   # $group = Get-AzSqlVMGroup -ResourceGroupName SQLVM-RG -Name Cluster
   # $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   #  -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
   #  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
   #  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

   $sqlvm3 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
   $group = Get-AzSqlVMGroup  -ResourceGroupName <resource group name> -Name <name>
   $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

   Update-AzSqlVM -ResourceId $sqlvm3.ResourceId -SqlVM $sqlvmconfig3
   ```

1. Utilize o SQL Server Management Studio para adicionar a instância do SQL Server como uma réplica dentro do grupo de disponibilidade.
1. Adicione os metadados VM do servidor SQL ao ouvinte:

   ```powershell-interactive
   # Update the listener metadata with the new VM
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #   -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs, along with new SQL VM> 

   ```

---

### <a name="remove-a-replica"></a>Remover uma réplica

Para remover uma réplica do grupo de disponibilidade:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Remova a réplica do grupo de disponibilidade utilizando o SQL Server Management Studio. 
1. Remova os metadados VM do servidor SQL do ouvinte:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Remova o SQL Server VM do cluster:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Remova a réplica do grupo de disponibilidade utilizando o SQL Server Management Studio. 
1. Remova os metadados VM do servidor SQL do ouvinte:

   ```powershell-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #  -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs that remain>

   ```
1. Remova o SQL Server VM do cluster:

   ```powershell-interactive
   # Remove the SQL VM from the cluster
   # example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
   #  $sqlvm. SqlVirtualMachineGroup = ""
   #  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

   $sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
      $sqlvm. SqlVirtualMachineGroup = ""
    
    Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
   ```

---

## <a name="remove-listener"></a>Remover ouvinte
Se mais tarde precisar de remover o ouvinte do grupo de disponibilidade configurado com o Azure CLI, deve passar pela extensão do Agente IAAS SQL. Como o ouvinte está registado através da extensão sql IaaS Agent, apenas a sua eliminação através do SQL Server Management Studio é insuficiente. 

O melhor método é eliminá-lo através da extensão sql IaaS Agent utilizando o seguinte corte de código no Azure CLI. Ao fazê-lo, remove os metadados do grupo de disponibilidade da extensão do Agente IAAS SQL. Também elimina fisicamente o ouvinte do grupo de disponibilidade. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Remove the availability group listener
# example: Remove-AzAvailabilityGroupListener -Name AGListener `
#   -ResourceGroupName SQLVM-RG -SqlVMGroupName Cluster

Remove-AzAvailabilityGroupListener -Name <Listener> `
   -ResourceGroupName <Resource Group Name> -SqlVMGroupName <cluster name>
```

---

## <a name="remove-cluster"></a>Remover o aglomerado

Retire todos os nós do cluster para destruí-lo e, em seguida, remova os metadados de cluster da extensão do Agente IAAS SQL. Pode fazê-lo utilizando o Azure CLI ou o PowerShell. 


# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, remova todos os VMs do SqL Server do cluster: 

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

Se estes são os únicos VMs no aglomerado, então o aglomerado será destruído. Se houver outros VMs no cluster para além dos VMs do SQL Server que foram removidos, os outros VMs não serão removidos e o cluster não será destruído. 

Em seguida, remova os metadados de cluster da extensão do Agente IAAS SQL: 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
```



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Primeiro, remova todos os VMs do SQL Server do cluster. Isto removerá fisicamente os nóleiros do aglomerado e destruirá o aglomerado: 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

Se estes são os únicos VMs no aglomerado, então o aglomerado será destruído. Se houver outros VMs no cluster para além dos VMs do SQL Server que foram removidos, os outros VMs não serão removidos e o cluster não será destruído. 

Em seguida, remova os metadados de cluster da extensão do Agente IAAS SQL: 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos: 

* [Visão geral dos VMs do servidor SQL](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [FAQ para VMs do servidor SQL](frequently-asked-questions-faq.md)
* [Notas de lançamento para VMs do servidor SQL](../../database/doc-changes-updates-release-notes.md)
* [Mudar modelos de licenciamento para um SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [Visão geral de sempre em grupos de disponibilidade &#40;&#41;de servidor SQL ](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Configuração de uma instância de servidor para sempre grupos de disponibilidade &#40;&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Administração de um grupo de disponibilidade &#40;&#41;de servidor SQL ](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitorização de grupos de disponibilidade &#40;&#41;do servidor SQL ](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Visão geral das declarações do Transact-SQL para grupos de disponibilidade always on &#40;&#41;do servidor SQL ](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Visão geral dos cmdlets PowerShell para sempre em grupos de disponibilidade &#40;&#41;do servidor SQL ](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)