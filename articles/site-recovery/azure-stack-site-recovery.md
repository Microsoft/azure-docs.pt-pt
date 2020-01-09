---
title: Replicar VMs Azure Stack no Azure usando Azure Site Recovery | Microsoft Docs
description: Saiba como configurar a recuperação de desastres para o Azure para VMs Azure Stack com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 08/05/2019
ms.author: raynew
ms.openlocfilehash: 15cd729063545914f791de39a075af9084f72bef
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426569"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Replicar as VMs do Azure Stack para o Azure

Este artigo mostra como configurar a recuperação de desastre Azure Stack VMs para o Azure, usando o [serviço Azure site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview).

Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre). O serviço garante que suas cargas de trabalho de VM permaneçam disponíveis quando esperadas e interrupções inesperadas ocorrerem.

- Site Recovery orquestra e gerencia a replicação de VMs para o armazenamento do Azure.
- Quando ocorre uma interrupção no site primário, você usa Site Recovery para fazer failover no Azure.
- No failover, as VMs do Azure são criadas a partir dos dados armazenados da VM e os usuários podem continuar acessando as cargas de trabalho em execução nessas VMs do Azure.
- Quando tudo estiver funcionando novamente, você poderá fazer failback de VMs do Azure para seu site primário e iniciar a replicação para o armazenamento do Azure novamente.


Neste artigo, vai aprender a:

> [!div class="checklist"]
> * **Etapa 1: preparar VMs do Azure Stack para replicação**. Verifique se as VMs estão em conformidade com os requisitos de Site Recovery e prepare-se para a instalação do serviço de mobilidade de Site Recovery. Esse serviço é instalado em cada VM que você deseja replicar.
> * **Etapa 2: configurar um cofre dos serviços de recuperação**. Configure um cofre para Site Recovery e especifique o que você deseja replicar. Site Recovery componentes e ações são configurados e gerenciados no cofre.
> * **Etapa 3: configurar o ambiente de replicação de origem**. Configure um servidor de configuração de Site Recovery. O servidor de configuração é uma única VM Azure Stack que executa todos os componentes necessários para Site Recovery. Depois de configurar o servidor de configuração, registre-o no cofre.
> * **Etapa 4: configurar o ambiente de replicação de destino**. Selecione sua conta do Azure e a conta de armazenamento do Azure e a rede que você deseja usar. Durante a replicação, os dados da VM são copiados para o armazenamento do Azure. Após o failover, as VMs do Azure são unidas à rede especificada.
> * **Etapa 5: habilitar a replicação**. Defina as configurações de replicação e habilite a replicação para VMs. O serviço de mobilidade será instalado em uma VM quando a replicação estiver habilitada. Site Recovery executa uma replicação inicial da VM e, em seguida, a replicação em andamento começa.
> * **Etapa 6: executar uma análise de recuperação de desastre**: depois que a replicação estiver em funcionamento, você verificará se o failover funcionará conforme o esperado ao executar uma análise. Para iniciar a análise, execute um failover de teste no Site Recovery. O failover de teste não afeta seu ambiente de produção.

Com essas etapas concluídas, você pode executar um failover completo para o Azure como e quando precisar.

## <a name="architecture"></a>Arquitetura

![Arquitetura](./media/azure-stack-site-recovery/architecture.png)

**Localização** | **Componente** |**Detalhes**
--- | --- | ---
**Servidor de configuração** | É executado em uma única VM Azure Stack. | Em cada assinatura, você configura uma VM do servidor de configuração. Essa VM executa os seguintes componentes de Site Recovery:<br/><br/> -Servidor de configuração: coordena as comunicações entre o local e o Azure e gerencia a replicação de dados. -Servidor de processo: atua como um gateway de replicação. Ele recebe dados de replicação, otimiza com caching, compactação e criptografia; e o envia para o armazenamento do Azure.<br/><br/> Se as VMs que você deseja replicar excederem os limites declarados abaixo, você poderá configurar um servidor de processo autônomo separado. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-process-server-scale).
**Serviço de mobilidade** | Instalado em cada VM que você deseja replicar. | Nas etapas deste artigo, preparamos uma conta para que o serviço de mobilidade seja instalado automaticamente em uma VM quando a replicação estiver habilitada. Se você não quiser instalar o serviço automaticamente, há vários outros métodos que podem ser usados. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/vmware-azure-install-mobility-service).
**Azure** | No Azure, você precisa de um cofre de serviços de recuperação, uma conta de armazenamento e uma rede virtual. |  Os dados replicados são armazenados na conta de armazenamento. As VMs do Azure são adicionadas à rede do Azure quando ocorre o failover. 


A replicação funciona da seguinte maneira:

1. No cofre, você especifica a origem e o destino da replicação, define o servidor de configuração, cria uma política de replicação e habilita a replicação.
2. O serviço de mobilidade está instalado no computador (se você usou a instalação por push) e os computadores iniciam a replicação de acordo com a política de replicação.
3. Uma cópia inicial dos dados do servidor é replicada para o armazenamento do Azure.
4. Após a conclusão da replicação inicial, a replicação de alterações delta para o Azure é iniciada. As alterações registadas relativas a uma máquina são guardadas num ficheiro .hrl.
5. O servidor de configuração orquestra o gerenciamento de replicação com o Azure (porta HTTPS 443 de saída).
6. O servidor de processo recebe dados de computadores de origem, otimiza-os e criptografa-os e os envia para o armazenamento do Azure (porta 443 de saída).
7. Os computadores replicados se comunicam com o servidor de configuração (porta HTTPS 443 de entrada, para o gerenciamento de replicação. Os computadores enviam dados de replicação para o servidor de processo (porta HTTPS 9443 de entrada-pode ser modificado).
8. O tráfego é replicado para pontos finais públicos do armazenamento do Azure, através da Internet. Como alternativa, você pode usar o emparelhamento público do Azure ExpressRoute. A replicação de tráfego através de uma rede de VPNs a partir de um site no local para o Azure não é suportada.

## <a name="prerequisites"></a>Pré-requisitos

Veja o que você precisa para configurar esse cenário.

**Requisito** | **Detalhes**
--- | ---
**Conta de assinatura do Azure** | Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
**Permissões de conta do Azure** | A conta do Azure usada precisa de permissões para:<br/><br/> -Criar um cofre do serviço de recuperação<br/><br/> -Criar uma máquina virtual no grupo de recursos e na rede virtual que você usa para o cenário<br/><br/> -Gravar na conta de armazenamento que você especificar<br/><br/> Tenha em atenção que:<br/><br/> -Se você criar uma conta, você é o administrador da sua assinatura e pode executar todas as ações.<br/><br/> -Se você usar uma assinatura existente e não for o administrador, precisará trabalhar com o administrador para atribuir permissões de proprietário ou colaborador.<br/><br/> -Se você precisar de permissões mais granulares, leia [Este artigo](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control). 
**VM Azure Stack** | Você precisa de uma VM Azure Stack na assinatura de locatário, que será implantada como o servidor de configuração de Site Recovery. 


### <a name="prerequisites-for-the-configuration-server"></a>Pré-requisitos para o servidor de configuração

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]


 
## <a name="step-1-prepare-azure-stack-vms"></a>Etapa 1: preparar VMs Azure Stack

### <a name="verify-the-operating-system"></a>Verificar o sistema operacional

Verifique se as VMs estão executando um dos sistemas operacionais resumidos na tabela.


**Sistema operativo** | **Detalhes**
--- | ---
**Windows de 64 bits** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (do SP1)
**CentOS** | 5,2 a 5,11, 6,1 a 6,9, 7,0 a 7,3
**Ubuntu** | 14, 4 LTS Server, 16, 4 LTS Server. Examinar [kernels com suporte](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#ubuntu-kernel-versions)

### <a name="prepare-for-mobility-service-installation"></a>Preparar para a instalação do serviço de mobilidade

Todas as VMs que você deseja replicar devem ter o serviço de mobilidade instalado. Para que o servidor de processo instale o serviço automaticamente na VM quando a replicação estiver habilitada, verifique as configurações da VM.

#### <a name="windows-machines"></a>Computadores Windows

- Você precisa de conectividade de rede entre a VM na qual deseja habilitar a replicação e a máquina que executa o servidor de processo (por padrão, essa é a VM do servidor de configuração).
- Você precisa de uma conta com direitos de administrador (domínio ou local) no computador para o qual você habilita a replicação.
    - Você especifica essa conta ao configurar Site Recovery. Em seguida, o servidor de processo usa essa conta para instalar o serviço de mobilidade quando a replicação é habilitada.
    - Essa conta só será usada pelo Site Recovery para a instalação por push e para atualizar o serviço de mobilidade.
    - Se você não estiver usando uma conta de domínio, será necessário desabilitar o controle de acesso de usuário remoto na VM:
        - No registro, crie o valor DWORD **LocalAccountTokenFilterPolicy** em HKEY_LOCAL_MACHINE \Software\Microsoft\Windows\CurrentVersion\Policies\System.
        - Defina o valor para 1.
        - Para fazer isso no prompt de comando, digite o seguinte: **REG ADD HKEY_LOCAL_MACHINE \Software\Microsoft\Windows\CurrentVersion\Policies\System/V LocalAccountTokenFilterPolicy/t REG_DWORD/d 1**.
- No firewall do Windows na VM que você deseja replicar, permita o compartilhamento de arquivos e impressoras e o WMI.
    - Para fazer isso, execute **WF. msc** para abrir o console do firewall do Windows. Clique com o botão direito do mouse em **regras de entrada** > **nova regra**. Selecione **predefinido**e escolha **compartilhamento de arquivos e impressoras** na lista. Conclua o assistente, selecione para permitir que a conexão > seja **concluída**.
    - Para computadores de domínio, você pode usar um GPO para fazer isso.

    
#### <a name="linux-machines"></a>Computadores Linux

- Certifique-se de que existe conectividade de rede entre o computador Linux e o servidor de processos.
- No computador para o qual você habilita a replicação, você precisa de uma conta que seja um usuário raiz no servidor Linux de origem:
    - Você especifica essa conta ao configurar Site Recovery. Em seguida, o servidor de processo usa essa conta para instalar o serviço de mobilidade quando a replicação é habilitada.
    - Essa conta só será usada pelo Site Recovery para a instalação por push e para atualizar o serviço de mobilidade.
- Verifique se o ficheiro /etc/hosts no servidor Linux de origem tem entradas que mapeiam o nome de anfitrião local para endereços IP associados a todos os adaptadores de rede.
- Instale os pacotes openssh, openssh-server e openssl mais recentes no computador que quer replicar.
- Certifique-se de que o Secure Shell (SSH) está ativado e em execução na porta 22.
- Ative a autenticação de subsistema e palavra-passe SFTP no ficheiro sshd_config:
    1. Para fazer isso, entre como raiz.
    2. Localize a linha que começa com **PasswordAuthentication**, no arquivo/etc/ssh/sshd_config. Remova os comentários da linha e altere o valor para **sim**.
    3. Encontre a linha que começa por **Subsystem** e remova os comentários da mesma.

        ![Serviço de mobilidade do Linux](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Reinicie o serviço sshd.


### <a name="note-the-vm-private-ip-address"></a>Anote o endereço IP privado da VM

Para cada computador que você deseja replicar, localize o endereço IP:

1. No portal de Azure Stack, clique na VM.
2. No menu de **recursos** , clique em **interfaces de rede**.
3. Anote o endereço IP privado.

    ![Endereço IP privado](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>Etapa 2: criar um cofre e selecionar uma meta de replicação

1. Na portal do Azure, selecione **criar um recurso** > **ferramentas de gerenciamento** > **backup e site Recovery**.
2. Em **Nome**, introduza um nome amigável para identificar o cofre. 
3. Em **grupo de recursos**, crie ou selecione um grupo de recursos. Estamos usando o **contosoRG**.
4. Em **local**, insira a região do Azure. estamos a utilizar **Europa Ocidental**.
5. Para aceder rapidamente ao cofre a partir do dashboard, selecione **Afixar ao dashboard** > **Criar**.

   ![Criar um novo cofre](./media/azure-stack-site-recovery/new-vault-settings.png)

   O novo cofre é apresentado em **Dashboard** > **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

### <a name="select-a-replication-goal"></a>Selecionar um objetivo de replicação

1. Em **cofres dos serviços de recuperação** > especifique um nome de cofre. Estamos usando o **ContosoVMVault**.
2. Em **Introdução**, selecione Site Recovery. Em seguida, selecione **Preparar Infraestrutura**.
3. Em **Objetivo de proteção** > **Onde estão localizadas as máquinas virtuais**, selecione **No local**.
4. Em **Para onde pretende replicar as máquinas**, selecione **Para o Azure**.
5. Em **seus computadores são virtualizados**, selecione **não virtualizados/outros**. Em seguida, selecione **OK**.

    ![Meta de proteção](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>Etapa 3: configurar o ambiente de origem

Configure o computador do servidor de configuração, registre-o no cofre e descubra as máquinas que você deseja replicar.

1. Clique em **Preparar a Infraestrutura** > **Origem**.
2. Em **Preparar origem**, clique em **+Servidor de configuração**.

    ![Configurar a origem](./media/azure-stack-site-recovery/plus-config-srv.png)

3. Em **Adicionar servidor**, verifique se o **servidor de configuração** aparece em tipo de **servidor**.
5. Baixe o arquivo de instalação do Site Recovery Unified setup.
6. Transfira a chave de registo do cofre. Você precisa da chave de registro ao executar a instalação unificada. A chave é válida durante cinco dias depois de gerá-la.

    ![Configurar a origem](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Executar Azure Site Recovery configuração unificada

Para instalar e registrar o servidor de configuração, faça uma conexão RDP com a VM que você deseja usar para o servidor de configuração e execute a instalação unificada.

Antes de começar, verifique se o relógio está [sincronizado com um servidor de horário](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) na VM antes de começar. A instalação falhará se o tempo tiver mais de cinco minutos fora da hora local.

Agora, instale o servidor de configuração:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> O servidor de configuração também pode ser instalado a partir da linha de comando. [Saiba mais](physical-manage-configuration-server.md#install-from-the-command-line).
> 
> Pode demorar 15 minutos ou mais para o nome da conta aparecer no portal. Para atualizar imediatamente, selecione **Servidores de Configuração** > ***nome do servidor*** > **Atualizar Servidor**.

## <a name="step-4-set-up-the-target-environment"></a>Etapa 4: configurar o ambiente de destino

Selecione e verifique os recursos de destino.

1. Em **preparar infraestrutura** > **destino**, selecione a assinatura do Azure que você deseja usar.
2. Especifique o modelo de implantação de destino.
3. A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis. Se ele não for encontrado, você precisará criar pelo menos uma conta de armazenamento e uma rede virtual para concluir o assistente.


## <a name="step-5-enable-replication"></a>Etapa 5: habilitar a replicação

### <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Clique em **preparar infraestrutura** > **configurações de replicação**.
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limiar RPO**, especifique o limite do objetivo de ponto de recuperação (RPO).
    - Os pontos de recuperação para dados replicados são criados de acordo com o tempo definido.
    - Essa configuração não afeta a replicação, que é contínua. Ele simplesmente emitirá um alerta se o limite for atingido sem a criação de um ponto de recuperação.
4. Em **retenção do ponto de recuperação**, especifique quanto tempo cada ponto de recuperação é mantido. As VMs replicadas podem ser recuperadas para qualquer ponto na janela de tempo especificada.
5. Em **frequência de instantâneo consistente com o aplicativo**, especifique com que frequência os instantâneos consistentes com o aplicativo são criados.

    - Um instantâneo consistente com o aplicativo é um instantâneo pontual dos dados do aplicativo dentro da VM.
    - O Serviço de Cópias de Sombra de Volume (VSS) garante que os aplicativos na VM estejam em um estado consistente quando o instantâneo for tirado.
6. Selecione **OK** para criar a política.


### <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

Você pode ignorar esta etapa agora. Na lista suspensa **planejamento de implantação** , clique em **Sim, fiz isso**.



### <a name="enable-replication"></a>Ativar a replicação

Verifique se você concluiu todas as tarefas na [etapa 1: preparar o computador](#step-1-prepare-azure-stack-vms). Em seguida, habilite a replicação da seguinte maneira:

1. Selecione **Replicar aplicação** > **Origem**.
2. Em **Origem**, selecione o servidor de configuração.
3. Em **tipo de computador**, selecione **computadores físicos**.
4. Selecione o servidor de processos (servidor de configuração). Em seguida, clique em **OK**.
5. Em **destino**, selecione a assinatura e o grupo de recursos no qual você deseja criar as VMs após o failover. Escolha o modelo de implantação que você deseja usar para as VMs com failover.
6. Selecione a conta de armazenamento do Azure na qual você deseja armazenar os dados replicados.
7. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se ligam quando forem criadas após a ativação pós-falha.
8. Selecione **Configurar agora para as máquinas selecionadas** para aplicar a definição de rede para todas as máquinas selecionadas para proteção. Selecione **configurar mais tarde** se desejar selecionar a rede do Azure separadamente para cada computador.
9. Em **computadores físicos**, clique em **+ computador físico**. Especifique o nome, o endereço IP e o tipo de so de cada computador que você deseja replicar.

    - Use o endereço IP interno do computador.
    - Se você especificar o endereço IP público, a replicação poderá não funcionar conforme o esperado.

10. Em **propriedades** > **Configurar Propriedades**, selecione a conta que o servidor de processo usará para instalar automaticamente o serviço de mobilidade no computador.
11. Em **configurações de replicação** > **definir configurações de replicação**, verifique se a política de replicação correta está selecionada.
12. Clique em **Ativar Replicação**.
13. Acompanhe o progresso do trabalho **habilitar proteção** em **configurações** > **trabalhos** > **trabalhos de site Recovery**. Depois de a tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.

> [!NOTE]
> O Site Recovery instala o Serviço de Mobilidade quando a replicação está ativada para uma VM.
> 
> Pode demorar 15 minutos ou mais tempo para as alterações produzirem efeitos e aparecerem no portal.
> 
> Para monitorizar as VMs que adiciona, verifique a última hora de deteção de VMs em **Servidores de Configuração** > **Último Contacto Em**. Para adicionar VMs sem aguardar a deteção agendada, realce o servidor de configuração (não o selecione) e selecione **Atualizar**.


## <a name="step-6-run-a-disaster-recovery-drill"></a>Etapa 6: executar uma análise de recuperação de desastre

Você executa um failover de teste no Azure para certificar-se de que tudo está funcionando conforme o esperado. Esse failover não afetará seu ambiente de produção.

### <a name="verify-machine-properties"></a>Verificar Propriedades da máquina

Antes de executar um failover de teste, verifique as propriedades do computador e certifique-se de que elas estejam em conformidade com [os requisitos do Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements). Você pode exibir e modificar as propriedades da seguinte maneira:

1. Em **Itens Protegidos**, clique em **Itens Replicados** > VM.
2. No painel **Item replicado**, existe um resumo das informações, do estado de funcionamento e dos pontos de recuperação mais recentes disponíveis da VM. Clique em **Propriedades** para ver mais detalhes.
3. Em **computação e rede**, modifique as configurações conforme necessário.

    - Você pode modificar o nome da VM do Azure, o grupo de recursos, o tamanho do destino, o [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md)e as configurações de disco gerenciado.
    - Você também pode exibir e modificar as configurações de rede. Isso inclui a rede/sub-rede para a qual a VM do Azure é unida após o failover e o endereço IP que será atribuído à VM.
1. Em **discos**, exiba informações sobre o sistema operacional e os discos de dados na VM.
   

### <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Quando executa uma ativação pós-falha de teste, ocorre o seguinte:

1. É executada uma verificação dos pré-requisitos, para garantir que estão satisfeitas todas as condições necessárias para a ativação pós-falha.
2. O failover processa os dados usando o ponto de recuperação especificado:
    - **Mais recente processado**: o computador faz failover para o último ponto de recuperação processado pelo site Recovery. O carimbo de data/hora é apresentado. Com esta opção, não é despendido tempo a processar os dados, pelo que oferece um RTO (objetivo de tempo de recuperação) baixo.
    - **Consistente com o aplicativo mais recente**: o computador faz failover para o ponto de recuperação consistente com o aplicativo mais recente.
    - **Personalizado**: selecione o ponto de recuperação usado para failover.

3. Uma VM do Azure é criada usando os dados processados.
4. O failover de teste pode limpar automaticamente as VMs do Azure criadas durante a análise.

Execute um failover de teste para uma VM da seguinte maneira:

1. Em **Definições** > **Itens Replicados**, clique na VM > **+Testar Ativação Pós-falha**.
2. Para esta explicação, selecionaremos usar o último ponto de recuperação **processado** . 
3. Em **failover de teste**, selecione a rede do Azure de destino.
4. Clique em **OK** para iniciar a ativação pós-falha.
5. Acompanhe o andamento clicando na VM para abrir suas propriedades. Ou clique no trabalho **failover de teste** no *nome do cofre* > **configurações** > **trabalhos** >**trabalhos site Recovery**.
6. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Verifique se a VM é do tamanho apropriado, conectada à rede correta e em execução.
7. Deverá conseguir ligar-se à VM replicada no Azure agora. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover).
8. Para eliminar as VMs do Azure criadas durante a ativação pós-falha de teste, clique em **Limpar ativação pós-falha de teste** na VM. Em **observações**, salve todas as observações associadas ao failover de teste.

## <a name="fail-over-and-fail-back"></a>Ativação e reativação pós-falha

Depois de configurar a replicação e executar uma análise para verificar se tudo está funcionando, você pode reprovar as máquinas no Azure, conforme necessário.

Antes de executar um failover, se você quiser se conectar ao computador no Azure após o failover, [Prepare-se para conectar-](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover) se antes de começar.

Em seguida, execute um failover da seguinte maneira:


1. Em **configurações** > **itens replicados**, clique na máquina > **failover**.
2. Selecione o ponto de recuperação que você deseja usar.
3. Em **failover de teste**, selecione a rede do Azure de destino.
4. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. Com essa configuração, Site Recovery tenta desligar o computador de origem antes de iniciar o failover. No entanto, o failover continua mesmo se o desligamento falhar. 
5. Clique em **OK** para iniciar a ativação pós-falha. Pode seguir o progresso da ativação pós-falha na página **Trabalhos**.
6. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Se você estiver preparado para se conectar após o failover, verifique se a VM é do tamanho apropriado, conectada à rede correta e em execução.
7. Depois de verificar a VM, clique em **confirmar** para concluir o failover. Isso exclui todos os pontos de recuperação disponíveis.

> [!WARNING]
> Não cancelar um failover em andamento: antes de o failover ser iniciado, a replicação da VM é interrompida. Se cancelar uma ativação pós-falha que esteja em curso, a mesma para, mas a VM não será replicada outra vez.


### <a name="fail-back-to-azure-stack"></a>Failback para Azure Stack

Quando o site primário estiver em execução novamente, você poderá fazer failback do Azure para Azure Stack. Para fazer isso, você precisa baixar o VHD de VM do Azure e carregá-lo no Azure Stack.

1. Desligue a VM do Azure para que o VHD possa ser baixado. 
2. Para iniciar o download do VHD, instale o [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).
3. Navegue até a VM no portal do Azure (usando o nome da VM).
4. Em **discos**, clique no nome do disco e colete as configurações.

    - Por exemplo, o URI do VHD usado em nosso teste: https://502055westcentralus.blob.core.windows.net/wahv9b8d2ceb284fb59287/copied-3676553984.vhd pode ser dividido para obter os seguintes parâmetros de entrada que são usados para baixar o VHD.
        - Conta de armazenamento: 502055westcentralus
        - Contêiner: wahv9b8d2ceb284fb59287
        - Nome do VHD: copied-3676553984. vhd

5. Agora, use Gerenciador de Armazenamento do Azure para baixar o VHD.
6. Carregue o VHD para Azure Stack com [estas etapas](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-disks#use-powershell-to-add-multiple-disks-to-a-vm).
7. Na VM ou nova VM existente, anexe os VHDs carregados.
8. Verifique se o disco do sistema operacional está correto e inicie a VM.


Neste estágio, o failback foi concluído.


## <a name="conclusion"></a>Conclusão

Neste artigo, replicamos Azure Stack VMs para o Azure. Com a replicação in-loco, executamos uma análise de recuperação de desastre para verificar se o failover do Azure funcionou conforme o esperado. O artigo também incluiu etapas para executar um failover completo no Azure e failback para Azure Stack.

## <a name="next-steps"></a>Passos seguintes

Após o failback, você pode proteger novamente a VM e começar a replicá-la para o Azure novamente para fazer isso, repita as etapas neste artigo.

