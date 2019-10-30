---
title: Failover durante a recuperação de desastre com o Azure Site Recovery
description: Saiba mais sobre como fazer failover de VMs e servidores físicos durante a recuperação de desastre com o serviço de Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2019
ms.author: raynew
ms.openlocfilehash: 1585c5dbdecf11bbc6ef3dad63bf4f982c70f73e
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053762"
---
# <a name="fail-over-vms-and-physical-servers"></a>Failover de VMs e servidores físicos 

Este artigo descreve como fazer failover de máquinas virtuais e servidores físicos protegidos por Site Recovery.

## <a name="prerequisites"></a>Pré-requisitos
1. Antes de fazer um failover, faça um [failover de teste](site-recovery-test-failover-to-azure.md) para garantir que tudo esteja funcionando conforme o esperado.
1. [Prepare a rede](site-recovery-network-design.md) no local de destino antes de fazer um failover.  

Use a tabela a seguir para saber sobre as opções de failover fornecidas pelo Azure Site Recovery. Essas opções também são listadas para cenários de failover diferentes.

| Cenário | Requisito de recuperação de aplicativo | Fluxo de trabalho para Hyper-V | Fluxo de trabalho para VMware
|---|--|--|--|
|Failover planejado devido a um tempo de inatividade futuro do datacenter| Perda zero de dados para o aplicativo quando uma atividade planejada é executada| Para o Hyper-V, o ASR replica dados em uma frequência de cópia que é especificada pelo usuário. O failover planejado é usado para substituir a frequência e replicar as alterações finais antes de um failover ser iniciado. <br/> <br/> 1. Planeje uma janela de manutenção de acordo com o processo de gerenciamento de alterações de sua empresa. <br/><br/> 2. notifique os usuários sobre o tempo de inatividade futuro. <br/><br/> 3. Coloque o aplicativo voltado para o usuário offline.<br/><br/>4. Inicie o failover planejado usando o portal de ASR. A máquina virtual local é desligada automaticamente.<br/><br/>Perda de dados de aplicativo efetiva = 0 <br/><br/>Um diário de pontos de recuperação também é fornecido em uma janela de retenção para um usuário que deseja usar um ponto de recuperação mais antigo. (retenção de 24 horas para o Hyper-V). Se a replicação tiver sido interrompida além do período da janela de retenção, os clientes ainda poderão fazer failover usando os pontos de recuperação mais recentes disponíveis. | Para o VMware, o ASR replica os dados continuamente usando a CDP. O failover dá ao usuário a opção de fazer failover para os dados mais recentes (incluindo o desligamento do aplicativo post)<br/><br/> 1. planejar uma janela de manutenção de acordo com o processo de gerenciamento de alterações <br/><br/>2. notifique os usuários sobre o tempo de inatividade futuro <br/><br/>3. Coloque o aplicativo voltado para o usuário offline.<br/><br/>4. Inicie um failover planejado usando o portal de ASR para o último ponto depois que o aplicativo estiver offline. Use a opção "failover planejado" no portal e selecione o ponto mais recente para failover. A máquina virtual local é desligada automaticamente.<br/><br/>Perda de dados de aplicativo efetiva = 0 <br/><br/>Um diário de pontos de recuperação em uma janela de retenção é fornecido para um cliente que deseja usar um ponto de recuperação mais antigo. (72 horas de retenção para VMware). Se a replicação tiver sido interrompida além do período da janela de retenção, os clientes ainda poderão fazer failover usando os pontos de recuperação mais recentes disponíveis.
|Failover devido a um tempo de inatividade do datacenter não planejado (desastre natural ou de ti) | Perda mínima de dados para o aplicativo | 1. iniciar o plano BCP da organização <br/><br/>2. Inicie o failover não planejado usando o portal de ASR para a versão mais recente ou um ponto da janela de retenção (diário).| 1. Inicie o plano BCP da organização. <br/><br/>2. Inicie o failover não planejado usando o portal de ASR para a versão mais recente ou um ponto da janela de retenção (diário).


## <a name="run-a-failover"></a>Executar uma ativação pós-falha
Este procedimento descreve como executar um failover para um [plano de recuperação](site-recovery-create-recovery-plans.md). Como alternativa, você pode executar o failover para uma única máquina virtual ou servidor físico da página de **itens replicados** , conforme descrito [aqui](vmware-azure-tutorial-failover-failback.md#run-a-failover-to-azure).


![Ativação Pós-Falha](./media/site-recovery-failover/Failover.png)

1. Selecione **planos de recuperação** > *recoveryplan_name*. Clique em **failover**
2. Na tela **failover** , selecione um **ponto de recuperação** para o qual fazer failover. Pode utilizar uma das opções seguintes:
   1. **Mais recente**: essa opção inicia o trabalho primeiro processando todos os dados que foram enviados para site Recovery serviço. O processamento dos dados cria um ponto de recuperação para cada máquina virtual. Esse ponto de recuperação é usado pela máquina virtual durante o failover. Essa opção fornece o RPO (objetivo de ponto de recuperação) mais baixo que a máquina virtual criada após o failover ter todos os dados que foram replicados para Site Recovery serviço quando o failover foi disparado.
   1. **Mais recente processado**: essa opção faz failover de todas as máquinas virtuais do plano de recuperação para o último ponto de recuperação que já foi processado pelo serviço de site Recovery. Quando você estiver fazendo failover de teste de uma máquina virtual, o carimbo de data/hora do último ponto de recuperação processado também será mostrado. Se você estiver fazendo failover de um plano de recuperação, poderá ir para uma máquina virtual individual e examinar o bloco de **pontos de recuperação mais recente** para obter essas informações. Como nenhum tempo é gasto para processar os dados não processados, essa opção fornece uma opção de failover de baixo RTO (objetivo de tempo de recuperação).
   1. **Consistente com o aplicativo mais recente**: essa opção faz failover de todas as máquinas virtuais do plano de recuperação para o ponto de recuperação consistente do aplicativo mais recente que já foi processado pelo serviço de site Recovery. Quando você estiver fazendo failover de teste de uma máquina virtual, o carimbo de data/hora do último ponto de recuperação consistente com o aplicativo também será mostrado. Se você estiver fazendo failover de um plano de recuperação, poderá ir para uma máquina virtual individual e examinar o bloco de **pontos de recuperação mais recente** para obter essas informações.
   1. **Várias VMs processadas mais recentemente**: essa opção só está disponível para planos de recuperação que tenham pelo menos uma máquina virtual com consistência de várias VMs no. Máquinas virtuais que fazem parte de um failover de grupo de replicação para o ponto de recuperação consistente de várias VMs comum mais recente. Outros failover de máquinas virtuais para o último ponto de recuperação processado.  
   1. **Várias VMs mais recentes consistentes com o aplicativo**: essa opção só está disponível para planos de recuperação que tenham pelo menos uma máquina virtual com consistência de várias VMs no. Máquinas virtuais que fazem parte de um failover de grupo de replicação para o ponto de recuperação mais recente consistente com o aplicativo de várias VMs comum. Outros failover de máquinas virtuais para seu ponto de recuperação consistente com o aplicativo mais recente.
   1. **Personalizado**: se você estiver fazendo failover de teste de uma máquina virtual, poderá usar essa opção para fazer failover para um ponto de recuperação específico.

      > [!NOTE]
      > A opção de escolher um ponto de recuperação só estará disponível quando você estiver fazendo failover no Azure.
      >
      >


1. Se algumas das máquinas virtuais no plano de recuperação tiverem realizado failover em uma execução anterior e agora as máquinas virtuais estiverem ativas no local de origem e de destino, você poderá usar a opção **alterar direção** para decidir a direção em que o failover deve ocorrer.
1. Se você estiver fazendo failover no Azure e a criptografia de dados estiver habilitada para a nuvem (aplica-se somente quando você tiver protegido máquinas virtuais do Hyper-v de um servidor VMM), em **chave de criptografia** , selecione o certificado que foi emitido quando você habilitou a criptografia de dados durante configuração no servidor do VMM.
1. Selecione **desligar computador antes de iniciar o failover** se quiser que site Recovery tente fazer um desligamento das máquinas virtuais de origem antes de disparar o failover. O failover continua mesmo se o desligamento falhar.  

    > [!NOTE]
    > Se as máquinas virtuais do Hyper-v estiverem protegidas, a opção de desligamento também tentará sincronizar os dados locais que ainda não foram enviados ao serviço antes de disparar o failover.
    >
    >

1. Pode seguir o progresso da ativação pós-falha na página **Tarefas**. Mesmo se ocorrerem erros durante um failover não planejado, o plano de recuperação será executado até ser concluído.
1. Após o failover, valide a máquina virtual fazendo logon nela. Se desejar alternar para outro ponto de recuperação da máquina virtual, você poderá usar a opção **alterar ponto de recuperação** .
1. Quando estiver satisfeito com a ativação pós-falha da máquina virtual, pode **Consolidar** a ativação pós-falha. **Confirmar exclui todos os pontos de recuperação disponíveis com o serviço e a** opção **alterar ponto de recuperação** não está mais disponível.

## <a name="planned-failover"></a>Failover planejado
Máquinas virtuais/servidores físicos protegidos usando Site Recovery também dão suporte ao **failover planejado**. O failover planejado é uma opção de failover de perda de dados zero. Quando um failover planejado é disparado, primeiro as máquinas virtuais de origem são desligadas, os dados mais recentes são sincronizados e, em seguida, um failover é disparado.

> [!NOTE]
> Durante o failover de máquinas virtuais do Hyper-v de um site local para outro site local, para voltar ao site local primário, você precisa primeiro **replicar** a máquina virtual de volta para o site primário e, em seguida, disparar um failover. Se a máquina virtual primária não estiver disponível, antes de começar a fazer a **replicação inversa** , você precisará restaurar a máquina virtual de um backup.   
 
 
## <a name="failover-job"></a>Trabalho de failover

![Ativação Pós-Falha](./media/site-recovery-failover/FailoverJob.png)

Quando um failover é disparado, ele envolve as seguintes etapas:

1. Verificação de pré-requisitos: essa etapa garante que todas as condições necessárias para o failover sejam atendidas
1. Failover: essa etapa processa os dados e os torna prontos para que uma máquina virtual do Azure possa ser criada fora dele. Se você tiver escolhido o ponto de recuperação **mais recente** , essa etapa criará um ponto de recuperação a partir dos dados que foram enviados para o serviço.
1. Início: esta etapa cria uma máquina virtual do Azure usando os dados processados na etapa anterior.

> [!WARNING]
> **Não cancelar um failover em andamento**: antes de o failover ser iniciado, a replicação da máquina virtual é interrompida. Se você **Cancelar** um trabalho em andamento, o failover será interrompido, mas a máquina virtual não começará a ser replicada. A replicação não pode ser iniciada novamente.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Tempo necessário para failover no Azure

Em determinados casos, o failover de máquinas virtuais requer uma etapa intermediária extra que geralmente leva cerca de 8 a 10 minutos para ser concluída. Nos casos a seguir, o tempo necessário para o failover será maior do que o normal:

* Máquinas virtuais VMware usando o serviço de mobilidade da versão mais antiga que 9,8
* Servidores físicos
* Máquinas virtuais VMware Linux
* Máquinas virtuais do Hyper-V protegidas como servidores físicos
* Máquinas virtuais VMware em que os drivers a seguir não estão presentes como drivers de inicialização
    * storvsc
    * VMBus
    * storflt
    * intelide
    * ATAPI
* Máquinas virtuais VMware que não têm o serviço DHCP habilitado, independentemente de estarem usando endereços IP estáticos ou DHCP

Em todos os outros casos, essa etapa intermediária não é necessária e o tempo necessário para o failover é menor.

## <a name="using-scripts-in-failover"></a>Usando scripts no failover
Talvez você queira automatizar determinadas ações durante um failover. Você pode usar scripts ou [runbooks de automação do Azure](site-recovery-runbook-automation.md) em [planos de recuperação](site-recovery-create-recovery-plans.md) para fazer isso.

## <a name="post-failover-considerations"></a>Considerações sobre pós-failover
Após o failover, talvez você queira considerar as seguintes recomendações:
### <a name="retaining-drive-letter-after-failover"></a>Retenção da letra da unidade após o failover
Azure Site Recovery lida com a retenção de letras de unidade. [Leia mais](vmware-azure-exclude-disk.md#example-1-exclude-the-sql-server-tempdb-disk) sobre como isso é feito quando você opta por excluir alguns discos.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar a ligação para VMs do Azure após a ativação pós-falha

Se quiser ligar a VMs do Azure através de RDP/SSH após a ativação pós-falha, siga os requisitos resumidos na tabela [aqui](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

Siga os passos descritos [aqui](site-recovery-failover-to-azure-troubleshoot.md) para resolver quaisquer problemas de conectividade após a ativação pós-falha.


## <a name="next-steps"></a>Passos seguintes

> [!WARNING]
> Depois de realizar o failover de máquinas virtuais e a data center local estiver disponível, você deve [**proteger**](vmware-azure-reprotect.md) novamente as máquinas virtuais VMware de volta para o Data Center local.

Use a opção [**failover planejado**](hyper-v-azure-failback.md) para fazer **failback** de máquinas virtuais Hyper-v de volta para o local do Azure.

Se você tiver feito o failover de uma máquina virtual do Hyper-v para outro data center local gerenciado por um servidor do VMM e o data center primário estiver disponível, use a opção **replicação inversa** para iniciar a replicação de volta para o data center primário.
