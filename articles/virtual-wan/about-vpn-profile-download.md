---
title: 'Azure Virtual WAN: Perfis de clientes VPN do utilizador'
description: Isto ajuda-o a trabalhar com o ficheiro de perfil do cliente
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: d83b6ed2ae83db569d3c61e3cf4cd887f875eb25
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99980920"
---
# <a name="working-with-user-vpn-client-profile-files"></a>Trabalhar com ficheiros de perfil de cliente VPN do utilizador

Os ficheiros de perfil contêm informações necessárias para configurar uma ligação VPN. Este artigo ajuda-o a obter e a compreender as informações necessárias para um perfil de cliente VPN do utilizador.

## <a name="download-the-profile"></a>Descarregue o perfil

Pode utilizar os passos no artigo [de perfis de descarregamento](global-hub-profile.md) para descarregar o ficheiro zip do perfil do cliente.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* A **pasta OpenVPN** contém o perfil *ovpn* que precisa de ser modificado para incluir a chave e o certificado. Para mais informações, consulte [os clientes Configure OpenVPN.](../virtual-wan/howto-openvpn-clients.md#windows)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a VPN do utilizador VIRTUAL WAN, consulte [Criar uma ligação VPN do utilizador](virtual-wan-point-to-site-portal.md).
