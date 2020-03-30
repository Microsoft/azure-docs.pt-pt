---
title: Atualizar um Wan Virtual Azure de Basic para Standard - Portal Azure [ Microsoft Docs
description: Pode atualizar o seu tipo virtual WAN para uma maior funcionalidade.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 83fd5bafb5496908403c50dc0e000fd33a836c95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515814"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Atualize um WAN virtual de Basic para Standard

Este artigo ajuda-o a atualizar um Wan Básico para um Wan Padrão. Um tipo 'Basic' WAN cria todos os centros dentro dele como centros Básicos SKU. Num hub Básico, limita-se apenas à funcionalidade VPN site-to-site. Um tipo 'Standard' WAN cria todos os centros dentro dele como hubs Standard SKU. Quando utiliza os hubs Standard, pode ativar o ExpressRoute, user (Ponto-a-site) VPN, um hub de malha completa e o trânsito VNet-to-VNet através dos hubs Azure.

A tabela que se segue mostra as configurações disponíveis para cada tipo WAN:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>Para alterar o tipo virtual WAN

1. Na página para o seu WAN virtual, selecione **Configuração** para abrir a página de Configuração.

   ![Diagrama da WAN Virtual](./media/upgrade-virtual-wan/1.png)
2. Para o tipo Virtual WAN, selecione **Standard** a partir do dropdown.

   ![Diagrama da WAN Virtual](./media/upgrade-virtual-wan/2.png)
3. Entenda que se fizer upgrade para um Wan virtual Standard, não pode voltar a um Wan virtual Básico. Selecione **Confirmar** se quiser fazer o upgrade.

   ![Diagrama da WAN Virtual](./media/upgrade-virtual-wan/4.png)
4. Uma vez que a mudança tenha sido guardada, a sua página virtual WAN parece semelhante a este exemplo.

   ![Diagrama da WAN Virtual](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).