---
title: Migrar os seus VMs Windows para o Armazenamento Premium Azure com recuperação do site Azure
description: Migrar as suas máquinas virtuais existentes para o Armazenamento Azure Premium utilizando a Recuperação do Site. O Premium Storage oferece suporte de disco de alto desempenho e baixa latência para cargas de trabalho intensivas em I/O em execução em Máquinas Virtuais Azure.
services: virtual-machines-windows,storage
cloud: Azure
author: luywang
ms.service: virtual-machines-windows
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: bd5f9fc787a6299e8d7c14f4b99f6f4d59cf78af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74819076"
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migrar para armazenamento premium usando a recuperação do site Azure

Os [SSDs premium Azure](disks-types.md) oferecem suporte de disco de alto desempenho e baixa latência para máquinas virtuais (VMs) que estão a executar cargas de trabalho intensivas em I/O. Este guia ajuda-o a migrar os seus discos VM de uma conta de armazenamento padrão para uma conta de armazenamento premium utilizando a Recuperação do [Site Azure.](../../site-recovery/site-recovery-overview.md)

O Site Recovery é um serviço Azure que contribui para a sua estratégia de continuidade de negócios e recuperação de desastres orquestrando a replicação de servidores físicos no local e VMs para a nuvem (Azure) ou para um centro de dados secundário. Quando ocorrem interrupções na sua localização principal, falha-se no local secundário para manter as aplicações e cargas de trabalho disponíveis. Falha de volta ao seu local principal quando volta ao normal. 

A Recuperação do Local fornece falhas nos testes para apoiar exercícios de recuperação de desastres sem afetar os ambientes de produção. Pode executar falhas com perda mínima de dados (dependendo da frequência de replicação) para desastres inesperados. No cenário de migrar para o Armazenamento Premium, pode utilizar a [falha na Recuperação](../../site-recovery/site-recovery-failover.md) do Site para migrar discos-alvo para uma conta de armazenamento premium.

Recomendamos a migração para o Armazenamento Premium utilizando a Recuperação do Site porque esta opção proporciona o mínimo de tempo de inatividade. Esta opção também evita a execução manual de discos de cópia e criação de novos VMs. A Recuperação do Site copiará sistematicamente os seus discos e criará novos VMs durante a failover. 

A Recuperação do Site suporta uma série de tipos de falhas com o mínimo ou sem tempo de inatividade. Para planear o seu tempo de inatividade e estimar a perda de dados, consulte os [tipos de falha na Recuperação do Site](../../site-recovery/site-recovery-failover.md). Se [se preparar para ligar aos VMs Azure após](../../site-recovery/vmware-walkthrough-overview.md)a falha, deverá ser capaz de ligar ao Azure VM utilizando RDP após a falha.

![Diagrama de recuperação de desastres][1]

## <a name="azure-site-recovery-components"></a>Componentes de recuperação do site Azure

Estes componentes de Recuperação do Site são relevantes para este cenário de migração:

* O servidor de **configuração** é um VM Azure que coordena a comunicação e gere os processos de replicação e recuperação de dados. Neste VM, executa um único ficheiro de configuração para instalar o servidor de configuração e um componente adicional, chamado servidor de processos, como um portal de replicação. Leia sobre os [pré-requisitos](../../site-recovery/vmware-walkthrough-overview.md)do servidor de configuração . Configurao o servidor de configuração apenas uma vez e pode usá-lo para todas as migrações para a mesma região.

* O servidor de **processos** é um portal de replicação que: 

  1. Recebe dados de replicação de VMs de origem.
  2. Otimiza os dados com cache, compressão e encriptação.
  3. Envia os dados para uma conta de armazenamento. 

  Também lida com a instalação de impulso do serviço de mobilidade para obter VMs de origem e realiza a descoberta automática de VMs de origem. O servidor de processo predefinido está instalado no servidor de configuração. Pode implementar servidores de processo sionantes adicionais para escalar a sua implementação. Leia sobre [as melhores práticas para a implementação](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) do servidor de processos e [implemente servidores de processos adicionais](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Configurao o servidor de processos apenas uma vez e pode usá-lo para todas as migrações para a mesma região.

* O serviço de **mobilidade** é um componente que é implantado em todos os VM padrão que pretende replicar. Captura os dados escritos no VM padrão e encaminha-os para o servidor de processos. Leia sobre [os pré-requisitos](../../site-recovery/vmware-walkthrough-overview.md)da máquina replicada .

Este gráfico mostra como estes componentes interagem:

![Interação de componentes de Recuperação do Site][15]

> [!NOTE]
> A Recuperação do Site não suporta a migração de discos espaços de armazenamento.

Para componentes adicionais para outros cenários, consulte [a arquitetura cenário.](../../site-recovery/vmware-walkthrough-overview.md)

## <a name="azure-essentials"></a>Essenciais azure

Estes são os requisitos do Azure para este cenário de migração:

* Uma subscrição do Azure.
* Uma conta de armazenamento premium Azure para armazenar dados replicados.
* Uma rede virtual Azure à qual os VMs se ligarão quando forem criados no failover. A rede virtual Azure deve estar na mesma região em que funciona a Recuperação do Site.
* Uma conta de armazenamento padrão Azure para armazenar registos de replicação. Esta pode ser a mesma conta de armazenamento para os discos VM que estão a ser migrados.

## <a name="prerequisites"></a>Pré-requisitos

* Compreenda os componentes do cenário de migração relevantes na secção anterior.
* Planeie o seu tempo de inatividade aprendendo sobre [o failover na Recuperação do Site.](../../site-recovery/site-recovery-failover.md)

## <a name="setup-and-migration-steps"></a>Etapas de configuração e migração

Pode utilizar a Recuperação do Site para migrar VMs Azure IaaS entre regiões ou dentro da mesma região. As seguintes instruções são adaptadas para este cenário de migração a partir do artigo [Replicate VMware VMs ou servidores físicos para Azure](../../site-recovery/vmware-walkthrough-overview.md). Siga os links para passos detalhados, para além das instruções deste artigo.

### <a name="step-1-create-a-recovery-services-vault"></a>Passo 1: Criar um cofre de serviços de recuperação

1. Abra o [portal Azure.](https://portal.azure.com)
2. Selecione **Criar uma**cópia de segurança de > **gestão** > de recursos e recuperação do**site (OMS)**. Em alternativa, pode selecionar O > **Abóbada** > **Add**de Serviços de Recuperação de **Navegação**.
   >[!NOTE]
   >Backup e Recuperação do Site anteriormente fazia parte da [suite OMS.](/azure/azure-monitor/terminology#april-2018---retirement-of-operations-management-suite-brand)
1. Especifique uma região para a qual os VMs serão replicados. Para efeitos de migração na mesma região, selecione a região onde estão as suas Fontes de VMs e as suas contas de armazenamento de origem. 

### <a name="step-2-choose-your-protection-goals"></a>Passo 2: Escolha os seus objetivos de proteção 

1. No VM onde pretende instalar o servidor de configuração, abra o [portal Azure](https://portal.azure.com).
2. Vá aos **cofres dos serviços**de recuperação > **Definições** > **Local De recuperação** > Passo 1: Preparar objetivo de**proteção de****infraestruturas** > .

   ![Navegação para o painel de objetivos de proteção][2]

3. Sob **o objetivo de Proteção**, na primeira lista de drop-down, selecione To **Azure**. Na segunda lista de descidas, selecione **Não virtualizado / Outros,** e, em seguida, selecione **OK**.

   ![Painel de objetivos de proteção com caixas cheias][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Passo 3: Configurar o ambiente de origem (servidor de configuração)

1. Baixe a **Configuração Unificada de Recuperação** do Site Azure e a chave de registo do cofre indo para a **infraestrutura** > prepare**preparar** > as panelas add**server.** 
 
   Vai precisar da chave de registo do cofre para executar a configuração unificada. A chave é válida durante cinco dias depois de gerá-la.

   ![Navegação no painel Add Server][4]

2. No painel **Add Server,** adicione um servidor de configuração.

   ![Adicionar painel do servidor com servidor de configuração selecionado][5]

3. No VM que está a usar como servidor de configuração, execute a Configuração Unificada para instalar o servidor de configuração e o servidor de processo. Pode [percorrer as imagens](../../site-recovery/vmware-walkthrough-overview.md) para completar a instalação. Pode consultar as seguintes imagens para os passos especificados para este cenário de migração.

   1. Antes **de começar,** selecione **Instale o servidor de configuração e processe**o servidor .

      ![Antes de começar a página][6]

   2. No **Registo,** navegue e selecione a chave de registo que descarregou do cofre.

      ![Página de registo][7]

   3. Em **Detalhes do ambiente**, selecione se pretende replicar VMs do VMware. Para este cenário de migração, escolha **Nº**.

      ![Página de Detalhes do Ambiente][8]

4. Depois de concluída a instalação, faça o seguinte na janela do Servidor de Configuração de Configuração de Recuperação do **Site microsoft Azure:**
 
   1. Utilize o separador **'Gerir Contas'** para criar a conta que a Recuperação do Site pode utilizar para a descoberta automática. (No cenário de proteger as máquinas físicas, a configuração da conta não é relevante, mas é necessário pelo menos uma conta para permitir um dos seguintes passos. Neste caso, pode nomear a conta e a palavra-passe como qualquer outra.) 
   2. Utilize o separador **de registo do cofre** para carregar o ficheiro credencial do cofre.

      ![Separador de registo do cofre][9]

### <a name="step-4-set-up-the-target-environment"></a>Passo 4: Configurar o ambiente-alvo

Selecione Prepare o**Target**de **infraestrutura** > e especifique o modelo de implementação que pretende utilizar para VMs após a falha. Pode escolher **Classic** ou **Resource Manager,** dependendo do seu cenário.

![Painel de alvo][10]

A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis. 

> [!NOTE]
> Se estiver a utilizar uma conta de armazenamento premium para dados replicados, precisa de configurar uma conta de armazenamento padrão adicional para armazenar registos de replicação.

### <a name="step-5-set-up-replication-settings"></a>Passo 5: Configurar as definições de replicação

Para verificar se o seu servidor de configuração está associado com sucesso à política de replicação que cria, siga as definições de [replicação configurar](../../site-recovery/vmware-walkthrough-overview.md).

### <a name="step-6-plan-capacity"></a>Passo 6: Capacidade do plano

1. Utilize o planejador de [capacidades](../../site-recovery/site-recovery-capacity-planner.md) para estimar com precisão a largura de banda da rede, armazenamento e outros requisitos para satisfazer as suas necessidades de replicação. 
2. Quando terminar, selecione **Sim, já o fiz** em **ter completado o planeamento**de capacidade?

   ![Caixa para confirmar que completou o planeamento da capacidade][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Passo 7: Instale o serviço de mobilidade e permita a replicação

1. Pode optar por empurrar a [instalação](../../site-recovery/vmware-walkthrough-overview.md) para os Seus VMs de origem ou [instalar manualmente o serviço](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) de mobilidade nos seus VMs de origem. Pode encontrar a exigência de empurrar a instalação e o caminho do instalador manual no link fornecido. Se estiver a fazer uma instalação manual, poderá ter de utilizar um endereço IP interno para encontrar o servidor de configuração.

   ![Página de detalhes do servidor de configuração][12]

   O VM falhado terá dois discos temporários: um do VM primário e o outro criado durante o fornecimento do VM na região de recuperação. Para excluir o disco temporário antes da replicação, instale o serviço de mobilidade antes de ativar a replicação. Para saber mais sobre como excluir o disco temporário, consulte [Excluir discos da replicação](../../site-recovery/vmware-walkthrough-overview.md).

2. Ative a replicação da seguinte forma:
   1. Selecione **Replicate Application** > **Source**. Depois de ter ativado a replicação pela primeira vez, selecione **+Replicate** no cofre para permitir a replicação de máquinas adicionais.
   2. No passo 1, configura a **Fonte** como servidor de processos.
   3. No passo 2, especifique o modelo de implementação pós-falha, uma conta de armazenamento premium para migrar, uma conta de armazenamento padrão para guardar registos e uma rede virtual para não o fazer.
   4. No passo 3, adicione VMs protegidos por endereço IP. (Pode precisar de um endereço IP interno para encontrá-los.)
   5. No passo 4, configure as propriedades selecionando as contas que configura previamente no servidor de processos.
   6. No passo 5, escolha a política de replicação que criou anteriormente em "Passo 5: Configurar as definições de replicação".
   7. Selecione **OK**.

   > [!NOTE]
   > Quando um VM Azure é transferido e reiniciado, não há garantias de que receberá o mesmo endereço IP. Se o endereço IP do servidor/servidor de processo de configuração ou os VMs azure protegidos mudar, a replicação neste cenário pode não funcionar corretamente.

   ![Ativar o painel de replicação com fonte selecionada][13]

Quando projetar o seu ambiente de armazenamento Azure, recomendamos que utilize contas de armazenamento separadas para cada VM num conjunto de disponibilidade. Recomendamos que siga as melhores práticas na camada de armazenamento para [utilizar várias contas](../linux/manage-availability.md)de armazenamento para cada conjunto de disponibilidade . Distribuir discos VM para várias contas de armazenamento ajuda a melhorar a disponibilidade de armazenamento e distribui o I/S através da infraestrutura de armazenamento Azure.

Se os seus VMs estiverem num conjunto de disponibilidade, em vez de replicar discos de todos os VMs numa única conta de armazenamento, recomendamos vivamente a migração de vários VMs várias vezes. Desta forma, os VMs no mesmo conjunto de disponibilidade não partilham uma única conta de armazenamento. Utilize o painel de **replicação ativa** para criar uma conta de armazenamento de destino para cada VM, um de cada vez.
 
Pode escolher um modelo de implementação pós-falha de acordo com a sua necessidade. Se escolher o Gestor de Recursos Azure como modelo de implementação pós-falha, pode falhar um VM (Gestor de Recursos) para um VM (Gestor de Recursos), ou pode falhar sobre um VM (clássico) para um VM (Gestor de Recursos).

### <a name="step-8-run-a-test-failover"></a>Passo 8: Executar um teste failover

Para verificar se a sua replicação está completa, selecione a sua instância de Recuperação do Site e, em seguida, selecione**Itens Replicados**de **Definições** > . Verá o estado e a percentagem do seu processo de replicação. 

Depois de concluída a replicação inicial, execute uma falha no teste para validar a sua estratégia de replicação. Para obter etapas detalhadas de uma falha de teste, consulte [Executar uma falha de teste na Recuperação do Local](../../site-recovery/vmware-walkthrough-overview.md). 

> [!NOTE]
> Antes de executar qualquer falha, certifique-se de que os seus VMs e estratégia de replicação cumprem os requisitos. Para obter mais informações sobre a execução de um teste failover, consulte [Test failover to Azure in Site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

Pode ver o estado do seu teste falhar em **Definições** > **Jobs** > *YOUR_FAILOVER_PLAN_NAME*. No painel, pode ver uma desagregação dos passos e resultados de sucesso/falha. Se o teste falhar em qualquer passo, selecione o passo para verificar a mensagem de erro. 

### <a name="step-9-run-a-failover"></a>Passo 9: Executar uma falha

Depois de concluída a falha do teste, execute uma falha para migrar os seus discos para o Armazenamento Premium e replicar as instâncias vm. Siga os passos detalhados em [Executar uma falha](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Certifique-se de selecionar **VMs de desligar e sincronizar os dados mais recentes**. Esta opção especifica que a Recuperação do Site deve tentar desligar os VMs protegidos e sincronizar os dados para que a versão mais recente dos dados seja falhada. Se não selecionar esta opção ou se a tentativa não for bem sucedida, a falha será do mais recente ponto de recuperação disponível para o VM. 

A Recuperação do Site criará uma instância VM cujo tipo é o mesmo ou semelhante a um VM com capacidade para armazenamento Premium. Pode verificar o desempenho e o preço de várias instâncias vM indo ao [Windows Virtual Machines Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) ou [Linux Virtual Machines Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Passos pós-migração

1. **Configure os VMs replicados para o conjunto de disponibilidade, se aplicável**. A Recuperação do Site não suporta VMs migratórios juntamente com o conjunto de disponibilidade. Dependendo da implantação do seu VM replicado, faça um dos seguintes:
   * Para um VM criado através do modelo de implementação clássico: Adicione o VM à disponibilidade definida no portal Azure. Para passos detalhados, vá adicionar [uma máquina virtual existente a um conjunto de disponibilidade](../linux/classic/configure-availability-classic.md).
   * Para um VM criado através do modelo de implementação do Gestor de Recursos: Guarde a sua configuração do VM e, em seguida, elimine e recrie os VMs no conjunto de disponibilidade. Para isso, utilize o script no Conjunto de [Disponibilidade vm do Gestor](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)de Recursos set Azure . Antes de executar este guião, verifique as suas limitações e planeie o seu tempo de inatividade.

2. **Eliminar vMs e discos antigos.** Certifique-se de que os discos Premium são consistentes com os discos de origem e que os novos VMs desempenham a mesma função que os VMs de origem. Elimine o VM e elimine os discos das suas contas de armazenamento de origem no portal Azure. Se houver um problema em que o disco não seja apagado, mesmo que tenha eliminado o VM, consulte erros de eliminação de recursos de [armazenamento troubleshoot](storage-resource-deletion-errors.md).

3. **Limpe a infraestrutura de recuperação do sítio Azure.** Se a Recuperação do Local já não for necessária, pode limpar a sua infraestrutura. Eliminar itens replicados, o servidor de configuração e a política de recuperação e, em seguida, eliminar o cofre de recuperação do site Azure.

## <a name="troubleshooting"></a>Resolução de problemas

* [Monitorizar e resolução de problemas para máquinas virtuais e servidores físicos](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Fórum de Recuperação de Sites do Microsoft Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Passos seguintes

Para cenários específicos para máquinas virtuais migratórias, consulte os seguintes recursos:

* [Máquinas virtuais migrate Azure entre contas de armazenamento](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Criar e carregar um VHD do Windows Server para o Azure](upload-generalized-managed.md)
* [Máquinas virtuais migratórias da Amazon AWS para o Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Consulte também os seguintes recursos para saber mais sobre o Armazenamento Azure e as Máquinas Virtuais Azure:

* [Storage do Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Máquinas Virtuais Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)

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
