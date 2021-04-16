---
title: Criar um FCI com uma partilha de ficheiros premium
description: Utilize uma partilha de ficheiros premium (PFS) para criar uma instância de cluster failover (FCI) com o SQL Server em máquinas virtuais Azure.
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
ms.openlocfilehash: ddd25c605ef159bddfb8a9c7cb4d02ac7094c511
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482199"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>Criar um FCI com uma partilha de ficheiros premium (SQL Server em VMs Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo explica como criar uma instância de cluster failover (FCI) com o SQL Server em Azure Virtual Machines (VMs) utilizando uma [partilha de ficheiros premium](../../../storage/files/storage-how-to-create-file-share.md).

As ações de ficheiros premium são partilhas de ficheiros de armazenamento Diretas (SSD), consistentemente de baixa latência, que são totalmente suportadas para utilização com casos de cluster de failover para SQL Server 2012 ou mais tarde no Windows Server 2012 ou mais tarde. As ações de ficheiros premium conferem-lhe maior flexibilidade, permitindo-lhe redimensionar e escalar uma partilha de ficheiros sem qualquer tempo de inatividade.

Para saber mais, consulte uma visão geral da [FCI com o SQL Server em VMs Azure](failover-cluster-instance-overview.md) e [as melhores práticas do cluster](hadr-cluster-best-practices.md). 

## <a name="prerequisites"></a>Pré-requisitos

Antes de completar as instruções deste artigo, já deve ter:

- Uma subscrição do Azure.
- Uma conta que tem permissões para criar objetos tanto em máquinas virtuais Azure como no Ative Directory.
- [Duas ou mais máquinas virtuais do Windows Azure preparadas](failover-cluster-instance-prepare-vm.md) num [conjunto de disponibilidades](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) ou [diferentes zonas de disponibilidade](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- Uma [partilha de ficheiro premium](../../../storage/files/storage-how-to-create-file-share.md) para ser usada como unidade agrupada, com base na quota de armazenamento da sua base de dados para os seus ficheiros de dados.
- A versão mais recente do [PowerShell.](/powershell/azure/install-az-ps) 

## <a name="mount-premium-file-share"></a>Monte partilha de arquivo premium

1. Inicie sessão no [portal do Azure](https://portal.azure.com). e ir para a sua conta de armazenamento.
1. Vá a **''' Arquivar ações** em **serviço de ficheiros** e, em seguida, selecione a partilha de ficheiros premium que pretende utilizar para o seu armazenamento SQL.
1. Selecione **Connect** para aumentar a cadeia de ligação para a sua partilha de ficheiros.
1. Na lista de drop-down, selecione a letra de unidade que pretende utilizar e, em seguida, copie ambos os blocos de código para o Bloco de Notas.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="Copie ambos os comandos PowerShell do portal de ligação de partilha de ficheiros":::

1. Utilize o Protocolo de Ambiente de Trabalho Remoto (RDP) para ligar ao SQL Server VM com a conta que o seu SQL Server FCI utilizará para a conta de serviço.
1. Abra uma consola administrativa de comando PowerShell.
1. Executar os comandos que guardou mais cedo quando estava a trabalhar no portal.
1. Aceda à partilha utilizando o File Explorer ou a caixa de diálogo **Executar** (selecione Windows + R). Utilize o caminho da `\\storageaccountname.file.core.windows.net\filesharename` rede. Por exemplo, `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Crie pelo menos uma pasta na partilha de ficheiros recentemente ligada para colocar os seus ficheiros de dados SQL.
1. Repita estes passos em cada SQL Server VM que participará no cluster.

  > [!IMPORTANT]
  > - Considere utilizar uma partilha de ficheiros separada para ficheiros de backup para guardar as operações de entrada/saída por segundo (IOPS) e a capacidade de espaço desta partilha para ficheiros de dados e registos. Pode utilizar uma Partilha de Ficheiros Premium ou Standard para ficheiros de backup.
  > - Se estiver no Windows 2012 R2 ou mais cedo, siga estes mesmos passos para montar a partilha de ficheiros que vai usar como testemunha de partilha de ficheiros. 
  > 


## <a name="add-windows-cluster-feature"></a>Adicionar funcionalidade de cluster windows

1. Ligue-se à primeira máquina virtual com RDP utilizando uma conta de domínio que é membro dos administradores locais e que tem permissão para criar objetos no Ative Directory. Utilize esta conta para o resto da configuração.

1. [Adicione o agrupamento de falhas a cada máquina virtual](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

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

## <a name="validate-cluster"></a>Validar o cluster

Valide o cluster na UI ou utilizando o PowerShell.

Para validar o cluster utilizando a UI, faça o seguinte numa das máquinas virtuais:

1. Sob **o Gestor do Servidor**, selecione **Ferramentas** e, em seguida, selecione **O Gestor de Cluster Failover**.
1. Em **'Failover Cluster Manager',** selecione **Ação** e, em seguida, selecione **Validate Configuration**.
1. Selecione **Seguinte**.
1. Em **Servidores Selecionados ou num Cluster,** insira os nomes de ambas as máquinas virtuais.
1. Nas **opções de Teste,** selecione **Executar apenas testes que seleciono**. 
1. Selecione **Seguinte**.
1. Em **Seleção de Testes**, selecione todos os testes, exceto para espaços **de armazenamento** e armazenamento **direto,** como mostrado aqui:

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="Selecione testes de validação de clusters":::

1. Selecione **Seguinte**.
1. Em **Confirmação**, selecione **Seguinte**.

O assistente **de validação de uma configuração** executa os testes de validação.

Para validar o cluster utilizando o PowerShell, execute o seguinte script a partir de uma sessão PowerShell de administrador numa das máquinas virtuais:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
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


## <a name="test-cluster-failover"></a>Falha do cluster de teste

Teste o fracasso do seu aglomerado. No **Failover Cluster Manager,** clique com o botão direito no seu cluster, selecione **Mais Ações**  >  **Move Core Cluster Resource** Select  >  **node** e, em seguida, selecione o outro nó do cluster. Mova o recurso de cluster do núcleo para cada nó do cluster e, em seguida, movimente-o de volta para o nó primário. Se conseguir mover o cluster com sucesso para cada nó, está pronto para instalar o SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Falha no cluster de teste, movendo o recurso do núcleo para os outros nos dois":::


## <a name="create-sql-server-fci"></a>Criar SQL Server FCI

Depois de configurar o cluster failover, pode criar o SQL Server FCI.

1. Ligue-se à primeira máquina virtual utilizando RDP.

1. No **Failover Cluster Manager,** certifique-se de que todos os recursos de cluster do núcleo estão na primeira máquina virtual. Se necessário, mova todos os recursos para esta máquina virtual.

1. Localize os meios de instalação. Se a máquina virtual utilizar uma das imagens do Azure Marketplace, os meios de comunicação estão localizados em `C:\SQLServer_<version number>_Full` . 

1. Selecione **Configuração**.

1. No **Centro de Instalação do Servidor SQL,** selecione **Instalação**.

1. Selecione **a instalação do cluster de falha** do novo SQL Server e siga as instruções do assistente para instalar o SQL Server FCI.

   Os diretórios de dados da FCI têm de estar na parte de ficheiros premium. Insira o caminho completo da partilha, neste formato: `\\storageaccountname.file.core.windows.net\filesharename\foldername` . Aparecerá um aviso, informando-o de que especificou um servidor de ficheiros como diretório de dados. Este aviso é esperado. Certifique-se de que a conta de utilizador utilizada para aceder ao VM via RDP quando persistisse a partilha de ficheiros é a mesma que o serviço SQL Server utiliza para evitar possíveis falhas.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="Use a partilha de ficheiros como diretórios de dados SQL":::

1. Depois de completar os passos no assistente, a Configuração instalará um SQL Server FCI no primeiro nó.

1. Depois de configurar a INSTALAÇÃO instala o FCI no primeiro nó, ligue-o ao segundo nó utilizando RDP.

1. Abra o **Centro de Instalação do Servidor SQL** e, em seguida, selecione **Instalação**.

1. **Selecione Adicionar nó a um cluster de falha do sql server**. Siga as instruções do assistente para instalar o SQL Server e adicione o servidor à FCI.

   >[!NOTE]
   >Se usou uma imagem de galeria do Azure Marketplace com o SQL Server, as ferramentas sql Server foram incluídas com a imagem. Se não usou uma dessas imagens, instale as ferramentas SQL Server separadamente. Para mais informações, consulte [o Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

1. Repita estes passos em quaisquer outros nós que pretenda adicionar à instância de cluster de falha do SQL Server. 

## <a name="register-with-the-sql-vm-rp"></a>Registe-se com o SQL VM RP

Para gerir o seu SQL Server VM a partir do portal, registe-o com a extensão sql IaaS Agent (RP) no [modo de gestão leve](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode), atualmente o único modo suportado com FCI e SQL Server em VMs Azure. 

Registar um SQL Server VM em modo leve com PowerShell (-LicenseType pode ser `PAYG` `AHUB` ou):

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Configurar a conectividade 

Para encaminhar o tráfego adequadamente para o nó primário atual, configufique a opção de conectividade adequada para o seu ambiente. Pode criar um [equilibrador de carga Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) ou, se estiver a utilizar o SQL Server 2019 CU2 (ou mais tarde) e o Windows Server 2016 (ou mais tarde), pode utilizar a funcionalidade [de nome de rede distribuída.](failover-cluster-instance-distributed-network-name-dnn-configure.md) 

Para obter mais detalhes sobre as opções de conectividade do cluster, consulte [as ligações Route HADR ao SQL Server em VMs Azure](hadr-cluster-best-practices.md#connectivity). 

## <a name="limitations"></a>Limitações

- O Coordenador de Transações Distribuídas da Microsoft (MSDTC) não é suportado no Windows Server 2016 e anteriormente. 
- O Filestream não é suportado por um cluster de failover com uma partilha de ficheiros premium. Para utilizar o fluxo de ficheiros, desloque o seu cluster utilizando os discos partilhados [Desrmessórios Diretos](failover-cluster-instance-storage-spaces-direct-manually-configure.md) ou [Azure.](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- Apenas é suportado o registo com a extensão sql IaaS Agent em [modo de gestão leve.](sql-server-iaas-agent-extension-automate-management.md#management-modes) 
- As fotos de dados não são atualmente suportadas com [ficheiros Azure devido a limitações de ficheiros escassos](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).  

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, configugue a conectividade com o seu FCI com um [nome de rede virtual e um equilibrador de carga Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) ou nome de rede distribuído [(DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 


Se as ações de ficheiros premium não forem a solução de armazenamento fci adequada para si, considere criar o seu FCI utilizando [discos partilhados Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) ou [Espaços de Armazenamento Direto.](failover-cluster-instance-storage-spaces-direct-manually-configure.md) 

Para saber mais, consulte uma visão geral da [FCI com o SQL Server em VMs Azure](failover-cluster-instance-overview.md) e [as melhores práticas de configuração do cluster](hadr-cluster-best-practices.md). 

Para obter mais informações, consulte: 
- [Tecnologias de cluster windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server falha casos de cluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
