---
title: 'Inquilino Azure AD para ligações VPN utilizador: Autenticação AZURE AD'
description: Pode utilizar a VPN do utilizador Azure Virtual WAN (ponto a local) para ligar ao seu VNet utilizando a autenticação AZure AD
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: 76c65d194d03dd1b7ff4cc2f3b45d84ff7909968
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753365"
---
# <a name="create-an-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Criar um inquilino de Diretório Ativo Azure para ligações de protocolos OpenVPN VPN do utilizador

Ao ligar-se ao seu VNet, pode utilizar a autenticação baseada em certificados ou a autenticação RADIUS. No entanto, quando utilizar o protocolo Open VPN, também pode utilizar a autenticação do Azure Ative Directory. Este artigo ajuda a configurar um inquilino Azure AD para a autenticação VPN do utilizador virtual WAN (ponto a local).

> [!NOTE]
> A autenticação Azure AD é suportada apenas para &reg; ligações de protocolo OpenVPN.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Criar o inquilino AZURE AD

Criar um inquilino AZure AD usando os passos no novo artigo de [inquilino:](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Nome organizacional
* Nome de domínio inicial

Exemplo:

   ![Novo inquilino da AD AZure](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Criar utilizadores inquilinos da AD Azure

Em seguida, crie duas contas de utilizador. Crie uma conta Global Admin e uma conta de utilizador principal. A conta principal do utilizador é utilizada como a sua conta principal de incorporação (conta de serviço). Quando cria uma conta de utilizador de inquilino Azure AD, ajusta a função de Diretório para o tipo de utilizador que pretende criar.

Use os passos [deste artigo](../active-directory/fundamentals/add-users-azure-active-directory.md) para criar pelo menos dois utilizadores para o seu inquilino AZURE AD. Certifique-se de alterar a **Função de Diretório** para criar os tipos de conta:

* Admin Global
* Utilizador

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Ativar a autenticação AD AZure no gateway VPN

1. Localize a identificação do diretório do diretório que pretende utilizar para autenticação. Está listado na secção de propriedades da página Ative Directory.

    ![ID do diretório](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Copie o ID do Diretório.

3. Inscreva-se no portal Azure como um utilizador que é atribuído à **função de administrador Global.**

4. Em seguida, dê consentimento administrativo. Copie e cole o URL que diz respeito à sua localização de implantação na barra de endereço do seu navegador:

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
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Selecione a conta **Global Admin** se solicitado.

    ![ID do diretório](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Selecione **Aceitar** quando solicitado.

    ![Aceitar](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Sob o seu Azure AD, em **aplicações enterprise,** você vê **Azure VPN** listado.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Configurar a autenticação Azure AD para VPN do utilizador e atribuí-la a um Hub Virtual seguindo os passos na [autenticação Configure Azure AD para ligação ponto-a-local ao Azure](virtual-wan-point-to-site-azure-ad.md)

## <a name="next-steps"></a>Próximos passos

Para se ligar à sua rede virtual, deve criar e configurar um perfil de cliente VPN e associá-lo a um Centro Virtual. Consulte [a autenticação AD configurada para a ligação ponto-a-local ao Azure](virtual-wan-point-to-site-azure-ad.md).
