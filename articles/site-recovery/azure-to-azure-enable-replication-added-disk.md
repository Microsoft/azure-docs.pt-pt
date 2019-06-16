---
title: Ativar a replicação para um disco adicionado a uma VM do Azure replicadas pelo Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como ativar a replicação para um disco adicionado a uma VM do Azure que está ativada para a recuperação após desastre com o Azure Site Recovery
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: asgang
ms.openlocfilehash: 69122ffe9cefa3e1b9c6c8fbadfa80492ebebbde
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64928066"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Ativar a replicação para um disco adicionado a uma VM do Azure


Este artigo descreve como ativar a replicação para discos de dados que são adicionados a uma VM do Azure que já está ativada para recuperação após desastre para outra região do Azure, utilizando [do Azure Site Recovery](site-recovery-overview.md).

Ativar a replicação para um disco de que adicionar a uma VM é suportada para VMs do Azure com discos geridos.

Quando adiciona um novo disco a uma VM do Azure que está a replicar para outra região do Azure, ocorre o seguinte:

-   Estado de funcionamento de replicação para a VM mostra um aviso e uma nota no portal do informa que um ou mais discos estão disponíveis para proteção.
-   Se ativar a proteção para os discos adicionados, o aviso desaparece após a replicação inicial do disco.
-   Se optar por não ativar a replicação para o disco, pode optar por ignorar o aviso.

![Novo disco adicionado](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Antes de começar

Este artigo pressupõe que já tiver definido a recuperação após desastre para a VM para a qual estiver a adicionar o disco. Caso contrário, siga os [tutorial de recuperação após desastre do Azure](azure-to-azure-tutorial-enable-replication.md). 

## <a name="enable-replication-for-an-added-disk"></a>Ativar a replicação para um disco adicionado 

Para ativar a replicação para um disco adicionado, faça o seguinte:

1. No cofre > **itens replicados**, clique na VM à qual adicionou o disco.
2. Clique em **discos**e, em seguida, selecione o disco de dados para o qual pretende ativar a replicação (estes discos têm um **não protegido** Estado).
3.  Na **detalhes do disco**, clique em **ativar a replicação**.

    ![Ativar a replicação para o disco adicionado](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

Depois da tarefa de replicação de ativação é executada e a replicação inicial for concluída, o aviso do Estado de funcionamento de replicação para o problema de disco é removido.



# <a name="next-steps"></a>Passos Seguintes

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre como executar uma ativação pós-falha de teste.
