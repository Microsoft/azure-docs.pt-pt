---
title: Arquitetura para recuperação de desastres do Hyper-V em um site local secundário com Azure Site Recovery
description: Este artigo fornece uma visão geral da arquitetura de recuperação de desastres de VMs do Hyper-V locais para um site do VMM do System Center secundário com Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 2d8e9c3531e031538c593cfd60d83b4ae97b4f4c
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813744"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Arquitetura – replicação do Hyper-V para um site secundário

Este artigo descreve os componentes e os processos envolvidos ao replicar máquinas virtuais (VMs) de Hyper-V no local em clouds do System Center Virtual Machine Manager (VMM) para um site de VMM secundário com o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.


## <a name="architectural-components"></a>Componentes da arquitetura

A tabela e o gráfico a seguir fornecem uma exibição de alto nível dos componentes usados para replicação do Hyper-V para um site secundário.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Subscrição do Azure | Criar um cofre dos Serviços de Recuperação na subscrição do Azure para orquestrar e gerir a replicação entre localizações do VMM.
**Servidor VMM** | Precisa de uma localização primária e secundária do VMM. | Recomenda-se um servidor VMM no site primário e um no site secundário.
**Servidor Hyper-V** |  Um ou mais servidores de anfitriões Hyper-V nas clouds do VMM primárias e secundárias. | Os dados são replicados entre os servidores anfitrião primário e secundário Hyper-V através de LAN ou VPN mediante a utilização de Kerberos ou da autenticação de certificados.  
**VMs de Hyper-V** | No servidor de anfitrião Hyper-V. | O servidor de anfitrião de origem deve ter, pelo menos, uma VM que queira replicar.

**Arquitetura de local para local**

![Local para local](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Processo de replicação

1. Quando a replicação inicial é disparada, um instantâneo de [instantâneo da VM do Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) é obtido.
2. Os discos rígidos virtuais na VM são replicados um a um, para o local secundário.
3. Se ocorrerem alterações no disco enquanto a replicação inicial estiver em andamento, o controlador de replicação de réplica do Hyper-V acompanhará as alterações como logs de replicação do Hyper-V (. HRL). Esses arquivos de log estão localizados na mesma pasta que os discos. Cada disco tem um arquivo. HRL associado que é enviado para o local secundário. Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso.
4. Quando a replicação inicial é concluída, o instantâneo da VM é excluído e a replicação delta começa.
5. As alterações de disco delta no registo são sincronizadas e unidas ao disco principal.


## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

- Você pode fazer failover de um único computador ou criar planos de recuperação para orquestrar o failover de vários computadores.
- Você pode executar um failover planejado ou não planejado entre sites locais. Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados.
    - Se você executar um failover não planejado para um site secundário, depois que os computadores de failover no local secundário não estiverem protegidos.
    - Se tiver executado uma ativação pós-falha planeada, as máquinas na localização secundária estão protegidas após a ativação pós-falha.
- Após a execução do failover inicial, você o confirma, para começar a acessar a carga de trabalho da VM de réplica.
- Quando o local principal estiver disponível novamente, você poderá fazer failback.
    - Você inicia a replicação inversa para iniciar a replicação do site secundário para o primário. A replicação inversa coloca as máquinas virtuais num estado protegido, mas a localização ativa continua a ser o datacenter secundário.
    - Para que o site primário volte a ser a localização ativa, inicie uma ativação pós-falha planeada do site secundário para o primário, seguida de outra replicação inversa.



## <a name="next-steps"></a>Passos seguintes


Siga [este tutorial](hyper-v-vmm-disaster-recovery.md) para habilitar a replicação do Hyper-V entre nuvens do VMM.
