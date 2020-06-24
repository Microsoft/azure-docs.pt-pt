---
title: 'VPN Gateway: Inquilino Azure AD para diferentes grupos de utilizadores: Autenticação AZURE AD'
description: Pode utilizar o P2S VPN para ligar ao seu VNet utilizando a autenticação AD Azure
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 2fc329bd77bafb2e11575b75be102314df98131f
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84987207"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Criar um inquilino do Azure Active Directory para as ligações do protocolo OpenVPN P2S

Ao ligar-se ao seu VNet, pode utilizar a autenticação baseada em certificados ou a autenticação RADIUS. No entanto, quando utilizar o protocolo Open VPN, também pode utilizar a autenticação do Azure Ative Directory. Se quiser que um conjunto diferente de utilizadores seja capaz de se conectar a diferentes gateways VPN, pode registar várias aplicações em AD e ligá-las a diferentes gateways VPN. Este artigo ajuda a configurar um inquilino Azure AD para a autenticação P2S OpenVPN e criar e registar várias aplicações em Azure AD para permitir acesso diferente para diferentes utilizadores e grupos.

> [!NOTE]
> A autenticação AZure AD é suportada apenas para ligações de protocolo ® OpenVPN.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. Ativar a autenticação no gateway

Neste passo, você ativa a autenticação AZure AD no gateway VPN.

1. Ativar a autenticação Azure AD no gateway VPN executando os seguintes comandos. Certifique-se de modificar os comandos para refletir o seu próprio ambiente:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > Não utilize o ID de aplicação do cliente Azure VPN nos comandos acima: Irá conceder a todos os utilizadores acesso ao gateway VPN. Utilize o ID das aplicações que registou.

2. Crie e descarregue o perfil executando os seguintes comandos. Altere os valores -ResourcGroupName e -Nome para corresponder aos seus.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. Depois de executar os comandos, vê um resultado semelhante ao abaixo. Copie o URL de resultados para o seu navegador para descarregar o ficheiro zip do perfil.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Extraia o ficheiro zip descarregado.

5. Navegue na pasta "AzureVPN" desapertada.

6. Tome nota da localização do ficheiro "azurevpnconfig.xml". O azurevpnconfig.xml contém a definição para a ligação VPN e pode ser importado diretamente para a aplicação do Cliente Azure VPN. Também pode distribuir este ficheiro a todos os utilizadores que necessitem de se conectar por e-mail ou outros meios. O utilizador necessitará de credenciais AZure AD válidas para se conectar com sucesso.

## <a name="next-steps"></a>Passos seguintes

Para se ligar à sua rede virtual, tem de criar e configurar um perfil de cliente VPN. Consulte [configurar um cliente VPN para ligações P2S VPN](openvpn-azure-ad-client.md).
