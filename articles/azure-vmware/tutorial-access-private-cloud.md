---
title: Tutorial - Aceda à sua nuvem privada
description: Saiba como aceder a uma nuvem privada Azure VMware Solution
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: f2af1cffda08bf4b9c62e63f32d36cc9bbd7024a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103494398"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>Tutorial: Aceda a uma nuvem privada Azure VMware Solution

A Azure VMware Solution não lhe permite gerir a sua nuvem privada com o seu vCenter no local. Terá de se ligar à solução VMware VCenter Azure através de uma caixa de salto. 

Neste tutorial, irá criar uma caixa de salto no grupo de recursos que criou no [tutorial anterior](tutorial-configure-networking.md) e assinará no Azure VMware Solution vCenter. Esta caixa de salto é uma máquina virtual (VM) do Windows na mesma rede virtual que criou.  Fornece acesso tanto ao vCenter como ao NSX Manager. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma máquina virtual Windows para acesso à Solução VMware Azure vCenter
> * Inscreva-se no vCenter a partir desta máquina virtual

## <a name="create-a-new-windows-virtual-machine"></a>Criar uma nova máquina virtual do Windows

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Ligue-se ao vCenter local da sua nuvem privada

1. A partir da caixa de salto, inscreva-se no vSphere Client com VMware vCenter SSO utilizando um nome de utilizador de administração em nuvem e verifique se a interface do utilizador é exibida com sucesso.

1. No portal Azure, selecione a sua nuvem privada e, em seguida, **Gerencie**  >  **a Identidade**. 

   Os URLs e as credenciais de utilizador para o visor privado de vCenter e NSX-T Manager.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Exiba urls e credenciais de cloud vCenter e NSX Manager." border="true" lightbox="media/tutorial-access-private-cloud/ss4-display-identity.png":::

1. Navegue para o VM que criou no passo anterior e ligue-se à máquina virtual. 

   Se precisar de ajuda para se ligar ao VM, consulte [a ligação a uma máquina virtual](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) para obter mais detalhes.

1. No Windows VM, abra um navegador e navegue para os URLs vCenter e NSX-T Manger em dois separadores. 

1. No separador vCenter, introduza `cloudadmin@vmcp.local` as credenciais de utilizador do passo anterior.

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Inscreva-se na nuvem privada vCenter." border="true":::

   :::image type="content" source="media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="portal vCenter." border="true":::

1. No segundo separador do navegador, inscreva-se no gestor NSX-T.

   :::image type="content" source="media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="No segundo separador do navegador, inscreva-se no gestor NSX-T." border="true":::



## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma máquina virtual windows para usar para ligar ao vCenter
> * Faça login no vCenter a partir da sua máquina virtual

Continue até ao próximo tutorial para aprender a criar uma rede virtual para configurar a gestão local para os seus clusters de nuvem privada.

> [!div class="nextstepaction"]
> [Criar uma Rede Virtual](tutorial-configure-networking.md)


