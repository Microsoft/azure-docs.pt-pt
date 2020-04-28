---
title: 'Configure a política personalizada do IPsec para O WAN Virtual Azure: Portal [ Microsoft Docs'
description: Saiba como configurar a política personalizada de IPsec para o Azure Virtual WAN utilizando o portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73515749"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Configure uma política personalizada de IPsec para O WAN Virtual usando o portal

Pode configurar a política personalizada de IPsec para O WAN Virtual no portal Azure. As políticas personalizadas são úteis quando deseja que ambos os lados (no local e gateway Azure VPN) utilizem as mesmas definições para a fase 1 ike e a fase 2 do IKE.

## <a name="working-with-custom-policies"></a>Trabalhar com políticas personalizadas

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Configurar uma política

1. **Localize o centro virtual.** Num browser, navegue para o [Portal do Azure](https://aka.ms/azurevirtualwanpreviewfeatures) e inicie sessão com a sua conta do Azure. Localize o centro virtual para o seu site.
2. **Selecione o site VPN**. A partir da página do hub, selecione o Site VPN para o qual pretende configurar uma política personalizada.

   ![selecionar](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Editar a ligação VPN**. A partir do **menu Contexto** **...**, selecione **Editar Ligação VPN**.

   ![editar](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Configure as definições**. Na página de **ligação VPN de edição,** configure as definições das definições. Selecione **Guardar** para guardar as suas definições.

   ![configurar e salvar](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).