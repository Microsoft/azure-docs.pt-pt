---
title: Eliminar os nó de nodes para VMware Solution by CloudSimple - Azure
description: Saiba como eliminar nós do seu VMWare com implementação CloudSimple. Os nós CloudSimple são medidos. Elimine os nós que não são utilizados do portal Azure.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 569bc6350b1bfa01228d49d28a1d12e2ab62f6f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88142269"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>Eliminar os nódes da Solução VMware Azure por CloudSimple

Os nós CloudSimple são medidos uma vez que são criados.  Os nós devem ser apagados para parar a medição dos nós.  Elimina os nós que não são utilizados do portal Azure.

## <a name="before-you-begin"></a>Antes de começar

Um nó só pode ser eliminado nas seguintes condições:

* Uma Nuvem Privada criada com os nós é eliminada.  Para eliminar uma nuvem privada, consulte [Eliminar uma Solução VMware Azure por CloudSimple Private Cloud](delete-private-cloud.md).
* O nó foi removido da Nuvem Privada diminuindo a Nuvem Privada.  Para encolher uma nuvem privada, consulte [Shrink Azure VMware Solution by CloudSimple Private Cloud](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-cloudsimple-node"></a>Eliminar nó CloudSimple

1. Selecione **Todos os serviços**.

2. Procure por **Nóns CloudSimple**.

   ![Pesquisar CloudSimple Nosdes](media/create-cloudsimple-node-search.png)

3. Selecione **CloudSimple Nós**.

4. Selecione nós que não pertencem a uma Nuvem Privada para apagar.  **Coluna PRIVATE CLOUD NAME** mostra o nome private Cloud ao qual um nó pertence.  Se um nó não for utilizado por uma Nuvem Privada, o valor estará vazio. 

    ![Selecione CloudSimple Nosdes](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Apenas os nós que não fazem parte da Nuvem Privada podem ser eliminados.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a Nuvem Privada](cloudsimple-private-cloud.md)
