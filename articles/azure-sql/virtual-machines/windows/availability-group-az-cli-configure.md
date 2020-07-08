---
title: Configure um grupo de disponibilidade utilizando o CLI Azure
description: Utilize o CLI Azure para criar o cluster de failover do Windows, o ouvinte do grupo de disponibilidade e o balançador de carga interno num SQL Server VM em Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3366438012ecc3395e7f4ae3774316ef1ddcd3b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84669346"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-azure-vm"></a>Utilize o Azure CLI para configurar um grupo de disponibilidade Always On para O Servidor SQL em Azure VM
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo descreve como utilizar o [CLI Azure](/cli/azure/sql/vm?view=azure-cli-latest/) para implementar um cluster de failover do Windows, adicionar VMs sql server ao cluster e criar o equilibrador de carga interno e o ouvinte para um grupo de disponibilidade Always On. A implementação do grupo de disponibilidade Always On ainda é feita manualmente através do SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Pré-requisitos
Para automatizar a configuração de um grupo de disponibilidade Always On utilizando o Azure CLI, deve ter os seguintes pré-requisitos: 
- Uma [assinatura Azure](https://azure.microsoft.com/free/).
- Um grupo de recursos com um controlador de domínio. 
- Uma ou mais VMs de domínio [em Azure executando SQL Server 2016 (ou posteriormente) Edição Empresarial](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) no *mesmo conjunto de disponibilidade ou diferentes zonas de disponibilidade* que tenham sido [registadas com o fornecedor de recursos SQL VM](sql-vm-resource-provider-register.md).  
- O [Azure CLI.](/cli/azure/install-azure-cli) 
- Dois endereços IP disponíveis (não utilizados por nenhuma entidade). Um é para o equilibrador de carga interno. O outro é para o ouvinte do grupo de disponibilidade dentro da mesma sub-rede que o grupo de disponibilidade. Se estiver a utilizar um equilibrador de carga existente, necessita apenas de um endereço IP disponível para o ouvinte do grupo de disponibilidade. 

## <a name="permissions"></a>Permissões
Necessita das seguintes permissões de conta para configurar o grupo always on disponibilidade utilizando o Azure CLI: 

- Uma conta de utilizador de domínio existente que tem a permissão **de Objeto de Computador** criar no domínio. Por exemplo, uma conta de administração de domínio normalmente tem permissão suficiente (por exemplo: account@domain.com ). _Esta conta também deve fazer parte do grupo de administradores locais em cada VM para criar o cluster._
- A conta de utilizador de domínio que controla o SQL Server. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>Passo 1: Criar uma conta de armazenamento como testemunha em nuvem
O aglomerado precisa de uma conta de armazenamento para agir como testemunha da nuvem. Pode utilizar qualquer conta de armazenamento existente, ou pode criar uma nova conta de armazenamento. Se quiser utilizar uma conta de armazenamento existente, avance para a secção seguinte. 

O seguinte código corta-códigos cria a conta de armazenamento: 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Pode ver o erro `az sql: 'vm' is not in the 'az sql' command group` se estiver a usar uma versão desatualizada do Azure CLI. Descarregue a [versão mais recente do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) para ultrapassar este erro.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>Passo 2: Definir metadados de cluster de failover do Windows
O grupo de comando Azure CLI [az sql vm](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) gere os metadados do serviço Windows Server Failover Cluster (WSFC) que acolhe o grupo de disponibilidade. Os metadados de cluster incluem o domínio do Diretório Ativo, contas de cluster, contas de armazenamento a serem usadas como testemunha em nuvem e a versão SQL Server. Utilize [o grupo az sql vm criar](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) para definir os metadados para WSFC de modo que quando o primeiro SQL Server VM é adicionado, o cluster é criado como definido. 

O seguinte código de corte define os metadados para o cluster:
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

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>Passo 3: Adicione VMs do servidor SQL ao cluster
Adicionar o primeiro SQL Server VM ao cluster cria o cluster. O comando [az sql vm add-to-group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) cria o cluster com o nome anteriormente dado, instala a função de cluster nos VMs do SQL Server e adiciona-os ao cluster. As utilizações subsequentes do `az sql vm add-to-group` comando adicionam mais VMs do Servidor SQL ao cluster recém-criado. 

O seguinte corte de código cria o cluster e adiciona-lhe o primeiro SQL Server VM: 

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

## <a name="step-4-create-the-availability-group"></a>Passo 4: Criar o grupo de disponibilidade
Crie manualmente o grupo de disponibilidade como normalmente faria, utilizando [o SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)ou [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Não* crie um ouvinte neste momento porque isto é feito através do CLI Azure nas seguintes secções.  

## <a name="step-5-create-the-internal-load-balancer"></a>Passo 5: Criar o equilibrador de carga interno

O ouvinte do grupo Always On está disponível requer uma instância interna do Balançador de Carga Azure. O equilibrador de carga interno fornece um endereço IP "flutuante" para o ouvinte do grupo de disponibilidade que permite uma maior falha e reconexão. Se os VMs do Servidor SQL de um grupo de disponibilidade fizerem parte do mesmo conjunto de disponibilidade, pode utilizar um equilibrador de carga Básico. Caso contrário, é necessário utilizar um equilibrador de carga Standard.  

> [!NOTE]
> O balançador de carga interno deve estar na mesma rede virtual que as instâncias VM do SQL Server. 

O seguinte corte de código cria o equilibrador de carga interno:

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> O recurso IP público para cada SQL Server VM deve ter um SKU Standard para ser compatível com o balanceador de carga Standard. Para determinar o SKU do recurso IP público do seu VM, vá ao **Grupo de Recursos,** selecione o seu recurso **de endereço IP público** para o VM do servidor SQL desejado e localize o valor sob **SKU** no painel **de visão geral.**  

## <a name="step-6-create-the-availability-group-listener"></a>Passo 6: Criar o ouvinte do grupo de disponibilidade
Depois de criar manualmente o grupo de disponibilidade, pode criar o ouvinte utilizando [um az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 

O *ID do recurso sub-rede* é o valor de `/subnets/<subnetname>` anexado ao ID de recursos do recurso de rede virtual. Para identificar o ID do recurso sub-rede:
   1. Vá ao seu grupo de recursos no [portal Azure](https://portal.azure.com). 
   1. Selecione o recurso de rede virtual. 
   1. Selecione **Propriedades** no painel **de definições.** 
   1. Identifique o ID de recursos para a rede virtual e `/subnets/<subnetname>` apense-o até ao fim para criar o ID de recursos da sub-rede. Por exemplo:
      - O seu ID de recursos de rede virtual é:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - O nome da sua sub-rede é:`default`
      - Portanto, o seu ID de recurso de sub-rede é:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


O seguinte corte de código cria o ouvinte do grupo de disponibilidade:

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

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>Modifique o número de réplicas num grupo de disponibilidade
Há uma camada adicional de complexidade quando você está implantando um grupo de disponibilidade para SQL Server VMs hospedados em Azure. O fornecedor de recursos e o grupo de máquinas virtuais gerem agora os recursos. Como tal, quando está a adicionar ou a remover réplicas no grupo de disponibilidade, há um passo adicional para atualizar os metadados do ouvinte com informações sobre os VMs do Servidor SQL. Ao modificar o número de réplicas no grupo de disponibilidade, também deve utilizar o comando [de atualização ag-listener do grupo az sql vm](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) para atualizar o ouvinte com os metadados dos VMs do SqL Server. 


### <a name="add-a-replica"></a>Adicione uma réplica

Para adicionar uma nova réplica ao grupo de disponibilidade:

1. Adicione o SQL Server VM ao cluster:
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
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

### <a name="remove-a-replica"></a>Remover uma réplica

Para remover uma réplica do grupo de disponibilidade:

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

## <a name="remove-the-availability-group-listener"></a>Remova o ouvinte do grupo de disponibilidade
Se mais tarde precisar de remover o ouvinte do grupo de disponibilidade configurado com o Azure CLI, tem de passar pelo fornecedor de recursos SQL VM. Como o ouvinte está registado através do fornecedor de recursos SQL VM, apenas a sua eliminação através do SQL Server Management Studio é insuficiente. 

O melhor método é eliminá-lo através do fornecedor de recursos SQL VM utilizando o seguinte corte de código no Azure CLI. Ao fazê-lo, remove os metadados de ouvintes do grupo de disponibilidade do fornecedor de recursos SQL VM. Também elimina fisicamente o ouvinte do grupo de disponibilidade. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos: 

* [Visão geral dos VMs do servidor SQL](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [FAQ para VMs do servidor SQL](frequently-asked-questions-faq.md)
* [Notas de lançamento para VMs do servidor SQL](../../database/doc-changes-updates-release-notes.md)
* [Mudar modelos de licenciamento para um SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [Visão geral de sempre em grupos de disponibilidade &#40;&#41;de servidor SQL](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Configuração de uma instância de servidor para sempre grupos de disponibilidade &#40;&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Administração de um grupo de disponibilidade &#40;&#41;de servidor SQL](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitorização de grupos de disponibilidade &#40;&#41;do servidor SQL](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Visão geral das declarações do Transact-SQL para grupos de disponibilidade always on &#40;&#41;do servidor SQL](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Visão geral dos cmdlets PowerShell para sempre em grupos de disponibilidade &#40;&#41;do servidor SQL](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
