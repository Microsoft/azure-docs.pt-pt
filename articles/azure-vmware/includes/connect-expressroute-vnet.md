---
title: Conecte o ExpressRoute ao gateway de rede virtual
description: Passos para ligar o ExpressRoute ao portal de rede virtual.
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: 6e2e3748dbfd8d69b53dcc4c3a09809756ac48dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103494371"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. Navegue para a nuvem privada que criou no [tutorial Deploy vSphere Cluster em Azure.](../tutorial-create-private-cloud.md) Selecione **Conectividade** em **Manage**, selecione o **separador ExpressRoute.**

1. Copie a chave de autorização. Se não houver uma chave de autorização, tem de criar uma, selecione **+ Solicite uma chave de autorização.**

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Copie a chave de autorização. Se não houver uma chave de autorização, precisa de criar uma, selecione + Solicite uma chave de autorização." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Navegue para o Gateway de Rede Virtual que criou no passo anterior e em **Definições**, selecione **Connections**. Na página **'Ligações',** selecione **+ Adicionar**.

1. Na página **de ligação Adicionar,** forneça valores para os campos e selecione **OK**. 

   | Campo | Valor |
   | --- | --- |
   | **Nome**  | Introduza um nome para a ligação.  |
   | **Tipo de ligação**  | Selecione **ExpressRoute**.  |
   | **Autorização de redentor**  | Certifique-se de que esta caixa está selecionada.  |
   | **Gateway de rede virtual** | A porta de entrada da Rede Virtual que criou anteriormente.  |
   | **Chave de autorização**  | Copie e cole a chave de autorização do separador ExpressRoute para o seu Grupo de Recursos. |
   | **Circuito de pares URI**  | Copie e cole o ID ExpressRoute do separador ExpressRoute para o seu Grupo de Recursos.  |

   :::image type="content" source="../media/expressroute-global-reach/expressroute-add-connection.png" alt-text="Screenshot da página de ligação Adicionar para ligar o ExpressRoute ao gateway de rede virtual.":::

A ligação entre o seu circuito ExpressRoute e a sua Rede Virtual é criada.

:::image type="content" source="../media/expressroute-global-reach/virtual-network-gateway-connections.png" alt-text="Screenshot das ligações de gateway de rede virtual.":::