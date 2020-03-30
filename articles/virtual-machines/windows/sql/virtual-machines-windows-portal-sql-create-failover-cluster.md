---
title: SQL Server FCI - Máquinas Virtuais Azure / Microsoft Docs
description: Este artigo explica como criar uma instância de cluster de falha do SQL Server em máquinas virtuais Azure.
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
ms.openlocfilehash: 20c231e4f3052797eac79a3c97a3d8148690b8c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249805"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Configure uma instância de cluster de falha do Servidor SQL em máquinas virtuais Azure

Este artigo explica como criar uma instância de cluster de falha do SQL Server (FCI) em máquinas virtuais Azure no modelo Azure Resource Manager. Esta solução utiliza a [edição do Datacenter Do Windows 2016 Storage Spaces Direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) como um SAN virtual baseado em software que sincroniza o armazenamento (discos de dados) entre os nós (VMs Azure) num cluster Windows. Espaços de Armazenamento Direct foi novidade no Windows Server 2016.

O diagrama que se segue mostra a solução completa nas máquinas virtuais azure:

![A solução completa](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Este diagrama mostra:

- Duas máquinas virtuais Azure num Cluster de Failover do Servidor do Windows. Quando uma máquina virtual está num aglomerado de falhas, também é chamada de nó de *cluster* ou *nó.*
- Cada máquina virtual tem dois ou mais discos de dados.
- Espaços de Armazenamento A sincronização direta dos dados nos discos de dados e apresenta o armazenamento sincronizado como um pool de armazenamento.
- O pool de armazenamento apresenta um Cluster Shared Volume (CSV) ao cluster failover.
- A função de cluster SQL Server FCI utiliza o CSV para as unidades de dados.
- Um equilibrador de carga Azure para manter o endereço IP para o SQL Server FCI.
- Um conjunto de disponibilidade azure detém todos os recursos.

>[!NOTE]
>Todos os recursos do Azure no diagrama estão no mesmo grupo de recursos.

Para mais detalhes sobre espaços de armazenamento Direto, consulte a edição do [Windows Server 2016 Datacenter Espaços de Armazenamento Direto](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

Espaços de Armazenamento A Direct suporta dois tipos de arquiteturas: convergentes e hiperconvergentes. A arquitetura neste documento é hiper-convergida. Uma infraestrutura hiper-convergente coloca o armazenamento nos mesmos servidores que acolhem a aplicação agrupada. Nesta arquitetura, o armazenamento está em cada nó SQL Server FCI.

## <a name="licensing-and-pricing"></a>Licenciamento e preços

Em máquinas virtuais Azure, pode licenciar o SQL Server utilizando imagens VM pay-as-you-go (PAYG) ou trazer as imagens VM da sua própria licença (BYOL). O tipo de imagem que escolhe saem da forma como é cobrado.

Com o licenciamento pay-as-you-go, uma instância de cluster failover (FCI) do SQL Server em máquinas virtuais Azure incorre em encargos para todos os nós do FCI, incluindo os nós passivos. Para mais informações, consulte o Preço das [Máquinas Virtuais da Empresa de Servidores SQL](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Se tiver acordo de empresa com garantia de software, pode utilizar um nó FCI passivo gratuito para cada nó ativo. Para aproveitar este benefício em Azure, utilize imagens BYOL VM e use a mesma licença tanto nos nós ativos como passivos do FCI. Para mais informações, consulte [o Acordo de Empresa.](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)

Para comparar o licenciamento pay-as-you-go e byoL para o SQL Server em máquinas virtuais Azure, consulte [Get started with SQL VMs](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Para obter informações completas sobre o licenciamento do Servidor SQL, consulte [O Preço](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Modelo de azure exemplo

Você pode criar toda esta solução em Azure a partir de um modelo. Um exemplo de um modelo está disponível nos modelos GitHub [Azure Quickstart](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). Este exemplo não é projetado ou testado para qualquer carga de trabalho específica. You can run the template to create a SQL Server FCI with Storage Spaces Direct storage connected to your domain. Pode avaliar o modelo e modificá-lo para os seus fins.

## <a name="before-you-begin"></a>Antes de começar

Há algumas coisas que precisa saber e ter no lugar antes de começar.

### <a name="what-to-know"></a>O que saber
Deve ter uma compreensão operacional destas tecnologias:

- [Tecnologias de cluster windows](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [Casos de cluster de falha do servidor SQL](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Uma coisa a ter em conta é que num cluster de failover de hóspedes Do VM Azure IaaS, recomendamos um único NIC por servidor (nó de cluster) e uma única subnet. A rede Azure tem redundância física, o que torna os NICs e subnets adicionais desnecessários num aglomerado de hóspedes Azure IaaS VM. O relatório de validação do cluster irá avisá-lo de que os nós são acessíveis apenas numa única rede. Pode ignorar este aviso sobre os clusters de failover de hóspedes da Azure IaaS VM.

Deve também ter uma compreensão geral destas tecnologias:

- [Soluções hiperconvergentes que utilizam espaços de armazenamento direto no Windows Server 2016](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Grupos de recursos azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Neste momento, as instâncias de cluster de falha do SQL Server nas máquinas virtuais Azure são suportadas apenas com o modo de [gestão leve](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) da extensão do [agente SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Para mudar do modo de extensão completo para leve, elimine o recurso **SQL Virtual Machine** para os VMs correspondentes e, em seguida, registe-os com o fornecedor de recursos SQL VM em modo leve. Ao apagar o recurso **SQL Virtual Machine** utilizando o portal Azure, limpe a caixa de **verificação junto à máquina virtual correta**. A extensão completa suporta funcionalidades como backup automatizado, patching e gestão avançada do portal. Estas funcionalidades não funcionarão para VMs SQL depois de o agente ser reinstalado em modo de gestão leve.

### <a name="what-to-have"></a>O que ter

Antes de completar os passos deste artigo, já deve ter:

- Uma subscrição do Microsoft Azure.
- Um domínio Windows em máquinas virtuais Azure.
- Uma conta que tem permissões para criar objetos tanto nas máquinas virtuais Azure como no Ative Directory.
- Uma rede virtual Azure e uma subrede com espaço de endereço IP suficiente para estes componentes:
   - Ambas as máquinas virtuais.
   - O endereço IP do cluster failover.
   - Um endereço IP para cada FCI.
- DNS configurado na rede Azure, apontando para os controladores de domínio.

Com estes pré-requisitos no lugar, você pode começar a construir o seu cluster de failover. O primeiro passo é criar as máquinas virtuais.

## <a name="step-1-create-the-virtual-machines"></a>Passo 1: Criar as máquinas virtuais

1. Inscreva-se no [portal Azure](https://portal.azure.com) com a sua subscrição.

1. [Crie um conjunto de disponibilidade azure.](../tutorial-availability-sets.md)

   O conjunto de disponibilidade groups máquinas virtuais através de domínios de falha e domínios de atualização. Garante que a sua aplicação não é afetada por um único ponto de falha, como o interruptor de rede ou a unidade de alimentação de um rack de servidores.

   Se ainda não criou o grupo de recursos para as suas máquinas virtuais, faça-o quando criar um conjunto de disponibilidade sinuoso Azure. Se estiver a usar o portal Azure para criar o conjunto de disponibilidade, tome estes passos:

   1. No portal Azure, selecione **Criar um recurso** para abrir o Azure Marketplace. Procure por **disponibilidade definida**.
   1. Selecione **Conjunto de Disponibilidade**.
   1. Selecione **Criar**.
   1. No **conjunto de disponibilidade criar,** fornecer estes valores:
      - **Nome**: Um nome para o conjunto de disponibilidade.
      - **Subscrição**: A sua subscrição Azure.
      - **Grupo de recursos**: Se pretender utilizar um grupo existente, clique em **Selecionar existente** e, em seguida, selecione o grupo a partir da lista. Caso contrário, selecione **Criar novo** e insira um nome para o grupo.
      - **Localização**: Detete o local onde pretende criar as suas máquinas virtuais.
      - **Domínios**de avaria: Utilize o padrão **(3**).
      - **Domínios de atualização**: Utilize o padrão **(5**).
   1. Selecione **Criar** para criar o conjunto de disponibilidade.

1. Crie as máquinas virtuais no conjunto de disponibilidade.

   Fornecer duas máquinas virtuais SQL Server no conjunto de disponibilidade do Azure. Para obter instruções, consulte [A provisionia uma máquina virtual SQL Server no portal Azure](virtual-machines-windows-portal-sql-server-provision.md).

   Coloque ambas as máquinas virtuais:

   - No mesmo grupo de recursos Azure que o seu conjunto de disponibilidade.
   - Na mesma rede que o seu controlador de domínio.
   - Numa sub-rede que tem espaço de endereço IP suficiente para máquinas virtuais e todas as FCIs que poderá eventualmente utilizar no cluster.
   - No conjunto de disponibilidade do Azure.

      >[!IMPORTANT]
      >Não pode definir ou alterar o conjunto de disponibilidade depois de ter criado uma máquina virtual.

   Escolha uma imagem do Azure Marketplace. Pode utilizar uma imagem do Azure Marketplace que inclui o Windows Server e o SQL Server, ou utilizar uma que apenas inclui o Windows Server. Para mais detalhes, consulte a [visão geral do Servidor SQL nas máquinas virtuais Azure](virtual-machines-windows-sql-server-iaas-overview.md).

   As imagens oficiais do SQL Server na Galeria Azure incluem uma instância de Servidor SQL instalada, o software de instalação SQL Server e a chave necessária.

   Escolha a imagem certa, com base na forma como pretende pagar a licença Do Servidor SQL:

   - **Licenciamento pay-per-use**. O custo por segundo destas imagens inclui o licenciamento do SQL Server:
      - **SQL Server 2016 Enterprise no Windows Server 2016 Datacenter**
      - **Padrão do SQL Server 2016 no Windows Server 2016 Datacenter**
      - **Desenvolvedor do SQL Server 2016 no Windows Server 2016 Datacenter**

   - **Trazer a sua própria licença (BYOL)**

      - **(BYOL) SQL Server 2016 Enterprise no Windows Server 2016 Datacenter**
      - **(BYOL) Padrão do SQL Server 2016 no Windows Server 2016 Datacenter**

   >[!IMPORTANT]
   >Depois de criar a máquina virtual, remova a instância de Servidor SQL autónoma pré-instalada. Utilizará os meios de comunicação SQL Server pré-instalados para criar o SQL Server FCI depois de configurar o cluster failover e espaços de armazenamento Direto.

   Em alternativa, pode utilizar imagens do Azure Marketplace que contenham apenas o sistema operativo. Escolha uma imagem **do Datacenter do Windows Server 2016** e instale o SQL Server FCI depois de configurar o cluster failover e espaços de armazenamento Direto. Esta imagem não contém meios de instalação do SQL Server. Coloque os meios de instalação do SQL Server num local onde possa executá-lo para cada servidor.

1. Depois do Azure criar as suas máquinas virtuais, ligue-se a cada uma utilizando RDP.

   Quando se conecta pela primeira vez a uma máquina virtual utilizando RDP, uma solicitação pergunta-lhe se pretende permitir que o PC seja detetável na rede. Selecione **Sim**.

1. Se estiver a utilizar uma das imagens da máquina virtual baseada no Servidor SQL, remova a instância do Servidor SQL.

   1. Em **Programas e Funcionalidades,** clique à direita **no Microsoft SQL Server 2016 (64 bits)** e selecione **Desinstalar/Alterar**.
   1. Selecione **Remover**.
   1. Selecione a instância predefinida.
   1. Remova todas as funcionalidades em **Serviços**de Motor de Base de Dados . Não remova **as funcionalidades partilhadas.** Verá algo como a seguinte imagem:

      ![Selecione Funcionalidades](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Selecione **Next**, e, em seguida, selecione **Remover**.

1. <a name="ports"></a>Abra as portas de firewall.

   Em cada máquina virtual, abra estas portas na Firewall do Windows:

   | Objetivo | Porta TCP | Notas
   | ------ | ------ | ------
   | SQL Server | 1433 | Porta normal para casos padrão do Servidor SQL. Se usou uma imagem da galeria, esta porta é aberta automaticamente.
   | Sonda de estado de funcionamento | 59999 | Qualquer porta TCP aberta. Num passo posterior, configure a sonda de [saúde](#probe) do equilibrador de carga e o cluster para utilizar esta porta.  

1. Adicione o armazenamento à máquina virtual. Para obter informações detalhadas, consulte [adicionar armazenamento](../disks-types.md).

   Ambas as máquinas virtuais precisam de pelo menos dois discos de dados.

   Fixe discos crus, não discos formados em NTFS.
      >[!NOTE]
      >Se anexar discos formados ntfS, só pode ativar espaços de armazenamento diretamente sem uma verificação de elegibilidade do disco.  

   Fixe um mínimo de dois SSDs premium a cada VM. Recomendamos pelo menos discos P30 (1-TB).

   Definir o cache do hospedeiro para **read-only**.

   A capacidade de armazenamento que utiliza em ambientes de produção depende da sua carga de trabalho. Os valores descritos neste artigo são para demonstração e teste.

1. [Adicione as máquinas virtuais ao seu domínio pré-existente](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Depois de criar e configurar as máquinas virtuais, pode configurar o cluster failover.

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>Passo 2: Configure o cluster de falha do servidor do Windows com espaços de armazenamento direto

O próximo passo é configurar o cluster failover com Espaços de Armazenamento Direto. Neste passo, completará estes subpassos:

1. Adicione a função de Clustering de Falha do Servidor do Windows.
1. Validar o cluster.
1. Crie o cluster de falhas.
1. Crie a testemunha da nuvem.
1. Adicione o armazenamento.

### <a name="add-windows-server-failover-clustering"></a>Adicionar clustering de falha do servidor do Windows

1. Ligue-se à primeira máquina virtual com RDP utilizando uma conta de domínio que é membro dos administradores locais e que tem permissão para criar objetos no Diretório Ativo. Utilize esta conta para o resto da configuração.

1. [Adicione o Agrupamento failover a cada máquina virtual](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Para instalar o Clusterfailover a partir da UI, dê estes passos em ambas as máquinas virtuais:
   1. No **Gestor do Servidor,** selecione **Gerir**e, em seguida, selecione **Adicionar Funções e Funcionalidades**.
   1. No **Assistente de Adicionar Funções e Funcionalidades,** selecione **Seguinte** até **selecionar funcionalidades**.
   1. Em **Funcionalidades selecionadas,** selecione **'Clustering Failover**'. Inclua todas as funcionalidades necessárias e as ferramentas de gestão. Selecione **Adicionar Funcionalidades**.
   1. Selecione **Next**, e, em seguida, selecione **Terminar** para instalar as funcionalidades.

   Para instalar o Failover Clustering utilizando o PowerShell, execute o seguinte script a partir de uma sessão de administrador PowerShell numa das máquinas virtuais:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Para mais referências sobre os próximos passos, consulte as instruções sob o Passo 3 da [solução hiperconvergente utilizando espaços de armazenamento direto no Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Validar o cluster

Valide o cluster na UI ou utilizando o PowerShell.

Para validar o cluster utilizando a UI, dê os seguintes passos numa das máquinas virtuais:

1. Sob **o Gestor do Servidor,** selecione **Ferramentas**e, em seguida, selecione **Failover Cluster Manager**.
1. Em **Failover Cluster Manager,** selecione **Action**, e, em seguida, selecione **Validate Configuration**.
1. Selecione **Next**.
1. Em **Servidores Selecionados ou num Cluster,** introduza os nomes de ambas as máquinas virtuais.
1. Em **opções**de teste, selecione **apenas testes de execução que selecionei**. Selecione **Next**.
1. Em seleção de **testes,** selecione todos os testes exceto o **armazenamento,** como mostrado aqui:

   ![Selecione testes de validação de clusters](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Selecione **Next**.
1. Sob **confirmação,** selecione **Seguinte**.

O Validar um Assistente de Configuração executa os testes de validação.

Para validar o cluster utilizando o PowerShell, execute o seguinte script a partir de uma sessão de administrador PowerShell numa das máquinas virtuais:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Depois de validar o cluster, crie o cluster failover.

### <a name="create-the-failover-cluster"></a>Criar o cluster de ativação pós-falha

Para criar o cluster failover, você precisa:
- Os nomes das máquinas virtuais que se tornarão os nós do cluster.
- Um nome para o cluster failover
- Um endereço IP para o cluster failover. Pode utilizar um endereço IP que não seja utilizado na mesma rede virtual Azure e sub-rede que os nós do cluster.

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows Server 2008 através do Windows Server 2016

O seguinte script PowerShell cria um cluster de failover para Windows Server 2008 através do Windows Server 2016. Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível da rede virtual Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

O seguinte script PowerShell cria um cluster de failover para o Windows Server 2019. Para mais informações, consulte [O cluster Failover: Cluster Network Object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível da rede virtual Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Criar uma testemunha de nuvem

Cloud Witness é um novo tipo de testemunha quórum de cluster que está armazenada numa bolha de armazenamento Azure. Isto elimina a necessidade de um VM separado que acolhe uma partilha de testemunhas.

1. [Crie uma testemunha de nuvem para o cluster failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Crie um recipiente de bolhas.

1. Guarde as chaves de acesso e o URL do recipiente.

1. Configure a testemunha quórum do cluster failover. Consulte [a configuração da testemunha quórum na interface do utilizador](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

### <a name="add-storage"></a>Adicionar armazenamento

Os discos para espaços de armazenamento Diretos precisam de estar vazios. Não podem conter divisórias ou outros dados. Para limpar os discos, siga [os passos deste guia](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives).

1. [Ativar espaços de loja direto](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   O seguinte script PowerShell permite espaços de armazenamento direto:  

   ```powershell
   Enable-ClusterS2D
   ```

   Em **Failover Cluster Manager,** já pode ver a piscina de armazenamento.

1. [Criar um volume.](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes)

   Espaços de Armazenamento O Direct cria automaticamente um depósito quando o ativa. Está pronto para criar um volume. O cmdlet `New-Volume` PowerShell automatiza o processo de criação de volume. Este processo inclui a formatação, a adição do volume ao cluster e a criação de um Volume Partilhado cluster (CSV). Este exemplo cria um CSV de 800 gigabytes (GB):

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Após o fim deste comando, um volume de 800 GB é montado como um recurso de cluster. O volume `C:\ClusterStorage\Volume1\`está em .

   Esta imagem mostra um Volume Partilhado cluster com espaços de armazenamento direto:

   ![Volume partilhado cluster](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Passo 3: Falha de teste do cluster failover

No **Failover Cluster Manager,** verifique se pode mover o recurso de armazenamento para o outro nó de cluster. Se conseguir ligar-se ao cluster failover utilizando o **Failover Cluster Manager** e mover o armazenamento de um nó para o outro, está pronto para configurar o FCI.

## <a name="step-4-create-the-sql-server-fci"></a>Passo 4: Criar o Servidor SQL FCI

Depois de configurar o cluster failover e todos os componentes do cluster, incluindo o armazenamento, pode criar o SQL Server FCI.

1. Ligue-se à primeira máquina virtual utilizando RDP.

1. No **Failover Cluster Manager,** certifique-se de que todos os Recursos Core Cluster estão na primeira máquina virtual. Se necessário, mova todos os recursos para aquela máquina virtual.

1. Localize os meios de instalação. Se a máquina virtual utilizar uma das imagens do `C:\SQLServer_<version number>_Full`Azure Marketplace, os meios de comunicação estão localizados em . **Selecione Configuração**.

1. No **Centro de Instalação do Servidor SQL,** selecione **Instalação**.

1. Selecione nova instalação de **cluster de falha do Servidor SQL**. Siga as instruções no assistente para instalar o SQL Server FCI.

   Os diretórios de dados da FCI têm de estar em armazenamento agrupado. Com Espaços de Armazenamento Direto, não é um disco partilhado, mas um ponto de montagem para um volume em cada servidor. Espaços de Armazenamento Sincronizadiretamente o volume entre ambos os nós. O volume é apresentado ao cluster como um Volume Partilhado cluster. Utilize o ponto de montagem do CSV para os diretórios de dados.

   ![Diretórios de dados](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Depois de completar as instruções no assistente, a Configuração instalará um SQL Server FCI no primeiro nó.

1. Depois de configurar o FCI no primeiro nó, ligue-se ao segundo nó utilizando RDP.

1. Abra o Centro de Instalação do **Servidor SQL**. **Selecione Instalação**.

1. Selecione Adicionar nó a um cluster de falha do **Servidor SQL**. Siga as instruções no assistente para instalar o Servidor SQL e adicione o servidor ao FCI.

   >[!NOTE]
   >Se usou uma imagem de galeria Azure Marketplace que contém o SQL Server, as ferramentas do SQL Server foram incluídas com a imagem. Se não usou uma dessas imagens, instale as ferramentas Do Servidor SQL separadamente. Consulte o [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-the-azure-load-balancer"></a>Passo 5: Criar o equilibrador de carga Azure

Nas máquinas virtuais azure, os clusters usam um equilibrador de carga para segurar um endereço IP que precisa estar num nó de cluster de cada vez. Nesta solução, o equilibrador de carga detém o endereço IP para o SQL Server FCI.

Para mais informações, consulte [Criar e configurar um equilibrador](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)de carga Azure .

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Crie o equilibrador de carga no portal Azure

Para criar o equilibrador de carga:

1. No portal Azure, vá ao grupo de recursos que contém as máquinas virtuais.

1. Selecione **Adicionar**. Pesquise no Mercado Azure para **balancer de carga.** Selecione **Balanceor de carga**.

1. Selecione **Criar**.

1. Configure o equilibrador de carga com:

   - **Subscrição**: A sua subscrição Azure.
   - **Grupo de recursos**: O grupo de recursos que contém as suas máquinas virtuais.
   - **Nome**: Um nome que identifique o equilibrador de carga.
   - **Região**: A localização Azure que contém as suas máquinas virtuais.
   - **Tipo**: Público ou privado. Um equilibrador de carga privado pode ser acedido a partir da rede virtual. A maioria das aplicações Azure pode usar um equilibrador de carga privado. Se a sua aplicação necessitar de acesso ao SQL Server diretamente através da internet, utilize um equilibrador de carga público.
   - **SKU:** Standard.
   - **Rede virtual**: A mesma rede que as máquinas virtuais.
   - **Atribuição de endereço IP**: Estática. 
   - **Endereço IP privado**: O endereço IP que atribuiu ao recurso de rede de cluster SQL Server FCI.

 A seguinte imagem mostra o Create **load balancer** UI:

   ![Configurar o equilibrador de carga](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Configure a piscina de backend do equilibrador de carga

1. Volte ao grupo de recursos Azure que contém as máquinas virtuais e localize o novo equilibrador de carga. Talvez seja necessário refrescar a vista sobre o grupo de recursos. Selecione o equilibrador de carga.

1. Selecione **piscinas backend**e, em seguida, selecione **Adicionar**.

1. Associe o pool de backend com o conjunto de disponibilidade que contém os VMs.

1. Sob **as configurações IP**da rede Target, selecione VIRTUAL **MACHINE** e escolha as máquinas virtuais que irão participar como nós de cluster. Certifique-se de incluir todas as máquinas virtuais que irão acolher o FCI.

1. Selecione **OK** para criar a piscina de backend.

### <a name="configure-a-load-balancer-health-probe"></a>Configurar uma sonda de estado de funcionamento do balanceador de carga

1. Na lâmina do equilíbrio de carga, selecione **sondas de saúde**.

1. Selecione **Adicionar**.

1. Na lâmina da sonda <a name="probe"> </a>de **saúde Adicionar,** defina os parâmetros da sonda de saúde.

   - **Nome**: Um nome para a sonda de saúde.
   - **Protocolo**: TCP.
   - **Porta**: Instale na porta que criou na firewall para a sonda de saúde [neste passo](#ports). Neste artigo, o exemplo utiliza `59999`a porta TCP .
   - **Intervalo:** 5 segundos.
   - **Limiar pouco saudável:** 2 falhas consecutivas.

1. Selecione **OK**.

### <a name="set-load-balancing-rules"></a>Definir regras de equilíbrio de carga

1. Na lâmina do equilíbrio de carga, selecione Regras de **equilíbrio de carga**.

1. Selecione **Adicionar**.

1. Definir os parâmetros da regra de equilíbrio da carga:

   - **Nome**: Um nome para as regras de equilíbrio de carga.
   - **Endereço IP frontend**: O endereço IP para o recurso de rede de cluster SQL Server FCI.
   - **Porta**: Porta SQL Server FCI TCP. A porta de instância padrão é 1433.
   - **Porta de backend**: Utiliza a mesma porta que o valor da **porta** quando ativa o **IP flutuante (devolução do servidor direto)**.
   - **Piscina de backend**: O nome da piscina de backend que configurau anteriormente.
   - **Sonda de saúde**: A sonda de saúde que configuraste anteriormente.
   - **Persistência da sessão**: Nenhuma.
   - **Tempo limite (minutos)**: 4.
   - **IP flutuante (devolução do servidor direto)**: Ativado.

1. Selecione **OK**.

## <a name="step-6-configure-the-cluster-for-the-probe"></a>Passo 6: Configure o cluster para a sonda

Defina o parâmetro da porta da sonda cluster no PowerShell.

Para definir o parâmetro da porta da sonda cluster, atualize as variáveis no seguinte script com valores do seu ambiente. Retire os suportes`<` `>`angulares (e) do script.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

A lista que se segue descreve os valores que precisa de atualizar:

   - `<Cluster Network Name>`: O nome do Cluster Failover do Windows Server para a rede. Nas > **Redes**de Gestor de **Cluster Failover,** clique à direita na rede e selecione **Propriedades**. O valor correto está em **nome** no separador **Geral.**

   - `<SQL Server FCI IP Address Resource Name>`: O nome do recurso de endereço IP do Servidor SQL FCI. Nas > **funções**de Gestor de **Cluster failover,** sob a função SQL Server FCI, sob **o nome do servidor,** clique no recurso de endereço IP e selecione **Propriedades**. O valor correto está em **nome** no separador **Geral.** 

   - `<ILBIP>`: O endereço IP ILB. Este endereço está configurado no portal Azure como o endereço frontal ILB. Este é também o endereço IP Do Servidor SQL FCI. Pode encontrá-lo no **Failover Cluster Manager** na mesma `<SQL Server FCI IP Address Resource Name>`página de propriedades onde localizou o .  

   - `<nnnnn>`: A porta de sonda que configurana na sonda de saúde do equilibrador de carga. Qualquer porta TCP não utilizada é válida.

>[!IMPORTANT]
>A máscara de sub-rede para o parâmetro do `255.255.255.255`cluster deve ser o endereço de transmissão IP do TCP: .

Depois de definir a sonda de cluster, pode ver todos os parâmetros do cluster no PowerShell. Execute este guião:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Passo 7: Falha do Teste FCI

Teste falha do FCI para validar a funcionalidade do cluster. Siga estes passos:

1. Ligue-se a um dos nós de cluster SQL Server FCI utilizando RDP.

1. Open **Failover Cluster Manager**. Selecione **Funções**. Note qual nó detém o papel SQL Server FCI.

1. Clique na função SQL Server FCI.

1. Selecione **Mover**e, em seguida, selecione **o Melhor Nó Possível**.

**O Failover Cluster Manager** mostra o papel, e os seus recursos ficam offline. Os recursos movem-se e vêm online no outro nó.

### <a name="test-connectivity"></a>Testar conectividade

Para testar a conectividade, inscreva-se noutra máquina virtual na mesma rede virtual. Abra o **Estúdio de Gestão de Servidores SQL** e ligue-se ao nome SQL Server FCI.

>[!NOTE]
>Se precisar, pode baixar o Estúdio de Gestão de [Servidores SQL](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Limitações

As máquinas virtuais Azure suportam o Microsoft Distributed Transaction Coordinator (MSDTC) no Windows Server 2019 com armazenamento em Volumes Partilhados Clustered (CSV) e um [equilíbrio de carga padrão.](../../../load-balancer/load-balancer-standard-overview.md)

Nas máquinas virtuais Azure, o MSDTC não é suportado no Windows Server 2016 ou mais cedo porque:

- O recurso MSDTC agrupado não pode ser configurado para usar armazenamento partilhado. No Windows Server 2016, se criar um recurso MSDTC, não mostrará qualquer armazenamento partilhado disponível para utilização, mesmo que o armazenamento esteja disponível. Este problema foi corrigido no Windows Server 2019.
- O equilibrador de carga básico não trata das portas RPC.

## <a name="see-also"></a>Consulte também

[Configurar espaços de armazenamento diretamente com ambiente de trabalho remoto (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Solução hiperconvergente com Espaços de Armazenamento Direto](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[Descrição Geral dos Espaços de Armazenamento Direto](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Suporte do Servidor SQL para espaços de armazenamento direto](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
