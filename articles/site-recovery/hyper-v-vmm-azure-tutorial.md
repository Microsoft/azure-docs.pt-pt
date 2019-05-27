---
title: Configurar a recuperação após desastre para VMs de Hyper-V no local em clouds do VMM para o Azure com o Site Recovery | Documentos da Microsoft
description: Saiba como configurar a recuperação após desastre de VMs de Hyper-V no local em clouds do System Center VMM para o Azure, utilizando a recuperação de Site.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 8c3c9347a027cccfaef6def84bfdc4c83555e98a
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966482"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Configurar a recuperação após desastre de VMs de Hyper-V no local em clouds do VMM para o Azure

Este artigo descreve como ativar a replicação para VMs de Hyper-V no local geridos pelo System Center Virtual Machine Manager (VMM), para recuperação após desastre para o Azure utilizando o [do Azure Site Recovery](site-recovery-overview.md) serviço. Se não estiver a utilizar o VMM, [siga este tutorial](hyper-v-azure-tutorial.md) em vez disso.

Este é o terceiro tutorial de uma série que mostra como configurar a recuperação após desastre para o Azure das VMs de VMware no local. No tutorial anterior, podemos [preparado o ambiente de Hyper-V no local](hyper-v-prepare-on-premises-tutorial.md) para recuperação após desastre para o Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Selecionar a origem e o destino da replicação.
> * Configure o ambiente de replicação de origem, incluindo os componentes do Site Recovery no local e o ambiente de replicação de destino.
> * Configure o mapeamento de rede para mapeamento entre redes de VM do VMM e redes virtuais do Azure.
> * Criar uma política de replicação.
> * Ativar a replicação para uma VM.

> [!NOTE]
> Tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, reveja os artigos a **guias de procedimentos** secção a [documentação da recuperação de Site](https://docs.microsoft.com/en-us/azure/site-recovery).

## <a name="before-you-begin"></a>Antes de começar

Este é o terceiro tutorial de uma série. Parte do princípio de que já concluiu as tarefas nos tutoriais anteriores:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Prepara Hyper-V no local](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Selecionar um objetivo de replicação

1. No portal do Azure, aceda a **cofres dos serviços de recuperação** e selecione o cofre. Que preparamos no cofre **ContosoVMVault** no tutorial anterior.
2. Na **introdução**, selecione **Site Recovery**e, em seguida, selecione **preparar infraestrutura**.
3. Na **objetivo de proteção** > **onde estão localizadas as máquinas virtuais?**, selecione **locais**.
4. Na **onde pretende replicar as máquinas?**, selecione **para o Azure**.
5. Na **as suas máquinas virtualizadas estão?**, selecione **Sim, com o Hyper-V**.
6. Na **está a utilizar o System Center VMM para gerir os anfitriões de Hyper-V?**, selecione **Sim**.
7.  Selecione **OK**.

    ![Objetivo da replicação](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

1. Na **planeamento da implementação**, se estiver a planear uma implementação de grande dimensão, transfira o planeador de implementação para Hyper-V da ligação na página. [Saiba mais](hyper-v-deployment-planner-overview.md) sobre o planeamento de implementação de Hyper-V.
2. Para este tutorial, não precisamos o planeador de implementação. Na **concluiu o planeamento da implementação?**, selecione **faço mais tarde**e, em seguida, selecione **OK**.

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Quando configurar o ambiente de origem, instala o fornecedor do Azure Site Recovery no servidor do VMM e registar o servidor no cofre. Instalar o agente dos serviços de recuperação do Azure em cada anfitrião de Hyper-V.

1. Na **preparar infraestrutura**, selecione **origem**.
2. Na **preparar origem**, selecione **+ VMM** para adicionar um servidor do VMM. Em **Adicionar Servidor**, verifique se **Servidor do System Center VMM** aparece em **Tipo de servidor**.
3. Transferir o instalador para o Microsoft Azure Site Recovery Provider.
4. Transferir a chave de registo do cofre. Precisará desta chave quando executar a configuração do fornecedor. A chave é válida durante cinco dias depois de gerá-la.
5. Transferir o instalador para o agente dos serviços de recuperação do Microsoft Azure.

    ![Transferir o fornecedor, a chave de registo e o agente](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalar o Fornecedor no servidor VMM

1. No assistente de Configuração do Fornecedor do Azure Site Recovery > **Microsoft Update**, opte por utilizar o Microsoft Update para procurar atualizações do Fornecedor.
2. Na **instalação**, aceite a localização de instalação predefinida para o fornecedor e selecione **instalar**.
3. Após a instalação, no Assistente de registo do Microsoft Azure Site Recovery > **definições do cofre**, selecione **procurar**e, na **ficheiro de chave**, selecione a chave do Cofre de ficheiros que que transferiu.
4. Especifique a subscrição do Azure Site Recovery e o nome do cofre (**ContosoVMVault**). Especifique um nome amigável para o servidor do VMM, a identificá-lo no cofre.
5. Em **Definições de Proxy**, selecione **Ligar diretamente ao Azure Site Recovery sem um proxy**.
6. Aceite a localização predefinida para o certificado que é utilizado para encriptar dados. Serão possível desencriptar dados encriptados quando efetuar a ativação pós-falha.
7. Na **sincronizar metadados da nuvem**, selecione **metadados para o portal de recuperação de Site da cloud de sincronização**. Esta ação precisa ocorrer apenas uma vez em cada servidor. Em seguida, selecione **registar**.
8. Depois do servidor está registado no cofre, selecione **concluir**.

Após a conclusão do registo, os metadados do servidor são obtidos pelo Azure Site Recovery e o servidor do VMM é apresentado na **infraestrutura do Site Recovery**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Instale o agente dos serviços de recuperação nos anfitriões de Hyper-V

Instale o agente em cada anfitrião de Hyper-V que contém VMs que pretende replicar.

1. No Assistente de configuração do agente de recuperação dos serviços Microsoft Azure > **verificação de pré-requisitos**, selecione **próxima**. Os pré-requisitos em falta serão instalados automaticamente.
2. Na **definições de instalação**, aceite a localização de instalação e a localização da cache. A unidade de cache tem de, pelo menos, 5 GB de armazenamento. Recomendamos que uma unidade com 600 GB ou mais de espaço livre. Em seguida, selecione **Instalar**.
3. Na **instalação**, quando a instalação for concluída, selecione **fechar** para concluir o assistente.

    ![Instalar agente](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

1. Selecione **Preparar infraestrutura** > **Destino**.
2. Selecione a subscrição e o grupo de recursos (**ContosoRG**) no qual as VMs do Azure serão criadas após a ativação pós-falha.
3. Selecione o **Resource Manager** modelo de implementação.

A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

## <a name="configure-network-mapping"></a>Configurar o mapeamento da rede

1. Na **infraestrutura do Site Recovery** > **mapeamentos da rede** > **mapeamento da rede**, selecione o **+ mapeamento da rede** ícone.
2. Na **adicionar mapeamento da rede**, selecione o servidor do VMM de origem. Selecione **Azure** como destino.
3. Verifique a subscrição e o modelo de implementação, após a ativação pós-falha.
4. Na **rede de origem**, selecione a rede VM de origem no local.
5. Na **rede de destino**, selecione a rede do Azure na qual as VMs do Azure de réplica estarão localizada quando forem criadas após a ativação pós-falha. Em seguida, selecione **OK**.

    ![Mapeamento da rede](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. Selecione **preparar infraestrutura** > **definições de replicação** > **+ criar e associar**.
2. Em **Criar e associar política**, especifique um nome de política. Estamos a utilizar **ContosoReplicationPolicy**.
3. Deixe as predefinições e selecione **OK**.
    - **Frequência de cópia** indica que, após a replicação inicial, dados delta replicará a cada cinco minutos.
    - **Retenção do ponto de recuperação** indica que cada ponto de recuperação será retido durante duas horas.
    - **Frequência de instantâneos consistentes com a aplicação** indica que os pontos de recuperação que contêm instantâneos consistentes com a aplicação serão criados a cada hora.
    - **Hora de início da replicação inicial** indica que a replicação inicial será imediatamente iniciada.
    - **Encriptar dados armazenados no Azure** está definido como a predefinição (**desativar**) e indica que os dados Inativos no Azure não estão encriptados.
4. Depois da política é criada, selecione **OK**. Quando cria uma nova política, é automaticamente associado com a cloud do VMM.

## <a name="enable-replication"></a>Ativar replicação

1. Na **replicar aplicação**, selecione **origem**.
2. Na **origem**, selecione a nuvem do VMM. Em seguida, selecione **OK**.
3. Na **destino**, verifique se o destino (Azure), a subscrição do cofre e selecione o **Resource Manager** modelo.
4. Selecione o **contosovmsacct1910171607** conta de armazenamento e o **ContosoASRnet** rede do Azure.
5. Na **máquinas virtuais** > **selecione**, selecione a VM que pretende replicar. Em seguida, selecione **OK**.

   Pode controlar o progresso da ação **Ativar Proteção** em **Tarefas** > **Tarefas do Site Recovery**. Depois do **finalizar proteção** tarefa for concluída, a replicação inicial está concluída e a VM está preparada para ativação pós-falha.

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md)
