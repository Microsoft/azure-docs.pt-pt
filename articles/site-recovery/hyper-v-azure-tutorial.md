---
title: Configurar a recuperação após desastre de VMs de Hyper-V (sem VMM) no local para o Azure com o Azure Site Recovery | Microsoft Docs
description: Saiba como configurar a recuperação após desastre de VMs de Hyper-V (sem VMM) no local para o Azure com o serviço Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5a7161d05b153a556cce20ec4f4d0cbbfdf1d2d1
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315509"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Configurar a recuperação após desastre de VMs de Hyper-V no local para o Azure

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para a estratégia de recuperação após desastre, através da gestão e orquestração de replicação, ativação pós-falha e reativação pós-falha de máquinas no local e máquinas virtuais (VMs) do Azure.

Este tutorial mostra como configurar a recuperação após desastre de VMs de Hyper-V no local para o Azure. O tutorial é relevante para as VMs de Hyper-V que não são geridas pelo System Center Virtual Machine Manager (VMM). Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Selecionar a origem e o destino da replicação.
> * Configurar o ambiente de replicação de origem, incluindo os componentes do Site Recovery no local e o ambiente de replicação de destino.
> * Criar uma política de replicação.
> * Ativar a replicação para uma VM.

Este é o terceiro tutorial de uma série. Este tutorial parte do princípio de que já concluiu as tarefas nos tutoriais anteriores:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Prepara Hyper-V no local](tutorial-prepare-on-premises-hyper-v.md)

Antes de começar, é útil [rever a arquitetura](concepts-hyper-v-to-azure-architecture.md) deste cenário de recuperação após desastre.

## <a name="select-a-replication-goal"></a>Selecionar um objetivo de replicação


1. Em **Todos os Serviços** > **Cofres dos Serviços de Recuperação**, selecione o cofre que foi preparado no tutorial anterior, **ContosoVMVault**.
2. Em **Introdução**, clique em **Site Recovery**. Em seguida, clique em **Preparar Infraestrutura**
3. Em **Objetivo de proteção** > **Onde estão localizadas as máquinas virtuais**, selecione **No local**.
4. Em **Para onde pretende replicar as máquinas**, selecione **Para o Azure**.
5. Em **Está a utilizar o System Center VMM para gerir os anfitriões Hyper-V?**, selecione **Não**. Em seguida, clique em **OK**.

    ![Objetivo da replicação](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

Quando estiver a planear uma implementação de grande dimensão, deve verificar se o [planeamento de implementação para a replicação de Hyper-V](hyper-v-deployment-planner-overview.md) foi concluído. Para efeitos deste tutorial em **Concluiu o planeamento da implementação?**, selecione **Faço mais tarde** na lista pendente.

![Planeamento da implementação](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Para configurar o ambiente de origem, crie um site Hyper-V e adicione anfitriões Hyper-V ao site. Em seguida, transfira e instale o Fornecedor do Azure Site Recovery e o agente dos Serviços de Recuperação do Azure em cada anfitrião e registe o site Hyper-V no cofre. 

1. Em **Preparar Infraestrutura**, clique em **Origem**.
2. Clique em **+Site Hyper-V** e especifique o nome do site criado no tutorial anterior, **ContosoHyperVSite**.

    ![Site Hyper-V](./media/hyper-v-azure-tutorial/hyperv-site.png)

3. Depois de ter criado o site, clique em **+Hyper-V Server**.

    ![Servidor Hyper-V](./media/hyper-v-azure-tutorial/hyperv-server.png)

4. Transfira o ficheiro de configuração do Fornecedor.
6. Transfira a chave de registo do cofre. Precisará desta chave quando executar a configuração do Fornecedor. A chave é válida durante cinco dias depois de gerá-la.

    ![Transferir o Fornecedor](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Instalar o Fornecedor

Execute o ficheiro de configuração do Fornecedor (AzureSiteRecoveryProvider.exe) em cada anfitrião Hyper-V adicionado ao site **ContosoHyperVSite**. A configuração instala o Fornecedor do Azure Site Recovery e o agente dos Serviços de Recuperação em cada anfitrião Hyper-V.

1. No assistente de Configuração do Fornecedor do Azure Site Recovery > **Microsoft Update**, opte por utilizar o Microsoft Update para procurar atualizações do Fornecedor.
2. Em **Instalação**, aceite a localização de instalação predefinida para o Fornecedor e o agente e, em seguida, clique em **Instalar**.
3. Após a instalação, no Assistente de Registo do Microsoft Azure Site Recovery > **Definições do Cofre**, clique em **Procurar** e, em **Ficheiro de Chave**, selecione o ficheiro de chave do cofre transferido. 
4. Especifique a subscrição do Azure Site Recovery, o nome do cofre (**ContosoVMVault**) e o site Hyper-V (**ContosoHyperVSite**) ao qual pertence o servidor Hyper-V.
5. Em **Definições de Proxy**, selecione **Ligar diretamente ao Azure Site Recovery sem um proxy**.
6. Em **Registo**, após registar o servidor no cofre, clique em **Concluir**.

Os metadados do servidor Hyper-V são obtidos pelo Azure Site Recovery e o servidor é apresentado em **Infraestrutura do Site Recovery** > **Anfitriões Hyper-V**. Este processo pode demorar até 30 minutos a concluir.        

No caso de estiver usando um server core do Hyper-V, siga os passos depois de baixar as credenciais do cofre e fornecedor conforme mencionado abaixo [aqui](#set-up-the-source-environment)

1. Extraia os ficheiros do AzureSiteRecoveryProvider.exe através da execução

    ``AzureSiteRecoveryProvider.exe /x:. /q``
 
    Isto irá extrair os ficheiros no diretório local.
 
2.  Execute ``.\setupdr.exe /i ``

    Os resultados serão registados para %Programdata%\ASRLogs\DRASetupWizard.log

3.  Registre-se o servidor com o comando:

``cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved" ``
 

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione e verifique os recursos de destino. 

1. Clique em **Preparar a Infraestrutura** > **Destino**.
2. Selecione a subscrição e o grupo de recursos **ContosoRG**, no qual as VMs do Azure serão criadas após a ativação pós-falha.
3. Selecione o modelo de implementação **Resource Manager"**.

A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.


## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

> [!NOTE]
> Para políticas de replicação do Hyper-V para o Azure, a opção de frequência de cópia de 15 minutos vai ser extinta a favor de 5 minutos, e as definições de frequência de cópia serão de 30 segundos. As políticas de replicação com uma frequência de cópia de 15 minutos serão atualizadas automaticamente para utilizar a definição de frequência de cópia de 5 minutos. As opções de frequência de cópia de 5 minutos e de 30 segundos proporcionam um desempenho de replicação melhorado e melhores objetivos de ponto de recuperação, em comparação com uma frequência de cópia de 15 minutos, com impacto mínimo na utilização da largura de banda e no volume de transferência de dados.

1. Clique em **Preparar a infraestrutura** > **Definições de Replicação** > **+Criar e associar**.
2. Em **Criar e associar política**, especifique um nome para a política, **ContosoReplicationPolicy**.
3. Mantenha as predefinições e clique em **OK**.
    - **Copiar frequência** indica que os dados delta (após a replicação inicial) serão replicados a cada cinco minutos.
    - **A retenção do ponto de recuperação** indica que as janelas de retenção de cada ponto de recuperação serão de duas horas.
    - **Frequência de instantâneos consistentes com a aplicação** indica que os pontos de recuperação que contêm instantâneos consistentes com a aplicação serão criados a cada hora.
    - **Hora de início da replicação inicial** indica que a replicação inicial será imediatamente iniciada.
4. Após a criação da política, clique em **OK**. Quando cria uma nova política, esta fica automaticamente associada ao site Hyper-V especificado (**ContosoHyperVSite**)

    ![Política de replicação](./media/hyper-v-azure-tutorial/replication-policy.png)


## <a name="enable-replication"></a>Ativar a replicação


1. Em **Replicar aplicação**, clique em **Origem**. 
2. Em **Origem**, selecione o site **ContosoHyperVSite**. Em seguida, clique em **OK**.
3. Em **Destino**, verifique o Azure como o destino, a subscrição do cofre e o modelo de implementação **Resource Manager**.
4. Selecione a conta de armazenamento **contosovmsacct1910171607** criada no tutorial anterior para os dados replicados e a rede **ContosoASRnet**, na qual as VMs do Azure estarão localizadas após a ativação pós-falha.
5. Em **Máquinas virtuais** > **Selecionar**, selecione a VM que pretende replicar. Em seguida, clique em **OK**.

   Pode controlar o progresso da ação **Ativar Proteção** em **Tarefas** > **Tarefas do Site Recovery**. Após a conclusão da tarefa **Finalizar Proteção**, a replicação inicial estará concluída e a máquina virtual estará preparada para a ativação pós-falha.

## <a name="next-steps"></a>Passos Seguintes
[Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md)
