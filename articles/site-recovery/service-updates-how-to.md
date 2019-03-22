---
title: Atualiza o Azure Site Recovery | Documentos da Microsoft
description: Fornece uma descrição geral das atualizações de serviço e como atualizar componentes utilizados no Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/05/2019
ms.author: rajanaki
ms.openlocfilehash: e27dee213baf8365c3ad4efc69602f66e2081abe
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311157"
---
# <a name="service-updates-in-azure-site-recovery"></a>Atualizações de serviço no Azure Site Recovery
Como uma organização, terá de descobrir como pretende manter os dados protegidos e aplicações/cargas de trabalho em execução quando planeada e falhas não planeadas ocorrerem. O Azure Site Recovery contribui para a sua estratégia BCDR ao manter as suas aplicações em execução em VMs e servidores físicos disponíveis se um site ficar inativo. O Site Recovery replica as cargas de trabalho em execução em VMs e servidores físicos para que permaneçam disponíveis num local secundário, se o site primário não estiver disponível. Recupera as cargas de trabalho para o site primário quando estiver novamente operacional.

O Site Recovery pode gerir a replicação de:

- [VMs do Azure entre regiões do Azure a replicar](azure-to-azure-tutorial-dr-drill.md).
- Máquinas virtuais no local e servidores físicos que replicam para o Azure ou para um site secundário.
Para saber mais consulte a documentação [aqui](https://docs.microsoft.com/azure/site-recovery) .

O Azure Site Recovery publica atualizações de serviço regularmente - incluindo a adição de novos recursos, aprimoramentos na matriz de suporte e correções de erros, se aplicável. Para se manter atual tire partido da todos os mais recentes funcionalidades e aprimoramentos & correções de erros se qualquer um, os usuários são aconselhados a sempre atualizar para as versões mais recentes dos componentes do Azure SIte Recovery. 
 
## <a name="support-statement-for-azure-site-recovery"></a>Declaração de suporte do Azure Site Recovery 

> [!IMPORTANT]
> **A cada nova versão "n" de um Azure Site Recovery componente que for lançado, todas as versões anteriores ao ' n-4' é considerado sem suporte**. Por conseguinte, é sempre aconselhável atualizar para versões mais recentes disponíveis.

> [!IMPORTANT]
> O suporte oficial para atualizações é de > N-4 para a versão de N (N a ser a versão mais recente). Se estiver num N-6, terá de atualizar primeiro para N-4 e, em seguida, atualizar para o N.

### <a name="upgrading-when-the-difference-between-current-version-and-latest-released-version-is-greater-than-4"></a>Atualizar quando a diferença entre a versão atual e versão de lançamento mais recente é superior a 4

1. Como primeiro passo, atualizar o componente atualmente instalado do dizer de versão N para N + 4 e, em seguida, mova para a próxima versão compatível. Digamos que a versão atual é 9.24 e estiver a utilizar 9.16, primeira atualização 9.20 e, em seguida, 9.24.
2. Siga o mesmo processo para todos os componentes, dependendo do cenário.

### <a name="support-for-latest-oskernel-versions"></a>Suporte para versões mais recentes do kernel do SO

> [!NOTE]
> Se tiver uma janela de manutenção agendada, e uma reinicialização é parte do mesmo, recomendamos que Atualize os componentes do Site Recovery e continuar com o restante das atividades agendadas pela primeira vez.

1. Antes de atualizar suas versões de Kernel/OS, certifique-se primeiro de se a versão de destino é suportada pelo Azure Site Recovery. Pode encontrar as informações na nossa documentação para VMs do Azure, [VMs de VMware](vmware-physical-azure-support-matrix.md) & VMs de Hyper-V no
2. Consulte nossos [atualizações de serviço](https://azure.microsoft.com/updates/?product=site-recovery) para descobrir qual versão do Site Recovery componentes suportam a versão específica que pretende atualizar para o.
3. Em primeiro lugar, atualize para a versão mais recente de recuperar o Site.
4. Agora, atualize o sistema operacional/Kernel para as versões pretendidas.
5. Realize uma reinicialização.
6. Isto irá garantir que a versão de Kernel do sistema operacional nas suas máquinas são atualizados para a versão mais recente e também que as alterações mais recentes da recuperação de sites que são necessários para suportar a nova versão também são carregadas na máquina de origem.



## <a name="azure-vm-disaster-recovery-to-azure"></a>Recuperação após desastre da VM para o Azure
Neste cenário, é altamente recomendável que [ativar](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-autoupdate) as atualizações automáticas. Pode optar por permitir a recuperação de Site gerir as atualizações das seguintes formas:

- Como parte do passo ativar replicação
- Ativar/desativar a extensão de atualizar as definições no interior do Cofre

No caso de optou por gerir manualmente as atualizações, siga estes passos:

1. Aceda ao portal do Azure e, em seguida, navegue para a sua "Cofre dos Recovery services."
2. Vá para o painel de "Itens replicados" no portal do Azure para o "Cofre dos Recovery services."
3. Clique na notificação seguinte na parte superior do ecrã:
    
    *Nova atualização de agente de replicação da recuperação de Site está disponível*
    
    *Clique para instalar ->*

4. Selecione as VMs que pretende aplicar a atualização e, em seguida, clique em **OK**.

## <a name="between-two-on-premises-vmm-sites"></a>Entre dois sites no local VMM
1. Baixe a atualização mais recente Rollup para o Microsoft Azure Site Recovery Provider.
2. Instale o Update Rollup primeiro no servidor do VMM no local que está a gerir o site de recuperação.
3. Após a recuperação do site é atualizado, instalar o pacote cumulativo de atualizações no servidor do VMM que está a gerir o site primário.

> [!NOTE]
> Se o VMM é um altamente disponível do VMM (VMM em cluster), certifique-se de que instale a atualização em todos os nós do cluster onde o serviço do VMM está instalado.

## <a name="between-an-on-premises-vmm-site-and-azure"></a>Entre um site VMM no local e o Azure
1. Baixe o pacote cumulativo de atualizações para o fornecedor do Microsoft Azure Site Recovery.
2. Instale o pacote cumulativo de atualizações no servidor do VMM no local.
3. Instale o agente de MARS mais recente do agente em todos os anfitriões de Hyper-V.

> [!NOTE]
> Se o VMM é um altamente disponível do VMM (VMM em cluster), certifique-se de que instale a atualização em todos os nós do cluster onde o serviço do VMM está instalado.

## <a name="between-an-on-premises-hyper-v-site-and-azure"></a>Entre um site de Hyper-V no local e o Azure

1. Baixe o pacote cumulativo de atualizações para o fornecedor do Microsoft Azure Site Recovery.
2. Instale o fornecedor em cada nó dos servidores Hyper-V que registou no Azure Site Recovery.

> [!NOTE]
> Se o Hyper-V é um servidor de anfitrião em cluster Hyper-V, certifique-se de que instale a atualização em todos os nós do cluster

## <a name="between-an-on-premises-vmware-or-physical-site-to-azure"></a>Entre um VMware no local ou num site físico para o Azure

Antes de continuar com as atualizações, consulte [declaração de suporte do Site Recovery](#support-statement-for-azure-site-recovery) para compreender o caminho de atualização.

1. Com base na sua declaração de suporte e a versão atual indicada acima, instale a atualização em primeiro lugar no seu servidor de gestão no local ao seguir as diretrizes dadas [aqui](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server). Este é o servidor que tenha o servidor de configuração e funções de servidor de processo.
2. Se tiver de aumentar horizontalmente servidores do processo, em seguida a atualizá-los por seguir diretrizes, dado [aqui](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. Em seguida, para atualizar o agente de mobilidade em cada item protegido, aceda ao portal do Azure e, em seguida, vá para o **itens protegidos** > **itens replicados** página. Selecione uma VM nesta página. Selecione o **Windows Update Agent** botão é exibido na parte inferior da página para cada VM. Este procedimento atualiza o agente do serviço de mobilidade em todas as VMs protegidas.

### <a name="reboot-of-source-machine-after-mobility-agent-upgrade"></a>Reinício da máquina de origem depois de atualizar o agente de mobilidade

Uma reinicialização é recomendada após cada atualização do agente de mobilidade para se certificar de que todas as alterações mais recentes são carregadas na máquina de origem. No entanto é **não seja obrigatório**. Se a diferença entre a versão do agente durante a última reinicialização e a versão atual for superior a 4, em seguida, é um reinício obrigatório. Consulte a tabela seguinte para uma explicação detalhada.

|**Versão de agente durante a última reinicialização** | **Atualizar para o** | **É de reinício obrigatório?**|
|---------|---------|---------|
|9.16 |  9.18 | Não é obrigatório|
|9.16 | 9.19 | Não é obrigatório|
| 9.16 | 9.20 | Não é obrigatório
 | 9.16 | 9.21 | Sim, o primeiro de atualizar para 9.20, e reinicializar antes de atualizar para 9.21 como a diferença entre as versões (9.16 onde foi efetuada a última reinicialização e a versão de destino 9.21) é > 4,

## <a name="links-to-currently-supported-update-rollups"></a>Links para cumulativos suportado atualmente

|Pacote cumulativo de atualizações  |Fornecedor  |Configuração unificada| OVF  |MARS|
|---------|---------|---------|---------|--------|
|[O Update Rollup 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery)     |   5.1.4000.0  |  9.23.5163.1   |  5.1.4000.0  | 2.0.9156.0
|[Update Rollup 34](https://support.microsoft.com/en-us/help/4490016/update-rollup-34-for-azure-site-recovery) -Hot fix     |   5.1.3950.0  |  9.22.5142.1   |  5.1.3950.0  | 2.0.9155.0
|[O Update Rollup 33](https://support.microsoft.com/en-us/help/4489582/update-rollup-33-for-azure-site-recovery)     |   5.1.3900.0  |  9.22.5109.1   |  5.1.3900.0  | 2.0.9155.0
|[O Update Rollup 32](https://support.microsoft.com/en-us/help/4485985/update-rollup-32-for-azure-site-recovery)     |   5.1.3800.0  |  9.21.5091.1   |  5.1.3800.0  |2.0.9144.0
|[O Update Rollup 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)     |     5.1.3700.0      |   9.20.5051.1      |     5.1.3700.0    |2.0.9144.0
|[O Update Rollup 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30)     |    5.1.3650.0   |   9.19.5007.1    |     5.1.3650.0    |2.0.9139.0

## <a name="previous-update-rollups"></a>Rollups de atualizações anteriores

- [O Update Rollup 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery)
- [O Update Rollup 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery)
- [O Update Rollup 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery)
- [O Update Rollup 26](https://support.microsoft.com/help/4344054/update-rollup-26-for-azure-site-recovery)  
- [O Update Rollup 25](https://support.microsoft.com/help/4278275/update-rollup-25-for-azure-site-recovery) 
- [O Update Rollup 23](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 
- [O Update Rollup 22](https://support.microsoft.com/help/4072852/update-rollup-22-for-azure-site-recovery) 
- [O Update Rollup 21](https://support.microsoft.com/help/4051380/update-rollup-21-for-azure-site-recovery) 
- [O Update Rollup 20](https://support.microsoft.com/help/4041105/update-rollup-20-for-azure-site-recovery) 
- [O Update Rollup 19](https://support.microsoft.com/help/4034599/update-rollup-19-for-azure-site-recovery) 
