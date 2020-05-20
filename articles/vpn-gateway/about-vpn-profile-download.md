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
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650642"
---
# <a name="about-p2s-vpn-client-profiles"></a>Sobre os perfis de clientes P2S VPN

O ficheiro de perfil descarregado contém informações necessárias para configurar uma ligação VPN. Este artigo irá ajudá-lo a obter e entender as informações necessárias para um perfil de cliente VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* A **pasta OpenVPN** contém o perfil *ovpn* que precisa de ser modificado para incluir a chave e o certificado. Para mais informações, consulte [os clientes Configure OpenVPN para Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows). Se a autenticação Azure AD for selecionada no gateway VPN, esta pasta não está presente no ficheiro zip. Em vez disso, navegue para a pasta AzureVPN e localize azurevpnconfig.xml.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o ponto-a-local, consulte [o ponto-a-local](point-to-site-about.md).
