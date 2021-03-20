---
title: Configurar a recuperação de desastres hiper-V usando a recuperação do local de Azure
description: Aprenda a configurar a recuperação de desastres de Hiper-V VMs (sem VMM) para Azure utilizando a Recuperação do Local.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5ce98c785700301bba92926d7d5a243b614eca7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87504233"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Configurar a recuperação após desastre de VMs de Hyper-V no local para o Azure

O serviço [de Recuperação do Local Azure](site-recovery-overview.md) contribui para a sua estratégia de recuperação de desastres, gerindo e orquestrando a replicação, failover e o failback de máquinas no local e máquinas virtuais Azure (VMs).

Este é o terceiro tutorial de uma série. Mostra como configurar a recuperação de desastres de Hiper-V VMs para Azure. Este tutorial aplica VMs Hiper-V que não são geridos pelo Microsoft System Center Virtual Machine Manager (VMM).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Selecionar a origem e o destino da replicação.
> * Configurar o ambiente de replicação de fontes, incluindo componentes de recuperação do local e o ambiente de replicação do alvo.
> * Criar uma política de replicação.
> * Ativar a replicação para uma VM.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, reveja os artigos na secção **'Como orientar'** a [documentação de Recuperação](./index.yml)do Local .



## <a name="before-you-begin"></a>Antes de começar

Este é o terceiro tutorial de uma série. Assume que já completou as tarefas nos tutoriais anteriores:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar Hyper-V no local](./hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Selecionar um objetivo de replicação

1. No portal Azure, vá aos **cofres dos Serviços de Recuperação** e selecione o cofre. Preparámos o cofre **ContosoVMVault** no tutorial anterior.
2. Em **Começar,** selecione **A Recuperação do Local** e, em seguida, selecione Prepare a **Infraestrutura**.
3. Em **Objetivo de Proteção**  >  **Onde estão as suas máquinas localizadas?** 
4. Em **Onde pretende replicar as suas máquinas?** 
5. Em **As suas máquinas estão virtualizadas?** 
6. Em **Está a utilizar o System Center VMM para gerir os seus anfitriões Hiper-V?** 
7. Selecione **OK**.

    ![Screenshot das opções de objetivos de Proteção em infraestrutura de preparação.](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

1. No **planeamento de implementação**, se estiver a planear uma grande implantação, descarregue o Deployment Planner para Hyper-V a partir do link na página. [Saiba mais](hyper-v-deployment-planner-overview.md) sobre o planeamento de implantação do Hyper-V.
2. Para este tutorial, não precisamos do Planejador de Implantação. Em **Já concluiu o planeamento de implantação?**  

    ![Screenshot das opções de planeamento de implantação na infraestrutura de preparação.](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Para configurar o ambiente de origem, cria um site Hiper-V e adiciona a esse site os anfitriões Hiper-V que contêm VMs que pretende replicar. Em seguida, você descarrega e instala o Azure Site Recovery Provider e o agente Azure Recovery Services em cada hospedeiro, e registre o site Hyper-V no cofre.

1. Em **Infraestrutura de Preparação**, selecione **Fonte**.
2. Na **fonte de preparação,** selecione **+ Hyper-V Site**.
3. No **site Create Hyper-V,** especifique o nome do site. Estamos a usar **o ContosoHyperVSite.**

    ![Screenshot da seleção do site Hyper-V na infraestrutura de preparação.](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. Depois de o site ser criado, em **Prepare a fonte** Passo  >  **1: Selecione o site Hyper-V,** selecione o site que criou.
5. Selecione **+ Servidor Hiper-V**.

    ![Screenshot da seleção do servidor Hyper-V na infraestrutura Prepare.](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Descarregue o instalador para o Microsoft Azure Site Recovery Provider.
7. Transfira a chave de registo do cofre. Precisa desta chave para instalar o Fornecedor. A chave é válida durante cinco dias depois de gerá-la.

    ![Screenshot das opções para descarregar o Fornecedor e a chave de registo.](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Instalar o Fornecedor

Instale o ficheiro de configuração descarregado (AzureSiteRecoveryProvider.exe) em cada anfitrião Hiper-V que pretende adicionar ao site Hyper-V. A Configuração instala o Fornecedor de Recuperação do Local Azure e o agente dos Serviços de Recuperação em cada anfitrião Hiper-V.

1. Executar o ficheiro de configuração.
2. No assistente de Configuração do Fornecedor do Azure Site Recovery > **Microsoft Update**, opte por utilizar o Microsoft Update para procurar atualizações do Fornecedor.
3. Na **Instalação**, aceite o local de instalação predefinido para o Fornecedor e agente, e selecione **Instalar**.
4. Após a instalação, no Assistente de Registo de Recuperação do Site do Microsoft Azure > **Configurações de Cofre**, selecione **Browse** e em **Key File**, selecione o ficheiro da chave do cofre que descarregou.
5. Especifique a subscrição do Azure Site Recovery, o nome do cofre (**ContosoVMVault**) e o site Hyper-V (**ContosoHyperVSite**) ao qual pertence o servidor Hyper-V.
6. Em **Definições de Proxy**, selecione **Ligar diretamente ao Azure Site Recovery sem um proxy**.
7. In **Registration**, after the server is registered in the vault, select **Finish**.

Os metadados do servidor Hyper-V são recuperados pela Recuperação do Site Azure e o servidor é apresentado nos  >  **anfitriões hiper-V da infraestrutura de** recuperação do local . Este processo pode demorar até 30 minutos a concluir.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Instale o Fornecedor num servidor central Hiper-V

Se estiver a executar um servidor central Hyper-V, descarregue o ficheiro de configuração e siga estes passos:

1. Extrair os ficheiros de AzureSiteRecoveryProvider.exe para um diretório local executando este comando:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Execute `.\setupdr.exe /i`. Os resultados são registados em %Programdata%\ASRLogs\DRASetupWizard.log.

3. Registar o servidor executando este comando:

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione e verifique os recursos-alvo:

1. Selecione Preparar o alvo **da infraestrutura**  >  .
2. Selecione a subscrição e o grupo de recursos **ContosoRG** no qual os VMs Azure serão criados após o failover.
3. Selecione o modelo de implementação **do Gestor de Recursos.**

A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. **Selecione Preparar**  >  **definições de replicação de** infraestrutura  >  **+Criar e associar**.
2. Na **política de criar e associar,** especifique um nome de política. Estamos a usar **a Política de ContosoReplication.**
3. Para este tutorial, deixaremos as definições padrão:
    - **A frequência de cópia** indica quantas vezes os dados delta (após a replicação inicial) se replicam. A frequência padrão é a cada cinco minutos.
    - **A retenção do ponto de recuperação** indica que os pontos de recuperação serão mantidos por duas horas. O valor máximo permitido para a retenção ao proteger as máquinas virtuais hospedadas em anfitriões Hiper-V é de 24 horas.
    - **Frequência de instantâneos consistentes com a aplicação** indica que os pontos de recuperação que contêm instantâneos consistentes com a aplicação serão criados a cada hora.
    - **A hora inicial de início da replicação** indica que a replicação inicial começará imediatamente.
4. Depois de a apólice ser criada, selecione **OK**. Quando cria uma nova política, está automaticamente associada ao site hiper-V especificado. No nosso tutorial, é **ContosoHyperVSite.**

    ![Política de replicação](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Ativar a replicação

1. Na **aplicação Replicar**, selecione **Fonte**.
2. Em **Origem**, selecione o site **ContosoHyperVSite**. Em seguida, selecione **OK**.
3. No **Target,** verifique o alvo (Azure), a subscrição do cofre e o modelo de implementação **do Gestor de Recursos.**
4. Se estiver a utilizar configurações tutoriais, selecione a conta de armazenamento **contosovmsacct1910171607** criada no tutorial anterior para dados replicados. Selecione também a rede **ContosoASRnet,** na qual os VMs Azure serão localizados após a falha.
5. Em **Máquinas Virtuais**  >  **Selecione,** selecione o VM que pretende replicar. Em seguida, selecione **OK**.

   Pode controlar o progresso da ação **Ativar Proteção** em **Tarefas** > **Tarefas do Site Recovery**. Após o acabamento do trabalho **de Proteção finalizado,** a replicação inicial está completa, e o VM está pronto para o failover.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md)
