---
title: Provision nodes para Soluções VMware (AVS) - Azure
description: Saiba como adicionar nódosos ao seu VMWare com implementação AVS
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 94ca681153f1e3ccd9ff628b41a9d0e547be5802
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024811"
---
# <a name="provision-nodes-for-azure-vmware-solutions-avs"></a>Nós de provisão para soluções Azure VMware (AVS)

Provisões no portal Azure. Depois, pode configurar a capacidade de pagamento para o seu ambiente de nuvem privada AVS.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-avs-private-cloud"></a>Adicione um nó à sua nuvem privada AVS

1. Selecione **Todos os serviços**.
2. Pesquisa por **Nódos AVS**.

   ![Pesquisar avs nodes](media/create-cloudsimple-node-search.png)

3. Selecione **Nódosos AVS**.
4. Clique em **Adicionar** para criar nós.

    ![Adicionar nódosos AVS](media/create-cloudsimple-node-add.png)

5. Selecione a subscrição onde pretende fornecer os nódosos AVS.
6. Selecione o grupo de recursos para os nós. Para adicionar um novo grupo de recursos, clique em **criar novo**.
7. Insira o prefixo para identificar os nós.
8. Selecione o local dos recursos do nó.
9. Selecione o local dedicado para hospedar os recursos do nó.
10. Selecione o [tipo de nó](cloudsimple-node.md).
11. Selecione o número de nós a serem provisionados.
12. Selecione **revisão + criar**.
13. Examine as configurações. Para modificar as configurações, clique em **anterior**.
14. Selecione **Criar**.

## <a name="next-steps"></a>Passos seguintes

* [Criar nuvem privada AVS](create-private-cloud.md)
