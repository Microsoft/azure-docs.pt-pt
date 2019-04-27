---
title: Configurar a recuperação após desastre de VMs de Hyper-V (sem VMM) no local para o Azure com o Azure Site Recovery | Microsoft Docs
description: Saiba como configurar a recuperação após desastre de VMs de Hyper-V (sem VMM) no local para o Azure com o serviço Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: da643a4d7a1dc74385b3854c1952af5ba93bd241
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749227"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Configurar a recuperação após desastre de VMs de Hyper-V no local para o Azure

c

> [!div class="checklist"]
> * Selecionar a origem e o destino da replicação.
> * Configurar o ambiente de replicação de origem, incluindo os componentes do Site Recovery no local e o ambiente de replicação de destino.
> * Criar uma política de replicação.
> * Ativar a replicação para uma VM.

> [!NOTE]
> Tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, consulte o artigo na seção como a do Site Recovery sumário.

## <a name="before-you-begin"></a>Antes de começar
Este é o terceiro tutorial de uma série. Este tutorial parte do princípio de que já concluiu as tarefas nos tutoriais anteriores:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Prepara Hyper-V no local](tutorial-prepare-on-premises-hyper-v.md)



## <a name="select-a-replication-goal"></a>Selecionar um objetivo de replicação

1. Em **Cofres dos Serviços de Recuperação**, selecione o cofre. Que preparamos no cofre **ContosoVMVault** no tutorial anterior.
2. Em **Introdução**, clique em **Site Recovery**. Em seguida, clique em **Preparar Infraestrutura**
3. Na **objetivo de proteção** > **onde estão localizadas as máquinas virtuais?** selecione **locais**.
4. Na **onde pretende replicar as máquinas?**, selecione **para o Azure**.
5. Na **as suas máquinas virtualizadas estão?** selecionar **Sim, com o Hyper-V**.
6. Em **Está a utilizar o System Center VMM para gerir os anfitriões Hyper-V?**, selecione **Não**. Em seguida, clique em **OK**.

    ![Objetivo da replicação](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

1. Na **planeamento da implementação**, se estiver a planear uma implementação de grande dimensão, transfira o planeador de implementação para Hyper-V da ligação na página. [Saiba mais](hyper-v-deployment-planner-overview.md) sobre o planeamento de implementação de Hyper-V.
2. Para efeitos deste tutorial, não precisamos o planeador de implementação. Na **concluiu o planeamento da implementação?** selecionar **faço mais tarde**. Em seguida, clique em **OK**.

    ![Planeamento da implementação](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Para configurar o ambiente de origem, criar um site de Hyper-V e adicione anfitriões Hyper-V, que contém as VMs que pretende replicar para o site. Em seguida, transfira e instale o Fornecedor do Azure Site Recovery e o agente dos Serviços de Recuperação do Azure em cada anfitrião e registe o site Hyper-V no cofre. 

1. Sob **preparar infraestrutura**, clique em **origem**.
2. Na **preparar origem**, clique em **+ Site Hyper-V**.
3. Na **site Hyper-V criar**e especifique o nome do site. Estamos a utilizar **ContosoHyperVSite**.

    ![Site Hyper-V](./media/hyper-v-azure-tutorial/hyperv-site.png)

3. Depois do site é criado, no **preparar origem** > **passo 1: Selecione site de Hyper-V**, selecione o site que criou.
4. Clique em **+Servidor Hyper-V**.

    ![Servidor Hyper-V](./media/hyper-v-azure-tutorial/hyperv-server.png)

4. Transferir o instalador para o Microsoft Azure Site Recovery Provider.
6. Transfira a chave de registo do cofre. Precisará desta chave para instalar o fornecedor. A chave é válida durante cinco dias depois de gerá-la.

    ![Transferir o Fornecedor](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Instalar o Fornecedor

Instale o ficheiro de configuração transferido (AzureSiteRecoveryProvider.exe) em cada anfitrião de Hyper-V que pretende adicionar ao site Hyper-V. A configuração instala o Fornecedor do Azure Site Recovery e o agente dos Serviços de Recuperação em cada anfitrião Hyper-V.

1. Execute o ficheiro de configuração.
2. No assistente de Configuração do Fornecedor do Azure Site Recovery > **Microsoft Update**, opte por utilizar o Microsoft Update para procurar atualizações do Fornecedor.
3. Em **Instalação**, aceite a localização de instalação predefinida para o Fornecedor e o agente e, em seguida, clique em **Instalar**.
4. Após a instalação, no Assistente de Registo do Microsoft Azure Site Recovery > **Definições do Cofre**, clique em **Procurar** e, em **Ficheiro de Chave**, selecione o ficheiro de chave do cofre transferido. 
5. Especifique a subscrição do Azure Site Recovery, o nome do cofre (**ContosoVMVault**) e o site Hyper-V (**ContosoHyperVSite**) ao qual pertence o servidor Hyper-V.
6. Em **Definições de Proxy**, selecione **Ligar diretamente ao Azure Site Recovery sem um proxy**.
7. Em **Registo**, após registar o servidor no cofre, clique em **Concluir**.

Os metadados do servidor Hyper-V são obtidos pelo Azure Site Recovery e o servidor é apresentado em **Infraestrutura do Site Recovery** > **Anfitriões Hyper-V**. Este processo pode demorar até 30 minutos a concluir.        

#### <a name="install-on-hyper-v-core-server"></a>Instalar no server core de Hyper-V

Se estiver a executar um server core do Hyper-V, transfira o ficheiro de configuração e faça o seguinte:

1. Extraia os ficheiros do AzureSiteRecoveryProvider.exe para um diretório local através da execução

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2.  Run `.\setupdr.exe /i. Os resultados são registados % Programdata%\ASRLogs\DRASetupWizard.log.

3.  Registe o servidor com este comando:

    ```
    cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```
 

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione e verifique os recursos de destino. 

1. Clique em **Preparar a Infraestrutura** > **Destino**.
2. Selecione a subscrição e o grupo de recursos **ContosoRG**, no qual as VMs do Azure serão criadas após a ativação pós-falha.
3. Selecione o modelo de implementação **Resource Manager"**.

A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.


## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. Clique em **Preparar a infraestrutura** > **Definições de Replicação** > **+Criar e associar**.
2. Em **Criar e associar política**, especifique um nome de política. Estamos a utilizar **ContosoReplicationPolicy**.
3. Para este tutorial Vamos deixar as predefinições.
    - **Frequência de cópia** indica a frequência com que irão replicar dados delta (após a replicação inicial), a cada cinco minutos por predefinição.
    - **Retenção do ponto de recuperação** indica que serão mantidos os pontos de recuperação de duas horas.
    - **Frequência de instantâneos consistentes com a aplicação** indica que os pontos de recuperação que contêm instantâneos consistentes com a aplicação serão criados a cada hora.
    - **Hora de início da replicação inicial** indica que a replicação inicial será imediatamente iniciada.
4. Após a criação da política, clique em **OK**. Quando cria uma nova política, é automaticamente associado com o site de Hyper-V especificado (no nosso tutorial isso **ContosoHyperVSite**)

    ![Política de replicação](./media/hyper-v-azure-tutorial/replication-policy.png)


## <a name="enable-replication"></a>Ativar a replicação


1. Em **Replicar aplicação**, clique em **Origem**. 
2. Em **Origem**, selecione o site **ContosoHyperVSite**. Em seguida, clique em **OK**.
3. Em **Destino**, verifique o Azure como o destino, a subscrição do cofre e o modelo de implementação **Resource Manager**.
4. Se estiver a utilizar as definições de tutoriais, selecione o **contosovmsacct1910171607** conta de armazenamento criada no tutorial anterior para os dados replicados e o **ContosoASRnet** rede, na qual irá de VMs do Azure é possível localizar após a ativação pós-falha.
5. Em **Máquinas virtuais** > **Selecionar**, selecione a VM que pretende replicar. Em seguida, clique em **OK**.

   Pode controlar o progresso da ação **Ativar Proteção** em **Tarefas** > **Tarefas do Site Recovery**. Após a conclusão da tarefa **Finalizar Proteção**, a replicação inicial estará concluída e a máquina virtual estará preparada para a ativação pós-falha.

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md)
