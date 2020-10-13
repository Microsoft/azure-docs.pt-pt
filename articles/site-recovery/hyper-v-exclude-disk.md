---
title: Excluir discos VM Hiper-V da recuperação de desastres para Azure com recuperação do local de Azure
description: Como excluir discos Hiper-V VM da replicação para Azure com Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 373cffe96119af4a2fc0d74e2090e6cc24dcaf8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86131253"
---
# <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

Este artigo descreve como excluir discos ao replicar Hiper-VMs para Azure. É melhor excluir os discos da replicação por uma série de razões:

- Certifique-se de que os dados não importantes do disco excluído não sejam replicados.
- Otimize a largura de banda de replicação consumida, ou os recursos do lado alvo, excluindo discos que não precisa de replicar.
- Guarde os recursos de armazenamento e rede não replicando dados de que não precisa.

Antes de excluir discos da replicação:

- [Saiba mais](exclude-disks-replication.md) sobre a exclusão dos discos.
- Rever cenários e [exemplos típicos](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) de [exclusão](exclude-disks-replication.md#typical-scenarios) que mostram como excluir um disco afeta a replicação, failover e failback.

## <a name="before-you-start"></a>Antes de começar

Note o seguinte antes de começar:

- **Replicação:** Por predefinição, todos os discos de uma máquina são replicados.
- **Tipo de disco:**
    - Pode excluir discos básicos da replicação.
    - Não é possível excluir discos do sistema operativo.
    - Recomendamos que não exclua discos dinâmicos. A Recuperação do Site não consegue identificar qual o VHD básico ou dinâmico no VM convidado.  Se não excluir todos os discos de volume dinâmicos dependentes, o disco dinâmico protegido torna-se um disco falhado num VM falhado e os dados desse disco não estão acessíveis.
- **Adicionar/remover/excluir discos**: Depois de ativar a replicação, não é possível adicionar/remover/excluir discos para replicação. Se pretender adicionar/remover ou excluir um disco, tem de desativar a proteção para o VM e, em seguida, ative-o novamente.
- **Falha:** Depois de falha, se falhado sobre as aplicações precisa de excluir discos para funcionar, é necessário criar esses discos manualmente. Em alternativa, pode integrar a automatização Azure num plano de recuperação, para criar o disco durante a falha da máquina.
- **Failback**: Quando falhas no teu local após a falha, os discos que criaste manualmente em Azure não são falhados. Por exemplo, se falhar em três discos e criar dois discos diretamente num VM Azure, apenas três discos que foram falhados são então falhados. Não é possível incluir discos que foram criados manualmente em failback, ou na replicação inversa de VMs.

## <a name="exclude-disks"></a>Excluir discos

1. Para excluir discos quando [ativar a replicação](./hyper-v-azure-tutorial.md) de um Hiper-VM, depois de selecionar os VMs que pretende replicar, na página **De Propriedades de Replicação**  >  **Properties**  >  **Configure properties** ativa, reveja os **Discos para replicar colunas.** Por predefinição, todos os discos são selecionados para replicação.
2. Se não quiser replicar um disco específico, em **Discos para replicar** a seleção de quaisquer discos que pretenda excluir. 

    ![Excluir discos da replicação](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>Passos seguintes
Depois da implementação estar instalada e em execução, [saiba mais](failover-failback-overview.md) sobre os diferentes tipos de ativação pós-falha.
