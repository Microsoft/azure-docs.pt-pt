---
title: 'VPN Gateway: Inquilino Azure AD para diferentes grupos de utilizadores: Autenticação AZURE AD'
description: Pode utilizar o P2S VPN para ligar ao seu VNet utilizando a autenticação AD Azure
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/25/2020
ms.author: alzam
ms.openlocfilehash: 0ef0c7d3a269753067e53a69b9da680db969e25d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85414436"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Criar um inquilino do Azure Active Directory para as ligações do protocolo OpenVPN P2S

Ao ligar-se ao seu VNet, pode utilizar a autenticação baseada em certificados ou a autenticação RADIUS. No entanto, quando utilizar o protocolo Open VPN, também pode utilizar a autenticação do Azure Ative Directory. Se quiser que um conjunto diferente de utilizadores seja capaz de se conectar a diferentes gateways VPN, pode registar várias aplicações em AD e ligá-las a diferentes gateways VPN. Este artigo ajuda a configurar um inquilino Azure AD para a autenticação P2S OpenVPN e criar e registar várias aplicações em Azure AD para permitir acesso diferente para diferentes utilizadores e grupos.

> [!NOTE]
> A autenticação AZure AD é suportada apenas para ligações de protocolo ® OpenVPN.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. Ativar a autenticação no gateway

Neste passo, você ativará a autenticação AZure AD no gateway VPN.

1. Ativar a autenticação AZure AD no gateway VPN navegando para a **configuração ponto-a-local** e escolhendo **o OpenVPN (SSL)** como o **tipo de túnel**. Selecione **O Diretório Ativo Azure** como o **tipo de Autenticação** e, em seguida, preencha as informações na secção **Diretório Ativo Azure.**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-ad-auth-portal.png)

    > [!NOTE]
    > Não utilize o ID de aplicação do cliente Azure VPN: Irá permitir a todos os utilizadores o acesso ao gateway VPN. Utilize o ID das aplicações que registou.

2. Crie e descarregue o perfil clicando no link de **cliente VPN de descarregamento.**

3. Extraia o ficheiro zip descarregado.

4. Navegue na pasta "AzureVPN" desapertada.

5. Tome nota da localização do ficheiro "azurevpnconfig.xml". O azurevpnconfig.xml contém a definição para a ligação VPN e pode ser importado diretamente para a aplicação do Cliente Azure VPN. Também pode distribuir este ficheiro a todos os utilizadores que necessitem de se conectar por e-mail ou outros meios. O utilizador necessitará de credenciais AZure AD válidas para se conectar com sucesso.

## <a name="next-steps"></a>Próximos passos

Para se ligar à sua rede virtual, tem de criar e configurar um perfil de cliente VPN. Consulte [configurar um cliente VPN para ligações P2S VPN](openvpn-azure-ad-client.md).
