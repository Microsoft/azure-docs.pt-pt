---
title: Mover VMs do Azure entre regiões governamentais e públicas com Azure Site Recovery
description: Use Azure Site Recovery para mover as VMs do Azure entre o Azure governamental e as regiões públicas.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 2a749e9345fec0e91751641cd15805d7f7d62d95
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961416"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Mover VMs do Azure entre o Azure governamental e as regiões públicas 

Talvez você queira mover suas VMs de IaaS entre o Azure governamental e as regiões públicas para aumentar a disponibilidade de suas VMs existentes, melhorar a capacidade de gerenciamento ou por motivos de governança, conforme detalhado [aqui](azure-to-azure-move-overview.md).

Além de usar o serviço de [Azure site Recovery](site-recovery-overview.md) para gerenciar e orquestrar a recuperação de desastres de máquinas locais e VMs do Azure para fins de continuidade de negócios e recuperação de desastres (BCDR), você também pode usar site Recovery para gerenciar a movimentação de VMs do Azure para uma região secundária.       

Este tutorial mostra como mover VMs do Azure entre regiões do Azure governamental e públicas usando o Azure Site Recovery. O mesmo pode ser estendido para mover VMs entre pares de regiões que não estão dentro do mesmo cluster geográfico. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verificar os pré-requisitos
> * Preparar as VMs de origem
> * Preparar a região de destino
> * Copiar dados para a região de destino
> * Testar a configuração
> * Executar a movimentação
> * Descartar os recursos na região de origem

> [!IMPORTANT]
> Este tutorial mostra como mover VMs do Azure entre regiões do Azure governamental e públicas, ou entre pares de regiões que não têm suporte da solução de recuperação de desastre regular para VMs do Azure. Caso haja [suporte](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)para os pares de regiões de origem e destino, consulte este [documento](azure-to-azure-tutorial-migrate.md) para a movimentação. Se seu requisito for melhorar a disponibilidade movendo as VMs em um conjunto de disponibilidade para VMs fixadas de zona em uma região diferente, consulte o tutorial [aqui](move-azure-VMs-AVset-Azone.md).

> [!IMPORTANT]
> Não é aconselhável usar esse método para configurar a DR entre pares de regiões sem suporte, já que os pares são definidos mantendo a latência de dados em mente, o que é essencial para um cenário de recuperação de desastres.

## <a name="verify-prerequisites"></a>Verificar os pré-requisitos

> [!NOTE]
> Verifique se você entendeu a [arquitetura e os componentes](physical-azure-architecture.md) para esse cenário. Essa arquitetura será usada para mover VMs do Azure, **tratando as VMs como servidores físicos**.

- Reveja os [requisitos de suporte](vmware-physical-secondary-support-matrix.md) de todos os componentes.
- Verifique se os servidores que você deseja replicar estão em conformidade com [os requisitos de VM do Azure](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Prepare uma conta para a instalação automática do serviço de mobilidade em cada servidor que você deseja replicar.

- Observe que, depois de fazer failover para a região de destino no Azure, você não pode executar um failback diretamente para a região de origem. Você precisará configurar a replicação novamente de volta para o destino.

### <a name="verify-azure-account-permissions"></a>Verificar permissões de conta do Azure

Verifique se sua conta do Azure tem permissões para replicação de VMs para o Azure.

- Examine as [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) necessárias para replicar computadores no Azure.
- Verifique e modifique as permissões [de acesso baseado em função](../role-based-access-control/role-assignments-portal.md) . 

### <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Configure uma rede de destino do [Azure](../virtual-network/quick-create-portal.md).

- As VMs do Azure são colocadas nessa rede quando são criadas após o failover.
- A rede deve estar na mesma região que o cofre dos serviços de recuperação


### <a name="set-up-an-azure-storage-account"></a>Configurar uma conta de armazenamento do Azure

Configure uma [conta de armazenamento do Azure](../storage/common/storage-quickstart-create-account.md).

- Site Recovery Replica computadores locais para o armazenamento do Azure. As VMs do Azure são criadas a partir do armazenamento após a ocorrência do failover.
- A conta de armazenamento tem de estar na mesma região que o cofre dos Serviços de Recuperação.


## <a name="prepare-the-source-vms"></a>Preparar as VMs de origem

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de mobilidade deve ser instalado em cada servidor que você deseja replicar. Site Recovery instala esse serviço automaticamente quando você habilita a replicação para o servidor. Para instalar automaticamente, você precisa preparar uma conta que Site Recovery será usada para acessar o servidor.

- Você pode usar uma conta local ou de domínio
- Para VMs do Windows, se você não estiver usando uma conta de domínio, desabilite o controle de acesso de usuário remoto no computador local. Para fazer isso, no registro em **HKEY_LOCAL_MACHINE \Software\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy**, com um valor de 1.
- Para adicionar a entrada do registro para desabilitar a configuração de uma CLI, digite: ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Para o Linux, a conta deve ser raiz no servidor Linux de origem.


## <a name="prepare-the-target-region"></a>Preparar a região de destino

1. Certifique-se de que a sua subscrição do Azure permite criar VMs na região de destino utilizada para recuperação após desastre. Contacte o suporte para ativar a quota necessária.

2. Certifique-se de que a sua subscrição tem recursos suficientes para suportar as VMs com tamanhos que correspondem às VMs de origem. Se você estiver usando Site Recovery para copiar dados para o destino, ele escolherá o mesmo tamanho ou o tamanho mais próximo possível para a VM de destino.

3. Certifique-se de criar um recurso de destino para cada componente identificado no layout de rede de origem. Isso é importante para garantir que, após a recorte para a região de destino, suas VMs tenham toda a funcionalidade e os recursos que você tinha na origem.

    > [!NOTE]
    > Azure Site Recovery descobre e cria automaticamente uma rede virtual quando você habilita a replicação para a VM de origem, ou você também pode criar previamente uma rede e atribuir à VM no fluxo do usuário para habilitar a replicação. Mas para todos os outros recursos, conforme mencionado abaixo, você precisa criá-los manualmente na região de destino.

     Consulte os documentos a seguir para criar os recursos de rede usados com mais frequência relevantes para você, com base na configuração da VM de origem.

    - [Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Balanceadores de carga](https://docs.microsoft.com/azure/load-balancer)
    - [IP público](../virtual-network/virtual-network-public-ip-address.md)
    
    Para qualquer outro componente de rede, consulte a documentação de rede [.](https://docs.microsoft.com/azure/#pivot=products&panel=network) 

4. [Crie manualmente uma rede que não seja de produção](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) na região de destino se desejar testar a configuração antes de executar a última transferência para a região de destino. Isso criará uma interferência mínima com a produção e é recomendado.

## <a name="copy-data-to-the-target-region"></a>Copiar dados para a região de destino
As etapas a seguir irão orientá-lo sobre como usar Azure Site Recovery para copiar dados para a região de destino.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Crie o cofre em qualquer região, exceto na região de origem.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
2. Clique em **criar um recurso** > **ferramentas de gerenciamento** > **backup e site Recovery**.
3. No **Nome**, especifique o nome amigável **ContosoVMVault**. Se você tiver mais de um. , selecione a assinatura apropriada.
4. Crie um grupo de recursos **ContosoRG**.
5. Selecione uma região do Azure. Para verificar as regiões suportadas, veja a disponibilidade geográfica em [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Em cofres dos serviços de recuperação, clique em **visão geral** > **ConsotoVMVault** >  **+ replicar**
7. Selecione **para o Azure** > **não virtualizados/outros**.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Configure o servidor de configuração para descobrir VMs.


Configure o servidor de configuração, registre-o no cofre e descubra as VMs.

1. Clique em **Site Recovery** > **preparar a infraestrutura** > **origem**.
2. Se você não tiver um servidor de configuração, clique em **+ servidor de configuração**.
3. Em **Adicionar servidor**, verifique se o **servidor de configuração** aparece em tipo de **servidor**.
4. Baixe o arquivo de instalação do Site Recovery Unified setup.
5. Transfira a chave de registo do cofre. Isso é necessário quando você executa a instalação unificada. A chave é válida durante cinco dias depois de gerá-la.

   ![Configurar a origem](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registrar o servidor de configuração no cofre

Antes de começar, faça o seguinte: 

#### <a name="verify-time-accuracy"></a>Verificar precisão do tempo
No computador do servidor de configuração, verifique se o relógio do sistema está sincronizado com um [servidor de horário](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Ele deve corresponder. Se for 15 minutos na frente ou atrás, a instalação poderá falhar.

#### <a name="verify-connectivity"></a>Verificar conectividade
Verifique se o computador pode acessar essas URLs com base em seu ambiente: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

As regras de firewall baseadas em endereço IP devem permitir a comunicação com todas as URLs do Azure listadas acima na porta HTTPS (443). Para simplificar e limitar os intervalos de IP, é recomendável que a filtragem de URL seja feita.

- **IPS comerciais** – permitir os [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)e a porta HTTPS (443). Permitir intervalos de endereços IP para a região do Azure da sua assinatura para dar suporte às URLs do AAD, backup, replicação e armazenamento.  
- **IPS do governo** -permita os [intervalos de IP do datacenter do Azure governamental](https://www.microsoft.com/en-us/download/details.aspx?id=57063)e a porta HTTPS (443) para todas as regiões USGov (Virgínia, Texas, Arizona e Iowa) para dar suporte ao AAD, backup, replicação e URLs de armazenamento.  

#### <a name="run-setup"></a>Executar a configuração
Execute a instalação unificada como um administrador local para instalar o servidor de configuração. O servidor de processo e o servidor de destino mestre também são instalados por padrão no servidor de configuração.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Após a conclusão do registro, o servidor de configuração é exibido na página **configurações** > **servidores** no cofre.

### <a name="configure-target-settings-for-replication"></a>Definir configurações de destino para replicação

Selecione e verifique os recursos de destino.

1. Clique em **Preparar a infraestrutura** > **Destino** e selecione a subscrição do Azure que pretende utilizar.
2. Especifique o modelo de implantação de destino.
3. A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

   ![Destino](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Para criar uma nova política de replicação, clique em **Infraestrutura do Site Recovery** > **Políticas de Replicação** >  **+Política de Replicação**.
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limiar RPO**, especifique o limite do objetivo de ponto de recuperação (RPO). Esse valor especifica a frequência com que os pontos de recuperação de dados são criados. Será gerado um alerta se a replicação contínua exceder este limite.
4. Em **Retenção do ponto de recuperação**, especifique (em horas) a duração da janela de retenção para cada ponto de recuperação. As VMs replicadas podem ser recuperadas para qualquer ponto numa janela. É suportada uma retenção de até 24 horas para máquinas replicadas para o armazenamento premium e até 72 horas para armazenamento standard.
5. Em **frequência de instantâneo consistente com o aplicativo**, especifique com que frequência (em minutos) os pontos de recuperação que contêm instantâneos consistentes com o aplicativo serão criados. Clique em **OK** para criar a política.

    ![Política de replicação](./media/physical-azure-disaster-recovery/replication-policy.png)


A política é associada automaticamente ao servidor de configuração. Por predefinição, uma política correspondente é criada automaticamente para reativação pós-falha. Por exemplo, se a política de replicação for **Rep-Policy** , uma política de failback **Rep-Policy-failback** será criada. Esta política não é utilizada depois de iniciar uma reativação pós-falha a partir do Azure.

### <a name="enable-replication"></a>Ativar a replicação

- Site Recovery instalará o serviço de mobilidade quando a replicação estiver habilitada.
- Quando você habilita a replicação para um servidor, pode levar 15 minutos ou mais para que as alterações entrem em vigor e apareçam no Portal.

1. Clique em **Replicar aplicação** > **Origem**.
2. Em **Origem**, selecione o servidor de configuração.
3. Em **tipo de computador**, selecione **computadores físicos**.
4. Selecione o servidor de processo (o servidor de configuração). Em seguida, clique em **OK**.
5. Em **destino**, selecione a assinatura e o grupo de recursos no qual você deseja criar as VMs do Azure após o failover. Escolha o modelo de implantação que você deseja usar no Azure (clássico ou gerenciamento de recursos).
6. Selecione a conta de armazenamento do Azure que pretende utilizar para replicar os dados. 
7. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se vão ligar quando forem criadas após a ativação pós-falha.
8. Selecione **Configurar agora para as máquinas selecionadas** para aplicar a definição de rede a todas as máquinas selecionadas para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por máquina. 
9. Em **computadores físicos**e clique em **+ computador físico**. Especifique o nome e o endereço IP. Selecione o sistema operacional do computador que você deseja replicar. Leva alguns minutos para que os servidores sejam descobertos e listados. 

   > [!WARNING]
   > Você precisa inserir o endereço IP da VM do Azure que pretende mover

10. Em **propriedades** > **Configurar Propriedades**, selecione a conta que será usada pelo servidor de processo para instalar automaticamente o serviço de mobilidade no computador.
11. Em **Definições de replicação** > **Configurar as definições de replicação**, certifique-se de que a política de replicação correta está selecionada. 
12. Clique em **Ativar Replicação**. Pode controlar o progresso da tarefa **Ativar Proteção** em **Definições** > **Tarefas** > **Tarefas do Site Recovery**. Depois da tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.


Para monitorar os servidores adicionados, você pode verificar a hora da última descoberta para eles em **servidores de configuração** > **último contato em**. Para adicionar computadores sem aguardar um horário de descoberta agendada, realce o servidor de configuração (não clique nele) e clique em **Atualizar**.

## <a name="test-the-configuration"></a>Testar a configuração


1. Navegue até o cofre, em **configurações** > **itens replicados**, clique na máquina virtual que você pretende mover para a região de destino, clique em + ícone de **failover de teste** .
2. Em **Ativação Pós-falha**, selecione um ponto de recuperação para utilizar na ativação pós-falha:

   - **Processado mais recentemente**: faz a ativação pós-falha da VM para o ponto de recuperação mais recente processado pelo serviço do Site Recovery. O carimbo de data/hora é apresentado. Com esta opção, não é despendido tempo a processar os dados, pelo que oferece um RTO (Objetivo de Tempo de Recuperação) baixo
   - **Consistente com a aplicação mais recente**: esta opção faz a ativação pós-falha de todas as VMs para o último ponto de recuperação consistente com a aplicação. O carimbo de data/hora é apresentado.
   - **Personalizado**: selecione qualquer ponto de recuperação.

3. Selecione a rede virtual do Azure de destino para a qual você deseja mover as VMs do Azure para testar a configuração. 

   > [!IMPORTANT]
   > É recomendável que você use uma rede de VM do Azure separada para o failover de teste e não a rede de produção na qual você deseja mover suas VMs eventualmente que foram configuradas quando você habilitou a replicação.

4. Para começar a testar a movimentação, clique em **OK**. Para acompanhar o progresso, clique em VM para abrir as respetivas propriedades. Também pode clicar na tarefa **Ativação Pós-falha de Teste** no nome do cofre > **Definições** > **Tarefas** > **Tarefas do Site Recovery**.
5. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Certifique-se de que a VM está em execução, tem as dimensões adequadas e está ligada à rede devida.
6. Se você quiser excluir a VM criada como parte do teste da movimentação, clique em **limpar failover de teste** no item replicado. Em **observações**, registre e salve as observações associadas ao teste.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Execute a movimentação para a região de destino e confirme.

1. Navegue até o cofre, em **configurações** > **itens replicados**, clique na máquina virtual e, em seguida, clique em **failover**.
2. Em **Ativação pós-falha**, selecione **Mais recente**. 
3. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. O Site Recovery tenta encerrar a VM de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode seguir o progresso da ativação pós-falha na página **Tarefas**. 
4. Quando o trabalho for concluído, verifique se a VM aparece na região de destino do Azure conforme o esperado.
5. Em **Itens replicados**, clique com o botão direito do rato na VM > **Consolidar**. Isso conclui o processo de movimentação para a região de destino. Aguarde até que o trabalho de confirmação seja concluído.

## <a name="discard-the-resource-in-the-source-region"></a>Descartar o recurso na região de origem 

- Navegue até a VM.  Clique em **desabilitar replicação**.  Isso interrompe o processo de copiar os dados para a VM.  

   > [!IMPORTANT]
   > É importante executar essa etapa para evitar ser cobrado pela replicação ASR.

Caso você não tenha planos para reutilizar qualquer um dos recursos de origem, continue com o próximo conjunto de etapas.

1. Continue para excluir todos os recursos de rede relevantes na região de origem que você listou como parte da etapa 4 em [preparar as VMs de origem](#prepare-the-source-vms) 
2. Exclua a conta de armazenamento correspondente na região de origem.



## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você moveu uma VM do Azure para uma região diferente do Azure. Agora você pode configurar a recuperação de desastre para a VM movida.

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre após a migração](azure-to-azure-quickstart.md)
