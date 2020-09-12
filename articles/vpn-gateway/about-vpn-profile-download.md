---
title: 'Azure VPN Gateway: Sobre os perfis de clientes P2S VPN'
description: Isto ajuda-o a trabalhar com o ficheiro de perfil do cliente
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 326abaa123ede1fbe371c69fcbed47a310b54511
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89424842"
---
# <a name="about-p2s-vpn-client-profiles"></a>Sobre os perfis de clientes P2S VPN

O ficheiro de perfil descarregado contém informações necessárias para configurar uma ligação VPN. Este artigo irá ajudá-lo a obter e a compreender as informações necessárias para um perfil de cliente VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* A **pasta OpenVPN** contém o perfil *ovpn* que precisa de ser modificado para incluir a chave e o certificado. Para mais informações, consulte [os clientes Configure OpenVPN para Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows). Se a autenticação AZure AD for selecionada no gateway VPN, esta pasta não está presente no ficheiro zip. Em vez disso, navegue na pasta AzureVPN e localize azurevpnconfig.xml.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre ponto a local, consulte [Sobre o ponto a local.](point-to-site-about.md)
