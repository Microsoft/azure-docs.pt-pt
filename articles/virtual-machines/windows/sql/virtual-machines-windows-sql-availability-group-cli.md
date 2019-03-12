---
title: Utilizar a CLI de VM do SQL do Azure para configurar o grupo de disponibilidade Always On do SQL Server numa VM do Azure
description: 'Utilize a CLI do Azure para criar o Cluster de ativação pós-falha do Windows, o serviço de escuta do grupo de disponibilidade e o Balanceador de carga interno numa VM do SQL Server no Azure. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8af860293fc332437d67ff4db63d7686be7efff0
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57765276"
---
# <a name="use-azure-sql-vm-cli-to-configure-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Utilizar a CLI de VM do SQL do Azure para configurar o grupo de disponibilidade Always On do SQL Server numa VM do Azure
Este artigo descreve como utilizar [CLI de VM do SQL do Azure](https://docs.microsoft.com/mt-mt/cli/azure/ext/sqlvm-preview/sqlvm?view=azure-cli-2018-03-01-hybrid) para implementar um Cluster Windows de ativação pós-falha (WSFC) e adicionar VMs do SQL Server para o cluster, bem como para criar o Balanceador de carga interno e o serviço de escuta para um grupo de disponibilidade Always On.  A implantação real do grupo de disponibilidade Always On é ainda feita manualmente por meio do SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Pré-requisitos
Para automatizar a configuração de um grupo de disponibilidade Always On na CLI do Azure SQL VM, já tem de ter os seguintes pré-requisitos: 
- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- Um grupo de recursos com um controlador de domínio. 
- Um ou mais associado a um domínio [VMs no Azure em execução do SQL Server 2016 (ou superior) Enterprise edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) no *mesmo conjunto de disponibilidade ou zonas de disponibilidade diferentes* que tenham sido [registado com o fornecedor de recursos de VM do SQL](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider).  
- [CLI do Azure](/cli/azure/install-azure-cli). 
- (Não utilizado por qualquer entidade) disponíveis dois endereços IP, um para o Balanceador de carga interno e para o serviço de escuta do grupo de disponibilidade na mesma sub-rede que o grupo de disponibilidade. Se está a ser utilizado um balanceador de carga existente, em seguida, apenas um endereço IP disponível é necessária para o serviço de escuta do grupo de disponibilidade. 

## <a name="permissions"></a>Permissões
As seguintes permissões de conta são necessárias para configurar o grupo de disponibilidade Always On na CLI do Azure SQL VM. 

- Uma domínio conta de utilizador existente que tenha a permissão "Criar objeto de computador" no domínio.  Por exemplo, normalmente, uma conta de administrador de domínio tem permissão suficiente (ex: account@domain.com). _Esta conta também deve ser parte do grupo de administradores local em cada VM para criar o cluster._
- A conta de utilizador de domínio que controla o serviço SQL Server. 
 
## <a name="step-1---create-storage-account-as-a-cloud-witness"></a>Passo 1 - criar conta de armazenamento como um testemunho de nuvem
O cluster precisa de uma conta de armazenamento para atuar como o testemunho de nuvem. Pode utilizar qualquer conta de armazenamento existente ou pode criar uma nova conta de armazenamento. Se pretender utilizar uma conta de armazenamento existente, avançar diretamente para a secção seguinte. 

O fragmento de código seguinte cria a conta de armazenamento: 
```cli
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

   >[!TIP]
   > Poderá ver o erro `az sql: 'vm' is not in the 'az sql' command group` se estiver a utilizar uma versão desatualizada do CLI do Azure. Transfira o [a versão mais recente da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) para ignorar este erro.

## <a name="step-2---define-windows-failover-cluster-metadata"></a>Passo 2: Definir metadados de Cluster de ativação pós-falha do Windows
A CLI de VM de SQL do Azure [grupo de VMS de sql az](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) comando grupo gere os metadados do serviço de Cluster de ativação pós-falha do Windows (WSFC) que aloja o grupo de disponibilidade. Metadados de cluster incluem o domínio do AD, contas de cluster, as contas de armazenamento a ser utilizado como o testemunho de nuvem e a versão do SQL Server. Uso [criar grupo de VMS de sql az](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) para definir os metadados para o WSFC para que quando é adicionado a primeira VM do SQL Server, o cluster for criado, conforme definido. 

O fragmento de código seguinte define os metadados para o cluster:
```cli
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

## <a name="step-3---add-sql-server-vms-to-cluster"></a>Passo 3 - Adicionar VMs do SQL Server ao cluster
Adicionar a primeira VM do SQL Server para o cluster cria o cluster. O [az vm adicionar-para-grupo sql](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) comando cria o cluster com o nome fornecido anteriormente, instala a função de cluster em VMs do SQL Server e adiciona-o ao cluster. Utilizações subsequentes do `az sql vm add-to-group` comando adiciona VMs adicionais do SQL Server para o cluster recém-criado. 

O fragmento de código seguinte cria o cluster e adiciona a primeira VM do SQL Server para o mesmo: 

```cli
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
Utilize este comando para adicionar quaisquer outras VMs do SQL Server para o cluster, apenas modificar o `-n` parâmetro para o nome de VM do SQL Server. 

## <a name="step-4---create-availability-group"></a>Passo 4 - criar o grupo de disponibilidade
Criar manualmente o grupo de disponibilidade, tal como faria normalmente, através de um [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell), ou [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

  >[!IMPORTANT]
  > Fazer **não** criar um serviço de escuta neste momento, porque isso é feito através da CLI do Azure nas seções a seguir.  

## <a name="step-5---create-internal-load-balancer"></a>Passo 5 - criar o Balanceador de carga interno

O Always On (AG) serviço de escuta requer um balanceador de carga de Azure interno (ILB). O ILB fornece um endereço IP "flutuante" para o serviço de escuta de AG que permite que mais rápida de ativação pós-falha e restabelecimento de ligação. Se as VMs do SQL Server num grupo de disponibilidade fazem parte do mesmo conjunto de disponibilidade, pode utilizar um balanceador de carga básico; caso contrário, terá de utilizar um balanceador de carga Standard.  **O ILB deve estar na mesma vNet como as instâncias de VM do SQL Server.** 

O fragmento de código seguinte cria o Balanceador de carga interno:

```cli
# Create the Internal Load Balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

  >[!IMPORTANT]
  > O recurso de IP público para cada VM do SQL Server deve ter um SKU standard para ser compatível com o Balanceador de carga Standard. Para determinar o SKU de recurso de IP público da VM, navegue até à sua **grupo de recursos**, selecione seu **endereço IP público** recurso para o SQL Server VM pretendida e localize o valor sob **SKU**  das **descrição geral** painel.  

## <a name="step-6---create-availability-group-listener"></a>Passo 6 – criar serviço de escuta de grupo de disponibilidade
Quando o grupo de disponibilidade tiver sido criado manualmente, é possível criar o serviço de escuta usando [az sql vm ag-listener](https://docs.microsoft.com/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 


- O **ID de recurso de sub-rede** é o valor de `/subnets/<subnetname>` anexado ao ID de recurso do recurso da vNet. Para identificar o ID de recurso de sub-rede, faça o seguinte:
   1. Navegue para o grupo de recursos no [portal do Azure](https://portal.azure.com). 
   1. Selecione o recurso da vNet. 
   1. Selecione **propriedades** no **definições** painel. 
   1. Identificar o ID de recurso para a vNet e de acréscimo `/subnets/<subnetname>`ao final do mesmo para criar o ID de recurso de sub-rede. Por exemplo:
        - É o meu ID de recurso da vNet: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
        - Meu nome de sub-rede é `default`.
        - Por conseguinte, é o meu ID de recurso de sub-rede: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


O fragmento de código seguinte irá criar o serviço de escuta do grupo de disponibilidade:

```cli
# Create the AG listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM’s hosting AG replicas ex: sqlvm1 sqlvm2>
```
## <a name="modify-number-of-replicas-in-availability-group"></a>Modificar o número de réplicas no grupo de disponibilidade
Há uma camada adicional de complexidade ao implementar um grupo de disponibilidade para VMs do SQL Server alojado no Azure, como recursos agora são geridos pelo fornecedor de recursos e pelo `virtual machine group`. Como tal, quando a adição ou remoção de réplicas ao grupo de disponibilidade, há uma etapa adicional de atualizar os metadados de serviço de escuta com informações sobre as VMs do SQL Server. Portanto, ao adicionar réplicas de VM do SQL Server adicionais ao grupo de disponibilidade, também tem de utilizar o [az sqlvm aglistener adicionar-sqlvm](/cli/azure/ext/sqlvm-preview/sqlvm/aglistener?view=azure-cli-2018-03-01-hybrid#ext-sqlvm-preview-az-sqlvm-aglistener-add-sqlvm) comando para adicionar VM do SQL Server para os metadados do serviço de escuta. Da mesma forma, ao remover as réplicas do grupo de disponibilidade, também tem de utilizar o [az o serviço de escuta de ag sqlvm remove-sqlvm](/cli/azure/ext/sqlvm-preview/sqlvm/aglistener?view=azure-cli-2018-03-01-hybrid#ext-sqlvm-preview-az-sqlvm-aglistener-remove-sqlvm) para remover metadados dessa VM de servidor de SQL do serviço de escuta. 

### <a name="adding-a-replica"></a>Adicionar uma réplica
Para adicionar uma nova réplica ao grupo de disponibilidade, faça o seguinte:

1. Adicione VM do SQL Server para o cluster: 

    ```cli
    # Add SQL Server VM to the Cluster
    # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
    #  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

    az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
    -b <bootstrap account password> -p <operator account password> -s <service account password>
    ```
1. Utilize o SQL Server Management Studio (SSMS) para adicionar a instância do SQL Server como uma réplica dentro do grupo de disponibilidade.
1. Adicione a fazer de metadados de VM do SQL Server o serviço de escuta:
    ```cli
    # Add SQL VM metadata to cluster
    # example: az sqlvm aglistener add-sqlvm  --group-name Cluster`
    # --name AGListener` --resource-group SQLVM-RG `
    #--sqlvm-rid /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Compute/virtualMachines/SQLVM3
    
    az sqlvm aglistener add-sqlvm --group-name <Cluster name> `
    --name <AG Listener name> --resource-group <RG group name> `
    --sqlvm-rid <SQL VM resource ID>
    ```

### <a name="removing-a-replica"></a>Remover uma réplica
Para remover uma réplica do grupo de disponibilidade, faça o seguinte:

1. Remova a réplica do grupo de disponibilidade com o SQL Server Management Studio (SSMS). 
1. Remova o serviço de escuta os metadados de VM do SQL Server:
    ```cli
    #Remove SQL VM metadata from listener
    # example: az sqlvm aglistener remove-sqlvm --group-name Cluster `
    --name AGListener` --resource-group SQLVM-RG `
    --sqlvm-rid /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Compute/virtualMachines/SQLVM3
    
    az sqlvm aglistener remove-sqlvm --group-name <Cluster name> `
    --name <AG Listener name> --resource-group <RG group name> `
    --sqlvm-rid <SQL VM resource ID>
    ``` 
1. Remova a VM do SQL Server dos metadados do cluster:

    ```cli
    # Remove SQL VM from cluster metadata
    #example: az sqlvm remove-from-group --name SQLVM3 --resource-group SQLVM-RG
    
    az sqlvm remove from group --name <SQL VM name> --resource-group <RG name> 
    ```

## <a name="remove-availability-group-listener"></a>Remover o serviço de escuta de grupo de disponibilidade
Se precisar de remover o serviço de escuta configurado com a CLI do Azure mais tarde, deve percorrer o fornecedor de recursos de VM do SQL Server. Uma vez que o serviço de escuta é registrado por meio do Provedor de recursos de VM do SQL Server, basta excluí-lo através do SQL Server Management Studio é insuficiente. Na verdade, ele deve ser eliminado por meio do Provedor de recursos de VM do SQL Server com a CLI do Azure. Se o fizer, remove os metadados do serviço de escuta de AG do fornecedor de recursos de VM do SQL Server e exclui fisicamente o serviço de escuta do grupo de disponibilidade. 

O fragmento de código seguinte elimina o serviço de escuta do grupo de disponibilidade do SQL a partir de ambos os o fornecedor de recursos do SQL e do seu grupo de disponibilidade: 

```cli
# Remove the AG listener
# example: az sqlvm aglistener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG
az sqlvm aglistener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Descrição geral da VM do SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)
* [FAQ de VM do SQL Server](virtual-machines-windows-sql-server-iaas-faq.md)
* [Notas de versão de VM do SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Modelos de licenciamento mudar para uma VM do SQL Server](virtual-machines-windows-sql-ahb.md)
* [Descrição geral de grupos de disponibilidade Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Configuração de uma instância de servidor para grupos de disponibilidade Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Administração de um grupo de disponibilidade &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitorização de grupos de disponibilidade &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Descrição geral de declarações de Transact-SQL para grupos de disponibilidade Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Descrição geral dos Cmdlets do PowerShell para grupos de disponibilidade Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
