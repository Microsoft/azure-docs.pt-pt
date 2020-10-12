---
title: Criar um FCI com discos partilhados Azure (Pré-visualização)
description: Utilize discos partilhados Azure para criar uma instância de cluster failover (FCI) com o SQL Server em Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2020
ms.author: mathoma
ms.openlocfilehash: 6e32f183709aca8a78f8448f2d6e6b63a77f2133
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91272655"
---
# <a name="create-an-fci-with-azure-shared-disks-sql-server-on-azure-vms"></a>Criar um FCI com discos partilhados Azure (SQL Server em VMs Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo explica como criar uma instância de cluster failover (FCI) utilizando discos partilhados Azure com SQL Server em Azure Virtual Machines (VMs). 

Para saber mais, consulte uma visão geral da [FCI com o SQL Server em VMs Azure](failover-cluster-instance-overview.md) e [as melhores práticas do cluster](hadr-cluster-best-practices.md). 


## <a name="prerequisites"></a>Pré-requisitos 

Antes de completar as instruções deste artigo, já deve ter:

- Uma subscrição do Azure. Começa de [graça.](https://azure.microsoft.com/free/) 
- [Duas ou mais máquinas virtuais Windows Azure](failover-cluster-instance-prepare-vm.md). [Os conjuntos de disponibilidade](../../../virtual-machines/windows/tutorial-availability-sets.md) e [os grupos de colocação de proximidade](../../../virtual-machines/windows/co-location.md#proximity-placement-groups) (PPGs) são ambos suportados. Se utilizar um PPG, todos os nós devem existir no mesmo grupo.
- Uma conta que tem permissões para criar objetos tanto em máquinas virtuais Azure como no Ative Directory.
- A versão mais recente do [PowerShell.](/powershell/azure/install-az-ps?view=azps-4.2.0) 


## <a name="add-azure-shared-disk"></a>Adicione disco compartilhado Azure
Implemente um disco SSD Premium gerido com a função de disco partilhado ativada. De `maxShares` forma a **alinhar-se com o número de nós de cluster** para tornar o disco partilhável em todos os nós fci. 

Adicione um disco compartilhado Azure fazendo o seguinte: 


1. Guarde o seguinte script à medida * queSharedDiskConfig.jsem*: 

   ```JSON
   { 
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "dataDiskName": {
         "type": "string",
         "defaultValue": "mySharedDisk"
       },
       "dataDiskSizeGB": {
         "type": "int",
         "defaultValue": 1024
       },
       "maxShares": {
         "type": "int",
         "defaultValue": 2
       }
     },
     "resources": [
       {
         "type": "Microsoft.Compute/disks",
         "name": "[parameters('dataDiskName')]",
         "location": "[resourceGroup().location]",
         "apiVersion": "2019-07-01",
         "sku": {
           "name": "Premium_LRS"
         },
         "properties": {
           "creationData": {
             "createOption": "Empty"
           },
           "diskSizeGB": "[parameters('dataDiskSizeGB')]",
           "maxShares": "[parameters('maxShares')]"
         }
       }
     ] 
   }
   ```


2. Executar *SharedDiskConfig.jsutilizando* o PowerShell: 

   ```powershell
   $rgName = < specify your resource group name>
       $location = 'westcentralus'
       New-AzResourceGroupDeployment -ResourceGroupName $rgName `
   -TemplateFile "SharedDiskConfig.json"
   ```

3. Para cada VM, inicialize os discos partilhados anexados como tabela de partição GUID (GPT) e formate-os como New Technology File System (NTFS) executando este comando: 

   ```powershell
   $resourceGroup = "<your resource group name>"
       $location = "<region of your shared disk>"
       $ppgName = "<your proximity placement groups name>"
       $vm = Get-AzVM -ResourceGroupName "<your resource group name>" `
           -Name "<your VM node name>"
       $dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup `
           -DiskName "<your shared disk name>"
       $vm = Add-AzVMDataDisk -VM $vm -Name "<your shared disk name>" `
           -CreateOption Attach -ManagedDiskId $dataDisk.Id `
           -Lun <available LUN  check disk setting of the VM>
    update-AzVm -VM $vm -ResourceGroupName $resourceGroup
   ```


## <a name="create-failover-cluster"></a>Criar cluster de failover

Para criar o cluster de failover, você precisa:

- Os nomes das máquinas virtuais que se tornarão os nós do cluster.
- Um nome para o aglomerado de falhanços.
- Um endereço IP para o cluster de failover. Pode utilizar um endereço IP que não seja utilizado na mesma rede virtual Azure e sub-rede que os nós de cluster.


# <a name="windows-server-2012-2016"></a>[Windows Server 2012-2016](#tab/windows2012)

O seguinte script PowerShell cria um cluster de falha. Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível a partir da rede virtual Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

O seguinte script PowerShell cria um cluster de falha. Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível a partir da rede virtual Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Para obter mais informações, consulte [o cluster Failover: Cluster Network Object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>Configure quórum

Configure a solução de quórum que melhor se adapte às necessidades do seu negócio. Pode configurar uma [Testemunha de Disco,](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)uma [Testemunha em Nuvem](/windows-server/failover-clustering/deploy-cloud-witness)ou uma Testemunha de Partilha de [Ficheiros](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum). Para obter mais informações, consulte [Quorum com VMs do SQL Server](hadr-cluster-best-practices.md#quorum). 

## <a name="validate-cluster"></a>Validar o cluster
Valide o cluster na UI ou utilizando o PowerShell.

Para validar o cluster utilizando a UI, faça o seguinte numa das máquinas virtuais:

1. Sob **o Gestor do Servidor**, selecione **Ferramentas**e, em seguida, selecione **O Gestor de Cluster Failover**.
1. Em **'Failover Cluster Manager',** selecione **Ação**e, em seguida, selecione **Validate Configuration**.
1. Selecione **Seguinte**.
1. Em **Servidores Selecionados ou num Cluster,** insira os nomes de ambas as máquinas virtuais.
1. Nas **opções de Teste,** selecione **Executar apenas testes que seleciono**. 
1. Selecione **Seguinte**.
1. Em **Seleção de Testes**, selecione todos os testes, *exceto* **Armazenamento**

## <a name="test-cluster-failover"></a>Falha do cluster de teste

Teste o fracasso do seu aglomerado. No **Failover Cluster Manager,** clique com o botão direito no seu cluster, selecione **Mais Ações**  >  **Move Core Cluster Resource**Select  >  **node**e, em seguida, selecione o outro nó do cluster. Mova o recurso de cluster do núcleo para cada nó do cluster e, em seguida, movimente-o de volta para o nó primário. Se conseguir mover o cluster com sucesso para cada nó, está pronto para instalar o SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Falha no cluster de teste, movendo o recurso do núcleo para os outros nos dois":::

## <a name="create-sql-server-fci"></a>Criar SQL Server FCI

Depois de configurar o cluster failover e todos os componentes do cluster, incluindo o armazenamento, pode criar o SQL Server FCI.

1. Ligue-se à primeira máquina virtual utilizando o Protocolo de Ambiente de Trabalho Remoto (RDP).

1. No **Failover Cluster Manager,** certifique-se de que todos os recursos de cluster de núcleo estão na primeira máquina virtual. Se necessário, mova todos os recursos para a máquina virtual.

1. Localize os meios de instalação. Se a máquina virtual utilizar uma das imagens do Azure Marketplace, os meios de comunicação estão localizados em `C:\SQLServer_<version number>_Full` . 

1. Selecione **Configuração**.

1. No **Centro de Instalação do Servidor SQL,** selecione **Instalação**.

1. Selecione **a instalação de cluster de falha do novo sql server**. Siga as instruções do assistente para instalar o SQL Server FCI.

Os diretórios de dados da FCI têm de estar nos Discos Partilhados do Azure. 

1. Depois de completar as instruções no assistente, a Configuração instalará um SQL Server FCI no primeiro nó.

1. Depois de configurar a INSTALAÇÃO instala o FCI no primeiro nó, ligue-o ao segundo nó utilizando RDP.

1. Abra o **Centro de Instalação do Servidor SQL**e, em seguida, selecione **Instalação**.

1. **Selecione Adicionar nó a um cluster de falha do sql server**. Siga as instruções do assistente para instalar o SQL Server e adicione o servidor à FCI.

   >[!NOTE]
   >Se usou uma imagem de galeria do Azure Marketplace que contém SQL Server, as ferramentas sql Server foram incluídas com a imagem. Se não usou uma dessas imagens, instale as ferramentas SQL Server separadamente. Para mais informações, consulte [o Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).
   >

## <a name="register-with-the-sql-vm-rp"></a>Registe-se com o SQL VM RP

Para gerir o seu SQL Server VM a partir do portal, registe-o com o fornecedor de recursos SQL VM (RP) em [modo de gestão leve,](sql-vm-resource-provider-register.md#lightweight-management-mode)atualmente o único modo suportado com FCI e SQL Server em VMs Azure. 


Registar um SQL Server VM em modo leve com PowerShell:  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Configurar a conectividade 

Para encaminhar o tráfego adequadamente para o nó primário atual, configufique a opção de conectividade adequada para o seu ambiente. Pode criar um [balanceador de carga Azure](hadr-vnn-azure-load-balancer-configure.md) ou, se estiver a utilizar o SQL Server 2019 CU2+ e o Windows Server 2016 (ou mais tarde) pode visualizar a funcionalidade [de nome de rede distribuída.](hadr-distributed-network-name-dnn-configure.md) 

## <a name="limitations"></a>Limitações

- Apenas é suportado o registo com o fornecedor de recursos SQL VM em [modo de gestão leve.](sql-vm-resource-provider-register.md#management-modes)

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, configugue a conectividade com o seu FCI com um [nome de rede virtual e um equilibrador de carga Azure](hadr-vnn-azure-load-balancer-configure.md) ou nome de rede distribuído [(DNN)](hadr-distributed-network-name-dnn-configure.md). 

Se os discos partilhados do Azure não forem a solução de armazenamento FCI adequada para si, considere criar o seu FCI utilizando [ações de ficheiros premium](failover-cluster-instance-premium-file-share-manually-configure.md) ou [espaços de armazenamento Direto.](failover-cluster-instance-storage-spaces-direct-manually-configure.md) 

Para saber mais, consulte uma visão geral da [FCI com o SQL Server em VMs Azure](failover-cluster-instance-overview.md) e [as melhores práticas de configuração do cluster](hadr-cluster-best-practices.md).

Para obter mais informações, veja: 
- [Tecnologias de cluster windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server falha casos de cluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
