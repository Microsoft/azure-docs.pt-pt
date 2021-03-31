---
title: Recuperação de desastres arquitetura-Hiper-V para um local secundário com recuperação do local de Azure
description: Este artigo fornece uma visão geral da arquitetura para a recuperação de desastres de Hiper-V VMs no local para um site de VMM do Centro de Sistema secundário com recuperação do local de Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 703a6afdc12c8a9863ff0f480ec7a577ec31ef77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87496003"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Arquitetura - Replicação hiper-V para um local secundário

Este artigo descreve os componentes e os processos envolvidos ao replicar máquinas virtuais (VMs) de Hyper-V no local em clouds do System Center Virtual Machine Manager (VMM) para um site de VMM secundário com o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.
um

## <a name="architectural-components"></a>Componentes da arquitetura

A tabela e o gráfico seguintes proporcionam uma visão de alto nível dos componentes utilizados para a replicação do Hiper-V para um local secundário.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Subscrição do Azure | Criar um cofre dos Serviços de Recuperação na subscrição do Azure para orquestrar e gerir a replicação entre localizações do VMM.
**Servidor VMM** | Precisa de uma localização primária e secundária do VMM. | Recomenda-se um servidor VMM no site primário e um no site secundário.
**Servidor Hyper-V** |  Um ou mais servidores de anfitriões Hyper-V nas clouds do VMM primárias e secundárias. | Os dados são replicados entre os servidores anfitrião primário e secundário Hyper-V através de LAN ou VPN mediante a utilização de Kerberos ou da autenticação de certificados.  
**VMs Hyper-V** | No servidor de anfitrião Hyper-V. | O servidor de anfitrião de origem deve ter, pelo menos, uma VM que queira replicar.

**No local para a arquitetura no local**

![Diagrama mostrando no local a proteção no local.](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Processo de replicação

1. Quando a replicação inicial é desencadeada, é tirada uma imagem [instantânea Hyper-VM.](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560637(v=ws.10))
2. Os discos rígidos virtuais no VM são replicados um a um, para a localização secundária.
3. Se as alterações do disco ocorrerem enquanto a replicação inicial está em andamento, o Localizador de Replicação de Réplica Hiper-V rastreia as alterações como registos de replicação de Hiper-V (.hrl). Estes ficheiros de registo estão localizados na mesma pasta que os discos. Cada disco tem um ficheiro .hrl associado que é enviado para o local secundário. Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso.
4. Quando a replicação inicial termina, o instantâneo VM é eliminado e começa a replicação delta.
5. As alterações de disco delta no registo são sincronizadas e unidas ao disco principal.


## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

- Pode fazer a ativação pós-falha de uma máquina individual ou criar planos de recuperação para orquestrar a ativação pós-falha de várias máquinas.
- Executa uma ativação pós-falha planeada ou não planeada entre os sites no local. Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados.
    - Se efetuar uma falha não planeada para um local secundário, depois de as máquinas de failover na localização secundária não estiverem protegidas.
    - Se tiver executado uma ativação pós-falha planeada, as máquinas na localização secundária estão protegidas após a ativação pós-falha.
- Após a execução inicial do failover, compromete-o a começar a aceder à carga de trabalho a partir da réplica VM.
- Quando a localização primária estiver novamente disponível, pode falhar.
    - Inicia-se a replicação inversa, para começar a replicar-se do local secundário para o primário. A replicação inversa coloca as máquinas virtuais num estado protegido, mas a localização ativa continua a ser o datacenter secundário.
    - Para que o site primário volte a ser a localização ativa, inicie uma ativação pós-falha planeada do site secundário para o primário, seguida de outra replicação inversa.



## <a name="next-steps"></a>Passos seguintes


Siga [este tutorial](hyper-v-vmm-disaster-recovery.md) para permitir a replicação do Hiper-V entre as nuvens de VMM.
