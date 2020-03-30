---
title: 'Inquilino da AD Azure para ligações VPN de utilizador: autenticação Azure AD'
description: Pode utilizar vpn de utilizador virtual WAN (ponto-a-site) para se ligar ao seu VNet utilizando a autenticação Azure AD
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: 74347ce969b6a5ffd57f5ca8396517e78590f3f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059446"
---
# <a name="create-an-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Criar um inquilino de Diretório Ativo Azure para ligações de protocolo VPN OpenVPN do utilizador

Ao ligar-se ao seu VNet, pode utilizar autenticação baseada em certificado ou autenticação RADIUS. No entanto, ao utilizar o protocolo Open VPN, também pode utilizar a autenticação do Diretório Ativo Azure. Este artigo ajuda-o a configurar um inquilino Azure AD para a autenticação VPN do utilizador virtual WAN (ponto-a-site) Open VPN.

> [!NOTE]
> A autenticação Azure AD é suportada apenas para ligações protocolares OpenVPN.&reg;
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Criar o inquilino da AD Azure

Crie um inquilino DaD Azure usando os passos no [Artigo criar um novo inquilino:](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Nome organizacional
* Nome de domínio inicial

Exemplo:

   ![Novo inquilino da AD Azure](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Criar utilizadores de inquilinos da AD Azure

Em seguida, crie duas contas de utilizador. Crie uma conta Global Admin e uma conta de utilizador principal. A conta de utilizador principal é utilizada como conta de incorporação principal (conta de serviço). Quando cria uma conta de utilizador de inquilinos Azure AD, ajusta a função de Diretório para o tipo de utilizador que pretende criar.

Use os passos [deste artigo](../active-directory/fundamentals/add-users-azure-active-directory.md) para criar pelo menos dois utilizadores para o seu inquilino Azure AD. Certifique-se de alterar a **Função de Diretório** para criar os tipos de conta:

* Admin Global
* Utilizador

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Ativar a autenticação adato da AD Azure no gateway VPN

1. Localize a id do Diretório do diretório que pretende utilizar para autenticação. Está listado na secção de propriedades da página Ative Directy.

    ![ID do diretório](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Copie o ID do Diretório.

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
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Selecione a conta **Global Admin** se solicitado.

    ![ID do diretório](./media/openvpn-create-azure-ad-tenant/pick.png)

6. **Selecione Aceitar** quando solicitado.

    ![Aceitar](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Sob o seu Azure AD, em **aplicações Enterprise,** vê **Azure VPN** listado.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Configure autenticação aD Azure para vpn do utilizador e atribua-a a um Hub Virtual seguindo os passos na [autenticação adado do Configure Azure para a ligação Ponto-a-Local ao Azure](virtual-wan-point-to-site-azure-ad.md)

## <a name="next-steps"></a>Passos seguintes

Para se ligar à sua rede virtual, deve criar e configurar um perfil de cliente VPN e associá-lo a um Hub Virtual. Consulte a [autenticação aditiva Do Configure Azure para a ligação Ponto-a-Local ao Azure](virtual-wan-point-to-site-azure-ad.md).
