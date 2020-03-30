---
title: Provision nodes para VMware Solution by CloudSimple - Azure
description: Saiba como adicionar nódosos ao seu VMWare com implementação CloudSimple
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eb033425d18b472c9da1a2d6a1bb6f166702905e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024811"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>Provision nodes para Azure VMware Solution by CloudSimple

Provisões no portal Azure. Em seguida, você pode configurar a capacidade pay-as-you para o seu ambiente de nuvem privada CloudSimple.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no portal [https://portal.azure.com](https://portal.azure.com)Azure em .

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>Adicione um nó à sua nuvem privada CloudSimple

1. Selecione **Todos os serviços**.
2. Procure por **Nodes CloudSimple**.

   ![Pesquisar narizes simples cloudsimple](media/create-cloudsimple-node-search.png)

3. Selecione **CloudSimple Nodes**.
4. Clique em **Adicionar** para criar nódosos.

    ![Adicione nódosos CloudSimple](media/create-cloudsimple-node-add.png)

5. Selecione a subscrição onde pretende fornecer os nódosos CloudSimple.
6. Selecione o grupo de recursos para os nódosos. Para adicionar um novo grupo de recursos, clique em **Criar Novo**.
7. Introduza o prefixo para identificar os nódosos.
8. Selecione a localização para os recursos do nó.
9. Selecione a localização dedicada para hospedar os recursos do nó.
10. Selecione o tipo de [nó](cloudsimple-node.md).
11. Selecione o número de nós para a provisão.
12. Selecione **Rever + Criar**.
13. Reveja as definições. Para modificar quaisquer definições, clique em **Anterior**.
14. Selecione **Criar**.

## <a name="next-steps"></a>Passos seguintes

* [Criar nuvem privada](create-private-cloud.md)
