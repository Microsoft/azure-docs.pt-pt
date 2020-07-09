---
title: 'Azure VPN Gateway: Sobre os perfis de clientes P2S VPN'
description: Isto ajuda-o a trabalhar com o ficheiro de perfil do cliente
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 05/13/2020
ms.author: cherylmc
ms.openlocfilehash: 4526611fa8b80a7b97ee7317f5e285c50f05b0de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83650642"
---
# <a name="about-p2s-vpn-client-profiles"></a>Sobre os perfis de clientes P2S VPN

O ficheiro de perfil descarregado contém informações necessárias para configurar uma ligação VPN. Este artigo irá ajudá-lo a obter e a compreender as informações necessárias para um perfil de cliente VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* A **pasta OpenVPN** contém o perfil *ovpn* que precisa de ser modificado para incluir a chave e o certificado. Para mais informações, consulte [os clientes Configure OpenVPN para Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows). Se a autenticação AZure AD for selecionada no gateway VPN, esta pasta não está presente no ficheiro zip. Em vez disso, navegue na pasta AzureVPN e localize azurevpnconfig.xml.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre ponto a local, consulte [Sobre o ponto a local.](point-to-site-about.md)
