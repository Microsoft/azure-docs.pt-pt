---
title: 'Azure Virtual WAN: Perfis de clientes VPN do utilizador'
description: Isto ajuda-o a trabalhar com o ficheiro de perfil do cliente
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 116ec518f7788e620fc6bd177e535c8f6af99d10
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267776"
---
# <a name="working-with-user-vpn-client-profiles"></a>Trabalhar com perfis de clientes VPN do utilizador

O ficheiro de perfil descarregado contém informações necessárias para configurar uma ligação VPN. Este artigo ajuda-o a obter e a compreender as informações necessárias para um perfil de cliente VPN do utilizador.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* A **pasta OpenVPN** contém o perfil *ovpn* que precisa de ser modificado para incluir a chave e o certificado. Para mais informações, consulte [os clientes Configure OpenVPN.](../virtual-wan/howto-openvpn-clients.md#windows)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a VPN do utilizador VIRTUAL WAN, consulte [Criar uma ligação VPN do utilizador](virtual-wan-point-to-site-portal.md).
