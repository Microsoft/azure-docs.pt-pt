---
title: SQL Server FCI-máquinas virtuais do Azure | Microsoft Docs
description: Este artigo explica como criar uma instância de cluster de failover SQL Server em máquinas virtuais do Azure.
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
ms.openlocfilehash: 1a69741ba3ced91b6b0d1fc4bcd4aea887452151
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792174"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Configurar uma instância de cluster de failover SQL Server em máquinas virtuais do Azure

Este artigo explica como criar uma FCI (instância de cluster de failover) SQL Server em máquinas virtuais do Azure no modelo de Azure Resource Manager. Essa solução usa o [Windows Server 2016 datacenter edition espaços de armazenamento diretos](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) como uma SAN virtual baseada em software que sincroniza o armazenamento (discos de dados) entre os nós (VMS do Azure) em um cluster do Windows. Espaços de Armazenamento Diretos era novo no Windows Server 2016.

O diagrama a seguir mostra a solução completa em máquinas virtuais do Azure:

![A solução completa](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Este diagrama mostra:

- Duas máquinas virtuais do Azure em um cluster de failover do Windows Server. Quando uma máquina virtual está em um cluster de failover, ela também é chamada de nó de *cluster* ou *nó*.
- Cada máquina virtual tem dois ou mais discos de dados.
- Espaços de Armazenamento Diretos sincroniza os dados nos discos de dados e apresenta o armazenamento sincronizado como um pool de armazenamento.
- O pool de armazenamento apresenta um Volume Compartilhado Clusterizado (CSV) para o cluster de failover.
- A função de cluster SQL Server FCI usa o CSV para as unidades de dados.
- Um balanceador de carga do Azure para manter o endereço IP para o SQL Server FCI.
- Um conjunto de disponibilidade do Azure mantém todos os recursos.

>[!NOTE]
>Todos os recursos do Azure no diagrama estão no mesmo grupo de recursos.

Para obter detalhes sobre Espaços de Armazenamento Diretos, consulte [Windows Server 2016 datacenter edition espaços de armazenamento diretos](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

O Espaços de Armazenamento Diretos dá suporte a dois tipos de arquiteturas: convergido e hiperconvergente. A arquitetura deste documento é hiperconvergente. Uma infraestrutura hiperconvergente coloca o armazenamento nos mesmos servidores que hospedam o aplicativo clusterizado. Nessa arquitetura, o armazenamento está em cada nó de SQL Server FCI.

## <a name="licensing-and-pricing"></a>Licenciamento e preços

Em máquinas virtuais do Azure, você pode licenciar SQL Server usando imagens de VM PAYG (pré-pago) ou BYOL (traga sua própria licença). O tipo de imagem que você escolhe afeta como você é cobrado.

Com o licenciamento pago conforme o uso, uma FCI (instância de cluster de failover) de SQL Server em máquinas virtuais do Azure incorre em encargos para todos os nós do FCI, incluindo os nós passivos. Para obter mais informações, consulte [SQL Server Enterprise preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Se você tiver Enterprise Agreement com o Software Assurance, poderá usar um nó FCI passivo gratuito para cada nó ativo. Para aproveitar esse benefício no Azure, use imagens de VM BYOL e use a mesma licença nos nós ativo e passivo do FCI. Para obter mais informações, consulte [Enterprise Agreement](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Para comparar o licenciamento pago conforme o uso e o BYOL para SQL Server em máquinas virtuais do Azure, consulte Introdução [às VMs do SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Para obter informações completas sobre SQL Server de licenciamento, consulte [preços](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Modelo do Azure de exemplo

Você pode criar essa solução inteira no Azure a partir de um modelo. Um exemplo de um modelo está disponível nos modelos de [início rápido do Azure do](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad)github. Este exemplo não é projetado ou testado para qualquer carga de trabalho específica. Você pode executar o modelo para criar um SQL Server FCI com Espaços de Armazenamento Diretos armazenamento conectado ao seu domínio. Você pode avaliar o modelo e modificá-lo para suas finalidades.

## <a name="before-you-begin"></a>Antes de começar

Há algumas coisas que você precisa saber e ter em vigor antes de começar.

### <a name="what-to-know"></a>O que saber
Você deve ter uma compreensão operacional dessas tecnologias:

- [Tecnologias de cluster do Windows](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server instâncias de cluster de failover](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Uma coisa a ser observada é que, em um cluster de failover convidado da VM IaaS do Azure, recomendamos uma única NIC por servidor (nó de cluster) e uma única sub-rede. A rede do Azure tem redundância física, o que torna as NICs e sub-redes adicionais desnecessárias em um cluster convidado da VM IaaS do Azure. O relatório de validação de cluster avisará que os nós podem ser acessados apenas em uma única rede. Você pode ignorar esse aviso em clusters de failover do Azure IaaS VM guest.

Você também deve ter uma compreensão geral dessas tecnologias:

- [Soluções hiperconvergentes que usam Espaços de Armazenamento Diretos no Windows Server 2016](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Grupos de recursos do Azure](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Neste momento, SQL Server instâncias de cluster de failover em máquinas virtuais do Azure só têm suporte com o [modo de gerenciamento leve](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) da [extensão do agente IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Para alterar do modo de extensão completo para leve, exclua o recurso de **máquina virtual do SQL** para as VMs correspondentes e registre-as com o provedor de recursos de VM do SQL no modo leve. Ao excluir o recurso de **máquina virtual do SQL** usando o portal do Azure, **desmarque a caixa de seleção ao lado da máquina virtual correta**. A extensão completa oferece suporte a recursos como backup automatizado, aplicação de patch e gerenciamento avançado do Portal. Esses recursos não funcionarão para VMs do SQL depois que o agente for reinstalado no modo de gerenciamento leve.

### <a name="what-to-have"></a>O que ter

Antes de concluir as etapas neste artigo, você já deve ter:

- Uma assinatura Microsoft Azure.
- Um domínio do Windows em máquinas virtuais do Azure.
- Uma conta que tem permissões para criar objetos em máquinas virtuais do Azure e em Active Directory.
- Uma rede virtual do Azure e uma sub-rede com espaço de endereço IP suficiente para esses componentes:
   - Ambas as máquinas virtuais.
   - O endereço IP do cluster de failover.
   - Um endereço IP para cada FCI.
- DNS configurado na rede do Azure, apontando para os controladores de domínio.

Com esses pré-requisitos em vigor, você pode começar a criar seu cluster de failover. A primeira etapa é criar as máquinas virtuais.

## <a name="step-1-create-the-virtual-machines"></a>Etapa 1: criar as máquinas virtuais

1. Entre no [portal do Azure](https://portal.azure.com) com sua assinatura.

1. [Crie um conjunto de disponibilidade do Azure](../tutorial-availability-sets.md).

   O conjunto de disponibilidade agrupa máquinas virtuais entre domínios de falha e domínios de atualização. Ele garante que seu aplicativo não seja afetado por pontos únicos de falha, como o comutador de rede ou a unidade de alimentação de um rack de servidores.

   Se você não criou o grupo de recursos para suas máquinas virtuais, faça isso ao criar um conjunto de disponibilidade do Azure. Se você estiver usando o portal do Azure para criar o conjunto de disponibilidade, siga estas etapas:

   1. Na portal do Azure, selecione **criar um recurso** para abrir o Azure Marketplace. Procure o **conjunto de disponibilidade**.
   1. Selecione **conjunto de disponibilidade**.
   1. Selecione **Criar**.
   1. Em **criar conjunto de disponibilidade**, forneça estes valores:
      - **Nome**: um nome para o conjunto de disponibilidade.
      - **Assinatura**: sua assinatura do Azure.
      - **Grupo de recursos**: se você quiser usar um grupo existente, clique em **selecionar existente** e selecione o grupo na lista. Caso contrário, selecione **criar novo** e insira um nome para o grupo.
      - **Local**: defina o local em que você planeja criar suas máquinas virtuais.
      - **Domínios de falha**: Use o padrão (**3**).
      - **Domínios de atualização**: Use o padrão (**5**).
   1. Selecione **criar** para criar o conjunto de disponibilidade.

1. Crie as máquinas virtuais no conjunto de disponibilidade.

   Provisione duas máquinas virtuais SQL Server no conjunto de disponibilidade do Azure. Para obter instruções, consulte [provisionar uma máquina virtual SQL Server no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).

   Coloque ambas as máquinas virtuais:

   - No mesmo grupo de recursos do Azure que seu conjunto de disponibilidade.
   - Na mesma rede que o controlador de domínio.
   - Em uma sub-rede que tem espaço de endereço IP suficiente para ambas as máquinas virtuais e todas as FCIs que você pode usar eventualmente no cluster.
   - No conjunto de disponibilidade do Azure.

      >[!IMPORTANT]
      >Você não pode definir ou alterar o conjunto de disponibilidade depois de ter criado uma máquina virtual.

   Escolha uma imagem do Azure Marketplace. Você pode usar uma imagem do Azure Marketplace que inclui o Windows Server e SQL Server ou usar uma que inclui apenas o Windows Server. Para obter detalhes, consulte [visão geral de SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

   As imagens do SQL Server oficial na galeria do Azure incluem uma instância SQL Server instalada, o software de instalação do SQL Server e a chave necessária.

   Escolha a imagem correta, com base em como você deseja pagar pela licença de SQL Server:

   - **Licenciamento de pagamento por uso**. O custo por segundo dessas imagens inclui o SQL Server licenciamento:
      - **SQL Server 2016 Enterprise no Windows Server 2016 datacenter**
      - **SQL Server 2016 Standard no Windows Server 2016 datacenter**
      - **SQL Server desenvolvedor 2016 no Windows Server 2016 datacenter**

   - **BYOL (traga sua própria licença)**

      - **BYOL SQL Server 2016 Enterprise no Windows Server 2016 datacenter**
      - **BYOL SQL Server 2016 Standard no Windows Server 2016 datacenter**

   >[!IMPORTANT]
   >Depois de criar a máquina virtual, remova a instância de SQL Server autônoma pré-instalada. Você usará a mídia de SQL Server pré-instalada para criar o SQL Server FCI depois de configurar o cluster de failover e Espaços de Armazenamento Diretos.

   Como alternativa, você pode usar as imagens do Azure Marketplace que contêm apenas o sistema operacional. Escolha uma imagem do **Windows Server 2016 datacenter** e instale o SQL Server FCI depois de configurar o cluster de failover e espaços de armazenamento diretos. Esta imagem não contém SQL Server mídia de instalação. Coloque a mídia de instalação do SQL Server em um local onde você possa executá-lo para cada servidor.

1. Depois que o Azure criar suas máquinas virtuais, conecte-se a cada uma usando o RDP.

   Quando você se conecta pela primeira vez a uma máquina virtual usando o RDP, um prompt pergunta se você deseja permitir que o computador seja detectável na rede. Selecione **Sim**.

1. Se você estiver usando uma das imagens de máquina virtual com base em SQL Server, remova a instância de SQL Server.

   1. Em **programas e recursos**, clique com o botão direito do mouse em **Microsoft SQL Server 2016 (64 bits)** e selecione **Desinstalar/alterar**.
   1. Selecione **Remover**.
   1. Selecione a instância padrão.
   1. Remova todos os recursos em **serviços mecanismo de banco de Dadoss**. Não remova os **recursos compartilhados**. Você verá algo semelhante à captura de tela a seguir:

      ![Selecionar recursos](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Selecione **Avançar**e, em seguida, selecione **remover**.

1. <a name="ports"></a>Abra as portas do firewall.

   Em cada máquina virtual, abra essas portas no firewall do Windows:

   | Finalidade | Porta TCP | Notas
   | ------ | ------ | ------
   | SQL Server | 1433 | Porta normal para instâncias padrão de SQL Server. Se você usou uma imagem da galeria, essa porta será aberta automaticamente.
   | Sonda de estado de funcionamento | 59999 | Qualquer porta TCP aberta. Em uma etapa posterior, configure a [investigação de integridade](#probe) do balanceador de carga e o cluster para usar essa porta.  

1. Adicione armazenamento à máquina virtual. Para obter informações detalhadas, consulte [Adicionar armazenamento](../disks-types.md).

   Ambas as máquinas virtuais precisam de pelo menos dois discos de dados.

   Anexe discos brutos, não discos formatados para NTFS.
      >[!NOTE]
      >Se você anexar discos formatados com NTFS, poderá habilitar Espaços de Armazenamento Diretos apenas sem uma verificação de qualificação do disco.  

   Anexe um mínimo de dois SSDs Premium a cada VM. Recomendamos pelo menos discos de p30 (1 TB).

   Defina o cache do host como **somente leitura**.

   A capacidade de armazenamento que você usa em ambientes de produção depende da sua carga de trabalho. Os valores descritos neste artigo são para demonstração e teste.

1. [Adicione as máquinas virtuais ao domínio já existente](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Depois de criar e configurar as máquinas virtuais, você pode configurar o cluster de failover.

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>Etapa 2: configurar o cluster de failover do Windows Server com o Espaços de Armazenamento Diretos

A próxima etapa é configurar o cluster de failover com o Espaços de Armazenamento Diretos. Nesta etapa, você concluirá estas subetapas:

1. Adicione o recurso Windows Server failover clustering.
1. Valide o cluster.
1. Crie o cluster de failover.
1. Crie a testemunha de nuvem.
1. Adicionar armazenamento.

### <a name="add-windows-server-failover-clustering"></a>Adicionar clustering de failover do Windows Server

1. Conecte-se à primeira máquina virtual com o RDP usando uma conta de domínio que seja membro dos administradores locais e que tenha permissão para criar objetos no Active Directory. Use essa conta para o restante da configuração.

1. [Adicionar clustering de failover a cada máquina virtual](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Para instalar o clustering de failover da interface do usuário, execute estas etapas em ambas as máquinas virtuais:
   1. Em **Gerenciador do servidor**, selecione **gerenciar**e, em seguida, selecione **adicionar funções e recursos**.
   1. No **Assistente Adicionar funções e recursos**, selecione **Avançar** até chegar a **selecionar recursos**.
   1. Em **selecionar recursos**, selecione **clustering de failover**. Inclua todos os recursos necessários e as ferramentas de gerenciamento. Selecione **Adicionar recursos**.
   1. Selecione **Avançar**e, em seguida, selecione **concluir** para instalar os recursos.

   Para instalar o clustering de failover usando o PowerShell, execute o seguinte script de uma sessão de administrador do PowerShell em uma das máquinas virtuais:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Para obter mais referências sobre as próximas etapas, consulte as instruções na etapa 3 da [solução hiperconvergente usando espaços de armazenamento diretos no Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Validar o cluster

Valide o cluster na interface do usuário ou usando o PowerShell.

Para validar o cluster usando a interface do usuário, execute as seguintes etapas em uma das máquinas virtuais:

1. Em **Gerenciador do servidor**, selecione **ferramentas**e, em seguida, selecione **Gerenciador de cluster de failover**.
1. Em **Gerenciador de cluster de failover**, selecione **ação**e, em seguida, selecione **validar configuração**.
1. Selecione **Seguinte**.
1. Em **selecionar servidores ou um cluster**, insira os nomes de ambas as máquinas virtuais.
1. Em **Opções de teste**, selecione **executar apenas testes que eu selecionar**. Selecione **Seguinte**.
1. Em **seleção de teste**, selecione todos os testes, exceto o **armazenamento**, conforme mostrado aqui:

   ![Selecionar testes de validação de cluster](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Selecione **Seguinte**.
1. Em **confirmação**, selecione **Avançar**.

O assistente para validar uma configuração executa os testes de validação.

Para validar o cluster usando o PowerShell, execute o seguinte script de uma sessão de administrador do PowerShell em uma das máquinas virtuais:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Depois de validar o cluster, crie o cluster de failover.

### <a name="create-the-failover-cluster"></a>Criar o cluster de failover

Para criar o cluster de failover, você precisa de:
- Os nomes das máquinas virtuais que se tornarão os nós de cluster.
- Um nome para o cluster de failover
- Um endereço IP para o cluster de failover. Você pode usar um endereço IP que não é usado na mesma rede virtual e sub-rede do Azure que os nós de cluster.

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows Server 2008 por meio do Windows Server 2016

O script do PowerShell a seguir cria um cluster de failover para o Windows Server 2008 por meio do Windows Server 2016. Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível da rede virtual do Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

O script do PowerShell a seguir cria um cluster de failover para o Windows Server 2019. Para obter mais informações, consulte [cluster de failover: objeto de rede de cluster](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível da rede virtual do Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Criar uma testemunha de nuvem

A testemunha de nuvem é um novo tipo de testemunha de quorum de cluster que é armazenado em um blob de armazenamento do Azure. Isso elimina a necessidade de uma VM separada que hospede um compartilhamento de testemunha.

1. [Crie uma testemunha de nuvem para o cluster de failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Crie um contêiner de BLOBs.

1. Salve as chaves de acesso e a URL do contêiner.

1. Configure a testemunha de quorum do cluster de failover. Consulte [Configurar a testemunha de quorum na interface do usuário](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

### <a name="add-storage"></a>Adicionar armazenamento

Os discos para Espaços de Armazenamento Diretos precisam estar vazios. Eles não podem conter partições ou outros dados. Para limpar os discos, siga [as etapas neste guia](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives).

1. [Habilite espaços de armazenamento diretos](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   O seguinte script do PowerShell habilita Espaços de Armazenamento Diretos:  

   ```powershell
   Enable-ClusterS2D
   ```

   No **Gerenciador de cluster de failover**, agora você pode ver o pool de armazenamento.

1. [Crie um volume](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Espaços de Armazenamento Diretos cria automaticamente um pool de armazenamento quando você o habilita. Agora você está pronto para criar um volume. O cmdlet do PowerShell `New-Volume` automatiza o processo de criação de volume. Esse processo inclui a formatação, a adição do volume ao cluster e a criação de um Volume Compartilhado Clusterizado (CSV). Este exemplo cria um CSV de 800 GB:

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Depois que esse comando for concluído, um volume de 800 GB será montado como um recurso de cluster. O volume está em `C:\ClusterStorage\Volume1\`.

   Esta captura de tela mostra uma Volume Compartilhado Clusterizado com Espaços de Armazenamento Diretos:

   ![Volume Compartilhado Clusterizado](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Etapa 3: failover de cluster de failover de teste

Em **Gerenciador de cluster de failover**, verifique se você pode mover o recurso de armazenamento para o outro nó de cluster. Se você puder se conectar ao cluster de failover usando **Gerenciador de cluster de failover** e mover o armazenamento de um nó para outro, você estará pronto para configurar o FCI.

## <a name="step-4-create-the-sql-server-fci"></a>Etapa 4: criar o SQL Server FCI

Depois de configurar o cluster de failover e todos os componentes do cluster, incluindo o armazenamento, você pode criar o SQL Server FCI.

1. Conecte-se à primeira máquina virtual usando o RDP.

1. Em **Gerenciador de cluster de failover**, verifique se todos os recursos de cluster principais estão na primeira máquina virtual. Se necessário, mova todos os recursos para essa máquina virtual.

1. Localize a mídia de instalação. Se a máquina virtual usar uma das imagens do Azure Marketplace, a mídia estará localizada em `C:\SQLServer_<version number>_Full`. Selecione **instalação**.

1. Na **central de instalação SQL Server**, selecione **instalação**.

1. Selecione **novo SQL Server instalação de cluster de failover**. Siga as instruções no Assistente para instalar o SQL Server FCI.

   Os diretórios de dados do FCI precisam estar no armazenamento clusterizado. Com o Espaços de Armazenamento Diretos, ele não é um disco compartilhado, mas um ponto de montagem para um volume em cada servidor. Espaços de Armazenamento Diretos sincroniza o volume entre ambos os nós. O volume é apresentado ao cluster como um Volume Compartilhado Clusterizado. Use o ponto de montagem de CSV para os diretórios de dados.

   ![Diretórios de dados](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Depois de concluir as instruções no assistente, a instalação instalará um SQL Server FCI no primeiro nó.

1. Após a instalação instalar o FCI no primeiro nó, conecte-se ao segundo nó usando o RDP.

1. Abra a **central de instalação do SQL Server**. Selecione **instalação**.

1. Selecione **adicionar nó a um cluster de failover SQL Server**. Siga as instruções no Assistente para instalar SQL Server e adicionar o servidor ao FCI.

   >[!NOTE]
   >Se você usou uma imagem da galeria do Azure Marketplace que contém SQL Server, SQL Server ferramentas foram incluídas com a imagem. Se você não usou uma dessas imagens, instale as ferramentas de SQL Server separadamente. Consulte [baixar SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-the-azure-load-balancer"></a>Etapa 5: criar o Azure Load Balancer

Em máquinas virtuais do Azure, os clusters usam um balanceador de carga para manter um endereço IP que precisa estar em um nó de cluster por vez. Nessa solução, o balanceador de carga mantém o endereço IP para o SQL Server FCI.

Para obter mais informações, consulte [criar e configurar um balanceador de carga do Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Criar o balanceador de carga no portal do Azure

Para criar o balanceador de carga:

1. Na portal do Azure, vá para o grupo de recursos que contém as máquinas virtuais.

1. Selecione **Adicionar**. Pesquise **Load Balancer**no Azure Marketplace. Selecione **Load Balancer**.

1. Selecione **Criar**.

1. Configure o balanceador de carga com:

   - **Assinatura**: sua assinatura do Azure.
   - **Grupo de recursos**: o grupo de recursos que contém suas máquinas virtuais.
   - **Nome**: um nome que identifica o balanceador de carga.
   - **Região**: o local do Azure que contém suas máquinas virtuais.
   - **Tipo**: público ou privado. Um balanceador de carga privado pode ser acessado de dentro da rede virtual. A maioria dos aplicativos do Azure pode usar um balanceador de carga privado. Se seu aplicativo precisar de acesso a SQL Server diretamente pela Internet, use um balanceador de carga público.
   - **SKU**: Standard.
   - **Rede virtual**: a mesma rede que as máquinas virtuais.
   - **Atribuição de endereço IP**: estática. 
   - **Endereço IP privado**: o endereço IP que você atribuiu ao recurso de rede de cluster SQL Server FCI.

 A captura de tela a seguir mostra a interface do usuário **criar balanceador de carga** :

   ![Configurar o balanceador de carga](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Configurar o pool de back-end do balanceador de carga

1. Retorne ao grupo de recursos do Azure que contém as máquinas virtuais e localize o novo balanceador de carga. Talvez seja necessário atualizar a exibição no grupo de recursos. Selecione o balanceador de carga.

1. Selecione **pools de back-end**e, em seguida, selecione **Adicionar**.

1. Associe o pool de back-end ao conjunto de disponibilidade que contém as VMs.

1. Em **configurações de IP de rede de destino**, selecione **máquina virtual** e escolha as máquinas virtuais que participarão como nós de cluster. Certifique-se de incluir todas as máquinas virtuais que hospedarão o FCI.

1. Selecione **OK** para criar o pool de back-end.

### <a name="configure-a-load-balancer-health-probe"></a>Configurar uma investigação de integridade do balanceador de carga

1. Na folha balanceador de carga, selecione **investigações de integridade**.

1. Selecione **Adicionar**.

1. Na folha **Adicionar investigação de integridade** , <a name="probe"> </a>defina os parâmetros de investigação de integridade.

   - **Nome**: um nome para a investigação de integridade.
   - **Protocolo**: TCP.
   - **Porta**: defina para a porta que você criou no firewall para a investigação de integridade nesta [etapa](#ports). Neste artigo, o exemplo usa a porta TCP `59999`.
   - **Intervalo**: 5 segundos.
   - **Limite não íntegro**: 2 falhas consecutivas.

1. Selecione **OK**.

### <a name="set-load-balancing-rules"></a>Definir regras de balanceamento de carga

1. Na folha balanceador de carga, selecione **regras de balanceamento de carga**.

1. Selecione **Adicionar**.

1. Defina os parâmetros da regra de balanceamento de carga:

   - **Nome**: um nome para as regras de balanceamento de carga.
   - **Endereço IP de front-end**: o endereço IP para o recurso de rede de cluster SQL Server FCI.
   - **Porta**: a porta TCP do SQL Server FCI. A porta de instância padrão é 1433.
   - **Porta de back-end**: usa a mesma porta que o valor da **porta** quando você habilita o **IP flutuante (retorno de servidor direto)** .
   - **Pool de back-end**: o nome do pool de back-end que você configurou anteriormente.
   - **Investigação de integridade**: a investigação de integridade que você configurou anteriormente.
   - **Persistência da sessão**: nenhuma.
   - **Tempo limite de ociosidade (minutos)** : 4.
   - **IP flutuante (retorno de servidor direto)** : habilitado.

1. Selecione **OK**.

## <a name="step-6-configure-the-cluster-for-the-probe"></a>Etapa 6: configurar o cluster para a investigação

Defina o parâmetro de porta de investigação do cluster no PowerShell.

Para definir o parâmetro de porta de investigação de cluster, atualize as variáveis no script a seguir com valores de seu ambiente. Remova os colchetes angulares (`<` e `>`) do script.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

A lista a seguir descreve os valores que você precisa atualizar:

   - `<Cluster Network Name>`: o nome do cluster de failover do Windows Server para a rede. Em **Gerenciador de Cluster de Failover** > **redes**, clique com o botão direito do mouse na rede e selecione **Propriedades**. O valor correto está sob **nome** na guia **geral** .

   - `<SQL Server FCI IP Address Resource Name>`: o nome do recurso de endereço IP do SQL Server FCI. Em **Gerenciador de Cluster de Failover** > **funções**, na função SQL Server FCI, em **nome do servidor**, clique com o botão direito do mouse no recurso de endereço IP e selecione **Propriedades**. O valor correto está sob **nome** na guia **geral** . 

   - `<ILBIP>`: o endereço IP ILB. Esse endereço é configurado no portal do Azure como o endereço de front-end ILB. Esse também é o endereço IP do SQL Server FCI. Você pode encontrá-lo em **Gerenciador de cluster de failover** na mesma página de propriedades em que você localizou o `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: a porta de investigação configurada na investigação de integridade do balanceador de carga. Qualquer porta TCP não utilizada é válida.

>[!IMPORTANT]
>A máscara de sub-rede do parâmetro de cluster deve ser o endereço de difusão IP TCP: `255.255.255.255`.

Depois de definir a investigação de cluster, você poderá ver todos os parâmetros de cluster no PowerShell. Executar este script:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Etapa 7: testar o failover do FCI

Teste o failover do FCI para validar a funcionalidade do cluster. Execute as seguintes etapas:

1. Conecte-se a um dos nós de cluster SQL Server FCI usando o RDP.

1. Abra **Gerenciador de cluster de failover**. Selecione **funções**. Observe qual nó possui a função SQL Server FCI.

1. Clique com o botão direito do mouse na função SQL Server FCI.

1. Selecione **mover**e, em seguida, selecione o **melhor nó possível**.

**Gerenciador de cluster de failover** mostra a função e seus recursos ficam offline. Os recursos são movidos e ficam online no outro nó.

### <a name="test-connectivity"></a>Testar conectividade

Para testar a conectividade, entre em outra máquina virtual na mesma rede virtual. Abra **SQL Server Management Studio** e conecte-se ao nome do SQL Server FCI.

>[!NOTE]
>Se precisar, você pode [baixar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Limitações

As máquinas virtuais do Azure dão suporte ao Microsoft Coordenador de Transações Distribuídas (MSDTC) no Windows Server 2019 com armazenamento em CSV (volumes compartilhados clusterizados) e um [balanceador de carga padrão](../../../load-balancer/load-balancer-standard-overview.md).

Em máquinas virtuais do Azure, o MSDTC não tem suporte no Windows Server 2016 ou anterior porque:

- O recurso MSDTC clusterizado não pode ser configurado para usar o armazenamento compartilhado. No Windows Server 2016, se você criar um recurso MSDTC, ele não mostrará nenhum armazenamento compartilhado disponível para uso, mesmo que o armazenamento esteja disponível. Esse problema foi corrigido no Windows Server 2019.
- O balanceador de carga básico não lida com portas RPC.

## <a name="see-also"></a>Ver também

[Configurar Espaços de Armazenamento Diretos com a área de trabalho remota (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Solução hiperconvergente com Espaços de Armazenamento Diretos](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[Visão geral de Espaços de Armazenamento Diretos](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Suporte de SQL Server para Espaços de Armazenamento Diretos](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
