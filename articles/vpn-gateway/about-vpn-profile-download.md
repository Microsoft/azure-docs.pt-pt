---
title: 'Azure VPN Gateway: Sobre os perfis de clientes P2S VPN'
description: Isto ajuda-o a trabalhar com o ficheiro de perfil do cliente
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/17/2020
ms.author: cherylmc
ms.openlocfilehash: b78d32dbb7b4e0d9a3b13ff741e6e38c12be0e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528511"
---
# <a name="about-p2s-vpn-client-profiles"></a>Sobre os perfis de clientes P2S VPN

O ficheiro de perfil descarregado contém informações necessárias para configurar uma ligação VPN. Este artigo irá ajudá-lo a obter e entender as informações necessárias para um perfil de cliente VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* A **pasta OpenVPN** contém o perfil *ovpn* que precisa de ser modificado para incluir a chave e o certificado. Para mais informações, consulte [os clientes Configure OpenVPN para Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows). Esta pasta não estará presente no ficheiro zip se a autenticação da AD Azure for selecionada no gateway VPN. Em vez disso, o azurevpnconfig.xml estará na pasta AzureVPN.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o ponto-a-local, consulte [o ponto-a-local](point-to-site-about.md).
