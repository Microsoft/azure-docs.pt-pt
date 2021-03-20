---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 91dc6ba0bcd455aefe9de2efdff2feb20938152d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376395"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Passo 1: Navegue até ao portal de rede virtual

1. No [portal Azure,](https://portal.azure.com)navegue para **todos os recursos.** 
2. Para abrir a página de gateway de rede virtual, navegue para o gateway de rede virtual e clique para selecioná-la. 

### <a name="step-2-delete-connections"></a>Passo 2: Eliminar ligações

1. Na página para o seu gateway de rede virtual, clique em **Conexões** para visualizar todas as ligações do portal.
2. Clique no **'...'** na linha do nome da ligação e, em seguida, selecione **Eliminar** a partir do dropdown.
3. Clique **em Sim** para confirmar que deseja eliminar a ligação. Se tiver várias ligações, elimine cada ligação.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Passo 3: Apagar o portal de rede virtual

Esteja ciente de que se tiver uma configuração P2S para este VNet para além da sua configuração S2S, a eliminação do gateway de rede virtual desligará automaticamente todos os clientes P2S sem aviso prévio.

1. Na página de gateway de rede virtual, clique em **Overview**.
2. Na página **'Vista Geral',** clique em **Eliminar** para eliminar o gateway.
