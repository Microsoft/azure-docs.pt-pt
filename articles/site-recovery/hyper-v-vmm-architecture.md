---
title: Arquitetura para recuperação após desastre de Hyper-V para um secundário no site local com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo fornece uma descrição geral da arquitetura para recuperação após desastre de VMs de Hyper-V no local para um site do System Center VMM secundário com o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/19/2019
ms.author: raynew
ms.openlocfilehash: 553ecefc9f7588de0ee9056f2b2385f9a0ede50f
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316172"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Arquitetura - replicação de Hyper-V para um site secundário

Este artigo descreve os componentes e os processos envolvidos ao replicar máquinas virtuais (VMs) de Hyper-V no local em clouds do System Center Virtual Machine Manager (VMM) para um site de VMM secundário com o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.


## <a name="architectural-components"></a>Componentes da arquitetura

A tabela seguinte e o gráfico fornecem uma visão geral dos componentes utilizados para a replicação de Hyper-V para um site secundário.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Subscrição do Azure | Criar um cofre dos Serviços de Recuperação na subscrição do Azure para orquestrar e gerir a replicação entre localizações do VMM.
**Servidor VMM** | Precisa de uma localização primária e secundária do VMM. | Recomenda-se um servidor VMM no site primário e um no site secundário.
**Servidor Hyper-V** |  Um ou mais servidores de anfitriões Hyper-V nas clouds do VMM primárias e secundárias. | Os dados são replicados entre os servidores anfitrião primário e secundário Hyper-V através de LAN ou VPN mediante a utilização de Kerberos ou da autenticação de certificados.  
**VMs de Hyper-V** | No servidor de anfitrião Hyper-V. | O servidor de anfitrião de origem deve ter, pelo menos, uma VM que queira replicar.

**No local para a arquitetura de no local**

![Local para local](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Processo de replicação

1. Quando a replicação inicial é acionada, uma [instantâneo da VM de Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) instantâneo.
2. Discos rígidos virtuais na VM são replicados um de cada, para a localização secundária.
3. Se ocorrerem alterações de disco enquanto a replicação inicial está em curso, o controlador de replicação de réplica do Hyper-V controla as alterações como registos de replicação de Hyper-V (. hrl). Estes ficheiros de registo estão localizados na mesma pasta que os discos. Cada disco tem um ficheiro. hrl associado que é enviado para a localização secundária. Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso.
4. Quando a replicação inicial estiver concluída, o instantâneo VM é eliminado e começa a replicação de diferenças.
5. As alterações de disco delta no registo são sincronizadas e unidas ao disco principal.


## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

- Pode efetuar a ativação pós-falha de uma máquina individual ou criar planos de recuperação, para orquestrar a ativação pós-falha de várias máquinas.
- Pode executar uma ativação pós-falha planeada ou não planeada entre sites no local. Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados.
    - Se executar uma ativação pós-falha não planeada para um site secundário, depois das máquinas de ativação pós-falha na localização secundária não estão protegidas.
    - Se tiver executado uma ativação pós-falha planeada, as máquinas na localização secundária estão protegidas após a ativação pós-falha.
- Após a ativação pós-falha inicial é executada, confirmá-lo, para começar a aceder à carga de trabalho da VM de réplica.
- Quando o local primário estiver novamente disponível, pode efetuar a reativação pós-falha.
    - Iniciar replicação inversa, para iniciar a replicação do site secundário para o primário. A replicação inversa coloca as máquinas virtuais num estado protegido, mas a localização ativa continua a ser o datacenter secundário.
    - Para que o site primário volte a ser a localização ativa, inicie uma ativação pós-falha planeada do site secundário para o primário, seguida de outra replicação inversa.



## <a name="next-steps"></a>Passos Seguintes


Siga [deste tutorial](hyper-v-vmm-disaster-recovery.md) para ativar a replicação de Hyper-V entre nuvens do VMM.
