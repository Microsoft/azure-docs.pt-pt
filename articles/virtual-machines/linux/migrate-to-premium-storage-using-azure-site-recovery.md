---
title: Migre suas VMs do Linux para o armazenamento Premium do Azure com Azure Site Recovery
description: Migre suas máquinas virtuais existentes para o armazenamento Premium do Azure usando Site Recovery. O armazenamento Premium oferece suporte a disco de alto desempenho e baixa latência para cargas de trabalho com uso intensivo de e/s em execução em máquinas virtuais do Azure.
services: virtual-machines-linux,storage
cloud: Azure
author: luywang
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: b8c299869da003d8dc4e9ac111ba8c5e8bb27345
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035833"
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migrar para o armazenamento Premium usando Azure Site Recovery

O [SSDs Premium do Azure](disks-types.md) fornece suporte de disco de alto desempenho e baixa latência para VMs (máquinas virtuais) que executam cargas de trabalho com e/s intensivas. Este guia ajuda a migrar seus discos de VM de uma conta de armazenamento Standard para uma conta de armazenamento Premium usando [Azure site Recovery](../../site-recovery/site-recovery-overview.md).

Site Recovery é um serviço do Azure que contribui para sua estratégia de continuidade dos negócios e recuperação de desastres, orquestrando a replicação de servidores físicos locais e VMs para a nuvem (Azure) ou para um datacenter secundário. Quando ocorrem paralisações em seu local primário, você faz failover para o local secundário para manter os aplicativos e as cargas de trabalho disponíveis. Você faz failback para o local principal quando ele retorna à operação normal. 

Site Recovery fornece failovers de teste para dar suporte a análises de recuperação de desastre sem afetar os ambientes de produção. Você pode executar failovers com perda mínima de dados (dependendo da frequência de replicação) para desastres inesperados. No cenário de migração para o armazenamento Premium, você pode usar o [failover em site Recovery](../../site-recovery/site-recovery-failover.md) para migrar os discos de destino para uma conta de armazenamento Premium.

É recomendável migrar para o armazenamento Premium usando Site Recovery, pois essa opção fornece tempo de inatividade mínimo. Essa opção também evita a execução manual da cópia de discos e a criação de novas VMs. Site Recovery irá, sistematicamente, copiar seus discos e criar novas VMs durante o failover. 

O Site Recovery dá suporte a vários tipos de failover com pouco ou nenhum tempo de inatividade. Para planejar o tempo de inatividade e estimar a perda de dados, consulte os [tipos de failover em site Recovery](../../site-recovery/site-recovery-failover.md). Se você se [preparar para se conectar às VMs do Azure após o failover](../../site-recovery/vmware-walkthrough-overview.md), você deverá ser capaz de se conectar à VM do Azure usando o RDP após o failover.

![Diagrama de recuperação de desastre][1]

## <a name="azure-site-recovery-components"></a>Componentes do Azure Site Recovery

Esses Site Recovery componentes são relevantes para este cenário de migração:

* O **servidor de configuração** é uma VM do Azure que coordena a comunicação e gerencia os processos de replicação e recuperação de dados. Nessa VM, você executa um único arquivo de instalação para instalar o servidor de configuração e um componente adicional, chamado de servidor de processo, como um gateway de replicação. Leia sobre os [pré-requisitos do servidor de configuração](../../site-recovery/vmware-walkthrough-overview.md). Você define o servidor de configuração apenas uma vez e pode usá-lo para todas as migrações para a mesma região.

* O **servidor de processo** é um gateway de replicação que: 

  1. Recebe dados de replicação de VMs de origem.
  2. Otimiza os dados com caching, compactação e criptografia.
  3. Envia os dados para uma conta de armazenamento. 

  Ele também trata da instalação por push do serviço de mobilidade para VMs de origem e executa a descoberta automática de VMs de origem. O servidor de processo padrão é instalado no servidor de configuração. Você pode implantar servidores de processo autônomos adicionais para dimensionar sua implantação. Leia sobre [as práticas recomendadas para a implantação do servidor de processo](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) e [implantação de servidores de processo adicionais](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Você configura o servidor de processo apenas uma vez e pode usá-lo para todas as migrações para a mesma região.

* O **serviço de mobilidade** é um componente que é implantado em cada VM padrão que você deseja replicar. Ele captura gravações de dados na VM padrão e as encaminha para o servidor de processo. Leia sobre os [pré-requisitos de máquina replicados](../../site-recovery/vmware-walkthrough-overview.md).

Este gráfico mostra como esses componentes interagem:

![Interação dos componentes do Site Recovery][15]

> [!NOTE]
> Site Recovery não oferece suporte à migração de discos de espaços de armazenamento.

Para componentes adicionais para outros cenários, consulte [arquitetura do cenário](../../site-recovery/vmware-walkthrough-overview.md).

## <a name="azure-essentials"></a>Fundamentos do Azure

Estes são os requisitos do Azure para este cenário de migração:

* Uma subscrição do Azure.
* Uma conta de armazenamento Premium do Azure para armazenar dados replicados.
* Uma rede virtual do Azure à qual as VMs serão conectadas quando forem criadas no failover. A rede virtual do Azure deve estar na mesma região que aquela em que Site Recovery é executado.
* Uma conta de armazenamento standard do Azure para armazenar logs de replicação. Essa pode ser a mesma conta de armazenamento para os discos de VM que estão sendo migrados.

## <a name="prerequisites"></a>Pré-requisitos

* Entenda os componentes relevantes do cenário de migração na seção anterior.
* Planeje seu tempo de inatividade aprendendo sobre [failover em site Recovery](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Etapas de instalação e migração

Você pode usar Site Recovery para migrar VMs IaaS do Azure entre regiões ou na mesma região. As instruções a seguir são adaptadas para esse cenário de migração do artigo [replicar VMs VMware ou servidores físicos para o Azure](../../site-recovery/vmware-walkthrough-overview.md). Siga os links para obter as etapas detalhadas, além das instruções neste artigo.

### <a name="step-1-create-a-recovery-services-vault"></a>Etapa 1: criar um cofre dos serviços de recuperação

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Selecione **criar um recurso** > **Gerenciamento** > **backup** e **site Recovery (OMS)** . Como alternativa, você pode selecionar **procurar** > **cofre dos serviços de recuperação** > **Adicionar**. 
3. Especifique uma região para a qual as VMs serão replicadas. Para fins de migração na mesma região, selecione a região onde as suas VMs de origem e as contas de armazenamento de origem são. 

### <a name="step-2-choose-your-protection-goals"></a>Etapa 2: escolher suas metas de proteção 

1. Na VM em que você deseja instalar o servidor de configuração, abra o [portal do Azure](https://portal.azure.com).
2. Vá para **cofres dos serviços de recuperação** > **configurações** > **site Recovery** > **etapa 1: preparar a infraestrutura** > meta de **proteção**.

   ![Navegando até o painel meta de proteção][2]

3. Em **meta de proteção**, na primeira lista suspensa, selecione para o **Azure**. Na segunda lista suspensa, selecione **não virtualizado/outro**e, em seguida, selecione **OK**.

   ![Painel de meta de proteção com caixas preenchidas][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Etapa 3: configurar o ambiente de origem (servidor de configuração)

1. Baixe **Azure site Recovery configuração unificada** e a chave de registro do cofre acessando os painéis **preparar a infraestrutura** > **preparar fonte** > **Adicionar servidor** . 
 
   Você precisará da chave de registro do cofre para executar a configuração unificada. A chave é válida durante cinco dias depois de gerá-la.

   ![Navegando até o painel Adicionar servidor][4]

2. No painel **Adicionar servidor** , adicione um servidor de configuração.

   ![Adicionar painel de servidor com o servidor de configuração selecionado][5]

3. Na VM que você está usando como o servidor de configuração, execute a instalação unificada para instalar o servidor de configuração e o servidor de processo. Você pode [percorrer as capturas de tela](../../site-recovery/vmware-walkthrough-overview.md) para concluir a instalação. Você pode consultar as seguintes capturas de tela para obter as etapas especificadas para esse cenário de migração.

   1. Em **antes de começar**, selecione **instalar o servidor de configuração e o servidor de processo**.

      ![Antes de começar a página][6]

   2. Em **registro**, procure e selecione a chave de registro que você baixou do cofre.

      ![Página de registro][7]

   3. Em **Detalhes do ambiente**, selecione se pretende replicar VMs do VMware. Para esse cenário de migração, escolha **não**.

      ![Página de detalhes do ambiente][8]

4. Depois que a instalação for concluída, faça o seguinte na janela **Microsoft Azure site Recovery Configuration Server** :
 
   1. Use a guia **gerenciar contas** para criar a conta que site Recovery pode usar para a descoberta automática. (No cenário de proteção de computadores físicos, a configuração da conta não é relevante, mas você precisa de pelo menos uma conta para habilitar uma das etapas a seguir. Nesse caso, você pode nomear a conta e a senha como qualquer.) 
   2. Use a guia **registro do cofre** para carregar o arquivo de credencial do cofre.

      ![Guia de registro do cofre][9]

### <a name="step-4-set-up-the-target-environment"></a>Etapa 4: configurar o ambiente de destino

Selecione **preparar infraestrutura** > **destino**e especifique o modelo de implantação que você deseja usar para VMs após o failover. Você pode escolher o **clássico** ou o **Gerenciador de recursos**, dependendo do seu cenário.

![Painel de destino][10]

A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis. 

> [!NOTE]
> Se você estiver usando uma conta de armazenamento Premium para dados replicados, precisará configurar uma conta de armazenamento padrão adicional para armazenar os logs de replicação.

### <a name="step-5-set-up-replication-settings"></a>Etapa 5: definir as configurações de replicação

Para verificar se o servidor de configuração está associado com êxito à política de replicação que você criou, siga [definir as configurações de replicação](../../site-recovery/vmware-walkthrough-overview.md).

### <a name="step-6-plan-capacity"></a>Etapa 6: planejar a capacidade

1. Use o [planejador de capacidade](../../site-recovery/site-recovery-capacity-planner.md) para estimar com precisão a largura de banda de rede, o armazenamento e outros requisitos para atender às suas necessidades de replicação. 
2. Quando terminar, selecione **Sim, já fiz isso** em **você concluiu o planejamento de capacidade?** .

   ![Caixa para confirmar que você concluiu o planejamento de capacidade][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Etapa 7: instalar o serviço de mobilidade e habilitar a replicação

1. Você pode optar [por enviar por push a instalação](../../site-recovery/vmware-walkthrough-overview.md) para suas VMs de origem ou [instalar manualmente o serviço de mobilidade](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) em suas VMs de origem. Você pode encontrar o requisito de instalação por push e o caminho do instalador manual no link fornecido. Se você estiver fazendo uma instalação manual, talvez seja necessário usar um endereço IP interno para localizar o servidor de configuração.

   ![Página de detalhes do servidor de configuração][12]

   A VM com failover terá dois discos temporários: um da VM primária e o outro criado durante o provisionamento da VM na região de recuperação. Para excluir o disco temporário antes da replicação, instale o serviço de mobilidade antes de habilitar a replicação. Para saber mais sobre como excluir o disco temporário, confira [excluir discos da replicação](../../site-recovery/vmware-walkthrough-overview.md).

2. Ative a replicação da seguinte forma:
   1. Selecione **replicar aplicativo** > **origem**. Depois de habilitar a replicação pela primeira vez, selecione **+ replicar** no cofre para habilitar a replicação para computadores adicionais.
   2. Na etapa 1, configure a **origem** como seu servidor de processo.
   3. Na etapa 2, especifique o modelo de implantação após o failover, uma conta de armazenamento Premium para migrar para o, uma conta de armazenamento padrão para salvar logs e uma rede virtual para falha.
   4. Na etapa 3, adicione VMs protegidas por endereço IP. (Talvez você precise de um endereço IP interno para encontrá-los.)
   5. Na etapa 4, configure as propriedades selecionando as contas que você configurou anteriormente no servidor de processo.
   6. Na etapa 5, escolha a política de replicação que você criou anteriormente em "etapa 5: definir as configurações de replicação".
   7. Selecione **OK**.

   > [!NOTE]
   > Quando uma VM do Azure é desalocada e iniciada novamente, não há nenhuma garantia de que ela receberá o mesmo endereço IP. Se o endereço IP do servidor de configuração/do servidor de processo ou as VMs do Azure protegidas forem alteradas, a replicação nesse cenário poderá não funcionar corretamente.

   ![Habilitar o painel de replicação com a origem selecionada][13]

Ao projetar seu ambiente de armazenamento do Azure, recomendamos que você use contas de armazenamento separadas para cada VM em um conjunto de disponibilidade. Recomendamos que você siga a prática recomendada na camada de armazenamento para [usar várias contas de armazenamento para cada conjunto de disponibilidade](../linux/manage-availability.md). A distribuição de discos de VM para várias contas de armazenamento ajuda a melhorar a disponibilidade do armazenamento e distribui a e/s na infraestrutura de armazenamento do Azure.

Se suas VMs estiverem em um conjunto de disponibilidade, em vez de replicar discos de todas as VMs em uma conta de armazenamento, é altamente recomendável migrar várias VMs várias vezes. Dessa forma, as VMs no mesmo conjunto de disponibilidade não compartilham uma única conta de armazenamento. Use o painel **habilitar replicação** para configurar uma conta de armazenamento de destino para cada VM, uma de cada vez.
 
Você pode escolher um modelo de implantação de pós-failover de acordo com sua necessidade. Se você escolher Azure Resource Manager como seu modelo de implantação de pós-failover, poderá fazer failover de uma VM (Resource Manager) para uma VM (Resource Manager) ou poderá fazer failover de uma VM (clássica) para uma VM (Gerenciador de recursos).

### <a name="step-8-run-a-test-failover"></a>Etapa 8: executar um failover de teste

Para verificar se a replicação foi concluída, selecione sua instância do Site Recovery e, em seguida, selecione **configurações** > **itens replicados**. Você verá o status e a porcentagem do processo de replicação. 

Após a conclusão da replicação inicial, execute um failover de teste para validar sua estratégia de replicação. Para obter as etapas detalhadas de um failover de teste, consulte [executar um failover de teste no site Recovery](../../site-recovery/vmware-walkthrough-overview.md). 

> [!NOTE]
> Antes de executar qualquer failover, verifique se as VMs e a estratégia de replicação atendem aos requisitos. Para obter mais informações sobre como executar um failover de teste, consulte [failover de teste para o Azure em site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

Você pode ver o status do failover de teste em **configurações** > **trabalhos** > *YOUR_FAILOVER_PLAN_NAME*. No painel, você pode ver uma análise das etapas e os resultados de êxito/falha. Se o failover de teste falhar em qualquer etapa, selecione a etapa para verificar a mensagem de erro. 

### <a name="step-9-run-a-failover"></a>Etapa 9: executar um failover

Depois que o failover de teste for concluído, execute um failover para migrar seus discos para o armazenamento Premium e replicar as instâncias de VM. Siga as etapas detalhadas em [executar um failover](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Certifique-se de selecionar **desligar VMs e sincronizar os dados mais recentes**. Essa opção especifica que Site Recovery deve tentar desligar as VMs protegidas e sincronizar os dados para que a versão mais recente dos dados seja reprovada. Se você não selecionar essa opção ou se a tentativa não for concluída, o failover será do ponto de recuperação mais recente disponível para a VM. 

Site Recovery criará uma instância de VM cujo tipo é igual ou semelhante a uma VM com capacidade de armazenamento Premium. Você pode verificar o desempenho e o preço de várias instâncias de VM acessando [máquinas virtuais do Windows preço](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) ou [máquinas virtuais do Linux preço](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Etapas pós-migração

1. **Configure VMs replicadas para o conjunto de disponibilidade, se aplicável**. Site Recovery não oferece suporte à migração de VMs junto com o conjunto de disponibilidade. Dependendo da implantação da VM replicada, siga um destes procedimentos:
   * Para uma VM criada por meio do modelo de implantação clássico: Adicione a VM ao conjunto de disponibilidade no portal do Azure. Para obter etapas detalhadas, vá para [Adicionar uma máquina virtual existente a um conjunto de disponibilidade](../linux/classic/configure-availability-classic.md).
   * Para uma VM criada por meio do modelo de implantação do Gerenciador de recursos: Salve a configuração da VM e, em seguida, exclua e recrie as VMs no conjunto de disponibilidade. Para fazer isso, use o script em [conjunto Azure Resource Manager conjunto de disponibilidade da VM](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Antes de executar esse script, verifique suas limitações e planeje o tempo de inatividade.

2. **Exclua VMs e discos antigos**. Verifique se os discos Premium estão consistentes com os discos de origem e se as novas VMs executam a mesma função que as VMs de origem. Exclua a VM e exclua os discos das contas de armazenamento de origem no portal do Azure. Se houver um problema em que o disco não seja excluído mesmo que você tenha excluído a VM, consulte [solucionar problemas de erros de exclusão de recursos de armazenamento](storage-resource-deletion-errors.md).

3. **Limpe a infraestrutura de Azure site Recovery**. Se Site Recovery não for mais necessária, você poderá limpar sua infraestrutura. Exclua itens replicados, o servidor de configuração e a política de recuperação e, em seguida, exclua o cofre de Azure Site Recovery.

## <a name="troubleshooting"></a>Resolução de problemas

* [Monitorar e solucionar problemas de proteção para máquinas virtuais e servidores físicos](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Fórum de Site Recovery de Microsoft Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Passos seguintes

Para cenários específicos de migração de máquinas virtuais, consulte os seguintes recursos:

* [Migrar máquinas virtuais do Azure entre contas de armazenamento](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Carregar um disco rígido virtual do Linux](upload-vhd.md)
* [Migrando máquinas virtuais do Amazon AWS para Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Além disso, consulte os seguintes recursos para saber mais sobre o armazenamento do Azure e as máquinas virtuais do Azure:

* [Storage do Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Selecionar um tipo de disco para VMs IaaS](disks-types.md)

[1]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png
