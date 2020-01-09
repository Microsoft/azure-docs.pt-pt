---
title: 'Gateway de VPN: locatário do Azure AD para grupos de usuários diferentes: autenticação do Azure AD'
description: Você pode usar a VPN P2S para se conectar à sua VNet usando a autenticação do Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: alzam
ms.openlocfilehash: 73a47b20010be0b4eadec790dd8fb254f912ff2a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477177"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Criar um locatário de Azure Active Directory para conexões de protocolo P2S OpenVPN

Ao conectar-se à sua VNet, você pode usar a autenticação baseada em certificado ou a autenticação RADIUS. No entanto, ao usar o protocolo VPN aberto, você também pode usar a autenticação Azure Active Directory. Se desejar que um conjunto diferente de usuários seja capaz de se conectar a diferentes gateways de VPN, você poderá registrar vários aplicativos no AD e vinculá-los a diferentes gateways de VPN. Este artigo ajuda você a configurar um locatário do Azure AD para autenticação P2S OpenVPN e criar e registrar vários aplicativos no Azure AD para permitir acesso diferente para diferentes usuários e grupos.

> [!NOTE]
> A autenticação do Azure AD tem suporte apenas para conexões de protocolo OpenVPN®.
>

## <a name="tenant"></a>1. criar o locatário do Azure AD

Crie um locatário do Azure AD usando as etapas no artigo [criar um novo locatário](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nome organizacional
* Nome de domínio inicial

Exemplo:

   ![Novo locatário do Azure AD](./media/openvpn-azure-ad-tenant-multi-app/newtenant.png)

## <a name="users"></a>2. criar usuários de locatário do Azure AD

Em seguida, crie duas contas de usuário. Crie uma conta de administrador global e uma conta de usuário mestre. A conta de usuário mestre é usada como sua conta de incorporação mestre (conta de serviço). Ao criar uma conta de usuário de locatário do Azure AD, você ajusta a função de diretório para o tipo de usuário que deseja criar.

Use as etapas neste [artigo](../active-directory/fundamentals/add-users-azure-active-directory.md) para criar pelo menos dois usuários para seu locatário do Azure AD. Certifique-se de alterar a **função de diretório** para criar os tipos de conta:

* Admin Global
* Utilizador

## <a name="enable-authentication"></a>3. registrar o cliente VPN do Azure no locatário do Azure AD

1. Localize a ID de diretório do diretório que você deseja usar para autenticação. Ele é listado na seção Propriedades da página Active Directory.

    ![ID do diretório](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Copie a ID do diretório.

3. Entre no portal do Azure como um usuário que é atribuído à função de **administrador global** .

4. Em seguida, dê consentimento ao administrador. Copie e cole a URL que pertence ao seu local de implantação na barra de endereços do seu navegador:

    Público

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Selecione a conta de **administrador global** , se solicitado.

    ![ID do diretório](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Selecione **aceitar** quando solicitado.

    ![Aceitar](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Em seu Azure AD, em **aplicativos empresariais**, você verá a **VPN do Azure** listada.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/azurevpn.png)

## <a name="enable-authentication"></a>4. registrar aplicativos adicionais para vários usuários/grupos

1. Em seu Azure Active Directory, clique em **registros de aplicativo** e em **+ novo registro**

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Na folha **registrar um aplicativo** , insira o **nome** e selecione os **tipos de conta com suporte** desejados e clique em **registrar**

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Depois que o novo aplicativo tiver sido registrado, clique em **expor uma API** na folha do aplicativo

4. Clique em **+ Adicionar um escopo**
5. Deixe o **URI da ID do aplicativo** padrão e clique em **salvar e continuar**

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app3.png)
6. Preencha os campos obrigatórios e verifique se o **estado** está **habilitado**. Clique em **Adicionar escopo**

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app4.png)
7. Clique em **expor uma API** e, em seguida, em **+ Adicionar um aplicativo cliente**.  Para **ID do cliente**, insira os seguintes valores, dependendo da nuvem:
    -   Insira **41b23e61-6c1e-4545-B367-cd054e0ed4b4** para o Azure **Public**
    -   Insira o **51bb15d4-3a4f-4ebf-9dca-40096fe32426** para o Azure **governamental**
    -   Insira o **538ee9e6-310A-468d-AFEF-ea97365856a9** para o Azure **Alemanha**
    -   Insira o **49f817b6-84ae-4cc0-928C-73f27289b3aa** para o Azure **China 21vianet**


8. Clique em **Adicionar aplicativo**

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app5.png)
9. Copie a **ID do aplicativo (cliente)** da página **visão geral** . Você precisará dela para configurar seu gateway (s) de VPN

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Repita as etapas nesta seção (4) para criar quantos aplicativos forem necessários para seu requisito de segurança. Cada aplicativo será associado a um gateway de VPN e pode ter um conjunto diferente de usuários. Somente um aplicativo pode ser associado a um gateway.

## <a name="enable-authentication"></a>5. atribuir usuários a seus aplicativos

1. Em Azure AD, **aplicativos empresariais**, selecione o aplicativo recentemente registrado e clique em **Propriedades**. Verifique se a **atribuição de usuário é necessária?** está definida como **Sim**. Clicar em **Guardar**

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Na página do aplicativo, clique em **usuários e grupos** e, em seguida, em **Adicionar usuário**

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/user3.png)
3. Em **Adicionar atribuição**, clique em **usuários e grupos**. Selecione os usuários que você deseja que possam acessar este aplicativo VPN. Clique em **Selecionar**

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/user4.png)

## <a name="enable-authentication"></a>6. habilitar a autenticação do Azure AD no gateway de VPN

1. Habilite a autenticação do Azure AD no gateway de VPN executando os comandos a seguir, certifique-se de modificar o comando para refletir seu próprio ambiente:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
> [!NOTE]
> Não use a ID do aplicativo do cliente de VPN do Azure nos comandos acima, pois ele concederá a todos os usuários acesso ao gateway de VPN. Use a ID dos aplicativos que você registrou.

2. Crie e baixe o perfil executando os comandos a seguir. Altere os valores-ResourcGroupName e-Name para que correspondam aos seus próprios.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. Depois de executar os comandos, você verá um resultado semelhante ao mostrado abaixo. Copie a URL do resultado para o navegador para baixar o arquivo zip do perfil.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Extraia o arquivo zip baixado.

5. Navegue até a pasta "AzureVPN" descompactada.

6. Anote o local do arquivo "azurevpnconfig. xml". O azurevpnconfig. xml contém a configuração para a conexão VPN e pode ser importado diretamente para o aplicativo cliente VPN do Azure. Você também pode distribuir esse arquivo para todos os usuários que precisam se conectar por email ou outros meios. O usuário precisará de credenciais válidas do Azure AD para se conectar com êxito.

## <a name="next-steps"></a>Passos seguintes

Para se conectar à sua rede virtual, você deve criar e configurar um perfil de cliente VPN. Consulte [configurar um cliente VPN para conexões VPN P2S](openvpn-azure-ad-client.md).
