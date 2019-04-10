---
title: Configurar a recuperação após desastre de VMs de Hyper-V no local em clouds do VMM para o Azure com o Azure Site Recovery | Documentos da Microsoft
description: Saiba como configurar a recuperação após desastre de VMs de Hyper-V no local em clouds do System Center VMM para o Azure, com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 64559f653ba8a466de7bec10db34383b508e3e4b
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361289"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Configurar a recuperação após desastre de VMs de Hyper-V no local em clouds do VMM para o Azure

Este artigo descreve como ativar a replicação de VMs de Hyper-V no local geridos pelo System Center Virtual Machine Manager (VMM), para recuperação após desastre para o Azure com o [do Azure Site Recovery](site-recovery-overview.md) serviço. Se não estiver a utilizar o VMM, em seguida, [siga este tutorial](hyper-v-azure-tutorial.md).

Este é o terceiro tutorial de uma série que mostra como configurar a recuperação após desastre para o Azure das VMs de VMware no local. No tutorial anterior, podemos [preparado o ambiente de Hyper-V no local](hyper-v-prepare-on-premises-tutorial.md) para recuperação após desastre para o Azure. 

Neste tutorial, ficará a saber como:


> [!div class="checklist"]
> * Selecionar a origem e o destino da replicação.
> * Configurar o ambiente de replicação de origem, incluindo os componentes do Site Recovery no local e o ambiente de replicação de destino.
> * Configure o mapeamento de rede, para mapeamento entre redes de VM do VMM e redes virtuais do Azure.
> * Criar uma política de replicação
> * Ativar replicação para uma VM


> [!NOTE]
> Tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, consulte o artigo na seção como a do Site Recovery sumário.

## <a name="before-you-begin"></a>Antes de começar

Este é o terceiro tutorial de uma série. Este tutorial parte do princípio de que já concluiu as tarefas nos tutoriais anteriores:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar Hyper-V no local](tutorial-prepare-on-premises-hyper-v.md) este é o terceiro tutorial de uma série. Este tutorial parte do princípio de que já concluiu as tarefas nos tutoriais anteriores:


## <a name="select-a-replication-goal"></a>Selecionar um objetivo de replicação

1. Em **Cofres dos Serviços de Recuperação**, selecione o cofre. Que preparamos no cofre **ContosoVMVault** no tutorial anterior.
2. Em **Introdução**, clique em **Site Recovery**. Em seguida, clique em **Preparar Infraestrutura**
3. Na **objetivo de proteção** > **onde estão localizadas as máquinas virtuais?**, selecione **locais**.
4. Na **onde pretende replicar as máquinas?**, selecione **para o Azure**.
5. Na **as suas máquinas virtualizadas estão?** selecionar **Sim, com o Hyper-V**.
6. Na **está a utilizar o System Center VMM**, selecione **Sim**. Em seguida, clique em **OK**.

    ![Objetivo da replicação](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)


## <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

1. Na **planeamento da implementação**, se estiver a planear uma implementação de grande dimensão, transfira o planeador de implementação para Hyper-V da ligação na página. [Saiba mais](hyper-v-deployment-planner-overview.md) sobre o planeamento de implementação de Hyper-V.
2. Para efeitos deste tutorial, não precisamos o planeador de implementação. Na **concluiu o planeamento da implementação?**, selecione **faço mais tarde**. Em seguida, clique em **OK**.


## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Quando configurar o ambiente de origem, instalar o Azure Site Recovery Provider no servidor do VMM e registe o servidor no cofre. Instalar o agente dos serviços de recuperação do Azure em cada anfitrião de Hyper-V. 

1. Em **Preparar Infraestrutura**, clique em **Origem**.
2. Em **Preparar a origem**, clique em **+ VMM** para adicionar um servidor VMM. Em **Adicionar Servidor**, verifique se **Servidor do System Center VMM** aparece em **Tipo de servidor**.
3. Transferir o instalador para o Microsoft Azure Site Recovery Provider.
4. Transfira a chave de registo do cofre. Precisará dela quando executar a configuração do Fornecedor. A chave é válida durante cinco dias depois de gerá-la.
5. Transferir o instalador para o agente dos serviços de recuperação do Microsoft Azure.

    ![Transferência](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalar o Fornecedor no servidor VMM

1. No assistente de Configuração do Fornecedor do Azure Site Recovery > **Microsoft Update**, opte por utilizar o Microsoft Update para procurar atualizações do Fornecedor.
2. Na **instalação**, aceite a localização de instalação predefinida para o fornecedor e clique em **instalar**. 
3. Após a instalação, no Assistente de registo do Microsoft Azure Site Recovery > **definições do cofre**, clique em **procurar**e, na **ficheiro de chave**, selecione a chave do Cofre de ficheiros que transferido.
4. Especifique a subscrição do Azure Site Recovery e o nome do cofre (**ContosoVMVault**). Especifique um nome amigável para o servidor do VMM, a identificá-lo no cofre.
5. Em **Definições de Proxy**, selecione **Ligar diretamente ao Azure Site Recovery sem um proxy**.
6. Aceite a localização predefinida para o certificado que é utilizado para encriptar dados. Serão possível desencriptar dados encriptados quando efetuar a ativação pós-falha.
7. Na **sincronizar metadados da nuvem**, selecione **metadados para o portal de recuperação de Site da cloud de sincronização**. Esta ação só deverá ocorrer uma vez em cada servidor. Em seguida, clique em **registar**.
8. Depois do servidor está registado no cofre, clique em **concluir**.

Após a conclusão do registo, os metadados do servidor são obtidos pelo Azure Site Recovery e o servidor do VMM é apresentado na **infraestrutura do Site Recovery**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Instale o agente dos serviços de recuperação nos anfitriões de Hyper-V

Instale o agente em cada anfitrião de Hyper-V que contém VMs que pretende replicar.

1. No Assistente de configuração do agente de recuperação dos serviços Microsoft Azure > **verificação de pré-requisitos**, clique em **próxima**. Os pré-requisitos em falta serão instalados automaticamente.
2. Na **definições de instalação**, aceite a localização de instalação e a localização da cache. A unidade de cache tem de, pelo menos, 5 GB de armazenamento. Recomendamos que uma unidade com 600 GB ou mais de espaço livre. Em seguida, clique em **Instalar**.
3. Na **instalação**, quando a instalação for concluída, clique em **fechar** para concluir o assistente.

    ![Instalar agente](./media/hyper-v-vmm-azure-tutorial/mars-install.png)
    

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

1. Clique em **Preparar a Infraestrutura** > **Destino**.
2. Selecione a subscrição e o grupo de recursos (**ContosoRG**) no qual as VMs do Azure serão criadas após a ativação pós-falha.
3. Selecione o **Resource Manager** modelo de implementação.

A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.


## <a name="configure-network-mapping"></a>Configurar o mapeamento da rede

1. Em **Infraestrutura do Site Recovery** > **Mapeamentos da Rede** > **Mapeamento da Rede**, clique no ícone **+ Mapeamento da Rede**.
2. Na **adicionar mapeamento da rede**, selecione o servidor do VMM de origem. Selecione **Azure** como destino.
3. Verifique a subscrição e o modelo de implementação, após a ativação pós-falha.
4. Na **rede de origem**, selecione a rede VM de origem no local.
5. Na **rede de destino**, selecione a rede do Azure na qual as VMs do Azure de réplica estarão localizada quando forem criadas após a ativação pós-falha. Em seguida, clique em **OK**.

    ![Mapeamento da rede](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. Clique em **Preparar a infraestrutura** > **Definições de Replicação** > **+Criar e associar**.
2. Em **Criar e associar política**, especifique um nome de política. Estamos a utilizar **ContosoReplicationPolicy**.
3. Mantenha as predefinições e clique em **OK**.
    - **Copiar frequência** indica que os dados delta (após a replicação inicial) serão replicados a cada cinco minutos.
    - **Retenção do ponto de recuperação** indica para cada ponto de recuperação será retido durante duas horas.
    - **Frequência de instantâneos consistentes com a aplicação** indica que os pontos de recuperação que contêm instantâneos consistentes com a aplicação serão criados a cada hora.
    - **Hora de início da replicação inicial** indica que a replicação inicial será imediatamente iniciada.
    - **Encriptar dados armazenados no Azure** -a predefinição **desativar** definição indica que Inativos dados no Azure não estão encriptados.
4. Após a criação da política, clique em **OK**. Quando cria uma nova política, automaticamente fica associada à nuvem do VMM.

## <a name="enable-replication"></a>Ativar a replicação

1. Em **Replicar aplicação**, clique em **Origem**. 
2. Na **origem**, selecione a nuvem do VMM. Em seguida, clique em **OK**.
3. Na **destino**, verifique o Azure como o destino, a subscrição do cofre e selecione o **Resource Manager** modelo.
4. Selecione o **contosovmsacct1910171607** conta de armazenamento e o **ContosoASRnet** rede do Azure.
5. Em **Máquinas virtuais** > **Selecionar**, selecione a VM que pretende replicar. Em seguida, clique em **OK**.

   Pode controlar o progresso da ação **Ativar Proteção** em **Tarefas** > **Tarefas do Site Recovery**. Após a conclusão do trabalho **Finalizar Proteção**, a replicação inicial estará concluída e a VM estará preparada para a ativação pós-falha.



## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md)
