---
title: Configurar a recuperação de desastres Hyper-V para um local secundário com recuperação do local de Azure
description: Saiba como configurar a recuperação após desastre para VMs de Hyper-V entre os sites no local com o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: how-to
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: b2164f8927e5c3224f8b07c30d057f48fb7bbc32
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87495981"
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>Configurar a recuperação após desastre para VMs Hyper-V para um site no local secundário

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para a estratégia de recuperação após desastre, através da gestão e orquestração de replicação, ativação pós-falha e reativação pós-falha de máquinas no local e máquinas virtuais (VMs) do Azure.

Este artigo mostra-lhe como configurar a recuperação após desastre para um site secundário para VMs Hyper-V no local geridas em clouds do System Center Virtual Machine Manager (VMM). Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Preparar os servidores do VMM no local e os anfitriões de Hyper-V
> * Criar um cofre dos Serviços de Recuperação para o Site Recovery 
> * Configurar os ambientes de origem e de destino da replicação 
> * Configurar o mapeamento de rede 
> * Criar uma política de replicação
> * Ativar a replicação para uma VM


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este cenário:

- Reveja a [arquitetura e os componentes do cenário](hyper-v-vmm-architecture.md).
- Confirme que os servidores do VMM e os anfitriões de Hyper-V estão em conformidade com os [requisitos de suporte](hyper-v-vmm-secondary-support-matrix.md).
- Verifique que as VMs que quer replicar estão em conformidade com o [suporte de máquinas replicadas](hyper-v-vmm-secondary-support-matrix.md#replicated-vm-support).
- Prepare os servidores do VMM para mapeamento de rede.

### <a name="prepare-for-network-mapping"></a>Preparar o mapeamento da rede

O [mapeamento de rede](hyper-v-vmm-network-mapping.md) faz o mapeamento entre as redes das VMs do VMM no local nas clouds de origem e de destino. O mapeamento faz o seguinte:

- Liga as VMs às redes de VMs de destino adequadas após a ativação pós-falha. 
- Coloca as VMs de réplica nos servidores dos anfitriões de Hyper-V de destino, de forma otimizada. 
- Se não configurar o mapeamento da rede, as réplicas de VMs não serão ligadas a uma rede VM após a falha.

Prepare o VMM da seguinte forma:

1. Confirme que tem [redes lógicas do VMM](/system-center/vmm/network-logical) nos servidores do VMM de origem e de destino.
    - A rede lógica no servidor de origem deve ser associada à cloud de origem na qual os anfitriões de Hyper-V estão localizados.
    - A rede lógica no servidor de destino deve ser associada à cloud de destino.
1. Certifique-se de que tem [redes de VMs](/system-center/vmm/network-virtual) nos servidores do VMM de origem e de destino. As redes de VMs devem ser ligadas à rede lógica em cada localização.
2. Ligue as VMs nos anfitriões de Hyper-V de origem à rede de VMs de origem. 


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Escolher um objetivo de proteção

Selecione o que pretende replicar e para onde pretende que seja replicado.

1. Clique no Passo de **Recuperação do Local**  >  **1: Prepare o** objetivo de  >  **proteção da** infraestrutura .
2. Selecione **Para site de recuperação** e selecione **Sim, com o Hyper-V**.
3. Selecione **Sim** para indicar que está a utilizar o VMM para gerir os anfitriões de Hyper-V.
4. Se tiver um servidor do VMM secundário, selecione **Sim**. Se estiver a implementar a replicação entre clouds num único servidor do VMM, clique em **Não**. Em seguida, clique em **OK**.


## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Instale o Fornecedor do Azure Site Recovery nos servidores do VMM e descubra e registe os servidores no cofre.

1. Clique **em Preparar Fonte de Infraestrutura**  >  .
2. Na **fonte preparar**, clique + **VMM** para adicionar um servidor VMM.
3. Em **Adicionar Servidor**, verifique se **Servidor do System Center VMM** aparece em **Tipo de servidor**.
4. Transfira o ficheiro de instalação do Fornecedor do Azure Site Recovery.
5. Transfira a chave de registo. Vai precisar da chave para instalar o Fornecedor. A chave é válida durante cinco dias depois de gerá-la.

    ![Screenshot das opções para descarregar Fornecedor e chave de registo.](./media/hyper-v-vmm-disaster-recovery/source-settings.png)

6. Instale o Fornecedor em cada servidor do VMM. Não é necessário instalar explicitamente nada nos anfitriões de Hyper-V.

### <a name="install-the-azure-site-recovery-provider"></a>Instalar o Fornecedor do Azure Site Recovery

1. Execute o ficheiro de configuração do Fornecedor em cada servidor do VMM. Se o VMM estiver implementado num cluster, instale pela primeira vez da seguinte forma:
    -  Instale o Fornecedor num nó ativo e conclua a instalação para registar o servidor do VMM no cofre.
    - Em seguida, instale o Fornecedor nos outros nós. Todos os nós do cluster devem executar a mesma versão do Fornecedor.
2. A configuração executa algumas verificações de pré-requisitos e pede permissão para parar o serviço VMM. O serviço VMM será reiniciado automaticamente após a conclusão da configuração. Se estiver a instalar num cluster do VMM, é-lhe pedido que pare a função Cluster.
3. Em **Microsoft Update**, pode optar por especificar que as atualizações do Fornecedor sejam instaladas de acordo com a sua política do Microsoft Update.
4. Em **Instalação**, aceite ou modifique a localização de instalação predefinida e clique em **Instalar**.
5. Quando a instalação for concluída, clique em **Registar** para registar o servidor no cofre.

    ![Screenshot do ecrã de instalação do fornecedor, incluindo o local de instalação.](./media/hyper-v-vmm-disaster-recovery/provider-register.png)
6. Em **Nome do cofre**, verifique o nome do cofre no qual o servidor será registado. Clique em **Seguinte**.
7. Em **Ligação Proxy**, especifique a forma como o Fornecedor em execução no servidor do VMM se liga à Internet.
   - Pode especificar que o Fornecedor se deve ligar diretamente à Internet ou através de um proxy. Especifique as definições do proxy, conforme necessário.
   - Se utilizar um proxy, é criada automaticamente uma conta RunAs do VMM (DRAProxyAccount) com as credenciais de proxy especificadas. Configure o servidor proxy para que esta conta possa autenticar-se com êxito. As definições da conta RunAs podem ser modificadas na consola VMM > **Configurações**  >  **De Segurança** Executar como  >  **contas**.
   - Reinicie o serviço VMM para atualizar as alterações.
8. Em **Chave de Registo**, selecione a chave que transferiu e copiou para o servidor do VMM.
9. A definição de encriptação não é relevante para este cenário. 
10. Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Num cluster, especifique o nome da função de cluster do VMM.
11. Em **Sincronizar metadados da cloud**, selecione se pretende sincronizar os metadados de todas as clouds no servidor do VMM. Esta ação só deverá ocorrer uma vez em cada servidor. Se não quiser sincronizar todas as clouds, deixe esta definição desmarcada. Pode sincronizar cada cloud individualmente nas propriedades da cloud na consola do VMM.
12. Clique em **Seguinte** para concluir o processo. Após o registo, o Site Recovery obtém os metadados do servidor do VMM. O servidor é apresentado nos  >  **Servidores VMM dos Servidores do** Servidor no cofre.
13. Depois de o servidor aparecer no cofre, na fonte De preparação **de origem**  >   selecione o servidor VMM e selecione a nuvem na qual o anfitrião Hyper-V está localizado. Em seguida, clique em **OK**.


## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione o servidor do VMM e a cloud de destino:

1. Clique **em Preparar a infraestrutura**  >  **Target** e selecione o servidor VMM alvo.
2. São apresentadas as clouds do VMM que estão sincronizadas com o Site Recovery. Selecione a cloud de destino.

   ![Screenshot das seleções do VMM Server e Cloud alvo.](./media/hyper-v-vmm-disaster-recovery/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

Antes de começar, confirme que todos os anfitriões que utilizam a política têm o mesmo sistema operativo. Se os anfitriões estiverem a executar versões diferentes do Windows Server, precisa de várias políticas de replicação.

1. Para criar uma nova política de replicação, clique em **Preparar**  >  **Definições de Replicação de** Infraestrutura  >  **+Criar e associar.**
2. Na **política de criar e associar,** especifique um nome de política. O tipo de origem e de destino deve ser **Hyper-V**.
3. Em **Versão do anfitrião de Hyper-V**, selecione o sistema operativo que está a ser executado no anfitrião.
4. Em **Tipo de autenticação** e **Porta de autenticação**, especifique de que forma é que o tráfego é autenticado entre os servidores do anfitrião de Hyper-V principal e de recuperação.
    - Selecione **Certificado**, a menos que tenha um ambiente do Kerberos em funcionamento. O Azure Site Recovery configura automaticamente os certificados para autenticação HTTPS. Não é necessário fazer nada manualmente.
    - Por predefinição, as portas 8083 e 8084 (para os certificados) estarão abertas no Windows Firewall nos servidores do anfitrião de Hyper-V.
    - Se selecionar **Kerberos**, será utilizado um ticket do Kerberos para autenticação mútua dos servidores do anfitrião. O Kerberos só é relevante para os servidores do anfitrião de Hyper-V no Windows Server 2012 R2 ou posterior.
1. Na **frequência Copy**, especifique com que frequência pretende replicar dados delta após a replicação inicial (a cada 30 segundos, 5 ou 15 minutos).
2. Em **Retenção do ponto de recuperação**, especifique (em horas) qual será a duração da janela de retenção para cada ponto de recuperação. As máquinas replicadas podem ser recuperadas para qualquer ponto nessa janela.
3. Em **Frequência de instantâneos consistentes com a aplicação**, especifique a frequência (1 a 12 horas) com que os pontos de recuperação que contêm os instantâneos consistentes com aplicações são criados. O Hyper-V utiliza dois tipos de instantâneos:
    - **Instantâneo padrão**: disponibiliza um instantâneo incremental de toda a máquina virtual.
    - **Instantâneo consistente com a aplicação**: cria um instantâneo de ponto no tempo dos dados da aplicação dentro da VM. O Serviço de Cópia Sombra de Volumes (VSS) garante que as aplicações estão num estado consistente quando se obtém o instantâneo. A ativação dos instantâneos consistentes com a aplicação afeta o desempenho das aplicações nas VMs de origem. Defina um número menor do que o número de pontos de recuperação adicionais que está a configurar.
4. Em **Compressão da transferência de dados**, especifique se os dados de replicação transferidos devem ser comprimidos.
5. Selecione **Eliminar VM de réplica** para especificar que a máquina virtual de réplica deve ser eliminada caso desative a proteção na VM de origem. Se ativar esta definição, quando desativar a proteção na VM de origem, esta é removida da consola do Site Recovery, as definições do Site Recovery para o VMM são removidas da consola do VMM e a réplica é eliminada.
6. Em **Método de replicação inicial**, se estiver a replicar através da rede, especifique se a replicação inicial deve ser iniciada ou agendada. Para poupar largura de banda, pode considerar agendá-la para fora do seu horário mais preenchido. Em seguida, clique em **OK**.

     ![Screenshot das opções de política de replicação.](./media/hyper-v-vmm-disaster-recovery/replication-policy.png)
     
7. A política nova é associada automaticamente à cloud do VMM. Em **Política de replicação**, clique em **OK**. 


## <a name="enable-replication"></a>Ativar a replicação

1. Clique **em Replicar a aplicação**  >  **Fonte**. 
2. Em **Origem**, selecione o servidor do VMM e a cloud na qual estão localizados os anfitriões de Hyper-V que pretende replicar. Em seguida, clique em **OK**.
3. Em **Destino**, verifique o servidor e a cloud do VMM secundários.
4. Em **Máquinas Virtuais**, selecione as VMs que pretende proteger a partir da lista.


Pode controlar o progresso da ação **Ativar Proteção** em **Tarefas** > **Tarefas do Site Recovery**. Após a conclusão do trabalho **Finalizar Proteção**, a replicação inicial estará concluída e a VM estará preparada para a ativação pós-falha.

## <a name="next-steps"></a>Passos seguintes

[Executar um teste de recuperação após desastre](hyper-v-vmm-test-failover.md)
