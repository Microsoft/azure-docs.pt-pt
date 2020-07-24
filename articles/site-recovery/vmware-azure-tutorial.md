---
title: Configurar a recuperação de desastres VMware VM para Azure com recuperação do site Azure
description: Saiba como configurar a recuperação após desastre para o Azure das VMs VMware no local com o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 2ad06a7583789a9f4db974251e7c2e7387b72549
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076085"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Configurar a recuperação após desastre para o Azure das VMs VMware no local

Este artigo descreve como permitir a replicação para VMware VMs no local, para recuperação de desastres para Azure usando o serviço [de recuperação do local de Azure.](site-recovery-overview.md)

Este é o terceiro tutorial de uma série que mostra como configurar a recuperação de desastres para Azure para VMware VMware no local. No tutorial anterior, [preparámos o ambiente VMware no local](vmware-azure-tutorial-prepare-on-premises.md) para a recuperação de desastres para Azure.


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar as definições de replicação de origem e um servidor de configuração de recuperação do local.
> * Configurar as definições do alvo de replicação.
> * Criar uma política de replicação.
> * Ativar a replicação de um VMware VM.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, reveja o artigo na secção Como Fazer da Tabela de Recuperação do Site de Conteúdos.

## <a name="before-you-start"></a>Antes de começar

Complete os tutoriais anteriores:
1. Certifique-se de que [instalou a Azure](tutorial-prepare-azure.md) para a recuperação de desastres da VMware no local para Azure.
2. Siga [estes passos](vmware-azure-tutorial-prepare-on-premises.md) para preparar a sua implementação VMware no local para a recuperação de desastres para Azure.
3. Neste tutorial mostramos-lhe como replicar um único VM. Se estiver a implementar vários VMware VMware, deve utilizar a [Ferramenta de Planeamento de Implementação](https://aka.ms/asr-deployment-planner). [Saiba mais](site-recovery-deployment-planner.md) sobre esta ferramenta.
4. Este tutorial utiliza uma série de opções que pode querer fazer de forma diferente:
    - O tutorial utiliza um modelo OVA para criar o VMware VMware do servidor de configuração. Se não conseguir fazê-lo por alguma razão, siga [estas instruções](physical-manage-configuration-server.md) para configurar manualmente o servidor de configuração.
    - Neste tutorial, a Recuperação do Site descarrega e instala automaticamente o MySQL no servidor de configuração. Se preferir, pode 15 001m manualmente. [Saiba mais](vmware-azure-deploy-configuration-server.md#configure-settings).




## <a name="select-a-protection-goal"></a>Selecionar um objetivo de proteção

1. Em **Cofres dos Serviços de Recuperação**, selecione o nome do cofre. Neste cenário, estamos a utilizar **ContosoVMVault**.
2. Em **Introdução**, selecione Site Recovery. Em seguida, selecione **Preparar Infraestrutura**.
3. Em **Objetivo de Proteção**  >  **Onde estão as suas máquinas,** selecione **On-ins**.
4. Em **Para onde pretende replicar as máquinas**, selecione **Para o Azure**.
5. Em **As suas máquinas estão virtualizadas**, selecione **Sim, com o VMware vSphere Hypervisor**. Em seguida, selecione **OK**.



## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

No seu ambiente de origem, necessita de uma única máquina, altamente disponível, no local para acolher estes componentes de Recuperação do Local no local:

- **Servidor de configuração**: O servidor de configuração coordena as comunicações entre as instalações e o Azure e gere a replicação de dados.
- **Servidor de**processo : O servidor de processo funciona como um gateway de replicação. Recebe dados de replicação; otimiza-o com caching, compressão e encriptação, e envia-o para uma conta de armazenamento de cache em Azure. O servidor de processo também instala o agente do Serviço de Mobilidade em VMs que pretende replicar, e executa a descoberta automática de VMware VMware no local.
- **Servidor alvo principal**: O servidor-alvo principal trata os dados de replicação durante a falha do Azure.


Todos estes componentes são instalados em conjunto nas máquinas únicas no local que é conhecido como o servidor de *configuração*. Por padrão, para a recuperação de desastres VMware, configuramos o servidor de configuração como um VMware VMware altamente disponível. Para isso, você descarrega um modelo de Aplicação de Virtualização Aberta (OVA) preparado e importa o modelo em VMware para criar o VM.

- A versão mais recente do servidor de configuração está disponível no portal. Também pode descarregá-lo diretamente do [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Se por alguma razão não puder utilizar um modelo OVA para configurar um VM, siga [estas instruções](physical-manage-configuration-server.md) para configurar manualmente o servidor de configuração.
- A licença fornecida com o modelo OVF é uma licença de avaliação válida por 180 dias. As janelas em execução no VM devem ser ativadas com a licença necessária.


### <a name="download-the-vm-template"></a>Transferir o modelo de VM

1. No cofre, vá para preparar a **fonte de infraestrutura.**  >  **Source**
2. Em **Preparar origem**, selecione **+Servidor de configuração**.
3. Em **Adicionar Servidor**, verifique se o **Servidor de configuração para VMware** é apresentado no **Tipo de servidor**.
4. Descarregue o modelo OVA para o servidor de configuração.



## <a name="import-the-template-in-vmware"></a>Importar o modelo no VMware


1. Inicie sessão no servidor VMware vCenter ou no anfitrião vSphere ESXi, com o VMWare vSphere Client.
2. No menu **'Ficheiro',** selecione **implementar o modelo OVF** para iniciar o **Assistente de Modelo de OVF**de implementação .

     ![Modelo OVF](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. Em **Selecionar origem**, introduza a localização do OVF transferido.
4. Em **Rever detalhes**, selecione **Seguinte**.
5. Em **Selecionar nome e pasta** e **Selecionar configuração**, aceite as predefinições.
6. Em **Selecionar armazenamento**, para obter o melhor desempenho, selecione **Thick Provision Eager Zeroed** em **Selecionar formato de disco virtual**.
7. Nas restantes páginas do assistente, aceite as predefinições.
8. Em **Pronto para concluir**, para configurar a VM com as predefinições, selecione **Ligar após a implementação** > **Concluir**.

   > [!TIP]
   > Se quiser adicionar um NIC adicional, **limpe a energia após a colocação**  >  **Terminar**. Por predefinição, o modelo contém uma NIC única. Pode adicionar mais NICs após a implementação.

## <a name="add-an-additional-adapter"></a>Adicionar outro adaptador

Se pretender adicionar um NIC adicional ao servidor de configuração, adicione-o antes de registar o servidor no cofre. Adicionar mais adaptadores não é suportado após o registo.

1. No inventário do vSphere Client, clique na VM com o botão direito do rato e selecione **Editar Definições**.
2. Em **Hardware**, selecione **Adicionar** > **Adaptador Ethernet**. Em seguida, selecione **Seguinte**.
3. Selecione um tipo de adaptador e uma rede.
4. Para ligar o NIC virtual quando a VM estiver ligada, selecione **Estabelecer ligação ao ligar**. Selecione **Seguinte** > **Concluir**. Em seguida, selecione **OK**.


## <a name="register-the-configuration-server"></a>Registar o servidor de configuração

Depois de configurar o servidor de configuração, regista-o no cofre.

1. A partir da consola do VMWare vSphere Client, ligue a VM.
2. A VM arranca para uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e introduza uma palavra-passe de administrador.
3. Após a conclusão da instalação, inicie sessão na VM como administrador.
4. Da primeira vez que iniciar sessão, a Ferramenta de Configuração do Azure Site Recovery é iniciada em poucos segundos.
5. Introduza um nome que será utilizado para registar o servidor de configuração no Site Recovery. Em seguida, selecione **Seguinte**.
6. A ferramenta verifica se a VM pode ligar ao Azure. Depois de a ligação estar estabelecida, selecione **Iniciar sessão** para iniciar sessão na sua subscrição do Azure. As credenciais têm de ter acesso ao cofre no qual pretende registar o servidor de configuração. Certifique-se de que as [funções](vmware-azure-deploy-configuration-server.md#azure-active-directory-permission-requirements) necessárias são atribuídas a este utilizador.
7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reiniciada.
8. Inicie sessão na máquina novamente. Em poucos segundos, o Assistente de Gestão do Servidor de Configuração inicia automaticamente.


### <a name="configure-settings-and-add-the-vmware-server"></a>Configurar as definições e adicionar o servidor VMware

Termine de configurar e registar o servidor de configuração. Antes de prosseguir, certifique-se de que todos os [pré-requisitos](vmware-azure-deploy-configuration-server.md#prerequisites) são cumpridos para uma configuração bem sucedida do servidor de configuração.


1. No assistente de gestão do servidor de configuração, selecione **a conectividade de configuração**. A partir das reduções, selecione primeiro o NIC que o servidor de processo incorporado utiliza para a descoberta e a instalação de serviço de mobilidade em máquinas de origem e, em seguida, selecione o NIC que o Servidor de Configuração utiliza para conectividade com o Azure. Em seguida, selecione **Guardar**. Não é possível alterar esta definição depois de configurada.
2. No **cofre Select Recovery Services**, selecione a sua subscrição Azure e o grupo de recursos relevantes e o cofre.
3. Em **Instalar software de terceiros**, aceite o contrato de licença. Selecione **Transferir e Instalar** para instalar o Servidor MySQL. Se colocar o MySQL no caminho, este passo pode ser ignorado. Saiba [mais](vmware-azure-deploy-configuration-server.md#configure-settings)
4. Em **Validar configuração da aplicação**, os pré-requisitos são verificados antes de continuar.
5. Em **Configurar servidor vCenter Server/vSphere ESXi**, introduza o FQDN ou endereço IP do servidor vCenter ou anfitrião vSphere onde estão localizadas as VMs que pretende replicar. Introduza a porta em que o servidor está a escutar. Introduza um nome amigável a utilizar para o servidor VMware no cofre.
6. Introduza as credenciais de utilizador que o servidor de configuração irá utilizar para ligar ao servidor VMware. Confirme que o nome de utilizador e a palavra-passe estão corretos e que fazem parte do grupo de Administradores da máquina virtual a ser protegida. O Site Recovery utiliza estas credenciais para detetar automaticamente as VMs VMware que estão disponíveis para replicação. Selecione **Adicionar** e, em seguida, selecione **Continuar**.
7. Em **Configurar as credenciais da máquina virtual**, introduza o nome de utilizador e a palavra-passe que servirão para instalar automaticamente o Serviço de Mobilidade nas VMs, quando a replicação estiver ativada.
    - Para computadores Windows, a conta precisa de privilégios de administrador local nos computadores que pretende replicar.
    - Para o Linux, forneça detalhes para a conta raiz.
8. Selecione **Finalizar configuração** para concluir o registo.
9. Após o início do registo, abra o portal Azure e verifique se o servidor de configuração e o servidor VMware estão listados nos **Servidores**de Configuração da Infraestrutura de  >  **Manage**  >  **Recuperação do Cofre**do Cofre  >  **Configuration Servers**de Recuperação do Cofre .


Após o registo do servidor de configuração, a Recuperação do Site liga-se aos servidores VMware utilizando as definições especificadas e descobre VMs.

> [!NOTE]
> Pode demorar 15 minutos ou mais para o nome da conta aparecer no portal. Para atualizar imediatamente, selecione **Servidor de Configuração**  >  ***servidor nome***Refresh  >  **Server**.

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione e verifique os recursos de destino.

1. Selecione Preparar o alvo **da infraestrutura**  >  **Target**. Selecione a subscrição do Azure que pretende utilizar. Estamos a utilizar um modelo do Resource Manager.
2. A Recuperação do Site verifica se tem uma ou mais redes virtuais. Deve tê-los ao configurar os componentes do Azure no [primeiro tutorial](tutorial-prepare-azure.md) nesta série de tutoriais.

   ![Separador Destino](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Abra o [portal do Azure](https://portal.azure.com). Procure e selecione **cofres dos Serviços de Recuperação.**
2. Selecione o cofre dos Serviços de Recuperação (**ContosoVMVault** neste tutorial).
3. Para criar uma política de replicação, selecione Políticas de replicação **da infraestrutura de recuperação**de  >  **Replication Policies**  >  **sítios +Política de replicação**.
4. Em **Criar política de replicação**, introduza o nome da política. Estamos a utilizar o **VMwareRepPolicy**.
5. Em **Limiar de RPO**, utilize a predefinição de 60 minutos. Este valor define com que frequência são criados pontos de recuperação. Será gerado um alerta se a replicação contínua exceder este limite.
6. Na **Retenção do ponto de recuperação**, especifique quanto tempo cada ponto de recuperação será retido. Para este tutorial, utilizamos 72 horas. As VMs replicadas podem ser recuperadas para qualquer ponto numa janela de retenção.
7. Na **Frequência de instantâneos consistentes com a aplicação**, especifique a frequência com que são criados instantâneos consistentes com a aplicação. Estamos a utilizar a predefinição de 60 minutos. Selecione **OK** para criar a política.

   ![Criar política de replicação](./media/vmware-azure-tutorial/replication-policy.png)

- A política é associada automaticamente ao servidor de configuração.
- Uma política correspondente é criada automaticamente para reativação pós-falha por predefinição. Por exemplo, se a política de replicação for **rep-policy**, a política de reativação pós-falha é **rep-policy-failback**. Esta política não é utilizada depois de iniciar uma reativação pós-falha a partir do Azure.

Nota: No cenário VMware-to-Azure, o instantâneo consistente com o crash é tirado a um intervalo de 5 min.

## <a name="enable-replication"></a>Ativar a replicação

Ativar a replicação dos VMs da seguinte forma:

1. Selecione **Aplicação Replicate**  >  **Fonte**.
2. Em **Origem**, selecione **No local** e selecione o servidor de configuração na **Localização de origem**.
3. Em **Tipo de máquina**, selecione **Máquinas Virtuais**.
4. Em **vCenter/vSphere Hypervisor**, selecione o anfitrião vSphere ou o servidor vCenter que gere o anfitrião.
5. Selecione o servidor de processo (instalado por predefinição na VM do servidor de configuração). Em seguida, selecione **OK**. O estado de saúde de cada servidor de processo é indicado de acordo com os limites recomendados e outros parâmetros. Escolha um servidor de processo saudável. Um servidor de processo [crítico](vmware-physical-azure-monitor-process-server.md#process-server-alerts) não pode ser escolhido. Pode [resolver problemas e resolver](vmware-physical-azure-troubleshoot-process-server.md) os erros **ou** configurar um servidor de processo [de escala](vmware-azure-set-up-process-server-scale.md).
6. Em **Destino**, selecione a subscrição e o grupo de recursos no qual pretende criar as VMs de ativação pós-falha. Utilizamos o modelo de implementação do Resource Manager.
7. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se ligam quando forem criadas após a ativação pós-falha.
8. Selecione **Configurar agora para as máquinas selecionadas** para aplicar a definição de rede para todas as VMs em que ativa a replicação. Selecione **Configurar mais tarde** para selecionar a rede do Azure por máquina.
9. Em **Máquinas Virtuais**  >  **Selecione máquinas virtuais,** selecione cada máquina que pretende replicar. Só pode selecionar máquinas para as quais a replicação pode ser ativada. Em seguida, selecione **OK**. Se não conseguir visualizar/selecionar nenhuma máquina virtual em particular, [saiba mais](https://aka.ms/doc-plugin-VM-not-showing) sobre a resolução do problema.
10. Nas propriedades **De Configuração de**  >  **Propriedades**, selecione a conta a utilizar pelo servidor de processos para instalar automaticamente o Serviço de Mobilidade na máquina.
11. Nas **definições de replicação,**  >  **certifique-se de que**a política de replicação correta está selecionada.
12. Selecione **Ativar a replicação**. O Site Recovery instala o Serviço de Mobilidade quando a replicação está ativada para uma VM.
13. Pode acompanhar o progresso do trabalho de **Proteção ativa** em **Definições**  >  **de**  >  **Empregos Locais de Recuperação do Local .** Após o **trabalho de Finalize Protection** e uma geração de ponto de recuperação estar completa, a máquina está pronta para o failover.
14. Pode demorar 15 minutos ou mais tempo para as alterações produzirem efeitos e aparecerem no portal.
15. Para monitorizar os VMs que adicionar, verifique a última vez descoberta para VMs em **Servidores de Configuração**  >  **Último Contacto Em**. Para adicionar VMs sem aguardar a deteção agendada, realce o servidor de configuração (não o selecione) e selecione **Atualizar**.

## <a name="next-steps"></a>Passos seguintes
Depois de permitir a replicação, faça um berbequim para garantir que tudo está funcionando como esperado.
> [!div class="nextstepaction"]
> [Executar um teste de recuperação após desastre](site-recovery-test-failover-to-azure.md)
