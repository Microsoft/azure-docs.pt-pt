---
title: SQL Server FCI em Azure Virtual Machines
description: Este artigo explica como criar uma instância de cluster de failover do SQL Server (FCI) em Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
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
ms.openlocfilehash: 0333088c1b373fcf41fdab3eb4b4a9ec2c2d8eb5
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669057"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Configure uma instância de cluster de failover do SQL Server em Máquinas virtuais Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo explica como criar uma instância de cluster failover (FCI) do SQL Server em Azure Virtual Machines no modelo Azure Resource Manager. Esta solução utiliza [o Windows Server 2016 Datacenter edição De armazenamento Espaços de Armazenamento Direto](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) como um SAN virtual baseado em software que sincroniza o armazenamento (discos de dados) entre os nós (VMs Azure) num cluster Windows. O Storage Spaces Direct foi novo no Windows Server 2016.

O seguinte diagrama mostra a solução completa em Azure Virtual Machines:

![A solução completa](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

Este diagrama mostra:

- Duas máquinas virtuais num Cluster de Falha do Servidor do Windows. Quando uma máquina virtual está num aglomerado de falhas, também é chamada de nó de *cluster* ou *nó.*
- Cada máquina virtual tem dois ou mais discos de dados.
- Espaços de Armazenamento O Direct sincroniza os dados dos discos de dados e apresenta o armazenamento sincronizado como um conjunto de armazenamento.
- A piscina de armazenamento apresenta um Cluster Shared Volume (CSV) para o cluster failover.
- A função de cluster SQL Server FCI utiliza o CSV para as unidades de dados.
- Um balançador de carga Azure para manter o endereço IP para o SQL Server FCI.
- Um conjunto de disponibilidade Azure contém todos os recursos.

>[!NOTE]
>Todos os recursos Azure no diagrama estão no mesmo grupo de recursos.

Para mais informações sobre os espaços de armazenamento direto, consulte [o Windows Server 2016 Datacenter edição De armazenamento Espaços de Armazenamento Direto](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

Espaços de Armazenamento Direct suporta dois tipos de arquiteturas: convergentes e hiper-convergentes. A arquitetura neste documento é hiper-convergente. Uma infraestrutura hiper-convergente coloca o armazenamento nos mesmos servidores que acolhem a aplicação agrupada. Nesta arquitetura, o armazenamento está em cada nó SQL Server FCI.

## <a name="licensing-and-pricing"></a>Licenciamento e preços

Nas Máquinas Virtuais Azure, pode licenciar o SQL Server utilizando imagens VM pay-as-you-go (PAYG) ou "bring-your-your-own-license" (BYOL). O tipo de imagem que escolhes afeta a forma como és cobrado.

Com o licenciamento pay-as-you-go, uma instância de cluster failover (FCI) do SQL Server em Azure Virtual Machines incorre em taxas para todos os nós da FCI, incluindo os nós passivos. Para obter mais informações, consulte o [preço das máquinas virtuais da empresa SQL.](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)

Se tiver o Acordo Empresarial com a Garantia de Software, pode utilizar um nó FCI passivo gratuito para cada nó ativo. Para tirar partido deste benefício em Azure, utilize imagens BYOL VM e use a mesma licença nos nós ativos e passivos da FCI. Para mais informações, consulte [o Acordo de Empresa.](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)

Para comparar o licenciamento pay-as-you-go e BYOL para O Servidor SQL em Máquinas Virtuais Azure, consulte [Começar com VMs do SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms).

Para obter informações completas sobre o licenciamento do SQL Server, consulte [o Preço](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Modelo de Azure de exemplo

Você pode criar toda esta solução em Azure a partir de um modelo. Um exemplo de um modelo está disponível nos modelos GitHub [Azure Quickstart.](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) Este exemplo não é projetado ou testado para qualquer carga de trabalho específica. Pode executar o modelo para criar um SQL Server FCI com espaços de armazenamento Armazenamento Armazenamento Direto ligado ao seu domínio. Pode avaliar o modelo e modificá-lo para os seus propósitos.

## <a name="before-you-begin"></a>Before you begin

Há algumas coisas que precisa saber e ter no lugar antes de começar.

### <a name="what-to-know"></a>O que saber

Deve ter uma compreensão operacional destas tecnologias:

- [Tecnologias de cluster windows](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server Failover Cluster Instances](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Uma coisa a ter em conta é que num cluster de failover de hóspedes Azure IaaS VM, recomendamos um único NIC por servidor (nó de cluster) e uma única sub-rede. O azure networking tem redundância física, o que torna NICs adicionais e sub-redes desnecessários em um cluster de hóspedes Azure IaaS VM. O relatório de validação do cluster irá avisá-lo de que os nós são alcançáveis apenas numa única rede. Você pode ignorar este aviso em Azure IaaS VM clusters de failover de hóspedes.

Deve também ter uma compreensão geral destas tecnologias:

- [Soluções hiperconversadas que utilizam espaços de armazenamento direto no Windows Server 2016](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Grupos de recursos do Azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Neste momento, as instâncias de cluster failover do SQL Server nas Máquinas Virtuais Azure só são suportadas com o modo de [gestão leve](sql-vm-resource-provider-register.md#management-modes) da Extensão do [Agente IAAS](sql-server-iaas-agent-extension-automate-management.md)do SqL Server . Para mudar do modo de extensão total para leve, elimine o recurso **SQL Virtual Machine** para os VMs correspondentes e registe-os com o fornecedor de recursos SQL VM em modo leve. Ao eliminar o recurso **SQL Virtual Machine** utilizando o portal Azure, **limpe a caixa de verificação ao lado da Máquina Virtual correta**. A extensão completa suporta funcionalidades como backup automatizado, patching e gestão avançada do portal. Estas funcionalidades não funcionarão para VMS SQL após a reinstalação do agente em modo de gestão leve.
> 

### <a name="what-to-have"></a>O que ter

Antes de completar os passos deste artigo, já deve ter:

- Uma subscrição do Microsoft Azure
- Um domínio Windows em Azure Virtual Machines
- Uma conta que tem permissões para criar objetos tanto em máquinas virtuais como no Ative Directory
- Uma rede virtual Azure e uma sub-rede com espaço de endereço IP suficiente para estes componentes:
   - Ambas as máquinas virtuais
   - O endereço IP do cluster de failover
   - Um endereço IP para cada FCI
- DNS configurado na rede Azure, apontando para os controladores de domínio

Com estes pré-requisitos no lugar, pode começar a construir o seu aglomerado de falhanços. O primeiro passo é criar as máquinas virtuais.

## <a name="step-1-create-the-virtual-machines"></a>Passo 1: Criar as máquinas virtuais

1. Inscreva-se no [portal Azure](https://portal.azure.com) com a sua subscrição.

1. [Crie um conjunto de disponibilidade Azure](../../../virtual-machines/linux/tutorial-availability-sets.md).

   O conjunto de disponibilidadesgrupam máquinas virtuais em domínios de avaria e domínios de atualização. Garante que a sua aplicação não é afetada por pontos únicos de falha, como o interruptor de rede ou a unidade de alimentação de um rack de servidores.

   Se não criou o grupo de recursos para as suas máquinas virtuais, faça-o quando criar um conjunto de disponibilidades Azure. Se estiver a utilizar o portal Azure para criar o conjunto de disponibilidades, tome estes passos:

   1. No portal Azure, selecione **Criar um recurso** para abrir o Mercado Azure. Procurar **conjunto de Disponibilidade**.
   1. Selecione **Conjunto de Disponibilidade**.
   1. Selecione **Criar**.
   1. No **Conjunto de disponibilidades Create,** forneça estes valores:
      - **Nome**: Um nome para o conjunto de disponibilidade.
      - **Subscrição**: A sua subscrição Azure.
      - **Grupo de recursos**: Se pretender utilizar um grupo existente, clique em **Selecionar o** grupo existente na lista. Caso contrário, **selecione Criar novo** e insira um nome para o grupo.
      - **Localização**: Desajei a localização onde planeia criar as suas máquinas virtuais.
      - **Domínios de avaria :** Utilize o padrão **(3**).
      - **Domínios de atualização**: Utilize o padrão **(5**).
   1. Selecione **Criar** para criar o conjunto de disponibilidade.

1. Crie as máquinas virtuais no conjunto de disponibilidade.

   Provisões duas máquinas virtuais SQL Server no conjunto de disponibilidade do Azure. Para obter instruções, consulte [a Provision a sql Server virtual machine no portal Azure](create-sql-vm-portal.md).

   Coloque ambas as máquinas virtuais:

   - No mesmo grupo de recursos Azure que o seu conjunto de disponibilidade
   - Na mesma rede que o seu controlador de domínio
   - Numa sub-rede que tem espaço suficiente para endereços IP tanto para máquinas virtuais como para todas as FCIs que poderá eventualmente utilizar no cluster
   - No conjunto de disponibilidade do Azure

      >[!IMPORTANT]
      >Não é possível definir ou alterar o conjunto de disponibilidade depois de ter criado uma máquina virtual.
      >

   Escolha uma imagem do Azure Marketplace. Pode utilizar uma imagem do Azure Marketplace que inclua o Windows Server e o SQL Server, ou utilizar uma que apenas inclui o Windows Server. Para mais detalhes, consulte [a visão geral do SQL Server em Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).

   As imagens oficiais do SQL Server na Galeria Azure incluem uma instância de servidor SQL instalada, o software de instalação do SQL Server e a chave necessária.

   Escolha a imagem certa, com base na forma como pretende pagar a licença SQL Server:

   - **Licenciamento pay-per-use**. O custo por segundo destas imagens inclui o licenciamento do SQL Server:
      - **SQL Server 2016 Enterprise no Windows Server 2016 Datacenter**
      - **SQL Server 2016 Standard no Windows Server 2016 Datacenter**
      - **Desenvolvedor do SQL Server 2016 no Centro de Dados do Windows Server 2016**

   - **Trazer a sua própria licença (BYOL)**

      - **(BYOL) SQL Server 2016 Enterprise no Windows Server 2016 Datacenter**
      - **(BYOL) SQL Server 2016 Standard no Windows Server 2016 Datacenter**

   >[!IMPORTANT]
   >Depois de criar a máquina virtual, remova a instância sql server pré-instalada. Utilizará o meio de servidor SQL pré-instalado para criar o SQL Server FCI depois de configurar o cluster de failover e os espaços de armazenamento diretos.
   >

   Em alternativa, pode utilizar imagens do Azure Marketplace que contenham apenas o sistema operativo. Escolha uma imagem **do Datacenter 2016** do Windows Server e instale o SQL Server FCI depois de configurar o cluster de failover e os espaços de armazenamento diretos. Esta imagem não contém meios de instalação SQL Server. Coloque o meio de instalação do SQL Server num local onde possa executá-lo para cada servidor.

1. Depois de o Azure criar as suas máquinas virtuais, ligue-se a cada uma delas utilizando o protocolo de ambiente de trabalho remoto (RDP).

   Quando se liga a uma máquina virtual utilizando RDP, um pedido pergunta-lhe se pretende permitir que o PC seja detetável na rede. Selecione **Sim**.

1. Se estiver a utilizar uma das imagens de máquinas virtuais baseadas no SQL Server, remova a instância do SQL Server.

   1. Em **Programas e Funcionalidades,** clique com o botão direito **Microsoft SQL Server 2016 (64-bit)** e selecione **Desinstalar/Alterar**.
   1. Selecione **Remover**.
   1. Selecione a instância padrão.
   1. Remova todas as funcionalidades nos **serviços de motores de base de dados.** Não remova **funcionalidades partilhadas.** Verá algo como a seguinte imagem:

      ![Selecione funcionalidades](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/03-remove-features.png)

   1. Selecione **Seguinte**e, em seguida, selecione **Remover**.

1. <a name="ports"></a>Abra as portas de firewall.

   Em cada máquina virtual, abra estas portas no Windows Firewall:

   | Objetivo | Porta TCP | Notas
   | ------ | ------ | ------
   | SQL Server | 1433 | Porta normal para instâncias padrão do SQL Server. Se utilizar uma imagem da galeria, esta porta é aberta automaticamente.
   | Sonda de estado de funcionamento | 59999 | Qualquer porta TCP aberta. Em um passo posterior, configurar a [sonda de saúde](#probe) do balanceador de carga e o cluster para usar esta porta.  

1. Adicione armazenamento à máquina virtual. Para obter informações detalhadas, consulte [o armazenamento.](../../../virtual-machines/linux/disks-types.md)

   Ambas as máquinas virtuais precisam de pelo menos dois discos de dados.

   Prenda discos crus, não discos formatados com NTFS.

      >[!NOTE]
      >Se ligar discos formatados em NTFS, pode ativar os espaços de armazenamento direto apenas sem uma verificação de elegibilidade do disco. 
      > 

   Anexar um mínimo de dois SSDs premium a cada VM. Recomendamos pelo menos discos P30 (1-TB).

   Definir o anfitrião caching para **Read-only**.

   A capacidade de armazenamento que utiliza em ambientes de produção depende da sua carga de trabalho. Os valores descritos neste artigo são para demonstração e teste.

1. [Adicione as máquinas virtuais ao seu domínio pré-existente](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain).

Depois de criar e configurar as máquinas virtuais, pode configurar o cluster de failover.

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>Passo 2: Configurar o cluster de failover do servidor do Windows com espaços de armazenamento direto

Agora configura o cluster de failover com espaços de armazenamento direto. Nesta secção, completa estes passos:

1. Adicione a função de clustering de falha do servidor do Windows.
1. Valide o cluster.
1. Crie o aglomerado de falhas.
1. Criar a testemunha da nuvem.
1. Adicione armazenamento.

### <a name="add-windows-server-failover-clustering"></a>Adicionar cluster de failover do servidor do Windows

1. Ligue-se à primeira máquina virtual com RDP utilizando uma conta de domínio que é membro dos administradores locais e que tem permissão para criar objetos no Ative Directory. Utilize esta conta para o resto da configuração.

1. [Adicione o Cluster de Failover a cada máquina virtual.](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms)

   Para instalar o Failover Clustering a partir da UI, tome estes passos em ambas as máquinas virtuais:

   1. No **Gestor do Servidor**, selecione **Gerir**e, em seguida, selecione **Adicionar Funções e Funcionalidades**.
   1. No **'Add Roles and Features Wizard',** selecione **Next** até obter **funcionalidades selecionadas**.
   1. Em **Funcionalidades Selecionadas**, selecione **Clustering Failover**. Inclua todas as funcionalidades necessárias e as ferramentas de gestão. Selecione **adicionar funcionalidades**.
   1. Selecione **Seguinte**e, em seguida, selecione **Finish** para instalar as funcionalidades.

   Para instalar o Clustering Failover utilizando o PowerShell, execute o seguinte script a partir de uma sessão PowerShell de administrador numa das máquinas virtuais:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Para obter mais referências sobre os próximos passos, consulte as instruções no passo 3 da [solução hiperconversada utilizando espaços de armazenamento direto no Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Validar o cluster

Valide o cluster na UI ou utilizando o PowerShell.

Para validar o cluster utilizando a UI, tome os seguintes passos numa das máquinas virtuais:

1. Sob **o Gestor do Servidor**, selecione **Ferramentas**e, em seguida, selecione **O Gestor de Cluster Failover**.
1. Em **'Failover Cluster Manager',** selecione **Ação**e, em seguida, selecione **Validate Configuration**.
1. Selecione **Seguinte**.
1. Em **Servidores Selecionados ou num Cluster,** insira os nomes de ambas as máquinas virtuais.
1. Nas **opções de Teste,** selecione **Executar apenas testes que seleciono**. Selecione **Seguinte**.
1. Em **Seleção de Testes**, selecione todos os testes, exceto **para armazenamento,** como mostrado aqui:

   ![Selecione testes de validação de clusters](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. Selecione **Seguinte**.
1. Em **Confirmação**, selecione **Seguinte**.

O Assistente de Validação de Um Assistente de Configuração executa os testes de validação.

Para validar o cluster utilizando o PowerShell, execute o seguinte script a partir de uma sessão PowerShell de administrador numa das máquinas virtuais:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Depois de validar o cluster, crie o cluster de failover.

### <a name="create-the-failover-cluster"></a>Criar o cluster de ativação pós-falha

Para criar o cluster de failover, você precisa:

- Os nomes das máquinas virtuais que se tornarão os nóns de cluster
- Um nome para o cluster failover
- Um endereço IP para o cluster de failover <br/>
  Pode utilizar um endereço IP que não seja utilizado na mesma rede virtual Azure e sub-rede que os nós de cluster.

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows Server 2008 através do Windows Server 2016

O seguinte script PowerShell cria um cluster de falha para o Windows Server 2008 através do Windows Server 2016. Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível a partir da rede virtual Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

O seguinte script PowerShell cria um cluster de falha para o Windows Server 2019. Para obter mais informações, consulte [o cluster Failover: Cluster Network Object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível a partir da rede virtual Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Criar uma testemunha em nuvem

Cloud Witness é um novo tipo de testemunha de quórum que está guardada numa bolha de armazenamento Azure. Isto elimina a necessidade de um VM separado que acolhe uma parte de testemunhas.

1. [Crie uma testemunha em nuvem para o aglomerado de falhas.](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)

1. Crie um recipiente para bolhas.

1. Guarde as chaves de acesso e o URL do recipiente.

1. Configure a testemunha do quórum do cluster failover. Consulte [a configuração da testemunha do quórum na interface do utilizador](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

### <a name="add-storage"></a>Adicionar armazenamento

Os discos para espaços de armazenamento diretos têm de estar vazios. Não podem conter divisórias ou outros dados. Para limpar os discos, siga [os passos deste guia.](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives)

1. [Ativar os espaços da loja direto.](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct)

   O seguinte script PowerShell permite espaços de armazenamento direto:  

   ```powershell
   Enable-ClusterS2D
   ```

   No **Failover Cluster Manager,** já pode ver a piscina de armazenamento.

1. [Criar um volume](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Espaços de Armazenamento O Direct cria automaticamente uma piscina de armazenamento quando o ativa. Agora estás pronto para criar um volume. O cmdlet PowerShell `New-Volume` automatiza o processo de criação de volume. Este processo inclui formatação, adicionar o volume ao cluster e criar um Cluster Shared Volume (CSV). Este exemplo cria um CSV de 800 gigabytes (GB):

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Após a conclusão deste comando, um volume de 800 GB é montado como recurso de cluster. O volume está em `C:\ClusterStorage\Volume1\` .

   Esta imagem mostra um volume compartilhado de cluster com espaços de armazenamento direto:

   ![Cluster Volume Compartilhado](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Passo 3: Teste falha no cluster de failover

No **Failover Cluster Manager,** verifique se pode mover o recurso de armazenamento para o outro nó de cluster. Se conseguir ligar-se ao cluster failover utilizando o **Failover Cluster Manager** e mover o armazenamento de um nó para o outro, está pronto para configurar o FCI.

## <a name="step-4-create-the-sql-server-fci"></a>Passo 4: Criar o SQL Server FCI

Depois de configurar o cluster failover e todos os componentes do cluster, incluindo o armazenamento, pode criar o SQL Server FCI.

1. Ligue-se à primeira máquina virtual utilizando RDP.

1. No **Failover Cluster Manager,** certifique-se de que todos os Recursos de Cluster Core estão na primeira máquina virtual. Se necessário, mova todos os recursos para a máquina virtual.

1. Localize os meios de instalação. Se a máquina virtual utilizar uma das imagens do Azure Marketplace, os meios de comunicação estão localizados em `C:\SQLServer_<version number>_Full` . Selecione **Configuração**.

1. No **Centro de Instalação do Servidor SQL,** selecione **Instalação**.

1. Selecione **a instalação de cluster de falha do novo sql server**. Siga as instruções do assistente para instalar o SQL Server FCI.

   Os diretórios de dados da FCI têm de estar no armazenamento agrupado. Com os Espaços de Armazenamento Direto, não é um disco partilhado, mas um ponto de montagem para um volume em cada servidor. Espaços de Armazenamento Sincronizam diretamente o volume entre os dois nós. O volume é apresentado ao cluster como um Cluster Shared Volume. Utilize o ponto de montagem CSV para os diretórios de dados.

   ![Diretórios de dados](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. Depois de completar as instruções no assistente, a Configuração instalará um SQL Server FCI no primeiro nó.

1. Depois de configurar a INSTALAÇÃO instala o FCI no primeiro nó, ligue-o ao segundo nó utilizando RDP.

1. Abra o **Centro de Instalação do Servidor SQL**. Selecione **instalação**.

1. **Selecione Adicionar nó a um cluster de falha do sql server**. Siga as instruções do assistente para instalar o SQL Server e adicione o servidor à FCI.

   >[!NOTE]
   >Se usou uma imagem de galeria do Azure Marketplace que contém SQL Server, as ferramentas sql Server foram incluídas com a imagem. Se não usou uma dessas imagens, instale as ferramentas SQL Server separadamente. Consulte [o Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).
   >

## <a name="step-5-create-the-azure-load-balancer"></a>Passo 5: Criar o equilibrador de carga Azure

Nas Máquinas Virtuais Azure, os clusters usam um equilibrador de carga para conter um endereço IP que precisa de estar num nó de cluster de cada vez. Nesta solução, o equilibrador de carga detém o endereço IP para o SQL Server FCI.

Para obter mais informações, consulte [Criar e configurar um equilibrador de carga Azure](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Criar o equilibrador de carga no portal Azure

Para criar o equilibrador de carga:

1. No portal Azure, vá ao grupo de recursos que contém as máquinas virtuais.

1. Selecione **Adicionar**. Procure no Mercado Azure para **o Balanceador de Carga.** Selecione **o balanceador de carga**.

1. Selecione **Criar**.

1. Configure o balançador de carga com:

   - **Subscrição**: A sua subscrição Azure.
   - **Grupo de recursos**: O grupo de recursos que contém as suas máquinas virtuais.
   - **Nome**: Um nome que identifica o esquilibrador de carga.
   - **Região**: A localização azul que contém as suas máquinas virtuais.
   - **Tipo**: Público ou privado. Um equilibrador de carga privado pode ser acedido a partir da rede virtual. A maioria das aplicações Azure pode usar um equilibrador de carga privado. Se a sua aplicação necessitar de acesso ao SQL Server diretamente através da internet, utilize um equilibrador de carga público.
   - **SKU**: Padrão.
   - **Rede virtual**: A mesma rede que as máquinas virtuais.
   - **Atribuição de endereço IP**: Estática. 
   - **Endereço IP privado**: O endereço IP que atribuiu ao recurso de rede de cluster SQL Server FCI.

 A imagem a seguir mostra o **UI do balançador de carga Create:**

   ![Configurar o equilibrador de carga](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Configure a piscina de backend do balançador de carga

1. Volte ao grupo de recursos Azure que contém as máquinas virtuais e localize o novo equilibrador de carga. Pode ser necessário refrescar a vista sobre o grupo de recursos. Selecione o equilibrador de carga.

1. Selecione **piscinas backend**e, em seguida, selecione **Adicionar**.

1. Associe o pool de backend com o conjunto de disponibilidade que contém os VMs.

1. Nas **configurações IP da rede Target**, selecione VIRTUAL **MACHINE** e escolha as máquinas virtuais que participarão como nós de cluster. Certifique-se de incluir todas as máquinas virtuais que irão acolher o FCI.

1. Selecione **OK** para criar a piscina de backend.

### <a name="configure-a-load-balancer-health-probe"></a>Configurar uma sonda de estado de funcionamento do balanceador de carga

1. Na lâmina do balançador de carga, selecione **sondas de saúde**.

1. Selecione **Adicionar**.

1. Na lâmina da **sonda de saúde Add,** <a name="probe"></a> desa estale os parâmetros da sonda de saúde.

   - **Nome**: Um nome para a sonda de saúde
   - **Protocolo**: TCP
   - **Porto**: Definir para o porto que criou na firewall para a sonda de saúde [neste passo](#ports) <br/>Neste artigo, o exemplo utiliza a porta `59999` TCP.
   - **Intervalo:** 5 Segundos.
   - **Limiar pouco saudável**: 2 falhas consecutivas

1. Selecione **OK**.

### <a name="set-load-balancing-rules"></a>Definir regras de equilíbrio de carga

1. Na lâmina do balançador de carga, selecione **regras de equilíbrio de carga**.

1. Selecione **Adicionar**.

1. Definir os parâmetros da regra de equilíbrio de carga:

   - **Nome**: Um nome para as regras de equilíbrio de carga.
   - **Endereço IP frontend**: O endereço IP para o recurso de rede de cluster SQL Server FCI.
   - **Porta**: A porta TCP SQL Server FCI. A porta de instância padrão é 1433.
   - **Porta de backend**: Utiliza a mesma porta que o valor da **porta** quando ativa o **IP flutuante (retorno direto do servidor)**.
   - **Piscina de backend**: O nome da piscina de backend que configuraste anteriormente.
   - **Sonda de saúde**: A sonda de saúde que configuraste anteriormente.
   - **Persistência da sessão**: Nenhuma.
   - **Tempo de 20 minutos ( minutos):** 4.
   - **IP flutuante (retorno do servidor direto)**: Ativado.

1. Selecione **OK**.

## <a name="step-6-configure-the-cluster-for-the-probe"></a>Passo 6: Configurar o cluster para a sonda

Desa estale o parâmetro da porta da sonda de cluster no PowerShell.

Para definir o parâmetro da porta da sonda de cluster, atualize as variáveis no seguinte script com valores do seu ambiente. Retire os suportes angulares `<` `>` (e) do script.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

A lista que se segue descreve os valores que precisa de atualizar:

   - `<Cluster Network Name>`: O nome do Cluster failover do servidor do Windows para a rede. Nas Redes **de Gestores de Cluster Failover,**  >  **Networks**clique à direita na rede e selecione **Propriedades.** O valor correto está no **nome** no separador **Geral.**

   - `<SQL Server FCI IP Address Resource Name>`: O nome do endereço IP do SQL Server FCI. Em **Funções de Gestor de Cluster Failover**  >  **Roles**, sob a função SQL Server FCI, em Nome **do Servidor,** clique com o botão direito no recurso de endereço IP e selecione **Propriedades**. O valor correto está no **nome** no separador **Geral.** 

   - `<ILBIP>`: O endereço IP ILB. Este endereço está configurado no portal Azure como o endereço frontal ILB. Este é também o endereço IP SQL Server FCI. Pode encontrá-lo no **Failover Cluster Manager** na mesma página de propriedades onde localiza o `<SQL Server FCI IP Address Resource Name>` .  

   - `<nnnnn>`: A porta da sonda configurada na sonda de saúde do balanceador de carga. Qualquer porta TCP nãousada é válida.

>[!IMPORTANT]
>A máscara de sub-rede para o parâmetro de cluster deve ser o endereço de transmissão IP TCP: `255.255.255.255` .
>

Depois de definir a sonda de cluster, pode ver todos os parâmetros de cluster no PowerShell. Executar este script:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Passo 7: Teste fci failover

Teste de falha da FCI para validar a funcionalidade do cluster. Siga estes passos:

1. Ligue-se a um dos nós de cluster SQL Server FCI utilizando RDP.

1. Open **Failover Cluster Manager**. Selecione **Funções**. Note qual o nó que detém a função SQL Server FCI.

1. Clique com o botão direito no papel SQL Server FCI.

1. Selecione **Mover**e, em seguida, selecione **O Melhor Nó Possível**.

**O Failover Cluster Manager** mostra o papel e os seus recursos offline. Os recursos movem-se e entram online no outro nó.

### <a name="test-connectivity"></a>Testar conectividade

Para testar a conectividade, inscreva-se noutra máquina virtual na mesma rede virtual. Abra **o SQL Server Management Studio** e ligue-se ao nome FCI do SQL Server.

>[!NOTE]
>Se precisar, pode [baixar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
>

## <a name="limitations"></a>Limitações

As Máquinas Virtuais Azure suportam o Coordenador de Transações Distribuídas da Microsoft (MSDTC) no Windows Server 2019 com armazenamento em Volumes Compartilhados Agrupados (CSV) e um [balanceador de carga padrão](../../../load-balancer/load-balancer-standard-overview.md).

Nas Máquinas Virtuais Azure, o MSDTC não é suportado no Windows Server 2016 ou mais cedo porque:

- O recurso MSDTC agrupado não pode ser configurado para usar armazenamento partilhado. No Windows Server 2016, se criar um recurso MSDTC, não apresentará qualquer armazenamento partilhado disponível para utilização, mesmo que o armazenamento esteja disponível. Este problema foi corrigido no Windows Server 2019.
- O equilibrador de carga básico não lida com portas RPC.

## <a name="see-also"></a>Consulte também

[Configurar espaços de armazenamento direto com ambiente de trabalho remoto (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Solução hiperconverente com espaços de armazenamento direto](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[Descrição Geral dos Espaços de Armazenamento Direto](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Suporte do Servidor SQL para espaços de armazenamento direto](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
