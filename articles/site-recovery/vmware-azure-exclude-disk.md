---
title: Excluir discos VMware VM da recuperação de desastres para Azure com recuperação do site Azure
description: Como excluir discos VMware VM da replicação para Azure com Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: c4842172ff181b5cdbe7f6fecf69da8755ae43fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86129881"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>Excluir discos da replicação VMware VM para Azure

Este artigo descreve como excluir discos ao replicar VMware VMs para Azure para recuperação de desastres. É melhor excluir os discos da replicação por uma série de razões:

- Certifique-se de que os dados não importantes do disco excluído não sejam replicados.
- Otimize a largura de banda de replicação consumida, ou os recursos do lado alvo, excluindo discos que não precisa de replicar.
- Guarde os recursos de armazenamento e rede não replicando dados de que não precisa.

Antes de excluir discos da replicação:

- [Saiba mais](exclude-disks-replication.md) sobre a exclusão dos discos.
- Rever cenários e [exemplos típicos](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) de [exclusão](exclude-disks-replication.md#typical-scenarios) que mostram como excluir um disco afeta a replicação, failover e failback.

## <a name="before-you-start"></a>Antes de começar

 Note o seguinte antes de começar:

- **Replicação:** Por defeito, todos os discos de uma máquina são replicados.
- **Tipo de** disco : Só os discos básicos podem ser excluídos da replicação. Não é possível excluir discos do sistema operativo ou discos dinâmicos.
- **Serviço de mobilidade**: Para excluir um disco de replicação, deve instalar manualmente o serviço de Mobilidade na máquina antes de permitir a replicação. Não é possível utilizar a instalação push, uma vez que este método instala o serviço mobility num VM apenas após a replicação estar ativada.  
- **Adicionar/remover/excluir discos**: Depois de ativar a replicação, não é possível adicionar/remover/excluir discos para replicação. Se pretender adicionar/remover ou excluir discos, tem de desativar a proteção da máquina e, em seguida, ative-a novamente.
- **Falha:** Depois de falha, se falhado sobre as aplicações precisa de discos excluídos para funcionar, é necessário criar esses discos manualmente. Em alternativa, pode integrar a automatização Azure num plano de recuperação, para criar o disco durante a falha da máquina.
- **Falha no Windows**: Quando falha no seu local após falha, os discos Windows que cria manualmente no Azure não são ressarcidas. Por exemplo, se falhar em três discos e criar dois discos diretamente nos VMs Azure, apenas os três discos que foram falhados serão ressarídos.
- **Failback-Linux**: Para o recuo das máquinas Linux, os discos que cria manualmente em Azure estão a falhar. Por exemplo, se falhar em três discos e criar dois discos diretamente nos VMs Azure, os cinco serão ressarados. Não é possível excluir discos que foram criados manualmente na falha, ou na reprotecção de VMs.



## <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

1. Quando [ativar a replicação](./hyper-v-azure-tutorial.md) de um VMware VM, depois de selecionar os VMs que pretende replicar, na página **De Replication**  >  **Properties**  >  **Configure properties,** reveja os Discos para replicar **a** coluna. Por predefinição, todos os discos são selecionados para replicação.
2. Se não quiser replicar um disco específico, em **Discos para replicar** a seleção de quaisquer discos que pretenda excluir. 

    ![Excluir discos da replicação](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>Passos seguintes
Depois da implementação estar instalada e em execução, [saiba mais](failover-failback-overview.md) sobre os diferentes tipos de ativação pós-falha.
