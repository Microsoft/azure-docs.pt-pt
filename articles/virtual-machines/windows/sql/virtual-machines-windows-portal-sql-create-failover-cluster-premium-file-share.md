---
title: SQL Server FCI com partilha de ficheiropremium - Máquinas Virtuais Azure
description: Este artigo explica como criar uma instância de cluster de falha do SQL Server utilizando uma partilha de ficheiro premium em máquinas virtuais Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/09/2019
ms.author: mathoma
ms.openlocfilehash: 9595ee87801fa4ce187a50197fc58d6c448eac24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303227"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Configure uma instância de cluster de failover do SQL Server com partilha de ficheiropremium em máquinas virtuais Azure

Este artigo explica como criar uma instância de cluster de failover do SQL Server (FCI) em máquinas virtuais Azure utilizando uma partilha de [ficheiro premium](../../../storage/files/storage-how-to-create-premium-fileshare.md).

As ações de ficheiropremium são ações de ficheiros apoiadas por SSD, consistentemente de baixa latência, que são totalmente suportadas para utilização com instâncias de cluster Failover para SQL Server 2012 ou posteriormente no Windows Server 2012 ou posteriormente. As ações de ficheiropremium dão-lhe maior flexibilidade, permitindo-lhe redimensionar e escalar uma parte de ficheiro sem qualquer tempo de inatividade.


## <a name="before-you-begin"></a>Antes de começar

Há algumas coisas que precisa saber e ter no lugar antes de começar.

Deve ter uma compreensão operacional destas tecnologias:

- [Tecnologias de cluster windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Casos de cluster de falha do servidor SQL](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Uma coisa a ter em conta é que num cluster de falha de VM Azure IaaS, recomendamos um único NIC por servidor (nó de cluster) e uma única sub-rede. A rede Azure tem redundância física que torna os NICs e subnets adicionais desnecessários num aglomerado de hóspedes Azure IaaS VM. O relatório de validação do cluster irá avisá-lo de que os nós são acessíveis apenas numa única rede. Pode ignorar este aviso sobre os clusters de falha do Azure IaaS VM.

Deve também ter uma compreensão geral destas tecnologias:

- [Partilha de ficheiros premium Azure](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Grupos de recursos azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Neste momento, as instâncias de cluster de falha do SQL Server nas máquinas virtuais Azure são suportadas apenas com o modo de [gestão leve](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) da extensão do [agente SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Para mudar do modo de extensão completo para leve, elimine o recurso **SQL Virtual Machine** para os VMs correspondentes e, em seguida, registe-os com o fornecedor de recursos SQL VM em modo leve. Ao apagar o recurso **SQL Virtual Machine** utilizando o portal Azure, limpe a caixa de **verificação junto à máquina virtual correta**. A extensão completa suporta funcionalidades como backup automatizado, patching e gestão avançada do portal. Estas funcionalidades não funcionarão para VMs SQL depois de o agente ser reinstalado em modo de gestão leve.

As ações de ficheiropremium fornecem IOPS e em todas as capacidades que irão atender às necessidades de muitas cargas de trabalho. Para cargas de trabalho intensivas em IO, considere [as instâncias de cluster de failover do Servidor SQL com espaços](virtual-machines-windows-portal-sql-create-failover-cluster.md)de armazenamento diretos, com base em discos premium geridos ou discos ultra.  

Verifique a atividade iops do seu ambiente e verifique se as ações de ficheiropremium fornecerão o IOPS de que necessita antes de iniciar uma implementação ou migração. Utilize os contadores de discos do Windows Performance Monitor para monitorizar o total de IOPS (Transferências de Discos/segundo) e a entrada (Bytes/segundo do Disco) necessárias para os ficheiros SQL Server, Log e Temp DB.

Muitas cargas de trabalho rebentaram iO, por isso é uma boa ideia verificar durante períodos de uso pesados e notar tanto o IOPS máximo como o IOPS médio. As ações de ficheiropremium fornecem IOPS com base no tamanho da ação. As ações de ficheiro premium também fornecem uma explosão gratuita que lhe permite rebentar o seu IO para triplicar o valor de base por até uma hora.

Para obter mais informações sobre o desempenho da partilha de ficheiros premium, consulte os níveis de [desempenho da partilha de ficheiros](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers).

### <a name="licensing-and-pricing"></a>Licenciamento e preços

Em máquinas virtuais Azure, pode licenciar o SQL Server utilizando imagens VM pay-as-you-go (PAYG) ou trazer as imagens VM da sua própria licença (BYOL). O tipo de imagem que escolhe saem da forma como é cobrado.

Com o licenciamento pay-as-you-go, uma instância de cluster failover (FCI) do SQL Server em máquinas virtuais Azure incorre em encargos para todos os nós do FCI, incluindo os nós passivos. Para mais informações, consulte o Preço das [Máquinas Virtuais da Empresa de Servidores SQL](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Se tiver acordo de empresa com garantia de software, pode utilizar um nó FCI passivo gratuito para cada nó ativo. Para aproveitar este benefício em Azure, utilize imagens BYOL VM e use a mesma licença tanto nos nós ativos como passivos do FCI. Para mais informações, consulte [o Acordo de Empresa.](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)

Para comparar o licenciamento pay-as-you-go e byoL para o SQL Server em máquinas virtuais Azure, consulte [Get started with SQL VMs](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Para obter informações completas sobre o licenciamento do Servidor SQL, consulte [O Preço](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="filestream"></a>Filestream

Filestream não é suportado para um cluster failover com uma quota de ficheiro premium. Para utilizar o filestream, implemente o seu cluster utilizando espaços de [armazenamento direto](virtual-machines-windows-portal-sql-create-failover-cluster.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de completar os passos deste artigo, já deve ter:

- Uma subscrição do Microsoft Azure.
- Um domínio Windows em máquinas virtuais Azure.
- Uma conta de utilizador de domínio que tem permissões para criar objetos tanto nas máquinas virtuais do Azure como no Diretório Ativo.
- Uma conta de utilizador de domínio para executar o serviço SQL Server e com a qual pode entrar na máquina virtual com a montagem da partilha de ficheiros.  
- Uma rede virtual Azure e uma subrede com espaço de endereço IP suficiente para estes componentes:
   - Duas máquinas virtuais.
   - O endereço IP do cluster failover.
   - Um endereço IP para cada FCI.
- DNS configurado na rede Azure, apontando para os controladores de domínio.
- Uma partilha de [ficheiro premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) a ser utilizada como unidade agrupada, com base na quota de armazenamento da sua base de dados para os seus ficheiros de dados.
- Se estiver no Windows Server 2012 R2 ou mais velho, precisará de outra partilha de ficheiros para usar como testemunha de partilha de ficheiros, uma vez que as testemunhas na nuvem são suportadas para o Windows 2016 e mais recentes. Pode utilizar outra partilha de ficheiros Azure, ou pode utilizar uma partilha de ficheiros numa máquina virtual separada. Se vai utilizar outra partilha de ficheiros Azure, pode montá-la com o mesmo processo que para a partilha de ficheiros premium utilizada para a sua unidade agrupada. 

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

   >[!IMPORTANT]
   > Depois de criar a máquina virtual, remova a instância de Servidor SQL autónoma pré-instalada. Utilizará os meios de comunicação SQL Server pré-instalados para criar o SQL Server FCI depois de configurar o cluster failover e a partilha de ficheiros premium como armazenamento.

   Em alternativa, pode utilizar imagens do Azure Marketplace que contenham apenas o sistema operativo. Escolha uma imagem **do Datacenter do Windows Server 2016** e instale o SQL Server FCI depois de configurar o cluster failover e a partilha de ficheiros premium como armazenamento. Esta imagem não contém meios de instalação do SQL Server. Coloque os meios de instalação do SQL Server num local onde possa executá-lo para cada servidor.

1. Depois do Azure criar as suas máquinas virtuais, ligue-se a cada uma utilizando RDP.

   Quando se conecta pela primeira vez a uma máquina virtual utilizando RDP, uma solicitação pergunta-lhe se pretende permitir que o PC seja detetável na rede. Selecione **Sim**.

1. Se estiver a utilizar uma das imagens da máquina virtual baseada no Servidor SQL, remova a instância do Servidor SQL.

   1. Em **Programas e Funcionalidades,** clique à direita **no Microsoft SQL Server 201_ (64-bits)** e selecione **Desinstalar/Alterar**.
   1. Selecione **Remover**.
   1. Selecione a instância predefinida.
   1. Remova todas as funcionalidades em **Serviços**de Motor de Base de Dados . Não remova **as funcionalidades partilhadas.** Verá algo como a seguinte imagem:

        ![Selecione Funcionalidades](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Selecione **Next**, e, em seguida, selecione **Remover**.

1. <span id="ports"> </span> Abra as portas de firewall.  

   Em cada máquina virtual, abra estas portas na Firewall do Windows:

   | Objetivo | Porta TCP | Notas
   | ------ | ------ | ------
   | SQL Server | 1433 | Porta normal para casos padrão do Servidor SQL. Se usou uma imagem da galeria, esta porta é aberta automaticamente.
   | Sonda de estado de funcionamento | 59999 | Qualquer porta TCP aberta. Num passo posterior, configure a sonda de [saúde](#probe) do equilibrador de carga e o cluster para utilizar esta porta.
   | Partilha de ficheiros | 445 | Porta utilizada pelo serviço de partilha de ficheiros.

1. [Adicione as máquinas virtuais ao seu domínio pré-existente](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Depois de criar e configurar as máquinas virtuais, pode configurar a parte de ficheiro premium.

## <a name="step-2-mount-the-premium-file-share"></a>Passo 2: Monte a parte do ficheiro premium

1. Inscreva-se no [portal Azure](https://portal.azure.com) e vá à sua conta de armazenamento.
1. Vá a **File Shares** sob o serviço **de Ficheiros** e selecione a parte de ficheiro premium que pretende utilizar para o seu armazenamento SQL.
1. Selecione **Connect** para elevar o fio de ligação para a sua parte de ficheiro.
1. Selecione a letra de unidade que pretende utilizar a partir da lista de lançamentos e, em seguida, copie ambos os blocos de código para O Bloco de Notas.


   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/premium-file-storage-commands.png" alt-text="Copie ambos os comandos PowerShell do portal de ligação de partilha de ficheiros":::

1. Utilize RDP para ligar ao VM do Servidor SQL com a conta que o seu SQL Server FCI utilizará para a conta de serviço.
1. Abra uma consola de comando powerShell administrativa.
1. Execute os comandos que guardou mais cedo quando estava a trabalhar no portal.
1. Aceda à partilha utilizando o File Explorer ou a caixa de diálogo **Executar** (tecla de logotipo do Windows + r). Utilize o `\\storageaccountname.file.core.windows.net\filesharename`caminho da rede . Por exemplo, `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Crie pelo menos uma pasta na partilha de ficheiros recentemente ligada para colocar os seus ficheiros SQL Data.
1. Repita estes passos em cada VM do Servidor SQL que participará no cluster.

  > [!IMPORTANT]
  > - Considere utilizar uma partilha de ficheiroseparada para ficheiros de backup para guardar o IOPS e a capacidade espacial desta partilha para ficheiros Data e Log. Pode utilizar uma partilha de ficheiros premium ou padrão para ficheiros de backup.
  > - Se estiver no Windows 2012 R2 ou mais velho, siga estes mesmos passos para montar a sua partilha de ficheiros que vai usar como testemunha de partilha de ficheiros. 

## <a name="step-3-configure-the-failover-cluster"></a>Passo 3: Configure o cluster failover

O próximo passo é configurar o cluster failover. Neste passo, completará os seguintes subpassos:

1. Adicione a função de Clustering de Falha do Servidor do Windows.
1. Validar o cluster.
1. Crie o cluster de falhas.
1. Crie a testemunha em nuvem (para Windows Server 2016 e mais recente) ou a testemunha de partilha de ficheiros (para Windows Server 2012 R2 ou mais antiga).


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

### <a name="validate-the-cluster"></a>Validar o cluster

Valide o cluster na UI ou utilizando o PowerShell.

Para validar o cluster utilizando a UI, dê os seguintes passos numa das máquinas virtuais:

1. Sob **o Gestor do Servidor,** selecione **Ferramentas**e, em seguida, selecione **Failover Cluster Manager**.
1. Em **Failover Cluster Manager,** selecione **Action**, e, em seguida, selecione **Validate Configuration**.
1. Selecione **Next**.
1. Em **Servidores Selecionados ou num Cluster,** introduza os nomes de ambas as máquinas virtuais.
1. Em **opções**de teste, selecione **apenas testes de execução que selecionei**. Selecione **Next**.
1. Em **seleção**de testes, selecione todos os testes, exceto para **espaços** de armazenamento e **armazenamento diretos,** como mostrado aqui:

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/cluster-validation.png" alt-text="Selecione testes de validação de clusters":::

1. Selecione **Next**.
1. Sob **confirmação,** selecione **Seguinte**.

O **Validar um Assistente de Configuração** executa os testes de validação.

Para validar o cluster utilizando o PowerShell, execute o seguinte script a partir de uma sessão de administrador PowerShell numa das máquinas virtuais:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Depois de validar o cluster, crie o cluster failover.

### <a name="create-the-failover-cluster"></a>Criar o cluster de ativação pós-falha

Para criar o cluster failover, você precisa:
- Os nomes das máquinas virtuais que se tornarão os nós do cluster.
- Um nome para o cluster failover
- Um endereço IP para o cluster failover. Pode utilizar um endereço IP que não seja utilizado na mesma rede virtual Azure e sub-rede que os nós do cluster.

#### <a name="windows-server-2012-through-windows-server-2016"></a>Windows Server 2012 através do Windows Server 2016

O seguinte script PowerShell cria um cluster de failover para Windows Server 2012 através do Windows Server 2016. Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível da rede virtual Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

O seguinte script PowerShell cria um cluster de failover para o Windows Server 2019. Para mais informações, consulte [O cluster Failover: Cluster Network Object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível da rede virtual Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness-win-2016-"></a>Criar uma testemunha em nuvem (Ganhar 2016 +)

Se estiver no Windows Server 2016 ou melhor, terá de criar uma Testemunha de Nuvem. Cloud Witness é um novo tipo de testemunha quórum de cluster que está armazenada numa bolha de armazenamento Azure. Isto elimina a necessidade de um VM separado que acolhe uma partilha de testemunhas, ou usando uma partilha de ficheiro separada.

1. [Crie uma testemunha de nuvem para o cluster failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Crie um recipiente de bolhas.

1. Guarde as chaves de acesso e o URL do recipiente.

### <a name="configure-quorum"></a>Configure quórum 

Para o Windows Server 2016 e maior, configure o cluster para utilizar a testemunha em nuvem que acabou de criar. Siga todos os passos [Configure a testemunha quórum na interface do utilizador](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

Para o Windows Server 2012 R2 ou mais antigo, siga os mesmos passos na [Configuração da testemunha quórum na interface do utilizador](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) mas na página **'Select Quorum Witness',** selecione a opção de testemunha de partilha de **ficheiros Configurar.** Especifique a parte do ficheiro que atribuiu para ser a testemunha de partilha de ficheiros, quer seja uma que configuraste numa máquina virtual separada, ou montada a partir do Azure. 


## <a name="step-4-test-cluster-failover"></a>Passo 4: Falha do cluster de teste

Teste falha do seu cluster. No **Failover Cluster Manager,** clique no seu cluster e selecione **Mais Ações** > Move o cluster**de recursos** > **selecionados**e, em seguida, selecione o outro nó do cluster. Mova o recurso do cluster central para cada nó do cluster e, em seguida, mova-o de volta para o nó primário. Se conseguir mover o cluster com sucesso para cada nó, está pronto para instalar o SQL Server.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/test-cluster-failover.png" alt-text="Falha no cluster de teste movendo o recurso principal para os outros nós":::

## <a name="step-5-create-the-sql-server-fci"></a>Passo 5: Criar o Servidor SQL FCI

Depois de configurar o cluster failover, pode criar o SQL Server FCI.

1. Ligue-se à primeira máquina virtual utilizando RDP.

1. No **Failover Cluster Manager,** certifique-se de que todos os Recursos core cluster estão na primeira máquina virtual. Se precisar, mova todos os recursos para esta máquina virtual.

1. Localize os meios de instalação. Se a máquina virtual utilizar uma das imagens do `C:\SQLServer_<version number>_Full`Azure Marketplace, os meios de comunicação estão localizados em . **Selecione Configuração**.

1. No Centro de Instalação do **Servidor SQL,** selecione **Instalação**.

1. Selecione nova instalação de **cluster de falha do Servidor SQL**. Siga as instruções no assistente para instalar o SQL Server FCI.

   Os diretórios de dados da FCI têm de estar na parte do ficheiro premium. Insira o caminho completo da `\\storageaccountname.file.core.windows.net\filesharename\foldername`parte, desta forma: . Aparecerá um aviso, dizendo-lhe que especificou um servidor de ficheiros como o diretório de dados. Este aviso é esperado. Certifique-se de que a conta de utilizador com a qual o RDP'd entra no VM quando insistiu que a parte do ficheiro é a mesma conta que o serviço SQL Server utiliza para evitar possíveis falhas.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/use-file-share-as-data-directories.png" alt-text="Utilize a partilha de ficheiros como diretórios de dados SQL":::

1. Depois de completar os passos no assistente, a Configuração instalará um SQL Server FCI no primeiro nó.

1. Depois de configurar o FCI no primeiro nó, ligue-se ao segundo nó utilizando RDP.

1. Abra o Centro de Instalação do **Servidor SQL**. **Selecione Instalação**.

1. Selecione Adicionar nó a um cluster de falha do **Servidor SQL**. Siga as instruções no assistente para instalar o Servidor SQL e adicione o servidor ao FCI.

   >[!NOTE]
   >Se usou uma imagem de galeria Azure Marketplace com o SQL Server, as ferramentas do SQL Server foram incluídas com a imagem. Se não usou uma dessas imagens, instale as ferramentas Do Servidor SQL separadamente. Consulte o [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-the-azure-load-balancer"></a>Passo 6: Criar o equilibrador de carga Azure

Nas máquinas virtuais azure, os clusters usam um equilibrador de carga para segurar um endereço IP que precisa estar num nó de cluster de cada vez. Nesta solução, o equilibrador de carga detém o endereço IP para o SQL Server FCI.

Para mais informações, consulte [Criar e configurar um equilibrador](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)de carga Azure .

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Crie o equilibrador de carga no portal Azure

Para criar o equilibrador de carga:

1. No portal Azure, vá ao grupo de recursos que contém as máquinas virtuais.

1. Selecione **Adicionar**. Pesquise no Mercado Azure para **balancer de carga.** Selecione **Balanceor de carga**.

1. Selecione **Criar**.

1. Instale o equilibrador de carga utilizando os seguintes valores:

   - **Subscrição**: A sua subscrição Azure.
   - **Grupo de recursos**: O grupo de recursos que contém as suas máquinas virtuais.
   - **Nome**: Um nome que identifique o equilibrador de carga.
   - **Região**: A localização Azure que contém as suas máquinas virtuais.
   - **Tipo**: Público ou privado. Um equilibrador de carga privado pode ser acedido a partir da rede virtual. A maioria das aplicações Azure pode usar um equilibrador de carga privado. Se a sua aplicação necessitar de acesso ao SQL Server diretamente através da internet, utilize um equilibrador de carga público.
   - **SKU:** Standard.
   - **Rede virtual**: A mesma rede que as máquinas virtuais.
   - **Atribuição de endereço IP**: Estática. 
   - **Endereço IP privado**: O endereço IP que atribuiu ao recurso de rede de cluster SQL Server FCI.

   A imagem seguinte mostra o Create **load balancer** UI:

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

1. Na lâmina da sonda <span id="probe"> </span> de **saúde Add,** defina os seguintes parâmetros da sonda de saúde.

   - **Nome**: Um nome para a sonda de saúde.
   - **Protocolo**: TCP.
   - **Porta**: A porta que criou na firewall para a sonda de saúde [neste passo](#ports). Neste artigo, o exemplo utiliza `59999`a porta TCP .
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

## <a name="step-7-configure-the-cluster-for-the-probe"></a>Passo 7: Configure o cluster para a sonda

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

## <a name="step-8-test-fci-failover"></a>Passo 8: Falha do Teste FCI

Teste falha do FCI para validar a funcionalidade do cluster. Siga estes passos:

1. Ligue-se a um dos nós de cluster SQL Server FCI utilizando RDP.

1. Open **Failover Cluster Manager**. Selecione **Funções**. Note qual nó detém o papel SQL Server FCI.

1. Clique na função SQL Server FCI.

1. Selecione **Mover**e, em seguida, selecione **o Melhor Nó Possível**.

**O Failover Cluster Manager** mostra o papel, e os seus recursos ficam offline. Os recursos movem-se e voltam a funcionar no outro nó.

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

- [Tecnologias de cluster windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Casos de cluster de falha do servidor SQL](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
