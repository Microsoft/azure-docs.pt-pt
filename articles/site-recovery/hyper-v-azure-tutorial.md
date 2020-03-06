---
title: Configurar a recuperação de desastres hiper-V usando a recuperação do site azure
description: Aprenda a configurar a recuperação de desastres de Hiper-V VMs no local (sem VMM) para Azure utilizando a Recuperação do Local.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 741d4718b5e6140f4ddd2bb22e1a2ec830763176
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389629"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Configurar a recuperação após desastre de VMs de Hyper-V no local para o Azure

O serviço de recuperação de [sítios Azure](site-recovery-overview.md) contribui para a sua estratégia de recuperação de desastres, gerindo e orquestrando replicação, failover e failback de máquinas no local e máquinas virtuais Azure (VMs).

Este é o terceiro tutorial de uma série. Mostra como configurar a recuperação de desastres de VMs hiper-V no local para Azure. Este tutorial aplica VMs Hiper-V que não são geridos pelo Microsoft System Center Virtual Machine Manager (VMM).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Selecionar a origem e o destino da replicação.
> * Instale o ambiente de replicação de fontes, incluindo os componentes de recuperação do local no local e o ambiente de replicação do alvo.
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
7. Selecione **OK**.

    ![Objetivo da replicação](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

1. No planeamento de **implementação**, se estiver a planear uma grande implementação, descarregue o Planificador de Implantação para Hyper-V a partir do link da página. [Saiba mais](hyper-v-deployment-planner-overview.md) sobre o planeamento de implantação hyper-V.
2. Para este tutorial, não precisamos do Planejador de Implantação. In **Have Completed deployment planning?**

    ![Planeamento da implementação](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Para configurar o ambiente de origem, cria um site Hyper-V e adiciona a esse site os anfitriões Hyper-V que contêm VMs que pretende replicar. Em seguida, você descarrega e instala o Fornecedor de Recuperação do Site Azure e o agente de Serviços de Recuperação Azure em cada anfitrião, e registra o site Hyper-V no cofre.

1. Em **Preparação de Infraestruturas,** selecione **Origem**.
2. Na **fonte prepare-** selecione **+ Sítio Hiper-V**.
3. No **site Create Hyper-V,** especifique o nome do site. Estamos a usar **contosoHyperVSite.**

    ![Site Hyper-V](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. Depois de criado o site, em **Prepare fonte** > **Passo 1: Selecione o site Hyper-V,** selecione o site que criou.
5. Selecione **+ Servidor Hiper-V**.

    ![Servidor Hyper-V](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Descarregue o instalador para o Fornecedor de Recuperação do Site Microsoft Azure.
7. Transfira a chave de registo do cofre. Precisa desta chave para instalar o Fornecedor. A chave é válida durante cinco dias depois de gerá-la.

    ![Fornecedor de descarregamento e chave de registo](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Instalar o Fornecedor

Instale o ficheiro de configuração descarregado (AzureSiteRecoveryProvider.exe) em cada anfitrião Hyper-V que pretende adicionar ao site Hyper-V. A configuração instala o agente de recuperação e serviços de recuperação do site Azure em cada anfitrião Hyper-V.

1. Execute o ficheiro de configuração.
2. No assistente de Configuração do Fornecedor do Azure Site Recovery > **Microsoft Update**, opte por utilizar o Microsoft Update para procurar atualizações do Fornecedor.
3. Na **instalação,** aceite o local de instalação predefinido para o Fornecedor e o agente, e selecione **Instalar**.
4. Após a instalação, no Assistente de Registo de Recuperação do Site do Microsoft Azure > **Definições de Cofre,** selecione **Browse**, e no **Ficheiro chave,** selecione o ficheiro chave do cofre que descarregou.
5. Especifique a subscrição do Azure Site Recovery, o nome do cofre (**ContosoVMVault**) e o site Hyper-V (**ContosoHyperVSite**) ao qual pertence o servidor Hyper-V.
6. Em **Definições de Proxy**, selecione **Ligar diretamente ao Azure Site Recovery sem um proxy**.
7. No **Registo**, depois de o servidor estar registado no cofre, selecione **Terminar**.

Os metadados do servidor Hyper-V são obtidos pelo Azure Site Recovery e o servidor é apresentado em **Infraestrutura do Site Recovery** > **Anfitriões Hyper-V**. Este processo pode demorar até 30 minutos a concluir.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Instale o Fornecedor num servidor central hiper-V

Se estiver a executar um servidor central Hyper-V, descarregue o ficheiro de configuração e siga estes passos:

1. Extraios os ficheiros do AzureSiteRecoveryProvider.exe para um diretório local, executando este comando:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Execute `.\setupdr.exe /i`. Os resultados são registados em %Programdata%\ASRLogs\DRASetupWizard.log.

3. Registe o servidor executando este comando:

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione e verifique os recursos-alvo:

1. Selecione **Preparar infraestrutura** > **Destino**.
2. Selecione a subscrição e o grupo de recursos **ContosoRG** no qual os VMs Azure serão criados após a falha.
3. Selecione o modelo de implementação **Resource Manager"** .

A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. Selecione Preparar as definições de > **de replicação** da **infraestrutura** >  **+Criar e associar**.
2. Em **Criar e associar política**, especifique um nome de política. Estamos a usar a Política de **Replicação de Contoso.**
3. Para este tutorial, deixaremos as definições padrão:
    - **A frequência** de cópia indica a frequência com que os dados delta (após a replicação inicial) se replicarão. A frequência padrão é a cada cinco minutos.
    - **A retenção** do ponto de recuperação indica que os pontos de recuperação serão mantidos por duas horas.
    - **Frequência de instantâneos consistentes com a aplicação** indica que os pontos de recuperação que contêm instantâneos consistentes com a aplicação serão criados a cada hora.
    - O tempo inicial de **arranque da replicação** indica que a replicação inicial começará imediatamente.
4. Depois de criada a apólice, selecione **OK**. Quando se cria uma nova política, está automaticamente associada ao site hiper-V especificado. No nosso tutorial, é **ContosoHyperVSite.**

    ![Política de replicação](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Ativar a replicação

1. Na **aplicação Replicate,** selecione **Origem**.
2. Em **Origem**, selecione o site **ContosoHyperVSite**. Em seguida, selecione **OK**.
3. No **Target,** verifique o alvo (Azure), a subscrição do cofre e o modelo de implementação do **Gestor de Recursos.**
4. Se estiver a utilizar configurações tutoriais, selecione a conta de armazenamento **contosovmsacct1910171607** criada no tutorial anterior para dados replicados. Selecione também a rede **ContosoASRnet,** na qual os VMs Azure serão localizados após a falha.
5. Em **máquinas virtuais** > **Selecione**o VM que pretende replicar. Em seguida, selecione **OK**.

   Pode controlar o progresso da ação **Ativar Proteção** em **Tarefas** > **Tarefas do Site Recovery**. Após o acabamento do trabalho de **Proteção final,** a replicação inicial está completa e o VM está pronto para a falha.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md)
