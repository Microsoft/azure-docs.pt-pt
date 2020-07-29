---
title: Elimine uma Solução VMware Azure por CloudSimple Private Cloud
description: Descreve como eliminar uma CloudSimple Private Cloud.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77024760"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Excluir uma nuvem Simple Nuvem

O CloudSimple proporciona a flexibilidade para eliminar uma Nuvem Privada.  Uma Nuvem Privada consiste de um ou mais aglomerados vSphere. Cada aglomerado pode ter 3 a 16 nós. Quando eliminar uma Nuvem Privada, todos os clusters serão eliminados.

## <a name="before-you-begin"></a>Before you begin

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

## <a name="next-steps"></a>Próximos passos

* [Eliminar nós](delete-nodes.md)
