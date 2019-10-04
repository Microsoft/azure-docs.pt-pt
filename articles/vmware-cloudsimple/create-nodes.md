---
title: Provisionar nós para solução VMware por CloudSimple-Azure
description: Saiba como adicionar nós ao seu VMWare com a implantação do CloudSimple
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eb033425d18b472c9da1a2d6a1bb6f166702905e
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828168"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>Provisionar nós para a solução do Azure VMware por CloudSimple

Provisionar nós no portal do Azure. Em seguida, você pode configurar a capacidade paga conforme o uso para seu ambiente de nuvem privada do CloudSimple.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>Adicionar um nó à sua nuvem privada do CloudSimple

1. Selecione **Todos os serviços**.
2. Procure **nós CloudSimple**.

   ![Pesquisar nós do CloudSimple](media/create-cloudsimple-node-search.png)

3. Selecione **nós CloudSimple**.
4. Clique em **Adicionar** para criar nós.

    ![Adicionar nós CloudSimple](media/create-cloudsimple-node-add.png)

5. Selecione a assinatura em que você deseja provisionar nós CloudSimple.
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

* [Criar nuvem privada](create-private-cloud.md)
