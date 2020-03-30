---
title: Replicar VMs azure stack para Azure usando a recuperação do site Azure Microsoft Docs
description: Saiba como configurar a recuperação de desastres para Azure para VMs Azure Stack com o serviço de recuperação de sites Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 08/05/2019
ms.author: raynew
ms.openlocfilehash: 15cd729063545914f791de39a075af9084f72bef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426569"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Replicar as VMs do Azure Stack para o Azure

Este artigo mostra-lhe como configurar os VMs azure stack de recuperação de desastres para o Azure, utilizando o serviço de recuperação de [sítios Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview).

A Recuperação do Site contribui para a continuidade do seu negócio e estratégia de recuperação de desastres (BCDR). O serviço garante que as suas cargas de trabalho VM permanecem disponíveis quando ocorrem interrupções inesperadas.

- A Recuperação do Site orquestra e gere a replicação de VMs para o armazenamento Azure.
- Quando ocorre uma paragem no seu site principal, utiliza a Recuperação do Site para falhar com o Azure.
- No failover, os VMs Azure são criados a partir dos dados VM armazenados, e os utilizadores podem continuar a aceder a cargas de trabalho em execução nesses VMs Azure.
- Quando tudo estiver a funcionar novamente, podes voltar a ligar os VMs Azure para o teu local principal, e começar a replicar-te para o armazenamento do Azure novamente.


Neste artigo, vai aprender a:

> [!div class="checklist"]
> * **Passo 1: Prepare Os VMs da pilha Azure para replicação**. Verifique se os VMs cumprem os requisitos de Recuperação do Site e prepare-se para a instalação do serviço de Mobilidade de Recuperação do Local. Este serviço está instalado em cada VM que pretende replicar.
> * **Passo 2: Instale um cofre**de Serviços de Recuperação . Instale um cofre para a Recuperação do Local e especifique o que pretende replicar. Os componentes e ações de recuperação do site são configurados e geridos no cofre.
> * **Passo 3: Configurar o ambiente**de replicação de origem . Configurar um servidor de configuração de recuperação do site. O servidor de configuração é um único VM De Pilha Azure que executa todos os componentes necessários pela Recuperação do Site. Depois de configurar o servidor de configuração, registe-o no cofre.
> * **Passo 4: Configurar o ambiente**de replicação do alvo . Selecione a sua conta Azure e a conta de armazenamento Azure e rede que pretende utilizar. Durante a replicação, os dados vm são copiados para o armazenamento Azure. Após a falha, os VMs Azure juntam-se à rede especificada.
> * **Passo 5: Ativar a replicação**. Configure as definições de replicação e ative a replicação para VMs. O serviço de Mobilidade será instalado num VM quando a replicação estiver ativada. A Recuperação do Local executa uma replicação inicial do VM, e então começa a replicação contínua.
> * **Passo 6: Executar um exercício**de recuperação de desastres : Depois de a replicação estar a funcionar, verifica-se que a falha funcionará como esperado através de um exercício. Para iniciar a broca, executa uma falha de teste na Recuperação do Local. O fracasso do teste não afeta o seu ambiente de produção.

Com estes passos completos, pode então executar uma falha completa para Azure quando e quando precisar.

## <a name="architecture"></a>Arquitetura

![Arquitetura](./media/azure-stack-site-recovery/architecture.png)

**Localização** | **Componente** |**Detalhes**
--- | --- | ---
**Servidor de configuração** | Corre num único Azure Stack VM. | Em cada subscrição, configura um VM do servidor de configuração. Este VM executa os seguintes componentes de recuperação do site:<br/><br/> - Servidor de configuração: Coordena as comunicações entre as instalações e o Azure e gere a replicação de dados. - Servidor de processos: Atua como um portal de replicação. Recebe dados de replicação, otimiza com cache, compressão e encriptação; e envia-o para o armazém do Azure.<br/><br/> Se os VMs que pretende replicar excederem os limites indicados abaixo, pode configurar um servidor de processo autónomo separado. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-process-server-scale).
**Serviço de mobilidade** | Instalado em cada VM que pretende replicar. | Nos passos deste artigo, preparamos uma conta para que o serviço de Mobilidade seja instalado automaticamente num VM quando a replicação estiver ativada. Se não quiser instalar o serviço automaticamente, existem vários outros métodos que pode utilizar. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/vmware-azure-install-mobility-service).
**Azure** | Em Azure você precisa de um cofre de Serviços de Recuperação, uma conta de armazenamento, e uma rede virtual. |  Os dados replicados são armazenados na conta de armazenamento. Os VMs azure são adicionados à rede Azure quando ocorre a falha. 


A replicação funciona da seguinte forma:

1. No cofre, especifica a fonte de replicação e o alvo, configura o servidor de configuração, cria uma política de replicação e ativa a replicação.
2. O serviço de Mobilidade está instalado na máquina (se tiver usado a instalação push) e as máquinas iniciam a replicação de acordo com a política de replicação.
3. Uma cópia inicial dos dados do servidor é replicada para o armazenamento do Azure.
4. Após os acabamentos iniciais da replicação, começa a replicação das alterações delta para Azure. As alterações registadas relativas a uma máquina são guardadas num ficheiro .hrl.
5. O servidor de configuração orquestra a gestão da replicação com o Azure (saída da porta HTTPS 443).
6. O servidor de processos recebe dados de máquinas de origem, otimiza e encripta-os, e envia-os para o armazenamento Azure (saída da porta 443).
7. As máquinas replicadas comunicam com o servidor de configuração (porta HTTPS 443 de entrada, para gestão de replicação. As máquinas enviam dados de replicação para o servidor de processo (a entrada da porta HTTPS 9443 - pode ser modificada).
8. O tráfego é replicado para pontos finais públicos do armazenamento do Azure, através da Internet. Em alternativa, pode utilizar o peering público do Azure ExpressRoute. A replicação de tráfego através de uma rede de VPNs a partir de um site no local para o Azure não é suportada.

## <a name="prerequisites"></a>Pré-requisitos

Eis o que precisas para preparar este cenário.

**Requisito** | **Detalhes**
--- | ---
**Conta de subscrição do Azure** | Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://azure.microsoft.com/pricing/free-trial/)
**Permissões de conta Azure** | A conta Azure que utiliza necessita de permissões para:<br/><br/> - Criar um cofre de serviço de recuperação<br/><br/> - Criar uma máquina virtual no grupo de recursos e rede virtual que utiliza para o cenário<br/><br/> - Escreva na conta de armazenamento que especifica<br/><br/> Tenha em atenção que:<br/><br/> -Se criar uma conta, é o administrador da sua subscrição e pode executar todas as ações.<br/><br/> - Se utilizar uma subscrição existente e não for o administrador, terá de trabalhar com o administrador para lhe atribuir permissões de Proprietário ou Contribuinte.<br/><br/> - Se precisar de mais permissões granulares, reveja [este artigo.](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control) 
**VM de pilha de azure** | Você precisa de um VM Azure Stack na subscrição do inquilino, que será implementado como o servidor de configuração de recuperação do site. 


### <a name="prerequisites-for-the-configuration-server"></a>Pré-requisitos para o servidor de configuração

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]


 
## <a name="step-1-prepare-azure-stack-vms"></a>Passo 1: Prepare VMs de stack azure

### <a name="verify-the-operating-system"></a>Verifique o sistema operativo

Certifique-se de que os VMs estão a executar um dos sistemas operativos resumidos na tabela.


**Sistema Operativo** | **Detalhes**
--- | ---
**Janelas de 64 bits** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (a partir de SP1)
**CentOS** | 5,2 a 5,11, 6,1 a 6,9, 7,0 a 7,3
**Ubuntu** | 14.04 Servidor LTS, servidor 16.04 LTS. Reveja [os núcleos apoiados](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#ubuntu-kernel-versions)

### <a name="prepare-for-mobility-service-installation"></a>Preparar para instalação de serviço de mobilidade

Cada VM que pretende replicar deve ter o serviço de Mobilidade instalado. Para que o servidor de processos instale o serviço automaticamente no VM quando a replicação estiver ativada, verifique as definições vM.

#### <a name="windows-machines"></a>Máquinas windows

- Precisa de conectividade de rede entre o VM no qual pretende ativar a replicação, e a máquina que executa o servidor de processo (por padrão, este é o VM do servidor de configuração).
- Precisa de uma conta com direitos de administração (domínio ou local) na máquina para a qual ativa a replicação.
    - Especifica esta conta quando configura restabelece a Recuperação do Site. Em seguida, o servidor de processo utiliza esta conta para instalar o serviço de Mobilidade quando a replicação está ativada.
    - Esta conta só será utilizada pela Recovery do Site para a instalação push e para atualizar o serviço de Mobilidade.
    - Se não estiver a utilizar uma conta de domínio, tem de desativar o controlo de acesso remoto ao utilizador no VM:
        - No registo, crie o valor DWORD **LocalAccountTokenFilterPolicy** em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System.
        - Defina o valor para 1.
        - Para isso no pedido de comando, escreva o seguinte: **REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System/v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1**.
- Na Firewall do Windows no VM pretende replicar, permitir partilha de ficheiros e impressoras e WMI.
    - Para isso, faça o **wf.msc** para abrir a consola Windows Firewall. Clique certo Para as **regras** > de entrada**nova regra**. Selecione **Predefinido**, e escolha partilha de **Ficheiros e Impressora** seletivada da lista. Complete o assistente, selecione para permitir a ligação > **Terminar**.
    - Para computadores de domínio, pode usar um GPO para fazer isto.

    
#### <a name="linux-machines"></a>Máquinas Linux

- Certifique-se de que existe conectividade de rede entre o computador Linux e o servidor de processos.
- Na máquina para a qual ativa a replicação, precisa de uma conta que seja um utilizador raiz no servidor Linux fonte:
    - Especifica esta conta quando configura restabelece a Recuperação do Site. Em seguida, o servidor de processo utiliza esta conta para instalar o serviço de Mobilidade quando a replicação está ativada.
    - Esta conta só será utilizada pela Recovery do Site para a instalação push e para atualizar o serviço de Mobilidade.
- Verifique se o ficheiro /etc/hosts no servidor Linux de origem tem entradas que mapeiam o nome de anfitrião local para endereços IP associados a todos os adaptadores de rede.
- Instale os pacotes openssh, openssh-server e openssl mais recentes no computador que quer replicar.
- Certifique-se de que o Secure Shell (SSH) está ativado e em execução na porta 22.
- Ative a autenticação de subsistema e palavra-passe SFTP no ficheiro sshd_config:
    1. Para fazer isto, inscreva-se como raiz.
    2. Encontre a linha que começa com **A Autenticação password,** no ficheiro /etc/ssh/sshd_config. Remova os comentários da linha e altere o valor para **sim**.
    3. Encontre a linha que começa por **Subsystem** e remova os comentários da mesma.

        ![Serviço de Mobilidade Linux](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Reinicie o serviço sshd.


### <a name="note-the-vm-private-ip-address"></a>Note o endereço IP privado VM

Para cada máquina que pretende replicar, encontre o endereço IP:

1. No Portal Da Pilha Azure, clique no VM.
2. No menu **Recurso,** clique em **Interfaces de Rede**.
3. Anote o endereço IP privado.

    ![Endereço IP privado](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>Passo 2: Criar um cofre e selecionar um objetivo de replicação

1. No portal Azure, selecione Criar uma**Cópia de Segurança e Recuperação de Ferramentas** > de**Gestão**de **Recursos.** > 
2. Em **Nome**, introduza um nome amigável para identificar o cofre. 
3. No **grupo Recursos,** crie ou selecione um grupo de recursos. Estamos a usar **contosoRG.**
4. No **Local,** entre na região de Azure. estamos a utilizar **Europa Ocidental**.
5. Para aceder rapidamente ao cofre a partir do tablier, selecione **Pin para painel** > **criar**.

   ![Criar um novo cofre](./media/azure-stack-site-recovery/new-vault-settings.png)

   O novo cofre aparece no **Dashboard** > **Todos os recursos,** e na página principal dos Serviços de **Recuperação.**

### <a name="select-a-replication-goal"></a>Selecionar um objetivo de replicação

1. Nos **cofres dos Serviços** de Recuperação > especificar um nome do cofre. Estamos a usar **o ContosoVMVault.**
2. Em **Introdução**, selecione Site Recovery. Em seguida, selecione **Preparar Infraestrutura**.
3. No **objetivo** > de Proteção Onde**estão as suas máquinas localizadas,** selecione **On-instalações**.
4. Em **Para onde pretende replicar as máquinas**, selecione **Para o Azure**.
5. In **Are your machines virtualizados**, selecione **Não virtualizado/Outros**. Em seguida, selecione **OK**.

    ![Objetivo de proteção](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>Passo 3: Configurar o ambiente de origem

Instale a máquina do servidor de configuração, registe-a no cofre e descubra as máquinas que pretende replicar.

1. Clique **em preparar** > **fonte de**infraestrutura .
2. Em **Preparar origem**, clique em **+Servidor de configuração**.

    ![Configurar a origem](./media/azure-stack-site-recovery/plus-config-srv.png)

3. No **Servidor adicionar,** verifique se o Servidor de **Configuração** aparece no **tipo servidor**.
5. Descarregue o ficheiro de instalação unificada de recuperação do site.
6. Transfira a chave de registo do cofre. Precisa da chave de registo quando executar a Configuração Unificada. A chave é válida durante cinco dias depois de gerá-la.

    ![Configurar a origem](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Run Azure Site Recovery Unified Setup

Para instalar e registar o servidor de configuração, faça uma ligação RDP ao VM que pretende utilizar para o servidor de configuração e execute a Configuração Unificada.

Antes de começar, certifique-se de que o relógio está [sincronizado com um servidor](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) de tempo no VM antes de começar. A instalação falha se o tempo for superior a cinco minutos da hora local.

Agora instale o servidor de configuração:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> O servidor de configuração também pode ser instalado a partir da linha de comando. [Saiba mais](physical-manage-configuration-server.md#install-from-the-command-line).
> 
> Pode demorar 15 minutos ou mais para o nome da conta aparecer no portal. Para atualizar imediatamente, selecione 'Servidor **de Configuração'** > ***server name*** > nome Refresh**Server**.

## <a name="step-4-set-up-the-target-environment"></a>Passo 4: Configurar o ambiente-alvo

Selecione e verifique os recursos de destino.

1. Em **Prepare-se Target** > **de**infraestrutura, selecione a subscrição Azure que pretende utilizar.
2. Especifique o modelo de implementação do alvo.
3. A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis. Se não os encontrar, terá de criar pelo menos uma conta de armazenamento e rede virtual, para completar o assistente.


## <a name="step-5-enable-replication"></a>Passo 5: Ativar a replicação

### <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Clique em preparar definições de**replicação de** **infraestrutura** > .
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limiar RPO**, especifique o limite do objetivo de ponto de recuperação (RPO).
    - Os pontos de recuperação dos dados replicados são criados de acordo com o tempo definido.
    - Esta definição não afeta a replicação, que é contínua. Simplesmente emite um alerta se o limiar for atingido sem a criação de um ponto de recuperação.
4. Na retenção do ponto de **recuperação,** especifique quanto tempo cada ponto de recuperação é mantido. Os VMs replicados podem ser recuperados em qualquer ponto da janela de tempo especificada.
5. Na **frequência instantânea consistente**com aplicativos, especifique com que frequência são criados instantâneos consistentes com aplicações.

    - Um instantâneo consistente com aplicações é uma imagem pontual dos dados da aplicação dentro do VM.
    - O Serviço de Cópia sinuosa de Volume Sombra (VSS) garante que as aplicações no VM estão num estado consistente quando o instantâneo é tirado.
6. Selecione **OK** para criar a política.


### <a name="confirm-deployment-planning"></a>Confirmar o planeamento da implementação

Pode saltar este passo agora mesmo. Na lista de abandono do Planeamento de **Implementação,** clique **sim, já o fiz.**



### <a name="enable-replication"></a>Ativar a replicação

Certifique-se de que completou todas as tarefas no [Passo 1: Preparar](#step-1-prepare-azure-stack-vms)a máquina . Em seguida, ativar a replicação da seguinte forma:

1. Selecione **Replicate aplicação** > **Origem**.
2. Em **Origem**, selecione o servidor de configuração.
3. No **tipo máquina,** selecione **Máquinas Físicas**.
4. Selecione o servidor de processos (servidor de configuração). Em seguida, clique em **OK**.
5. No **Target,** selecione a subscrição e o grupo de recursos no qual pretende criar os VMs após a falha. Escolha o modelo de implementação que pretende utilizar para os VMs falhados.
6. Selecione a conta de armazenamento Azure na qual pretende armazenar os dados replicados.
7. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se ligam quando forem criadas após a ativação pós-falha.
8. Selecione **Configurar agora para as máquinas selecionadas** para aplicar a definição de rede para todas as máquinas selecionadas para proteção. **Selecione Configurar mais tarde** se pretender selecionar a rede Azure separadamente para cada máquina.
9. Em **Máquinas Físicas,** clique em **+Máquina Física**. Especifique o nome, endereço IP e tipo DE OS de cada máquina que pretende replicar.

    - Utilize o endereço IP interno da máquina.
    - Se especificar o endereço IP público, a replicação pode não funcionar como esperado.

10. Em **propriedades** > **Configurar propriedades,** selecione a conta que o servidor de processo utilizará para instalar automaticamente o Serviço de Mobilidade na máquina.
11. Nas **definições** > de replicação**Configurar as definições de replicação,** verifique se a política de replicação correta foi selecionada.
12. Clique na **replicação de ativação**.
13. Acompanhe o progresso do trabalho de **proteção ativa** em **cenários** > **empregos** > de recuperação de**locais**. Depois de a tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.

> [!NOTE]
> O Site Recovery instala o Serviço de Mobilidade quando a replicação está ativada para uma VM.
> 
> Pode demorar 15 minutos ou mais tempo para as alterações produzirem efeitos e aparecerem no portal.
> 
> Para monitorizar os VMs que adiciona, verifique a última hora descoberta de VMs nos **Servidores** > de Configuração**Último Contacto Em**. Para adicionar VMs sem aguardar a deteção agendada, realce o servidor de configuração (não o selecione) e selecione **Atualizar**.


## <a name="step-6-run-a-disaster-recovery-drill"></a>Passo 6: Executar um exercício de recuperação de desastres

Fazes um teste falhado para o Azure para ter a certeza que está tudo a funcionar como esperado. Esta falha não afetará o seu ambiente de produção.

### <a name="verify-machine-properties"></a>Verificar as propriedades da máquina

Antes de executar uma falha de teste, verifique as propriedades da máquina e certifique-se de que cumprem os [requisitos do Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements). Pode ver e modificar propriedades da seguinte forma:

1. Em **Itens Protegidos**, clique em **Itens Replicados** > VM.
2. No painel **Item replicado**, existe um resumo das informações, do estado de funcionamento e dos pontos de recuperação mais recentes disponíveis da VM. Clique em **Propriedades** para ver mais detalhes.
3. Em **Compute e Network,** modifique as definições conforme necessário.

    - Pode modificar o nome, grupo de recursos, tamanho do alvo, conjunto de [disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md)e definições de disco geridas.
    - Também pode visualizar e modificar as definições de rede. Estes incluem a rede/sub-rede a que o VM Azure é aderido após a falha, e o endereço IP que será atribuído ao VM.
1. Em **Discos,** veja informações sobre o sistema operativo e os discos de dados no VM.
   

### <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Quando executa uma ativação pós-falha de teste, ocorre o seguinte:

1. É executada uma verificação dos pré-requisitos, para garantir que estão satisfeitas todas as condições necessárias para a ativação pós-falha.
2. Failover processa os dados utilizando o ponto de recuperação especificado:
    - **Mais recente processado**: A máquina falha no último ponto de recuperação processado pela Recuperação do Site. O carimbo de data/hora é apresentado. Com esta opção, não é despendido tempo a processar os dados, pelo que oferece um RTO (objetivo de tempo de recuperação) baixo.
    - **Mais recente aplicação consistente**: A máquina falha no mais recente ponto de recuperação consistente com aplicações.
    - **Personalizado**: Selecione o ponto de recuperação utilizado para a falha.

3. Um VM Azure é criado usando os dados processados.
4. A falha no teste pode limpar automaticamente os VMs Azure criados durante a broca.

Executar uma falha de teste para um VM da seguinte forma:

1. Em **Definições** > **Itens Replicados,** clique no VM > **+Falha de teste**.
2. Para esta passagem, vamos selecionar para usar o último ponto de recuperação **processado.** 
3. No **Teste Failover,** selecione a rede Target Azure.
4. Clique em **OK** para iniciar a ativação pós-falha.
5. Acompanhe o progresso clicando no VM para abrir as suas propriedades. Ou, clique no trabalho **de Failover** de Teste em *nome* > de cofre**Definições** > **Empregos** >Trabalhos de**Recuperação do Site**.
6. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Verifique se o VM tem o tamanho adequado, ligado à rede certa e em funcionamento.
7. Deverá conseguir ligar-se à VM replicada no Azure agora. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover).
8. Para eliminar as VMs do Azure criadas durante a ativação pós-falha de teste, clique em **Limpar ativação pós-falha de teste** na VM. Em **Notas,** guarde quaisquer observações associadas à falha do teste.

## <a name="fail-over-and-fail-back"></a>Ativação e reativação pós-falha

Depois de configurar a replicação e fazer um exercício para ter certeza de que está tudo a funcionar, pode falhar as máquinas até ao Azure, conforme necessário.

Antes de executar uma falha, se quiser ligar-se à máquina em Azure após a falha, [prepare-se para se ligar](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover) antes de começar.

Em seguida, executar uma falha da seguinte forma:


1. Em **Definições** > **Itens Replicados,** clique na máquina > **Failover**.
2. Selecione o ponto de recuperação que pretende utilizar.
3. No **Teste Failover,** selecione a rede Target Azure.
4. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. Com esta definição, a Recuperação do Local tenta desligar a máquina de origem antes de iniciar a falha. No entanto, a falha continua mesmo que o encerramento falhe. 
5. Clique em **OK** para iniciar a ativação pós-falha. Pode acompanhar o progresso da falha na página **Jobs.**
6. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Se se preparar para ligar após a falha, verifique se o VM tem o tamanho adequado, ligado à rede certa e em funcionamento.
7. Depois de verificar o VM, clique **em comprometer-se** a terminar a falha. Isto elimina todos os pontos de recuperação disponíveis.

> [!WARNING]
> Não cancelar uma ativação pós-falha em curso: antes do início da ativação pós-falha, a replicação da VM é parada. Se cancelar uma ativação pós-falha que esteja em curso, a mesma para, mas a VM não será replicada outra vez.


### <a name="fail-back-to-azure-stack"></a>Falhar de volta ao Azure Stack

Quando o seu site principal estiver a funcionar novamente, pode voltar de Azure a Azure Stack. Para isso, você precisa baixar o VHD Azure VM e carregá-lo para Azure Stack.

1. Desligue o VM Azure, para que o VHD possa ser descarregado. 
2. Para começar a descarregar o VHD, instale o [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
3. Navegue para o VM no Portal Azure (utilizando o nome VM).
4. Em **Discos,** clique no nome do disco e recolha as definições.

    - Como exemplo, o VHD URI utilizado https://502055westcentralus.blob.core.windows.net/wahv9b8d2ceb284fb59287/copied-3676553984.vhd no nosso teste: pode ser desfeito para obter os seguintes parâmetros de entrada que são usados para descarregar o VHD.
        - Conta de Armazenamento: 502055westcentralus
        - Recipiente: wahv9b8d2ceb284fb59287
        - Nome VHD: copiado-3676553984.vhd

5. Agora, use o Azure Storage Explorer para descarregar o VHD.
6. Faça upload do VHD para Azure Stack com [estes passos](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-disks#use-powershell-to-add-multiple-disks-to-a-vm).
7. No VM existente ou no novo VM, prenda os VHDs carregados.
8. Verifique se o Disco OS está correto e inicie o VM.


Nesta fase, o failback está completo.


## <a name="conclusion"></a>Conclusão

Neste artigo replicamos Os VMs Azure Stack para o Azure. Com a replicação no lugar, fizemos um exercício de recuperação de desastres para garantir que o fracasso do Azure funcionasse como esperado. O artigo também incluía passos para executar uma falha completa para Azure, e não voltar a Azure Stack.

## <a name="next-steps"></a>Passos seguintes

Depois de ter falhado, pode reproteger o VM e começar a replicá-lo ao Azure novamente Para o fazer, repita os passos deste artigo.

