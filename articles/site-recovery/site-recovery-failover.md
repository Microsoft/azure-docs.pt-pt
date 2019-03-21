---
title: Ativação pós-falha durante a recuperação após desastre com o Azure Site Recovery | Documentos da Microsoft
description: Saiba como realizar a ativação pós-falha de VMs e servidores físicos durante a recuperação após desastre com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 1/18/2019
ms.author: mayg
ms.openlocfilehash: 8f76d4e54133e4e899e707e666703a67310e8702
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082097"
---
# <a name="fail-over-vms-and-physical-servers"></a>Efetuar a ativação pós-falha de VMs e servidores físicos 

Este artigo descreve como máquinas de virtuais de ativação pós-falha e físico servidores protegidos pelo Site Recovery.

## <a name="prerequisites"></a>Pré-requisitos
1. Antes de efetuar uma ativação pós-falha, faça uma [ativação pós-falha de teste](site-recovery-test-failover-to-azure.md) para se certificar de que está tudo a funcionar conforme esperado.
1. [Preparar a rede](site-recovery-network-design.md) na localização de destino antes de efetuar uma ativação pós-falha.  

Utilize a tabela seguinte para saber sobre as opções de ativação pós-falha fornecidas pelo Azure Site Recovery. Estas opções também estão listadas para cenários de ativação pós-falha diferente.

| Cenário | Requisito de recuperação do aplicativo | Fluxo de trabalho para o Hyper-V | Fluxo de trabalho do VMware
|---|--|--|--|
|Ativação pós-falha planeada devido a um período de indisponibilidade de datacenter de futuros| Zero perda de dados para a aplicação quando é efetuada uma atividade em planeadas| Para o Hyper-V, o ASR replica os dados com uma frequência de cópia que é especificado pelo utilizador. Ativação pós-falha planeada é utilizada para substituir a frequência e replicar as alterações finais antes de uma ativação pós-falha é iniciada. <br/> <br/> 1.    Planear uma janela de manutenção de acordo com o processo de gestão de alteração da sua empresa. <br/><br/> 2. notificar os utilizadores do período de indisponibilidade futura. <br/><br/> 3. Colocar offline a aplicação destinada ao utilizador.<br/><br/>4. Inicie a ativação pós-falha planeada através do portal de ASR. A máquina de virtual no local é automaticamente encerrar.<br/><br/>Perda de dados de aplicações eficazes = 0 <br/><br/>Um diário de pontos de recuperação também é fornecido numa janela de retenção para um utilizador que quer utilizar um ponto de recuperação mais antigo. (retenção de 24 horas para Hyper-V). Se a replicação foi interrompida além do período de tempo da janela de retenção, os clientes ainda poderão ser capazes de ativação pós-falha com os pontos de recuperação disponível mais recente. | Do VMware, o ASR replica dados continuamente com CDP. Ativação pós-falha disponibiliza ao utilizador a opção de ativação pós-falha para os dados mais recentes (incluindo a aplicação de post e encerramento)<br/><br/> 1. Planear uma janela de manutenção de acordo com o processo de gestão de alterações <br/><br/>2. notificar os utilizadores do período de indisponibilidade futura <br/><br/>3.    Colocar offline a aplicação destinada ao utilizador. <br/><br/>4.  Inicie uma ativação de pós-falha planeada com o portal de ASR do ponto mais recente depois da aplicação está offline. Utilize a opção de "Ativação pós-falha não planeada" no portal e selecione o ponto mais recente para ativação pós-falha. A máquina de virtual no local é automaticamente encerrar.<br/><br/>Perda de dados de aplicações eficazes = 0 <br/><br/>Um diário de pontos de recuperação numa janela de retenção é fornecido para um cliente que quer utilizar um ponto de recuperação mais antigo. (72 horas de retenção para VMware). Se a replicação foi interrompida além do período de tempo da janela de retenção, os clientes ainda poderão ser capazes de ativação pós-falha com os pontos de recuperação disponível mais recente.
|Ativação pós-falha devido a um período de indisponibilidade do Centro de dados não planeada (natural ou desastre de TI) | Perda mínima de dados para a aplicação | 1. iniciar o plano BCP da organização <br/><br/>2. Inicie ativação pós-falha não planeada com o portal de ASR para a versão mais recente ou um ponto da janela de retenção (diário).| 1. Inicie o plano BCP da organização. <br/><br/>2.  Inicie ativação pós-falha não planeada com o portal de ASR para a versão mais recente ou um ponto da janela de retenção (diário).


## <a name="run-a-failover"></a>Executar uma ativação pós-falha
Este procedimento descreve como executar uma ativação pós-falha para uma [plano de recuperação](site-recovery-create-recovery-plans.md). Em alternativa pode executar a ativação pós-falha para uma única máquina virtual ou servidor físico a partir da **itens replicados** página, tal como descrito [aqui](vmware-azure-tutorial-failover-failback.md#run-a-failover-to-azure).


![Ativação pós-falha](./media/site-recovery-failover/Failover.png)

1. Selecione **planos de recuperação** > *recoveryplan_name*. Clique em **ativação pós-falha**
2. Sobre o **ativação pós-falha** ecrã, selecione um **ponto de recuperação** a ativação pós-falha. Pode utilizar uma das opções seguintes:
   1. **Mais recente**: Esta opção inicia a tarefa pela primeira processar todos os dados que tenham sido enviados para o serviço Site Recovery. Processar os dados cria um ponto de recuperação para cada máquina virtual. Este ponto de recuperação é utilizado pela máquina virtual durante a ativação pós-falha. Esta opção disponibiliza o último RPO (objetivo de ponto de recuperação) que a máquina virtual criada após a ativação pós-falha tem todos os dados que foram replicada para o serviço Site Recovery quando a ativação pós-falha foi acionada.
   1. **Processado mais recentemente**: Esta opção faz a ativação pós-falha de todas as máquinas virtuais do plano de recuperação do ponto de recuperação mais recente que já foram processados pelo serviço Site Recovery. Quando estiver fazendo a ativação pós-falha de teste de uma máquina virtual, o carimbo de data / hora do ponto de recuperação processados também é mostrado. Se estiver a fazer a ativação pós-falha de um plano de recuperação, pode ir para a máquina virtual individual e examinar **pontos de recuperação mais recentes** mosaico para obter essas informações. Como não é despendido tempo a processar os dados não processados, esta opção fornece uma opção de ativação pós-falha RTO (objetivo de tempo de recuperação) baixo.
   1. **Mais recente consistente com a aplicação**: Esta opção faz a ativação pós-falha de todas as máquinas virtuais do plano de recuperação do ponto de recuperação consistente com a mais recente do aplicativo que já foram processados pelo serviço Site Recovery. Quando estiver fazendo a ativação pós-falha de teste de uma máquina virtual, o carimbo de hora o mais recente consistente com a aplicação do ponto de recuperação também é mostrado. Se estiver a fazer a ativação pós-falha de um plano de recuperação, pode ir para a máquina virtual individual e examinar **pontos de recuperação mais recentes** mosaico para obter essas informações.
   1. **MULTI-VM mais recente processado**: Esta opção só está disponível para planos de recuperação que tenham, pelo menos, uma máquina virtual com Diante de consistência de várias VMS. Ponto de máquinas virtuais que fazem parte de um grupo de replicação ativação pós-falha para a recuperação mais recente comuns multi-VMS consistente. Outra ativação pós-falha de máquinas virtuais para o ponto mais recente processado recuperação.  
   1. **MULTI-VM mais recente consistente com a aplicação**: Esta opção só está disponível para planos de recuperação que tenham, pelo menos, uma máquina virtual com Diante de consistência de várias VMS. Ponto de máquinas virtuais que fazem parte de um grupo de replicação ativação pós-falha para a recuperação mais recente comuns multi-VMS consistentes. Outra ativação pós-falha de máquinas virtuais para o ponto de recuperação consistente com a aplicação mais recente.
   1. **Custom**: Se estiver fazendo a ativação pós-falha de teste de uma máquina virtual, em seguida, pode utilizar esta opção para ativação pós-falha para um ponto de recuperação específico.

      > [!NOTE]
      > A opção de escolher um ponto de recuperação só está disponível quando estão a fazer failover para o Azure.
      >
      >


1. Se algumas das máquinas virtuais no plano de recuperação foram realizadas numa execução anterior e agora as máquinas virtuais estão ativas na localização de origem e destino, é possível usar **mudar a direção da** opção para decidir a direção na qual o ativação pós-falha deve acontecer.
1. Se estiver a fazer failover para o Azure e a encriptação de dados estiver ativada para a nuvem (aplica-se apenas quando proteger máquinas virtuais Hyper-v de um servidor do VMM), na **chave de encriptação** selecione o certificado que foi emitido quando ativado encriptação de dados durante a configuração no servidor do VMM.
1. Selecione **encerrar máquina antes de iniciar a ativação pós-falha** se pretender que o Site Recovery para tentar fazer um encerramento das máquinas de virtuais de origem antes de acionar a ativação pós-falha. Ativação pós-falha continua, mesmo que o encerramento falhe.  

    > [!NOTE]
    > Se as máquinas virtuais de Hyper-v estiverem protegidas, a opção de encerrar também tenta sincronizar os dados no local que tenham ainda não foram enviados para o serviço antes de acionar a ativação pós-falha.
    >
    >

1. Pode seguir o progresso da ativação pós-falha na página **Trabalhos**. Mesmo se ocorrerem erros durante uma ativação pós-falha não planeada, o plano de recuperação é executado até esta estar concluída.
1. Após a ativação pós-falha, valide a máquina virtual pelo registo-na ele. Se deseja alternar para outro ponto de recuperação da máquina virtual, em seguida, pode usar **alterar ponto de recuperação** opção.
1. Quando estiver satisfeito com a ativação pós-falha da máquina virtual, pode **Consolidar** a ativação pós-falha. **Consolidar elimina todos os pontos de recuperação disponíveis com o serviço** e **alterar ponto de recuperação** opção já não está disponível.

## <a name="planned-failover"></a>Ativação pós-falha planeada
Máquinas virtuais/servidores físicos protegidos com o Site Recovery também suporte **a ativação pós-falha planeada**. Ativação pós-falha planeada é uma zero opção do dados perda ativação pós-falha. Quando é acionada uma ativação pós-falha planeada, primeiro as máquinas de virtuais de origem são encerrar, os dados mais recentes são sincronizados e, em seguida, é acionada uma ativação pós-falha.

> [!NOTE]
> Durante a ativação pós-falha de máquinas de virtuais de Hyper-v de um site no local para outro site no local, para voltar para o site no local primário tem de primeiro **replicar inverso** a máquina virtual de volta para o site primário e, em seguida, acione uma ativação pós-falha. Se a máquina virtual primária não está disponível, em seguida, antes de começar a **replicar inverso** possui para restaurar a máquina virtual a partir de uma cópia de segurança.   
> 
> 
> ## <a name="failover-job"></a>Tarefa de ativação pós-falha

![Ativação pós-falha](./media/site-recovery-failover/FailoverJob.png)

Quando uma ativação pós-falha é disparada, ela envolve os seguintes passos:

1. Verificação de pré-requisitos: Este passo garante que todas as condições necessárias para a ativação pós-falha são cumpridas
1. Ativação pós-falha: Este passo processa os dados e torna pronto para que possa ser criada a máquina virtual do Azure fora dele. Se tiver escolhido **mais recente** ponto de recuperação, este passo cria um ponto de recuperação dos dados que tenham sido enviados para o serviço.
1. Iniciar: Este passo cria uma máquina virtual do Azure com os dados processados no passo anterior.

> [!WARNING]
> **Não cancelar em curso ativação pós-falha**: Antes de iniciar a ativação pós-falha, a replicação para a máquina virtual é parada. Se **Cancelar** uma tarefa de progresso, paradas de ativação pós-falha, mas a máquina virtual não será iniciado replicar. Não é possível iniciar a replicação novamente.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Tempo decorrido para a ativação pós-falha para o Azure

Em certos casos, a ativação pós-falha de máquinas virtuais requer uma etapa extra intermediária que normalmente demora cerca de 8 a 10 minutos a concluir. Nos seguintes casos, o tempo necessário para a ativação pós-falha será maior do que o habitual:

* Máquinas de virtuais de VMware com o serviço de mobilidade da versão anterior ao 9.8
* Servidores físicos
* Máquinas virtuais do Linux do VMware
* Máquinas de virtuais de Hyper-V protegidas que os servidores físicos
* Onde o seguintes controladores não estão presentes como controladores de arranque de máquinas virtuais do VMware
    * storvsc
    * VMBus
    * storflt
    * intelide
    * atapi
* Máquinas de virtuais de VMware que não têm o serviço DHCP ativado, independentemente se estão a utilizar DHCP ou estáticas de endereços IP

Em todos os outros casos, este passo intermédio não é necessário e o tempo decorrido para a ativação pós-falha é inferior.

## <a name="using-scripts-in-failover"></a>Usando scripts na ativação pós-falha
Convém automatizar determinadas ações ao efetuar uma ativação pós-falha. Pode utilizar scripts ou [runbooks de automatização do Azure](site-recovery-runbook-automation.md) na [planos de recuperação](site-recovery-create-recovery-plans.md) para fazer isso.

## <a name="post-failover-considerations"></a>Considerações de ativação pós-falha de postagem
Após a ativação pós-falha que pode querer considerar as seguintes recomendações:
### <a name="retaining-drive-letter-after-failover"></a>Mantendo a letra de unidade após a ativação pós-falha
Para manter a letra de unidade em máquinas virtuais após ativação pós-falha, pode definir o **política de SAN** para a máquina virtual **OnlineAll**. [Leia mais](https://support.microsoft.com/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure).

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar a ligação para VMs do Azure após a ativação pós-falha

Se quiser ligar a VMs do Azure através de RDP/SSH após a ativação pós-falha, siga os requisitos resumidos na tabela [aqui](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

Siga os passos descritos [aqui](site-recovery-failover-to-azure-troubleshoot.md) para resolver quaisquer problemas de conectividade após a ativação pós-falha.


## <a name="next-steps"></a>Passos Seguintes

> [!WARNING]
> Depois de ter houve ativação pós-falha de máquinas virtuais e o Centro de dados no local está disponível, deve [ **voltar a proteger** ](vmware-azure-reprotect.md) máquinas de virtuais de VMware de volta ao centro de dados no local.

Uso [ **a ativação pós-falha planeada** ](hyper-v-azure-failback.md) a opção de **reativação pós-falha** máquinas de virtuais de Hyper-v para o local do Azure.

Se não tiverem ao longo do Hyper-v máquina virtual para outro local Centro de dados gerido por um servidor VMM e o Centro de dados primária está disponível, em seguida, utilize **a replicação inversa** opção para iniciar a replicação novamente para os dados primários Centro.
