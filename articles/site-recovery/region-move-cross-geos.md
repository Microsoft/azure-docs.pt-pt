---
title: Mover VMs de IaaS do Azure entre regiões públicas com o serviço Azure Site Recovery e o Azure Government | Documentos da Microsoft
description: Utilize o Azure Site Recovery para mover VMs de IaaS do Azure entre o Azure Government e regiões públicas.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: fe2620c7a07389b2a86d36420eadd2ef5883f5da
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013238"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Mover as VMs do Azure entre regiões públicas e o Azure Government 

Pode querer mover as VMs de IaaS entre o Azure Government e regiões públicas para aumentar a disponibilidade das suas VMs existentes, melhorar a capacidade de gestão ou por motivos de governação, conforme detalhado [aqui](azure-to-azure-move-overview.md).

Além de utilizar o [do Azure Site Recovery](site-recovery-overview.md) serviço para gerir e orquestrar a recuperação após desastre de máquinas no local e VMs do Azure para fins de continuidade do negócio e recuperação após desastre (BCDR), também pode utilizar o Site Recuperação para gerir mover VMs do Azure para uma região secundária.       

Este tutorial mostra como mover VMs do Azure entre regiões públicas com o Azure Site Recovery e o Azure Government. O mesmo pode ser estendido para mover VMs entre pares de região que não estão dentro do mesmo cluster geográfico. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verificar os pré-requisitos
> * Preparar as VMs de origem
> * Preparar a região de destino
> * Copiar dados para a região de destino
> * Testar a configuração
> * Efetuar a mudança
> * Eliminar os recursos na região de origem

> [!IMPORTANT]
> Este tutorial mostra como mover VMs do Azure entre regiões públicas e o Azure Government, ou entre pares de regiões que não são suportadas pela solução de recuperação após desastre regular para VMs do Azure. No caso, os pares de regiões de origem e destino estão [suportado](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support), consulte este [documento](azure-to-azure-tutorial-migrate.md) para a mudança. Se o requisito melhorar a disponibilidade ao mover as VMs no conjunto de disponibilidade para VMs de afixadas por zona numa região diferente, consulte o tutorial [aqui](move-azure-VMs-AVset-Azone.md).

> [!IMPORTANT]
> Não é recomendado utilizar este método para configurar DR entre pares de região não suportada, como os pares são definidos tendo a latência de dados em mente, o que é fundamental para um cenário de DR.

## <a name="verify-prerequisites"></a>Verificar os pré-requisitos

> [!NOTE]
> Certifique-se de que compreende os [arquitetura e componentes](physical-azure-architecture.md) para este cenário. Esta arquitetura será utilizada para mover VMs do Azure, **tratando as VMs que os servidores físicos**.

- Reveja os [requisitos de suporte](vmware-physical-secondary-support-matrix.md) de todos os componentes.
- Certifique-se de que estão em conformidade com os servidores que pretende replicar [requisitos de VM do Azure](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Prepare uma conta para a instalação automática do serviço de mobilidade em cada servidor que pretende replicar.

- Tenha em atenção que depois de falhas para a região de destino no Azure, não é possível diretamente executar uma ativação pós-falha para a região de origem. Terá de configurar a replicação novamente para o destino.

### <a name="verify-azure-account-permissions"></a>Verifique as permissões de conta do Azure

Certifique-se de que a sua conta do Azure tem permissões para a replicação de VMs para o Azure.

- Reveja os [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) precisa de replicar máquinas para o Azure.
- Verifique e modifique [acesso baseado em funções](../role-based-access-control/role-assignments-portal.md) permissões. 

### <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Configurar um destino [rede do Azure](../virtual-network/quick-create-portal.md).

- VMs do Azure são colocadas na rede quando forem criadas após a ativação pós-falha.
- A rede deve estar na mesma região que o Cofre dos serviços de recuperação


### <a name="set-up-an-azure-storage-account"></a>Configurar uma conta de armazenamento do Azure

Configurar uma [conta de armazenamento do Azure](../storage/common/storage-quickstart-create-account.md).

- O site Recovery replica as máquinas no local para o armazenamento do Azure. VMs do Azure são criadas a partir do armazenamento após a ocorrência da ativação pós-falha.
- A conta de armazenamento tem de estar na mesma região que o cofre dos Serviços de Recuperação.


## <a name="prepare-the-source-vms"></a>Preparar as VMs de origem

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de mobilidade tem de ser instalado em cada servidor que pretende replicar. Site Recovery instala este serviço automaticamente quando ativa a replicação para o servidor. Para instalar automaticamente, terá de preparar uma conta que o Site Recovery irá utilizar para aceder ao servidor.

- Pode utilizar um domínio ou conta local
- Para VMs do Windows, se não estiver a utilizar uma conta de domínio, desative o controlo de acesso de utilizador remoto na máquina local. Para tal, no registo em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy**, com um valor de 1.
- Para adicionar a entrada de registo para desativar a definição de uma CLI, escreva:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Para o Linux, a conta deve ser a raiz no servidor Linux de origem.


## <a name="prepare-the-target-region"></a>Preparar a região de destino

1. Certifique-se de que a sua subscrição do Azure permite criar VMs na região de destino utilizada para recuperação após desastre. Contacte o suporte para ativar a quota necessária.

2. Certifique-se de que a sua subscrição tem recursos suficientes para suportar as VMs com tamanhos que correspondem às VMs de origem. Se estiver a utilizar o Site Recovery para copiar dados para o destino, ele escolhe o mesmo tamanho ou o tamanho mais próximo possível da VM de destino.

3. Certifique-se de que criar um recurso de destino para cada componente identificado no esquema de rede de origem. Isso é importante para se certificar de que, postagens cutting durante para a região de destino, as VMs têm todas as funcionalidades e funções que tinham na origem.

    > [!NOTE]
    > O Azure Site Recovery Deteta e cria uma rede virtual, quando ativar a replicação para a VM de origem, ou pode também criar previamente uma rede e atribuir à VM no fluxo de utilizador para ativar a replicação automaticamente. Mas para outros recursos, conforme mencionado abaixo, terá de criá-los manualmente na região de destino.

     Consulte os seguintes documentos para criar os recursos de rede mais comumente usada relevantes para si, com base na configuração da VM de origem.

    - [Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Balanceadores de carga](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [IP público](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
    Para outros componentes de rede, consulte o funcionamento em rede [documentação.](https://docs.microsoft.com/azure/#pivot=products&panel=network) 

4. Manualmente [criar uma rede de não produção](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) na região de destino se pretender testar a configuração antes de executar o desligamento final sobre a região de destino. Isso criará um mínimo interferências com a produção e é recomendado.

## <a name="copy-data-to-the-target-region"></a>Copiar dados para a região de destino
Os passos abaixo irão orientá-lo como utilizar o Azure Site Recovery para copiar dados para a região de destino.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Crie o cofre em qualquer região, exceto na região de origem.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
2. Clique em **criar um recurso** > **ferramentas de gestão** > **cópia de segurança e recuperação de Site**.
3. No **Nome**, especifique o nome amigável **ContosoVMVault**. Se tiver mais do que uma um. subscrição, selecionar a adequada.
4. Crie um grupo de recursos **ContosoRG**.
5. Selecione uma região do Azure. Para verificar as regiões suportadas, veja a disponibilidade geográfica em [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Nos cofres de serviços de recuperação, clique em **descrição geral** > **ConsotoVMVault** > **+ replicar**
7. Selecione **para o Azure** > **não virtualizado/outro**.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Configure o servidor de configuração para detetar VMs.


Configurar o servidor de configuração, registe-o no cofre e detetar VMs.

1. Clique em **recuperação de sites** > **preparar infraestrutura** > **origem**.
2. Se não tiver um servidor de configuração, clique em **+ servidor de configuração**.
3. Na **Adicionar servidor**, verifique se **servidor de configuração** aparece no **tipo de servidor**.
4. Transfira o ficheiro de instalação do Site Recovery Unified Setup.
5. Transfira a chave de registo do cofre. Precisará disto quando executar a configuração unificada. A chave é válida durante cinco dias depois de gerá-la.

   ![Configurar a origem](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registar o servidor de configuração no Cofre

Faça o seguinte antes de começar: 

#### <a name="verify-time-accuracy"></a>Certifique-se de precisão de hora
Na máquina do servidor de configuração, certifique-se de que o relógio do sistema está sincronizado com um [servidor de horas](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Deve ser igual. Se se trata de 15 minutos na frente ou por trás, a configuração poderá falhar.

#### <a name="verify-connectivity"></a>Verificar a conectividade
Certifique-se que a máquina pode aceder estes URLs com base no seu ambiente: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

Regras de firewall baseadas no endereço IP devem permitir a comunicação a todos os URLs do Azure que estão listadas acima através da porta HTTPS (443). Para simplificar e limitar a intervalos de IP, recomenda-se que a filtragem de URLs ser feito.

- **IPs comerciais** -permitir que o [intervalos de IP de Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)e a porta HTTPS (443). Permitir que os intervalos de endereços IP para a região do Azure da sua subscrição para suportar o AAD, a cópia de segurança, a replicação e a URLs de armazenamento.  
- **Governo IPs** -permitir que o [intervalos de IP do Azure Government Datacenter](https://www.microsoft.com/en-us/download/details.aspx?id=57063)e a porta HTTPS (443) para todas as regiões de USGov (Virginia, Texas, Arizona e Iowa) para suportar o AAD, cópia de segurança, replicação e URLs de armazenamento.  

#### <a name="run-setup"></a>Executar a configuração
Execute a configuração unificada como administrador Local, para instalar o servidor de configuração. O servidor de processos e o servidor de destino mestre também são instalados por predefinição no servidor de configuração.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Após a conclusão do registo, o servidor de configuração é apresentado no **configurações** > **servidores** página no cofre.

### <a name="configure-target-settings-for-replication"></a>Configurar definições de destino para replicação

Selecione e verifique os recursos de destino.

1. Clique em **Preparar a infraestrutura** > **Destino** e selecione a subscrição do Azure que pretende utilizar.
2. Especifique o modelo de implementação de destino.
3. A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

   ![Destino](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Para criar uma nova política de replicação, clique em **Infraestrutura do Site Recovery** > **Políticas de Replicação** > **+Política de Replicação**.
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limiar RPO**, especifique o limite do objetivo de ponto de recuperação (RPO). Este valor Especifica a frequência com que são criados pontos de recuperação de dados. Será gerado um alerta se a replicação contínua exceder este limite.
4. Em **Retenção do ponto de recuperação**, especifique (em horas) a duração da janela de retenção para cada ponto de recuperação. As VMs replicadas podem ser recuperadas para qualquer ponto numa janela. É suportada uma retenção de até 24 horas para máquinas replicadas para o armazenamento premium e até 72 horas para armazenamento standard.
5. Na **frequência de instantâneos consistentes com a aplicação**, especifique com que frequência (em minutos) pontos de recuperação que contêm instantâneos consistentes com aplicações serão criados. Clique em **OK** para criar a política.

    ![Política de replicação](./media/physical-azure-disaster-recovery/replication-policy.png)


A política é associada automaticamente ao servidor de configuração. Por predefinição, uma política correspondente é criada automaticamente para reativação pós-falha. Por exemplo, se a política de replicação for **rep-policy** , em seguida, uma política de reativação pós-falha **rep-policy-failback** é criado. Esta política não é utilizada depois de iniciar uma reativação pós-falha a partir do Azure.

### <a name="enable-replication"></a>Ativar a replicação

- Recuperação de site irá instalar o serviço de mobilidade quando a replicação está ativada.
- Quando ativa a replicação para um servidor, pode demorar 15 minutos ou mais tempo para que as alterações entrem em vigor e são apresentados no portal.

1. Clique em **Replicar aplicação** > **Origem**.
2. Em **Origem**, selecione o servidor de configuração.
3. Na **tipo de máquina**, selecione **máquinas físicas**.
4. Selecione o servidor de processos (o servidor de configuração). Em seguida, clique em **OK**.
5. Na **destino**, selecione a subscrição e o grupo de recursos no qual pretende criar as VMs do Azure após a ativação pós-falha. Escolha o modelo de implementação que pretende utilizar no Azure (clássico ou gestão de recursos).
6. Selecione a conta de armazenamento do Azure que pretende utilizar para replicar os dados. 
7. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se vão ligar quando forem criadas após a ativação pós-falha.
8. Selecione **Configurar agora para as máquinas selecionadas** para aplicar a definição de rede a todas as máquinas selecionadas para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por máquina. 
9. Na **máquinas físicas**e clique em **+ computador**. Especifique o nome e endereço IP. Selecione o sistema operativo da máquina que pretende replicar. Demora alguns minutos para que os servidores sejam detetadas e listados. 

   > [!WARNING]
   > Tem de introduzir o endereço IP da VM do Azure que pretende mover

10. Na **propriedades** > **configurar propriedades**, selecione a conta que será utilizada pelo servidor de processos para instalar automaticamente o serviço de mobilidade na máquina.
11. Em **Definições de replicação** > **Configurar as definições de replicação**, certifique-se de que a política de replicação correta está selecionada. 
12. Clique em **Ativar Replicação**. Pode controlar o progresso da tarefa **Ativar Proteção** em **Definições** > **Tarefas** > **Tarefas do Site Recovery**. Depois da tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.


Para monitorizar os servidores que adicionar, pode verificar a última hora de deteção na **servidores de configuração** > **último contacto em**. Para adicionar máquinas sem esperar por um período de deteção agendada, realce o servidor de configuração (não clique nele) e clique em **atualizar**.

## <a name="test-the-configuration"></a>Testar a configuração


1. Navegue para o cofre, no **configurações** > **itens replicados**, clique na máquina Virtual que pretende mover para a região de destino, clique em **+ ativação pós-falha de teste** ícone.
2. Em **Ativação Pós-falha**, selecione um ponto de recuperação para utilizar na ativação pós-falha:

   - **Processado mais recentemente**: Falha da VM através do ponto de recuperação mais recente processado pelo serviço do Site Recovery. O carimbo de data/hora é apresentado. Com esta opção, não é despendido tempo a processar os dados, pelo que oferece um RTO (Objetivo de Tempo de Recuperação) baixo
   - **Mais recente consistente com a aplicação**: Esta opção faz a ativação pós-falha de todas as VMs para o ponto de recuperação consistente com a aplicação mais recente. O carimbo de data/hora é apresentado.
   - **Custom**: Selecione qualquer ponto de recuperação.

3. Selecione o destino do Azure para o qual pretende mover as VMs do Azure para testar a configuração de rede virtual. 

   > [!IMPORTANT]
   > Recomendamos que utilize uma rede de VM do Azure separada para ativação pós-falha de teste e não a rede de produção para o qual pretende mover as suas VMs, eventualmente, que foi configurada quando ativou a replicação.

4. Para começar a testar a mudança, clique em **OK**. Para acompanhar o progresso, clique em VM para abrir as respetivas propriedades. Também pode clicar na tarefa **Ativação Pós-falha de Teste** no nome do cofre > **Definições** > **Tarefas** > **Tarefas do Site Recovery**.
5. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Certifique-se de que a VM está em execução, tem as dimensões adequadas e está ligada à rede devida.
6. Se pretender eliminar a VM criada como parte do teste a mudança, clique em **ativação pós-falha de teste de limpeza** no item replicado. Na **notas**, registe e guarde todas as observações associadas com o teste.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Efetuar a mudança para a região de destino e confirme.

1. Navegue para o cofre, no **configurações** > **itens replicados**, clique na máquina virtual e, em seguida, clique em **ativação pós-falha**.
2. Em **Ativação pós-falha**, selecione **Mais recente**. 
3. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. O Site Recovery tenta encerrar a VM de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode seguir o progresso da ativação pós-falha na página **Trabalhos**. 
4. Quando a tarefa estiver concluída, verifique se a VM aparece a região do Azure de destino conforme esperado.
5. Em **Itens replicados**, clique com o botão direito do rato na VM > **Consolidar**. Isto conclui o processo de mover para a região de destino. Aguarde até que a tarefa de consolidação é concluída.

## <a name="discard-the-resource-in-the-source-region"></a>Eliminar o recurso na região de origem 

- Navegue para a VM.  Clique em **desative a replicação**.  Isso interromperá o processo de copiar os dados para a VM.  

   > [!IMPORTANT]
   > É importante efetuar este passo para evitar ser cobrado para a replicação de ASR.

No caso de ter não existem planos para reutilizar qualquer um dos recursos de origem, avance com o próximo conjunto de passos.

1. Avance para eliminar todos os recursos de rede relevantes na região de origem que listei como parte do passo 4 em [preparar as VMs de origem](#prepare-the-source-vms) 
2. Elimine a conta de armazenamento correspondente na região de origem.



## <a name="next-steps"></a>Passos Seguintes

Neste tutorial Moveu uma VM do Azure para uma região do Azure diferente. Agora pode configurar a recuperação de desastres para a VM movida.

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre após a migração](azure-to-azure-quickstart.md)
