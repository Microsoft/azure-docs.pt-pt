---
title: Replicar VMs Azure Stack para Azure usando Azure Site Recovery / Microsoft Docs
description: Saiba como configurar a recuperação de desastres para Azure Stack VMs com o serviço de Recuperação do Local Azure.
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: a7e58f5b24786169c9d0c989b79a14c4115acca8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448971"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Replicar as VMs do Azure Stack para o Azure

Este artigo mostra-lhe como configurar a recuperação de desastres Azure Stack VMs para Azure, utilizando o serviço de recuperação do [local de Azure](site-recovery-overview.md).

A Recuperação do Site contribui para a estratégia de continuidade do seu negócio e recuperação de desastres (BCDR). O serviço garante que as suas cargas de trabalho em VM permanecem disponíveis quando ocorrem interrupções esperadas e inesperadas.

- A Recuperação do Site orquestra e gere a replicação de VMs para o armazenamento de Azure.
- Quando ocorre uma paragem no seu site primário, utilize a Recuperação do Site para falhar em Azure.
- No failover, os VMs Azure são criados a partir dos dados VM armazenados, e os utilizadores podem continuar a aceder às cargas de trabalho em execução nesses VMs Azure.
- Quando tudo estiver a funcionar novamente, podes falhar os VMs do Azure para o teu site primário e começar a replicar-te novamente no armazenamento do Azure.


Neste artigo, vai aprender a:

> [!div class="checklist"]
> * **Passo 1: Prepare vMs de pilha azul para replicação**. Verifique se os VMs cumprem os requisitos de Recuperação do Local e prepare-se para a instalação do serviço de Mobilidade de Recuperação do Local. Este serviço está instalado em cada VM que pretende replicar.
> * **Passo 2: Montar um cofre dos Serviços de Recuperação.** Configurar um cofre para recuperação do local e especificar o que pretende replicar. Os componentes e ações de recuperação do local são configurados e geridos no cofre.
> * **Passo 3: Configurar o ambiente de replicação da fonte**. Configurar um servidor de configuração de recuperação do site. O servidor de configuração é um único VM Azure Stack que executa todos os componentes necessários pela Recuperação do Site. Depois de configurar o servidor de configuração, registe-o no cofre.
> * **Passo 4: Configurar o ambiente de replicação do alvo**. Selecione a sua conta Azure e a conta de armazenamento Azure e rede que pretende utilizar. Durante a replicação, os dados VM são copiados para o armazenamento do Azure. Após o failover, os VMS Azure são unidos à rede especificada.
> * **Passo 5: Ativar a replicação**. Configurar as definições de replicação e ativar a replicação para VMs. O serviço de Mobilidade será instalado num VM quando a replicação estiver ativada. A Recuperação do Site executa uma replicação inicial do VM, e então começa a replicação em curso.
> * **Passo 6: Executar um exercício de recuperação de desastres**: Depois de a replicação estar a funcionar, verifique se a falha funcionará como esperado ao executar um berbequim. Para iniciar a broca, faça um teste de failover na Recuperação do Local. O teste de falha não afeta o seu ambiente de produção.

Com estes passos completos, você pode então executar um fracasso completo para Azure como e quando você precisa.

## <a name="architecture"></a>Arquitetura

![O diagrama mostra cofres dos Serviços de Recuperação para dois inquilinos em nuvens associadas a assinaturas de inquilinos, tanto numa infraestrutura comum de Azure Stack.](./media/azure-stack-site-recovery/architecture.png)

**Localização** | **Componente** |**Detalhes**
--- | --- | ---
**Servidor de configuração** | Funciona num único Azure Stack VM. | Em cada subscrição configura um VM do servidor de configuração. Este VM executa os seguintes componentes de Recuperação de Sítio:<br/><br/> - Servidor de configuração: Coordena as comunicações entre as instalações e o Azure e gere a replicação de dados. - Servidor de processo: Atua como um portal de replicação. Recebe dados de replicação, otimiza com caching, compressão e encriptação; e envia-o para o armazém da Azure.<br/><br/> Se os VMs pretenderem replicar exceder os limites indicados abaixo, pode configurar um servidor de processo autónomo separado. [Saiba mais](vmware-azure-set-up-process-server-scale.md).
**Serviço de Mobilidade** | Instalado em cada VM que pretende replicar. | Nos passos deste artigo, preparamos uma conta para que o serviço de Mobilidade seja instalado automaticamente num VM quando a replicação estiver ativada. Se não quiser instalar o serviço automaticamente, existem vários outros métodos que pode utilizar. [Saiba mais](vmware-azure-install-mobility-service.md).
**Azure** | Em Azure precisa de um cofre dos Serviços de Recuperação, uma conta de armazenamento e uma rede virtual. |  Os dados replicados são armazenados na conta de armazenamento. Os VMs Azure são adicionados à rede Azure quando ocorre uma falha.


A replicação funciona da seguinte forma:

1. No cofre, especifica a fonte de replicação e o alvo, configura o servidor de configuração, cria uma política de replicação e ativa a replicação.
2. O serviço mobility é instalado na máquina (se tiver utilizado a instalação push) e as máquinas começam a replicar-se de acordo com a política de replicação.
3. Uma cópia inicial dos dados do servidor é replicada para o armazenamento do Azure.
4. Após o final da replicação inicial, começa a replicação das alterações delta para Azure. As alterações registadas relativas a uma máquina são guardadas num ficheiro .hrl.
5. O servidor de configuração orquestra a gestão de replicação com o Azure (saída da porta HTTPS 443).
6. O servidor de processo recebe dados de máquinas de origem, otimiza-os e encripta-os e envia-os para o armazenamento Azure (porta 443 de saída).
7. As máquinas replicadas comunicam com o servidor de configuração (entrada HTTPS 443, para gestão de replicação. As máquinas enviam dados de replicação para o servidor de processo (porta HTTPS 9443 entrada - pode ser modificada).
8. O tráfego é replicado para pontos finais públicos do armazenamento do Azure, através da Internet. Em alternativa, pode utilizar o peering público do Azure ExpressRoute. A replicação de tráfego através de uma rede de VPNs a partir de um site no local para o Azure não é suportada.

## <a name="prerequisites"></a>Pré-requisitos

Eis o que precisas para preparar este cenário.

**Requisito** | **Detalhes**
--- | ---
**Conta de subscrição Azure** | Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
**Permissões de conta Azure** | A conta Azure que utiliza necessita de permissões para:<br/><br/> - Criar um cofre de serviço de recuperação<br/><br/> - Criar uma máquina virtual no grupo de recursos e rede virtual que utiliza para o cenário<br/><br/> - Escreva na conta de armazenamento que especifique<br/><br/> Tenha em atenção que:<br/><br/> -Se criar uma conta, é o administrador da sua subscrição e pode realizar todas as ações.<br/><br/> - Se utilizar uma subscrição existente e não for o administrador, tem de trabalhar com o administrador para lhe atribuir permissões de Proprietário ou Contribuinte.<br/><br/> - Se precisar de mais permissões granulares, [reveja este artigo.](site-recovery-role-based-linked-access-control.md)
**Azure Stack VM** | Você precisa de um VM Azure Stack na subscrição do inquilino, que será implementado como o servidor de configuração de recuperação do site.


### <a name="prerequisites-for-the-configuration-server"></a>Pré-requisitos para o servidor de configuração

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]



## <a name="step-1-prepare-azure-stack-vms"></a>Passo 1: Preparar VMs Azure Stack

### <a name="verify-the-operating-system"></a>Verifique o sistema operativo

Certifique-se de que os VMs estão a executar um dos sistemas operativos resumidos na tabela.


**Sistema operativo** | **Detalhes**
--- | ---
**Janelas de 64 bits** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (a partir de SP1)
**CentOS** | 5.2 a 5.11, 6.1 a 6.9, 7.0 a 7.3
**Ubuntu** | Servidor 14.04 LTS, servidor 16.04 LTS. Revisão [de núcleos suportados](vmware-physical-azure-support-matrix.md#ubuntu-kernel-versions)

### <a name="prepare-for-mobility-service-installation"></a>Preparar para instalação de serviço de mobilidade

Todos os VM que pretende replicar devem ter o serviço de Mobilidade instalado. Para que o servidor de processo instale o serviço automaticamente no VM quando a replicação estiver ativada, verifique as definições de VM.

#### <a name="windows-machines"></a>Máquinas de windows

- Necessita de conectividade de rede entre o VM no qual pretende ativar a replicação e a máquina que executa o servidor de processo (por padrão este é o VM do servidor de configuração).
- Precisa de uma conta com direitos de administração (domínio ou local) na máquina para a qual ativa a replicação.
    - Especifica esta conta quando configurar a Recuperação do Site. Em seguida, o servidor de processos utiliza esta conta para instalar o serviço Mobility quando a replicação está ativada.
    - Esta conta só será utilizada pela Recuperação do Site para a instalação push e para atualizar o serviço mobility.
    - Se não estiver a utilizar uma conta de domínio, tem de desativar o controlo de acesso ao utilizador remoto no VM:
        - No registo, crie o valor DWORD **LocalAccountTokenFilterPolicy** em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System.
        - Defina o valor para 1.
        - Para o fazer na indicação de comando, digite o seguinte: **REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1**.
- Na Firewall do Windows no VM pretende replicar, permitir a partilha de ficheiros e impressoras e o WMI.
    - Para isso, corra **wf.msc** para abrir a consola Windows Firewall. Clique à direita **Nas Regras de Entrada**Nova  >  **Regra**. Selecione **Predefined**, e escolha **a partilha de Ficheiros e Impressoras** da lista. Preencha o assistente, selecione para permitir a ligação > **Terminar**.
    - Para computadores de domínio, pode utilizar um GPO para o fazer.


#### <a name="linux-machines"></a>Máquinas Linux

- Certifique-se de que existe conectividade de rede entre o computador Linux e o servidor de processo.
- Na máquina para a qual ativa a replicação, precisa de uma conta que seja um utilizador raiz no servidor Linux de origem:
    - Especifica esta conta quando configurar a Recuperação do Site. Em seguida, o servidor de processos utiliza esta conta para instalar o serviço Mobility quando a replicação está ativada.
    - Esta conta só será utilizada pela Recuperação do Site para a instalação push e para atualizar o serviço mobility.
- Verifique se o ficheiro /etc/hosts no servidor Linux de origem tem entradas que mapeiam o nome de anfitrião local para endereços IP associados a todos os adaptadores de rede.
- Instale os pacotes openssh, openssh-server e openssl mais recentes no computador que quer replicar.
- Certifique-se de que o Secure Shell (SSH) está ativado e em execução na porta 22.
- Ative a autenticação de subsistema e palavra-passe SFTP no ficheiro sshd_config:
    1. Para fazer isto, inscreva-se como raiz.
    2. Encontre a linha que começa com **passwordAuthentication,** no ficheiro /etc/ssh/sshd_config. Remova os comentários da linha e altere o valor para **sim**.
    3. Encontre a linha que começa por **Subsystem** e remova os comentários da mesma.

        ![Serviço de Mobilidade Linux](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Reinicie o serviço sshd.


### <a name="note-the-vm-private-ip-address"></a>Note o endereço IP privado VM

Para cada máquina que pretende replicar, encontre o endereço IP:

1. No Portal Azure Stack, clique no VM.
2. No menu **Recursos,** clique em **Interfaces de Rede.**
3. Note o endereço IP privado.

    ![Endereço IP privado](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>Passo 2: Criar um cofre e selecionar um objetivo de replicação

1. No portal Azure, selecione **Criar uma**cópia de segurança das  >  **ferramentas de gestão**de recursos  >  **e recuperação do local.**
2. Em **Nome**, introduza um nome amigável para identificar o cofre.
3. No **grupo De recursos,** crie ou selecione um grupo de recursos. Estamos a usar **contosoRG.**
4. Em **Localização,** entre na região de Azure. estamos a utilizar **Europa Ocidental**.
5. Para aceder rapidamente ao cofre a partir do painel de instrumentos, selecione **Pin to dashboard**  >  **Create**.

   ![Criar um novo cofre](./media/azure-stack-site-recovery/new-vault-settings.png)

   O novo cofre aparece no **Dashboard**  >  **Todos os recursos**e na página principal dos **cofres dos Serviços de Recuperação.**

### <a name="select-a-replication-goal"></a>Selecionar um objetivo de replicação

1. Nos **cofres dos Serviços de Recuperação** > especificar um nome de cofre. Estamos a usar **o ContosoVMVault.**
2. Em **Introdução**, selecione Site Recovery. Em seguida, selecione **Preparar Infraestrutura**.
3. Em **Objetivo de Proteção**  >  **Onde estão as suas máquinas,** selecione **On-ins**.
4. Em **Para onde pretende replicar as máquinas**, selecione **Para o Azure**.
5. In **Are your machines virtualized**, select **Not virtualized/Other**. Em seguida, selecione **OK**.

    ![Objetivo de proteção](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>Passo 3: Configurar o ambiente de origem

Configurar a máquina do servidor de configuração, registá-la no cofre e descobrir as máquinas que pretende replicar.

1. Clique **em Preparar Fonte de Infraestrutura**  >  **Source**.
2. Em **Preparar origem**, clique em **+Servidor de configuração**.

    ![Screenshot do diálogo +Servidor de Configuração com a mensagem "Clique em +Servidor de Configuração na barra de comando acima para configurar um...".](./media/azure-stack-site-recovery/plus-config-srv.png)

3. In **Add Server**, verifique se o Servidor de **Configuração** aparece no **tipo servidor**.
5. Descarregue o ficheiro de instalação de configuração unificada de recuperação do site.
6. Transfira a chave de registo do cofre. Precisa da chave de registo quando executar a Configuração Unificada. A chave é válida durante cinco dias depois de gerá-la.

    ![Screenshot do diálogo do Servidor de Adicionar com o Tipo de Servidor definido para Servidor de Configuração e o botão de registo do cofre realçado.](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Executar Azure Site Recovery Unificado Configuração

Para instalar e registar o servidor de configuração, faça uma ligação RDP ao VM que pretende utilizar para o servidor de configuração e execute a Configuração Unificada.

Antes de começar, certifique-se de que o relógio está [sincronizado com um servidor de tempo](/windows-server/networking/windows-time-service/windows-time-service-top) no VM antes de começar. A instalação falha se a hora estiver a mais de cinco minutos de tempo local.

Agora instale o servidor de configuração:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> O servidor de configuração também pode ser instalado a partir da linha de comando. [Saiba mais](physical-manage-configuration-server.md#install-from-the-command-line).
>
> Pode demorar 15 minutos ou mais para o nome da conta aparecer no portal. Para atualizar imediatamente, selecione **Servidor de Configuração**  >  ***servidor nome***Refresh  >  **Server**.

## <a name="step-4-set-up-the-target-environment"></a>Passo 4: Configurar o ambiente-alvo

Selecione e verifique os recursos de destino.

1. In **Prepare a infraestrutura**  >  **Target**, selecione a subscrição Azure que pretende utilizar.
2. Especifique o modelo de implementação do alvo.
3. A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis. Se não os encontrar, terá de criar pelo menos uma conta de armazenamento e uma rede virtual, para completar o assistente.


## <a name="step-5-enable-replication"></a>Passo 5: Ativar a replicação

### <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Clique **em Preparar**  >  **Definições de Replicação de Infraestruturas**.
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limiar RPO**, especifique o limite do objetivo de ponto de recuperação (RPO).
    - Os pontos de recuperação para dados replicados são criados de acordo com o tempo definido.
    - Esta definição não afeta a replicação, que é contínua. Simplesmente emite um alerta se o limite de limiar for atingido sem que seja criado um ponto de recuperação.
4. Na **retenção do ponto de recuperação,** especifique quanto tempo cada ponto de recuperação é mantido. Os VMs replicados podem ser recuperados até qualquer ponto da janela de tempo especificada.
5. Na **frequência snapshot consistente da App,** especifique quantas imagens são criadas imagens consistentes com aplicações.

    - Um instantâneo consistente com aplicações é uma imagem pontual dos dados da aplicação dentro do VM.
    - O Volume Shadow Copy Service (VSS) garante que as aplicações no VM estão num estado consistente quando o instantâneo é tirado.
6. Selecione **OK** para criar a política.


### <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

Podes saltar este passo agora mesmo. Na lista de **desistências do Planeamento de Implantação,** clique em **Sim, já o fiz.**



### <a name="enable-replication"></a>Ativar a replicação

Certifique-se de que completou todas as tarefas no [Passo 1: Prepare a máquina](#step-1-prepare-azure-stack-vms). Em seguida, ative a replicação da seguinte forma:

1. Selecione **Aplicação Replicate**  >  **Fonte**.
2. Em **Origem**, selecione o servidor de configuração.
3. No **tipo máquina,** selecione **máquinas físicas**.
4. Selecione o servidor de processos (servidor de configuração). Em seguida, clique em **OK**.
5. No **Target,** selecione a subscrição e o grupo de recursos no qual pretende criar os VMs após o failover. Escolha o modelo de implantação que pretende utilizar para os VMs falhados.
6. Selecione a conta de armazenamento Azure na qual pretende armazenar os dados replicados.
7. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se ligam quando forem criadas após a ativação pós-falha.
8. Selecione **Configurar agora para as máquinas selecionadas** para aplicar a definição de rede para todas as máquinas selecionadas para proteção. Selecione **Configurar mais tarde** se pretender selecionar a rede Azure separadamente para cada máquina.
9. Nas **Máquinas Físicas,** clique **em +Máquina física.** Especifique o nome, endereço IP e tipo de SO de cada máquina que pretende replicar.

    - Utilize o endereço IP interno da máquina.
    - Se especificar o endereço IP público, a replicação pode não funcionar como esperado.

10. Nas propriedades **de Configuração de**  >  **Propriedades**, selecione a conta que o servidor de processo utilizará para instalar automaticamente o Serviço de Mobilidade na máquina.
11. Nas **definições de replicação,**  >  **certifique-se de que**a política de replicação correta está selecionada.
12. Clique **em Ativar a replicação.**
13. Acompanhe o progresso do trabalho de **Proteção de Ativação** em **Definições**  >  **de**  >  **Empregos Locais De Recuperação de Locais**. Depois de a tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.

> [!NOTE]
> O Site Recovery instala o Serviço de Mobilidade quando a replicação está ativada para uma VM.
>
> Pode demorar 15 minutos ou mais tempo para as alterações produzirem efeitos e aparecerem no portal.
>
> Para monitorizar os VMs que adicionar, verifique a última vez descoberta para VMs em **Servidores de Configuração**  >  **Último Contacto Em**. Para adicionar VMs sem aguardar a deteção agendada, realce o servidor de configuração (não o selecione) e selecione **Atualizar**.


## <a name="step-6-run-a-disaster-recovery-drill"></a>Passo 6: Executar um exercício de recuperação de desastres

Fazes um teste ao Azure para ter a certeza que está tudo a funcionar como esperado. Este fracasso não afetará o seu ambiente de produção.

### <a name="verify-machine-properties"></a>Verifique as propriedades da máquina

Antes de executar um teste de falha, verifique as propriedades da máquina e certifique-se de que cumprem os [requisitos do Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements). Pode ver e modificar propriedades da seguinte forma:

1. Em **Itens Protegidos**, clique em **Itens Replicados** > VM.
2. No painel **Item replicado**, existe um resumo das informações, do estado de funcionamento e dos pontos de recuperação mais recentes disponíveis da VM. Clique em **Propriedades** para ver mais detalhes.
3. No **Compute and Network,** modifique as definições conforme necessário.

    - Pode modificar o nome Azure VM, grupo de recursos, tamanho do alvo, [definição de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md)e definições de disco gerido.
    - Também pode visualizar e modificar as definições de rede. Estes incluem a rede/sub-rede à qual o VM Azure é acompanhado após o failover, e o endereço IP que será atribuído ao VM.
1. Em **Discos,** veja informações sobre o sistema operativo e os discos de dados no VM.


### <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Quando executa uma ativação pós-falha de teste, ocorre o seguinte:

1. É executada uma verificação dos pré-requisitos, para garantir que estão satisfeitas todas as condições necessárias para a ativação pós-falha.
2. A falha processa os dados utilizando o ponto de recuperação especificado:
    - **Mais recentemente processado**: A máquina falha no último ponto de recuperação processado pela Recuperação do Local. O carimbo de data/hora é apresentado. Com esta opção, não é despendido tempo a processar os dados, pelo que oferece um RTO (objetivo de tempo de recuperação) baixo.
    - **Aplicações mais recentes consistentes**: A máquina falha no mais recente ponto de recuperação consistente com aplicações.
    - **Costume**: Selecione o ponto de recuperação utilizado para o failover.

3. Um Azure VM é criado usando os dados processados.
4. A falha do teste pode limpar automaticamente os VMs Azure criados durante a broca.

Executar um teste de failover para um VM da seguinte forma:

1. Em **Definições**  >  **Itens Replicados,** clique no VM > **+Test Failover**.
2. Para esta passagem, vamos selecionar para usar o último ponto de recuperação **processado.**
3. Em **Test Failover**, selecione a rede Target Azure.
4. Clique em **OK** para iniciar a ativação pós-falha.
5. Acompanhe o progresso clicando no VM para abrir as suas propriedades. Ou, clique no teste **de failover** job em *nome de cofre*  >  **Definições Empregos**  >  **Jobs**  > **Empregos Trabalhos de Recuperação**do Local .
6. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Verifique se o VM tem o tamanho adequado, ligado à rede certa e em funcionamento.
7. Deverá conseguir ligar-se à VM replicada no Azure agora. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
8. Para eliminar as VMs do Azure criadas durante a ativação pós-falha de teste, clique em **Limpar ativação pós-falha de teste** na VM. Em **Notas**, guarde quaisquer observações associadas à falha do teste.

## <a name="fail-over-and-fail-back"></a>Ativação e reativação pós-falha

Depois de configurar a replicação e executar um berbequim para garantir que está tudo a funcionar, pode falhar as máquinas para o Azure, conforme necessário.

Antes de executar uma falha, se quiser ligar à máquina em Azure após a falha, [prepare-se para ligar](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) antes de iniciar.

Em seguida, executar um failover da seguinte forma:


1. Em **Definições**  >  **Itens Replicados**, clique na máquina > **Failover**.
2. Selecione o ponto de recuperação que pretende utilizar.
3. Em **Test Failover**, selecione a rede Target Azure.
4. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. Com esta definição, a Recuperação do Local tenta desligar a máquina de origem antes de iniciar a falha. No entanto, o fracasso continua mesmo que o encerramento falhe.
5. Clique em **OK** para iniciar a ativação pós-falha. Pode acompanhar o progresso falhado na página **Jobs.**
6. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Se se preparar para ligar após a falha, verifique se o VM é o tamanho adequado, ligado à rede certa e em funcionamento.
7. Depois de verificar o VM, clique **em Comprometer-se** a terminar a falha. Isto elimina todos os pontos de recuperação disponíveis.

> [!WARNING]
> Não cancelar uma ativação pós-falha em curso: antes do início da ativação pós-falha, a replicação da VM é parada. Se cancelar uma ativação pós-falha que esteja em curso, a mesma para, mas a VM não será replicada outra vez.


### <a name="fail-back-to-azure-stack"></a>Falha de volta à Pilha Azure

Quando o seu site principal estiver a funcionar novamente, pode falhar de volta de Azure a Azure Stack. Para isso, siga os passos listados [aqui.](https://docs.microsoft.com/azure-stack/operator/site-recovery-failback?view=azs-2005)

## <a name="conclusion"></a>Conclusão

Neste artigo replicamos VMs Azure Stack para Azure. Com a replicação no lugar, fizemos um exercício de recuperação de desastres para garantir que o fracasso para Azure funcionou como esperado. O artigo também incluiu passos para executar um fracasso total para Azure, e falhando de volta a Azure Stack.

## <a name="next-steps"></a>Passos seguintes

Depois de ter falhado, pode reprotegir o VM e começar a replicá-lo ao Azure novamente Para o fazer, repita os passos deste artigo.

