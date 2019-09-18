---
title: SQL Server FCI-máquinas virtuais do Azure | Microsoft Docs
description: Este artigo explica como criar SQL Server instância de cluster de failover em máquinas virtuais do Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: 3e954a6c714e525e5bbefe8f62c798cf8ac9a517
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036388"
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Configurar SQL Server instância de cluster de failover em máquinas virtuais do Azure

Este artigo explica como criar uma FCI (instância de cluster de failover) SQL Server em máquinas virtuais do Azure no modelo do Resource Manager. Essa solução usa o [Windows Server 2016 Datacenter Edition \(espaços de armazenamento diretos\) S2D](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) como uma SAN virtual baseada em software que sincroniza o armazenamento (discos de dados) entre os nós (VMS do Azure) em um cluster do Windows. O S2D é novo no Windows Server 2016.

O diagrama a seguir mostra a solução completa em máquinas virtuais do Azure:

![Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

O diagrama anterior mostra:

- Duas máquinas virtuais do Azure em um cluster de failover do Windows. Quando uma máquina virtual está em um cluster de failover, ela também é chamada de *nó de cluster*ou *nós*.
- Cada máquina virtual tem dois ou mais discos de dados.
- O S2D sincroniza os dados no disco de dados e apresenta o armazenamento sincronizado como um pool de armazenamento.
- O pool de armazenamento apresenta um CSV (volume compartilhado clusterizado) para o cluster de failover.
- A função de cluster SQL Server FCI usa o CSV para as unidades de dados.
- Um balanceador de carga do Azure para manter o endereço IP para o SQL Server FCI.
- Um conjunto de disponibilidade do Azure mantém todos os recursos.

   >[!NOTE]
   >Todos os recursos do Azure estão no diagrama no mesmo grupo de recursos.

Para obter detalhes sobre o s2d, consulte [Windows Server 2016 datacenter \(Edition\)espaços de armazenamento diretos S2D](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

O S2D dá suporte a dois tipos de arquiteturas-convergido e hiperconvergente. A arquitetura deste documento é hiperconvergente. Uma infraestrutura hiperconvergente coloca o armazenamento nos mesmos servidores que hospedam o aplicativo clusterizado. Nessa arquitetura, o armazenamento está em cada nó de SQL Server FCI.

## <a name="licensing-and-pricing"></a>Licenciamento e preços

Em máquinas virtuais do Azure, você pode licenciar SQL Server usando suas imagens de VM PAYG (BYOL) ou traga sua própria licença. O tipo de imagem que você escolhe afeta como você é cobrado.

Com o licenciamento PAYG, uma FCI (instância de cluster de failover) de SQL Server em máquinas virtuais do Azure incorre em encargos para todos os nós de FCI, incluindo os nós passivos. Para obter mais informações, consulte [SQL Server Enterprise preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

Os clientes com Enterprise Agreement com o Software Assurance têm o direito de usar um nó FCI passivo gratuito para cada nó ativo. Para aproveitar esse benefício no Azure, use imagens de VM BYOL e, em seguida, use a mesma licença nos nós ativo e passivo do FCI. Para obter mais informações, consulte [Enterprise Agreement](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Para comparar o licenciamento PAYG e BYOL para SQL Server em máquinas virtuais do Azure, confira introdução [às VMs do SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Para obter informações completas sobre SQL Server de licenciamento, consulte [preços](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Modelo do Azure de exemplo

Você pode criar a solução inteira no Azure a partir de um modelo. Um exemplo de um modelo está disponível nos modelos de [início rápido do Azure do](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad)github. Este exemplo não é projetado ou testado para qualquer carga de trabalho específica. Você pode executar o modelo para criar um SQL Server FCI com o armazenamento S2D conectado ao seu domínio. Você pode avaliar o modelo e modificá-lo para suas finalidades.

## <a name="before-you-begin"></a>Antes de começar

Há algumas coisas que você precisa saber e algumas coisas que você precisa ter antes de continuar.

### <a name="what-to-know"></a>O que saber
Você deve ter uma compreensão operacional das seguintes tecnologias:

- [Tecnologias de cluster do Windows](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server instâncias de cluster de failover](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).

Uma diferença importante é que, em um cluster de failover convidado da VM IaaS do Azure, recomendamos uma única NIC por servidor (nó de cluster) e uma única sub-rede. As redes do Azure têm redundância físicas, as quais tornam desnecessários NICs e sub-redes adicionais num cluster convidado da VM IaaS do Azure. Embora o relatório de validação do cluster emita um aviso de que os nós apenas são acessíveis numa única rede, este aviso pode ser ignorado com segurança nos clusters de ativação pós-falha convidados da VM IaaS do Azure. 

Além disso, você deve ter uma compreensão geral das seguintes tecnologias:

- [Solução hiperconvergente usando o Espaços de Armazenamento Diretos no Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Grupos de recursos do Azure](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Neste momento, não há suporte para a [extensão do agente IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md) para SQL Server FCI no Azure. Recomendamos que você desinstale a extensão de VMs que participam do FCI. Essa extensão oferece suporte a recursos, como backup automatizado e aplicação de patches e alguns recursos do portal para SQL. Esses recursos não funcionarão para VMs do SQL depois que o agente for desinstalado.

### <a name="what-to-have"></a>O que ter

Antes de seguir as instruções neste artigo, você já deve ter:

- Uma assinatura Microsoft Azure.
- Um domínio do Windows em máquinas virtuais do Azure.
- Uma conta com permissão para criar objetos na máquina virtual do Azure.
- Uma rede virtual do Azure e uma sub-rede com espaço de endereço IP suficiente para os seguintes componentes:
   - Ambas as máquinas virtuais.
   - O endereço IP do cluster de failover.
   - Um endereço IP para cada FCI.
- DNS configurado na rede do Azure, apontando para os controladores de domínio.

Com esses pré-requisitos em vigor, você pode prosseguir com a criação do cluster de failover. A primeira etapa é criar as máquinas virtuais.

## <a name="step-1-create-virtual-machines"></a>Passo 1: Criar máquinas virtuais

1. Faça logon no [portal do Azure](https://portal.azure.com) com sua assinatura.

1. [Crie um conjunto de disponibilidade do Azure](../tutorial-availability-sets.md).

   O conjunto de disponibilidade agrupa máquinas virtuais entre domínios de falha e domínios de atualização. O conjunto de disponibilidade garante que seu aplicativo não seja afetado por pontos únicos de falha, como o comutador de rede ou a unidade de alimentação de um rack de servidores.

   Se você não criou o grupo de recursos para suas máquinas virtuais, faça isso ao criar um conjunto de disponibilidade do Azure. Se você estiver usando o portal do Azure para criar o conjunto de disponibilidade, execute as seguintes etapas:

   - Na portal do Azure, clique **+** para abrir o Azure Marketplace. Procure o **conjunto de disponibilidade**.
   - Clique em **conjunto de disponibilidade**.
   - Clique em **Criar**.
   - Na folha **criar conjunto de disponibilidade** , defina os seguintes valores:
      - **Nome**: Um nome para o conjunto de disponibilidade.
      - **Subscrição**: A sua subscrição do Azure.
      - **Grupo de recursos**: Se você quiser usar um grupo existente, clique em **usar existente** e selecione o grupo na lista suspensa. Caso contrário, escolha **criar novo** e digite um nome para o grupo.
      - **Local**: Defina o local em que você planeja criar suas máquinas virtuais.
      - **Domínios de falha**: Use o padrão (3).
      - **Domínios de atualização**: Use o padrão (5).
   - Clique em **criar** para criar o conjunto de disponibilidade.

1. Crie as máquinas virtuais no conjunto de disponibilidade.

   Provisione duas máquinas virtuais SQL Server no conjunto de disponibilidade do Azure. Para obter instruções, consulte [provisionar uma máquina virtual SQL Server no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).

   Coloque ambas as máquinas virtuais:

   - No mesmo grupo de recursos do Azure em que seu conjunto de disponibilidade está.
   - Na mesma rede que o controlador de domínio.
   - Em uma sub-rede com espaço de endereço IP suficiente para ambas as máquinas virtuais e todos os FCIs que você pode usar por fim nesse cluster.
   - No conjunto de disponibilidade do Azure.   

      >[!IMPORTANT]
      >Você não pode definir ou alterar o conjunto de disponibilidade após a criação de uma máquina virtual.

   Escolha uma imagem do Azure Marketplace. Você pode usar uma imagem do Marketplace com o que inclui o Windows Server e SQL Server, ou apenas o Windows Server. Para obter detalhes, consulte [visão geral de SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md)

   As imagens do SQL Server oficial na galeria do Azure incluem uma instância SQL Server instalada, além do software de instalação do SQL Server e a chave necessária.

   Escolha a imagem correta de acordo com o modo como você deseja pagar pela licença de SQL Server:

   - **Pagar por licenciamento de uso**: O custo por segundo dessas imagens inclui o SQL Server licenciamento:
      - **SQL Server 2016 Enterprise no Windows Server Datacenter 2016**
      - **SQL Server 2016 Standard no Windows Server Datacenter 2016**
      - **SQL Server Developer 2016 no Windows Server Datacenter 2016**

   - **BYOL (traga sua própria licença)**

      - **BYOL SQL Server 2016 Enterprise no Windows Server Datacenter 2016**
      - **BYOL SQL Server 2016 Standard no Windows Server Datacenter 2016**

   >[!IMPORTANT]
   >Depois de criar a máquina virtual, remova a instância de SQL Server autônoma pré-instalada. Você usará a mídia de SQL Server pré-instalada para criar o SQL Server FCI depois de configurar o cluster de failover e o S2D.

   Como alternativa, você pode usar as imagens do Azure Marketplace apenas com o sistema operacional. Escolha uma imagem do **Windows Server 2016 datacenter** e instale o SQL Server FCI depois de configurar o cluster de failover e o S2D. Esta imagem não contém SQL Server mídia de instalação. Coloque a mídia de instalação em um local onde você possa executar a instalação do SQL Server para cada servidor.

1. Depois que o Azure cria suas máquinas virtuais, conecte-se a cada máquina virtual com RDP.

   Quando você se conecta pela primeira vez a uma máquina virtual com o RDP, o computador pergunta se você deseja permitir que este computador seja detectável na rede. Clique em **Sim**.

1. Se você estiver usando uma das imagens de máquina virtual com base em SQL Server, remova a instância de SQL Server.

   - Em **programas e recursos**, clique com o botão direito do mouse em **Microsoft SQL Server 2016 (64 bits)** e clique em **Desinstalar/alterar**.
   - Clique em **remover**.
   - Selecione a instância padrão.
   - Remova todos os recursos em **serviços mecanismo de banco de Dadoss**. Não remova os **recursos compartilhados**. Consulte a figura a seguir:

      ![Remover recursos](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Clique em **Avançar**e em **remover**.

1. <a name="ports"></a>Abra as portas do firewall.

   Em cada máquina virtual, abra as seguintes portas no firewall do Windows.

   | Objetivo | Porta TCP | Notas
   | ------ | ------ | ------
   | SQL Server | 1433 | Porta normal para instâncias padrão de SQL Server. Se você usou uma imagem da galeria, essa porta será aberta automaticamente.
   | Sonda de estado de funcionamento | 59999 | Qualquer porta TCP aberta. Em uma etapa posterior, configure a [investigação de integridade](#probe) do balanceador de carga e o cluster para usar essa porta.  

1. Adicione armazenamento à máquina virtual. Para obter informações detalhadas, consulte [Adicionar armazenamento](../disks-types.md).

   Ambas as máquinas virtuais precisam de pelo menos dois discos de dados.

   Anexar discos brutos-não discos formatados NTFS.
      >[!NOTE]
      >Se você anexar discos formatados para NTFS, só poderá habilitar S2D sem verificação de qualificação de disco.  

   Anexe um mínimo de dois SSDs Premium a cada VM. Recomendamos pelo menos discos p30 (1 TB).

   Defina o cache do host como **somente leitura**.

   A capacidade de armazenamento que você usa em ambientes de produção depende da sua carga de trabalho. Os valores descritos neste artigo são para demonstração e teste.

1. [Adicione as máquinas virtuais ao domínio já existente](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Depois que as máquinas virtuais são criadas e configuradas, você pode configurar o cluster de failover.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>Passo 2: Configurar o cluster de failover do Windows com o S2D

A próxima etapa é configurar o cluster de failover com o S2D. Nesta etapa, você fará as seguintes subetapas:

1. Adicionar recurso de clustering de failover do Windows
1. Validar o cluster
1. Criar o cluster de failover
1. Criar a testemunha de nuvem
1. Adicionar armazenamento

### <a name="add-windows-failover-clustering-feature"></a>Adicionar recurso de clustering de failover do Windows

1. Para começar, conecte-se à primeira máquina virtual com RDP usando uma conta de domínio que seja membro de administradores locais e tenha permissões para criar objetos no Active Directory. Use essa conta para o restante da configuração.

1. [Adicione o recurso de clustering de failover a cada máquina virtual](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Para instalar o recurso de clustering de failover da interface do usuário, execute as etapas a seguir em ambas as máquinas virtuais.
   - Em **Gerenciador do servidor**, clique em **gerenciar**e em **adicionar funções e recursos**.
   - No **Assistente para adicionar funções e recursos**, clique em **Avançar** até chegar a **selecionar recursos**.
   - Em **selecionar recursos**, clique em **clustering de failover**. Inclua todos os recursos necessários e as ferramentas de gerenciamento. Clique em **Adicionar recursos**.
   - Clique em **Avançar** e em **concluir** para instalar os recursos.

   Para instalar o recurso de clustering de failover com o PowerShell, execute o seguinte script de uma sessão de administrador do PowerShell em uma das máquinas virtuais.

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Para referência, as próximas etapas seguem as instruções na etapa 3 da [solução hiperconvergente usando espaços de armazenamento diretos no Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Validar o cluster

Este guia refere-se às instruções em [validar cluster](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Valide o cluster na interface do usuário ou com o PowerShell.

Para validar o cluster com a interface do usuário, execute as etapas a seguir em uma das máquinas virtuais.

1. Em **Gerenciador do servidor**, clique em **ferramentas**e, em seguida, clique em **Gerenciador de cluster de failover**.
1. Em **Gerenciador de cluster de failover**, clique em **ação**e em **validar configuração...** .
1. Clique em **Seguinte**.
1. Em **selecionar servidores ou um cluster**, digite o nome de ambas as máquinas virtuais.
1. Em **Opções de teste**, escolha **executar apenas testes que eu selecionar**. Clique em **Seguinte**.
1. Na **seleção de teste**, inclua todos os testes, exceto **armazenamento**. Consulte a figura a seguir:

   ![Validar testes](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Clique em **Seguinte**.
1. Em **confirmação**, clique em **Avançar**.

O **Assistente para validar uma configuração** executa os testes de validação.

Para validar o cluster com o PowerShell, execute o seguinte script de uma sessão de administrador do PowerShell em uma das máquinas virtuais.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Depois de validar o cluster, crie o cluster de failover.

### <a name="create-the-failover-cluster"></a>Criar o cluster de failover

Este guia refere-se à [criação do cluster de failover](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster).

Para criar o cluster de failover, você precisa de:
- Os nomes das máquinas virtuais que se tornam os nós de cluster.
- Um nome para o cluster de failover
- Um endereço IP para o cluster de failover. Você pode usar um endereço IP que não é usado na mesma rede virtual e sub-rede do Azure que os nós de cluster.

#### <a name="windows-server-2008-2016"></a>Windows Server 2008-2016

O PowerShell a seguir cria um cluster de failover para o **Windows Server 2008-2016**. Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível da VNET do Azure:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server de 2019

O PowerShell a seguir cria um cluster de failover para o Windows Server 2019.  Para obter mais informações, consulte o [cluster de failover do blog: Objeto](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)de rede do cluster.  Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível da VNET do Azure:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Criar uma testemunha de nuvem

A testemunha em nuvem é um novo tipo de testemunha de quorum de cluster armazenada em um Azure Storage Blob. Isso elimina a necessidade de uma VM separada que hospeda um compartilhamento de testemunha.

1. [Crie uma testemunha de nuvem para o cluster de failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Crie um contêiner de BLOBs.

1. Salve as chaves de acesso e a URL do contêiner.

1. Configure a testemunha de quorum do cluster de failover. Confira [Configurar a testemunha de quorum na interface do usuário](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) na interface.

### <a name="add-storage"></a>Adicionar armazenamento

Os discos para S2D precisam estar vazios e sem partições ou outros dados. Para limpar os discos, siga [as etapas neste guia](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks).

1. [Habilitar espaços de armazenamento \(\)diretos S2D](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   O PowerShell a seguir habilita espaços de armazenamento diretos.  

   ```powershell
   Enable-ClusterS2D
   ```

   No **Gerenciador de cluster de failover**, agora você pode ver o pool de armazenamento.

1. [Crie um volume](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Um dos recursos do S2D é que ele cria automaticamente um pool de armazenamento quando você o habilita. Agora você está pronto para criar um volume. O commandlet `New-Volume` do PowerShell automatiza o processo de criação de volume, incluindo formatação, adição ao cluster e criação de um CSV (volume compartilhado clusterizado). O exemplo a seguir cria um CSV de 800 gigabytes (GB).

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Após a conclusão desse comando, um volume de 800 GB é montado como um recurso de cluster. O volume está em `C:\ClusterStorage\Volume1\`.

   O diagrama a seguir mostra um volume compartilhado clusterizado com S2D:

   ![ClusterSharedVolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Passo 3: Failover de cluster de failover de teste

Em Gerenciador de Cluster de Failover, verifique se você pode mover o recurso de armazenamento para o outro nó de cluster. Se você puder se conectar ao cluster de failover com **Gerenciador de cluster de failover** e mover o armazenamento de um nó para outro, você estará pronto para configurar o FCI.

## <a name="step-4-create-sql-server-fci"></a>Passo 4: Criar SQL Server FCI

Depois de configurar o cluster de failover e todos os componentes do cluster, incluindo o armazenamento, você pode criar o SQL Server FCI.

1. Conecte-se à primeira máquina virtual com RDP.

1. Em **Gerenciador de cluster de failover**, verifique se todos os recursos de núcleo do cluster estão na primeira máquina virtual. Se necessário, mova todos os recursos para esta máquina virtual.

1. Localize a mídia de instalação. Se a máquina virtual usar uma das imagens do Azure Marketplace, a mídia estará localizada em `C:\SQLServer_<version number>_Full`. Clique em **instalação**.

1. Na **central de instalação do SQL Server**, clique em **instalação**.

1. Clique em **novo SQL Server instalação de cluster de failover**. Siga as instruções no Assistente para instalar o SQL Server FCI.

   Os diretórios de dados do FCI precisam estar no armazenamento clusterizado. Com o S2D, ele não é um disco compartilhado, mas um ponto de montagem para um volume em cada servidor. O S2D sincroniza o volume entre ambos os nós. O volume é apresentado ao cluster como um volume compartilhado clusterizado. Use o ponto de montagem de CSV para os diretórios de dados.

   ![DataDirectories](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Depois de concluir o assistente, a instalação instalará um SQL Server FCI no primeiro nó.

1. Após a instalação instalar com êxito o FCI no primeiro nó, conecte-se ao segundo nó com RDP.

1. Abra a **central de instalação do SQL Server**. Clique em **instalação**.

1. Clique em **adicionar nó a um cluster de failover SQL Server**. Siga as instruções no Assistente para instalar o SQL Server e adicionar esse servidor ao FCI.

   >[!NOTE]
   >Se você usou uma imagem da galeria do Azure Marketplace com SQL Server, SQL Server ferramentas foram incluídas com a imagem. Se você não usou essa imagem, instale as ferramentas de SQL Server separadamente. Consulte [baixar SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-azure-load-balancer"></a>Passo 5: Criar um balanceador de carga do Azure

Em máquinas virtuais do Azure, os clusters usam um balanceador de carga para manter um endereço IP que precisa estar em um nó de cluster por vez. Nessa solução, o balanceador de carga mantém o endereço IP para o SQL Server FCI.

[Criar e configurar um balanceador de carga do Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Criar o balanceador de carga no portal do Azure

Para criar o balanceador de carga:

1. Na portal do Azure, vá para o grupo de recursos com as máquinas virtuais.

1. Clique em **+ Adicionar**. Procure **Load Balancer**no Marketplace. Clique em **Load Balancer**.

1. Clique em **Criar**.

1. Configure o balanceador de carga com:

   - **Nome**: Um nome que identifica o balanceador de carga.
   - **Tipo**: O balanceador de carga pode ser público ou privado. Um balanceador de carga privado pode ser acessado de dentro da mesma VNET. A maioria dos aplicativos do Azure pode usar um balanceador de carga privado. Se seu aplicativo precisar de acesso a SQL Server diretamente pela Internet, use um balanceador de carga público.
   - **Rede virtual**: A mesma rede que as máquinas virtuais.
   - **Sub-rede**: A mesma sub-rede que as máquinas virtuais.
   - **Endereço IP privado**: O mesmo endereço IP que você atribuiu ao recurso de rede de cluster SQL Server FCI.
   - **assinatura**: A sua subscrição do Azure.
   - **Grupo de recursos**: Use o mesmo grupo de recursos que suas máquinas virtuais.
   - **Local**: Use o mesmo local do Azure que suas máquinas virtuais.
   Consulte a figura a seguir:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Configurar o pool de back-end do balanceador de carga

1. Retorne ao grupo de recursos do Azure com as máquinas virtuais e localize o novo balanceador de carga. Talvez seja necessário atualizar a exibição no grupo de recursos. Clique no balanceador de carga.

1. Clique em **pools de back-end** e em **+ Adicionar** para adicionar um pool de back-end.

1. Associe o pool de back-end ao conjunto de disponibilidade que contém as VMs.

1. Em **configurações de IP de rede de destino**, marque **máquina virtual** e escolha as máquinas virtuais que participarão como nós de cluster. Certifique-se de incluir todas as máquinas virtuais que hospedarão o FCI. 

1. Clique em **OK** para criar o pool de back-end.

### <a name="configure-a-load-balancer-health-probe"></a>Configurar uma investigação de integridade do balanceador de carga

1. Na folha balanceador de carga, clique em **investigações de integridade**.

1. Clique em **+ Adicionar**.

1. Na folha **Adicionar investigação de integridade** , <a name="probe"> </a>defina os parâmetros de investigação de integridade:

   - **Nome**: Um nome para a investigação de integridade.
   - **Protocolo**: PROTOCOL.
   - **Porta**: Defina para a porta que você criou no firewall para a investigação de integridade nesta [etapa](#ports). Neste artigo, o exemplo usa a porta `59999`TCP.
   - **Intervalo**: 5 segundos.
   - **Limite não íntegro**: 2 falhas consecutivas.

1. Clique em OK.

### <a name="set-load-balancing-rules"></a>Definir regras de balanceamento de carga

1. Na folha balanceador de carga, clique em **regras de balanceamento de carga**.

1. Clique em **+ Adicionar**.

1. Defina os parâmetros de regras de balanceamento de carga:

   - **Nome**: Um nome para as regras de balanceamento de carga.
   - **Endereço IP de front-end**: Use o endereço IP para o recurso de rede de cluster SQL Server FCI.
   - **Porta**: Defina para a porta TCP do SQL Server FCI. A porta de instância padrão é 1433.
   - **Porta de back-end**: Esse valor usa a mesma porta que o valor da **porta** quando você HABILITA o **IP flutuante (retorno de servidor direto)** .
   - **Pool de back-end**: Use o nome do pool de back-end que você configurou anteriormente.
   - **Investigação de integridade**: Use a investigação de integridade que você configurou anteriormente.
   - **Persistência da sessão**: Nenhum.
   - **Tempo limite de ociosidade (minutos)** : 4.
   - **IP flutuante (retorno de servidor direto)** : Enabled

1. Clique em **OK**.

## <a name="step-6-configure-cluster-for-probe"></a>Passo 6: Configurar o cluster para investigação

Defina o parâmetro de porta de investigação do cluster no PowerShell.

Para definir o parâmetro de porta de investigação do cluster, atualize as variáveis no script a seguir com os valores de seu ambiente. Remova os colchetes `<>` angulares do script. 

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

No script anterior, defina os valores para o seu ambiente. A lista a seguir descreve os valores:

   - `<Cluster Network Name>`: Nome do cluster de failover do Windows Server para a rede. Em**redes** **Gerenciador de cluster de failover** > , clique com o botão direito do mouse na rede e clique em **Propriedades**. O valor correto está sob **nome** na guia **geral** . 

   - `<SQL Server FCI IP Address Resource Name>`: SQL Server nome do recurso de endereço IP do FCI. Em **Gerenciador de cluster de failover** > **funções**, na função SQL Server FCI, em **nome do servidor**, clique com o botão direito do mouse no recurso de endereço IP e clique em **Propriedades**. O valor correto está sob **nome** na guia **geral** . 

   - `<ILBIP>`: O endereço IP ILB. Esse endereço é configurado no portal do Azure como o endereço de front-end ILB. Esse também é o endereço IP do SQL Server FCI. Você pode encontrá-lo em **Gerenciador de cluster de failover** na mesma página de propriedades em que você `<SQL Server FCI IP Address Resource Name>`localizou.  

   - `<nnnnn>`: É a porta de investigação que você configurou na investigação de integridade do balanceador de carga. Qualquer porta TCP não utilizada é válida. 

>[!IMPORTANT]
>A máscara de sub-rede do parâmetro de cluster deve ser o endereço de difusão IP `255.255.255.255`TCP:.

Depois de definir a investigação de cluster, você poderá ver todos os parâmetros de cluster no PowerShell. Execute o seguintes script:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Passo 7: Testar o failover do FCI

Teste o failover do FCI para validar a funcionalidade do cluster. Execute as seguintes etapas:

1. Conecte-se a um dos nós de cluster SQL Server FCI com RDP.

1. Abra **Gerenciador de cluster de failover**. Clique em **funções**. Observe qual nó possui a função SQL Server FCI.

1. Clique com o botão direito do mouse na função SQL Server FCI.

1. Clique em **mover** e clique em **melhor nó possível**.

**Gerenciador de cluster de failover** mostra a função e seus recursos ficam offline. Os recursos são movidos e ficam online no outro nó.

### <a name="test-connectivity"></a>Testar conectividade

Para testar a conectividade, faça logon em outra máquina virtual na mesma rede virtual. Abra **SQL Server Management Studio** e conecte-se ao nome do SQL Server FCI.

>[!NOTE]
>Se necessário, você pode [baixar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Limitações

As máquinas virtuais do Azure dão suporte ao Microsoft Coordenador de Transações Distribuídas (MSDTC) no Windows Server 2019 com armazenamento em CSV (volumes compartilhados clusterizados) e um [balanceador de carga padrão](../../../load-balancer/load-balancer-standard-overview.md).

Em máquinas virtuais do Azure, o MSDTC não tem suporte no Windows Server 2016 e versões anteriores porque:

- O recurso MSDTC clusterizado não pode ser configurado para usar o armazenamento compartilhado. Com o Windows Server 2016 se você criar um recurso do MSDTC, ele não mostrará nenhum armazenamento compartilhado disponível para uso, mesmo que o armazenamento esteja lá. Esse problema foi corrigido no Windows Server 2019.
- O balanceador de carga básico não trata as portas RPC.

## <a name="see-also"></a>Consultar Também

[Configurar o S2D com a área de trabalho remota (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Solução hiperconvergente com espaços de armazenamento diretos](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct).

[Visão geral do espaço de armazenamento direto](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Suporte de SQL Server para S2D](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
