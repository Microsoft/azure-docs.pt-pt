---
title: 'Azure VPN Gateway: Sobre os perfis de clientes P2S VPN'
description: Use este artigo para encontrar a informação necessária para um perfil de cliente VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 6a09767a7992a5f902adea6f99e937f3fc6fa7fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985934"
---
# <a name="about-p2s-vpn-client-profiles"></a>Sobre os perfis de clientes P2S VPN

O ficheiro de perfil descarregado contém informações necessárias para configurar uma ligação VPN. Este artigo irá ajudá-lo a obter e a compreender as informações necessárias para um perfil de cliente VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* A **pasta OpenVPN** contém o perfil *ovpn* que precisa de ser modificado para incluir a chave e o certificado. Para mais informações, consulte [os clientes Configure OpenVPN para Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows). Se a autenticação AZure AD for selecionada no gateway VPN, esta pasta não está presente no ficheiro zip. Em vez disso, navegue na pasta AzureVPN e localize azurevpnconfig.xml.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre ponto a local, consulte [Sobre o ponto a local.](point-to-site-about.md)
