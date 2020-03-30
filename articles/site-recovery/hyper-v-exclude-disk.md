---
title: Excluir discos VM Hiper-V da recuperação de desastres para Azure com recuperação do site Azure
description: Como excluir discos VM Hiper-V da replicação para Azure com recuperação do site Azure.
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 50fb6da2905b2ae27547f25cce3d7a76ca7976b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498127"
---
# <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

Este artigo descreve como excluir discos ao replicar VMs Hiper-V para Azure. É melhor excluir discos da replicação por uma série de razões:

- Certifique-se de que os dados não importantes no disco excluído não são replicados.
- Otimize a largura de banda de replicação consumida, ou os recursos do lado do alvo, excluindo discos que não precisa de replicar.
- Poupe recursos de armazenamento e rede não replicando dados de que não precisa.

Antes de excluir discos de replicação:

- [Saiba mais](exclude-disks-replication.md) sobre a exclusão dos discos.
- Reveja [cenários e](exclude-disks-replication.md#typical-scenarios) [exemplos típicos](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) que mostrem como excluir um disco afeta a replicação, a falha e o recuo.

## <a name="before-you-start"></a>Antes de começar

Note o seguinte antes de começar:

- **Replicação**: Por padrão, todos os discos de uma máquina são replicados.
- **Tipo de disco:**
    - Pode excluir discos básicos da replicação.
    - Não é possível excluir discos do sistema operativo.
    - Recomendamos que não exclua discos dinâmicos. A Recuperação do Site não consegue identificar qual vHD é básico ou dinâmico no VM convidado.  Se não excluir todos os discos de volume dinâmicos dependentes, o disco dinâmico protegido torna-se um disco falhado num VM falhado, e os dados desse disco não são acessíveis.
- **Adicionar/remover/excluir discos**: Depois de ativar a replicação, não pode adicionar/remover/excluir discos para replicação. Se pretender adicionar/remover ou excluir um disco, tem de desativar a proteção do VM e, em seguida, ativa-lo novamente.
- **Failover**: Depois de falhar, se falhar em aplicações precisa excluir discos para funcionar, precisa de criar esses discos manualmente. Em alternativa, pode integrar a automatização azure num plano de recuperação, para criar o disco durante a falha da máquina.
- **Failback**: Quando falha no seu site após a falha, os discos que criou manualmente em Azure não são ressumidos. Por exemplo, se falhar em três discos e criar dois discos diretamente num VM Azure, apenas três discos que foram falhados são então falhados. Não é possível incluir discos que foram criados manualmente no failback, ou na replicação inversa de VMs.

## <a name="exclude-disks"></a>Excluir discos

1. Para excluir discos quando ativa a [replicação](site-recovery-hyper-v-site-to-azure.md) de um VM Hiper-V, depois de selecionar os VMs que pretende replicar, na**página** de propriedades de configuração de propriedades de **replicação** > **Properties** > Enable, reveja a coluna **De Discos para Replicar.** Por predefinição, todos os discos são selecionados para replicação.
2. Se não quiser replicar um disco específico, em **Discos para replicar** a seleção para quaisquer discos que queira excluir. 

    ![Excluir discos da replicação](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>Passos seguintes
Depois da implementação estar instalada e em execução, [saiba mais](failover-failback-overview.md) sobre os diferentes tipos de ativação pós-falha.
