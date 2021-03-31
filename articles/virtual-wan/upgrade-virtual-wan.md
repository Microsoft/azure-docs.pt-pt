---
title: Atualize um Azure Virtual WAN de Basic para Standard - Portal Azure | Microsoft Docs
description: Pode atualizar o seu tipo WAN virtual para uma maior funcionalidade.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 6290d89ed0ee539b4df4c2c8bc9070097da98c81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91447357"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Atualize um WAN virtual de Basic para Standard

Este artigo ajuda-o a atualizar um WAN Básico para um WAN Standard. Um tipo WAN 'Básico' cria todos os centros dentro dele como centros Básicos SKU. Num hub Basic, limita-se apenas à funcionalidade VPN site-to-site. Um tipo WAN 'Standard' cria todos os centros dentro dele como centros Standard SKU. Quando utilizar os hubs Standard, pode ativar a VPN ExpressRoute, User (Ponto a local), um hub de malha completa e o trânsito VNet-to-VNet através dos hubs Azure.

A tabela a seguir mostra as configurações disponíveis para cada tipo WAN:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>Para alterar o tipo WAN virtual

1. Na página para o seu WAN virtual, selecione **Configuração** para abrir a página de Configuração.

   ![Screenshot que mostra a página "Configuração" com uma caixa de texto de informação sobre o upgrade para um WAN virtual de tipo padrão realçado na parte inferior.](./media/upgrade-virtual-wan/1.png)
2. Para o tipo WAN virtual, selecione **Standard** a partir do drop-down.

   ![Screenshot que mostra o menu drop-down "Virtual WAN".](./media/upgrade-virtual-wan/2.png)
3. Entenda que se fizer upgrade para um WAN virtual padrão, não pode voltar a um WAN virtual básico. **Selecione Confirme** se pretender atualizar.

   ![Screenshot que mostra o diálogo de "confirmação de atualização".](./media/upgrade-virtual-wan/4.png)
4. Uma vez guardada a alteração, a sua página WAN virtual parece semelhante a este exemplo.

   ![Diagrama da WAN Virtual](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).