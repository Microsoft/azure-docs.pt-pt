---
title: 'Azure Virtual WAN: Perfis de clientes VPN do utilizador'
description: Isto ajuda-o a trabalhar com o ficheiro de perfil do cliente
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: c64e7988094612077131029547682c7ae3d25c98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753133"
---
# <a name="working-with-user-vpn-client-profiles"></a>Trabalhar com perfis de clientes VPN do utilizador

O ficheiro de perfil descarregado contém informações necessárias para configurar uma ligação VPN. Este artigo ajuda-o a obter e a compreender as informações necessárias para um perfil de cliente VPN do utilizador.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* A **pasta OpenVPN** contém o perfil *ovpn* que precisa de ser modificado para incluir a chave e o certificado. Para mais informações, consulte [os clientes Configure OpenVPN.](../virtual-wan/howto-openvpn-clients.md#windows)

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a VPN do utilizador VIRTUAL WAN, consulte [Criar uma ligação VPN do utilizador](virtual-wan-point-to-site-portal.md).
