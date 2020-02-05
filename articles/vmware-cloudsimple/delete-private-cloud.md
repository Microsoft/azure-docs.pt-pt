---
title: Eliminar uma Nuvem Privada De Soluções VMware Azure (AVS)
description: Descreve como eliminar uma Nuvem Privada AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 77cbfb19c3861bac517142f7491e6b1a5fb4ca27
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024760"
---
# <a name="delete-an-avs-private-cloud"></a>Eliminar uma Nuvem Privada AVS

A AVS fornece a flexibilidade para eliminar uma Nuvem Privada AVS. Uma Nuvem Privada AVS é composta por um ou mais aglomerados vSphere. Cada aglomerado pode ter 3 a 16 nós. Quando eliminar uma Nuvem Privada AVS, todos os clusters serão eliminados.

## <a name="before-you-begin"></a>Antes de começar

A eliminação de uma Nuvem Privada AVS elimina toda a Nuvem Privada AVS. Todos os componentes da Nuvem Privada AVS serão eliminados. Se quiser manter algum dos dados, certifique-se de que tem os dados no local de armazenamento ou armazenamento azure.

Os componentes de uma Nuvem Privada AVS incluem:

* Nódosos AVS
* Virtual Machines
* VLANs/Subnets
* Todos os dados do utilizador armazenados na Nuvem Privada AVS
* Todos os anexos de regras de firewall a uma VLAN/Subnet

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-an-avs-private-cloud"></a>Eliminar uma Nuvem Privada AVS

1. [Aceda ao portal AVS.](access-cloudsimple-portal.md)

2. Abra a página **recursos.**

3. Clique na Nuvem Privada AVS que pretende eliminar

4. Na página resumo, clique em **Apagar**.

    ![Eliminar avs nuvem privada](media/delete-private-cloud.png)

5. Na página de confirmação, introduza o nome da Nuvem Privada AVS e clique em **Eliminar**. 

    ![Eliminar avs nuvem privada - confirme](media/delete-private-cloud-confirm.png)

A Nuvem Privada AVS está marcada para a eliminação. O processo de eliminação começa após três horas e elimina a Nuvem Privada AVS.

> [!CAUTION]
> Os nódosos devem ser eliminados após a eliminação da Nuvem Privada AVS. A medição dos nódosos continuará até que os nódosos sejam eliminados da sua subscrição.

## <a name="next-steps"></a>Passos seguintes

* [Apagar os nódosos](delete-nodes.md)
