---
title: 'VPN Gateway: Inquilino da AD Azure para diferentes grupos de utilizadores: Autenticação Azure AD'
description: Pode utilizar o P2S VPN para se ligar ao seu VNet utilizando a autenticação Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485723"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Criar um inquilino de Diretório Ativo Azure para ligações ao protocolo P2S OpenVPN

Ao ligar-se ao seu VNet, pode utilizar autenticação baseada em certificado ou autenticação RADIUS. No entanto, ao utilizar o protocolo Open VPN, também pode utilizar a autenticação do Diretório Ativo Azure. Se pretender que diferentes utilizadores possam ligar-se a diferentes gateways VPN, pode registar várias aplicações em AD e ligá-las a diferentes gateways VPN. Este artigo ajuda-o a configurar um inquilino Azure AD para autenticação P2S OpenVPN e criar e registar várias aplicações em Azure AD para permitir diferentes acessos para diferentes utilizadores e grupos.

> [!NOTE]
> A autenticação Azure AD é suportada apenas para ligações de protocolo openVPN®.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. Ativar a autenticação no portal

Neste passo, permite a autenticação Azure AD no gateway VPN.

1. Ativar a autenticação AD Azure no gateway VPN executando os seguintes comandos. Certifique-se de modificar os comandos para refletir o seu próprio ambiente:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > Não utilize o ID de aplicação do cliente Azure VPN nos comandos acima: Irá conceder a todos os utilizadores o acesso ao gateway VPN. Utilize a identificação do(s) pedido que registou.

2. Crie e baixe o perfil executando os seguintes comandos. Altere os valores -ResourcGroupName e -Name para combinar com o seu próprio.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. Depois de executar os comandos, você vê um resultado semelhante ao abaixo. Copie o URL de resultados para o seu navegador para descarregar o ficheiro zip do perfil.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Extraio o ficheiro zip descarregado.

5. Navegue na pasta "AzureVPN" desapertado.

6. Tome nota da localização do ficheiro "azurevpnconfig.xml". O azurevpnconfig.xml contém a definição para a ligação VPN e pode ser importado diretamente para a aplicação azure VPN Client. Também pode distribuir este ficheiro a todos os utilizadores que necessitem de se conectar via e-mail ou outros meios. O utilizador necessitará de credenciais Azure AD válidas para se conectar com sucesso.

## <a name="next-steps"></a>Passos seguintes

Para se ligar à sua rede virtual, tem de criar e configurar um perfil de cliente VPN. Consulte [configurar um cliente VPN para ligações P2S VPN](openvpn-azure-ad-client.md).
