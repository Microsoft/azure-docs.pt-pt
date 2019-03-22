---
title: Configurar a recuperação após desastre para o Azure das VMs VMware no local com o Azure Site Recovery | Microsoft Docs
description: Saiba como configurar a recuperação após desastre para o Azure das VMs VMware no local com o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 3/18/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 06d18ccd6f14f0a2b31f579b0ed7250b2c4f0c92
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310596"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Configurar a recuperação após desastre para o Azure das VMs VMware no local

O [Azure Site Recovery](site-recovery-overview.md) contribui para a sua estratégia de continuidade comercial e recuperação após desastre (BCDR) ao manter as suas aplicações empresariais em funcionamento durante falhas planeadas e não planeadas. O Site Recovery gere e orquestra a recuperação após desastre de computadores no local e máquinas virtuais (VMs) do Azure, incluindo replicação, ativação pós-falha e recuperação.


Este tutorial mostra-lhe como implementar a recuperação de sites com as definições básicas, sem personalização. Para obter opções mais complexas, reveja os artigos em procedimentos.

    - Configure a [origem de replicação](vmware-azure-set-up-source.md) e o [servidor de configuração](vmware-azure-deploy-configuration-server.md).
    - Configure o [destino da replicação](vmware-azure-set-up-target.md).
    - Configure uma [política de replicação](vmware-azure-set-up-replication.md) e [ative a replicação](vmware-azure-enable-replication.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Introduza a origem e o destino da replicação.
> * Configure o ambiente de replicação de origem, incluindo os componentes do Azure Site Recovery no local e o ambiente de replicação de destino.
> * Criar uma política de replicação.
> * Ativar a replicação para uma VM.

## <a name="before-you-start"></a>Antes de começar

Antes de começar, deve:

- [Rever a arquitetura](vmware-azure-architecture.md) deste cenário de recuperação após desastre.
- Se quiser saber mais sobre como configurar a recuperação após desastre para as VMs de VMware com mais detalhe, reveja e utilize os seguintes recursos:
    - [Leia perguntas comuns](vmware-azure-common-questions.md) sobre a recuperação após desastre do VMware.
    - [Saiba](vmware-physical-azure-support-matrix.md) o que o VMware suporta e precisa.
- Neste tutorial mostramos como replicar uma VM única. Se estiver a implementar várias VMs deve utilizar o [ferramenta Planeador de implementações](https://aka.ms/asr-deployment-planner) para ajudar a planejar sua implantação. [Saiba mais](site-recovery-deployment-planner.md) sobre esta ferramenta.

E reveja estas dicas:
- Este tutorial utiliza um modelo OVA para criar o servidor de configuração da VM de VMware. Se não conseguir fazê-lo, siga [estas instruções](physical-manage-configuration-server.md) configurar manualmente o servidor de configuração.
- Neste tutorial, o Site Recovery transfere e instala o MySQL para o servidor de configuração. Se preferir, pode configurá-lo manualmente em vez disso. [Saiba mais](vmware-azure-deploy-configuration-server.md#configure-settings).
  >Pode transferir a versão mais recente do modelo do servidor de configuração diretamente do [Centro de Transferências da Microsoft](https://aka.ms/asrconfigurationserver).
  A licença fornecida com o modelo OVF é uma licença de avaliação válida por 180 dias. Windows em execução na VM tem de ser ativado com a licença necessária. 



## <a name="select-a-protection-goal"></a>Selecionar um objetivo de proteção

1. Em **Cofres dos Serviços de Recuperação**, selecione o nome do cofre. Neste cenário, estamos a utilizar **ContosoVMVault**.
2. Em **Introdução**, selecione Site Recovery. Em seguida, selecione **Preparar Infraestrutura**.
3. Em **Objetivo de proteção** > **Onde estão localizadas as máquinas virtuais**, selecione **No local**.
4. Em **Para onde pretende replicar as máquinas**, selecione **Para o Azure**.
5. Em **As suas máquinas estão virtualizadas**, selecione **Sim, com o VMware vSphere Hypervisor**. Em seguida, selecione **OK**.



## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

No seu ambiente de origem, precisará de uma única, elevada disponibilidade, máquina no local para o anfitrião local componentes do Site Recovery. Os componentes incluem o servidor de configuração, o servidor de processos e o servidor de destino principal:

- O servidor de configuração coordena as comunicações entre o local e o Azure, e gere a replicação de dados.
- O servidor de processos atua como um gateway de replicação. Recebe dados de replicação. otimiza-os com colocação em cache, compressão e encriptação; e envia-o para o armazenamento de cache da conta no Azure. O servidor de processos também instala o Serviço de Mobilidade nas VMs que pretende replicar, e efetua a deteção automática de VMs VMware no local.
- O servidor de destino principal processa dados de replicação durante a reativação pós-falha a partir do Azure.

Para configurar o servidor de configuração como uma VM VMware de elevada disponibilidade, transfira um modelo OVA (Open Virtualization Application) preparado e importe o modelo para o VMware, para criar a VM. Depois de configurar o servidor de configuração, registe-o no cofre. Após o registo, o Site Recovery deteta as VMs VMware no local.




### <a name="download-the-vm-template"></a>Transferir o modelo de VM

1. No cofre, vá para **Preparar Infraestrutura** > **Origem**.
2. Em **Preparar origem**, selecione **+Servidor de configuração**.
3. Em **Adicionar Servidor**, verifique se o **Servidor de configuração para VMware** é apresentado no **Tipo de servidor**.
4. Transfira o modelo OVF para o servidor de configuração.



## <a name="import-the-template-in-vmware"></a>Importar o modelo no VMware


1. Inicie sessão no servidor VMware vCenter ou no anfitrião vSphere ESXi, com o VMWare vSphere Client.
2. No menu **Ficheiro**, selecione **Implementar o Modelo OVF**, para iniciar o Assistente para **Implementar o Modelo OVF**. 

     ![Modelo OVF](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. Em **Selecionar origem**, introduza a localização do OVF transferido.
4. Em **Rever detalhes**, selecione **Seguinte**.
5. Em **Selecionar nome e pasta** e **Selecionar configuração**, aceite as predefinições.
6. Em **Selecionar armazenamento**, para obter o melhor desempenho, selecione **Thick Provision Eager Zeroed** em **Selecionar formato de disco virtual**.
7. Nas restantes páginas do assistente, aceite as predefinições.
8. Em **Pronto para concluir**, para configurar a VM com as predefinições, selecione **Ligar após a implementação** > **Concluir**.

   > [!TIP]
   > Se pretender adicionar outra NIC, desmarque **Ligar após a implementação** > **Concluir**. Por predefinição, o modelo contém uma NIC única. Pode adicionar mais NICs após a implementação.

## <a name="add-an-additional-adapter"></a>Adicionar outro adaptador

Para adicionar outro NIC ao servidor de configuração, adicione-o antes de registar o servidor no cofre. Adicionar mais adaptadores não é suportado após o registo.

1. No inventário do vSphere Client, clique na VM com o botão direito do rato e selecione **Editar Definições**.
2. Em **Hardware**, selecione **Adicionar** > **Adaptador Ethernet**. Em seguida, selecione **Seguinte**.
3. Selecione um tipo de adaptador e uma rede. 
4. Para ligar o NIC virtual quando a VM estiver ligada, selecione **Estabelecer ligação ao ligar**. Selecione **Seguinte** > **Concluir**. Em seguida, selecione **OK**.


## <a name="register-the-configuration-server"></a>Registar o servidor de configuração 

1. A partir da consola do VMWare vSphere Client, ligue a VM.
2. A VM arranca para uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e introduza uma palavra-passe de administrador.
3. Após a conclusão da instalação, inicie sessão na VM como administrador.
4. Da primeira vez que iniciar sessão, a Ferramenta de Configuração do Azure Site Recovery é iniciada em poucos segundos.
5. Introduza um nome que será utilizado para registar o servidor de configuração no Site Recovery. Em seguida, selecione **Seguinte**.
6. A ferramenta verifica se a VM pode ligar ao Azure. Depois de a ligação estar estabelecida, selecione **Iniciar sessão** para iniciar sessão na sua subscrição do Azure. As credenciais têm de ter acesso ao cofre no qual pretende registar o servidor de configuração.
7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reiniciada.
8. Inicie sessão na máquina novamente. Em poucos segundos, o Assistente de Gestão do Servidor de Configuração inicia automaticamente.

### <a name="configure-settings-and-add-the-vmware-server"></a>Configurar as definições e adicionar o servidor VMware

1. No Assistente de gestão do servidor de configuração, selecione **configurar a conectividade**. A partir as listas pendentes, primeiro selecione o NIC que o servidor de processos incorporado utiliza para a instalação de push e de deteção do serviço de mobilidade nas máquinas de origem e, em seguida, selecione o NIC que utiliza o servidor de configuração para a conectividade com o Azure. Em seguida, selecione **Guardar**. Não é possível alterar esta definição depois de estar configurada.
2. Em **Selecionar cofre dos Serviços de Recuperação**, selecione a sua subscrição do Azure e o grupo de recursos e cofre relevantes.
3. Em **Instalar software de terceiros**, aceite o contrato de licença. Selecione **Transferir e Instalar** para instalar o Servidor MySQL. Se o colocou o MySQL no caminho, este passo é ignorado.
4. Selecione **Instalar VMware PowerCLI**. Certifique-se de que todas as janelas do browser estão fechadas antes de efetuar este procedimento. Em seguida, selecione **Continuar**.
5. Em **Validar configuração da aplicação**, os pré-requisitos são verificados antes de continuar.
6. Em **Configurar servidor vCenter Server/vSphere ESXi**, introduza o FQDN ou endereço IP do servidor vCenter ou anfitrião vSphere onde estão localizadas as VMs que pretende replicar. Introduza a porta em que o servidor está a escutar. Introduza um nome amigável a utilizar para o servidor VMware no cofre.
7. Introduza as credenciais de utilizador que o servidor de configuração irá utilizar para ligar ao servidor VMware. Confirme que o nome de utilizador e a palavra-passe estão corretos e que fazem parte do grupo de Administradores da máquina virtual a ser protegida. O Site Recovery utiliza estas credenciais para detetar automaticamente as VMs VMware que estão disponíveis para replicação. Selecione **Adicionar** e, em seguida, selecione **Continuar**.
8. Em **Configurar as credenciais da máquina virtual**, introduza o nome de utilizador e a palavra-passe que servirão para instalar automaticamente o Serviço de Mobilidade nas VMs, quando a replicação estiver ativada.
    - Para computadores Windows, a conta precisa de privilégios de administrador local nos computadores que pretende replicar.
    - Para o Linux, forneça detalhes para a conta raiz.
9. Selecione **Finalizar configuração** para concluir o registo.
10. Após a conclusão do registo, no portal do Azure, certifique-se de que o servidor de configuração e o servidor VMware estão listados na página **Origem** do cofre. Em seguida, selecione **OK** para configurar as definições de destino.


O Site Recovery liga-se aos servidores VMware com as definições especificadas e deteta as VMs.

> [!NOTE]
> Pode demorar 15 minutos ou mais para o nome da conta aparecer no portal. Para atualizar imediatamente, selecione **Servidores de Configuração** > ***nome do servidor*** > **Atualizar Servidor**.

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione e verifique os recursos de destino.

1. Selecione **Preparar infraestrutura** > **Destino**. Selecione a subscrição do Azure que pretende utilizar. Estamos a utilizar um modelo do Resource Manager.
2. Recuperação de sites verifica que tem um ou mais redes virtuais. Deve tê-los ao configurar os componentes do Azure no [primeiro tutorial](tutorial-prepare-azure.md) nesta série de tutoriais.

   ![Separador Destino](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Abra o [portal do Azure](https://portal.azure.com) e selecione **Todos os recursos**.
2. Selecione o cofre dos Serviços de Recuperação (**ContosoVMVault** neste tutorial).
3. Para criar uma política de replicação, selecione **Infraestrutura do Site Recovery** > **Políticas de Replicação** > **+Política de Replicação**.
4. Em **Criar política de replicação**, introduza o nome da política. Estamos a utilizar o **VMwareRepPolicy**.
5. Em **Limiar de RPO**, utilize a predefinição de 60 minutos. Este valor define com que frequência são criados pontos de recuperação. Será gerado um alerta se a replicação contínua exceder este limite.
6. Na **Retenção do ponto de recuperação**, especifique quanto tempo cada ponto de recuperação será retido. Para este tutorial, utilizamos 72 horas. As VMs replicadas podem ser recuperadas para qualquer ponto numa janela de retenção.
7. Na **Frequência de instantâneos consistentes com a aplicação**, especifique a frequência com que são criados instantâneos consistentes com a aplicação. Estamos a utilizar a predefinição de 60 minutos. Selecione **OK** para criar a política.

   ![Criar política de replicação](./media/vmware-azure-tutorial/replication-policy.png)

- A política é associada automaticamente ao servidor de configuração.
- Uma política correspondente é criada automaticamente para reativação pós-falha por predefinição. Por exemplo, se a política de replicação for **rep-policy**, a política de reativação pós-falha é **rep-policy-failback**. Esta política não é utilizada depois de iniciar uma reativação pós-falha a partir do Azure.

## <a name="enable-replication"></a>Ativar a replicação

Pode ativar a replicação da seguinte forma:

1. Selecione **Replicar aplicação** > **Origem**.
1. Em **Origem**, selecione **No local** e selecione o servidor de configuração na **Localização de origem**.
1. Em **Tipo de máquina**, selecione **Máquinas Virtuais**.
1. Em **vCenter/vSphere Hypervisor**, selecione o anfitrião vSphere ou o servidor vCenter que gere o anfitrião.
1. Selecione o servidor de processo (instalado por predefinição na VM do servidor de configuração). Em seguida, selecione **OK**.
1. Em **Destino**, selecione a subscrição e o grupo de recursos no qual pretende criar as VMs de ativação pós-falha. Utilizamos o modelo de implementação do Resource Manager. 
1. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se ligam quando forem criadas após a ativação pós-falha.
1. Selecione **Configurar agora para as máquinas selecionadas** para aplicar a definição de rede para todas as VMs em que ativa a replicação. Selecione **Configurar mais tarde** para selecionar a rede do Azure por máquina.
1. Em **Máquinas Virtuais** > **Selecionar máquinas virtuais**, selecione cada máquina que pretende replicar. Só pode selecionar máquinas para as quais a replicação pode ser ativada. Em seguida, selecione **OK**. Se não é possível ver/selecionar qualquer máquina virtual específica, clique em [aqui](https://aka.ms/doc-plugin-VM-not-showing) para resolver o problema.
1. Em **Propriedades** > **Configurar propriedades**, selecione a conta que o servidor de processos irá utilizar para instalar automaticamente o Serviço de Mobilidade na máquina.
1. Em **Definições de replicação** > **Configurar as definições de replicação**, certifique-se de que a política de replicação correta está selecionada.
1. Selecione **Ativar Replicação**. O Site Recovery instala o Serviço de Mobilidade quando a replicação está ativada para uma VM.
1. Pode controlar o progresso da tarefa **Ativar Proteção** em **Definições** > **Tarefas** > **Tarefas do Site Recovery**. Depois de a tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.
1. Pode demorar 15 minutos ou mais tempo para as alterações produzirem efeitos e aparecerem no portal.
1. Para monitorizar as VMs que adiciona, verifique a última hora de deteção de VMs em **Servidores de Configuração** > **Último Contacto Em**. Para adicionar VMs sem aguardar a deteção agendada, realce o servidor de configuração (não o selecione) e selecione **Atualizar**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> Depois de ativar a replicação, [executar um teste de recuperação após desastre](site-recovery-test-failover-to-azure.md) para se certificar de que está tudo a funcionar conforme esperado.
