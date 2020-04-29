---
title: Eliminar uma solução Azure VMware pela CloudSimple Private Cloud
description: Descreve como eliminar uma CloudSimple Private Cloud.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024760"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Eliminar uma nuvem privada CloudSimple

A CloudSimple oferece a flexibilidade para eliminar uma Nuvem Privada.  Uma Nuvem Privada consiste em um ou mais aglomerados de vSphere. Cada aglomerado pode ter 3 a 16 nós. Quando eliminar uma Nuvem Privada, todos os clusters serão eliminados.

## <a name="before-you-begin"></a>Antes de começar

A eliminação de uma nuvem privada elimina toda a Nuvem Privada.  Todos os componentes da Nuvem Privada serão eliminados.  Se quiser manter algum dos dados, certifique-se de que tem os dados no local de armazenamento ou armazenamento azure.

Os componentes de uma Nuvem Privada incluem:

* CloudSimple Nodes
* Máquinas virtuais
* VLANs/Sub-redes
* Todos os dados do utilizador armazenados na Nuvem Privada
* Todos os anexos de regras de firewall a uma VLAN/Subnet

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-a-private-cloud"></a>Eliminar uma Cloud Privada

1. [Aceda ao portal CloudSimple](access-cloudsimple-portal.md).

2. Abra a página **recursos.**

3. Clique na Nuvem Privada que pretende eliminar

4. Na página resumo, clique em **Apagar**.

    ![Eliminar a nuvem privada](media/delete-private-cloud.png)

5. Na página de confirmação, introduza o nome da Nuvem Privada e clique em **Eliminar**. 

    ![Eliminar nuvem privada - confirme](media/delete-private-cloud-confirm.png)

A Nuvem Privada está marcada para a eliminação.  O processo de eliminação começa após três horas e elimina a Nuvem Privada.

> [!CAUTION]
> Os nódosos devem ser apagados após a eliminação da Nuvem Privada.  A medição dos nódosos continuará até que os nódosos sejam eliminados da sua subscrição.

## <a name="next-steps"></a>Passos seguintes

* [Eliminar nós](delete-nodes.md)
