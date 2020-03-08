---
title: 'VPN Gateway: Inquilino da AD Azure para ligações P2S VPN: Autenticação Azure AD'
description: Pode utilizar o P2S VPN para se ligar ao seu VNet utilizando a autenticação Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: alzam
ms.openlocfilehash: f4092f651a3058c8a2e738c81d9db7e296386bfa
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402889"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Criar um inquilino de Diretório Ativo Azure para ligações ao protocolo P2S OpenVPN

Ao ligar-se ao seu VNet, pode utilizar autenticação baseada em certificado ou autenticação RADIUS. No entanto, ao utilizar o protocolo Open VPN, também pode utilizar a autenticação do Diretório Ativo Azure. Este artigo ajuda-o a criar um inquilino Azure AD para autenticação P2S Open VPN.

> [!NOTE]
> A autenticação Azure AD é suportada apenas para ligações de protocolo openVPN®.
>


## <a name="tenant"></a>1. Verificar inquilino da AD Azure

Verifique se tem um inquilino da AD Azure. Se você não tem um inquilino Azure AD, você pode criar um usando os passos no [artigo criar um novo inquilino:](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Nome organizacional
* Nome inicial de domínio

Exemplo:

   ![Novo inquilino da AD Azure](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="users"></a>2. Criar utilizadores de inquilinos da AD Azure

O seu inquilino Azure AD precisa das seguintes contas: uma conta Global Admin e uma conta de utilizador principal. A conta de utilizador principal é utilizada como conta de incorporação principal (conta de serviço). Quando cria uma conta de utilizador de inquilinos Azure AD, ajusta a função de Diretório para o tipo de utilizador que pretende criar.

Use os passos [deste artigo](../active-directory/fundamentals/add-users-azure-active-directory.md) para criar pelo menos dois utilizadores para o seu inquilino Azure AD. Certifique-se de alterar a **Função de Diretório** para criar os tipos de conta:

* Admin Global
* Utilizador

## <a name="enable-authentication"></a>3. Ativar a autenticação adato da AD Azure no gateway VPN

1. Localize a id do Diretório do diretório que pretende utilizar para autenticação. Está listado na secção de propriedades da página Ative Directy.

    ![ID do diretório](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Copie a identificação do Diretório.

3. Inscreva-se no portal Azure como um utilizador que lhe é atribuído o papel de **administrador global.**

4. Em seguida, dê consentimento à administração. Copie e cole o URL que diz respeito à sua localização de implementação na barra de endereços do seu navegador:

    Público

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Alemanha

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Selecione a conta **Global Admin** se solicitado.

    ![ID do diretório](./media/openvpn-create-azure-ad-tenant/pick.png)

6. **Selecione Aceitar** quando solicitado.

    ![Aceitar](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Sob o seu Azure AD, em **aplicações Enterprise,** vê **Azure VPN** listado.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. Se ainda não tiver um ambiente de ponto a local funcional, siga as instruções para criar um. Consulte [Criar uma VPN ponto-a-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para criar e configurar um gateway VPN ponto-a-site com autenticação de certificado azure nativo. 

    > [!IMPORTANT]
    > O SKU Básico não é suportado para o OpenVPN.

9. Ativar a autenticação aditiva Azure na porta vpN executando os seguintes comandos, sendo certo que modifica o comando para refletir o seu próprio ambiente:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24 -VpnClientProtocol OpenVPN
    ```

   > [!NOTE]
   > Certifique-se de incluir um corte de rasto no final do valor `AadIssuerUri`. Caso contrário, o comando falhará.

10. Crie e baixe o perfil executando os seguintes comandos. Altere os valores -ResourceGroupName e -Name para combinar com o seu próprio.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

11. Depois de executar os comandos, você vê um resultado semelhante ao abaixo. Copie o URL de resultados para o seu navegador para descarregar o ficheiro zip do perfil.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/profile.png)

12. Extraio o ficheiro zip descarregado.

13. Navegue na pasta "AzureVPN" desapertado.

14. Tome nota da localização do ficheiro "azurevpnconfig.xml". O azurevpnconfig.xml contém a definição para a ligação VPN e pode ser importado diretamente para a aplicação azure VPN Client. Também pode distribuir este ficheiro a todos os utilizadores que necessitem de se conectar via e-mail ou outros meios. O utilizador necessitará de credenciais Azure AD válidas para se conectar com sucesso.

## <a name="next-steps"></a>Passos seguintes

Para se ligar à sua rede virtual, tem de criar e configurar um perfil de cliente VPN. Consulte [configurar um cliente VPN para ligações P2S VPN](openvpn-azure-ad-client.md).
