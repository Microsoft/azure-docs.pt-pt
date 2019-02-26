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
ms.openlocfilehash: fd33966a7c7d2ea72cdc98a23f601687d9577dde
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825214"
---
# <a name="use-azure-sql-vm-cli-to-configure-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Utilizar a CLI de VM do SQL do Azure para configurar o grupo de disponibilidade Always On do SQL Server numa VM do Azure
Este artigo descreve como utilizar [CLI de VM do SQL do Azure](https://docs.microsoft.com/mt-mt/cli/azure/ext/sqlvm-preview/sqlvm?view=azure-cli-2018-03-01-hybrid) para implementar um Cluster Windows de ativação pós-falha (WSFC) e adicionar VMs do SQL Server para o cluster, bem como para criar o Balanceador de carga interno e o serviço de escuta para um grupo de disponibilidade Always On.  A implantação real do grupo de disponibilidade Always On é ainda feita manualmente por meio do SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Pré-requisitos
Para automatizar a configuração de um grupo de disponibilidade Always On na CLI do Azure SQL VM, já tem de ter os seguintes pré-requisitos: 
- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- Um grupo de recursos com um controlador de domínio. 
- Um ou mais associado a um domínio [VMs no Azure em execução do SQL Server 2016 (ou superior) Enterprise edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) no *mesmo conjunto de disponibilidade ou zonas de disponibilidade diferentes* que tenham sido [registado com o fornecedor de recursos de VM do SQL](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider).  
 
## <a name="create-storage-account-as-a-cloud-witness"></a>Criar conta de armazenamento como um testemunho de nuvem
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

## <a name="define-windows-failover-cluster-metadata"></a>Definir metadados de Cluster de ativação pós-falha do Windows
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

## <a name="add-sql-server-vms-to-cluster"></a>Adicionar VMs do SQL Server ao cluster
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

## <a name="create-availability-group"></a>Criar grupo de disponibilidade
Criar manualmente o grupo de disponibilidade, tal como faria normalmente, através de um [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell), ou [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

  >[!IMPORTANT]
  > Fazer **não** criar um serviço de escuta neste momento, porque isso é feito através da CLI do Azure nas seções a seguir.  

## <a name="create-internal-load-balancer"></a>Criar Balanceador de carga interno

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

## <a name="create-availability-group-listener"></a>Criar serviço de escuta de grupo de disponibilidade
Quando o grupo de disponibilidade tiver sido criado manualmente, é possível criar o serviço de escuta usando [az sql vm ag-listener](https://docs.microsoft.com/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 


- O **ID de recurso de sub-rede** é o valor de `/subnets/<subnetname>` anexado ao ID de recurso do recurso da vNet. Para identificar o ID de recurso de sub-rede, faça o seguinte:
   1. Navegue para o grupo de recursos no [portal do Azure](https://portal.azure.com). 
   1. Selecione o recurso da vNet. 
   1. Selecione **propriedades** no **definições** painel. 
   1. Identificar o ID de recurso para a vNet e de acréscimo `/subnets/<subnetname>`ao final do mesmo para criar o ID de recurso de sub-rede. Por exemplo:
        - O meu recurso da vNet ID é `/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`.
        - Meu nome de sub-rede é `default`.
        - Por conseguinte, é o meu ID de recurso de sub-rede `/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


O fragmento de código seguinte irá criar o serviço de escuta do grupo de disponibilidade:

```cli
# Create the AG listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM’s hosting AG replicas ex: sqlvm1 sqlvm2>
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
* [Monitorização de grupos de disponibilidade &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server.md)
* [Descrição geral de declarações de Transact-SQL para grupos de disponibilidade Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Descrição geral dos Cmdlets do PowerShell para grupos de disponibilidade Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
