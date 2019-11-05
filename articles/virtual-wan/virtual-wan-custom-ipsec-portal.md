---
title: 'Configurar a política IPsec personalizada para a WAN virtual do Azure: Portal | Microsoft Docs'
description: Saiba como configurar a política IPsec personalizada para a WAN virtual do Azure usando o Portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515749"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Configurar uma política IPsec personalizada para WAN virtual usando o portal

Você pode configurar a política IPsec personalizada para a WAN virtual no portal do Azure. Políticas personalizadas são úteis quando você deseja que ambos os lados (gateway de VPN local e do Azure) usem as mesmas configurações para a fase 1 do IKE e a fase 2 do IKE.

## <a name="working-with-custom-policies"></a>Trabalhando com políticas personalizadas

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Configurar uma política

1. **Localize o Hub virtual**. Num browser, navegue para o [Portal do Azure](https://aka.ms/azurevirtualwanpreviewfeatures) e inicie sessão com a sua conta do Azure. Localize o Hub virtual do seu site.
2. **Selecione o site VPN**. Na página Hub, selecione o site VPN para o qual você deseja configurar uma política personalizada.

   ![Não](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Edite a conexão VPN**. No **menu de contexto** **...** , selecione **Editar conexão VPN**.

   ![Editar](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Defina as configurações**. Na página **Editar conexão VPN** , defina as configurações de configurações. Selecione **salvar** para salvar as configurações.

   ![configurar e salvar](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).