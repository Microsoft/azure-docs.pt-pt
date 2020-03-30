---
title: Excluir discos VM de VMware da recuperação de desastres para Azure com recuperação do site Azure
description: Como excluir discos VMware VM da replicação para Azure com recuperação do site Azure.
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: cd54da5ee01206e576157435135065189bfb8035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495362"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>Excluir discos da replicação VMware VM para o Azure

Este artigo descreve como excluir discos ao replicar VMware VMs para Azure para recuperação de desastres. É melhor excluir discos da replicação por uma série de razões:

- Certifique-se de que os dados não importantes no disco excluído não são replicados.
- Otimize a largura de banda de replicação consumida, ou os recursos do lado do alvo, excluindo discos que não precisa de replicar.
- Poupe recursos de armazenamento e rede não replicando dados de que não precisa.

Antes de excluir discos de replicação:

- [Saiba mais](exclude-disks-replication.md) sobre a exclusão dos discos.
- Reveja [cenários e](exclude-disks-replication.md#typical-scenarios) [exemplos típicos](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) que mostrem como excluir um disco afeta a replicação, a falha e o recuo.

## <a name="before-you-start"></a>Antes de começar

 Note o seguinte antes de começar:

- **Replicação**: Por defeito, todos os discos de uma máquina são replicados.
- **Tipo de disco:** Apenas os discos básicos podem ser excluídos da replicação. Não é possível excluir discos do sistema operativo ou discos dinâmicos.
- **Serviço de mobilidade**: Para excluir um disco de replicação, tem de instalar manualmente o serviço mobility na máquina antes de ativar a replicação. Não é possível utilizar a instalação push, uma vez que este método instala o serviço mobility num VM apenas após a replicação estar ativada.  
- **Adicionar/remover/excluir discos**: Depois de ativar a replicação, não pode adicionar/remover/excluir discos para replicação. Se pretender adicionar/remover ou excluir discos, tem de desativar a proteção da máquina e depois ativa-la novamente.
- **Failover**: Depois de falhar, se falhar em aplicações, precisa de discos excluídos para funcionar, precisa de criar esses discos manualmente. Em alternativa, pode integrar a automatização azure num plano de recuperação, para criar o disco durante a falha da máquina.
- **Failback-Windows**: Quando falha no seu site após falha, os discos windows que cria manualmente em Azure não são reemprimeiros. Por exemplo, se falhar em três discos e criar dois discos diretamente nos VMs Azure, apenas os três discos que foram falhados serão refalhados.
- **Failback-Linux**: Para o failback das máquinas Linux, os discos que cria manualmente em Azure são falhados. Por exemplo, se falhar em três discos e criar dois discos diretamente em VMs Azure, todos os cinco serão refalhados. Não é possível excluir discos que foram criados manualmente no backback, ou na reproteção de VMs.



## <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

1. Quando ativa a [replicação](site-recovery-hyper-v-site-to-azure.md) de um VMware VM, depois de selecionar os VMs que pretende replicar, na**página** de propriedades de configuração de propriedades de **replicação** > **Properties** > Enable, reveja a coluna **De Discos para Replicar.** Por predefinição, todos os discos são selecionados para replicação.
2. Se não quiser replicar um disco específico, em **Discos para replicar** a seleção para quaisquer discos que queira excluir. 

    ![Excluir discos da replicação](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>Passos seguintes
Depois da implementação estar instalada e em execução, [saiba mais](failover-failback-overview.md) sobre os diferentes tipos de ativação pós-falha.
