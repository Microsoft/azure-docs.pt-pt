---
title: Configurar a recuperação de desastre para VMs do Hyper-V locais (sem VMM) para o Azure com o Site Recovery | Microsoft Docs
description: Saiba como configurar a recuperação de desastre de VMs do Hyper-V locais (sem VMM) para o Azure usando Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 446c870ba60d7931fafb9f9b1c7e8fc017f60e4d
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933859"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Configurar a recuperação após desastre de VMs de Hyper-V no local para o Azure

O serviço de [Azure site Recovery](site-recovery-overview.md) contribui para sua estratégia de recuperação de desastres Gerenciando e orquestrando a replicação, o failover e o failback de máquinas locais e VMs (máquinas virtuais) do Azure.

Este é o terceiro tutorial de uma série. Ele mostra como configurar a recuperação de desastre de VMs do Hyper-V locais para o Azure. Este tutorial aplica as VMs do Hyper-V que não são gerenciadas pelo Microsoft System Center Virtual Machine Manager (VMM).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Selecionar a origem e o destino da replicação.
> * Configure o ambiente de replicação de origem, incluindo componentes de Site Recovery locais e o ambiente de replicação de destino.
> * Criar uma política de replicação.
> * Ativar a replicação para uma VM.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, examine os artigos na seção **guias** de instruções da documentação do [site Recovery](https://docs.microsoft.com/azure/site-recovery).

> [!WARNING]
> Observe que o suporte a ASR para usar a configuração do SCVMM em conta em breve será preterido e, portanto, recomendamos que você leia os detalhes de [substituição](scvmm-site-recovery-deprecation.md) antes de continuar.


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
6. No **, você está usando o System Center VMM para gerenciar seus hosts do Hyper-V?** , selecione **não**.
7. Selecione **OK**.

    ![Objetivo da replicação](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

1. Em **planejamento de implantação**, se você estiver planejando uma implantação grande, baixe o planejador de implantações do Hyper-V do link na página. [Saiba mais](hyper-v-deployment-planner-overview.md) sobre o planejamento de implantação do Hyper-V.
2. Para este tutorial, não precisamos do Planejador de Implantações. Se **você concluiu o planejamento de implantação?** , selecione **farei isso mais tarde**e, em seguida, selecione **OK**.

    ![Planeamento da implementação](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Para configurar o ambiente de origem, você cria um site do Hyper-V e adiciona a esse site os hosts do Hyper-V que contêm as VMs que você deseja replicar. Em seguida, você baixa e instala o provedor de Azure Site Recovery e o agente de serviços de recuperação do Azure em cada host e registra o site do Hyper-V no cofre.

1. Em **preparar infraestrutura**, selecione **origem**.
2. Em **preparar origem**, selecione **+ site do Hyper-V**.
3. Em **criar site do Hyper-V**, especifique o nome do site. Estamos usando o **ContosoHyperVSite**.

    ![Site Hyper-V](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. Depois que o site for criado, em **preparar origem** > **etapa 1: selecionar site do Hyper-V**, selecione o site que você criou.
5. Selecione **+ servidor Hyper-V**.

    ![Servidor Hyper-V](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Baixe o instalador para o provedor de Site Recovery de Microsoft Azure.
7. Transfira a chave de registo do cofre. Você precisa dessa chave para instalar o provedor. A chave é válida durante cinco dias depois de gerá-la.

    ![Baixar o provedor e a chave de registro](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Instalar o Fornecedor

Instale o arquivo de instalação baixado (AzureSiteRecoveryProvider. exe) em cada host Hyper-V que você deseja adicionar ao site do Hyper-V. A instalação instala o provedor de Azure Site Recovery e o agente de serviços de recuperação em cada host Hyper-V.

1. Execute o arquivo de instalação.
2. No assistente de Configuração do Fornecedor do Azure Site Recovery > **Microsoft Update**, opte por utilizar o Microsoft Update para procurar atualizações do Fornecedor.
3. Em **instalação**, aceite o local de instalação padrão para o provedor e o agente e selecione **instalar**.
4. Após a instalação, no assistente de registro do Microsoft Azure Site Recovery > **configurações do cofre**, selecione **procurar**e, em **arquivo de chave**, selecione o arquivo de chave do cofre que você baixou.
5. Especifique a subscrição do Azure Site Recovery, o nome do cofre (**ContosoVMVault**) e o site Hyper-V (**ContosoHyperVSite**) ao qual pertence o servidor Hyper-V.
6. Em **Definições de Proxy**, selecione **Ligar diretamente ao Azure Site Recovery sem um proxy**.
7. Em **registro**, depois que o servidor for registrado no cofre, selecione **concluir**.

Os metadados do servidor Hyper-V são obtidos pelo Azure Site Recovery e o servidor é apresentado em **Infraestrutura do Site Recovery** > **Anfitriões Hyper-V**. Este processo pode demorar até 30 minutos a concluir.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Instalar o provedor em um servidor núcleo do Hyper-V

Se você estiver executando um servidor de núcleo do Hyper-V, baixe o arquivo de instalação e siga estas etapas:

1. Extraia os arquivos de AzureSiteRecoveryProvider. exe para um diretório local executando este comando:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Execute `.\setupdr.exe /i`. Os resultados são registrados em%Programdata%\ASRLogs\DRASetupWizard.log.

3. Registre o servidor executando este comando:

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione e verifique os recursos de destino:

1. Selecione **Preparar infraestrutura** > **Destino**.
2. Selecione a assinatura e o grupo de recursos **ContosoRG** em que as VMs do Azure serão criadas após o failover.
3. Selecione o modelo de implementação **Resource Manager"** .

A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. Selecione **preparar infraestrutura** > **configurações de replicação** >  **+ criar e associar**.
2. Em **Criar e associar política**, especifique um nome de política. Estamos usando o **ContosoReplicationPolicy**.
3. Para este tutorial, vamos deixar as configurações padrão:
    - A **frequência de cópia** indica a frequência com que os dados Delta (após a replicação inicial) serão replicados. A frequência padrão é a cada cinco minutos.
    - **Retenção de ponto de recuperação** indica que os pontos de recuperação serão retidos por duas horas.
    - **Frequência de instantâneos consistentes com a aplicação** indica que os pontos de recuperação que contêm instantâneos consistentes com a aplicação serão criados a cada hora.
    - **Hora de início da replicação inicial** indica que a replicação inicial será iniciada imediatamente.
4. Depois que a política for criada, selecione **OK**. Quando você cria uma nova política, ela é automaticamente associada ao site do Hyper-V especificado. Em nosso tutorial, isso é **ContosoHyperVSite**.

    ![Política de replicação](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Ativar a replicação

1. No **aplicativo replicate**, selecione **origem**.
2. Em **Origem**, selecione o site **ContosoHyperVSite**. Em seguida, selecione **OK**.
3. Em **destino**, verifique o destino (Azure), a assinatura do cofre e o modelo de implantação do **Gerenciador de recursos** .
4. Se você estiver usando as configurações do tutorial, selecione a conta de armazenamento **contosovmsacct1910171607** criada no tutorial anterior para dados replicados. Selecione também a rede **ContosoASRnet** , na qual as VMs do Azure serão localizadas após o failover.
5. Em **máquinas virtuais** > **selecione**, selecione a VM que você deseja replicar. Em seguida, selecione **OK**.

   Pode controlar o progresso da ação **Ativar Proteção** em **Tarefas** > **Tarefas do Site Recovery**. Depois que o trabalho **finalizar proteção** for concluído, a replicação inicial será concluída e a VM estará pronta para failover.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md)
