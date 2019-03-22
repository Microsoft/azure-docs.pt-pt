---
title: Fazer a ativação pós-falha e a reativação pós-falha de VMs do VMware e de servidores físicos durante a recuperação após desastre para o Azure com o Site Recovery | Microsoft Docs
description: Saiba como fazer a ativação pós-falha de VMs do VMware e de servidores físicos para o Azure e a reativação pós-falha para o site no local durante a recuperação após desastre para o Azure com o Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 68f1c5156f4c12af33e6088d862fc12d98021fd4
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310222"
---
# <a name="fail-over-and-fail-back-vmware-vms-and-physical-servers-replicated-to-azure"></a>Fazer a ativação pós-falha e a reativação pós-falha de VMs do VMware e de servidores físicos replicados no Azure

Este tutorial descreve como fazer a ativação pós-falha de uma VM do VMware para o Azure. Depois de feita a ativação pós-falha, vai fazer a reativação pós-falha para o seu site no local quando estiver disponível. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verificar as propriedades da VM do VMware para ver a conformidade com os requisitos do Azure
> * Executar uma ativação pós-falha para o Azure
> * Criar um servidor de processos e um servidor de destino mestre para a reativação pós-falha
> * Voltar a proteger as VMs do Azure para o site no local
> * Fazer a ativação pós-falha do Azure o local
> * Voltar a proteger VMs no local, para iniciar a replicação para o Azure novamente

>[!NOTE]
>Os tutoriais são concebidos para mostrar o caminho de implementação mais simples num cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Se pretender saber mais sobre os passos de ativação pós-falha de teste, leia o [Guia de Procedimentos](site-recovery-failover.md).

Este é o quinto tutorial de uma série. Este tutorial parte do princípio de que já concluiu as tarefas nos tutoriais anteriores.

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar o VMware no local](vmware-azure-tutorial-prepare-on-premises.md)
3. [Configurar a recuperação após desastre](vmware-azure-tutorial.md)
4. [Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md)
5. Além dos passos acima, é útil [rever a arquitetura](vmware-azure-architecture.md) do cenário de recuperação após desastre.

## <a name="failover-and-failback"></a>Ativação pós-falha e reativação pós-falha

A ativação pós-falha e a reativação pós-falha têm quatro fases:

1. **Efetuar a ativação pós-falha para o Azure**: Pós-falha de máquinas do site no local para o Azure.
2. **Voltar a proteger VMs do Azure**: Voltar a proteger as VMs do Azure, para que comecem a ser replicadas novamente para as VMs do VMware no local. A VM no local é desativada durante a nova proteção. Desta forma, garante-se a consistência dos dados durante a replicação.
3. **Efetuar a ativação pós-falha no local**: Execute uma ativação pós-falha, reativação pós-falha do Azure.
4. **Voltar a proteger VMs no local**: Depois de dados falhou, novamente, voltar a proteger as VMs no local que falharam ao, para que comecem a ser replicadas para o Azure.

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Verifique as propriedades da VM e certifique-se de que a VM está em conformidade com [requisitos do Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Em **Itens Protegidos**, clique em **Itens Replicados** > VM.

2. No painel **Item replicado**, existe um resumo das informações, do estado de funcionamento e dos pontos de recuperação mais recentes disponíveis da VM. Clique em **Propriedades** para ver mais detalhes.

3. Na **computação e rede**, pode modificar o nome do Azure, o grupo de recursos, o tamanho de destino, [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md)e as definições de disco gerido

4. Pode ver e modificar as definições de rede, incluindo a rede/sub-rede na qual a VM do Azure será localizada após a ativação pós-falha e o endereço IP que será atribuído à mesma.

5. Em **Discos**, pode ver informações sobre o sistema operativo e os discos de dados na VM.

## <a name="run-a-failover-to-azure"></a>Executar uma ativação pós-falha para o Azure

1. Em **Definições** > **Itens replicados** clique na VM > **Ativação Pós-falha**.

2. Em **Ativação pós-falha**, selecione um **Ponto de Recuperação** para o qual irá realizar a ativação pós-falha. Pode utilizar uma das opções seguintes:
   - **Mais recente**: Esta opção processa primeiro todos os dados enviados para o Site Recovery. Disponibiliza o último RPO (Objetivo de Ponto de Recuperação), porque a VM do Azure criada após a ativação pós-falha tem todos os dados que foram replicados para o Site Recovery quando a ativação pós-falha foi acionada.
   - **Processado mais recentemente**: Esta opção faz a ativação pós-falha da VM do ponto de recuperação mais recente processado pelo Site Recovery. Esta opção proporciona um RTO (Objetivo de Tempo de Recuperação) baixo, porque não é despendido tempo ao processar os dados não processados.
   - **Mais recente consistente com a aplicação**: Esta opção faz a ativação pós-falha da VM para o ponto mais recente recuperação consistente com a aplicação processado pelo Site Recovery.
   - **Custom**: Especifique um ponto de recuperação.

3. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha** para tentar encerrar as máquinas virtuais de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode seguir o progresso da ativação pós-falha na página **Trabalhos**.

Em alguns cenários, a ativação pós-falha requer processamento adicional, que demora cerca de oito a dez minutos a concluir. Pode observar **tempos mais longos de ativação pós-falha de teste** para máquinas virtuais de VMware com o serviço de mobilidade na versão anterior à 9.8, servidores físicos, máquinas virtuais do Linux de VMware, máquinas virtuais de Hyper-V protegidas como servidores físicos, VMs de VMware que não têm o serviço DHCP ativado e VMs de VMware que não têm os seguintes controladores de arranque: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> **Não cancelar uma ativação pós-falha em curso**: Antes de iniciar a ativação pós-falha, a replicação de VM é interrompida.
> Se cancelar uma ativação pós-falha que esteja em curso, a mesma para, mas a VM não será replicada outra vez.

## <a name="connect-to-failed-over-virtual-machine-in-azure"></a>Ligar a máquina virtual com ativação pós-falha no Azure

1. Se quiser ligar a VMs do Azure através de RDP/SSH após a ativação pós-falha, siga os requisitos resumidos na tabela [aqui](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. Após a ativação pós-falha, vá para a máquina virtual e valide ao [ligar](../virtual-machines/windows/connect-logon.md) à mesma.
3. Após a validação, clique em **Consolidar** para finalizar o ponto de recuperação da máquina virtual após a ativação pós-falha. Após a consolidação, todos os outros pontos de recuperação disponíveis são eliminados. Este passo conclui a atividade de ativação pós-falha.

>[!TIP]
> **Alterar o ponto de recuperação** ajuda-o a escolher um ponto de recuperação diferente após a ativação pós-falha, se não estiver satisfeito com a máquina virtual com ativação pós-falha. Após a **consolidação**, esta opção já não estará disponível.

Siga os passos descritos [aqui](site-recovery-failover-to-azure-troubleshoot.md) para resolver quaisquer problemas de conectividade após a ativação pós-falha.

## <a name="preparing-for-reprotection-of-azure-vm"></a>Preparação para a nova proteção da VM do Azure

- Pode utilizar o servidor de processos no local (servidor de processos incorporado) que é instalado automaticamente no servidor de configuração, como parte da configuração **se tiver uma ligação do Azure ExpressRoute**.

> [!IMPORTANT]
> Se tiver uma ligação VPN entre o ambiente no local e o Azure, tem de configurar uma VM do Azure como um servidor de processos de reproteção e reativação pós-falha. Para configurar um servidor de processos no Azure, siga as instruções [neste artigo](vmware-azure-set-up-process-server-azure.md).

Para obter mais informações sobre os pré-requisitos de reproteção e reativação pós-falha Consulte este [secção](vmware-azure-reprotect.md##before-you-begin). 

### <a name="configure-the-master-target-server"></a>Configurar o servidor de destino mestre

Um servidor de destino principal recebe e processa os dados de replicação durante a reativação pós-falha a partir do Azure. Por predefinição, está disponível no servidor de configuração no local. Neste tutorial, vamos utilizar o servidor de destino principal predefinido.

>[!NOTE]
>Proteger uma máquina virtual baseada no Linux requer a criação de um Servidor de Destino Principal separado. [Clique aqui](vmware-azure-install-linux-master-target.md) para saber mais.

Se a VM estiver num **anfitrião ESXi que é gerido por um vCenter Server**, o servidor de destino principal tem de ter acesso ao arquivo de dados da VM (VMDK), para escrever os dados replicados nos discos da VM. Certifique-se de que o arquivo de dados da VM está montado no anfitrião do servidor de destino mestre, com acesso de leitura/escrita.

Se a VM estiver num **ESXi que não seja gerido por um vCenter Server**, o serviço Site Recovery cria uma VM nova durante a nova proteção. A VM é criada no anfitrião ESX no qual criou o servidor de destino principal.
O disco rígido da VM tem de estar num arquivo de dados que seja acessível pelo anfitrião no qual o servidor de destino principal está em execução.

Se a VM **não utilizar o vCenter**, deve concluir a deteção do anfitrião no qual o servidor de destino principal está em execução, antes de poder voltar a proteger a máquina. Isto também se aplica à reativação pós-falha de servidores físicos. Se a VM no local existir, outra opção é eliminá-la antes de fazer uma reativação pós-falha. A reativação pós-falha cria, em seguida, uma VM nova no mesmo anfitrião do anfitrião ESX do servidor de destino mestre. Quando fizer a reativação pós-falha para uma localização alternativa, os dados são recuperados para o mesmo arquivo de dados e para o mesmo anfitrião ESX que foi utilizado pelo servidor de destino mestre no local.

Não pode utilizar o Storage vMotion no servidor de destino mestre. Se o fizer, a reativação pós-falha não funcionará, porque os discos não estão disponíveis para a mesma. Exclua os servidores de destino mestre da sua lista do vMotion.

>[!Warning]
>Se utilizar um servidor de destino mestre diferente para voltar a proteger um grupo de replicação, o servidor não pode fornecer um ponto no tempo comum.

## <a name="reprotect-azure-vms"></a>Voltar a proteger VMs do Azure

Proteger novamente a VM do Azure leva à replicação de dados de sessão na VM no local. Este é um passo obrigatório antes de efetuar a ativação pós-falha do Azure para a VM no local. Siga as instruções fornecidas abaixo para executar a nova proteção.

1. Em **Definições** > **Itens replicados**, clique com o botão direito do rato na VM na qual foi feita a ativação pós-falha > **Voltar a proteger**.
2. Em **Voltar a proteger**, confirme que está selecionado **Do Azure para o local**.
3. Especifique o servidor de destino mestre no local e o servidor de processos.
4. Em **Arquivo de dados**, selecione o arquivo de dados de destino mestre para o qual pretende recuperar os discos no local. Se a VM tiver sido eliminada, são criados novos discos neste arquivo de dados. Esta definição é ignorada se os discos já existirem, mas tem de especificar um valor.
5. Selecione a unidade de retenção de destino mestre. A política de reativação pós-falha é selecionada automaticamente.
6. Clique em **OK** para começar a nova proteção. Um trabalho começa a replicar a máquina virtual do Azure para o site no local. Pode acompanhar o progresso no separador **Trabalhos**.
7. Depois de o estado da VM em **Itens replicados** mudar para **Protegido**, a máquina está preparada para a ativação pós-falha no local.

> [!NOTE]
> A VM do Azure pode ser recuperada para uma VM existente no local ou para uma localização alternativa. Leia [este artigo](concepts-types-of-failback.md) para saber mais.

## <a name="run-a-failover-from-azure-to-on-premises"></a>Executar uma ativação pós-falha do Azure para o local

Para replicar novamente para o local, é utilizada uma política de reativação pós-falha. Esta política é criada automaticamente quando criou uma política de replicação para replicação para o Azure:

- A política é associada automaticamente ao servidor de configuração.
- A política não pode ser modificada.
- Os valores da política são:
    - Limiar de RPO = 15 minutos
    - Retenção de ponto de recuperação = 24 horas
    - Frequência de instantâneos consistentes com a aplicação = 60 minutos

Execute a ativação pós-falha da seguinte forma:

1. Na página **Itens Replicados**, clique com o botão direito do rato na máquina > **Ativação pós-falha**.
2. Em **Confirmar Ativação Pós-falha**, confirme que a direção da ativação pós-falha provém do Azure.
    ![direção da ativação pós-falha](media/vmware-azure-tutorial-failover-failback/failover-direction.PNG)
3. Selecione o ponto de recuperação que pretende utilizar para a ativação pós-falha. Um ponto de recuperação consistente com a aplicação ocorre antes do ponto no tempo mais recente, o que provoca alguma perda de dados.

    >[!WARNING]
    >Quando a ativação pós-falha é executada, o Site Recovery encerra as VMs do Azure e arranca a VM no local. Haverá algum tempo de inatividade, pelo que deve escolher uma hora adequada.

4. O progresso da tarefa pode ser acompanhado no **Cofre dos Serviços de Recuperação** > **Monitorização e Relatórios** > **Tarefas do Site Recovery**.
5. Após a conclusão da ativação pós-falha, clique na máquina virtual com o botão direito do rato e clique em **Consolidar**. É acionado um trabalho que remove as VMs do Azure.
6. Confirme que as VMs do Azure foram encerradas, conforme esperado.

## <a name="reprotect-on-premises-machines-to-azure"></a>Voltar a proteger máquinas no local para o Azure

Os dados devem estar agora outra vez no site no local, mas não estão a ser replicados para o Azure. Pode iniciar a replicação para o Azure novamente da seguinte forma:

1. No cofre > **Itens protegidos** >**Itens Replicados**, selecione a VM na qual foi realizada a reativação pós-falha e clique em **Voltar a Proteger**.
2. Selecione o servidor de processos que vai ser utilizado para enviar os dados replicados para o Azure e clique em **OK**.

Após a conclusão da nova proteção, a VM é replicada outra vez para o Azure e pode executar uma ativação pós-falha, conforme necessário.
