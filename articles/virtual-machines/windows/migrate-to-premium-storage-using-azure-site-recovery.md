---
title: Migrar as suas VMs do Windows para o armazenamento Premium do Azure com o Azure Site Recovery | Documentos da Microsoft
description: Migre as suas máquinas virtuais existentes para o armazenamento Premium do Azure utilizando a recuperação de Site. O armazenamento Premium oferece suporte de disco de elevado desempenho e de baixa latência para cargas de trabalho de e/S intensivas executadas em máquinas de virtuais do Azure.
services: virtual-machines-windows,storage
cloud: Azure
author: luywang
ms.service: virtual-machines-windows
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: 5744ee76d36b1cea256cd2594bcbc07c954f38f7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993290"
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migrar para o armazenamento Premium com o Azure Site Recovery

[Premium do Azure SSDs](disks-types.md) fornecer suporte de disco de elevado desempenho e de baixa latência para máquinas virtuais (VMs) que estejam a executar cargas de trabalho de e/S intensivas. Este guia ajuda-o a migrar os discos da VM a partir de uma conta de armazenamento standard para uma conta de armazenamento premium, utilizando [do Azure Site Recovery](../../site-recovery/site-recovery-overview.md).

Recuperação de sites é um serviço do Azure que contribui para a sua estratégia de continuidade do negócio e recuperação após desastre ao orquestrar a replicação de VMs e servidores físicos no local para a nuvem (Azure) ou num Datacenter secundário. Quando ocorrem falhas na sua localização principal, ativação pós-falha para a localização secundária para manter as aplicações e cargas de trabalho disponíveis. Reativação pós-falha para a sua localização principal ao retornar ao funcionamento normal. 

Recuperação de sites fornece ativações pós-falha de teste para oferecer suporte a testes de recuperação após desastre sem afetar os ambientes de produção. Pode executar ativações pós-falha com perda mínima de dados (dependendo da frequência de replicação) perante desastres inesperados. No cenário de migração para o armazenamento Premium, pode utilizar o [ativação pós-falha no Site Recovery](../../site-recovery/site-recovery-failover.md) para migrar discos de destino para uma conta de armazenamento premium.

Recomendamos que migre para o armazenamento Premium com o Site Recovery uma vez que esta opção fornece o período de indisponibilidade mínimo. Esta opção também evita a execução manual de copiar os discos e criar novas VMs. Site Recovery sistematicamente irá copiar os discos e criar novas VMs durante a ativação pós-falha. 

Site Recovery suporta um número de tipos de ativação pós-falha com o mínimo ou nenhum tempo de inatividade. Planear o seu tempo de inatividade e estimar a perda de dados, consulte a [tipos de ativação pós-falha no Site Recovery](../../site-recovery/site-recovery-failover.md). Se [preparar a ligação para as VMs do Azure após a ativação pós-falha](../../site-recovery/vmware-walkthrough-overview.md), deverá conseguir ligar à VM do Azure com RDP após a ativação pós-falha.

![Diagrama de recuperação após desastre][1]

## <a name="azure-site-recovery-components"></a>Componentes da recuperação de sites do Azure

Esses componentes do Site Recovery são relevantes para este cenário de migração:

* **Servidor de configuração** é uma VM do Azure que coordena a comunicação e gere processos de replicação e a recuperação de dados. Nesta VM, o que execute um ficheiro de configuração individual para instalar o servidor de configuração e um componente adicional, chamado de um servidor de processos, como um gateway de replicação. Leia sobre [pré-requisitos de servidor de configuração](../../site-recovery/vmware-walkthrough-overview.md). Configurar o servidor de configuração apenas uma vez, e pode usá-lo para todas as migrações para a mesma região.

* **Servidor de processos** é um gateway de replicação que: 

  1. Recebe dados de replicação de VMs de origem.
  2. Otimiza os dados com a colocação em cache, compressão e encriptação.
  3. Envia os dados para uma conta de armazenamento. 

  Também processa a instalação push do serviço de mobilidade para VMs de origem e efetua a deteção automática de VMs de origem. O servidor de processos de predefinição está instalado no servidor de configuração. Pode implementar servidores de processo de autónomos adicionais para dimensionar a sua implementação. Leia sobre [melhores práticas para a implementação de servidor de processo](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) e [implementar servidores de processos adicionais](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Configurar o servidor de processos apenas uma vez, e pode usá-lo para todas as migrações para a mesma região.

* **O serviço de mobilidade** é um componente que é implementado em cada VM padrão que pretende replicar. Ele obtém dados, escreve na standard VM e reencaminha-os para o servidor de processos. Leia sobre [replicados pré-requisitos de máquina](../../site-recovery/vmware-walkthrough-overview.md).

Este gráfico mostra como esses componentes interagem:

![Interação de componentes do Site Recovery][15]

> [!NOTE]
> Recuperação de sites não suporta a migração de discos de espaços de armazenamento.

Para obter componentes adicionais para outros cenários, consulte [arquitetura do cenário](../../site-recovery/vmware-walkthrough-overview.md).

## <a name="azure-essentials"></a>O Azure essentials

Estes são os requisitos do Azure para este cenário de migração:

* Uma subscrição do Azure.
* Uma conta de armazenamento premium do Azure para armazenar dados replicados.
* Uma rede virtual do Azure à qual se ligarão as VMs quando ocorrer a ativação pós-falha. A rede virtual do Azure tem de ser na mesma região que aquela em que o Site Recovery é executado.
* Uma conta de armazenamento standard do Azure para armazenar os registos de replicação. Isso pode ser a mesma conta de armazenamento para os discos VM que estão a ser migrados.

## <a name="prerequisites"></a>Pré-requisitos

* Compreenda os componentes de cenário de migração relevantes na secção anterior.
* Planear o seu tempo de inatividade, aprendendo sobre [ativação pós-falha no Site Recovery](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Passos de configuração e migração

Pode utilizar o Site Recovery para migrar VMs IaaS do Azure entre regiões ou dentro da mesma região. As instruções seguintes são adaptadas para este cenário de migração do artigo [replicar VMs de VMware ou servidores físicos para o Azure](../../site-recovery/vmware-walkthrough-overview.md). Siga as ligações para obter passos detalhados, além das instruções neste artigo.

### <a name="step-1-create-a-recovery-services-vault"></a>Passo 1: Criar um cofre dos Serviços de Recuperação 

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Selecione **criar um recurso** > **gestão** > **cópia de segurança e Site Recovery (OMS)**. Em alternativa, pode selecionar **navegue** > **cofre dos Recovery Services** > **adicionar**.
   >[!NOTE]
   >Cópia de segurança e recuperação de sites foi anteriormente conhecido como parte dos ![OMS suite](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand).
1. Especifique uma região que as VMs serão replicadas. Para fins de migração na mesma região, selecione a região onde estão as VMs de origem e as contas de armazenamento de origem. 

### <a name="step-2-choose-your-protection-goals"></a>Passo 2: Escolha os seus objetivos de proteção 

1. Na VM onde pretende instalar o servidor de configuração, abra a [portal do Azure](https://portal.azure.com).
2. Aceda a **cofres dos serviços de recuperação** > **definições** > **Site Recovery** > **passo 1: Preparar infraestrutura** > **objetivo de proteção**.

   ![Navegar para o painel objetivo de proteção][2]

3. Sob **objetivo de proteção**, na primeira lista pendente, selecione **para o Azure**. Na segunda lista pendente, selecione **não virtualizado / outro**e, em seguida, selecione **OK**.

   ![Painel de objetivo de proteção com caixas de preenchidos][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Passo 3: Configurar o ambiente de origem (servidor de configuração)

1. Baixe **do Azure Site Recovery Unified Setup** e a chave de registo do cofre ao aceder a **preparar a infraestrutura** > **preparar origem**  >  **Adicionar servidor** painéis. 
 
   Terá a chave de registo do cofre para executar a configuração unificada. A chave é válida durante cinco dias depois de gerá-la.

   ![Navegar para o painel Adicionar servidor][4]

2. Na **Adicionar servidor** painel, adicionar um servidor de configuração.

   ![Adicionar o painel de servidor com o servidor de configuração selecionado][5]

3. Na VM que está a utilizar como o servidor de configuração, execute a configuração de unificação de mensagens em fila para instalar o servidor de configuração e o servidor de processos. Pode [percorrer as capturas de ecrã](../../site-recovery/vmware-walkthrough-overview.md) para concluir a instalação. Pode consultar as capturas de ecrã seguintes para obter os passos especificados para este cenário de migração.

   1. Na **antes de começar**, selecione **instalar o servidor de configuração e o servidor de processos**.

      ![Antes de iniciar a página][6]

   2. Na **registo**, navegue e selecione a chave de registo que transferiu a partir do cofre.

      ![Página de registo][7]

   3. Em **Detalhes do ambiente**, selecione se pretende replicar VMs do VMware. Para este cenário de migração, escolha **não**.

      ![Página de detalhes do ambiente][8]

4. Depois de concluída a instalação, efetue o seguinte procedimento **o servidor de configuração do Microsoft Azure Site Recovery** janela:
 
   1. Utilizar o **gerir contas** separador para criar a conta desse Site Recovery pode utilizar para a deteção automática. (No cenário sobre como proteger máquinas físicas, como configurar a conta não é relevante, mas precisa de pelo menos uma conta para ativar um dos seguintes passos. Neste caso, pode nomear a conta e palavra-passe como qualquer.) 
   2. Utilize o **registo de cofre** separador para carregar o ficheiro de credenciais do cofre.

      ![Separador registo do Cofre][9]

### <a name="step-4-set-up-the-target-environment"></a>Passo 4: Configurar o ambiente de destino

Selecione **preparar infraestrutura** > **destino**e especifique o modelo de implementação que pretende utilizar para as VMs após a ativação pós-falha. Pode escolher **clássica** ou **Resource Manager**, dependendo do seu cenário.

![Painel de destino][10]

A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis. 

> [!NOTE]
> Se estiver a utilizar uma conta de armazenamento premium para os dados replicados, terá de configurar uma conta de armazenamento standard adicional para armazenar os registos de replicação.

### <a name="step-5-set-up-replication-settings"></a>Passo 5: Configurar as definições de replicação

Para verificar se o seu servidor de configuração está com êxito associado à política de replicação que criar, siga [configurar as definições de replicação](../../site-recovery/vmware-walkthrough-overview.md).

### <a name="step-6-plan-capacity"></a>Passo 6: Planear a capacidade

1. Utilize o [Planeador de capacidade](../../site-recovery/site-recovery-capacity-planner.md) calcular com precisão a largura de banda de rede, armazenamento e outros requisitos para satisfazer a replicação precisa. 
2. Quando tiver terminado, selecione **Sim, já o fiz** na **concluiu o planeamento de capacidade?**.

   ![Caixa para confirmar que concluiu o planeamento de capacidade][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Passo 7: Instalar o serviço de mobilidade e ativar a replicação

1. Pode optar por [instalação push](../../site-recovery/vmware-walkthrough-overview.md) às suas VMs de origem ou a [instalar manualmente o serviço de mobilidade](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) sobre as VMs de origem. Pode encontrar o requisito de envio por push de instalação e o caminho do programa de instalação manual na ligação fornecida. Se estiver fazendo uma instalação manual, poderá ter de utilizar um endereço IP interno para localizar o servidor de configuração.

   ![Página de detalhes do servidor de configuração][12]

   A VM com ativação pós-falha terá dois discos temporários: um da VM principal e o outro criado durante o aprovisionamento da VM na região de recuperação. Para excluir o disco temporário antes da replicação, instale o serviço de mobilidade, antes de ativar a replicação. Para saber mais sobre como excluir o disco temporário, veja [excluir discos da replicação](../../site-recovery/vmware-walkthrough-overview.md).

2. Ative a replicação da seguinte forma:
   1. Selecione **replicar aplicação** > **origem**. Depois de ativar a replicação pela primeira vez, selecione **+ replicar** no cofre para ativar a replicação para máquinas adicionais.
   2. No passo 1, configure **origem** como seu servidor de processos.
   3. Passo 2, especifique o modelo de implementação após a ativação pós-falha, uma conta de armazenamento premium para migrar para o, uma conta de armazenamento standard para guardar os registos e uma rede virtual para efetuar a ativação.
   4. Passo 3, adicione VMs protegidas por endereço IP. (Poderá ter um endereço IP interno para encontrá-los.)
   5. Passo 4, configure as propriedades ao selecionar as contas que configurou anteriormente no servidor de processos.
   6. No passo 5, escolha a política de replicação que criou anteriormente em "passo 5: Configurar as definições de replicação."
   7. Selecione **OK**.

   > [!NOTE]
   > Quando uma VM do Azure é desalocada e iniciada novamente, não é garantido que obterá o mesmo endereço IP. Se o endereço IP do servidor de processo do servidor de configuração ou as VMs do Azure protegido for alterado, a replicação neste cenário poderão não funcionar corretamente.

   ![Ativar o painel de replicação com origem selecionada][13]

Ao projetar o seu ambiente de armazenamento do Azure, recomendamos que utilize contas de armazenamento separadas para cada VM num conjunto de disponibilidade. Recomendamos que siga o procedimento recomendado na camada de armazenamento para [utilizar várias contas de armazenamento para cada conjunto de disponibilidade](../linux/manage-availability.md). Distribuição de discos VM para várias contas de armazenamento ajuda a melhorar a disponibilidade de armazenamento e distribui a e/s em toda a infraestrutura de armazenamento do Azure.

Se as VMs estiverem num conjunto de disponibilidade, em vez de replicar discos de todas as VMs numa conta de armazenamento, recomendamos vivamente a migrar várias VMs várias vezes. Dessa forma, as VMs no mesmo conjunto de disponibilidade não partilham uma única conta de armazenamento. Utilize o **ativar a replicação** painel para configurar uma conta de armazenamento de destino para cada VM, uma de cada vez.
 
Pode escolher um modelo de implementação após a ativação pós-falha, de acordo com suas necessidades. Se escolher o Azure Resource Manager como seu modelo de implementação após a ativação pós-falha, pode efetuar a ativação pós-falha numa VM (Resource Manager) para uma VM (Resource Manager) ou pode efetuar a ativação pós-falha numa VM (clássica) a uma VM (Resource Manager).

### <a name="step-8-run-a-test-failover"></a>Passo 8: Executar uma ativação pós-falha de teste

Para verificar se a replicação estiver concluída, selecione a sua instância do Site Recovery e, em seguida, selecione **configurações** > **itens replicados**. Verá o estado e a percentagem de seu processo de replicação. 

Após a replicação inicial estiver concluída, execute uma ativação pós-falha de teste para validar a sua estratégia de replicação. Para obter passos detalhados de uma ativação pós-falha de teste, consulte [execute uma ativação pós-falha de teste no Site Recovery](../../site-recovery/vmware-walkthrough-overview.md). 

> [!NOTE]
> Antes de executar qualquer ativação pós-falha, certifique-se de que as VMs e a estratégia de replicação cumprem os requisitos. Para obter mais informações sobre como executar uma ativação pós-falha de teste, consulte [testar a ativação pós-falha para o Azure no Site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

Pode ver o estado de sua ativação pós-falha de teste no **configurações** > **tarefas** > *YOUR_FAILOVER_PLAN_NAME*. No painel, pode ver uma análise detalhada dos passos e resultados de êxito/falha. Se a ativação pós-falha de teste falhar em qualquer passo, selecione o passo para verificar a mensagem de erro. 

### <a name="step-9-run-a-failover"></a>Passo 9: Executar uma ativação pós-falha

Após o teste de ativação pós-falha é concluída, execute uma ativação pós-falha para migrar os seus discos para o armazenamento Premium e replicar as instâncias de VM. Siga os passos detalhados [executar uma ativação pós-falha](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Verifique se seleciona **encerrar VMs e sincronizar os dados mais recentes**. Esta opção especifica que o Site Recovery deve tentar encerrar as VMs protegidas e sincronizar os dados para que a versão mais recente dos dados irá efetuar a ativação pós-falha. Se não selecionar esta opção ou a tentativa não for bem sucedido, a ativação pós-falha será partir do ponto de recuperação disponível mais recente para a VM. 

Recuperação de sites irá criar uma instância de VM cujo tipo é o mesmo como ou semelhante a uma VM com capacidade de armazenamento Premium. Pode verificar o desempenho e preço de várias instâncias de VM ao aceder [preços de máquinas virtuais do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) ou [preços de máquinas virtuais do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Passos de pós-migração

1. **Configurar as VMs replicadas para o conjunto se aplicável de disponibilidade**. Recuperação de sites não suporta a migração de VMs, juntamente com o conjunto de disponibilidade. Consoante a implementação da sua VM replicada, efetue um dos seguintes:
   * Para uma VM criada através do modelo de implementação clássica: Adicione a VM para a conjunto de disponibilidade no portal do Azure. Para obter passos detalhados, consulte [adicionar uma máquina virtual existente a um conjunto de disponibilidade](../linux/classic/configure-availability-classic.md).
   * Para uma VM criada através do modelo de implementação do Resource Manager: Guardar a configuração da VM e, em seguida, eliminar e voltar a criar as VMs no conjunto de disponibilidade. Para tal, utilize o script na [conjunto do Azure Resource Manager VM conjunto de disponibilidade](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Antes de executar este script, verifique suas limitações e planejar seu período de indisponibilidade.

2. **Eliminar antigo VMs e os discos**. Certifique-se de que os discos Premium são consistentes com discos de origem e que as novas VMs realizam a mesma função que as VMs de origem. Elimine a VM e eliminar os discos das suas contas de armazenamento de origem no portal do Azure. Se houver um problema que o disco não é eliminada, mesmo que tenha eliminado a VM, consulte [resolver problemas de erros de eliminação de recursos de armazenamento](storage-resource-deletion-errors.md).

3. **Limpar a infraestrutura do Azure Site Recovery**. Se o Site Recovery já não for necessário, pode limpar a sua infraestrutura. Eliminar itens replicados, o servidor de configuração e a política de recuperação e, em seguida, elimine o cofre do Azure Site Recovery.

## <a name="troubleshooting"></a>Resolução de problemas

* [Monitorizar e resolver problemas de proteção para máquinas virtuais e servidores físicos](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Fórum do Microsoft Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Passos Seguintes

Para cenários específicos para migrar máquinas virtuais, consulte os seguintes recursos:

* [Migrar máquinas virtuais do Azure entre contas de armazenamento](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Criar e carregar um VHD do Windows Server para o Azure](upload-generalized-managed.md)
* [Migração de máquinas virtuais da Amazon AWS para o Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Além disso, consulte os seguintes recursos para saber mais sobre o armazenamento do Azure e máquinas virtuais do Azure:

* [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)

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
