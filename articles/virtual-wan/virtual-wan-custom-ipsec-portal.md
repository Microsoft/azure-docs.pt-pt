---
title: 'Configure a política personalizada do IPsec para a Azure Virtual WAN: Portal / Microsoft Docs'
description: Saiba como configurar a política personalizada do IPsec para o Azure Virtual WAN utilizando o portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 07eae453441ea8bff81d7cdb60f9c46c08a22829
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91851183"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Configure uma política personalizada do IPsec para o WAN virtual usando o portal

Pode configurar uma política IPsec personalizada para uma ligação Virtual WAN VPN no portal Azure. As políticas personalizadas são úteis quando pretende que ambos os lados (no local e gateway Azure VPN) utilizem as mesmas definições para a Fase 1 e fase 2 do IKE.

## <a name="working-with-custom-policies"></a>Trabalhar com políticas personalizadas

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Configure uma política

1. **Localize o centro virtual.** Num browser, navegue para o [Portal do Azure](https://aka.ms/azurevirtualwanpreviewfeatures) e inicie sessão com a sua conta do Azure. Navegue para o seu recurso WAN virtual e localize o hub virtual ao qual o seu site VPN está ligado.
2. **Selecione o site VPN**. A partir da página de visão geral do hub, clique **em VPN (Site para site)** e selecione o Site VPN para o qual pretende configurar uma política IPsec personalizada.

   ![selecionar](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Editar a ligação VPN**. A partir do **menu Contexto** **...**, selecione **Editar Ligação VPN**.

   ![editar](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Configure as definições**. Na página **de ligação Editar VPN,** altere a definição IPsec de predefinição para personalizada e personalize a política IPsec. **Selecione Guardar** para guardar as suas definições.

   ![configurar e salvar](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).