---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: 15c29648e42ba190991d51188489883e29bee165
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041587"
---
>[!NOTE]
>Tem de ter direitos de Administrador no computador cliente Windows a partir do qual está a ligar.
>

1. Para ligar ao seu VNet, no computador cliente, navegue para as definições de VPN e localize a ligação VPN que criou. Tem o mesmo nome da sua rede virtual. Selecione **Ligar**. Poderá aparecer uma mensagem pop-up que se refere à utilização do certificado. **Selecione Continue** a utilizar privilégios elevados.

1. Na página **''' 'Ligação',** **selecione Connect** para iniciar a ligação. Se vir um ecrã **Selecionar Certificado**, verifique se o certificado de cliente apresentado é aquele que pretende utilizar para se ligar. Se não for, utilize a seta para baixo para selecionar o certificado correto e, em seguida, selecione **OK**.

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connection-status.png" alt-text="Ligar a partir de um computador Windows":::

1. A ligação é estabelecida.

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connected.png" alt-text="Ligue de um computador a um Azure VNet - Diagrama de ligação ponto-a-local":::
