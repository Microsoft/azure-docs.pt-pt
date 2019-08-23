---
title: Excluir uma solução do Azure VMware da nuvem privada CloudSimple
description: Descreve como excluir uma nuvem privada do CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972811"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Excluir uma nuvem privada do CloudSimple

O CloudSimple fornece a flexibilidade para excluir uma nuvem privada.  Uma nuvem privada consiste em um ou mais clusters vSphere. Cada cluster pode ter de 3 a 16 nós. Quando você exclui uma nuvem privada, todos os clusters serão excluídos.

## <a name="before-you-begin"></a>Antes de começar

A exclusão de uma nuvem privada exclui toda a nuvem privada.  Todos os componentes da nuvem privada serão excluídos.  Se você quiser manter qualquer um dos dados, certifique-se de ter feito backup dos dados no armazenamento local ou no armazenamento do Azure.

Os componentes de uma nuvem privada incluem:

* Nós CloudSimple
* Máquinas virtuais
* VLANs/sub-redes
* Todos os dados de usuário armazenados na nuvem privada
* Todos os anexos de regra de firewall para uma VLAN/sub-rede

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-a-private-cloud"></a>Excluir uma nuvem privada

1. [Acesse o portal do CloudSimple](access-cloudsimple-portal.md).

2. Abra a página **recursos** .

3. Clique na nuvem privada que você deseja excluir

4. Na página Resumo, clique em **excluir**.

    ![Excluir nuvem privada](media/delete-private-cloud.png)

5. Na página confirmação, insira o nome da nuvem privada e clique em **excluir**. 

    ![Excluir nuvem privada-confirmar](media/delete-private-cloud-confirm.png)

A nuvem privada está marcada para exclusão.  O processo de exclusão é iniciado após três horas e exclui a nuvem privada.

> [!CAUTION]
> Os nós devem ser excluídos após a exclusão da nuvem privada.  A medição de nós continuará até que os nós sejam excluídos da sua assinatura.

## <a name="next-steps"></a>Passos seguintes

* [Excluir nós](delete-nodes.md)
