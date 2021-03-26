---
title: Falha em Hiper-V VMs de Azure com recuperação do local de Azure
description: Como falhar os Hiper-VMs para um local no local de Azure com Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: ramamill
ms.openlocfilehash: 96c3dce17ab78e08b28bb41c0100e51a72a666e7
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110258"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Executar uma reativação pós-falha para VMs Hyper-V

Este artigo descreve como falhar os VMs Azure que foram criados após a falha de Hiper-VMs de um site no local para Azure, com [Azure Site Recovery](site-recovery-overview.md).

- Falhas em Hiper-V VMs de Azure, executando uma falha planeada de Azure para o local. Se a direção de falha é de Azure para o local, é considerado um fracasso.
- Uma vez que o Azure é um ambiente altamente disponível e os VMs estão sempre disponíveis, o failback do Azure é uma atividade planeada. Você pode planejar um pequeno tempo de inatividade para que as cargas de trabalho possam começar a funcionar no local novamente. 
- O failback planeado desliga os VMs em Azure e descarrega as últimas alterações. Não é esperada nenhuma perda de dados.

## <a name="before-you-start"></a>Antes de começar

1. [Reveja os tipos de falha](failover-failback-overview.md#hyper-v-reprotectionfailback) que pode usar - recuperação original da localização e recuperação de localização alternativa.
2. Certifique-se de que os VMs Azure estão a utilizar uma conta de armazenamento e não discos geridos. O recuo das máquinas virtuais Hyper-V, que falhou nas máquinas Azure usando discos geridos, não é suportado.
3. Verifique se o anfitrião Hiper-V no local (ou servidor VMM do System Center se estiver a utilizar com a Recuperação do Site) está a funcionar e ligado ao Azure. 
4. Certifique-se de que o failover e o compromisso estão completos para os VMs. Não precisa de configurar componentes específicos de Recuperação do Local para falha de Hiper-VMs da Azure.
5. O tempo necessário para completar a sincronização de dados e iniciar o VM no local dependerá de uma série de fatores. Para acelerar o download de dados, pode configurar o agente dos Serviços de Recuperação da Microsoft para utilizar mais linhas para paralelizar o download. [Saiba mais](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="fail-back-to-the-original-location"></a>Falhe de volta ao local original

Para remediar os VMs Hiper-V em Azure para os VM originais no local, executar uma falha planeada de Azure para o local do local da seguinte forma:

1. No cofre > **itens replicados,** selecione o VM. Clique com o botão direito no VM > **Failover Planeado**. Se estiver a falhar num plano de recuperação, selecione o nome do plano e clique em **Failover**  >  **Planned Failover**.
2. Em **Confirmação Planeada,** escolha os locais de origem e alvo. Reparem na direção de falha. Se o failover do primário funcionou como esperado e todas as máquinas virtuais estão no local secundário isto é apenas para informação.
3. Na **Sincronização de Dados,** selecione uma opção:
    - **Sincronizar os dados antes do failover (sincronizar apenas alterações delta)**— Esta opção minimiza o tempo de inatividade dos VMs à medida que sincroniza sem os desligar.
        - **Fase 1**: Tira uma foto de Azure VM e copia-a para o anfitrião Hiper-V no local. A máquina continua a funcionar em Azure.
        - **Fase 2**: Desliga o Azure VM para que não ocorram novas alterações. O conjunto final de alterações delta é transferido para o servidor no local e o VM no local é iniciado.
    - **Sincronizar os dados apenas durante o failover (download completo)**— Esta opção é mais rápida porque presumimos que a maior parte do disco mudou e não queremos gastar tempo a calcular as despesas de verificação. Esta opção não efetua nenhum cálculo de checkum.
        - Faz um download do disco. 
        - Recomendamos que utilize esta opção se estiver a executar o Azure durante algum tempo (um mês ou mais) ou se o VM no local for eliminado.

4. Apenas para o VMM, se a encriptação de dados estiver ativada para a nuvem, na **Chave de Encriptação,** selecione o certificado que foi emitido quando ativou a encriptação de dados durante a instalação do Fornecedor no servidor VMM.
5. Iniciar a ativação pós-falha. Pode seguir o progresso da ativação pós-falha no separador **Trabalhos**.
6. Se selecionou a opção de sincronizar os dados antes da falha, depois de concluída a sincronização inicial dos dados e estiver pronto para desligar as máquinas virtuais em Azure, clique em **Jobs** > nome de trabalho > **Complete Failover**. Isto faz o seguinte:
    - Desliga a máquina Azure.
    - Transfere as últimas alterações para o VM no local.
    - Começa o VM no local.
7. Pode agora entrar na máquina VM no local para verificar se está disponível como esperado.
8. A máquina virtual está em estado pendente. Clique **em Comprometer-se** a cometer o failover.
9. Para completar o failback, clique em **Reverse Replicate** para começar a replicar novamente o VM no local para Azure.



## <a name="fail-back-to-an-alternate-location"></a>Falhar de volta a um local alternativo 

Volte a um local alternativo da seguinte forma:

1. Se estiver a configurar um novo hardware, instale uma [versão suportada do Windows](hyper-v-azure-support-matrix.md#replicated-vms)e o papel De Hiper-V na máquina.
2. Crie um interruptor de rede virtual com o mesmo nome que tinha no servidor original.
3. No Grupo de Proteção **de Itens Protegidos,**  >    >  \<ProtectionGroupName>  ->  \<VirtualMachineName> selecione o VM que pretende falhar e, em seguida, selecione **"Failover" planeado**.
4. Em **ConfirmaÇão Planeada Failover** s, eleja **Crie máquina virtual no local se não existir**.
5. No **Nome do Anfitrião,** selecione o novo servidor de anfitrião Hyper-V no qual pretende colocar o VM.
6. Na **Sincronização de Dados,** recomendamos que selecione a opção de sincronizar os dados antes da falha. Isto minimiza o tempo de inatividade para os VMs à medida que sincroniza sem os desligar. Faz o seguinte:
    - **Fase 1**: Tira uma foto do Azure VM e copia-a para o anfitrião Hiper-V no local. A máquina continua a funcionar em Azure.
    - **Fase 2**: Desliga o Azure VM para que não ocorram novas alterações. O conjunto final de alterações é transferido para o servidor no local e a máquina virtual no local é iniciada.
    
7. Clique na marca de verificação para iniciar a falha (failback).
8. Depois de terminar a sincronização inicial e estiver pronto para desligar o Azure VM, clique em **Jobs**  >  \<planned failover job>  >  **Complete Failover**. Isto desliga a máquina Azure, transfere as últimas alterações para o VM no local, e inicia-a.
9. Pode inscrever-se no VM no local para verificar se está tudo a funcionar como esperado.
10. Clique **em Comprometer-se** a terminar a falha. Compromete-se a eliminar o Azure VM e os seus discos e prepara o VM no local para ser novamente protegido.
10. Clique **em Reverso Replicar** para começar a replicar os VM no local para Azure. Apenas as mudanças delta desde que o VM foi desligado em Azure serão replicadas.

    > [!NOTE]
    > Se cancelar o trabalho de failback durante a sincronização de dados, o VM no local estará num estado corrompido. Isto porque a sincronização de dados copia os dados mais recentes dos discos VM do Azure para os discos de dados no local, e até que a sincronização esteja concluída, os dados do disco podem não estar em estado consistente. Se o VM no local começar após o cancelamento da sincronização de dados, poderá não arrancar. Neste caso, repercutir o failover para completar a sincronização de dados.


## <a name="next-steps"></a>Passos seguintes
Depois de o VM no local estar a replicar-se para o Azure, [podes executar outra falha](site-recovery-failover.md) para o Azure, conforme necessário.
