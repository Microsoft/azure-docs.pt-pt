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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67184130"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Passo 1: Navegar para o portal da rede virtual

1. No [portal Azure,](https://portal.azure.com)navegue para **todos os recursos.** 
2. Para abrir a página de gateway da rede virtual, navegue para o portal de rede virtual que pretende eliminar e clicar nela.

### <a name="step-2-delete-connections"></a>Passo 2: Eliminar ligações

1. Na página do seu portal de rede virtual, clique em **Ligações** para visualizar todas as ligações para o gateway.
2. Clique no **'...'** na linha do nome da ligação e, em seguida, selecione **Apagar** a partir da queda.
3. Clique em **Sim** para confirmar que pretende eliminar a ligação. Se tiver várias ligações, elimine cada ligação.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Passo 3: Eliminar o gateway da rede virtual

Esteja ciente de que se tiver uma configuração P2S para este VNet para além da sua configuração S2S, eliminar o gateway de rede virtual irá desligar automaticamente todos os clientes P2S sem aviso prévio.

1. Na página de gateway da rede virtual, clique em **Visão Geral**.
2. Na página **'Visão Geral',** clique em **Apagar** para eliminar o gateway.
