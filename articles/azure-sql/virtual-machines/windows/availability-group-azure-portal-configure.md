---
title: Configure um grupo de disponibilidade (portal Azure)
description: Utilize o portal Azure para criar o cluster de failover do Windows, o ouvinte do grupo de disponibilidade e o balançador de carga interno num SQL Server VM em Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 3a8086c75a7125b744730de83c760db44ce222e9
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790105"
---
# <a name="use-azure-portal-to-configure-an-availability-group-preview-for-sql-server-on-azure-vm"></a>Utilize o portal Azure para configurar um grupo de disponibilidade (Pré-visualização) para O Servidor SQL em Azure VM 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo descreve como usar o [portal Azure](https://portal.azure.com) para configurar um grupo de disponibilidade para SQL Server em VMs Azure. 

Utilize o portal Azure para criar um novo cluster ou a bordo de um cluster existente e, em seguida, crie o grupo de disponibilidade, ouvinte e equilibrador de carga interno. 

Esta funcionalidade encontra-se em pré-visualização. 

Embora este artigo utilize o portal Azure para configurar o ambiente de grupo de disponibilidade, também é possível fazê-lo utilizando [o PowerShell ou o Azure CLI](availability-group-az-commandline-configure.md), [os modelos Azure Quickstart,](availability-group-quickstart-template-configure.md)ou [Manualmente](availability-group-manually-configure-tutorial.md) também. 


## <a name="prerequisites"></a>Pré-requisitos

Para configurar um grupo de disponibilidade Always On utilizando o portal Azure, deve ter os seguintes pré-requisitos: 

- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- Um grupo de recursos com um controlador de domínio. 
- Uma ou mais VMs de domínio [em Azure executando SQL Server 2016 (ou posteriormente) Edição empresarial](./create-sql-vm-portal.md) no *mesmo* conjunto de disponibilidade ou *diferentes* zonas de disponibilidade que foram [registadas com o fornecedor de recursos SQL VM em modo de gestão total](sql-vm-resource-provider-register.md) e estão usando a mesma conta de domínio para o serviço SQL Server em cada VM.
- Dois endereços IP disponíveis (não utilizados por nenhuma entidade). Um é para o equilibrador de carga interno. O outro é para o ouvinte do grupo de disponibilidade dentro da mesma sub-rede que o grupo de disponibilidade. Se estiver a utilizar um equilibrador de carga existente, só precisa de um endereço IP disponível para o ouvinte do grupo de disponibilidade. 

## <a name="permissions"></a>Permissões

Precisa das seguintes permissões de conta para configurar o grupo de disponibilidade utilizando o portal Azure: 

- Uma conta de utilizador de domínio existente que tem a permissão **de Objeto de Computador** criar no domínio. Por exemplo, uma conta de administração de domínio normalmente tem permissão suficiente (por exemplo: account@domain.com ). _Esta conta também deve fazer parte do grupo de administradores locais em cada VM para criar o cluster._
- A conta de utilizador de domínio que controla o SQL Server. Esta deve ser a mesma conta para cada SQL Server VM que pretende adicionar ao grupo de disponibilidade. 

## <a name="configure-cluster"></a>Aglomerado de configuração

Configure o cluster utilizando o portal Azure. Pode criar um novo cluster, ou se já tiver um cluster existente, pode a bordo do fornecedor de recursos SQL VM para a gestão do portal.


### <a name="create-a-new-cluster"></a>Criar um novo cluster

Se já tiver um cluster, salte esta secção e mude-se para o [cluster existente a bordo.](#onboard-existing-cluster) 

Se ainda não tiver um cluster existente, crie-o utilizando o portal Azure com estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
1. Navegue para o seu recurso [de máquinas virtuais SQL.](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 
1. Selecione **Alta Disponibilidade** em **Definições** . 
1. Selecione **+ novo cluster de falha do Servidor do Windows** para abrir a página de cluster **Configure Windows Failover.**  

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-cluster.png" alt-text="Criar um novo cluster selecionando o cluster + novo no portal":::

1. Diga o seu cluster e forneça uma conta de armazenamento para usar como Testemunha da Nuvem. Utilize uma conta de armazenamento existente ou selecione **Criar novo** para criar uma nova conta de armazenamento. O nome da conta de armazenamento deve ter entre 3 e 24 caracteres de comprimento e utilizar apenas números e letras minúsculas.

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-1.png" alt-text="Criar um novo cluster selecionando o cluster + novo no portal":::

1. Expanda **as credenciais do Windows Server Failover Cluster** para fornecer [credenciais](/rest/api/sqlvm/sqlvirtualmachinegroups/createorupdate#wsfcdomainprofile) para a conta de serviço sql Server, bem como para o operador de cluster e contas de bootstrap se forem diferentes da conta utilizada para o serviço SQL Server. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-2.png" alt-text="Criar um novo cluster selecionando o cluster + novo no portal"
    ```
    


### <a name="onboard-existing-cluster"></a>Aglomerado existente a bordo

Se já tiver um cluster configurado no seu ambiente VM do SQL Server, pode embarcar a partir do portal Azure.

Para tal, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
1. Navegue para o seu recurso [de máquinas virtuais SQL.](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 
1. Selecione **Alta Disponibilidade** em **Definições** . 
1. Selecione **o Cluster de Falha do Servidor do Windows existente** para abrir a página do Cluster de Falha do Servidor do **Windows.** 

   :::image type="content" source="media/availability-group-az-portal-configure/onboard-existing-cluster.png" alt-text="Criar um novo cluster selecionando o cluster + novo no portal":::

1. Reveja as definições do seu cluster. 
1. Selecione **Aplicar** a bordo do seu cluster e, em seguida, selecionar **Sim** na hora de proceder.




## <a name="create-availability-group"></a>Criar grupo de disponibilidade

Depois de o seu cluster ter sido criado ou a bordo, crie o grupo de disponibilidade utilizando o portal Azure. Para tal, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
1. Navegue para o seu recurso [de máquinas virtuais SQL.](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 
1. Selecione **Alta Disponibilidade** em **Definições** . 
1. Selecione **+ Novo grupo de disponibilidade para** abrir a página do grupo de disponibilidade **Criar.**

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-availability-group.png" alt-text="Criar um novo cluster selecionando o cluster + novo no portal":::

1. Insira um nome para o grupo de disponibilidade. 
1. Selecione **Configurar** o ouvinte para abrir a página **de ouvintes do grupo de disponibilidade Configure.** 

   :::image type="content" source="media/availability-group-az-portal-configure/create-availability-group.png" alt-text="Criar um novo cluster selecionando o cluster + novo no portal":::

1. Preencha os valores e utilize um equilibrador de carga existente, ou selecione **Criar novo** para criar um novo equilibrador de carga.  **Selecione Aplicar** para guardar as suas definições e criar o seu ouvinte e o balançador de carga. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-listener.png" alt-text="Criar um novo cluster selecionando o cluster + novo no portal":::

1. Escolha **+ Selecione réplica** para abrir a página de **réplicas do grupo de configure.**
1. Selecione as máquinas virtuais que pretende adicionar ao grupo de disponibilidade e escolha as definições de grupo de disponibilidade que melhor se adequam às necessidades do seu negócio. **Selecione Aplicar** para guardar as suas definições. 

   :::image type="content" source="media/availability-group-az-portal-configure/add-replicas.png" alt-text="Criar um novo cluster selecionando o cluster + novo no portal":::

1. Verifique as definições do grupo de disponibilidade e, em seguida, **selecione Aplicar** para criar o seu grupo de disponibilidade. 

Pode verificar o estado da sua implantação no **registo de atividade** que está acessível a partir do ícone da campainha na barra de navegação superior. 

  > [!NOTE]
  > A sua **saúde de sincronização** na página de **Alta Disponibilidade** do portal Azure mostrará como **Não saudável** até adicionar bases de dados ao seu grupo de disponibilidade. 


## <a name="add-database-to-availability-group"></a>Adicionar base de dados ao grupo de disponibilidade

Adicione as suas bases de dados ao seu grupo de disponibilidade após a implementação concluída. Os passos abaixo utilizam o SQL Server Management Studio (SSMS), mas também pode utilizar [o Transact-SQL ou o PowerShell.](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) 

Para adicionar bases de dados ao seu grupo de disponibilidade utilizando o SQL Server Management Studio, siga estes passos:

1. Ligue-se a um dos VMs do seu servidor SQL utilizando o seu método preferido, como a Ligação remota de ambiente de trabalho (RDP). 
1. Open SQL Server Management Studio (SSMS).
1. Ligue-se à sua instância sql Server. 
1. Expandir **sempre em alta disponibilidade** no Explorador de **Objetos.**
1. Expandir **Grupos de Disponibilidade,** clicar com o botão direito do seu grupo de disponibilidade e optar por **adicionar base de dados...** .

   :::image type="content" source="media/availability-group-az-portal-configure/add-database.png" alt-text="Criar um novo cluster selecionando o cluster + novo no portal":::

1. Siga as indicações para selecionar a(s) base de dados(s) que pretende adicionar ao seu grupo de disponibilidade. 
1. Selecione **OK** para guardar as suas definições e adicione a sua base de dados ao grupo de disponibilidade. 
1. Após a adição da base de dados, atualize o **Object Explorer** para confirmar o estado da sua base de dados como `synchronized` . 

Após a adição das bases de dados, pode verificar o estado do seu grupo de disponibilidade no portal Azure: 

:::image type="content" source="media/availability-group-az-portal-configure/healthy-availability-group.png" alt-text="Criar um novo cluster selecionando o cluster + novo no portal":::

## <a name="add-more-vms"></a>Adicione mais VMs

Para adicionar mais VMs do Servidor SQL ao cluster, siga estes passos: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
1. Navegue para o seu recurso [de máquinas virtuais SQL.](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 
1. Selecione **Alta Disponibilidade** em **Definições** . 
1. Selecione **Configurar o Cluster de Falha do Servidor do Windows** para abrir a página de Cluster de Falha do Servidor do **Windows.** 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-existing-cluster.png" alt-text="Criar um novo cluster selecionando o cluster + novo no portal":::

1. Expanda **as credenciais do Windows Server Failover Cluster** e introduza nas contas utilizadas para o serviço SQL Server, operador de cluster e contas de botas de cluster. 
1. Selecione os VMs do servidor SQL que pretende adicionar ao cluster. 
1. Selecione **Aplicar** . 

Pode verificar o estado da sua implantação no **registo de atividade** que está acessível a partir do ícone da campainha na barra de navegação superior. 


## <a name="modify-availability-group"></a>Modificar o grupo de disponibilidade 


Pode **adicionar mais réplicas** ao grupo de disponibilidade, **configurar o Ouvinte,** ou **Eliminar o Ouvinte** da página De Alta **Disponibilidade** no portal Azure selecionando as elipses (...) junto ao seu grupo de disponibilidade: 

:::image type="content" source="media/availability-group-az-portal-configure/configure-listener.png" alt-text="Criar um novo cluster selecionando o cluster + novo no portal":::

## <a name="remove-cluster"></a>Remover o aglomerado

Remova todos os VMs do SQL Server do cluster para destruí-lo e, em seguida, remova os metadados de cluster do fornecedor de recursos SQL VM. Pode fazê-lo utilizando a versão mais recente do [Azure CLI](/cli/azure/install-azure-cli) ou PowerShell. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, remova todos os VMs do SQL Server do cluster. Isto removerá fisicamente os nóleiros do aglomerado e destruirá o aglomerado:  

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

Se estes são os únicos VMs no aglomerado, então o aglomerado será destruído. Se houver outros VMs no cluster para além dos VMs do SQL Server que foram removidos, os outros VMs não serão removidos e o cluster não será destruído. 

Em seguida, remova os metadados de cluster do fornecedor de recursos SQL VM: 

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


Em seguida, remova os metadados de cluster do fornecedor de recursos SQL VM: 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="troubleshooting"></a>Resolução de problemas

Se tiver problemas, pode verificar o histórico de implementação e rever os erros comuns, bem como as suas resoluções. 

### <a name="check-deployment-history"></a>Verifique o histórico de implementação

As alterações ao cluster e grupo de disponibilidade através do portal são feitas através de implementações. O histórico de implementação pode fornecer mais detalhes se houver problemas com a criação, ou a bordo do cluster, ou com a criação do grupo de disponibilidade.

Para ver os registos para a implantação e verificar o histórico de implantação, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para o seu grupo de recursos.
1. Selecione **Implementações** em **Definições** .
1. Selecione a implementação de interesse para saber mais sobre a implementação. 


   :::image type="content" source="media/availability-group-az-portal-configure/failed-deployment.png" alt-text="Criar um novo cluster selecionando o cluster + novo no portal" :::

### <a name="common-errors"></a>Erros comuns

Reveja os seguintes erros comuns e as suas resoluções. 

#### <a name="the-account-which-is-used-to-start-up-sql-service-is-not-a-domain-account"></a>A conta que é usada para iniciar o serviço sql não é uma conta de domínio

Esta é uma indicação de que o fornecedor de recursos não conseguiu aceder ao serviço SQL Server com as credenciais fornecidas. Algumas resoluções comuns:
- Certifique-se de que o controlador de domínio está a funcionar.
- Validar as credenciais fornecidas no portal correspondem às do serviço SQL Server. 


## <a name="next-steps"></a>Passos seguintes


Para obter mais informações sobre grupos de disponibilidade, consulte:

- [Visão geral dos grupos de disponibilidade](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
* [Administração de um grupo de disponibilidade](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitorização de grupos de disponibilidade &#40;&#41;do servidor SQL ](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Disponibilidade grupo Declarações Transact-SQL ](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Grupos de disponibilidade Comandos PowerShell](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  

Para obter mais informações sobre os VMs do Servidor SQL, consulte: 

* [Visão geral dos VMs do servidor SQL](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Notas de lançamento para VMs do servidor SQL](../../database/doc-changes-updates-release-notes.md)
* [FAQ para VMs do servidor SQL](frequently-asked-questions-faq.md)