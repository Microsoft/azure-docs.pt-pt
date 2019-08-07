---
title: Provisionar nós para solução VMware por CloudSimple-Azure
description: Saiba como adicionar nós ao seu VMWare com a implantação do CloudSimple
author: dikamath
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9eb02f04b5873e5906782a27ce833a724ceecfe3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812374"
---
# <a name="provision-nodes-for-vmware-solution-by-cloudsimple---azure"></a>Provisionar nós para solução VMware por CloudSimple-Azure

Provisionar nós no portal do Azure. Em seguida, você pode configurar a capacidade paga conforme o uso para seu ambiente de nuvem privada do CloudSimple.

## <a name="add-a-provisioned-node-to-your-cloudsimple-private-cloud"></a>Adicionar um nó provisionado à sua nuvem privada do CloudSimple

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
10. Selecione o tipo de nó. Você pode escolher a [opção CS28 ou CS36](cloudsimple-node.md). A última opção inclui a capacidade máxima de computação e memória.
11. Selecione o número de nós a serem provisionados.
12. Selecione **revisão + criar**.
13. Examine as configurações. Para modificar as configurações, clique em **anterior**.
14. Selecione **Criar**.

## <a name="next-steps"></a>Passos Seguintes

* [Criar nuvem privada](https://docs.azure.cloudsimple.com/create-private-cloud/)
