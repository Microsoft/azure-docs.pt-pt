---
title: Configurar a recuperação após desastre para VMs de Hyper-V no local (sem VMM) para o Azure com o Site Recovery | Documentos da Microsoft
description: Saiba como configurar a recuperação após desastre de VMs de Hyper-V no local (sem VMM) para o Azure utilizando a recuperação de Site.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: f4c9ddf608033f6b85268fe74b12861eb8c30082
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295161"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Configurar a recuperação após desastre de VMs de Hyper-V no local para o Azure

O [do Azure Site Recovery](site-recovery-overview.md) serviço contribui para a sua estratégia de recuperação após desastre através da gestão e orquestração de replicação, ativação pós-falha e reativação pós-falha de máquinas no local e máquinas virtuais do Azure (VMs).

Este é o terceiro tutorial de uma série. Mostra como configurar a recuperação após desastre de VMs de Hyper-V no local para o Azure. Este tutorial aplica-se as VMs de Hyper-V que não são geridos pelo Microsoft System Center Virtual Machine Manager (VMM).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Selecionar a origem e o destino da replicação.
> * Configure o ambiente de replicação de origem, incluindo os componentes do Site Recovery no local e o ambiente de replicação de destino.
> * Criar uma política de replicação.
> * Ativar a replicação para uma VM.

> [!NOTE]
> Tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, reveja os artigos a **guias de procedimentos** secção a [documentação da recuperação de Site](https://docs.microsoft.com/azure/site-recovery).

## <a name="before-you-begin"></a>Antes de começar

Este é o terceiro tutorial de uma série. Parte do princípio de que já concluiu as tarefas nos tutoriais anteriores:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Prepara Hyper-V no local](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Selecionar um objetivo de replicação

1. No portal do Azure, aceda a **cofres dos serviços de recuperação** e selecione o cofre. Que preparamos no cofre **ContosoVMVault** no tutorial anterior.
2. Na **introdução**, selecione **Site Recovery**e, em seguida, selecione **preparar infraestrutura**.
3. Na **objetivo de proteção** > **onde estão localizadas as máquinas virtuais?** , selecione **locais**.
4. Na **onde pretende replicar as máquinas?** , selecione **para o Azure**.
5. Na **as suas máquinas virtualizadas estão?** , selecione **Sim, com o Hyper-V**.
6. Na **está a utilizar o System Center VMM para gerir os anfitriões de Hyper-V?** , selecione **não**.
7. Selecione **OK**.

    ![Objetivo da replicação](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

1. Na **planeamento da implementação**, se estiver a planear uma implementação de grande dimensão, transfira o planeador de implementação para Hyper-V da ligação na página. [Saiba mais](hyper-v-deployment-planner-overview.md) sobre o planeamento de implementação de Hyper-V.
2. Para este tutorial, não precisamos o planeador de implementação. Na **concluiu o planeamento da implementação?** , selecione **faço mais tarde**e, em seguida, selecione **OK**.

    ![Planeamento da implementação](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Para configurar o ambiente de origem, criar um site de Hyper-V e adicionar a esse site, os anfitriões de Hyper-V que contém VMs que pretende replicar. Em seguida, transfira e instale o fornecedor do Azure Site Recovery e o agente dos serviços de recuperação do Azure em cada anfitrião e registe o site de Hyper-V no cofre.

1. Sob **preparar infraestrutura**, selecione **origem**.
2. Na **preparar origem**, selecione **+ Site Hyper-V**.
3. Na **site Hyper-V criar**, especifique o nome do site. Estamos a utilizar **ContosoHyperVSite**.

    ![Site Hyper-V](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. Depois do site é criado, no **preparar origem** > **passo 1: Selecione site de Hyper-V**, selecione o site que criou.
5. Selecione **+ servidor Hyper-V**.

    ![Servidor Hyper-V](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Transferir o instalador para o Microsoft Azure Site Recovery Provider.
7. Transferir a chave de registo do cofre. Precisará desta chave para instalar o fornecedor. A chave é válida durante cinco dias depois de gerá-la.

    ![Transferir o fornecedor e a chave de registo](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Instalar o Fornecedor

Instale o ficheiro de configuração transferido (AzureSiteRecoveryProvider.exe) em cada anfitrião de Hyper-V que pretende adicionar ao site Hyper-V. A configuração instala o fornecedor do Azure Site Recovery e agente dos serviços de recuperação em cada anfitrião de Hyper-V.

1. Execute o ficheiro de configuração.
2. No assistente de Configuração do Fornecedor do Azure Site Recovery > **Microsoft Update**, opte por utilizar o Microsoft Update para procurar atualizações do Fornecedor.
3. Na **instalação**, aceite a localização de instalação predefinida para o fornecedor e o agente e selecione **instalar**.
4. Após a instalação, no Assistente de registo do Microsoft Azure Site Recovery > **definições do cofre**, selecione **procurar**e, na **ficheiro de chave**, selecione a chave do Cofre de ficheiros que que transferiu.
5. Especifique a subscrição do Azure Site Recovery, o nome do cofre (**ContosoVMVault**) e o site Hyper-V (**ContosoHyperVSite**) ao qual pertence o servidor Hyper-V.
6. Em **Definições de Proxy**, selecione **Ligar diretamente ao Azure Site Recovery sem um proxy**.
7. Na **Registro**, após o servidor está registado no cofre, selecione **concluir**.

Os metadados do servidor Hyper-V são obtidos pelo Azure Site Recovery e o servidor é apresentado em **Infraestrutura do Site Recovery** > **Anfitriões Hyper-V**. Este processo pode demorar até 30 minutos a concluir.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Instalar o fornecedor num server core Hyper-V

Se estiver a executar um server core do Hyper-V, transfira o ficheiro de configuração e siga estes passos:

1. Extraia os ficheiros do AzureSiteRecoveryProvider.exe para um diretório local ao executar este comando:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Execute `.\setupdr.exe /i`. Os resultados são registados % Programdata%\ASRLogs\DRASetupWizard.log.

3. Registe o servidor ao executar este comando:

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione e verifique os recursos de destino:

1. Selecione **Preparar infraestrutura** > **Destino**.
2. Selecione a subscrição e o grupo de recursos **ContosoRG** no qual as VMs do Azure serão criadas após a ativação pós-falha.
3. Selecione o modelo de implementação **Resource Manager"** .

A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. Selecione **preparar infraestrutura** > **definições de replicação** >  **+ criar e associar**.
2. Em **Criar e associar política**, especifique um nome de política. Estamos a utilizar **ContosoReplicationPolicy**.
3. Para este tutorial, vamos deixar as predefinições:
    - **Frequência de cópia** indica a frequência com que irão replicar dados delta (após a replicação inicial). A frequência de predefinição é a cada cinco minutos.
    - **Retenção do ponto de recuperação** indica que serão mantidos os pontos de recuperação de duas horas.
    - **Frequência de instantâneos consistentes com a aplicação** indica que os pontos de recuperação que contêm instantâneos consistentes com a aplicação serão criados a cada hora.
    - **Hora de início da replicação inicial** indica que a replicação inicial será imediatamente iniciada.
4. Depois da política é criada, selecione **OK**. Quando cria uma nova política, é automaticamente associado com o site de Hyper-V especificado. No nosso tutorial, isso **ContosoHyperVSite**.

    ![Política de replicação](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Ativar a replicação

1. Na **replicar aplicação**, selecione **origem**.
2. Em **Origem**, selecione o site **ContosoHyperVSite**. Em seguida, selecione **OK**.
3. Na **destino**, verifique se o destino (Azure), a subscrição do cofre e o **Resource Manager** modelo de implementação.
4. Se estiver a utilizar as definições de tutoriais, selecione o **contosovmsacct1910171607** conta de armazenamento criada no tutorial anterior para os dados replicados. Selecione também a **ContosoASRnet** rede, na qual as VMs do Azure estarão localizada após a ativação pós-falha.
5. Na **máquinas virtuais** > **selecione**, selecione a VM que pretende replicar. Em seguida, selecione **OK**.

   Pode controlar o progresso da ação **Ativar Proteção** em **Tarefas** > **Tarefas do Site Recovery**. Depois do **finalizar proteção** tarefa for concluída, a replicação inicial está concluída e a VM está preparada para ativação pós-falha.

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md)
