---
title: Configurar a recuperação de desastres hyper-V (com VMM) utilizando a Recuperação do Site Azure
description: Aprenda a configurar a recuperação de desastres de Hiper-V VMs no centro de sistema sinuoso vMM nuvens para Azure usando a Recuperação do Local.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 0c570702e4c3899ef2847883e6fc8649e603a787
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389636"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Instale recuperação de desastres de Hiper-V VMs no local em nuvens VMM para Azure

Este artigo descreve como permitir a replicação para VMs hiper-V no local geridos pelo System Center Virtual Machine Manager (VMM), para recuperação de desastres para O Azure utilizando o serviço de recuperação do [site Azure.](site-recovery-overview.md) Se não estiver a usar VMM, [siga este tutorial.](hyper-v-azure-tutorial.md)

Este é o terceiro tutorial de uma série que mostra como configurar a recuperação de desastres para Azure para VMware no local. No tutorial anterior, [preparámos o ambiente Hyper-V no local](hyper-v-prepare-on-premises-tutorial.md) para a recuperação de desastres para o Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Selecionar a origem e o destino da replicação.
> * Instale o ambiente de replicação de fontes, incluindo os componentes de recuperação do local no local e o ambiente de replicação do alvo.
> * Configurar o mapeamento da rede para mapear entre as redes VMm VM e as redes virtuais Azure.
> * Criar uma política de replicação.
> * Ativar a replicação para uma VM.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, reveja os artigos na secção **Como guiar** a documentação de [recuperação](https://docs.microsoft.com/azure/site-recovery)do site .



## <a name="before-you-begin"></a>Antes de começar

Este é o terceiro tutorial de uma série. Assume que já preencheu as tarefas nos tutoriais anteriores:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Prepara Hyper-V no local](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Selecionar um objetivo de replicação

1. No portal Azure, vá aos **cofres dos Serviços** de Recuperação e selecione o cofre. Preparámos o cofre **ContosoVMVault** no tutorial anterior.
2. Em **Getting Started**, selecione A **Recuperação**do Site , e, em seguida, selecione **Prepare a Infraestrutura**.
3. Na ** > De Proteção** **Onde estão as suas máquinas localizadas?**
4. Em **onde pretende replicar as suas máquinas?**
5. In **Are your machines virtualizados?**
6. Em Está a utilizar o **System Center VMM para gerir os seus anfitriões Hyper-V?**
7.  Selecione **OK**.

    ![Objetivo da replicação](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

1. No planeamento de **implementação**, se estiver a planear uma grande implementação, descarregue o Planificador de Implantação para Hyper-V a partir do link da página. [Saiba mais](hyper-v-deployment-planner-overview.md) sobre o planeamento de implantação hyper-V.
2. Para este tutorial, não precisamos do Planejador de Implantação. In **Have Completed deployment planning?**

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Quando configura o ambiente de origem, instala o Fornecedor de Recuperação do Site Azure no servidor VMM e regista o servidor no cofre. Instala o agente dos Serviços de Recuperação Azure em cada hospedeiro Hyper-V.

1. Em **Preparar infraestruturas,** selecione **Fonte**.
2. Na **fonte Prepare- selecione** **+ VMM** para adicionar um servidor VMM. Em **Adicionar Servidor**, verifique se **Servidor do System Center VMM** aparece em **Tipo de servidor**.
3. Descarregue o instalador para o Fornecedor de Recuperação do Site Microsoft Azure.
4. Transfira a chave de registo do cofre. Precisa desta chave quando executar a configuração do Fornecedor. A chave é válida durante cinco dias depois de gerá-la.
5. Baixe o instalador para o agente dos Serviços de Recuperação do Microsoft Azure.

    ![Fornecedor de Descarregamento, chave de registo e agente](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalar o Fornecedor no servidor VMM

1. No assistente de Configuração do Fornecedor do Azure Site Recovery > **Microsoft Update**, opte por utilizar o Microsoft Update para procurar atualizações do Fornecedor.
2. Na **instalação,** aceite o local de instalação predefinido para o Fornecedor e selecione **Instalar**.
3. Após a instalação, no Assistente de Registo de Recuperação do Site do Microsoft Azure > **Definições de Cofre,** selecione **Browse**, e no **ficheiro Key,** selecione o ficheiro chave do cofre que descarregou.
4. Especifique a subscrição de Recuperação do Site Azure e o nome do cofre **(ContosoVMVault).** Especifique um nome amigável para o servidor VMM, para identificá-lo no cofre.
5. Em **Definições de Proxy**, selecione **Ligar diretamente ao Azure Site Recovery sem um proxy**.
6. Aceite a localização padrão do certificado que é usado para encriptar dados. Os dados encriptados serão desencriptados quando falhar.
7. Em **Synchronize metadados em nuvem,** selecione Sync cloud meta data para portal de **recuperação**do site . Esta ação só tem de acontecer uma vez em cada servidor. Em seguida, selecione **Register**.
8. Depois de o servidor estar registado no cofre, selecione **Terminar**.

Após o registo terminar, os metadados do servidor são recuperados pela Recuperação do Site Azure, e o servidor VMM é apresentado na Infraestrutura de **Recuperação**do Local .

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Instale o agente de Serviços de Recuperação em anfitriões Hiper-V

Instale o agente em cada hospedeiro Hyper-V contendo VMs que pretende replicar.

1. No Microsoft Azure Recovery Services Agent Setup Wizard > **Prerequisites Check**, selecione **Next**. Quaisquer pré-requisitos em falta serão instalados automaticamente.
2. Em Definições de **Instalação,** aceite a localização da instalação e a localização da cache. A unidade de cache precisa de pelo menos 5 GB de armazenamento. Recomendamos uma unidade com 600 GB ou mais de espaço livre. Em seguida, selecione **Instalar**.
3. Na **instalação**, quando a instalação terminar, selecione **Feche** para terminar o assistente.

    ![Instalar agente](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

1. Selecione **Preparar infraestrutura** > **Destino**.
2. Selecione a subscrição e o grupo de recursos **(ContosoRG)** no qual os VMs Azure serão criados após a falha.
3. Selecione o modelo de implementação do Gestor de **Recursos.**

A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

## <a name="configure-network-mapping"></a>Configurar o mapeamento da rede

1. Na **Infraestrutura** de recuperação do local > **mapeamentos de rede** > Mapeamento de **rede,** selecione o ícone **de mapeamento de rede +.**
2. Em **Adicionar mapeamento**de rede, selecione o servidor VMM de origem. Selecione **Azure** como alvo.
3. Verifique a subscrição e o modelo de implementação, após a ativação pós-falha.
4. Na **rede Source,** selecione a rede VM de origem no local.
5. Na **rede Target,** selecione a rede Azure na qual serão localizados Os VMs Azure de réplica quando forem criados após a falha. Em seguida, selecione **OK**.

    ![Mapeamento da rede](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. Selecione Preparar as definições de > **de replicação** da **infraestrutura** >  **+Criar e associar**.
2. Em **Criar e associar política**, especifique um nome de política. Estamos a usar a Política de **Replicação de Contoso.**
3. Deixe as definições predefinidas e selecione **OK**.
    - **A frequência** de cópia indica que, após a replicação inicial, os dados delta se replicarão a cada cinco minutos.
    - **A retenção** do ponto de recuperação indica que cada ponto de recuperação será mantido por duas horas.
    - **Frequência de instantâneos consistentes com a aplicação** indica que os pontos de recuperação que contêm instantâneos consistentes com a aplicação serão criados a cada hora.
    - O tempo inicial de **arranque da replicação** indica que a replicação inicial começará imediatamente.
    - **Os dados de encriptação armazenados no Azure** estão definidos para o padrão **(Off)** e indicam que os dados de repouso em Azure não estão encriptados.
4. Depois de criada a apólice, selecione **OK**. Quando se cria uma nova política, está automaticamente associada à nuvem VMM.

## <a name="enable-replication"></a>Ativar a replicação

1. Na **aplicação Replicate,** selecione **Origem**.
2. Na **Fonte,** selecione a nuvem VMM. Em seguida, selecione **OK**.
3. No **Target,** verifique o alvo (Azure), a subscrição do cofre e selecione o modelo De Gestor de **Recursos.**
4. Selecione a conta de armazenamento **contosovmsacct1910171607** e a rede **ContosoASRnet** Azure.
5. Em **máquinas virtuais** > **Selecione**o VM que pretende replicar. Em seguida, selecione **OK**.

   Pode controlar o progresso da ação **Ativar Proteção** em **Tarefas** > **Tarefas do Site Recovery**. Após o acabamento do trabalho de **Proteção final,** a replicação inicial está completa e o VM está pronto para a falha.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md)
