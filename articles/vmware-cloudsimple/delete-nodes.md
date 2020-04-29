---
title: Eliminar os nódosos para A Solução VMware pela CloudSimple - Azure
description: Saiba como eliminar os nódosos do seu VMWare com a implementação CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 122e0636f54e066ae86ed2d19cefe5863b026293
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024743"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>Eliminar os nódosos da Solução Azure VMware pela CloudSimple

Os nódosos CloudSimple são medidos assim que são criados.  Os nódosos devem ser eliminados para parar a medição dos nódosos.  Apaga os nódosos que não são utilizados no portal Azure.

## <a name="before-you-begin"></a>Antes de começar

Um nó só pode ser eliminado nas seguintes condições:

* Uma Nuvem Privada criada com os nódosos é eliminada.  Para eliminar uma nuvem privada, consulte [Delete a Azure VMware Solution by CloudSimple Private Cloud](delete-private-cloud.md).
* O nó foi removido da Nuvem Privada diminuindo a Nuvem Privada.  Para encolher uma nuvem privada, consulte [A Solução Shrink Azure VMware by CloudSimple Private Cloud](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-cloudsimple-node"></a>Eliminar o nó CloudSimple

1. Selecione **Todos os serviços**.

2. Procure por **Nodes CloudSimple**.

   ![Pesquisar narizes simples cloudsimple](media/create-cloudsimple-node-search.png)

3. Selecione **CloudSimple Nodes**.

4. Selecione os nódosos que não pertencem a uma Nuvem Privada para apagar.  **A** coluna PRIVATE CLOUD NAME mostra o nome Cloud Privado a que pertence um nó.  Se um nó não for usado por uma Nuvem Privada, o valor estará vazio. 

    ![Selecione CloudSimple Nodes](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Apenas os nódosos que não fazem parte da Nuvem Privada podem ser eliminados.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a Nuvem Privada](cloudsimple-private-cloud.md)
