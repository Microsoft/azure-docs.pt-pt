---
title: SQL Server FCI com compartilhamento de arquivos Premium – máquinas virtuais do Azure
description: Este artigo explica como criar uma instância de cluster de failover SQL Server usando um compartilhamento de arquivos Premium em máquinas virtuais do Azure.
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
ms.openlocfilehash: f92226a76462289b9f26ae9d3bab22d780fb35db
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464990"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Configurar uma instância de cluster de failover SQL Server com compartilhamento de arquivos Premium em máquinas virtuais do Azure

Este artigo explica como criar uma FCI (instância de cluster de failover) SQL Server em máquinas virtuais do Azure usando um [compartilhamento de arquivos Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md).

Os compartilhamentos de arquivos Premium são compartilhamentos de arquivos consistentemente de baixa latência que têm suporte total para uso com instâncias de cluster de failover para o SQL Server 2012 ou posterior no Windows Server 2012 ou posterior. Os compartilhamentos de arquivos Premium proporcionam maior flexibilidade, permitindo que você redimensione e dimensione um compartilhamento de arquivos sem nenhum tempo de inatividade.


## <a name="before-you-begin"></a>Antes de começar

Há algumas coisas que você precisa saber e ter em vigor antes de começar.

Você deve ter uma compreensão operacional dessas tecnologias:

- [Tecnologias de cluster do Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server instâncias de cluster de failover](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Uma coisa a ser observada é que, em um cluster de failover de VM IaaS do Azure, recomendamos uma única NIC por servidor (nó de cluster) e uma única sub-rede. A rede do Azure tem redundância física que torna as NICs e sub-redes adicionais desnecessárias em um cluster convidado da VM IaaS do Azure. O relatório de validação de cluster avisará que os nós podem ser acessados apenas em uma única rede. Você pode ignorar esse aviso em clusters de failover de VM IaaS do Azure.

Você também deve ter uma compreensão geral dessas tecnologias:

- [Compartilhamento de arquivos premium do Azure](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Grupos de recursos do Azure](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Neste momento, SQL Server instâncias de cluster de failover em máquinas virtuais do Azure só têm suporte com o [modo de gerenciamento leve](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) da [extensão do agente IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Para alterar do modo de extensão completo para leve, exclua o recurso de **máquina virtual do SQL** para as VMs correspondentes e registre-as com o provedor de recursos de VM do SQL no modo leve. Ao excluir o recurso de **máquina virtual do SQL** usando o portal do Azure, **desmarque a caixa de seleção ao lado da máquina virtual correta**. A extensão completa oferece suporte a recursos como backup automatizado, aplicação de patch e gerenciamento avançado do Portal. Esses recursos não funcionarão para VMs do SQL depois que o agente for reinstalado no modo de gerenciamento leve.

Os compartilhamentos de arquivos Premium fornecem IOPS e recursos que atenderão às necessidades de várias cargas de trabalho. Para cargas de trabalho com uso intensivo de e/s, considere [SQL Server instâncias de cluster de failover com espaços de armazenamento diretos](virtual-machines-windows-portal-sql-create-failover-cluster.md), com base em discos Premium gerenciados ou em discos ultraos.  

Verifique a atividade de IOPS do seu ambiente e verifique se os compartilhamentos de arquivos Premium fornecerão o IOPS de que você precisa antes de iniciar uma implantação ou migração. Use os contadores de disco do monitor de desempenho do Windows para monitorar o IOPS total (transferências de disco/segundo) e a taxa de transferência (bytes de disco/segundo) necessários para dados de SQL Server, log e arquivos de BD temporários.

Muitas cargas de trabalho têm intermitência de e/s, portanto, é uma boa ideia verificar durante períodos de uso pesado e observar o IOPS máximo e a média de IOPS. Os compartilhamentos de arquivos Premium fornecem IOPS com base no tamanho do compartilhamento. Os compartilhamentos de arquivos Premium também fornecem intermitências gratuitas que permitem a intermitência de sua e/s para triplo da quantidade de linha de base por até uma hora.

Para obter mais informações sobre o desempenho de compartilhamento de arquivos premium, consulte [níveis de desempenho de compartilhamento de arquivos](https://docs.microsoft.com/azure/storage/files/storage-files-planning#file-share-performance-tiers).

### <a name="licensing-and-pricing"></a>Licenciamento e preços

Em máquinas virtuais do Azure, você pode licenciar SQL Server usando imagens de VM PAYG (pré-pago) ou BYOL (traga sua própria licença). O tipo de imagem que você escolhe afeta como você é cobrado.

Com o licenciamento pago conforme o uso, uma FCI (instância de cluster de failover) de SQL Server em máquinas virtuais do Azure incorre em encargos para todos os nós do FCI, incluindo os nós passivos. Para obter mais informações, consulte [SQL Server Enterprise preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Se você tiver Enterprise Agreement com o Software Assurance, poderá usar um nó FCI passivo gratuito para cada nó ativo. Para aproveitar esse benefício no Azure, use imagens de VM BYOL e use a mesma licença nos nós ativo e passivo do FCI. Para obter mais informações, consulte [Enterprise Agreement](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Para comparar o licenciamento pago conforme o uso e o BYOL para SQL Server em máquinas virtuais do Azure, consulte Introdução [às VMs do SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Para obter informações completas sobre SQL Server de licenciamento, consulte [preços](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="filestream"></a>Filestream

FILESTREAM não tem suporte para um cluster de failover com um compartilhamento de arquivos premium. Para usar o FileStream, implante o cluster usando [espaços de armazenamento diretos](virtual-machines-windows-portal-sql-create-failover-cluster.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de concluir as etapas neste artigo, você já deve ter:

- Uma assinatura Microsoft Azure.
- Um domínio do Windows em máquinas virtuais do Azure.
- Uma conta que tem permissões para criar objetos em máquinas virtuais do Azure e em Active Directory.
- Uma rede virtual do Azure e uma sub-rede com espaço de endereço IP suficiente para esses componentes:
   - Duas máquinas virtuais.
   - O endereço IP do cluster de failover.
   - Um endereço IP para cada FCI.
- DNS configurado na rede do Azure, apontando para os controladores de domínio.
- Um [compartilhamento de arquivos Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) com base na cota de armazenamento do seu banco de dados para seus arquivos.

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

   >[!IMPORTANT]
   > Depois de criar a máquina virtual, remova a instância de SQL Server autônoma pré-instalada. Você usará a mídia de SQL Server pré-instalada para criar o SQL Server FCI depois de configurar o cluster de failover e o compartilhamento de arquivos Premium como armazenamento.

   Como alternativa, você pode usar as imagens do Azure Marketplace que contêm apenas o sistema operacional. Escolha uma imagem do **Windows Server 2016 datacenter** e instale o SQL Server FCI depois de configurar o cluster de failover e o compartilhamento de arquivos Premium como armazenamento. Esta imagem não contém SQL Server mídia de instalação. Coloque a mídia de instalação do SQL Server em um local onde você possa executá-lo para cada servidor.

1. Depois que o Azure criar suas máquinas virtuais, conecte-se a cada uma usando o RDP.

   Quando você se conecta pela primeira vez a uma máquina virtual usando o RDP, um prompt pergunta se você deseja permitir que o computador seja detectável na rede. Selecione **Sim**.

1. Se você estiver usando uma das imagens de máquina virtual com base em SQL Server, remova a instância de SQL Server.

   1. Em **programas e recursos**, clique com o botão direito do mouse em **Microsoft SQL Server 201_ (64 bits)** e selecione **Desinstalar/alterar**.
   1. Selecione **Remover**.
   1. Selecione a instância padrão.
   1. Remova todos os recursos em **serviços mecanismo de banco de Dadoss**. Não remova os **recursos compartilhados**. Você verá algo semelhante à captura de tela a seguir:

        ![Selecionar Funcionalidades](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Selecione **Avançar**e, em seguida, selecione **remover**.

1. <a name="ports"></a>Abra as portas do firewall.

   Em cada máquina virtual, abra essas portas no firewall do Windows:

   | Finalidade | Porta TCP | Notas
   | ------ | ------ | ------
   | SQL Server | 1433 | Porta normal para instâncias padrão de SQL Server. Se você usou uma imagem da galeria, essa porta será aberta automaticamente.
   | Sonda de estado de funcionamento | 59999 | Qualquer porta TCP aberta. Em uma etapa posterior, configure a [investigação de integridade](#probe) do balanceador de carga e o cluster para usar essa porta.
   | Partilha de ficheiros | 445 | Porta usada pelo serviço de compartilhamento de arquivos.

1. [Adicione as máquinas virtuais ao domínio já existente](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Depois de criar e configurar as máquinas virtuais, você pode configurar o compartilhamento de arquivos premium.

## <a name="step-2-mount-the-premium-file-share"></a>Etapa 2: montar o compartilhamento de arquivos Premium

1. Entre no [portal do Azure](https://portal.azure.com) e vá para sua conta de armazenamento.
1. Vá para **compartilhamentos de arquivos** em **serviço de arquivo** e selecione o compartilhamento de arquivos premium que você deseja usar para o armazenamento do SQL.
1. Selecione **conectar** para exibir a cadeia de conexão para o compartilhamento de arquivos.
1. Selecione a letra da unidade que você deseja usar na lista suspensa e copie os dois blocos de código para o bloco de notas.


   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/premium-file-storage-commands.png" alt-text="Copie os dois comandos do PowerShell do portal de conexão de compartilhamento de arquivos":::

1. Use o RDP para se conectar à VM SQL Server com a conta que seu SQL Server FCI usará para a conta de serviço.
1. Abra um console de comando do PowerShell administrativo.
1. Execute os comandos que você salvou anteriormente quando estava trabalhando no Portal.
1. Vá para o compartilhamento usando o explorador de arquivos ou a caixa de diálogo **executar** (tecla com o logotipo do Windows + r). Use o `\\storageaccountname.file.core.windows.net\filesharename`de caminho de rede. Por exemplo, `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Crie pelo menos uma pasta no compartilhamento de arquivos conectado recentemente para posicionar seus arquivos de dados SQL.
1. Repita essas etapas em cada VM SQL Server que participará do cluster.

  > [!IMPORTANT]
  > Considere o uso de um compartilhamento de arquivos separado para arquivos de backup para salvar a capacidade de IOPS e espaço desse compartilhamento para arquivos de dados e de log. Você pode usar um compartilhamento de arquivos Premium ou Standard para arquivos de backup.

## <a name="step-3-configure-the-failover-cluster-with-the-file-share"></a>Etapa 3: configurar o cluster de failover com o compartilhamento de arquivos

A próxima etapa é configurar o cluster de failover. Nesta etapa, você concluirá as seguintes subetapas:

1. Adicione o recurso Windows Server failover clustering.
1. Valide o cluster.
1. Crie o cluster de failover.
1. Crie a testemunha de nuvem.


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

### <a name="validate-the-cluster"></a>Validar o cluster

Valide o cluster na interface do usuário ou usando o PowerShell.

Para validar o cluster usando a interface do usuário, execute as seguintes etapas em uma das máquinas virtuais:

1. Em **Gerenciador do servidor**, selecione **ferramentas**e, em seguida, selecione **Gerenciador de cluster de failover**.
1. Em **Gerenciador de cluster de failover**, selecione **ação**e, em seguida, selecione **validar configuração**.
1. Selecione **Seguinte**.
1. Em **selecionar servidores ou um cluster**, insira os nomes de ambas as máquinas virtuais.
1. Em **Opções de teste**, selecione **executar apenas testes que eu selecionar**. Selecione **Seguinte**.
1. Em **seleção de teste**, selecione todos os testes, exceto **armazenamento** e **espaços de armazenamento diretos**, conforme mostrado aqui:

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/cluster-validation.png" alt-text="Selecionar testes de validação de cluster":::

1. Selecione **Seguinte**.
1. Em **confirmação**, selecione **Avançar**.

O **Assistente para validar uma configuração** executa os testes de validação.

Para validar o cluster usando o PowerShell, execute o seguinte script de uma sessão de administrador do PowerShell em uma das máquinas virtuais:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Depois de validar o cluster, crie o cluster de failover.

### <a name="create-the-failover-cluster"></a>Criar o cluster de ativação pós-falha

Para criar o cluster de failover, você precisa de:
- Os nomes das máquinas virtuais que se tornarão os nós de cluster.
- Um nome para o cluster de failover
- Um endereço IP para o cluster de failover. Você pode usar um endereço IP que não é usado na mesma rede virtual e sub-rede do Azure que os nós de cluster.

#### <a name="windows-server-2012-through-windows-server-2016"></a>Windows Server 2012 por meio do Windows Server 2016

O script do PowerShell a seguir cria um cluster de failover para o Windows Server 2012 por meio do Windows Server 2016. Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível da rede virtual do Azure.

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


## <a name="step-4-test-cluster-failover"></a>Etapa 4: testar o failover de cluster

Failover de teste do cluster. Em **Gerenciador de cluster de failover**, clique com o botão direito do mouse no cluster e selecione **mais ações** > **mover recurso de cluster principal** > **selecione nó**e, em seguida, selecione o outro nó do cluster. Mova o recurso de cluster principal para cada nó do cluster e, em seguida, mova-o de volta para o nó primário. Se você puder mover o cluster para cada nó com êxito, estará pronto para instalar o SQL Server.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/test-cluster-failover.png" alt-text="Testar o failover de cluster movendo o recurso principal para os outros nós":::

## <a name="step-5-create-the-sql-server-fci"></a>Etapa 5: criar o SQL Server FCI

Depois de configurar o cluster de failover, você pode criar o SQL Server FCI.

1. Conecte-se à primeira máquina virtual usando o RDP.

1. Em **Gerenciador de cluster de failover**, verifique se todos os recursos de cluster principais estão na primeira máquina virtual. Se necessário, mova todos os recursos para esta máquina virtual.

1. Localize a mídia de instalação. Se a máquina virtual usar uma das imagens do Azure Marketplace, a mídia estará localizada em `C:\SQLServer_<version number>_Full`. Selecione **instalação**.

1. Na **central de instalação do SQL Server**, selecione **instalação**.

1. Selecione **novo SQL Server instalação de cluster de failover**. Siga as instruções no Assistente para instalar o SQL Server FCI.

   Os diretórios de dados do FCI precisam estar no compartilhamento de arquivos premium. Insira o caminho completo do compartilhamento, neste formulário: `\\storageaccountname.file.core.windows.net\filesharename\foldername`. Um aviso será exibido, informando que você especificou um servidor de arquivos como o diretório de dados. Esse aviso é esperado. Verifique se a conta com a qual você manteve o compartilhamento de arquivos é a mesma conta que o serviço SQL Server usa para evitar possíveis falhas.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/use-file-share-as-data-directories.png" alt-text="Usar o compartilhamento de arquivos como diretórios de dados SQL":::

1. Depois de concluir as etapas no assistente, a instalação instalará um SQL Server FCI no primeiro nó.

1. Após a instalação instalar o FCI no primeiro nó, conecte-se ao segundo nó usando o RDP.

1. Abra a **central de instalação do SQL Server**. Selecione **instalação**.

1. Selecione **adicionar nó a um cluster de failover SQL Server**. Siga as instruções no Assistente para instalar SQL Server e adicionar o servidor ao FCI.

   >[!NOTE]
   >Se você usou uma imagem da galeria do Azure Marketplace com SQL Server, SQL Server ferramentas foram incluídas com a imagem. Se você não usou uma dessas imagens, instale as ferramentas de SQL Server separadamente. Consulte [baixar SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-the-azure-load-balancer"></a>Etapa 6: criar o Azure Load Balancer

Em máquinas virtuais do Azure, os clusters usam um balanceador de carga para manter um endereço IP que precisa estar em um nó de cluster por vez. Nessa solução, o balanceador de carga mantém o endereço IP para o SQL Server FCI.

Para obter mais informações, consulte [criar e configurar um balanceador de carga do Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Criar o balanceador de carga no portal do Azure

Para criar o balanceador de carga:

1. Na portal do Azure, vá para o grupo de recursos que contém as máquinas virtuais.

1. Selecione **Adicionar**. Pesquise **Load Balancer**no Azure Marketplace. Selecione **Load Balancer**.

1. Selecione **Criar**.

1. Configure o balanceador de carga usando os seguintes valores:

   - **Assinatura**: sua assinatura do Azure.
   - **Grupo de recursos**: o grupo de recursos que contém suas máquinas virtuais.
   - **Nome**: um nome que identifica o balanceador de carga.
   - **Região**: o local do Azure que contém suas máquinas virtuais.
   - **Tipo**: público ou privado. Um balanceador de carga privado pode ser acessado de dentro da rede virtual. A maioria dos aplicativos do Azure pode usar um balanceador de carga privado. Se seu aplicativo precisar de acesso a SQL Server diretamente pela Internet, use um balanceador de carga público.
   - **SKU**: Standard.
   - **Rede virtual**: a mesma rede que as máquinas virtuais.
   - **Atribuição de endereço IP**: estática. 
   - **Endereço IP privado**: o endereço IP que você atribuiu ao recurso de rede de cluster SQL Server FCI.

   A imagem a seguir mostra a interface de usuário **criar balanceador de carga** :

   ![Configurar o balanceador de carga](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>Configurar o pool de back-end do balanceador de carga

1. Retorne ao grupo de recursos do Azure que contém as máquinas virtuais e localize o novo balanceador de carga. Talvez seja necessário atualizar a exibição no grupo de recursos. Selecione o balanceador de carga.

1. Selecione **pools de back-end**e, em seguida, selecione **Adicionar**.

1. Associe o pool de back-end ao conjunto de disponibilidade que contém as VMs.

1. Em **configurações de IP de rede de destino**, selecione **máquina virtual** e escolha as máquinas virtuais que participarão como nós de cluster. Certifique-se de incluir todas as máquinas virtuais que hospedarão o FCI.

1. Selecione **OK** para criar o pool de back-end.

### <a name="configure-a-load-balancer-health-probe"></a>Configurar uma pesquisa de estado de funcionamento do balanceador de carga

1. Na folha balanceador de carga, selecione **investigações de integridade**.

1. Selecione **Adicionar**.

1. Na folha **Adicionar investigação de integridade** , <a name="probe"> </a>defina os parâmetros de investigação de integridade a seguir.

   - **Nome**: um nome para a investigação de integridade.
   - **Protocolo**: TCP.
   - **Porta**: a porta que você criou no firewall para a investigação de integridade nesta [etapa](#ports). Neste artigo, o exemplo usa a porta TCP `59999`.
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

## <a name="step-7-configure-the-cluster-for-the-probe"></a>Etapa 7: configurar o cluster para a investigação

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

## <a name="step-8-test-fci-failover"></a>Etapa 8: testar o failover do FCI

Teste o failover do FCI para validar a funcionalidade do cluster. Siga os passos seguintes:

1. Conecte-se a um dos nós de cluster SQL Server FCI usando o RDP.

1. Abra **Gerenciador de cluster de failover**. Selecione **funções**. Observe qual nó possui a função SQL Server FCI.

1. Clique com o botão direito do mouse na função SQL Server FCI.

1. Selecione **mover**e, em seguida, selecione o **melhor nó possível**.

**Gerenciador de cluster de failover** mostra a função e seus recursos ficam offline. Os recursos são movidos e ficam online novamente no outro nó.

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

- [Tecnologias de cluster do Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server instâncias de cluster de failover](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
