---
title: 'VPN Gateway: Inquilino Azure AD para diferentes grupos de utilizadores: Autenticação AZURE AD'
description: Pode utilizar o P2S VPN para ligar ao seu VNet utilizando a autenticação AD Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 1305ca603aef63dafcc7b055d55e3f0fe281f4fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91819679"
---
# <a name="create-an-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>Criar um inquilino ative diretório (AD) para ligações de protocolo P2S OpenVPN

Ao ligar-se ao seu VNet, pode utilizar a autenticação baseada em certificados ou a autenticação RADIUS. No entanto, quando utilizar o protocolo Open VPN, também pode utilizar a autenticação do Azure Ative Directory. Se quiser que um conjunto diferente de utilizadores seja capaz de se conectar a diferentes gateways VPN, pode registar várias aplicações em AD e ligá-las a diferentes gateways VPN. Este artigo ajuda a configurar um inquilino Azure AD para a autenticação P2S OpenVPN e criar e registar várias aplicações em Azure AD para permitir acesso diferente para diferentes utilizadores e grupos.

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. Ativar a autenticação no gateway

Neste passo, você ativará a autenticação AZure AD no gateway VPN.

1. Ativar a autenticação AZure AD no gateway VPN navegando para a **configuração ponto-a-local** e escolhendo **o OpenVPN (SSL)** como o **tipo de túnel**. Selecione **O Diretório Ativo Azure** como o **tipo de Autenticação** e, em seguida, preencha as informações na secção **Diretório Ativo Azure.**

    ![Vista do portal Azure](./media/openvpn-azure-ad-tenant-multi-app/azure-ad-auth-portal.png)

    > [!NOTE]
    > Não utilize o ID de aplicação do cliente Azure VPN: Irá permitir a todos os utilizadores o acesso ao gateway VPN. Utilize o ID das aplicações que registou.

2. Crie e descarregue o perfil clicando no link de **cliente VPN de descarregamento.**

3. Extraia o ficheiro zip descarregado.

4. Navegue na pasta "AzureVPN" desapertada.

5. Tome nota da localização do ficheiro "azurevpnconfig.xml". O azurevpnconfig.xml contém a definição para a ligação VPN e pode ser importado diretamente para a aplicação do Cliente Azure VPN. Também pode distribuir este ficheiro a todos os utilizadores que necessitem de se conectar por e-mail ou outros meios. O utilizador necessitará de credenciais AZure AD válidas para se conectar com sucesso.

## <a name="next-steps"></a>Passos seguintes

Para se ligar à sua rede virtual, tem de criar e configurar um perfil de cliente VPN. Consulte [configurar um cliente VPN para ligações P2S VPN](openvpn-azure-ad-client.md).
