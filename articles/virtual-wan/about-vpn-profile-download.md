---
title: 'Azure Virtual WAN: Perfis de cliente VPN do utilizador'
description: Isto ajuda-o a trabalhar com o ficheiro de perfil do cliente
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: b88be5cccdaeb452288a2cb2f1dd4b4690a8b274
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066223"
---
# <a name="working-with-user-vpn-client-profiles"></a>Trabalhar com perfis de cliente VPN do utilizador

O ficheiro de perfil descarregado contém informações necessárias para configurar uma ligação VPN. Este artigo ajuda-o a obter e a compreender as informações necessárias para um perfil de cliente VPN do Utilizador.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* A **pasta OpenVPN** contém o perfil *ovpn* que precisa de ser modificado para incluir a chave e o certificado. Para mais informações, consulte [os clientes Configure OpenVPN](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Virtual WAN User VPN, consulte [Criar uma ligação VPN do utilizador](virtual-wan-point-to-site-portal.md).
