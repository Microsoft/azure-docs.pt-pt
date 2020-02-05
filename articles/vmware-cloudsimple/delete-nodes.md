---
title: Eliminar nós para Soluções VMware (AVS) - Azure
description: Saiba como eliminar os nódosos do seu VMWare com a implementação do AVS
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 30d128d6bd2f2e1e2705a7b742f02d11fd947a03
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024743"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-avs"></a>Eliminar os nódosos da Solução Azure VMware pela AVS

Os nódosos AVS são medidos uma vez criados. Os nódosos devem ser eliminados para parar a medição dos nódosos. Apaga os nódosos que não são utilizados no portal Azure.

## <a name="before-you-begin"></a>Antes de começar

Um nó só pode ser eliminado nas seguintes condições:

* Uma Nuvem Privada AVS criada com os nódosos é eliminada. Para eliminar uma Nuvem Privada AVS, consulte [Delete a Azure VMware Solution by AVS Private Cloud](delete-private-cloud.md).
* O nó foi removido da Nuvem Privada AVS, diminuindo a Nuvem Privada AVS. Para encolher uma Nuvem Privada AVS, consulte [AVS Solução VMware Shrink Azure by AVS Private Cloud](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-avs-node"></a>Eliminar o nó AVS

1. Selecione **Todos os serviços**.

2. Pesquisa por **Nódos AVS**.

   ![Pesquisar avs nodes](media/create-cloudsimple-node-search.png)

3. Selecione **Nódosos AVS**.

4. Selecione os nódosos que não pertencem a uma Nuvem Privada AVS para apagar. A coluna **AVS PRIVATE CLOUD NAME** mostra o nome AVS Private Cloud a que um nó pertence. Se um nó não for usado por uma Nuvem Privada AVS, o valor estará vazio. 

    ![Selecione nódosos AVS](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Apenas os nódosos que não fazem parte da Nuvem Privada AVS podem ser eliminados.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a Nuvem Privada AVS](cloudsimple-private-cloud.md)
