---
title: Configurar a recuperação de desastres hyper-V (com VMM) utilizando a Recuperação do Site Azure
description: Aprenda a configurar a recuperação de desastres de Hiper-V VMs no centro de sistema sinuoso vMM nuvens para Azure usando a Recuperação do Local.
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: a391d8eb3cf7bc43b52883cbf2e76170338c44c6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067584"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Configurar a recuperação após desastre de VMs Hyper-V no local em clouds VMM para o Azure

Este tutorial descreve como permitir a replicação para máquinas virtuais Hyper-V (VM) geridas pelo System Center Virtual Machine Manager (VMM) para Azure utilizando a Recuperação do [Site Azure.](site-recovery-overview.md) Se não estiver a usar VMM, [siga este tutorial.](hyper-v-azure-tutorial.md)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Selecionar a origem e o destino da replicação.
> * Instale o ambiente de replicação de fontes, incluindo os componentes de recuperação do local no local e o ambiente de replicação do alvo.
> * Configurar o mapeamento da rede entre as redes VMm VM e as redes virtuais Azure.
> * Criar uma política de replicação.
> * Ativar a replicação para uma VM.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, reveja os artigos na secção **Como guiar** a documentação de [recuperação](/azure/site-recovery/)do site .

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial assume que já completou os seguintes tutoriais:

1. [Preparar o Azure](tutorial-prepare-azure.md)
1. [Preparar servidores Hyper-V no local](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Selecionar um objetivo de replicação

1. No portal Azure, vá aos **cofres dos Serviços** de Recuperação e selecione o cofre **ContosoVMVault** que foi criado no tutorial [Prepare Azure.](tutorial-prepare-azure.md#create-a-recovery-services-vault)
1. A partir de **Iniciar,** selecione A Infraestrutura de**Preparação** de **Recuperação** > do Local e configure as seguintes definições:
    1. **Objetivo** > de proteção**Onde estão as suas máquinas localizadas?** **On-premises**
    1. **Onde pretende replicar as suas máquinas?** **To Azure**
    1. **As suas máquinas estão virtualizadas?** **Yes, with Hyper-V**
    1. **Está a usar o System Center VMM para gerir os anfitriões Hyper-V?** **Yes**
1. Selecione **OK**.

   ![Objetivo da replicação](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

1. No planeamento de **implementação**, se estiver a planear uma grande implementação, descarregue o Planificador de Implantação para Hyper-V a partir do link da página. [Saiba mais](hyper-v-deployment-planner-overview.md) sobre o planeamento de implantação hyper-V.
1. Para este tutorial, não precisamos do Planejador de Implantação. In **Have Completed deployment planning?** **I will do it later** **OK**

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Quando configura o ambiente de origem, instala o Fornecedor de Recuperação do Site Azure no servidor VMM e regista o servidor no cofre. Instala o agente dos Serviços de Recuperação Azure em cada hospedeiro Hyper-V.

1. **Preparar infraestruturas.** Selecione **Origem**.
1. **Preparar a fonte.** Selecione **+ VMM** para adicionar um servidor VMM. Em **Adicionar Servidor**, verifique se **Servidor do System Center VMM** aparece em **Tipo de servidor**.
1. Descarregue o instalador para o Fornecedor de Recuperação do Site Microsoft Azure.
1. Transfira a chave de registo do cofre. Precisa desta chave quando executar a configuração do Fornecedor. A chave é válida durante cinco dias depois de gerá-la.
1. Baixe o instalador para o agente dos Serviços de Recuperação do Microsoft Azure.

   ![Fornecedor de Descarregamento, chave de registo e agente](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalar o Fornecedor no servidor VMM

1. No assistente de configuração do fornecedor de recuperação do site Azure, **o Microsoft Update**. Opte por utilizar o Microsoft Update para verificar as atualizações do Fornecedor.
1. **Instalação**. Aceite a localização de instalação predefinida para o Fornecedor e selecione **Instalar**.
1. Após a instalação, no Assistente de Registo de Recuperação do Site microsoft Azure selecione **Definições de Cofre**, **Nave,** e no **ficheiro Chave,** selecione o ficheiro chave do cofre que descarregou.
1. Especifique a subscrição de Recuperação do Site Azure e o nome do cofre **(ContosoVMVault).** Especifique um nome amigável para o servidor VMM, para identificá-lo no cofre.
1. **Definições de procuração**. Selecione **Ligar diretamente à Recuperação do Site Azure sem procuração**.
1. Aceite a localização padrão do certificado que é usado para encriptar dados. Os dados encriptados serão desencriptados quando falhar.
1. **Sincronizar metadados em nuvem.** Selecione sync cloud meta data para portal de **recuperação**do site . Esta ação só tem de acontecer uma vez em cada servidor. Em seguida, selecione **Register**.
1. Depois de o servidor estar registado no cofre, selecione **Terminar**.

Após o registo terminar, os metadados do servidor são recuperados pela Recuperação do Site Azure e o servidor VMM é apresentado na Infraestrutura de **Recuperação**do Site .

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Instalar o agente dos Serviços de Recuperação nos anfitriões Hyper-V

Instale o agente em cada hospedeiro Hyper-V contendo VMs que pretende replicar.

No Assistente de Configuração do Agente de Recuperação do Microsoft Azure, configure estas definições:

1. **Pré-requisitos Verificar**. Selecione **Next**. Quaisquer pré-requisitos em falta serão instalados automaticamente.
1. **Definições de instalação**. Aceite a localização da instalação e a localização da cache. A unidade de cache precisa de pelo menos 5 GB de armazenamento. Recomendamos uma unidade com 600 GB ou mais de espaço livre. Em seguida, selecione **Instalar**.
1. **Instalação**. Quando a instalação terminar, selecione **Perto** para terminar o assistente.

   ![Agente de instalação](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

1. Selecione Preparar**o alvo**da **infraestrutura** > .
1. Selecione a subscrição e o grupo de recursos **(ContosoRG)** no qual os VMs Azure serão criados após a falha.
1. Selecione o modelo de implementação do Gestor de **Recursos.**

A Recuperação do Site verifica que há uma ou mais contas e redes de armazenamento Azure compatíveis.

## <a name="configure-network-mapping"></a>Configurar o mapeamento da rede

1. **Site Recovery Infrastructure** > Rede de Infraestruturas de Recuperação do Local**mapeia** > **mapeamento de rede.** Selecione o ícone **+Network Mapping.**
1. **Adicione o mapeamento da rede**. Selecione o servidor VMM do **Centro de Sistema fonte.** Para o **Target,** selecione Azure.
1. Verifique a subscrição e o modelo de implementação, após a ativação pós-falha.
1. **Rede de origem**. Selecione a rede VM de origem no local.
1. **Rede de destino**. Selecione a rede Azure na qual os VMs Azure de réplica serão localizados quando forem criados após a falha. Em seguida, selecione **OK**.

   ![Mapeamento da rede](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. Selecione Preparar**definições** > de replicação de **infraestrutura** > **+Criar e associar**.
1. Na **Política Criar e associar,** especifique um nome de política. Estamos a usar a Política de **Replicação de Contoso.**
1. Aceite as definições predefinidas e selecione **OK**:
   - **A frequência** de cópia indica que, após a replicação inicial, os dados delta se replicarão a cada cinco minutos.
   - **A retenção** do ponto de recuperação indica que cada ponto de recuperação será mantido por duas horas.
   - **Frequência de instantâneos consistentes com a aplicação** indica que os pontos de recuperação que contêm instantâneos consistentes com a aplicação serão criados a cada hora.
   - O tempo inicial de **arranque da replicação** indica que a replicação inicial começará imediatamente.
   - **Os dados de encriptação armazenados no Azure** estão definidos para o padrão **(Off)** e indicam que os dados de repouso em Azure não estão encriptados.
1. Depois de criada a apólice, selecione **OK**. Quando se cria uma nova política, está automaticamente associada à nuvem VMM.

## <a name="enable-replication"></a>Ativar a replicação

1. **Aplicação de replicação.** Selecione **Origem**.
1. **Fonte.** Selecione a nuvem VMM. Em seguida, selecione **OK**.
1. **Alvo.** Verifique o alvo (Azure), a subscrição do cofre e selecione o modelo De Gestor de **Recursos.**
1. Selecione a conta de armazenamento **contosovmsacct1910171607** e a rede **ContosoASRnet** Azure.
1. **Máquinas** > virtuais**Selecione**. Selecione o VM que pretende replicar. Em seguida, selecione **OK**.

   Pode controlar o progresso da ação **Ativar Proteção** em **Tarefas** > **Tarefas do Site Recovery**. Após o acabamento do trabalho de **Proteção final,** a replicação inicial está completa e o VM está pronto para a falha.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md)
