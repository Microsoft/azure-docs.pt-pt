---
title: Recuperação de desastres de arquitetura-hiper-V para um local secundário com recuperação do site Azure
description: Este artigo fornece uma visão geral da arquitetura para recuperação de desastres de Hiper-V V No local para um site de VMM do Centro de Sistema secundário com Recuperação do Site Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 3e81e353d2912f56a932ce118a0424e45e758df7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133008"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Arquitetura - Replicação hiper-V para um local secundário

Este artigo descreve os componentes e os processos envolvidos ao replicar máquinas virtuais (VMs) de Hyper-V no local em clouds do System Center Virtual Machine Manager (VMM) para um site de VMM secundário com o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.
a

## <a name="architectural-components"></a>Componentes da arquitetura

A tabela e o gráfico que se aseguir proporcionam uma visão de alto nível dos componentes utilizados para a replicação hyper-V para um local secundário.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Subscrição do Azure | Criar um cofre dos Serviços de Recuperação na subscrição do Azure para orquestrar e gerir a replicação entre localizações do VMM.
**Servidor VMM** | Precisa de uma localização primária e secundária do VMM. | Recomenda-se um servidor VMM no site primário e um no site secundário.
**Servidor Hyper-V** |  Um ou mais servidores de anfitriões Hyper-V nas clouds do VMM primárias e secundárias. | Os dados são replicados entre os servidores anfitrião primário e secundário Hyper-V através de LAN ou VPN mediante a utilização de Kerberos ou da autenticação de certificados.  
**VMs de Hyper-V** | No servidor de anfitrião Hyper-V. | O servidor de anfitrião de origem deve ter, pelo menos, uma VM que queira replicar.

**No local da arquitetura no local**

![Local para local](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Processo de replicação

1. Quando a replicação inicial é desencadeada, é tirada uma fotografia de [instantâneo de VM Hiper-V.](https://technet.microsoft.com/library/dd560637.aspx)
2. Os discos rígidos virtuais no VM são replicados um a um, para a localização secundária.
3. Se ocorrerem alterações no disco durante a replicação inicial, o Rastreador de Replicação de Replicação de Réplica seleções Hyper-V rastreia as alterações à medida que os registos de replicação Hyper-V (.hrl). Estes ficheiros de registo estão localizados na mesma pasta que os discos. Cada disco tem um ficheiro .hrl associado que é enviado para o local secundário. Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso.
4. Quando a replicação inicial termina, o instantâneo VM é eliminado e a replicação delta começa.
5. As alterações de disco delta no registo são sincronizadas e unidas ao disco principal.


## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

- Pode fazer a ativação pós-falha de uma máquina individual ou criar planos de recuperação para orquestrar a ativação pós-falha de várias máquinas.
- Executa uma ativação pós-falha planeada ou não planeada entre os sites no local. Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados.
    - Se executar uma falha não planeada num local secundário, depois de as máquinas de failover no local secundário não estiverem protegidas.
    - Se tiver executado uma ativação pós-falha planeada, as máquinas na localização secundária estão protegidas após a ativação pós-falha.
- Após as falhas iniciais, compromete-se a começar a aceder à carga de trabalho a partir da réplica VM.
- Quando a localização principal estiver novamente disponível, pode falhar.
    - Inicia-se a replicação inversa, para começar a replicar do local secundário para o primário. A replicação inversa coloca as máquinas virtuais num estado protegido, mas a localização ativa continua a ser o datacenter secundário.
    - Para que o site primário volte a ser a localização ativa, inicie uma ativação pós-falha planeada do site secundário para o primário, seguida de outra replicação inversa.



## <a name="next-steps"></a>Passos seguintes


Siga [este tutorial](hyper-v-vmm-disaster-recovery.md) para permitir a replicação hyper-V entre nuvens VMM.
