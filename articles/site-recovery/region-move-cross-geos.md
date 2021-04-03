---
title: Mover VMs Azure entre governo e regiões públicas com recuperação do local de Azure
description: Use a recuperação do local de Azure para mover VMs Azure entre o governo de Azure e as regiões públicas.
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: a76ebf95b92b6e1251a04daa9ffb48a9abe15b50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89425352"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Mover VMs do Azure entre regiões públicas e o Azure Government 

Talvez queira mover os seus VMs iaas entre o Governo de Azure e as regiões Públicas para aumentar a disponibilidade dos seus VM existentes, melhorar a gestão, ou por razões de governação, conforme detalhado [aqui.](azure-to-azure-move-overview.md)

Além de utilizar o serviço [de Recuperação do Local Azure](site-recovery-overview.md) para gerir e orquestrar a recuperação de desastres de máquinas no local e VMs Azure para efeitos de continuidade de negócios e recuperação de desastres (BCDR), também pode utilizar a Recuperação de Sítio para gerir a deslocação de VMs Azure para uma região secundária.       

Este tutorial mostra-lhe como mover VMs Azure entre o Governo de Azure e as regiões públicas usando a Recuperação do Sítio Azure. O mesmo pode ser alargado para mover VMs entre pares de regiões que não estão dentro do mesmo aglomerado geográfico. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verificar os pré-requisitos
> * Preparar os VMs de origem
> * Preparar a região alvo
> * Copiar dados para a região-alvo
> * Teste a configuração.
> * Realizar o movimento
> * Descartar os recursos na região origem

> [!IMPORTANT]
> Este tutorial mostra-lhe como mover VMs Azure entre o Governo de Azure e as regiões Públicas, ou entre regiões que não são apoiadas pela solução regular de recuperação de desastres para os VMs do Azure. No caso de os pares de regiões de origem e alvo serem [apoiados,](./azure-to-azure-support-matrix.md#region-support)consulte este [documento](azure-to-azure-tutorial-migrate.md) para a mudança. Se o seu requisito é melhorar a disponibilidade movendo VMs em uma disponibilidade definida para VMs de zona fixada em uma região diferente, consulte o tutorial [aqui](move-azure-VMs-AVset-Azone.md).

> [!IMPORTANT]
> Não é aconselhável utilizar este método para configurar o DR entre pares de regiões não suportados, uma vez que os pares são definidos mantendo a latência dos dados em mente, o que é fundamental para um cenário DR.

## <a name="verify-prerequisites"></a>Verificar os pré-requisitos

> [!NOTE]
> Certifique-se de que compreende a [arquitetura e os componentes](physical-azure-architecture.md) para este cenário. Esta arquitetura será usada para mover VMs Azure, **tratando os VMs como servidores físicos.**

- Reveja os [requisitos de suporte](vmware-physical-secondary-support-matrix.md) para todos os componentes.
- Certifique-se de que os servidores que pretende replicar cumprem os [requisitos do Azure VM](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Prepare uma conta para a instalação automática do serviço Mobility em cada servidor que pretende replicar.

- Note que depois de falhar na região alvo em Azure, não pode executar diretamente uma falha na região de origem. Terá que configurar a replicação de novo para o alvo.

### <a name="verify-azure-account-permissions"></a>Verifique permissões da conta Azure

Certifique-se de que a sua conta Azure tem permissões para a replicação de VMs para Azure.

- Reveja as [permissões necessárias](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para replicar máquinas para Azure.
- Verifique e modifique permissões [de controlo de acesso baseado em funções (Azure RBAC).](../role-based-access-control/role-assignments-portal.md) 

### <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Configurar uma [rede Azure alvo.](../virtual-network/quick-create-portal.md)

- Os VMs Azure são colocados nesta rede quando são criados após o failover.
- A rede deve estar na mesma região que o cofre dos Serviços de Recuperação.


### <a name="set-up-an-azure-storage-account"></a>Configurar uma conta de armazenamento do Azure

Crie uma [conta de armazenamento Azure.](../storage/common/storage-account-create.md)

- A Recuperação do Local replica máquinas no local para o armazenamento da Azure. Os VMs Azure são criados a partir do armazenamento após o fracasso.
- A conta de armazenamento tem de estar na mesma região que o cofre dos Serviços de Recuperação.


## <a name="prepare-the-source-vms"></a>Preparar os VMs de origem

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço Mobility tem de ser instalado em cada servidor que pretende replicar. A Recuperação do Site instala este serviço automaticamente quando ativa a replicação do servidor. Para instalar automaticamente, precisa de preparar uma conta que a Recuperação do Site utilizará para aceder ao servidor.

- Pode utilizar um domínio ou conta local
- Para os VMs do Windows, se não estiver a utilizar uma conta de domínio, desative o controlo de acesso ao utilizador remoto na máquina local. Para isso, no registo em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy,** com um valor de 1.
- Para adicionar a entrada de registo para desativar a definição de um CLI, escreva:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Para o Linux, a conta deve estar na raiz do servidor Linux de origem.


## <a name="prepare-the-target-region"></a>Preparar a região alvo

1. Certifique-se de que a sua subscrição do Azure permite criar VMs na região de destino utilizada para recuperação após desastre. Contacte o suporte para ativar a quota necessária.

2. Certifique-se de que a sua subscrição tem recursos suficientes para suportar as VMs com tamanhos que correspondem às VMs de origem. se estiver a utilizar a Recuperação do Site para copiar dados para o alvo, ele escolhe o mesmo tamanho ou o tamanho mais próximo possível para o VM alvo.

3. Certifique-se de que cria um recurso-alvo para todos os componentes identificados no layout de rede de origem. Isto é importante para garantir que, após o corte na região alvo, os seus VMs têm todas as funcionalidades e funcionalidades que tinha na fonte.

    > [!NOTE]
    > A Azure Site Recovery descobre e cria automaticamente uma rede virtual quando ativa a replicação para o VM de origem, ou também pode pré-criar uma rede e atribuir ao VM no fluxo do utilizador para permitir a replicação. Mas para quaisquer outros recursos, como mencionado abaixo, é necessário criá-los manualmente na região alvo.

     Consulte os seguintes documentos para criar os recursos de rede mais utilizados relevantes para si, com base na configuração VM de origem.

    - [Grupos de Segurança de Rede](../virtual-network/manage-network-security-group.md)
    - [Balanceadores de carga](../load-balancer/index.yml)
    - [IP público](../virtual-network/virtual-network-public-ip-address.md)
    
    Para quaisquer outros componentes de rede, consulte a [documentação](../index.yml?pivot=products&panel=network)de networking .

4. Crie manualmente [uma rede de não produção](../virtual-network/quick-create-portal.md) na região alvo se desejar testar a configuração antes de efetuar o corte final para a região alvo. Isto irá criar uma interferência mínima com a produção e é recomendado.

## <a name="copy-data-to-the-target-region"></a>Copiar dados para a região-alvo
Os passos abaixo irão guiá-lo como usar a Recuperação do Site Azure para copiar dados para a região alvo.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Crie o cofre em qualquer região, exceto na região de origem.

1. Inscreva-se nos Serviços de Recuperação [do portal Azure.](https://portal.azure.com)  >  
2. Clique **em Criar uma** cópia de segurança das  >  **ferramentas de gestão** de recursos  >  **e recuperação do site.**
3. No **Nome**, especifique o nome amigável **ContosoVMVault**. Se tiver mais do que um a. subscrição, selecione o apropriado.
4. Crie um grupo de recursos **ContosoRG**.
5. Selecione uma região do Azure. Para verificar as regiões suportadas, veja a disponibilidade geográfica em [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Nos cofres dos Serviços de Recuperação, clique em **Overview**  >  **ConsotoVMVault**  >  **+Replicate**
7. **Selecione Para Azure**  >  **Não virtualizado/Outros**.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Configurar o servidor de configuração para descobrir VMs.


Configurar o servidor de configuração, registá-lo no cofre e descobrir VMs.

1. Clique **na recuperação do local** Preparar Fonte  >  **de Infraestrutura**  >  .
2. Se não tiver um servidor de configuração, clique em **+Servidor de Configuração**.
3. In **Add Server**, verifique se o Servidor de **Configuração** aparece no **tipo servidor**.
4. Descarregue o ficheiro de instalação de configuração unificada de recuperação do site.
5. Transfira a chave de registo do cofre. Precisas disto quando executares a Configuração Unificada. A chave é válida durante cinco dias depois de gerá-la.

   ![Configurar a origem](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registar o servidor de configuração no cofre

Faça o seguinte antes de começar: 

#### <a name="verify-time-accuracy"></a>Verificar precisão de tempo
Na máquina do servidor de configuração, certifique-se de que o relógio do sistema está sincronizado com um [Servidor de Tempo](/windows-server/networking/windows-time-service/windows-time-service-top). Deve coincidir. Se forem 15 minutos na frente ou atrás, a configuração pode falhar.

#### <a name="verify-connectivity"></a>Verificar conectividade
Certifique-se de que a máquina pode aceder a estes URLs com base no seu ambiente: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

As regras de firewall baseadas em endereços IP devem permitir a comunicação a todos os URLs Azure listados acima sobre a porta HTTPS (443). Para simplificar e limitar as gamas IP, recomenda-se que seja feita a filtragem de URL.

- **IPs comerciais** - Permitir os intervalos IP do [Centro de Dados Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)e a porta HTTPS (443). Permita que os intervalos de endereços IP para a região Azure da sua subscrição suportem os URLs de AAD, Backup, Replicação e Armazenamento.  
- **IPs do governo** - Permitir que o [Azure Government Datacenter IP Ranges](https://www.microsoft.com/en-us/download/details.aspx?id=57063), e o porto HTTPS (443) para todas as regiões USGov (Virginia, Texas, Arizona e Iowa) apoiem URLs AAD, Backup, Replication e Storage.  

#### <a name="run-setup"></a>Executar a configuração
Executar configuração unificada como administrador local, para instalar o servidor de configuração. O servidor de processo e o servidor alvo principal também são instalados por padrão no servidor de configuração.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Após o início do registo, o servidor de configuração é apresentado na página **'Servidores' de Definições**  >   no cofre.

### <a name="configure-target-settings-for-replication"></a>Configurar as definições-alvo para a replicação

Selecione e verifique os recursos de destino.

1. Clique **em Preparar a infraestrutura**  >  **Target** e selecione a subscrição Azure que pretende utilizar.
2. Especifique o modelo de implementação do alvo.
3. A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

   ![Destino](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Para criar uma nova política de replicação, clique em Políticas de Replicação **da Infraestrutura de Recuperação do Local**  >    >  **+Política de Replicação**.
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limiar RPO**, especifique o limite do objetivo de ponto de recuperação (RPO). Este valor especifica a frequência com que são criados pontos de recuperação de dados. Será gerado um alerta se a replicação contínua exceder este limite.
4. Em **Retenção do ponto de recuperação**, especifique (em horas) a duração da janela de retenção para cada ponto de recuperação. As VMs replicadas podem ser recuperadas para qualquer ponto numa janela. É suportada uma retenção de até 24 horas para máquinas replicadas para o armazenamento premium e até 72 horas para armazenamento standard.
5. Na **frequência instantânea consistente da App,** especifique com que frequência (em minutos) serão criados pontos de recuperação que contenham instantâneos consistentes com aplicações. Clique em **OK** para criar a política.

    ![Política de replicação](./media/physical-azure-disaster-recovery/replication-policy.png)


A política é associada automaticamente ao servidor de configuração. Por predefinição, uma política correspondente é criada automaticamente para reativação pós-falha. Por exemplo, se a política de replicação for **a política de rep-política,** então é criada uma política **de rep-failback** política de recuo. Esta política não é utilizada depois de iniciar uma reativação pós-falha a partir do Azure.

### <a name="enable-replication"></a>Ativar a replicação

- A Recuperação do Site instalará o serviço mobility quando a replicação estiver ativada.
- Quando ativa a replicação de um servidor, pode demorar 15 minutos ou mais para que as alterações entrem em vigor e apareçam no portal.

1. Clique **em Replicar a aplicação**  >  **Fonte**.
2. Em **Origem**, selecione o servidor de configuração.
3. No **tipo máquina,** selecione **máquinas físicas**.
4. Selecione o servidor de processo (o servidor de configuração). Em seguida, clique em **OK**.
5. No **Target**, selecione a subscrição e o grupo de recursos no qual pretende criar os VMs Azure após o failover. Escolha o modelo de implementação que pretende utilizar no Azure (gestão clássica ou de recursos).
6. Selecione a conta de armazenamento do Azure que pretende utilizar para replicar os dados. 
7. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se vão ligar quando forem criadas após a ativação pós-falha.
8. Selecione **Configurar agora para máquinas selecionadas**, para aplicar a definição de rede a todas as máquinas selecionadas para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por máquina. 
9. Em **Máquinas Físicas,** clique em **+Máquina física.** Especifique o nome e o endereço IP. Selecione o sistema operativo da máquina que pretende replicar. Leva alguns minutos para os servidores serem descobertos e listados. 

   > [!WARNING]
   > Tem de introduzir o endereço IP do Azure VM que pretende mover

10. Nas propriedades **Properties**  >  **Configure**, selecione a conta que será utilizada pelo servidor de processos para instalar automaticamente o serviço de Mobilidade na máquina.
11. Nas **definições de replicação,**  >  **certifique-se de que** a política de replicação correta está selecionada. 
12. Clique **em Ativar a replicação.** Pode acompanhar o progresso do trabalho de **Proteção ativa** em **Definições**  >  **de**  >  **Empregos Locais de Recuperação do Local .** Depois de o trabalho **de Proteção finalização,** a máquina está pronta para falhar.


Para monitorizar os servidores que adiciona, pode verificar a última vez que os descobertos são os últimos contactos dos **servidores** de  >  **configuração.** Para adicionar máquinas sem esperar por um tempo de descoberta programado, realce o servidor de configuração (não clique nele) e clique em **Refresh**.

## <a name="test-the-configuration"></a>Teste a configuração.


1. Navegue para o cofre, em **Definições**  >  **Itens Replicados,** clique na máquina Virtual que pretende mover-se para a região alvo, clique no ícone **+Test Failover.**
2. Em **Ativação Pós-falha**, selecione um ponto de recuperação para utilizar na ativação pós-falha:

   - **Processado mais recentemente**: faz a ativação pós-falha da VM para o ponto de recuperação mais recente processado pelo serviço do Site Recovery. O carimbo de data/hora é apresentado. Com esta opção, não é despendido tempo a processar os dados, pelo que oferece um RTO (Objetivo de Tempo de Recuperação) baixo
   - **Consistente com a aplicação mais recente**: esta opção faz a ativação pós-falha de todas as VMs para o último ponto de recuperação consistente com a aplicação. O carimbo de data/hora é apresentado.
   - **Personalizado**: selecione qualquer ponto de recuperação.

3. Selecione a rede virtual Target Azure para a qual pretende mover os VMs Azure para testar a configuração. 

   > [!IMPORTANT]
   > Recomendamos que utilize uma rede VM Azure separada para a falha de teste, e não a rede de produção na qual pretende mover os seus VMs eventualmente que foi configurado quando permitiu a replicação.

4. Para começar a testar o movimento, clique **em OK**. Para acompanhar o progresso, clique em VM para abrir as respetivas propriedades. Também pode clicar na tarefa **Ativação Pós-falha de Teste** no nome do cofre > **Definições** > **Tarefas** > **Tarefas do Site Recovery**.
5. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Certifique-se de que a VM está em execução, tem as dimensões adequadas e está ligada à rede devida.
6. Se desejar eliminar o VM criado como parte do teste do movimento, clique em **teste de limpeza** no item replicado. Em **Notas,** registem e guarde quaisquer observações associadas ao teste.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Faça a mudança para a região alvo e confirme.

1. Navegue para o cofre, em **Definições**  >  **Itens Replicados,** clique na máquina virtual e, em seguida, clique em **Failover**.
2. Em **Ativação pós-falha**, selecione **Mais recente**. 
3. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. O Site Recovery tenta encerrar a VM de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode acompanhar o progresso falhado na página **Jobs.** 
4. Uma vez concluído o trabalho, verifique se o VM aparece na região de Azure alvo, como esperado.
5. Em **Itens replicados**, clique com o botão direito do rato na VM > **Consolidar**. Isto termina o processo de mudança para a região alvo. Espere até que o trabalho de compromisso termine.

## <a name="discard-the-resource-in-the-source-region"></a>Deite fora o recurso na região de origem 

- Navegue para o VM.  Clique na **replicação de desativação.**  Isto impede o processo de cópia dos dados para o VM.  

   > [!IMPORTANT]
   > É importante executar este passo para evitar ser carregado para a replicação DAR.

Caso não tenha planos para reutilizar nenhum dos recursos de origem, por favor, proceda ao próximo conjunto de passos.

1. Proceder à eliminação de todos os recursos de rede relevantes na região de origem que enumerou como parte do Passo 4 na [Preparação dos VM de origem](#prepare-the-source-vms) 
2. Eliminar a conta de armazenamento correspondente na região de origem.



## <a name="next-steps"></a>Passos seguintes

Neste tutorial mudou um Azure VM para uma região de Azure diferente. Agora pode configurar a recuperação de desastres para o VM em comovido.

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre após a migração](azure-to-azure-quickstart.md)
