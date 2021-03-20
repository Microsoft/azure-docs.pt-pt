---
title: Configurar a recuperação de desastres Hyper-V (com VMM) utilizando a recuperação do local de Azure
description: Aprenda a configurar a recuperação de desastres de Hiper-V VMs no System Center VMM nuvens para Azure usando a Recuperação do Site.
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: c806f968bc6530879f64ddbf6fd4c7d45aa7a8d3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89442825"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Configurar a recuperação após desastre de VMs Hyper-V no local em clouds VMM para o Azure

Este tutorial descreve como permitir a replicação para máquinas virtuais Hiper-V (VM) geridas pelo System Center Virtual Machine Manager (VMM) para Azure usando [a Recuperação do Local de Azure](site-recovery-overview.md). Se não estiver a utilizar o VMM, [siga este tutorial.](hyper-v-azure-tutorial.md)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Selecionar a origem e o destino da replicação.
> * Configurar o ambiente de replicação de fontes, incluindo componentes de recuperação do local e o ambiente de replicação do alvo.
> * Confiúdedo de rede entre redes VMM VM e redes virtuais Azure.
> * Criar uma política de replicação.
> * Ativar a replicação para uma VM.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, reveja os artigos na secção **'Como orientar'** a [documentação de Recuperação](./index.yml)do Local .

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que já completou os seguintes tutoriais:

1. [Preparar o Azure](tutorial-prepare-azure.md)
1. [Preparar servidores Hyper-V no local](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Selecionar um objetivo de replicação

1. No portal Azure, vá aos **cofres dos Serviços de Recuperação** e selecione o cofre **ContosoVMVault** que foi criado no tutorial [prepare Azure.](tutorial-prepare-azure.md#create-a-recovery-services-vault)
1. A partir **de Como Começar,** selecione Infraestrutura de **Preparação de Recuperação de Sítio** e  >   configurar as seguintes definições:
    1. **Objetivo de proteção**  >  **Onde estão as suas máquinas?** 
    1. **Onde pretende replicar as suas máquinas?** 
    1. **As suas máquinas estão virtualizadas?** 
    1. **Está a utilizar o System Center VMM para gerir os seus anfitriões Hiper-V?** 
1. Selecione **OK**.

   ![Objetivo da replicação](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

1. No **planeamento de implementação**, se estiver a planear uma grande implantação, descarregue o Deployment Planner para Hyper-V a partir do link na página. [Saiba mais](hyper-v-deployment-planner-overview.md) sobre o planeamento de implantação do Hyper-V.
1. Para este tutorial, não precisamos do Planejador de Implantação. Em **Já concluiu o planeamento de implantação?**  

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Ao configurar o ambiente de origem, instale o Fornecedor de Recuperação do Sítio Azure no servidor VMM e registe o servidor no cofre. Instale o agente dos Serviços de Recuperação Azure em cada anfitrião Hiper-V.

1. **Preparar Infraestruturas**. **Selecione Fonte**.
1. **Preparar fonte**. Selecione **+ VMM** para adicionar um servidor VMM. Em **Adicionar Servidor**, verifique se **Servidor do System Center VMM** aparece em **Tipo de servidor**.
1. Descarregue o instalador para o Microsoft Azure Site Recovery Provider.
1. Transfira a chave de registo do cofre. Precisa desta chave quando executar a configuração do Fornecedor. A chave é válida durante cinco dias depois de gerá-la.
1. Descarregue o instalador para o agente Microsoft Azure Recovery Services.

   ![Baixar Fornecedor, chave de registo e agente](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalar o Fornecedor no servidor VMM

1. No assistente de configuração do fornecedor de recuperação do site Azure, **o Microsoft Update**. Opte por utilizar o Microsoft Update para verificar as atualizações do Fornecedor.
1. **Instalação**. Aceite o local de instalação predefinido para o Fornecedor e **selecione Instalar.**
1. Após a instalação, no Microsoft Azure Site Recovery Wizard selecione **Definições de Cofre**, **Procurar** e no **ficheiro Key**, selecione o ficheiro da chave do cofre que descarregou.
1. Especificar a subscrição de Recuperação do Local Azure e o nome do cofre **(ContosoVMVault).** Especifique um nome amigável para o servidor VMM, para identificá-lo no cofre.
1. **Definições de procuração**. Selecione **Connect diretamente para Azure Site Recovery sem um proxy**.
1. Aceite a localização padrão para o certificado que é usado para encriptar dados. Os dados encriptados serão desencriptados quando falhar.
1. **Sincronizar metadados em nuvem.** Selecione **Sync cloud meta data para o portal de recuperação do site**. Esta ação tem de acontecer apenas uma vez em cada servidor. Em seguida, **selecione Registar.**
1. Depois de o servidor estar registado no cofre, selecione **Acabamento**.

Após o registo terminar, os metadados do servidor são recuperados pela Recuperação do Site Azure e o servidor VMM é apresentado na **Infraestrutura de Recuperação do Local**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Instalar o agente dos Serviços de Recuperação nos anfitriões Hyper-V

Instale o agente em cada hospedeiro Hiper-V contendo VMs que pretende replicar.

No Assistente de Configuração dos Serviços de Recuperação do Microsoft Azure, configura estas definições:

1. **Pré-requisitos Verificar**. Selecione **Seguinte**. Quaisquer pré-requisitos em falta serão instalados automaticamente.
1. **Definições de instalação**. Aceite o local de instalação. Em seguida, selecione **Instalar**.

    >[!NOTE]
    >Para a recuperação do local de Azure, a **localização da cache** não é necessária.

1. **Instalação**. Quando a instalação terminar, selecione **Perto** para terminar o assistente.

   ![Agente de instalação](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

1. Selecione Preparar o alvo **da infraestrutura**  >  .
1. Selecione a subscrição e o grupo de recursos **(ContosoRG**) no qual os VMs Azure serão criados após o failover.
1. Selecione o modelo de implementação **do Gestor de Recursos.**

A Recuperação do Site verifica se há uma ou mais contas e redes de armazenamento Azure compatíveis.

## <a name="configure-network-mapping"></a>Configurar o mapeamento da rede

1. Infraestrutura de **Recuperação de Locais**  >  **Mapeamentos de rede**  >  **Mapeamento de rede**. Selecione o ícone **de mapeamento de rede +.**
1. **Adicione mapeamento de rede**. Selecione o servidor **VMM do Centro de Sistema de Origem.** Para o **Alvo**, selecione Azure.
1. Verifique a subscrição e o modelo de implementação, após a ativação pós-falha.
1. **Rede de origem**. Selecione a rede VM de origem no local.
1. **Rede de alvos**. Selecione a rede Azure na qual os VMs Azure réplicas serão localizados quando forem criados após o failover. Em seguida, selecione **OK**.

   ![Mapeamento da rede](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. **Selecione Preparar**  >  **definições de replicação de** infraestrutura  >  **+Criar e associar**.
1. Na **política de criar e associar,** especifique um nome de política. Estamos a usar **a Política de ContosoReplication.**
1. Aceite as definições predefinidos e selecione **OK**:
   - **A frequência de cópia** indica que, após a replicação inicial, os dados delta serão replicados a cada cinco minutos.
   - **A retenção do ponto de recuperação** indica que cada ponto de recuperação será mantido por duas horas.
   - **Frequência de instantâneos consistentes com a aplicação** indica que os pontos de recuperação que contêm instantâneos consistentes com a aplicação serão criados a cada hora.
   - **A hora inicial de início da replicação** indica que a replicação inicial começará imediatamente.
   - **Os dados encriptados armazenados no Azure** estão definidos para o padrão **(Off)** e indicam que os dados de repouso em Azure não estão encriptados.
1. Depois de a apólice ser criada, selecione **OK**. Quando cria uma nova política, está automaticamente associada à nuvem VMM.

## <a name="enable-replication"></a>Ativar a replicação

1. **Aplicação de replicação**. **Selecione Fonte**.
1. **Fonte**. Selecione a nuvem VMM. Em seguida, selecione **OK**.
1. **Alvo**. Verifique o alvo (Azure), a subscrição do cofre e selecione o modelo **gestor de recursos.**
1. Selecione a conta de armazenamento **contosovmsacct1910171607** e a rede **ContosoASRnet** Azure.
1. **Máquinas**  >  virtuais **Selecione**. Selecione o VM que pretende replicar. Em seguida, selecione **OK**.

   Pode controlar o progresso da ação **Ativar Proteção** em **Tarefas** > **Tarefas do Site Recovery**. Após o acabamento do trabalho **de Proteção finalizado,** a replicação inicial está completa, e o VM está pronto para o failover.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md)
