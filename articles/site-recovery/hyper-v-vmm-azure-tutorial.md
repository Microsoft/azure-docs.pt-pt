---
title: Configurar a recuperação de desastre do Hyper-V (com VMM) usando Azure Site Recovery
description: Saiba como configurar a recuperação de desastre de VMs do Hyper-V locais em nuvens do System Center VMM para o Azure usando Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: fdf6d9674305ca13af51f3f7b97e0a40568738b6
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953960"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Configurar a recuperação de desastre de VMs do Hyper-V locais em nuvens do VMM para o Azure

Este artigo descreve como habilitar a replicação para VMs do Hyper-V locais gerenciadas pelo System Center Virtual Machine Manager (VMM), para recuperação de desastres no Azure usando o serviço [Azure site Recovery](site-recovery-overview.md) . Se você não estiver usando o VMM, [siga este tutorial](hyper-v-azure-tutorial.md) em vez disso.

Este é o terceiro tutorial de uma série que mostra como configurar a recuperação de desastres para o Azure para VMs VMware locais. No tutorial anterior, preparamos [o ambiente do Hyper-V local](hyper-v-prepare-on-premises-tutorial.md) para recuperação de desastre no Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Selecionar a origem e o destino da replicação.
> * Configure o ambiente de replicação de origem, incluindo componentes de Site Recovery locais e o ambiente de replicação de destino.
> * Configure o mapeamento de rede para mapear entre redes de VM do VMM e redes virtuais do Azure.
> * Criar uma política de replicação.
> * Ativar a replicação para uma VM.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, examine os artigos na seção **guias** de instruções da documentação do [site Recovery](https://docs.microsoft.com/azure/site-recovery).

> [!WARNING]
> O suporte para a recuperação de desastre de VMs do Hyper-V usando o System Center VMM será preterido em um futuro próximo. É recomendável ler os detalhes de [substituição](scvmm-site-recovery-deprecation.md) antes de continuar.


## <a name="before-you-begin"></a>Antes de começar

Este é o terceiro tutorial de uma série. Ele pressupõe que você já tenha concluído as tarefas nos tutoriais anteriores:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Prepara Hyper-V no local](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Selecionar um objetivo de replicação

1. Na portal do Azure, vá para **cofres dos serviços de recuperação** e selecione o cofre. Preparamos o cofre **ContosoVMVault** no tutorial anterior.
2. Em **introdução**, selecione **site Recovery**e, em seguida, selecione **preparar infraestrutura**.
3. Em **meta de proteção** > **onde os computadores estão localizados?** , selecione **local**.
4. Em **onde você deseja replicar seus computadores?** , selecione para o **Azure**.
5. Em **seus computadores são virtualizados?** , selecione **Sim, com o Hyper-V**.
6. No **, você está usando o System Center VMM para gerenciar seus hosts do Hyper-V?** , selecione **Sim**.
7.  Selecione **OK**.

    ![Objetivo da replicação](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

1. Em **planejamento de implantação**, se você estiver planejando uma implantação grande, baixe o planejador de implantações do Hyper-V do link na página. [Saiba mais](hyper-v-deployment-planner-overview.md) sobre o planejamento de implantação do Hyper-V.
2. Para este tutorial, não precisamos do Planejador de Implantações. Se **você concluiu o planejamento de implantação?** , selecione **farei isso mais tarde**e, em seguida, selecione **OK**.

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Ao configurar o ambiente de origem, você instala o provedor de Azure Site Recovery no servidor do VMM e registra o servidor no cofre. Você instala o agente dos serviços de recuperação do Azure em cada host Hyper-V.

1. Em **preparar infraestrutura**, selecione **origem**.
2. Em **preparar origem**, selecione **+ VMM** para adicionar um servidor do VMM. Em **Adicionar Servidor**, verifique se **Servidor do System Center VMM** aparece em **Tipo de servidor**.
3. Baixe o instalador para o provedor de Site Recovery de Microsoft Azure.
4. Transfira a chave de registo do cofre. Você precisa dessa chave ao executar a instalação do provedor. A chave é válida durante cinco dias depois de gerá-la.
5. Baixe o instalador para o agente de Serviços de Recuperação do Microsoft Azure.

    ![Baixar provedor, chave de registro e agente](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalar o Fornecedor no servidor VMM

1. No assistente de Configuração do Fornecedor do Azure Site Recovery > **Microsoft Update**, opte por utilizar o Microsoft Update para procurar atualizações do Fornecedor.
2. Em **instalação**, aceite o local de instalação padrão para o provedor e selecione **instalar**.
3. Após a instalação, no assistente de registro do Microsoft Azure Site Recovery > **configurações do cofre**, selecione **procurar**e, em **arquivo de chave**, selecione o arquivo de chave do cofre que você baixou.
4. Especifique a assinatura Azure Site Recovery e o nome do cofre (**ContosoVMVault**). Especifique um nome amigável para o servidor do VMM, para identificá-lo no cofre.
5. Em **Definições de Proxy**, selecione **Ligar diretamente ao Azure Site Recovery sem um proxy**.
6. Aceite o local padrão para o certificado que é usado para criptografar dados. Os dados criptografados serão descriptografados quando você fizer failover.
7. Em **sincronizar metadados de nuvem**, selecione **sincronizar metadados de nuvem para site Recovery portal**. Essa ação precisa ocorrer apenas uma vez em cada servidor. Em seguida, selecione **registrar**.
8. Depois que o servidor for registrado no cofre, selecione **concluir**.

Após a conclusão do registro, os metadados do servidor são recuperados por Azure Site Recovery e o servidor do VMM é exibido na **site Recovery infraestrutura**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Instalar o agente dos serviços de recuperação em hosts Hyper-V

Instale o agente em cada host Hyper-V que contém as VMs que você deseja replicar.

1. No assistente de instalação do agente de Serviços de Recuperação do Microsoft Azure > **verificação de pré-requisitos**, selecione **Avançar**. Todos os pré-requisitos ausentes serão instalados automaticamente.
2. Em **configurações de instalação**, aceite o local de instalação e o local do cache. A unidade de cache precisa de pelo menos 5 GB de armazenamento. Recomendamos uma unidade com 600 GB ou mais de espaço livre. Em seguida, selecione **Instalar**.
3. Em **instalação**, quando a instalação for concluída, selecione **fechar** para concluir o assistente.

    ![Instalar agente](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

1. Selecione **Preparar infraestrutura** > **Destino**.
2. Selecione a assinatura e o grupo de recursos (**ContosoRG**) em que as VMs do Azure serão criadas após o failover.
3. Selecione o modelo de implantação do **Gerenciador de recursos** .

A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

## <a name="configure-network-mapping"></a>Configurar o mapeamento da rede

1. Em **site Recovery infraestrutura** > **mapeamentos de rede** > **mapeamento de rede**, selecione o ícone **+ mapeamento de rede** .
2. Em **Adicionar mapeamento de rede**, selecione o servidor do VMM de origem. Selecione **Azure** como o destino.
3. Verifique a subscrição e o modelo de implementação, após a ativação pós-falha.
4. Em **rede de origem**, selecione a rede VM local de origem.
5. Em **rede de destino**, selecione a rede do Azure na qual as VMs do Azure de réplica serão localizadas quando elas forem criadas após o failover. Em seguida, selecione **OK**.

    ![Mapeamento da rede](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. Selecione **preparar infraestrutura** > **configurações de replicação** >  **+ criar e associar**.
2. Em **Criar e associar política**, especifique um nome de política. Estamos usando o **ContosoReplicationPolicy**.
3. Deixe as configurações padrão e selecione **OK**.
    - A **frequência de cópia** indica que, após a replicação inicial, os dados Delta serão replicados a cada cinco minutos.
    - **Retenção de ponto de recuperação** indica que cada ponto de recuperação será retido por duas horas.
    - **Frequência de instantâneos consistentes com a aplicação** indica que os pontos de recuperação que contêm instantâneos consistentes com a aplicação serão criados a cada hora.
    - **Hora de início da replicação inicial** indica que a replicação inicial será iniciada imediatamente.
    - **Criptografar dados armazenados no Azure** é definido como o padrão (**desativado**) e indica que os dados em repouso no Azure não são criptografados.
4. Depois que a política for criada, selecione **OK**. Quando você cria uma nova política, ela é automaticamente associada à nuvem do VMM.

## <a name="enable-replication"></a>Ativar a replicação

1. No **aplicativo replicate**, selecione **origem**.
2. Em **origem**, selecione a nuvem do VMM. Em seguida, selecione **OK**.
3. Em **destino**, verifique o destino (Azure), a assinatura do cofre e selecione o modelo do **Resource Manager** .
4. Selecione a conta de armazenamento do **contosovmsacct1910171607** e a rede do **ContosoASRnet** Azure.
5. Em **máquinas virtuais** > **selecione**, selecione a VM que você deseja replicar. Em seguida, selecione **OK**.

   Pode controlar o progresso da ação **Ativar Proteção** em **Tarefas** > **Tarefas do Site Recovery**. Depois que o trabalho **finalizar proteção** for concluído, a replicação inicial será concluída e a VM estará pronta para failover.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md)
