---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d36d2be59010c47348a8e196b28d87e5b967868e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184130"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Passo 1: Navegue até ao portal de rede virtual

1. No [portal Azure,](https://portal.azure.com)navegue para **todos os recursos.** 
2. Para abrir a página de gateway de rede virtual, navegue para o portal de rede virtual que pretende eliminar e clicar nele.

### <a name="step-2-delete-connections"></a>Passo 2: Eliminar ligações

1. Na página para o seu gateway de rede virtual, clique em **Conexões** para visualizar todas as ligações do portal.
2. Clique no **'...'** na linha do nome da ligação e, em seguida, selecione **Eliminar** a partir do dropdown.
3. Clique **em Sim** para confirmar que deseja eliminar a ligação. Se tiver várias ligações, elimine cada ligação.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Passo 3: Apagar o portal de rede virtual

Esteja ciente de que se tiver uma configuração P2S para este VNet para além da sua configuração S2S, a eliminação do gateway de rede virtual desligará automaticamente todos os clientes P2S sem aviso prévio.

1. Na página de gateway de rede virtual, clique em **Overview**.
2. Na página **'Vista Geral',** clique em **Eliminar** para eliminar o gateway.
