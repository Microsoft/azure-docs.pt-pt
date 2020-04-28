---
title: Ativar a replicação para um disco Azure VM adicionado na Recuperação do Site Azure
description: Este artigo descreve como permitir a replicação de um disco adicionado a um VM Azure que está habilitado para a recuperação de desastres com a Recuperação do Site Azure
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: 6cbbe63d7968816de78256f5a8408517bb8da278
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75973797"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Ativar a replicação de um disco adicionado a um VM Azure


Este artigo descreve como permitir a replicação de discos de dados que são adicionados a um VM Azure que já está habilitado para a recuperação de desastres para outra região do Azure, utilizando a [Recuperação do Site Azure.](site-recovery-overview.md)

Permitir a replicação de um disco que adiciona a um VM é suportado para VMs Azure com discos geridos.

Quando se adiciona um novo disco a um VM Azure que está a replicar-se noutra região do Azure, ocorre o seguinte:

-   A saúde de replicação para o VM mostra um aviso, e uma nota no portal informa-o de que um ou mais discos estão disponíveis para proteção.
-   Se ativar a proteção dos discos adicionados, o aviso desaparecerá após a replicação inicial do disco.
-   Se optar por não ativar a replicação do disco, pode selecionar para descartar o aviso.

![Novo disco adicionado](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Antes de começar

Este artigo assume que já estabeleceu uma recuperação de desastres para o VM ao qual está a adicionar o disco. Se não o fez, siga o tutorial de recuperação de [desastres do Azure até Azure.](azure-to-azure-tutorial-enable-replication.md)

## <a name="enable-replication-for-an-added-disk"></a>Ativar a replicação para um disco adicionado

Para permitir a replicação de um disco adicionado, faça o seguinte:

1. No cofre > **Itens Replicados,** clique no VM ao qual adicionou o disco.
2. Clique em **Discos**, e, em seguida, selecione o disco de dados para o qual pretende ativar a replicação (estes discos têm um estado **não protegido).**
3.  Em **Detalhes do disco,** clique em **ativar a replicação**.

    ![Ativar a replicação para o disco adicionado](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

Após o funcionar do trabalho de replicação ativa e os acabamentos iniciais de replicação, o aviso de saúde de replicação para a questão do disco é removido.



## <a name="next-steps"></a>Passos seguintes

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre executar um teste failover.
