---
title: Move VMs Azure entre governo e regiões públicas com recuperação do site Azure
description: Use a Recuperação do Sítio Azure para mover VMs Azure entre o governo de Azure e as regiões públicas.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: acaf16e7469b3ea4e5e391db91e37dc76be3b261
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78298535"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Mover VMs do Azure entre regiões públicas e o Azure Government 

É melhor transferir os seus VMs IaaS entre o Governo de Azure e as regiões públicas para aumentar a disponibilidade dos seus VMs existentes, melhorar a gestão, ou por razões de governação, conforme descrito [aqui.](azure-to-azure-move-overview.md)

Além de utilizar o serviço de recuperação de [sítios azure](site-recovery-overview.md) para gerir e orquestrar a recuperação de desastres de máquinas no local e VMs Azure para efeitos de continuidade de negócios e recuperação de desastres (BCDR), também pode usar a Recuperação do Site para gerir os VMs Azure para uma região secundária.       

Este tutorial mostra-lhe como mover VMs Azure entre o Governo azure e as regiões públicas usando a Recuperação do Sítio Azure. O mesmo pode ser alargado para mover VMs entre pares de região que não estão dentro do mesmo aglomerado geográfico. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verificar os pré-requisitos
> * Prepare as VMs de origem
> * Preparar a região alvo
> * Copiar dados para a região alvo
> * Testar a configuração
> * Execute o movimento
> * Descartar os recursos na região nascente

> [!IMPORTANT]
> Este tutorial mostra-lhe como mover VMs Azure entre o Governo azure e as regiões públicas, ou entre os pares de regiões que não são apoiados pela solução regular de recuperação de desastres para os VMs Azure. No caso de os pares de fontes e regiões-alvo serem [suportados,](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)consulte este [documento](azure-to-azure-tutorial-migrate.md) para a mudança. Se o seu requisito é melhorar a disponibilidade movendo VMs em um conjunto de disponibilidade para VMs de zona fixada em uma região diferente, consulte o tutorial [aqui](move-azure-VMs-AVset-Azone.md).

> [!IMPORTANT]
> Não é aconselhável utilizar este método para configurar o DR entre pares de região não suportados, uma vez que os pares são definidos mantendo em mente a latência de dados, o que é fundamental para um cenário de DR.

## <a name="verify-prerequisites"></a>Verificar os pré-requisitos

> [!NOTE]
> Certifique-se de que compreende a [arquitetura e os componentes](physical-azure-architecture.md) para este cenário. Esta arquitetura será usada para mover VMs Azure, **tratando os VMs como servidores físicos.**

- Reveja os [requisitos de suporte](vmware-physical-secondary-support-matrix.md) de todos os componentes.
- Certifique-se de que os servidores que pretende replicar cumprem os [requisitos do Azure VM](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Prepare uma conta para a instalação automática do serviço mobility em cada servidor que pretende replicar.

- Note que depois de falhar na região alvo em Azure, não pode realizar diretamente uma falha de volta à região de origem. Terá de voltar a instalar a replicação para o alvo.

### <a name="verify-azure-account-permissions"></a>Verifique as permissões da conta Azure

Certifique-se de que a sua conta Azure tem permissões para a replicação de VMs para o Azure.

- Reveja as [permissões necessárias](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para replicar as máquinas para o Azure.
- Verifique e modifique as permissões [de acesso baseadas em papéis.](../role-based-access-control/role-assignments-portal.md) 

### <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Criar uma [rede azure](../virtual-network/quick-create-portal.md)alvo.

- Os VMs azure são colocados nesta rede quando são criados após a falha.
- A rede deve estar na mesma região que o cofre dos Serviços de Recuperação


### <a name="set-up-an-azure-storage-account"></a>Configurar uma conta de armazenamento do Azure

Criar uma conta de [armazenamento Azure.](../storage/common/storage-account-create.md)

- A Recuperação do Local replica máquinas no local para o armazenamento azure. Os VMs azure são criados a partir do armazenamento após a falha ocorrer.
- A conta de armazenamento tem de estar na mesma região que o cofre dos Serviços de Recuperação.


## <a name="prepare-the-source-vms"></a>Prepare as VMs de origem

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de Mobilidade deve ser instalado em cada servidor que pretende replicar. A Recuperação do Site instala este serviço automaticamente quando ativa a replicação para o servidor. Para instalar automaticamente, é necessário preparar uma conta que a Recuperação do Site utilizará para aceder ao servidor.

- Você pode usar um domínio ou conta local
- Para os VMs do Windows, se não estiver a utilizar uma conta de domínio, desative o controlo de acesso remoto ao utilizador na máquina local. Para tal, no registo em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System,** adicione a entrada DWORD **LocalAccountTokenFilterPolicy,** com um valor de 1.
- Para adicionar a entrada de registo para desativar a definição de um CLI, escreva:``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Para o Linux, a conta deve estar na raiz do servidor Linux fonte.


## <a name="prepare-the-target-region"></a>Preparar a região alvo

1. Certifique-se de que a sua subscrição do Azure permite criar VMs na região de destino utilizada para recuperação após desastre. Contacte o suporte para ativar a quota necessária.

2. Certifique-se de que a sua subscrição tem recursos suficientes para suportar as VMs com tamanhos que correspondem às VMs de origem. se estiver a utilizar a Recuperação do Site para copiar dados para o alvo, ele escolhe o mesmo tamanho ou o tamanho mais próximo possível para o VM alvo.

3. Certifique-se de que cria um recurso-alvo para todos os componentes identificados no layout de rede de origem. Isto é importante para garantir que, cortando para a região alvo, os seus VMs têm todas as funcionalidades e funcionalidades que você tinha na fonte.

    > [!NOTE]
    > A Recuperação do Site Azure descobre e cria automaticamente uma rede virtual quando ativa a replicação para o VM de origem, ou também pode pré-criar uma rede e atribuir ao VM no fluxo do utilizador para ativar a replicação. Mas para quaisquer outros recursos, como mencionado abaixo, você precisa criá-los manualmente na região alvo.

     Consulte os seguintes documentos para criar os recursos de rede mais utilizados para si, com base na configuração vm de origem.

    - [Grupos de Segurança da Rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Balanceadores de carga](https://docs.microsoft.com/azure/load-balancer)
    - [IP público](../virtual-network/virtual-network-public-ip-address.md)
    
    Para quaisquer outros componentes de rede, consulte a [documentação](https://docs.microsoft.com/azure/?pivot=products&panel=network)de rede .

4. [Crie](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) manualmente uma rede de não produção na região alvo se pretender testar a configuração antes de executar o corte final para a região alvo. Isto criará uma interferência mínima na produção e é recomendado.

## <a name="copy-data-to-the-target-region"></a>Copiar dados para a região alvo
Os passos abaixo irão guiá-lo como usar a Recuperação do Site Azure para copiar dados para a região alvo.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Crie o cofre em qualquer região, exceto na região de origem.

1. Inscreva-se nos Serviços de > **Recuperação**do [portal Azure.](https://portal.azure.com)
2. Clique em Criar uma cópia de segurança**e recuperação**de**ferramentas** > de gestão de **recursos.** > 
3. No **Nome**, especifique o nome amigável **ContosoVMVault**. Se tiver mais de um por. subscrição, selecione a apropriada.
4. Crie um grupo de recursos **ContosoRG**.
5. Selecione uma região do Azure. Para verificar as regiões suportadas, veja a disponibilidade geográfica em [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Nos cofres dos Serviços de Recuperação, clique em **Overview** > **ConsotoVMVault** > **+Replicate**
7. Selecione **Para Azure** > **Não virtualizado/Outros**.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Configurar o servidor de configuração para descobrir VMs.


Instale o servidor de configuração, registe-o no cofre e descubra os VMs.

1. Clique em **recuperação** > do site Preparar**Fonte de****Infraestrutura** > .
2. Se não tiver um servidor de configuração, clique em **+Configuração do servidor**.
3. No **Servidor adicionar,** verifique se o Servidor de **Configuração** aparece no **tipo servidor**.
4. Descarregue o ficheiro de instalação unificada de recuperação do site.
5. Transfira a chave de registo do cofre. Precisas disto quando executas a Configuração Unificada. A chave é válida durante cinco dias depois de gerá-la.

   ![Configurar a origem](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registe o servidor de configuração no cofre

Faça o seguinte antes de começar: 

#### <a name="verify-time-accuracy"></a>Verifique a precisão do tempo
Na máquina do servidor de configuração, certifique-se de que o relógio do sistema está sincronizado com um Servidor de [Tempo](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Deve coincidir. Se estiverem 15 minutos à frente ou atrás, a configuração pode falhar.

#### <a name="verify-connectivity"></a>Verifique a conectividade
Certifique-se de que a máquina pode aceder a estes URLs com base no seu ambiente: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

As regras de firewall baseadas em endereçoip devem permitir a comunicação a todos os URLs Azure que estão listados acima sobre a porta HTTPS (443). Para simplificar e limitar as gamas IP, recomenda-se que a filtragem de URL seja feita.

- **IPs comerciais** - Permitir o [Azure Datacenter IP Ranges,](https://www.microsoft.com/download/confirmation.aspx?id=41653)e a porta HTTPS (443). Permita intervalos de endereçoip para a região Azure da sua subscrição para suportar os URLs AAD, Backup, Replication e Storage.  
- **IPs do governo** - Permitir que o [Datacenter IP Ranges do Governo Azure,](https://www.microsoft.com/en-us/download/details.aspx?id=57063)e o porto HTTPS (443) para todas as regiões USGov (Virgínia, Texas, Arizona e Iowa) apoiem os URLs AAD, Backup, Replication e Storage.  

#### <a name="run-setup"></a>Executar a configuração
Executar configuração unificada como administrador local, para instalar o servidor de configuração. O servidor de processos e o servidor de alvo principal também são instalados por padrão no servidor de configuração.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Após o registo terminar, o servidor de configuração é apresentado na página **'Servidores** de **Definições'** > no cofre.

### <a name="configure-target-settings-for-replication"></a>Configurar as definições de alvo para a replicação

Selecione e verifique os recursos de destino.

1. Clique em Preparar**o Target** **de Infraestruturas** > e selecione a subscrição Azure que pretende utilizar.
2. Especifique o modelo de implementação do alvo.
3. A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

   ![Destino](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Para criar uma nova política de replicação, clique em Políticas > de Replicação de **Infraestruturas** > de**Recuperação**do Site **+Política de Replicação**.
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limiar RPO**, especifique o limite do objetivo de ponto de recuperação (RPO). Este valor especifica a frequência com que os pontos de recuperação de dados são criados. Será gerado um alerta se a replicação contínua exceder este limite.
4. Em **Retenção do ponto de recuperação**, especifique (em horas) a duração da janela de retenção para cada ponto de recuperação. As VMs replicadas podem ser recuperadas para qualquer ponto numa janela. É suportada uma retenção de até 24 horas para máquinas replicadas para o armazenamento premium e até 72 horas para armazenamento standard.
5. Na frequência instantânea consistente com **aplicações,** especifique com que frequência (em minutos) serão criados pontos de recuperação contendo instantâneos consistentes com aplicações. Clique em **OK** para criar a política.

    ![Política de replicação](./media/physical-azure-disaster-recovery/replication-policy.png)


A política é associada automaticamente ao servidor de configuração. Por predefinição, uma política correspondente é criada automaticamente para reativação pós-falha. Por exemplo, se a política de replicação for a **política de rep-policy,** então é criada uma política de **retrocesso- failback da política de failback.** Esta política não é utilizada depois de iniciar uma reativação pós-falha a partir do Azure.

### <a name="enable-replication"></a>Ativar a replicação

- A Recuperação do Site instalará o serviço de Mobilidade quando a replicação estiver ativada.
- Quando ativa a replicação de um servidor, pode demorar 15 minutos ou mais para que as alterações produzam efeito e apareçam no portal.

1. Clique em **Replicate aplicação** > **Origem**.
2. Em **Origem**, selecione o servidor de configuração.
3. No **tipo máquina,** selecione **Máquinas Físicas**.
4. Selecione o servidor de processo (o servidor de configuração). Em seguida, clique em **OK**.
5. No **Target,** selecione a subscrição e o grupo de recursos em que pretende criar os VMs Azure após a falha. Escolha o modelo de implementação que pretende utilizar no Azure (gestão clássica ou de recursos).
6. Selecione a conta de armazenamento do Azure que pretende utilizar para replicar os dados. 
7. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se vão ligar quando forem criadas após a ativação pós-falha.
8. **Selecione configurar agora para máquinas selecionadas,** para aplicar a definição de rede em todas as máquinas selecionadas para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por máquina. 
9. Em **Máquinas Físicas**, e clique em **+Máquina Física**. Especifique o nome e o endereço IP. Selecione o sistema operativo da máquina que pretende replicar. Leva alguns minutos para os servidores serem descobertos e listados. 

   > [!WARNING]
   > Você precisa inserir o endereço IP do Azure VM que pretende mover

10. Em **propriedades** > **Configurar propriedades,** selecione a conta que será usada pelo servidor de processo para instalar automaticamente o serviço mobility na máquina.
11. Nas **definições** > de replicação**Configure as definições de replicação,** verifique se a política de replicação correta é selecionada. 
12. Clique na **replicação de ativação**. Pode acompanhar o progresso do trabalho de **Proteção ativa** em **Cenários** > **Empregos** > de Recuperação de**Locais**. Após o trabalho de **Proteção final,** a máquina está pronta para a falha.


Para monitorizar os servidores que adiciona, pode verificar a última hora descoberta para eles em **Servidores** > de Configuração**Último Contacto Em**. Para adicionar máquinas sem esperar por um tempo de descoberta programado, realce o servidor de configuração (não clique nele) e clique em **Refresh**.

## <a name="test-the-configuration"></a>Testar a configuração


1. Navegue para o cofre, em**itens replicados**de **Definições,** > clique na máquina Virtual que pretende mover-se para a região alvo, clique no ícone **+Failover** do teste.
2. Em **Ativação Pós-falha**, selecione um ponto de recuperação para utilizar na ativação pós-falha:

   - **Processado mais recentemente**: faz a ativação pós-falha da VM para o ponto de recuperação mais recente processado pelo serviço do Site Recovery. O carimbo de data/hora é apresentado. Com esta opção, não é despendido tempo a processar os dados, pelo que oferece um RTO (Objetivo de Tempo de Recuperação) baixo
   - **Consistente com a aplicação mais recente**: esta opção faz a ativação pós-falha de todas as VMs para o último ponto de recuperação consistente com a aplicação. O carimbo de data/hora é apresentado.
   - **Personalizado**: selecione qualquer ponto de recuperação.

3. Selecione a rede virtual Target Azure para a qual pretende mover os VMs Azure para testar a configuração. 

   > [!IMPORTANT]
   > Recomendamos que utilize uma rede Azure VM separada para a falha do teste e não a rede de produção para a qual pretende mover os seus VMs eventualmente que foram configurados quando permitiu a replicação.

4. Para começar a testar o movimento, clique em **OK**. Para acompanhar o progresso, clique em VM para abrir as respetivas propriedades. Também pode clicar na tarefa **Ativação Pós-falha de Teste** no nome do cofre > **Definições** > **Tarefas** > **Tarefas do Site Recovery**.
5. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Certifique-se de que a VM está em execução, tem as dimensões adequadas e está ligada à rede devida.
6. Se desejar eliminar o VM criado como parte do teste do movimento, clique em falha no teste de **limpeza** no item replicado. Em **Notas,** registe e guarde quaisquer observações associadas ao teste.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Execute a mudança para a região alvo e confirme.

1. Navegue para o cofre, em**itens replicados**de **Definições,** > clique na máquina virtual e clique em **Failover**.
2. Em **Ativação pós-falha**, selecione **Mais recente**. 
3. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. O Site Recovery tenta encerrar a VM de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode acompanhar o progresso da falha na página **Jobs.** 
4. Uma vez concluído o trabalho, verifique se o VM aparece na região-alvo de Azure, como esperado.
5. Em **Itens replicados**, clique com o botão direito do rato na VM > **Consolidar**. Isto termina o processo de mudança para a região alvo. Espere até o trabalho de compromisso terminar.

## <a name="discard-the-resource-in-the-source-region"></a>Descartar o recurso na região nascente 

- Navegue para o VM.  Clique na **replicação de desativação**.  Isto interrompe o processo de cópia dos dados para o VM.  

   > [!IMPORTANT]
   > É importante realizar este passo para evitar ser cobrado para a replicação da ASR.

Caso não tenha planos para reutilizar nenhum dos recursos de origem, proceda ao próximo conjunto de passos.

1. Proceda à eliminação de todos os recursos de rede relevantes na região de origem que enumerou como parte do Passo 4 em [Preparar as VMs de origem](#prepare-the-source-vms) 
2. Eliminar a conta de armazenamento correspondente na região fonte.



## <a name="next-steps"></a>Passos seguintes

Neste tutorial mudou um VM Azure para uma região azure diferente. Agora pode configurar a recuperação de desastres para o VM em movido.

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre após a migração](azure-to-azure-quickstart.md)
