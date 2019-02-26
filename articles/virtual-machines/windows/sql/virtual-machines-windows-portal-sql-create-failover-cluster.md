---
title: SQL Server FCI – máquinas virtuais do Azure | Documentos da Microsoft
description: Este artigo explica como criar instância de Cluster de ativação pós-falha do SQL Server em máquinas de virtuais do Azure.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: 778984d4d34d3b63d92378781d377fee557c7148
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821922"
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Configurar a instância de Cluster de ativação pós-falha do SQL Server em máquinas virtuais do Azure

Este artigo explica como criar uma ativação pós-falha de Cluster de instância (FCI) do SQL Server em máquinas virtuais do Azure no modelo do Resource Manager. Esta solução utiliza [espaços de armazenamento direto do Windows Server 2016 Datacenter edition \(S2D\) ](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) como uma SAN de virtual baseado em software que sincroniza o armazenamento (discos de dados) entre os nós (VMs do Azure) num Cluster do Windows. O S2D é novo no Windows Server 2016.

O diagrama seguinte mostra a solução completa em máquinas virtuais do Azure:

![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

O diagrama anterior mostra:

- Duas máquinas virtuais num Cluster de ativação pós-falha do Windows. Quando uma máquina virtual estiver num cluster de ativação pós-falha também é denominado um *nó de cluster*, ou *nós*.
- Cada máquina virtual tem duas ou mais discos de dados.
- O S2D sincroniza os dados no disco de dados e apresenta o armazenamento sincronizadas como um agrupamento de armazenamento.
- O agrupamento de armazenamento apresenta um volume partilhado de cluster (CSV) para o cluster de ativação pós-falha.
- A função de cluster do SQL Server FCI utiliza o CSV para as unidades de dados.
- Um balanceador de carga do Azure para manter o endereço IP para a FCI do SQL Server.
- Um conjunto de disponibilidade do Azure mantém todos os recursos.

   >[!NOTE]
   >Todos os recursos do Azure estão no diagrama estão no mesmo grupo de recursos.

Para obter detalhes sobre o S2D, consulte [espaços de armazenamento direto do Windows Server 2016 Datacenter edition \(S2D\)](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

O S2D suporta dois tipos de arquiteturas - convergidas e hiperconvergidos. A arquitetura neste documento é hiperconvergido. Uma infraestrutura hiperconvergida coloca o armazenamento nos mesmos servidores que alojam a aplicação em cluster. Nesta arquitetura, o armazenamento está em cada nó do SQL Server FCI.

## <a name="licensing-and-pricing"></a>Licenciamento e preços

Em máquinas de virtuais do Azure pode licenciar o SQL Server usando a opção pay as you go (PAYG) ou utilizar a sua própria licença imagens de VM (BYOL). O tipo de imagem que escolher afeta a forma como são cobradas.

Com o licenciamento PAYG, uma instância de cluster de ativação pós-falha (FCI) do SQL Server em máquinas de virtuais do Azure leva a custos de todos os nós da FCI, incluindo os nós passivos. Para obter mais informações, consulte [preços de máquinas virtuais do SQL Server Enterprise](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

Os clientes com contrato Enterprise com Software Assurance tem o direito de utilizar um nó FCI passivo livre para cada nó ativo. Para tirar partido deste benefício no Azure, utilizar imagens de BYOL VM e, em seguida, utilize a mesma licença em ambos os nós ativos e passivos da FCI. Para obter mais informações, consulte [contrato Enterprise](https://www.microsoft.com/en-us/Licensing/licensing-programs/enterprise.aspx).

Licenciamento do SQL Server em Azure Virtual Machines Consulte para comparar PAYG e BYOL [introdução às SQL VMs](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Para obter informações completas sobre o licenciamento do SQL Server, consulte [preços](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Exemplo modelo do Azure

Pode criar a solução completa no Azure a partir de um modelo. Um exemplo de um modelo está disponível no GitHub [modelos de início rápido do Azure](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). Neste exemplo não é criado ou testado para qualquer carga de trabalho específica. Pode executar o modelo para criar um SQL Server FCI com o armazenamento S2D ligado ao seu domínio. Pode avaliar o modelo e modificá-lo para os fins pretendidos.

## <a name="before-you-begin"></a>Antes de começar

Existem algumas coisas que precisa saber e algumas coisas que precisa em vigor antes de continuar.

### <a name="what-to-know"></a>O que deve saber
Deve ter uma compreensão operacional das seguintes tecnologias:

- [Tecnologias de cluster do Windows](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [Instâncias de Cluster de ativação pós-falha do SQL Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).

Uma diferença importante é que num cluster de ativação pós-falha de convidado de VM de IaaS do Azure, recomendamos que um único NIC por servidor (nó de cluster) e uma única sub-rede. Redes do Azure tem redundância física que torna desnecessário NICs e sub-redes adicionais num cluster de convidados de VM de IaaS do Azure. Embora o relatório de validação de cluster irá emitir um aviso de que os nós só são acessíveis numa única rede, este aviso pode ser ignorado com segurança em clusters de ativação pós-falha de convidado de VM de IaaS do Azure. 

Além disso, deve ter uma compreensão geral das seguintes tecnologias:

- [Solução hiperconvergida com espaços de armazenamento direto no Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Grupos de recursos do Azure](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Neste momento, o [extensão de agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md) não é suportada para a FCI do servidor SQL no Azure. Recomendamos que desinstale a extensão de VMs que participam do fci. Esta extensão oferece suporte a recursos, tais como cópia de segurança automatizada e a aplicação de patches e alguns recursos de portal para SQL. Estas funcionalidades não funcionarão para VMs de SQL após a desinstalação do agente.

### <a name="what-to-have"></a>O que fazer

Antes de seguir as instruções neste artigo, deverá já ter:

- Uma subscrição do Microsoft Azure.
- Um domínio do Windows em máquinas virtuais do Azure.
- Uma conta com permissão para criar objetos na máquina virtual do Azure.
- Uma rede virtual do Azure e a sub-rede IP suficiente espaço para os seguintes componentes de endereços:
   - Ambas as máquinas virtuais.
   - O endereço IP do cluster de ativação pós-falha.
   - Um endereço IP para cada FCI.
- DNS configurado na rede do Azure, que aponta para os controladores de domínio.

Com a configuração destes pré-requisitos, pode avançar com a criação do cluster de ativação pós-falha. A primeira etapa é criar as máquinas virtuais.

## <a name="step-1-create-virtual-machines"></a>Passo 1: Criar máquinas virtuais

1. Inicie sessão para o [portal do Azure](http://portal.azure.com) com a sua subscrição.

1. [Criar um conjunto de disponibilidade do Azure](../tutorial-availability-sets.md).

   A disponibilidade definida máquinas de virtuais de grupos em domínios de falha e domínios de atualização. O conjunto de disponibilidade torna-se de que a sua aplicação não é afetada por pontos únicos de falha, como o comutador de rede ou a unidade de energia de um bastidor de servidores.

   Se não tiver criado o grupo de recursos para as suas máquinas virtuais, fazê-lo ao criar um conjunto de disponibilidade do Azure. Se estiver a utilizar o portal do Azure para criar o conjunto de disponibilidade, siga os passos abaixo:

   - No portal do Azure, clique em **+** para abrir o Azure Marketplace. Procure **conjunto de disponibilidade**.
   - Clique em **conjunto de disponibilidade**.
   - Clique em **Criar**.
   - Sobre o **criar conjunto de disponibilidade** painel, defina os seguintes valores:
      - **Nome**: Um nome para o conjunto de disponibilidade.
      - **Subscrição**: A sua subscrição do Azure.
      - **Grupo de recursos**: Se pretender utilizar um grupo existente, clique em **utilizar existente** e selecione o grupo da lista pendente. Caso contrário, escolha **criar novo** e escreva um nome para o grupo.
      - **Localização**: Defina a localização em que planeja criar as suas máquinas virtuais.
      - **Domínios de falha**: Utilize a predefinição (3).
      - **Domínios de atualização**: Utilize a predefinição (5).
   - Clique em **criar** criar a disponibilidade definida.

1. Crie as máquinas virtuais no conjunto de disponibilidade.

   Aprovisione as duas máquinas virtuais do SQL Server no conjunto de disponibilidade do Azure. Para obter instruções, consulte [aprovisionar uma máquina virtual do SQL Server no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).

   Coloque a ambas as máquinas virtuais:

   - Estar na mesma o grupo de recursos que o conjunto de sua disponibilidade está em.
   - Na mesma rede como seu controlador de domínio.
   - Numa sub-rede com suficiente espaço de endereços IP para máquinas virtuais e todos os FCIs que, eventualmente, poderá utilizar este cluster.
   - No conjunto de disponibilidade do Azure.   

      >[!IMPORTANT]
      >Não é possível definir ou alterar o conjunto de disponibilidade depois de uma máquina virtual foi criada.

   Escolha uma imagem do Azure Marketplace. Pode usar um mercado imagem com o que inclui o Windows Server e SQL Server ou apenas o Windows Server. Para obter detalhes, consulte [descrição geral do SQL Server em máquinas de virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md)

   As imagens oficiais do SQL Server na galeria do Azure incluem uma instância do SQL Server instalada, além do software de instalação do SQL Server e a chave necessária.

   Escolha a imagem certa de acordo com a forma como pretende pagar a licença do SQL Server:

   - **Pagar por utilização licenciamento**: O custo por segundo destas imagens inclui o licenciamento do SQL Server:
      - **SQL Server 2016 Enterprise no Datacenter do Windows Server 2016**
      - **SQL Server 2016 Standard no Datacenter do Windows Server 2016**
      - **SQL Server 2016 Developer no Datacenter do Windows Server 2016**

   - **Bring-your-própria licença (BYOL)**

      - **{BYOL} SQL Server 2016 Enterprise no Datacenter do Windows Server 2016**
      - **{BYOL} SQL Server 2016 Standard no Datacenter do Windows Server 2016**

   >[!IMPORTANT]
   >Depois de criar a máquina virtual, remova a instância do SQL Server autónomo pré-instaladas. Irá utilizar o suporte de dados do SQL Server previamente instaladas para criar o SQL Server FCI depois de configurar o cluster de ativação pós-falha e o S2D.

   Em alternativa, pode utilizar imagens do Azure Marketplace com o sistema operacional. Escolher uma **Windows Server 2016 Datacenter** de imagem e instalar o SQL Server FCI depois de configurar o cluster de ativação pós-falha e o S2D. Esta imagem não contém o suporte de instalação do SQL Server. Coloque a mídia de instalação num local onde é possível executar a instalação do SQL Server para cada servidor.

1. Depois do Azure cria as suas máquinas virtuais, ligar-se a cada máquina virtual com RDP.

   Quando liga pela primeira vez a uma máquina virtual com RDP, o computador pergunta se pretende permitir este PC ser detectável na rede. Clique em **Sim**.

1. Se estiver a utilizar uma das imagens de máquina virtual baseada no SQL Server, remova a instância do SQL Server.

   - Na **programas e funcionalidades**, clique com botão direito **Microsoft SQL Server 2016 (64-bit)** e clique em **desinstalar/alterar**.
   - Clique em **remover**.
   - Selecione a instância predefinida.
   - Remover todas as funcionalidades em **serviços de motor de base de dados**. Não remova **recursos partilhados**. Veja a imagem seguinte:

      ![Remover funcionalidades](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Clique em **próxima**e, em seguida, clique em **remover**.

1. <a name="ports"></a>Abra as portas de firewall.

   Em cada máquina virtual, abra as seguintes portas na Firewall do Windows.

   | Objetivo | A porta TCP | Notas
   | ------ | ------ | ------
   | SQL Server | 1433 | Porta normal para instâncias padrão do SQL Server. Se utilizou uma imagem a partir da galeria, esta porta é aberta automaticamente.
   | Sonda de estado de funcionamento | 59999 | Abra qualquer porta TCP. Num passo posterior, configurar o Balanceador de carga [sonda de estado de funcionamento](#probe) e o cluster para utilizar esta porta.  

1. Adicione armazenamento para a máquina virtual. Para obter informações detalhadas, consulte [adicionar armazenamento](../disks-types.md).

   Ambas as máquinas virtuais tem de, pelo menos, dois discos de dados.

   Anexar discos brutos - não NTFS formatado discos.
      >[!NOTE]
      >Se anexar discos formatados com NTFS, apenas pode ativar o S2D com nenhuma verificação de elegibilidade de disco.  

   Anexe um mínimo de dois SSDs de premium para cada VM. Recomendamos, pelo menos, P30 (1 TB) de discos.

   Anfitrião de conjunto de colocação em cache para **só de leitura**.

   A capacidade de armazenamento que utilizar em ambientes de produção depende da sua carga de trabalho. Os valores descritos neste artigo são para teste e demonstração.

1. [Adicionar as máquinas virtuais para o seu domínio já existente](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Depois das máquinas virtuais são criadas e configuradas, pode configurar o cluster de ativação pós-falha.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>Passo 2: Configurar o Cluster de ativação pós-falha do Windows com o S2D

A próxima etapa é configurar o cluster de ativação pós-falha com o S2D. Neste passo, fará as substeps seguintes:

1. Adicionar a funcionalidade de Clustering de ativação pós-falha do Windows
1. Validar o cluster
1. Criar o cluster de ativação pós-falha
1. Criar o testemunho de nuvem
1. Adicionar armazenamento

### <a name="add-windows-failover-clustering-feature"></a>Adicionar a funcionalidade de Clustering de ativação pós-falha do Windows

1. Para começar, estabelecer ligação à primeira máquina virtual com RDP com uma conta de domínio que seja membro dos administradores locais e tem permissões para criar objetos no Active Directory. Utilize esta conta para o resto da configuração.

1. [Adicionar a funcionalidade de Clustering de ativação pós-falha para cada máquina virtual](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Para instalar a funcionalidade de Clustering de ativação pós-falha da interface do Usuário, siga os passos abaixo em ambas as máquinas virtuais.
   - Na **Gestor de servidores**, clique em **gerir**e, em seguida, clique em **adicionar funções e funcionalidades**.
   - Na **Assistente Adicionar funções e funcionalidades**, clique em **próxima** até chegar à **selecionar funcionalidades**.
   - Na **selecionar funcionalidades**, clique em **Clustering de ativação pós-falha**. Inclua todos os recursos e as ferramentas de gestão. Clique em **adicionar funcionalidades**.
   - Clique em **próxima** e, em seguida, clique em **concluir** para instalar as funcionalidades.

   Para instalar a funcionalidade Clustering de ativação pós-falha com o PowerShell, execute o seguinte script a partir de uma sessão do PowerShell de administrador em uma das máquinas virtuais.

   ```PowerShell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Para referência, os passos seguintes siga as instruções no passo 3 do [solução hiperconvergida com espaços de armazenamento direto no Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Validar o cluster

Este guia refere-se para as instruções em [validar cluster](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Valide o cluster na IU ou o PowerShell.

Para validar o cluster com a interface do Usuário, siga os passos abaixo a partir de uma das máquinas virtuais.

1. Na **Gestor de servidores**, clique em **ferramentas**, em seguida, clique em **Gestor de clusters de ativação pós-falha**.
1. Na **Gestor de clusters de ativação pós-falha**, clique em **ação**, em seguida, clique em **validar configuração...** .
1. Clique em **Seguinte**.
1. No **selecionar servidores ou um Cluster**, escreva o nome de ambas as máquinas virtuais.
1. No **opções de testes**, escolha **executar apenas os testes selecionados**. Clique em **Seguinte**.
1. No **seleção de teste**, inclua todos os testes, exceto **armazenamento**. Veja a imagem seguinte:

   ![Validar testes](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Clique em **Seguinte**.
1. No **confirmação**, clique em **próxima**.

O **validar um Assistente de configuração** executa os testes de validação.

Para validar o cluster com o PowerShell, execute o seguinte script a partir de uma sessão do PowerShell de administrador em uma das máquinas virtuais.

   ```PowerShell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Depois de validar o cluster, crie o cluster de ativação pós-falha.

### <a name="create-the-failover-cluster"></a>Criar o cluster de ativação pós-falha

Este guia se refere à [criar o cluster de ativação pós-falha](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster).

Para criar o cluster de ativação pós-falha, terá de:
- Os nomes das máquinas virtuais que se tornará os nós do cluster.
- Um nome para o cluster de ativação pós-falha
- Um endereço IP para o cluster de ativação pós-falha. Pode utilizar um endereço IP que não é utilizado a mesma rede virtual do Azure e a sub-rede como nós do cluster.

O PowerShell seguinte cria um cluster de ativação pós-falha. Atualize o script com os nomes de nós (os nomes de máquina virtual) e um endereço IP disponível na VNET do Azure:

```PowerShell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

### <a name="create-a-cloud-witness"></a>Criar um testemunho de nuvem

Testemunho de nuvem é um novo tipo de testemunho de quórum de cluster armazenado num Blob de armazenamento do Azure. Esta ação remove a necessidade de uma VM separada que aloja uma partilha de testemunho.

1. [Criar um testemunho de nuvem para o cluster de ativação pós-falha](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Crie um contentor de Blobs.

1. Guarde as chaves de acesso e o URL de contentor.

1. Configure o testemunho de quórum do cluster de ativação pós-falha. Ver, [configurar o testemunho de quórum na interface do usuário](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) na interface do Usuário.

### <a name="add-storage"></a>Adicionar armazenamento

Os discos para S2D tem de estar em branco e sem partições ou outros dados. Para limpar discos siga [os passos neste guia](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks).

1. [Ativar Store espaços diretos \(S2D\)](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   O PowerShell seguinte permite que os espaços de armazenamento direto.  

   ```PowerShell
   Enable-ClusterS2D
   ```

   Na **Gestor de clusters de ativação pós-falha**, agora, pode ver o agrupamento de armazenamento.

1. [Criar um volume](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Um dos recursos de S2D é que ele automaticamente cria um agrupamento de armazenamento se a ativar. Agora está pronto para criar um volume. O commandlet PowerShell `New-Volume` automatiza o processo de criação do volume, incluindo formatação, adicionar ao cluster e criar um volume partilhado de cluster (CSV). O exemplo seguinte cria um 800 gigabyte (GB) CSV.

   ```PowerShell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Depois de concluída este comando, um volume de 800 GB é montado como um recurso de cluster. O volume está em `C:\ClusterStorage\Volume1\`.

   O diagrama seguinte mostra um volume partilhado de cluster com o S2D:

   ![ClusterSharedVolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Passo 3: Ativação pós-falha de cluster de ativação pós-falha de teste

No Gestor de clusters de ativação pós-falha, certifique-se de que pode mover o recurso de armazenamento para o outro nó de cluster. Se consegue ligar ao cluster de ativação pós-falha com **Gestor de clusters de ativação pós-falha** e mover o armazenamento de um nó para outro, está pronto para configurar a FCI.

## <a name="step-4-create-sql-server-fci"></a>Passo 4: Criar o SQL Server FCI

Depois de configurar o cluster de ativação pós-falha e todos os componentes de cluster, incluindo o armazenamento, pode criar o SQL Server FCI.

1. Ligue-se à primeira máquina virtual com RDP.

1. Na **Gestor de clusters de ativação pós-falha**, certifique-se de que todos os recursos principais do cluster na primeira máquina virtual. Se necessário, mova todos os recursos para esta máquina virtual.

1. Localize a mídia de instalação. Se a máquina virtual utiliza uma das imagens do Azure Marketplace, o suporte de dados está localizada em `C:\SQLServer_<version number>_Full`. Clique em **configuração**.

1. Na **Centro de instalação do SQL Server**, clique em **instalação**.

1. Clique em **instalação de cluster de ativação pós-falha do novo servidor SQL**. Siga as instruções no Assistente para instalar o SQL Server FCI.

   Os diretórios de dados da FCI precisam estar no armazenamento em cluster. Com o S2D, não é um disco partilhado, mas como um ponto de montagem para um volume em cada servidor. O S2D sincroniza o volume entre ambos os nós. O volume é apresentado ao cluster como volume partilhado de cluster. Utilize o ponto de montagem CSV para os diretórios de dados.

   ![DataDirectories](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Depois de concluir o assistente, a configuração irá instalar um SQL Server FCI no primeiro nó.

1. Depois do programa de configuração instala com êxito a FCI no primeiro nó, ligar-se para o segundo nó com RDP.

1. Abra o **Centro de instalação do SQL Server**. Clique em **instalação**.

1. Clique em **adicionar nó num cluster de ativação pós-falha do SQL Server**. Siga as instruções no Assistente para instalar o SQL server e adicionar este servidor para a FCI.

   >[!NOTE]
   >Se utilizou uma imagem de galeria do Azure Marketplace com o SQL Server, ferramentas do SQL Server foram incluídas com a imagem. Se não utilizou esta imagem, instale as ferramentas do SQL Server em separado. Ver [transferir o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-azure-load-balancer"></a>Passo 5: Criar um balanceador de carga do Azure

Em máquinas virtuais do Azure, clusters, utilize um balanceador de carga para manter um endereço IP que tem de estar num nó de cluster, ao mesmo tempo. Nesta solução, o Balanceador de carga contém o endereço IP para a FCI do SQL Server.

[Criar e configurar um balanceador de carga](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Criar o Balanceador de carga no portal do Azure

Para criar o Balanceador de carga:

1. No portal do Azure, vá para o grupo de recursos com as máquinas virtuais.

1. Clique em **+ Adicionar**. Pesquisar no Marketplace **Balanceador de carga**. Clique em **Balanceador de carga**.

1. Clique em **Criar**.

1. Configure o Balanceador de carga com:

   - **Nome**: Um nome que identifica o Balanceador de carga.
   - **Tipo de**: O Balanceador de carga pode ser público ou privado. Um balanceador de carga privada pode ser acessado de dentro da mesma VNET. As aplicações mais do Azure podem utilizar um balanceador de carga privado. Se seu aplicativo precisa de aceder ao SQL Server diretamente através da Internet, utilize um balanceador de carga público.
   - **Rede virtual**: A rede mesmo que as máquinas virtuais.
   - **Sub-rede**: Da mesma sub-rede que as máquinas virtuais.
   - **O endereço IP privado**: O mesmo endereço IP que atribuiu para o recurso de rede de cluster do SQL Server FCI.
   - **Subscrição**: A sua subscrição do Azure.
   - **Grupo de recursos**: Utilize o mesmo grupo de recursos como suas máquinas virtuais.
   - **Localização**: Utilize a mesma localização do Azure como máquinas virtuais.
   Veja a imagem seguinte:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Configurar o conjunto de back-end de Balanceador de carga

1. Voltar para o grupo de recursos do Azure com as máquinas virtuais e localize o novo Balanceador de carga. Poderá ter de atualizar a vista no grupo de recursos. Clique no balanceador de carga.

1. Clique em **conjuntos de back-end** e clique em **+ adicionar** para adicionar um conjunto de back-end.

1. Associe o conjunto de back-end com o conjunto de disponibilidade que contém as VMs.

1. Sob **configurações de IP de rede de destino**, verifique **máquina VIRTUAL** e escolha as máquinas virtuais que irá participar como nós de cluster. Certifique-se de que inclui todas as máquinas virtuais que irá alojar a FCI. 

1. Clique em **OK** para criar o conjunto de back-end.

### <a name="configure-a-load-balancer-health-probe"></a>Configurar uma sonda de estado de funcionamento do Balanceador de carga

1. No painel de Balanceador de carga, clique em **sondas de estado de funcionamento**.

1. Clique em **+ Adicionar**.

1. Sobre o **adicionar sonda de estado de funcionamento** painel, <a name="probe"> </a>definir o estado de funcionamento parâmetros de pesquisa:

   - **Nome**: Um nome para a sonda de estado de funcionamento.
   - **Protocolo**: TCP.
   - **Porta**: Definido como uma porta TCP disponível. Esta porta precisa de uma porta de firewall aberta. Utilize o [mesma porta](#ports) definido para a sonda de estado de funcionamento à firewall.
   - **Intervalo**: 5 segundos.
   - **Limiar de mau estado de funcionamento**: 2 falhas consecutivas.

1. Clique em OK.

### <a name="set-load-balancing-rules"></a>Definir regras de balanceamento de carga

1. No painel de Balanceador de carga, clique em **regras de balanceamento de carga**.

1. Clique em **+ Adicionar**.

1. Defina parâmetros de regras de balanceamento de carga:

   - **Nome**: Um nome para a regras de balanceamento de carga.
   - **Endereço IP de front-end**: Utilize o endereço IP para o recurso de rede de cluster do SQL Server FCI.
   - **Porta**: Definir para a porta TCP do SQL Server FCI. A porta de instância padrão é 1433.
   - **Porta de back-end**: Este valor usa a mesma porta como a **porta** valor quando ativa **IP flutuante (devolução direta do servidor)**.
   - **Conjunto back-end**: Utilize o nome do conjunto de back-end que configurou anteriormente.
   - **Sonda de estado de funcionamento**: Utilize a sonda de estado de funcionamento que configurou anteriormente.
   - **Persistência da sessão**: Nenhum.
   - **Tempo limite (minutos) de inatividade**: 4.
   - **Vírgula flutuante (devolução direta do servidor) de IP**: Ativado

1. Clique em **OK**.

## <a name="step-6-configure-cluster-for-probe"></a>Passo 6: Configurar o cluster para a sonda

Defina o parâmetro de porta de sonda de cluster no PowerShell.

Para definir o parâmetro de porta de sonda de cluster, Atualize as variáveis no script seguinte com valores do seu ambiente. Remover os parênteses angulares `<>` do script. 

   ```PowerShell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

No script anterior, defina os valores para o seu ambiente. A lista seguinte descreve os valores:

   - `<Cluster Network Name>`: Nome do Cluster de ativação pós-falha do Windows Server para a rede. Na **Gestor de clusters de ativação pós-falha** > **redes**, clique com o botão direito na rede e clique em **propriedades**. O valor correto está sob **nome** sobre o **geral** separador. 

   - `<SQL Server FCI IP Address Resource Name>`: Nome do recurso de endereço IP do SQL Server FCI. Na **Gestor de clusters de ativação pós-falha** > **funções**, sob a função do SQL Server FCI, em **nome do servidor**, clique com o botão direito do rato no recurso de endereço IP e clique em **Propriedades**. O valor correto está sob **nome** sobre o **geral** separador. 

   - `<ILBIP>`: O endereço IP do ILB. Este endereço é configurado no portal do Azure como o endereço de front-end do ILB. Isso também é o endereço IP do SQL Server FCI. Pode encontrá-lo na **Gestor de clusters de ativação pós-falha** na mesma página de propriedades onde localizado o `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: É a porta de sonda que configurou na sonda de estado de funcionamento do Balanceador de carga. Qualquer porta TCP não utilizada é válida. 

>[!IMPORTANT]
>A máscara de sub-rede para o parâmetro de cluster tem de ser o endereço de difusão de TCP IP: `255.255.255.255`.

Depois de definir a sonda de cluster, pode ver todos os parâmetros de cluster no PowerShell. Execute o seguintes script:

   ```PowerShell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Passo 7: Ativação pós-falha da FCI de teste

Ativação pós-falha de teste da FCI para validar a funcionalidade de cluster. Siga os passos abaixo:

1. Ligar a um de nós de cluster do SQL Server FCI com RDP.

1. Open **Gestor de clusters de ativação pós-falha**. Clique em **funções**. Aviso de nó que possui a função do SQL Server FCI.

1. Com o botão direito a função do SQL Server FCI.

1. Clique em **mover** e clique em **melhor nó possível**.

**Gestor de clusters de ativação pós-falha** mostra a função e os respetivos recursos ficam offline. Os recursos, em seguida, moverem e ficam online no outro nó.

### <a name="test-connectivity"></a>Testar conectividade

Para testar a conectividade, inicie sessão outra máquina virtual na mesma rede virtual. Open **SQL Server Management Studio** e ligue-se ao nome do SQL Server FCI.

>[!NOTE]
>Se necessário, pode [transferir o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Limitações

Máquinas virtuais do Azure suportam o coordenador de transações distribuídas ' (MSDTC) da Microsoft no Windows Server 2019 com o armazenamento em volumes partilhados em cluster (CSV) e um [Balanceador de carga standard](../../../load-balancer/load-balancer-standard-overview.md).

Em máquinas virtuais do Azure, MSDTC não é suportada no Windows Server 2016 e versões anteriores porque:

- O recurso MSDTC em cluster não pode ser configurado para utilizar armazenamento partilhado. Com o Windows Server 2016 se criar um recurso MSDTC, só serão apresentadas qualquer armazenamento partilhado disponível para utilização, mesmo que o armazenamento está lá. Este problema foi corrigido no Windows Server 2019.
- O Balanceador de carga básico não processa as portas RPC.

## <a name="see-also"></a>Consultar Também

[Configuração de S2D com o ambiente de trabalho remoto (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Solução hiperconvergida com espaços de armazenamento direto](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct).

[Descrição geral direta do espaço de armazenamento](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Suporte do SQL Server para o S2D](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
