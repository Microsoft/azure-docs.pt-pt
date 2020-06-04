---
title: SQL Server FCI com partilha de ficheiros premium - Azure Virtual Machines
description: Este artigo explica como criar uma instância de cluster de failover do SQL Server utilizando uma partilha de ficheiros premium em Azure Virtual Machines.
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
ms.openlocfilehash: 01787fbf3339a7e079b705fb4be27ba1e30aee1b
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84342883"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Configure uma instância de cluster de failover do SQL Server com partilha de ficheiros premium em Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo explica como criar uma instância de cluster de failover (FCI) do SQL Server em Azure Virtual Machines utilizando uma [partilha de ficheiros premium](../../../storage/files/storage-how-to-create-premium-fileshare.md).

As ações de ficheiros premium são ações de ficheiros apoiadas por SSD, consistentemente de baixa latência, que são totalmente suportadas para utilização com Instâncias de Cluster Failover para SQL Server 2012 ou mais tarde no Windows Server 2012 ou posteriormente. As ações de ficheiros premium conferem-lhe maior flexibilidade, permitindo-lhe redimensionar e escalar uma partilha de ficheiros sem qualquer tempo de inatividade.


## <a name="before-you-begin"></a>Antes de começar

Há algumas coisas que precisa saber e ter no lugar antes de começar.

Deve ter uma compreensão operacional destas tecnologias:

- [Tecnologias de cluster windows](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server Failover Cluster Instances](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Uma coisa a ter em conta é que num cluster de failover VM Azure IaaS VM, recomendamos um único NIC por servidor (nó de cluster) e uma única sub-rede. A rede Azure tem redundância física que torna NICs e sub-redes adicionais desnecessários num cluster de hóspedes Azure IaaS VM. O relatório de validação do cluster irá avisá-lo de que os nós são alcançáveis apenas numa única rede. Pode ignorar este aviso sobre os aglomerados de failover Azure IaaS VM.

Deve também ter uma compreensão geral destas tecnologias:

- [Azure premium file share](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Grupos de recursos do Azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Neste momento, as instâncias de cluster failover do SQL Server nas Máquinas Virtuais Azure só são suportadas com o modo de [gestão leve](sql-vm-resource-provider-register.md#management-modes) da Extensão do [Agente IAAS](sql-server-iaas-agent-extension-automate-management.md)do SqL Server . Para mudar do modo de extensão total para leve, elimine o recurso **SQL Virtual Machine** para os VMs correspondentes e registe-os com o fornecedor de recursos SQL VM em modo leve. Ao eliminar o recurso **SQL Virtual Machine** utilizando o portal Azure, **limpe a caixa de verificação ao lado da Máquina Virtual correta**. A extensão completa suporta funcionalidades como backup automatizado, patching e gestão avançada do portal. Estas funcionalidades não funcionarão para VMS SQL após a reinstalação do agente em modo de gestão leve.

As ações de ficheiros premium fornecem IOPS e capacidades de produção que irão atender às necessidades de muitas cargas de trabalho. Para cargas de trabalho intensivas em IO, considere [sql Server Failover Cluster Instances with Storage Spaces Direct](failover-cluster-instance-storage-spaces-direct-manually-configure.md), com base em discos premium geridos ou discos ultra.  

Verifique a atividade do IOPS do seu ambiente e verifique se as ações de ficheiros premium fornecerão o IOPS de que necessita antes de iniciar uma implementação ou migração. Utilize os contadores de discos do Monitor de Desempenho do Windows para monitorizar o total de IOPS (Transferências de Discos/segundo) e a produção (Disk Bytes/second) necessárias para os ficheiros SQL Server, Log e Temp DB.

Muitas cargas de trabalho têm rebentado IO, por isso é uma boa ideia verificar durante períodos de uso pesado e notar tanto o IOPS máximo como o IOPS médio. As ações de ficheiros premium fornecem IOPS com base no tamanho da parte. As ações de ficheiro premium também fornecem uma explosão gratuita que lhe permite rebentar o seu IO para triplicar o valor da linha de base por até uma hora.

Para obter mais informações sobre o desempenho da partilha de ficheiros premium, consulte [os níveis de desempenho da partilha de ficheiros](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers).

### <a name="licensing-and-pricing"></a>Licenciamento e preços

Nas Máquinas Virtuais Azure, pode licenciar o SQL Server utilizando imagens VM pay-as-you-go (PAYG) ou bring-your-own-license (BYOL). O tipo de imagem que escolhes afeta a forma como és cobrado.

Com o licenciamento pay-as-you-go, uma instância de cluster failover (FCI) do SQL Server em Azure Virtual Machines incorre em taxas para todos os nós da FCI, incluindo os nós passivos. Para obter mais informações, consulte o [preço das máquinas virtuais da empresa SQL.](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)

Se tiver o Acordo Empresarial com a Garantia de Software, pode utilizar um nó FCI passivo gratuito para cada nó ativo. Para tirar partido deste benefício em Azure, utilize imagens BYOL VM e use a mesma licença nos nós ativos e passivos da FCI. Para mais informações, consulte [o Acordo de Empresa.](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)

Para comparar o licenciamento pay-as-you-go e BYOL para o SQL Server em Azure Virtual Machines, consulte [Começar com VMs SQL](sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms).

Para obter informações completas sobre o licenciamento do SQL Server, consulte [o Preço](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="filestream"></a>Filestream

O Filestream não é suportado por um cluster de failover com uma partilha de ficheiros premium. Para utilizar o fluxo de ficheiros, implemente o seu cluster utilizando [espaços de armazenamento diretos](failover-cluster-instance-storage-spaces-direct-manually-configure.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de completar os passos deste artigo, já deve ter:

- Uma subscrição do Microsoft Azure.
- Um domínio Windows em Azure Virtual Machines.
- Uma conta de utilizador de domínio que tem permissões para criar objetos tanto em Azure Virtual Machines como no Ative Directory.
- Uma conta de utilizador de domínio para executar o serviço SQL Server e que pode iniciar sessão na máquina virtual com a montagem da partilha de ficheiros.  
- Uma rede virtual Azure e uma sub-rede com espaço de endereço IP suficiente para estes componentes:
   - Duas máquinas virtuais.
   - O endereço IP do cluster de falhas.
   - Um endereço IP para cada FCI.
- DNS configurado na rede Azure, apontando para os controladores de domínio.
- Uma [partilha de ficheiro premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) para ser usada como unidade agrupada, com base na quota de armazenamento da sua base de dados para os seus ficheiros de dados.
- Se estiver no Windows Server 2012 R2 ou mais antigo, precisará de outra partilha de ficheiros para usar como testemunha de partilha de ficheiros, uma vez que as testemunhas na nuvem são suportadas pelo Windows 2016 e mais recentes. Pode utilizar outra partilha de ficheiros Azure, ou pode utilizar uma partilha de ficheiros numa máquina virtual separada. Se vai utilizar outra partilha de ficheiros Azure, pode montá-la com o mesmo processo que a partilha de ficheiros premium utilizada para a sua unidade agrupada. 

Com estes pré-requisitos no lugar, pode começar a construir o seu aglomerado de falhanços. O primeiro passo é criar as máquinas virtuais.

## <a name="step-1-create-the-virtual-machines"></a>Passo 1: Criar as máquinas virtuais

1. Inscreva-se no [portal Azure](https://portal.azure.com) com a sua subscrição.

1. [Crie um conjunto de disponibilidade Azure](../../../virtual-machines/linux/tutorial-availability-sets.md).

   O conjunto de disponibilidadesgrupam máquinas virtuais em domínios de avaria e domínios de atualização. Garante que a sua aplicação não é afetada por pontos únicos de falha, como o interruptor de rede ou a unidade de alimentação de um rack de servidores.

   Se não criou o grupo de recursos para as suas máquinas virtuais, faça-o quando criar um conjunto de disponibilidades Azure. Se estiver a utilizar o portal Azure para criar o conjunto de disponibilidades, tome estes passos:

   1. No portal Azure, selecione **Criar um recurso** para abrir o Azure Marketplace. Procurar **conjunto de Disponibilidade**.
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

   - No mesmo grupo de recursos Azure que o seu conjunto de disponibilidade.
   - Na mesma rede que o seu controlador de domínio.
   - Numa sub-rede que tem espaço suficiente para endereços IP para máquinas virtuais e todas as FCIs que poderá eventualmente utilizar no cluster.
   - No conjunto de disponibilidade do Azure.

      >[!IMPORTANT]
      >Não é possível definir ou alterar o conjunto de disponibilidade depois de ter criado uma máquina virtual.

   Escolha uma imagem do Azure Marketplace. Pode utilizar uma imagem do Azure Marketplace que inclua o Windows Server e o SQL Server, ou utilizar uma que apenas inclui o Windows Server. Para mais detalhes, consulte [a visão geral do SQL Server em Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).

   As imagens oficiais do SQL Server na Galeria Azure incluem uma instância de servidor SQL instalada, o software de instalação do SQL Server e a chave necessária.

   >[!IMPORTANT]
   > Depois de criar a máquina virtual, remova a instância sql server pré-instalada. Utilizará o meio de servidor SQL pré-instalado para criar o SQL Server FCI depois de configurar o cluster de failover e a partilha de ficheiros premium como armazenamento.

   Em alternativa, pode utilizar imagens do Azure Marketplace que contenham apenas o sistema operativo. Escolha uma imagem **do Datacenter 2016** do Windows Server e instale o SQL Server FCI depois de configurar o cluster de failover e a partilha de ficheiros premium como armazenamento. Esta imagem não contém meios de instalação SQL Server. Coloque o meio de instalação do SQL Server num local onde possa executá-lo para cada servidor.

1. Depois de o Azure criar as suas máquinas virtuais, ligue-se a cada uma utilizando RDP.

   Quando se liga a uma máquina virtual utilizando RDP, um pedido pergunta-lhe se pretende permitir que o PC seja detetável na rede. Selecione **Sim**.

1. Se estiver a utilizar uma das imagens de máquinas virtuais baseadas no SQL Server, remova a instância do SQL Server.

   1. Em **Programas e Funcionalidades,** clique com o botão direito **Microsoft SQL Server 201_ (64-bit)** e selecione **Desinstalar/Alterar**.
   1. Selecione **Remover**.
   1. Selecione a instância padrão.
   1. Remova todas as funcionalidades nos **serviços de motores de base de dados.** Não remova **funcionalidades partilhadas.** Verá algo como a seguinte imagem:

        ![Selecione funcionalidades](./media/failover-cluster-instance-premium-file-share-manually-configure/03-remove-features.png)

   1. Selecione **Seguinte**e, em seguida, selecione **Remover**.

1. <span id="ports"> </span> Abra as portas de firewall.  

   Em cada máquina virtual, abra estas portas no Windows Firewall:

   | Objetivo | Porta TCP | Notas
   | ------ | ------ | ------
   | SQL Server | 1433 | Porta normal para instâncias padrão do SQL Server. Se utilizar uma imagem da galeria, esta porta é aberta automaticamente.
   | Sonda de estado de funcionamento | 59999 | Qualquer porta TCP aberta. Em um passo posterior, configurar a [sonda de saúde](#probe) do balanceador de carga e o cluster para usar esta porta.
   | Partilha de ficheiros | 445 | Porta utilizada pelo serviço de partilha de ficheiros.

1. [Adicione as máquinas virtuais ao seu domínio pré-existente](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain).

Depois de criar e configurar as máquinas virtuais, pode configurar a parte de ficheiro premium.

## <a name="step-2-mount-the-premium-file-share"></a>Passo 2: Monte a parte do ficheiro premium

1. Inscreva-se no [portal Azure](https://portal.azure.com) e vá à sua conta de armazenamento.
1. Vá a **''' Arquivar ações** em **serviço de ficheiros** e selecione a partilha de ficheiros premium que pretende utilizar para o seu armazenamento SQL.
1. Selecione **Connect** para aumentar a cadeia de ligação para a sua partilha de ficheiros.
1. Selecione a letra de unidade que pretende utilizar na lista de drop-down e, em seguida, copie ambos os blocos de código para o Bloco de Notas.


   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/premium-file-storage-commands.png" alt-text="Copie ambos os comandos PowerShell do portal de ligação de partilha de ficheiros":::

1. Utilize RDP para ligar ao SQL Server VM com a conta que o seu SQL Server FCI utilizará para a conta de serviço.
1. Abra uma consola administrativa de comando PowerShell.
1. Executar os comandos que guardou mais cedo quando estava a trabalhar no portal.
1. Aceda à partilha utilizando o File Explorer ou a caixa de diálogo **Run** (tecla do logótipo do Windows + r). Utilize o caminho da `\\storageaccountname.file.core.windows.net\filesharename` rede. Por exemplo, `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Crie pelo menos uma pasta na partilha de ficheiros recentemente ligada para colocar os seus ficheiros SQL Data.
1. Repita estes passos em cada SQL Server VM que participará no cluster.

  > [!IMPORTANT]
  > - Considere utilizar uma partilha de ficheiros separada para ficheiros de cópia de segurança para guardar o IOPS e a capacidade de espaço desta partilha para ficheiros Data e Log. Pode utilizar uma partilha de ficheiros premium ou padrão para ficheiros de backup.
  > - Se estiver no Windows 2012 R2 ou mais velho, siga estes mesmos passos para montar a sua partilha de ficheiros que vai usar como testemunha de partilha de ficheiros. 

## <a name="step-3-configure-the-failover-cluster"></a>Passo 3: Configurar o cluster de failover

O próximo passo é configurar o aglomerado de falhanços. Neste passo, você completará os seguintes passos:

1. Adicione a função de clustering de falha do servidor do Windows.
1. Valide o cluster.
1. Crie o aglomerado de falhas.
1. Crie a testemunha em nuvem (para o Windows Server 2016 e mais recente) ou a testemunha de partilha de ficheiros (para o Windows Server 2012 R2 ou mais antigo).


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

### <a name="validate-the-cluster"></a>Validar o cluster

Valide o cluster na UI ou utilizando o PowerShell.

Para validar o cluster utilizando a UI, tome os seguintes passos numa das máquinas virtuais:

1. Sob **o Gestor do Servidor**, selecione **Ferramentas**e, em seguida, selecione **O Gestor de Cluster Failover**.
1. Em **'Failover Cluster Manager',** selecione **Ação**e, em seguida, selecione **Validate Configuration**.
1. Selecione **Seguinte**.
1. Em **Servidores Selecionados ou num Cluster,** insira os nomes de ambas as máquinas virtuais.
1. Nas **opções de Teste,** selecione **Executar apenas testes que seleciono**. Selecione **Seguinte**.
1. Em **Seleção de Testes**, selecione todos os testes, exceto para espaços **de armazenamento** e armazenamento **direto,** como mostrado aqui:

   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/cluster-validation.png" alt-text="Selecione testes de validação de clusters":::

1. Selecione **Seguinte**.
1. Em **Confirmação**, selecione **Seguinte**.

O **Assistente de Validação de Um Assistente** de Configuração executa os testes de validação.

Para validar o cluster utilizando o PowerShell, execute o seguinte script a partir de uma sessão PowerShell de administrador numa das máquinas virtuais:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Depois de validar o cluster, crie o cluster de failover.

### <a name="create-the-failover-cluster"></a>Criar o cluster de ativação pós-falha

Para criar o cluster de failover, você precisa:
- Os nomes das máquinas virtuais que se tornarão os nós do cluster.
- Um nome para o cluster failover
- Um endereço IP para o cluster de failover. Pode utilizar um endereço IP que não seja utilizado na mesma rede virtual Azure e sub-rede que os nós de cluster.

#### <a name="windows-server-2012-through-windows-server-2016"></a>Windows Server 2012 através do Windows Server 2016

O seguinte script PowerShell cria um cluster de falha para o Windows Server 2012 através do Windows Server 2016. Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível a partir da rede virtual Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

O seguinte script PowerShell cria um cluster de falha para o Windows Server 2019. Para obter mais informações, consulte [o cluster Failover: Cluster Network Object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível a partir da rede virtual Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness-win-2016-"></a>Criar uma testemunha em nuvem (Ganhar 2016 +)

Se estiver no Windows Server 2016 e for maior, terá de criar uma Cloud Witness. Cloud Witness é um novo tipo de testemunha de quórum que está guardada numa bolha de armazenamento Azure. Isto elimina a necessidade de um VM separado que acolhe uma partilha de testemunhas, ou usando uma partilha de ficheiros separada.

1. [Crie uma testemunha em nuvem para o aglomerado de falhas.](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)

1. Crie um recipiente para bolhas.

1. Guarde as chaves de acesso e o URL do recipiente.

### <a name="configure-quorum"></a>Configure quórum 

Para o Windows Server 2016 e maior, configura o cluster para utilizar a testemunha em nuvem que acabou de criar. Siga todos os passos [Configurar a testemunha do quórum na interface do utilizador.](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)

Para o Windows Server 2012 R2 ou mais antigo, siga os mesmos passos em [Configurar a testemunha quórum na interface do utilizador](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) mas na página Select **Quorum Witness,** selecione a opção Configurar uma testemunha **de partilha de ficheiros.** Especifique a partilha de ficheiros que alocou para ser a testemunha de partilha de ficheiros, quer seja aquela que configuraste numa máquina virtual separada, ou montada a partir do Azure. 


## <a name="step-4-test-cluster-failover"></a>Passo 4: Falha no cluster de teste

Teste de falha do seu aglomerado. No **Failover Cluster Manager,** clique com o botão direito do seu cluster e selecione **Mais Ações**  >  **Move Core Cluster Resource**Select  >  **node**e, em seguida, selecione o outro nó do cluster. Mova o recurso de cluster do núcleo para cada nó do cluster e, em seguida, movimente-o de volta para o nó primário. Se conseguir mover o cluster com sucesso para cada nó, está pronto para instalar o SQL Server.  

:::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/test-cluster-failover.png" alt-text="Falha no cluster de teste, movendo o recurso do núcleo para os outros nos dois":::

## <a name="step-5-create-the-sql-server-fci"></a>Passo 5: Criar o SQL Server FCI

Depois de configurar o cluster failover, pode criar o SQL Server FCI.

1. Ligue-se à primeira máquina virtual utilizando RDP.

1. No **Failover Cluster Manager,** certifique-se de que todos os Recursos de Cluster Core estão na primeira máquina virtual. Se precisar, mova todos os recursos para esta máquina virtual.

1. Localize os meios de instalação. Se a máquina virtual utilizar uma das imagens do Azure Marketplace, os meios de comunicação estão localizados em `C:\SQLServer_<version number>_Full` . Selecione **Configuração**.

1. No **Centro de Instalação do Servidor SQL,** selecione **Instalação**.

1. Selecione **a instalação de cluster de falha do novo sql server**. Siga as instruções do assistente para instalar o SQL Server FCI.

   Os diretórios de dados da FCI têm de estar na parte de ficheiros premium. Insira o caminho completo da partilha, desta forma: `\\storageaccountname.file.core.windows.net\filesharename\foldername` . Aparecerá um aviso, informando-o de que especificou um servidor de ficheiros como diretório de dados. Este aviso é esperado. Certifique-se de que a conta de utilizador que tem RDP'd para o VM com quando persiste a partilha de ficheiros é a mesma que o serviço SQL Server utiliza para evitar possíveis falhas.

   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/use-file-share-as-data-directories.png" alt-text="Use a partilha de ficheiros como diretórios de dados SQL":::

1. Depois de completar os passos no assistente, a Configuração instalará um SQL Server FCI no primeiro nó.

1. Depois de configurar a INSTALAÇÃO instala o FCI no primeiro nó, ligue-o ao segundo nó utilizando RDP.

1. Abra o **Centro de Instalação do Servidor SQL**. Selecione **instalação**.

1. **Selecione Adicionar nó a um cluster de falha do sql server**. Siga as instruções do assistente para instalar o SQL Server e adicione o servidor à FCI.

   >[!NOTE]
   >Se usou uma imagem de galeria do Azure Marketplace com o SQL Server, as ferramentas sql Server foram incluídas com a imagem. Se não usou uma dessas imagens, instale as ferramentas SQL Server separadamente. Consulte [o Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-the-azure-load-balancer"></a>Passo 6: Criar o equilibrador de carga Azure

Nas Máquinas Virtuais Azure, os clusters usam um equilibrador de carga para conter um endereço IP que precisa de estar num nó de cluster de cada vez. Nesta solução, o equilibrador de carga detém o endereço IP para o SQL Server FCI.

Para obter mais informações, consulte [Criar e configurar um equilibrador de carga Azure](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Criar o equilibrador de carga no portal Azure

Para criar o equilibrador de carga:

1. No portal Azure, vá ao grupo de recursos que contém as máquinas virtuais.

1. Selecione **Adicionar**. Procure no Mercado Azure para **o Balanceador de Carga.** Selecione **o balanceador de carga**.

1. Selecione **Criar**.

1. Configurar o balançador de carga utilizando os seguintes valores:

   - **Subscrição**: A sua subscrição Azure.
   - **Grupo de recursos**: O grupo de recursos que contém as suas máquinas virtuais.
   - **Nome**: Um nome que identifica o esquilibrador de carga.
   - **Região**: A localização azul que contém as suas máquinas virtuais.
   - **Tipo**: Público ou privado. Um equilibrador de carga privado pode ser acedido a partir da rede virtual. A maioria das aplicações Azure pode usar um equilibrador de carga privado. Se a sua aplicação necessitar de acesso ao SQL Server diretamente através da internet, utilize um equilibrador de carga público.
   - **SKU**: Padrão.
   - **Rede virtual**: A mesma rede que as máquinas virtuais.
   - **Atribuição de endereço IP**: Estática. 
   - **Endereço IP privado**: O endereço IP que atribuiu ao recurso de rede de cluster SQL Server FCI.

   A imagem a seguir mostra o **UI do equilibrador de carga Create:**

   ![Configurar o equilibrador de carga](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>Configure a piscina de backend do balançador de carga

1. Volte ao grupo de recursos Azure que contém as máquinas virtuais e localize o novo equilibrador de carga. Pode ser necessário refrescar a vista sobre o grupo de recursos. Selecione o equilibrador de carga.

1. Selecione **piscinas backend**e, em seguida, selecione **Adicionar**.

1. Associe o pool de backend com o conjunto de disponibilidade que contém os VMs.

1. Nas **configurações IP da rede Target**, selecione VIRTUAL **MACHINE** e escolha as máquinas virtuais que participarão como nós de cluster. Certifique-se de incluir todas as máquinas virtuais que irão acolher o FCI.

1. Selecione **OK** para criar a piscina de backend.

### <a name="configure-a-load-balancer-health-probe"></a>Configurar uma sonda de estado de funcionamento do balanceador de carga

1. Na lâmina do balançador de carga, selecione **sondas de saúde**.

1. Selecione **Adicionar**.

1. Na lâmina da sonda <span id="probe"> </span> de **saúde Add,** desa estale os seguintes parâmetros da sonda de saúde.

   - **Nome**: Um nome para a sonda de saúde.
   - **Protocolo**: TCP.
   - **Porto**: A porta que criou na firewall para a sonda de saúde [neste passo](#ports). Neste artigo, o exemplo utiliza a porta `59999` TCP.
   - **Intervalo:** 5 Segundos.
   - **Limiar pouco saudável:** 2 falhas consecutivas.

1. Selecione **OK**.

### <a name="set-load-balancing-rules"></a>Definir regras de equilíbrio de carga

1. Na lâmina do balançador de carga, selecione **regras de equilíbrio de carga**.

1. Selecione **Adicionar**.

1. Definir os parâmetros de regra de equilíbrio de carga:

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

## <a name="step-7-configure-the-cluster-for-the-probe"></a>Passo 7: Configurar o cluster para a sonda

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

Depois de definir a sonda de cluster, pode ver todos os parâmetros de cluster no PowerShell. Executar este script:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter
  ```

## <a name="step-8-test-fci-failover"></a>Passo 8: Teste fci failover

Teste de falha da FCI para validar a funcionalidade do cluster. Siga estes passos:

1. Ligue-se a um dos nós de cluster SQL Server FCI utilizando RDP.

1. Open **Failover Cluster Manager**. Selecione **Funções**. Note qual o nó que detém a função SQL Server FCI.

1. Clique com o botão direito no papel SQL Server FCI.

1. Selecione **Mover**e, em seguida, selecione **O Melhor Nó Possível**.

**O Failover Cluster Manager** mostra o papel e os seus recursos ficam offline. Os recursos movem-se e voltam a estar on-line no outro nó.

### <a name="test-connectivity"></a>Testar conectividade

Para testar a conectividade, inscreva-se noutra máquina virtual na mesma rede virtual. Abra **o SQL Server Management Studio** e ligue-se ao nome FCI do SQL Server.

>[!NOTE]
>Se precisar, pode [baixar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Limitações

A Azure Virtual Machines suporta o Coordenador de Transações Distribuídas da Microsoft (MSDTC) no Windows Server 2019 com armazenamento em Volumes Compartilhados Agrupados (CSV) e um [balanceador de carga padrão](../../../load-balancer/load-balancer-standard-overview.md).

Nas Máquinas Virtuais Azure, o MSDTC não é suportado no Windows Server 2016 ou mais cedo porque:

- O recurso MSDTC agrupado não pode ser configurado para usar armazenamento partilhado. No Windows Server 2016, se criar um recurso MSDTC, não apresentará nenhum armazenamento partilhado disponível para utilização, mesmo que o armazenamento esteja disponível. Este problema foi corrigido no Windows Server 2019.
- O equilibrador de carga básico não lida com portas RPC.

## <a name="see-also"></a>Ver também

- [Tecnologias de cluster windows](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server Failover Cluster Instances](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
