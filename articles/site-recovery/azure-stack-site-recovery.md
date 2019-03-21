---
title: Replicar VMs do Azure Stack para o Azure com o Azure Site Recovery | Documentos da Microsoft
description: Saiba como configurar a recuperação após desastre para o Azure das VMs de pilha do Azure com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: a84cbba968baf50563a2c2b0e2843d64f17bb34a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002380"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Replicar VMs do Azure Stack para o Azure

Este artigo mostra-lhe como configurar a recuperação após desastre do Azure Stack VMs para o Azure, utilizando o [serviço Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview).

Site Recovery contribui para a sua estratégia de recuperação (BCDR) de desastre e continuidade empresariais. O serviço garante que as cargas de trabalho VM permaneçam disponíveis quando esperado e ocorrerem falhas inesperadas.

- Site Recovery orquestra e gere a replicação de VMs para o armazenamento do Azure.
- Quando ocorre uma falha no seu site primário, utilize o Site Recovery para a ativação pós-falha para o Azure.
- Na ativação pós-falha, as VMs do Azure são criadas a partir os dados da VM armazenados e os utilizadores podem continuar a aceder a cargas de trabalho em execução nessas VMS do Azure.
- Quando tudo estiver em funcionamento novamente, pode efetuar a ativação de VMs atrás do Azure para o site primário e começar a replicar novamente para o armazenamento do Azure.


Neste artigo, vai aprender a:

> [!div class="checklist"]
> * **Passo 1: Preparar VMs do Azure stack para a replicação**. Verificar se as VMs estão em conformidade com requisitos de recuperação de sites e preparam a instalação do serviço de mobilidade de recuperação de Site do. Este serviço é instalado em cada VM que pretende replicar.
> * **Passo 2: Configurar um cofre dos serviços de recuperação**. Configurar um cofre do Site Recovery e especifique que pretende replicar. Componentes da recuperação de site e as ações são configuradas e geridas no cofre.
> * **Passo 3: Configurar o ambiente de replicação de origem**. Configure um servidor de configuração do Site Recovery. O servidor de configuração é uma VM única de pilha do Azure que executa todos os componentes necessários pelo Site Recovery. Depois de ter configurado o servidor de configuração, registe-o no cofre.
> * **Passo 4: Configurar o ambiente de replicação de destino**. Selecione a sua conta do Azure e a conta de armazenamento do Azure e a rede que pretende utilizar. Durante a replicação, os dados da VM são copiados para o armazenamento do Azure. Após a ativação pós-falha, as VMs do Azure são associadas à rede especificada.
> * **Passo 5: Ativar a replicação**. Configurar as definições de replicação e ativar a replicação para as VMs. O serviço de mobilidade será instalado numa VM quando a replicação está ativada. Recuperação de site efetua uma replicação inicial da VM e, em seguida, começa a replicação em curso.
> * **Passo 6: Executar um teste de recuperação após desastre**: Após a replicação está em execução, é a verificar que a ativação pós-falha irá funcionar conforme esperado ao executar um teste. Para iniciar o teste, executar uma ativação pós-falha no Site Recovery. Ativação pós-falha de teste não afeta o seu ambiente de produção.

Com estes passos completos, em seguida, pode executar uma ativação pós-falha completa para o Azure como e quando precisar de.

## <a name="architecture"></a>Arquitetura

![Arquitetura](./media/azure-stack-site-recovery/architecture.png)

**Localização** | **Componente** |**Detalhes**
--- | --- | ---
**Servidor de configuração** | É executada numa única VM do Azure Stack. | Cada subscrição vai configurar uma VM do servidor de configuração. Esta VM executa os seguintes componentes do Site Recovery:<br/><br/> -Servidor de configuração: Coordena as comunicações entre no local e o Azure e gere a replicação de dados. -Servidor de processos: Atua como um gateway de replicação. Ele recebe dados de replicação, otimiza com colocação em cache, compressão e encriptação; e envia-os para o armazenamento do Azure.<br/><br/> Se as VMs que pretende replicar excederem os limites indicados abaixo, pode configurar um servidor de processo autônomo separado. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-process-server-scale).
**Serviço de mobilidade** | Instalado em cada VM que pretende replicar. | Os passos neste artigo, vamos preparar uma conta para que o serviço de mobilidade é instalado automaticamente numa VM quando a replicação está ativada. Se não quiser instalar automaticamente o serviço, há inúmeros outros métodos que pode utilizar. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/vmware-azure-install-mobility-service).
**Azure** | No Azure, precisa ser um cofre dos serviços de recuperação, uma conta de armazenamento e uma rede virtual. |  Os dados replicados são armazenados na conta de armazenamento. VMs do Azure são adicionadas à rede do Azure quando ocorrer a ativação pós-falha. 


Replicação funciona da seguinte forma:

1. No cofre, especifique a origem de replicação e de destino, configurar o servidor de configuração, criar uma política de replicação e ative a replicação.
2. O serviço de mobilidade está instalado no computador (se já utilizou a instalação push) e máquinas iniciar a replicação em conformidade com a política de replicação.
3. Uma cópia inicial dos dados de servidor é replicada para o armazenamento do Azure.
4. Depois de concluída a replicação inicial, começa a replicação das alterações delta para o Azure. As alterações registadas relativas a uma máquina são guardadas num ficheiro .hrl.
5. O servidor de configuração orquestra a gestão da replicação com o Azure (porta HTTPS 443 de saída).
6. O servidor de processos recebe dados de máquinas de origem, otimiza e criptografa- e envia-os para o armazenamento do Azure (porta 443 de saída).
7. Máquinas replicadas comunicam com o servidor de configuração (porta HTTPS 443 de entrada, para a gestão de replicação. As máquinas enviam dados de replicação para o servidor de processos (porta HTTPS 9443 de entrada - pode ser modificada).
8. O tráfego é replicado para pontos finais públicos do armazenamento do Azure, através da Internet. Em alternativa, pode utilizar o peering público do Azure ExpressRoute. A replicação de tráfego através de uma rede de VPNs a partir de um site no local para o Azure não é suportada.

## <a name="prerequisites"></a>Pré-requisitos

Eis o que precisa de configurar este cenário.

**Requisito** | **Detalhes**
--- | ---
**Conta de subscrição do Azure** | Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
**Permissões de conta do Azure** | A conta do Azure que utiliza necessita de permissões para:<br/><br/> -Criar um cofre de serviço de recuperação<br/><br/> -Criar uma máquina virtual no grupo de recursos e utilizar para o cenário de rede virtual<br/><br/> -Escrever para a conta de armazenamento especificada<br/><br/> Tenha em atenção que:<br/><br/> -Se criar uma conta, o administrador da sua subscrição e pode efetuar todas as ações.<br/><br/> – Se utilizar uma subscrição existente e não for o administrador, terá de trabalhar com o administrador para lhe atribuir permissões de proprietário ou contribuinte.<br/><br/> – Se tiver permissões mais granulares, reveja [este artigo](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control). 
**VM do Azure Stack** | Terá de VM do Azure Stack na subscrição de inquilino, que irá ser implementada como o servidor de configuração do Site Recovery. 


### <a name="prerequisites-for-the-configuration-server"></a>Pré-requisitos para o servidor de configuração

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]


 
## <a name="step-1-prepare-azure-stack-vms"></a>Passo 1: Preparar VMs do Azure Stack

### <a name="verify-the-operating-system"></a>Verifique se o sistema operativo

Certifique-se de que as VMs estão em execução um dos sistemas operativos resumidos na tabela.


**Sistema operativo** | **Detalhes**
--- | ---
**Windows de 64 bits** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (from SP1)
**CentOS** | 5.2 para 5.11, 6.1 para 6.9, 7.0 para 7.3
**Ubuntu** | 14.04 server LTS, 16.04 LTS server. Revisão [suportado kernels](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#ubuntu-kernel-versions)

### <a name="prepare-for-mobility-service-installation"></a>Preparar para a instalação do serviço de mobilidade

Todas as VMS que pretende replicar tem de ter o serviço de mobilidade instalado. Por ordem para o servidor de processos instalar o serviço automaticamente na VM quando a replicação é ativada, verifique as definições de VM.

#### <a name="windows-machines"></a>Máquinas do Windows

- Precisa de conectividade entre a VM em que pretende ativar a replicação e a máquina com o servidor de processos de rede (por predefinição, é a VM do servidor de configuração).
- Precisa de uma conta com direitos de administrador (domínio ou local) na máquina para o qual pode ativar a replicação.
    - Esta conta é especificada quando configurou o Site Recovery. Em seguida, o servidor de processos utiliza esta conta para instalar o serviço de mobilidade quando a replicação está ativada.
    - Esta conta apenas será utilizada pelo Site Recovery para a instalação de push e para atualizar o serviço de mobilidade.
    - Se não estiver a utilizar uma conta de domínio, tem de desativar o controlo de acesso de utilizador remoto na VM:
        - No Registro, criar valor DWORD **LocalAccountTokenFilterPolicy** em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System.
        - Defina o valor como 1.
        - Para fazer isso no prompt de comando, digite o seguinte: **REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1**.
- Na Firewall do Windows na VM que pretende replicar, permitem o compartilhamento de arquivos e impressoras e WMI.
    - Para tal, execute **msc** para abrir a consola da Firewall do Windows. Clique com botão direito **regras de entrada** > **nova regra**. Selecione **predefinidos**e escolha **arquivos e impressoras partilha** da lista. Conclua o assistente, selecione para permitir a ligação > **concluir**.
    - Para computadores de domínio, pode usar um GPO para fazer isso.

    
#### <a name="linux-machines"></a>Máquinas do Linux

- Certifique-se de que existe conectividade de rede entre o computador Linux e o servidor de processos.
- No computador para o qual pode ativar a replicação, precisa de uma conta que seja um utilizador de raiz num servidor Linux de origem:
    - Esta conta é especificada quando configurou o Site Recovery. Em seguida, o servidor de processos utiliza esta conta para instalar o serviço de mobilidade quando a replicação está ativada.
    - Esta conta apenas será utilizada pelo Site Recovery para a instalação de push e para atualizar o serviço de mobilidade.
- Verifique se o ficheiro /etc/hosts no servidor Linux de origem tem entradas que mapeiam o nome de anfitrião local para endereços IP associados a todos os adaptadores de rede.
- Instale os pacotes openssh, openssh-server e openssl mais recentes no computador que quer replicar.
- Certifique-se de que o Secure Shell (SSH) está ativado e em execução na porta 22.
- Ative a autenticação de subsistema e palavra-passe SFTP no ficheiro sshd_config:
    1. Para tal, iniciar sessão como raiz.
    2. Encontre a linha que começa com **PasswordAuthentication**, no ficheiro /etc/ssh/sshd_config. Remova os comentários da linha e altere o valor para **sim**.
    3. Encontre a linha que começa por **Subsystem** e remova os comentários da mesma.

        ![Serviço de mobilidade do Linux](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Reinicie o serviço de sshd.


### <a name="note-the-vm-private-ip-address"></a>Tenha em atenção o endereço IP privado da VM

Para cada máquina que pretende replicar, encontre o endereço IP:

1. No Portal do Azure Stack, clique na VM.
2. Sobre o **Resource** menu, clique em **Interfaces de rede**.
3. Tome nota do particular endereço IP.

    ![Endereço IP privado](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>Passo 2: Criar um cofre e selecione um objetivo de replicação

1. No portal do Azure, selecione **criar um recurso** > **ferramentas de gestão** > **cópia de segurança e recuperação de Site**.
2. Em **Nome**, introduza um nome amigável para identificar o cofre. 
3. Na **grupo de recursos**, crie ou selecione um grupo de recursos. Estamos a utilizar **contosoRG**.
4. Na **localização**, introduza a região do Azure. estamos a utilizar **Europa Ocidental**.
5. Para aceder rapidamente ao cofre a partir do dashboard, selecione **Afixar ao dashboard** > **Criar**.

   ![Criar um novo cofre](./media/azure-stack-site-recovery/new-vault-settings.png)

   O novo cofre é apresentado em **Dashboard** > **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

### <a name="select-a-replication-goal"></a>Selecionar um objetivo de replicação

1. Na **cofres dos serviços de recuperação** > especifique um nome de cofre. Estamos a utilizar **ContosoVMVault**.
2. Em **Introdução**, selecione Site Recovery. Em seguida, selecione **Preparar Infraestrutura**.
3. Em **Objetivo de proteção** > **Onde estão localizadas as máquinas virtuais**, selecione **No local**.
4. Em **Para onde pretende replicar as máquinas**, selecione **Para o Azure**.
5. Na **as suas máquinas virtualizadas estão**, selecione **não virtualizado/outro**. Em seguida, selecione **OK**.

    ![Objetivo de proteção](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>Passo 3: Configurar o ambiente de origem

Configurar a máquina do servidor de configuração, registe-o no cofre e detetar máquinas que pretende replicar.

1. Clique em **Preparar a Infraestrutura** > **Origem**.
2. Em **Preparar origem**, clique em **+Servidor de configuração**.

    ![Configurar a origem](./media/azure-stack-site-recovery/plus-config-srv.png)

3. Na **Adicionar servidor**, verifique se **servidor de configuração** aparece no **tipo de servidor**.
5. Transfira o ficheiro de instalação do Site Recovery Unified Setup.
6. Transfira a chave de registo do cofre. Terá da chave de registo ao executar a configuração unificada. A chave é válida durante cinco dias depois de gerá-la.

    ![Configurar a origem](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Configuração unificada de execução do Azure Site Recovery

Para instalar e registar o servidor de configuração, fazer uma ligação RDP para a VM que pretende utilizar para o servidor de configuração e execute a configuração unificada.

Antes de começar, certifique-se de que o relógio [sincronizado com um servidor de tempo](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) na VM antes de começar. A instalação falha se o tempo é mais do que cinco minutos fora da hora local.

Agora a instalar o servidor de configuração:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Também pode ser instalado o servidor de configuração a partir da linha de comando. [Saiba mais](physical-manage-configuration-server.md#install-from-the-command-line).
> 
> Pode demorar 15 minutos ou mais para o nome da conta aparecer no portal. Para atualizar imediatamente, selecione **Servidores de Configuração** > ***nome do servidor*** > **Atualizar Servidor**.

## <a name="step-4-set-up-the-target-environment"></a>Passo 4: Configurar o ambiente de destino

Selecione e verifique os recursos de destino.

1. Na **preparar infraestrutura** > **destino**, selecione a subscrição do Azure que pretende utilizar.
2. Especifique o modelo de implementação de destino.
3. A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis. Se não encontrá-los, terá de criar pelo menos uma conta de armazenamento e rede virtual, para concluir o assistente.


## <a name="step-5-enable-replication"></a>Passo 5: Ativar a replicação

### <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Clique em **preparar infraestrutura** > **definições de replicação**.
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limiar RPO**, especifique o limite do objetivo de ponto de recuperação (RPO).
    - Pontos de recuperação para os dados replicados são criados de acordo com o conjunto de tempo.
    - Esta definição não afeta a replicação, que é contínua. Simplesmente emite um alerta se o limite do limiar for atingido sem um ponto de recuperação que está sendo criado.
4. Na **retenção do ponto de recuperação**, especifique o intervalo de tempo que cada ponto de recuperação é mantido. VMs replicadas podem ser recuperadas para qualquer ponto numa janela de tempo especificado.
5. Na **frequência de instantâneos consistentes com a aplicação**, especifique a frequência com que os instantâneos consistentes com a aplicação são criados.

    - Um instantâneo consistente com a aplicação é um instantâneo de ponto no tempo dos dados da aplicação dentro da VM.
    - Serviço de cópia de sombra de volumes (VSS) garante que as aplicações na VM estão num estado consistente quando o instantâneo.
6. Selecione **OK** para criar a política.


### <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

Pode ignorar este passo neste momento. Na **planejamento da implantação** lista pendente, clique em **Sim, já o fiz**.



### <a name="enable-replication"></a>Ativar a replicação

Certifique-se de que concluiu todas as tarefas no [passo 1: Preparar máquina](#step-1-prepare-azure-stack-vms). Em seguida, ative a replicação da seguinte forma:

1. Selecione **Replicar aplicação** > **Origem**.
2. Em **Origem**, selecione o servidor de configuração.
3. Na **tipo de máquina**, selecione **máquinas físicas**.
4. Selecione o servidor de processos (servidor de configuração). Em seguida, clique em **OK**.
5. Na **destino**, selecione a subscrição e o grupo de recursos no qual pretende criar as VMs após a ativação pós-falha. Selecione o modelo de implementação que pretende utilizar para as VMs com ativação pós-falha.
6. Selecione a conta de armazenamento do Azure na qual pretende armazenar os dados replicados.
7. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se ligam quando forem criadas após a ativação pós-falha.
8. Selecione **Configurar agora para as máquinas selecionadas** para aplicar a definição de rede para todas as máquinas selecionadas para proteção. Selecione **configurar mais tarde** se pretende selecionar a rede do Azure em separado para cada máquina.
9. Na **máquinas físicas**, clique em **+ computador**. Especifique o nome, o endereço IP e o tipo de SO de cada máquina que pretende replicar.

    - Utilize o endereço IP da máquina.
    - Se especificar o endereço IP público, a replicação poderá não funcionar conforme esperado.

10. Na **propriedades** > **configurar propriedades**, selecione a conta que o servidor de processos irá utilizar para instalar automaticamente o serviço de mobilidade na máquina.
11. Na **as definições de replicação** > **configurar as definições de replicação**, verifique que a política de replicação correta está selecionada.
12. Clique em **Ativar Replicação**.
13. Controlar o progresso dos **ativar proteção** da tarefa na **definições** > **tarefas** > **tarefas do Site Recovery**. Depois de a tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.

> [!NOTE]
> O Site Recovery instala o Serviço de Mobilidade quando a replicação está ativada para uma VM.
> 
> Pode demorar 15 minutos ou mais tempo para as alterações produzirem efeitos e aparecerem no portal.
> 
> Para monitorizar as VMs que adiciona, verifique a última hora de deteção de VMs em **Servidores de Configuração** > **Último Contacto Em**. Para adicionar VMs sem aguardar a deteção agendada, realce o servidor de configuração (não o selecione) e selecione **Atualizar**.


## <a name="step-6-run-a-disaster-recovery-drill"></a>Passo 6: Executar um teste de recuperação após desastre

Executar uma ativação pós-falha de teste para o Azure para se certificar de que está tudo a funcionar conforme esperado. Esta ativação pós-falha não afeta o seu ambiente de produção.

### <a name="verify-machine-properties"></a>Verifique se as propriedades da máquina

Antes de executar uma ativação pós-falha de teste, verifique as propriedades da máquina e certifique-se de que estão em conformidade com [requisitos do Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements). Pode ver e modificar as propriedades da seguinte forma:

1. Em **Itens Protegidos**, clique em **Itens Replicados** > VM.
2. No painel **Item replicado**, existe um resumo das informações, do estado de funcionamento e dos pontos de recuperação mais recentes disponíveis da VM. Clique em **Propriedades** para ver mais detalhes.
3. Na **computação e rede**, modifique as definições conforme necessário.

    - Pode modificar o nome de VM do Azure, o grupo de recursos, o tamanho de destino, [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md)e as definições de disco gerido.
    - Também pode ver e modificar as definições de rede. Estes incluem a rede/sub-rede à qual está associada a VM do Azure após a ativação pós-falha e o endereço IP que será atribuído à VM.
1. Na **discos**, ver informações sobre o sistema operativo e os discos de dados na VM.
   

### <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Quando executa uma ativação pós-falha de teste, ocorre o seguinte:

1. É executada uma verificação dos pré-requisitos, para garantir que estão satisfeitas todas as condições necessárias para a ativação pós-falha.
2. A ativação pós-falha processa os dados utilizando o ponto de recuperação especificada:
    - **Processado mais recentemente**: A máquina faz a ativação pós-falha do ponto de recuperação mais recente processado pelo Site Recovery. O carimbo de data/hora é apresentado. Com esta opção, não é despendido tempo a processar os dados, pelo que oferece um RTO (objetivo de tempo de recuperação) baixo.
    - **Mais recente consistente com a aplicação**: A máquina faz a ativação pós-falha do ponto de recuperação consistente com a aplicação mais recente.
    - **Custom**: Selecione o ponto de recuperação utilizado para a ativação pós-falha.

3. Uma VM do Azure é criada com os dados processados.
4. Ativação pós-falha de teste pode limpar automaticamente as VMs do Azure criadas durante o teste.

Execute uma ativação pós-falha de teste para uma VM da seguinte forma:

1. Em **Definições** > **Itens Replicados**, clique na VM > **+Testar Ativação Pós-falha**.
2. Nestas instruções, vamos selecionar para utilizar o **processado mais recentemente** ponto de recuperação. 
3. Na **ativação pós-falha de teste**, selecione a rede do Azure de destino.
4. Clique em **OK** para iniciar a ativação pós-falha.
5. Controlar o progresso clicando na VM para abrir as respetivas propriedades. Ou, clique nas **ativação pós-falha de teste** da tarefa no *nome do cofre* > **definições** > **tarefas**  > **Tarefas de recuperação de site**.
6. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Verifique que a VM tem um tamanho adequado, ligado à rede certa e, em execução.
7. Deverá conseguir ligar-se à VM replicada no Azure agora. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover).
8. Para eliminar as VMs do Azure criadas durante a ativação pós-falha de teste, clique em **Limpar ativação pós-falha de teste** na VM. Na **notas**, guarde todas as observações associadas à ativação pós-falha de teste.

## <a name="fail-over-and-fail-back"></a>Ativação e reativação pós-falha

Depois que tiver configurado a replicação e executar um teste para garantir que tudo está funcionando, pode pós-falha de máquinas para o Azure conforme necessário.

Antes de executar uma ativação pós-falha, se pretender ligar à máquina no Azure após a ativação pós-falha, em seguida, [preparar a ligação](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover) antes de começar.

Em seguida, execute uma ativação pós-falha da seguinte forma:


1. Na **configurações** > **itens replicados**, clique na máquina > **ativação pós-falha**.
2. Selecione o ponto de recuperação que pretende utilizar.
3. Na **ativação pós-falha de teste**, selecione a rede do Azure de destino.
4. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. Com esta definição, o Site Recovery tenta encerrar a máquina de origem antes de iniciar a ativação pós-falha. No entanto, ativação pós-falha continua, mesmo que o encerramento falhe. 
5. Clique em **OK** para iniciar a ativação pós-falha. Pode seguir o progresso da ativação pós-falha na página **Trabalhos**.
6. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Se preparou para ligar após a ativação pós-falha, verifique que a VM tem um tamanho adequado, ligado à rede certa e, em execução.
7. Depois de verificar a VM, clique em **consolidar** para concluir a ativação pós-falha. Esta ação elimina todos os pontos de recuperação disponíveis.

> [!WARNING]
> Não cancele uma ativação pós-falha em curso: Antes de iniciar a ativação pós-falha, a replicação de VM é interrompida. Se cancelar uma ativação pós-falha que esteja em curso, a mesma para, mas a VM não será replicada outra vez.


### <a name="fail-back-to-azure-stack"></a>Reativação pós-falha para o Azure Stack

Quando o site primário estiver novamente em funcionamento, pode efetuar a reativação pós-falha do Azure para o Azure Stack. Para tal, terá de transferir o VHD de VM do Azure e carregue-o para o Azure Stack.

1. Encerre a VM do Azure, para que o VHD pode ser baixado. 
2. Para começar a transferir o VHD, instale [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).
3. Navegue para a VM no Portal do Azure (com o nome da VM).
4. Na **discos**, clique no nome do disco e recolher as definições.

    - Por exemplo, o URI do VHD utilizados no nosso teste: https://502055westcentralus.blob.core.windows.net/wahv9b8d2ceb284fb59287/copied-3676553984.vhd pode ser dividida obter os seguintes parâmetros que são utilizados para transferir o VHD de entrada.
        - Conta de Armazenamento: 502055westcentralus
        - Contentor: wahv9b8d2ceb284fb59287
        - Nome do VHD: copiados-3676553984.vhd

5. Agora, utilize o Explorador de armazenamento do Azure para transferir o VHD.
6. Carregar o VHD para o Azure Stack com [essas etapas](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-disks#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm).
7. Na VM existente ou nova VM, anexe os VHDs carregados.
8. Verifique se o disco do SO está correto e iniciar a VM.


Nesta fase, a reativação pós-falha foi concluída.


## <a name="conclusion"></a>Conclusão

Neste artigo vamos replicados VMs do Azure Stack para o Azure. Com a replicação no local, executamos um teste de recuperação após desastre para se certificar de que a ativação pós-falha do Azure funcionou como esperado. O artigo também inclui passos para executar uma ativação pós-falha completa para o Azure e reativa para o Azure Stack.

## <a name="next-steps"></a>Passos Seguintes

Após a reativação pós-falha, pode voltar a proteger a VM e iniciar a replicação-lo para o Azure novamente para isso, repita os passos neste artigo.

