---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d36d2be59010c47348a8e196b28d87e5b967868e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184130"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Passo 1: Navegue para o gateway de rede virtual

1. Na [portal do Azure](https://portal.azure.com), navegue até à **todos os recursos**. 
2. Para abrir a página de gateway de rede virtual, navegue para o gateway de rede virtual que pretende eliminar e clique no mesmo.

### <a name="step-2-delete-connections"></a>Passo 2: Eliminar ligações

1. Na página do gateway de rede virtual, clique em **ligações** para ver todas as ligações para o gateway.
2. Clique nas **"..."** na linha do nome da ligação, em seguida, selecione **eliminar** na lista pendente.
3. Clique em **Sim** para confirmar que pretende eliminar a ligação. Se tiver várias ligações, elimine a cada conexão.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Passo 3: Eliminar o gateway de rede virtual

Lembre-se de que se tiver uma configuração P2S para esta VNet, além de sua configuração de S2S, a eliminar o gateway de rede virtual se desconectará automaticamente todos os clientes de P2S sem aviso.

1. Na página do gateway de rede virtual, clique em **descrição geral**.
2. Sobre o **descrição geral** página, clique em **eliminar** de eliminar o gateway.
