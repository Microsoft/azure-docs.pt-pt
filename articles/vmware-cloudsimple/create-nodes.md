---
title: Aprovisionar nós para solução de VMware ao CloudSimple - Azure
description: Saiba como adicionar nós para o VMWare com a implementação de CloudSimple
author: dikamath
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 33de07663c91f12d4e10c4661b841cd2dbe5a162
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165263"
---
# <a name="provision-nodes-for-vmware-solution-by-cloudsimple---azure"></a>Aprovisionar nós para solução de VMware ao CloudSimple - Azure

Aprovisionar nós no portal do Azure. Em seguida, pode configurar pay as you aceda capacidade para o seu ambiente de nuvem privada CloudSimple.

## <a name="add-a-provisioned-node-to-your-cloudsimple-private-cloud"></a>Adicionar um nó aprovisionado a sua nuvem privada CloudSimple

1. Selecione **Todos os serviços**.
2. Procure **CloudSimple nós**.

   ![Procurar CloudSimple nós](media/create-cloudsimple-node-search.png)

3. Selecione **CloudSimple nós**.
4. Clique em **adicionar** criar nós.

    ![Adicionar nós CloudSimple](media/create-cloudsimple-node-add.png)

5. Selecione a subscrição em que pretende aprovisionar nós de CloudSimple.
6. Selecione o grupo de recursos para os nós. Para adicionar um novo grupo de recursos, clique em **criar novo**.
7. Introduza o prefixo para identificar os nós.
8. Selecione a localização para os recursos de nó.
9. Selecione a localização dedicada para alojar os recursos de nó.
10. Selecione o tipo de nó. Pode escolher o [opção CS28 ou CS36](cloudsimple-node.md). A última opção inclui a capacidade de computação e memória máxima.
11. Selecione o número de nós para aprovisionar.
12. Selecione **rever + criar**.
13. Reveja as definições. Para alterar as definições, clique em **Previous**.
14. Selecione **Criar**.

## <a name="next-steps"></a>Passos Seguintes

* [Criar nuvem privada](https://docs.azure.cloudsimple.com/create-private-cloud/)
