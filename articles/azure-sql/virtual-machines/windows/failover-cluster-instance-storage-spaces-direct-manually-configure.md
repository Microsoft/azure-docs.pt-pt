---
title: Criar uma FCI com espaços de armazenamento direto
description: Utilize espaços de armazenamento diretamente para criar uma instância de cluster failover (FCI) com o SQL Server em máquinas virtuais Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: aa19cf6b59b1efa4b14501fbf64e319da3e4c0b3
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048646"
---
# <a name="create-an-fci-with-storage-spaces-direct-sql-server-on-azure-vms"></a>Criar um FCI com espaços de armazenamento direto (SQL Server em VMs Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo explica como criar uma instância de cluster failover (FCI) utilizando [espaços de armazenamento direto](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) com sql server em Azure Virtual Machines (VMs). Espaços de Armazenamento Direto funciona como uma rede de área de armazenamento virtual baseada em software (VSAN) que sincroniza o armazenamento (discos de dados) entre os nós (Azure VMs) num cluster Windows. 

Para saber mais, consulte uma visão geral da [FCI com o SQL Server em VMs Azure](failover-cluster-instance-overview.md) e [as melhores práticas do cluster](hadr-cluster-best-practices.md). 


## <a name="overview"></a>Descrição Geral 

[Espaços de Armazenamento Direct (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) suporta dois tipos de arquiteturas: convergentes e hiperconvergados. Uma infraestrutura hiperconverizada coloca o armazenamento nos mesmos servidores que acolhem a aplicação agrupada, de modo que o armazenamento está em cada nó SQL Server FCI. 

O diagrama a seguir mostra a solução completa, que utiliza espaços de armazenamento hiperconvergados diretamente com o Servidor SQL em VMs Azure: 

![Diagrama da solução completa, utilizando espaços de armazenamento hiperconvergados direto](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

O diagrama anterior mostra os seguintes recursos no mesmo grupo de recursos:

- Duas máquinas virtuais num cluster de falha do Windows Server. Quando uma máquina virtual está num aglomerado de falhas, também é chamada de nó de *cluster* ou *nó.*
- Cada máquina virtual tem dois ou mais discos de dados.
- Espaços de Armazenamento O Direct sincroniza os dados dos discos de dados e apresenta o armazenamento sincronizado como um conjunto de armazenamento.
- A piscina de armazenamento apresenta um Cluster Shared Volume (CSV) para o cluster failover.
- A função de cluster SQL Server FCI utiliza o CSV para as unidades de dados.
- Um balançador de carga Azure para manter o endereço IP para o SQL Server FCI.
- Um conjunto de disponibilidade Azure contém todos os recursos.

   > [!NOTE]
   > Você pode criar toda esta solução em Azure a partir de um modelo. Um exemplo de um modelo está disponível na página de [modelos de quickstart GitHub Azure.](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) Este exemplo não é projetado ou testado para qualquer carga de trabalho específica. Pode executar o modelo para criar um SQL Server FCI com espaços de armazenamento Armazenamento Armazenamento Direto ligado ao seu domínio. Pode avaliar o modelo e modificá-lo para os seus propósitos.


## <a name="prerequisites"></a>Pré-requisitos

Antes de completar as instruções deste artigo, já deve ter:

- Uma subscrição do Azure. Começa de [graça.](https://azure.microsoft.com/free/) 
- [Duas ou mais máquinas virtuais do Windows Azure preparadas](failover-cluster-instance-prepare-vm.md) num [conjunto de disponibilidades](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).
- Uma conta que tem permissões para criar objetos tanto em máquinas virtuais Azure como no Ative Directory.
- A versão mais recente do [PowerShell.](/powershell/azure/install-az-ps) 


## <a name="add-the-windows-cluster-feature"></a>Adicione a funcionalidade de cluster do Windows

1. Ligue-se à primeira máquina virtual utilizando o Remote Desktop Protocol (RDP) com uma conta de domínio que é membro dos administradores locais e que tem permissão para criar objetos no Ative Directory. Utilize esta conta para o resto da configuração.

1. Adicione o agrupamento de falhas a cada máquina virtual.

   Para instalar o agrupamento de falhas a partir da UI, faça o seguinte em ambas as máquinas virtuais:

   1. No **Gestor do Servidor**, selecione **Gerir** e, em seguida, selecione **Adicionar Funções e Funcionalidades**.
   1. No assistente **de adicionar funções e funcionalidades,** selecione **Seguinte** até obter **funcionalidades selecionadas**.
   1. Em **Funcionalidades Selecionadas**, selecione **Clustering Failover**. Inclua todas as funcionalidades necessárias e as ferramentas de gestão. 
   1. Selecione **adicionar funcionalidades**.
   1. Selecione **Seguinte** e, em seguida, selecione **Finish** para instalar as funcionalidades.

   Para instalar o agrupamento de falhas utilizando o PowerShell, execute o seguinte script a partir de uma sessão PowerShell de administrador numa das máquinas virtuais:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Para obter mais informações sobre os próximos passos, consulte as instruções na secção "Passo 3: Configurar espaços de armazenamento direto" da [solução Hyperconverged utilizando espaços de armazenamento direto no Windows Server 2016](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-3-configure-storage-spaces-direct).


## <a name="validate-the-cluster"></a>Validar o cluster

Valide o cluster na UI ou utilizando o PowerShell.

Para validar o cluster utilizando a UI, faça o seguinte numa das máquinas virtuais:

1. Sob **o Gestor do Servidor**, selecione **Ferramentas** e, em seguida, selecione **O Gestor de Cluster Failover**.
1. Em **'Failover Cluster Manager',** selecione **Ação** e, em seguida, selecione **Validate Configuration**.
1. Selecione **Seguinte**.
1. Em **Servidores Selecionados ou num Cluster,** insira os nomes de ambas as máquinas virtuais.
1. Nas **opções de Teste,** selecione **Executar apenas testes que seleciono**. 
1. Selecione **Seguinte**.
1. Em **Seleção de Testes**, selecione todos os testes, exceto **para armazenamento,** como mostrado aqui:

   ![Selecione testes de validação de clusters](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. Selecione **Seguinte**.
1. Em **Confirmação**, selecione **Seguinte**.

    O assistente **de validação de uma configuração** executa os testes de validação.

Para validar o cluster utilizando o PowerShell, execute o seguinte script a partir de uma sessão PowerShell de administrador numa das máquinas virtuais:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Depois de validar o cluster, crie o cluster de failover.


## <a name="create-failover-cluster"></a>Criar cluster de failover

Para criar o cluster de failover, você precisa:

- Os nomes das máquinas virtuais que se tornarão os nós do cluster.
- Um nome para o aglomerado de falhanços.
- Um endereço IP para o cluster de failover. Pode utilizar um endereço IP que não seja utilizado na mesma rede virtual Azure e sub-rede que os nós de cluster.


# <a name="windows-server-2012---2016"></a>[Windows Server 2012 - 2016](#tab/windows2012)

O seguinte script PowerShell cria um cluster de falha para o Windows Server 2012 através do Windows Server 2016. Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível a partir da rede virtual Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

O seguinte script PowerShell cria um cluster de falha para o Windows Server 2019.  Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível a partir da rede virtual Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Para obter mais informações, consulte [o cluster Failover: Cluster Network Object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>Configure quórum

Configure a solução de quórum que melhor se adapte às necessidades do seu negócio. Pode configurar uma [Testemunha de Disco,](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)uma [Testemunha em Nuvem](/windows-server/failover-clustering/deploy-cloud-witness)ou uma Testemunha de Partilha de [Ficheiros](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum). Para obter mais informações, consulte [Quorum com VMs do SQL Server](hadr-cluster-best-practices.md#quorum). 

## <a name="add-storage"></a>Adicionar armazenamento

Os discos para espaços de armazenamento diretos têm de estar vazios. Não podem conter divisórias ou outros dados. Para limpar os discos, siga as instruções em [Implementar espaços de armazenamento direto](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-31-clean-drives).

1. [Ativar os espaços de armazenamento direto.](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-35-enable-storage-spaces-direct)

   O seguinte script PowerShell permite espaços de armazenamento direto:  

   ```powershell
   Enable-ClusterS2D
   ```

   No **Failover Cluster Manager,** já pode ver a piscina de armazenamento.

1. [Criar um volume](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-36-create-volumes).

   Espaços de Armazenamento O Direct cria automaticamente uma piscina de armazenamento quando o ativa. Agora estás pronto para criar um volume. O cmdlet PowerShell `New-Volume` automatiza o processo de criação de volume. Este processo inclui formatação, adicionar o volume ao cluster e criar um CSV. Este exemplo cria um CSV de 800 gigabytes (GB):

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Depois de executar o comando anterior, um volume de 800 GB é montado como um recurso de cluster. O volume está em `C:\ClusterStorage\Volume1\` .

   Esta imagem mostra um CSV com espaços de armazenamento direto:

   ![Screenshot de um cluster volume compartilhado com espaços de armazenamento direto](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)



## <a name="test-cluster-failover"></a>Falha do cluster de teste

Teste o fracasso do seu aglomerado. No **Failover Cluster Manager,** clique com o botão direito no seu cluster, selecione **Mais Ações**  >  **Move Core Cluster Resource** Select  >  **node** e, em seguida, selecione o outro nó do cluster. Mova o recurso de cluster do núcleo para cada nó do cluster e, em seguida, movimente-o de volta para o nó primário. Se conseguir mover o cluster com sucesso para cada nó, está pronto para instalar o SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Falha no cluster de teste, movendo o recurso do núcleo para os outros nos dois":::

## <a name="create-sql-server-fci"></a>Criar SQL Server FCI

Depois de configurar o cluster failover e todos os componentes do cluster, incluindo o armazenamento, pode criar o SQL Server FCI.

1. Ligue-se à primeira máquina virtual utilizando RDP.

1. No **Failover Cluster Manager,** certifique-se de que todos os recursos de cluster do núcleo estão na primeira máquina virtual. Se necessário, mova todos os recursos para a máquina virtual.

1. Localize os meios de instalação. Se a máquina virtual utilizar uma das imagens do Azure Marketplace, os meios de comunicação estão localizados em `C:\SQLServer_<version number>_Full` . Selecione **Configuração**.

1. No **Centro de Instalação do Servidor SQL,** selecione **Instalação**.

1. Selecione **a instalação de cluster de falha do novo sql server**. Siga as instruções do assistente para instalar o SQL Server FCI.

   Os diretórios de dados da FCI têm de estar no armazenamento agrupado. Com os Espaços de Armazenamento Direto, não é um disco partilhado, mas um ponto de montagem para um volume em cada servidor. Espaços de Armazenamento Sincronizam diretamente o volume entre os dois nós. O volume é apresentado ao cluster como um CSV. Utilize o ponto de montagem CSV para os diretórios de dados.

   ![Diretórios de dados](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. Depois de completar as instruções no assistente, a Configuração instala um SQL Server FCI no primeiro nó.

1. Depois de configurar a INSTALAÇÃO instala o FCI no primeiro nó, ligue-o ao segundo nó utilizando RDP.

1. Abra o **Centro de Instalação do Servidor SQL**. Selecione **instalação**.

1. **Selecione Adicionar nó a um cluster de falha do sql server**. Siga as instruções do assistente para instalar o SQL Server e adicione o servidor à FCI.

   >[!NOTE]
   >Se usou uma imagem de galeria do Azure Marketplace que contém SQL Server, as ferramentas sql Server foram incluídas com a imagem. Se não usou uma dessas imagens, instale as ferramentas SQL Server separadamente. Para mais informações, consulte [o Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
   >


## <a name="register-with-the-sql-vm-rp"></a>Registe-se com o SQL VM RP

Para gerir o seu SQL Server VM a partir do portal, registe-o com a extensão sql IaaS Agent (RP) no [modo de gestão leve](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode), atualmente o único modo suportado com FCI e SQL Server em VMs Azure. 


Registar um SQL Server VM em modo leve com PowerShell:  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Configurar a conectividade 

Para encaminhar o tráfego adequadamente para o nó primário atual, configufique a opção de conectividade adequada para o seu ambiente. Pode criar um [equilibrador de carga Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) ou, se estiver a utilizar o SQL Server 2019 CU2 (ou mais tarde) e o Windows Server 2016 (ou mais tarde), pode utilizar a funcionalidade [de nome de rede distribuída.](failover-cluster-instance-distributed-network-name-dnn-configure.md) 

Para obter mais detalhes sobre as opções de conectividade do cluster, consulte [as ligações Route HADR ao SQL Server em VMs Azure](hadr-cluster-best-practices.md#connectivity). 

## <a name="limitations"></a>Limitações

- As máquinas virtuais Azure suportam o Coordenador de Transações Distribuídas da Microsoft (MSDTC) no Windows Server 2019 com armazenamento em CSVs e um [balanceador de carga padrão](../../../load-balancer/load-balancer-overview.md).
- Os discos que tenham sido anexados como discos formatados NTFS só podem ser utilizados com espaços de armazenamento diretos apenas se a opção de elegibilidade do disco não for verificada ou limpa quando o armazenamento for adicionado ao cluster. 
- Apenas é suportado o registo com a extensão sql IaaS Agent em [modo de gestão leve.](sql-server-iaas-agent-extension-automate-management.md#management-modes)

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, configugue a conectividade com o seu FCI com um [nome de rede virtual e um equilibrador de carga Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) ou nome de rede distribuído [(DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 

Se o Storage Spaces Direct não for a solução de armazenamento FCI adequada para si, considere criar o seu FCI utilizando [discos partilhados Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) ou [Partilhas de Ficheiros Premium.](failover-cluster-instance-premium-file-share-manually-configure.md) 

Para saber mais, consulte uma visão geral da [FCI com o SQL Server em VMs Azure](failover-cluster-instance-overview.md) e [as melhores práticas de configuração do cluster](hadr-cluster-best-practices.md). 

Para obter mais informações, consulte: 
- [Tecnologias de cluster windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server falha casos de cluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)