---
title: Elimine uma Solução VMware Azure por CloudSimple Private Cloud
description: Saiba como eliminar uma CloudSimple Private Cloud. Quando eliminar uma Nuvem Privada, todos os clusters serão eliminados.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7db967955dc86db39db4dcb2b3a2baf8906efb20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97896265"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Excluir uma nuvem Simple Nuvem

O CloudSimple proporciona a flexibilidade para eliminar uma Nuvem Privada.  Uma Nuvem Privada consiste de um ou mais aglomerados vSphere. Cada aglomerado pode ter 3 a 16 nós. Quando eliminar uma Nuvem Privada, todos os clusters serão eliminados.

## <a name="before-you-begin"></a>Antes de começar

A eliminação de uma Nuvem Privada elimina toda a Nuvem Privada.  Todos os componentes da Nuvem Privada serão eliminados.  Se quiser guardar algum dos dados, certifique-se de que fez o back up para o armazenamento no local ou para o armazenamento do Azure.

Os componentes de uma Nuvem Privada incluem:

* Nó de NuvensImple
* Máquinas virtuais
* VLANs/Sub-redes
* Todos os dados do utilizador armazenados na Nuvem Privada
* Todas as regras de firewall ligam-se a uma VLAN/Subnet

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-a-private-cloud"></a>Eliminar uma Cloud Privada

1. [Aceda ao portal CloudSimple](access-cloudsimple-portal.md).

2. Abra a página **Recursos.**

3. Clique na Nuvem Privada que pretende eliminar

4. Na página do resumo, clique em **Eliminar**.

    ![Apagar nuvem privada](media/delete-private-cloud.png)

5. Na página de confirmação, insira o nome da Nuvem Privada e clique em **Apagar**. 

    ![Apagar nuvem privada - confirme](media/delete-private-cloud-confirm.png)

A Nuvem Privada está marcada para a eliminação.  O processo de eliminação começa após três horas e elimina a Nuvem Privada.

> [!CAUTION]
> Os nós devem ser apagados após a eliminação da Nuvem Privada.  A medição dos nós continuará até que os nós sejam eliminados da sua subscrição.

## <a name="next-steps"></a>Passos seguintes

* [Eliminar nós](delete-nodes.md)
