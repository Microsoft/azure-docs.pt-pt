---
title: 'Configure a política personalizada do IPsec para a Azure Virtual WAN: Portal / Microsoft Docs'
description: Saiba como configurar a política personalizada do IPsec para o Azure Virtual WAN utilizando o portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 0ea4523d1558f6887e1aef344198026591dac617
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84752620"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Configure uma política personalizada do IPsec para o WAN virtual usando o portal

Pode configurar a política personalizada do IPsec para O WAN Virtual no portal Azure. As políticas personalizadas são úteis quando pretende que ambos os lados (no local e gateway Azure VPN) utilizem as mesmas definições para a Fase 1 e fase 2 do IKE.

## <a name="working-with-custom-policies"></a>Trabalhar com políticas personalizadas

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Configure uma política

1. **Localize o centro virtual.** Num browser, navegue para o [Portal do Azure](https://aka.ms/azurevirtualwanpreviewfeatures) e inicie sessão com a sua conta do Azure. Localize o centro virtual para o seu site.
2. **Selecione o site VPN**. Na página do hub, selecione o VPN Site para o qual pretende configurar uma política personalizada.

   ![selecionar](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Editar a ligação VPN**. A partir do **menu Contexto** **...**, selecione **Editar Ligação VPN**.

   ![editar](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Configure as definições**. Na página **de ligação Editar VPN,** configure as definições das definições. **Selecione Guardar** para guardar as suas definições.

   ![configurar e salvar](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).