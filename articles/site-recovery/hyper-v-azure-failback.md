---
title: Fail back Hyper-V VMs from Azure with Azure Site Recovery
description: Como reaver os Hiper-V V V MMs para um local no local a partir de Azure com recuperação do site Azure.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: rajanaki
ms.openlocfilehash: 4b005ae308576db6fd26fcf079161430b266ec3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79281785"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Executar uma reativação pós-falha para VMs Hyper-V

Este artigo descreve como reativar os VMs Azure que foram criados após a falha dos VMs Hiper-V de um site no local para Azure, com a Recuperação do [Site Azure.](site-recovery-overview.md)

- Você falha Hiper-V V V Ms de Azure executando uma falha planeada de Azure para o local no local. Se a direção de failover é de Azure para o local, é considerado um fracasso.
- Uma vez que o Azure é um ambiente altamente disponível e os VMs estão sempre disponíveis, o failback do Azure é uma atividade planeada. Pode planear uma pequena paragem para que as cargas de trabalho possam voltar a funcionar no local. 
- O failback planeado desliga os VMs em Azure e descarrega as últimas alterações. Não se espera nenhuma perda de dados.

## <a name="before-you-start"></a>Antes de começar

1. [Reveja os tipos de failback](failover-failback-overview.md#hyper-v-reprotectionfailback) que pode utilizar - recuperação original da localização e recuperação alternativa de localização.
2. Certifique-se de que os VMs Azure estão a utilizar uma conta de armazenamento e não discos geridos. O failback dos VMs hiper-V replicados utilizando discos geridos não é suportado.
3. Verifique se o hospedeiro Hyper-V no local (ou servidor VMM do System Center se estiver a usar com a Recuperação do Site) está a funcionar e ligado ao Azure. 
4. Certifique-se de que a falha e o compromisso estão completos para os VMs. Não é necessário configurar componentes específicos de recuperação do site para falhas de VMs Hiper-V do Azure.
5. O tempo necessário para completar a sincronização de dados e iniciar o VM no local dependerá de uma série de fatores. Para acelerar o download de dados, pode configurar o agente dos Serviços de Recuperação da Microsoft para utilizar mais fios para paralelar o download. [Saiba mais](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="fail-back-to-the-original-location"></a>Falhar de volta à localização original

Para falhar os Hiper-V VMs em Azure para o VM original no local, executar uma falha planeada de Azure para o local no local seguinte:

1. No cofre > **itens replicados,** selecione o VM. Clique à direita no VM > **Failover Planeado**. Se estiver a falhar num plano de recuperação, selecione o nome do plano e clique em **Failover** > **Planned Failover**.
2. Em **Confirmar Falha Planeada,** escolha a origem e os locais-alvo. Reparem na direção do failover. Se a falha das primárias funcionou como esperado e todas as máquinas virtuais estão no local secundário, isto é apenas para informação.
3. Na Sincronização de **Dados,** selecione uma opção:
    - **Sincronizar os dados antes da falha (sincronizar apenas as alterações delta)**— Esta opção minimiza o tempo de inatividade dos VMs à medida que sincroniza sem os desligar.
        - **Fase 1**: Tira uma foto do Azure VM e copia-o para o hospedeiro Hyper-V no local. A máquina continua a funcionar em Azure.
        - **Fase 2**: Desliga o VM Azure para que não ocorram novas alterações. O conjunto final de alterações delta é transferido para o servidor no local e o VM no local está iniciado.
    - **Sincronizar os dados apenas durante o failover (download completo)**— Esta opção é mais rápida porque presumimos que a maior parte do disco mudou e não queremos passar tempo a calcular cheques. Esta opção não realiza quaisquer cálculos de cheques.
        - Faz um download do disco. 
        - Recomendamos que utilize esta opção se estiver a executar o Azure há algum tempo (um mês ou mais) ou se o VM no local for eliminado.

4. Apenas para VMM, se a encriptação de dados estiver ativada para a nuvem, na **Chave de Encriptação,** selecione o certificado que foi emitido quando ativou a encriptação de dados durante a instalação do Fornecedor no servidor VMM.
5. Iniciar a ativação pós-falha. Pode seguir o progresso da ativação pós-falha no separador **Trabalhos**.
6. Se tiver selecionado a opção de sincronizar os dados antes da falha, depois de a sincronização inicial de dados estar completa e estiver pronto para desligar as máquinas virtuais em Azure, clique em **Jobs** > nome de trabalho > **Complete Failover**. Isto faz o seguinte:
    - Desliga a máquina Azure.
    - Transfere as últimas alterações para o VM no local.
    - Inicia o VM no local.
7. Agora pode entrar na máquina VM no local para verificar se está disponível como esperado.
8. A máquina virtual está num estado pendente. Clique **em comprometer-se** a cometer a falha.
9. Para completar o reversão, clique **em Reverter Replicate** para começar a replicar novamente o VM no local para Azure.



## <a name="fail-back-to-an-alternate-location"></a>Falhar de volta a um local alternativo 

Não volte a um local alternativo da seguinte forma:

1. Se estiver a configurar um novo hardware, instale uma [versão suportada do Windows](hyper-v-azure-support-matrix.md#replicated-vms)e a função Hyper-V na máquina.
2. Crie um interruptor de rede virtual com o mesmo nome que tinha no servidor original.
3. Em **itens protegidos** > **Protection Group** > \<ProtectionName \<> -> VirtualMachineName>, selecione o VM que pretende falhar e, em seguida, selecione **'Failover' planeado**.
4. Em **Confirmar Failover**s planeados, eleger **create on-local virtual machine if it não exist .**
5. No Nome do **Anfitrião,** selecione o novo servidor de anfitriões Hyper-V no qual pretende colocar o VM.
6. Na Sincronização de **Dados,** recomendamos que selecione a opção de sincronizar os dados antes da falha. Isto minimiza o tempo de inatividade para os VMs à medida que sincroniza sem os desligar. Faz o seguinte:
    - **Fase 1**: Tira fotografia do VM Azure e copia-o para o hospedeiro Hyper-V no local. A máquina continua a funcionar em Azure.
    - **Fase 2**: Desliga o VM Azure para que não ocorram novas alterações. O conjunto final de alterações é transferido para o servidor no local e a máquina virtual no local é iniciada.
    
7. Clique na marca de verificação para iniciar a falha (failback).
8. Depois de terminar a sincronização inicial e estiver pronto para desligar o Azure VM, clique em **Jobs** > \<planejado trabalho de failover> > **Complete Failover**. Isto desliga a máquina Azure, transfere as últimas alterações para o VM no local, e inicia-a.
9. Pode assinar no VM no local para verificar se está tudo a funcionar como esperado.
10. Clique **em comprometer-se** para terminar a falha. A comprometa a eliminar o VM Azure e os seus discos e prepara o VM no local para ser novamente protegido.
10. Clique em **Reverter Replicate** para começar a replicar o VM no local para Azure. Só as mudanças delta desde que o VM foi desligado em Azure serão replicadas.

    > [!NOTE]
    > Se cancelar o trabalho de failback durante a sincronização de dados, o VM no local estará num estado corrompido. Isto porque a sincronização de dados copia os dados mais recentes dos discos Azure VM para os discos de dados no local, e até que a sincronização esteja concluída, os dados do disco podem não estar num estado consistente. Se o VM no local começar após o cancelamento da sincronização de dados, pode não arrancar. Neste caso, recorra a falha para completar a sincronização de dados.


## <a name="next-steps"></a>Passos seguintes
Depois de o VM no local se replicar para o Azure, pode [saquear outra falha](site-recovery-failover.md) para o Azure, se necessário.
