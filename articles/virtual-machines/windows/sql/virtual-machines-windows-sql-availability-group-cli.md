---
title: Configurar um grupo de disponibilidade (CLI do Azure)
description: Use o CLI do Azure para criar o cluster de failover do Windows, o ouvinte do grupo de disponibilidade e o balanceador de carga interno em uma VM SQL Server no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: a6600af353daf2bfa7b49196f48ba5b60e6c45fb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022370"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Usar o CLI do Azure para configurar um grupo de disponibilidade Always On para SQL Server em uma VM do Azure
Este artigo descreve como usar o [CLI do Azure](/cli/azure/sql/vm?view=azure-cli-latest/) para implantar um cluster de failover do Windows, adicionar SQL Server VMs ao cluster e criar o balanceador de carga interno e o ouvinte para um grupo de disponibilidade Always on. A implantação do grupo de disponibilidade Always On ainda é feita manualmente por meio de SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Pré-requisitos
Para automatizar a configuração de um grupo de disponibilidade Always On usando o CLI do Azure, você deve ter os seguintes pré-requisitos: 
- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- Um grupo de recursos com um controlador de domínio. 
- Uma ou mais VMs ingressadas [no domínio no Azure que executam o SQL Server 2016 (ou posterior) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) no *mesmo conjunto de disponibilidade ou zonas de disponibilidade diferentes* que foram [registradas com o provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md).  
- O [CLI do Azure](/cli/azure/install-azure-cli). 
- Dois endereços IP disponíveis (não usados por nenhuma entidade). Um é para o balanceador de carga interno. O outro é para o ouvinte do grupo de disponibilidade na mesma sub-rede que o grupo de disponibilidade. Se você estiver usando um balanceador de carga existente, precisará apenas de um endereço IP disponível para o ouvinte do grupo de disponibilidade. 

## <a name="permissions"></a>Permissões
Você precisa das seguintes permissões de conta para configurar o grupo de disponibilidade Always On usando o CLI do Azure: 

- Uma conta de usuário de domínio existente que tenha a permissão **criar objeto de computador** no domínio. Por exemplo, uma conta de administrador de domínio normalmente tem permissão suficiente (por exemplo: account@domain.com). _Essa conta também deve fazer parte do grupo de Administradores local em cada VM para criar o cluster._
- A conta de usuário de domínio que controla o serviço de SQL Server. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>Etapa 1: criar uma conta de armazenamento como uma testemunha de nuvem
O cluster precisa de uma conta de armazenamento para atuar como a testemunha de nuvem. Você pode usar qualquer conta de armazenamento existente ou pode criar uma nova conta de armazenamento. Se você quiser usar uma conta de armazenamento existente, pule para a próxima seção. 

O trecho de código a seguir cria a conta de armazenamento: 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Você poderá ver o erro `az sql: 'vm' is not in the 'az sql' command group` se estiver usando uma versão desatualizada do CLI do Azure. Baixe a [versão mais recente do CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) para passar por esse erro.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>Etapa 2: definir os metadados do cluster de failover do Windows
O grupo de comando CLI do Azure [AZ SQL VM Group](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) gerencia os metadados do serviço WSFC (cluster de failover do Windows Server) que hospeda o grupo de disponibilidade. Os metadados do cluster incluem o domínio Active Directory, as contas de cluster, as contas de armazenamento a serem usadas como a testemunha de nuvem e a versão SQL Server. Use [AZ SQL VM Group Create](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) para definir os metadados do WSFC para que, quando a primeira SQL Server VM for adicionada, o cluster seja criado conforme definido. 

O trecho de código a seguir define os metadados para o cluster:
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

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>Etapa 3: Adicionar SQL Server VMs ao cluster
Adicionar a primeira SQL Server VM ao cluster cria o cluster. O comando [AZ SQL VM Add-to-Group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) cria o cluster com o nome fornecido anteriormente, instala a função de cluster nas VMs SQL Server e as adiciona ao cluster. Usos subsequentes do comando `az sql vm add-to-group` adicionar mais SQL Server VMs ao cluster recém-criado. 

O trecho de código a seguir cria o cluster e adiciona a primeira SQL Server VM a ele: 

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
Use este comando para adicionar qualquer outra VM SQL Server ao cluster. Modifique somente o parâmetro `-n` para o nome da VM SQL Server. 

## <a name="step-4-create-the-availability-group"></a>Etapa 4: criar o grupo de disponibilidade
Crie manualmente o grupo de disponibilidade como faria normalmente, usando [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)ou [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Não* crie um ouvinte neste momento porque isso é feito por meio do CLI do Azure nas seções a seguir.  

## <a name="step-5-create-the-internal-load-balancer"></a>Etapa 5: criar o balanceador de carga interno

O ouvinte do grupo de disponibilidade Always On requer uma instância interna do Azure Load Balancer. O balanceador de carga interno fornece um endereço IP "flutuante" para o ouvinte do grupo de disponibilidade que permite failover e reconexão mais rápidos. Se as SQL Server VMs em um grupo de disponibilidade fizerem parte do mesmo conjunto de disponibilidade, você poderá usar um balanceador de carga básico. Caso contrário, você precisa usar um balanceador de carga padrão.  

> [!NOTE]
> O balanceador de carga interno deve estar na mesma rede virtual que as instâncias de VM SQL Server. 

O trecho de código a seguir cria o balanceador de carga interno:

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> O recurso de IP público para cada VM SQL Server deve ter um SKU Standard para ser compatível com o Load Balancer padrão. Para determinar a SKU do recurso IP público da VM, vá para **grupo de recursos**, selecione o recurso de **endereço IP público** para a VM SQL Server desejada e localize o valor em **SKU** no painel **visão geral** .  

## <a name="step-6-create-the-availability-group-listener"></a>Etapa 6: criar o ouvinte do grupo de disponibilidade
Depois de criar manualmente o grupo de disponibilidade, você pode criar o ouvinte usando [AZ SQL VM AG-Listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 

A *ID de recurso de sub-rede* é o valor de `/subnets/<subnetname>` acrescentado à ID de recurso do recurso de rede virtual. Para identificar a ID de recurso de sub-rede:
   1. Vá para o grupo de recursos na [portal do Azure](https://portal.azure.com). 
   1. Selecione o recurso de rede virtual. 
   1. Selecione **Propriedades** no painel **configurações** . 
   1. Identifique a ID de recurso para a rede virtual e acrescente `/subnets/<subnetname>` ao final dela para criar a ID de recurso de sub-rede. Por exemplo:
      - A ID do recurso de rede virtual é: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - O nome da sub-rede é: `default`
      - Portanto, a ID de recurso de sub-rede é: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


O trecho de código a seguir cria o ouvinte do grupo de disponibilidade:

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

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>Modificar o número de réplicas em um grupo de disponibilidade
Há uma camada adicional de complexidade quando você está implantando um grupo de disponibilidade para SQL Server VMs hospedadas no Azure. O provedor de recursos e o grupo de máquinas virtuais agora gerenciam os recursos. Assim, quando você estiver adicionando ou removendo réplicas no grupo de disponibilidade, há uma etapa adicional de atualizar os metadados do ouvinte com informações sobre as VMs SQL Server. Quando estiver modificando o número de réplicas no grupo de disponibilidade, você também deverá usar o comando [AZ SQL VM Group AG-Listener Update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) para atualizar o ouvinte com os metadados das VMs SQL Server. 


### <a name="add-a-replica"></a>Adicionar uma réplica

Para adicionar uma nova réplica ao grupo de disponibilidade:

1. Adicione a VM SQL Server ao cluster:
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Use SQL Server Management Studio para adicionar a instância de SQL Server como uma réplica no grupo de disponibilidade.
1. Adicione o SQL Server metadados de VM ao ouvinte:
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Remover uma réplica

Para remover uma réplica do grupo de disponibilidade:

1. Remova a réplica do grupo de disponibilidade usando SQL Server Management Studio. 
1. Remova o SQL Server metadados da VM do ouvinte:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Remova a VM SQL Server do cluster:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>Remover o ouvinte do grupo de disponibilidade
Se posteriormente você precisar remover o ouvinte do grupo de disponibilidade configurado com o CLI do Azure, deverá passar pelo provedor de recursos da VM do SQL. Como o ouvinte é registrado por meio do provedor de recursos de VM do SQL, apenas excluí-lo via SQL Server Management Studio é insuficiente. 

O melhor método é excluí-lo por meio do provedor de recursos da VM do SQL usando o trecho de código a seguir na CLI do Azure. Isso remove os metadados do ouvinte do grupo de disponibilidade do provedor de recursos da VM do SQL. Ele também exclui fisicamente o ouvinte do grupo de disponibilidade. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Visão geral das VMs SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)
* [Perguntas frequentes para VMs SQL Server](virtual-machines-windows-sql-server-iaas-faq.md)
* [Notas de versão para VMs SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Alternando modelos de licenciamento para uma VM SQL Server](virtual-machines-windows-sql-ahb.md)
* [Visão geral do &#40;grupos de disponibilidade Always on SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Configuração de uma instância de servidor para &#40;grupos de disponibilidade Always on SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Administração de um grupo &#40;de disponibilidade SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitoramento de grupos &#40;de disponibilidade SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Visão geral das instruções Transact-SQL para &#40;grupos de disponibilidade Always on SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Visão geral dos cmdlets do PowerShell &#40;para grupos de disponibilidade Always on SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
