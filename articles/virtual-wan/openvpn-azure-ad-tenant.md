---
title: 'Inquilino Azure AD para ligações VPN utilizador: Autenticação AZURE AD'
description: Pode utilizar a VPN do utilizador Azure Virtual WAN (ponto a local) para ligar ao seu VNet utilizando a autenticação AZure AD
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: a54397fe7cfecef6813105645b7f2b218894875e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91367857"
---
# <a name="prepare-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Prepare o inquilino do Diretório Ativo Azure para as ligações de protocolo openVPN do utilizador VPN

Ao ligar-se ao seu Centro Virtual sobre o protocolo IKEv2, pode utilizar a autenticação baseada em certificados ou a autenticação RADIUS. No entanto, quando utilizar o protocolo OpenVPN, também pode utilizar a autenticação do Azure Ative Directory. Este artigo ajuda a configurar um inquilino Azure AD para o VPN do utilizador virtual WAN (ponto a local) usando a autenticação OpenVPN.

> [!NOTE]
> A autenticação Azure AD é suportada apenas para &reg; ligações de protocolo OpenVPN.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Criar o inquilino AZURE AD

Verifique se tem um inquilino da AD Azure. Se você não tem um inquilino AD Azure, você pode criar um usando os passos no [artigo criar um novo inquilina:](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Nome da organização
* Nome de domínio inicial

Exemplo:

   ![Novo inquilino da AD AZure](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Criar utilizadores inquilinos da AD Azure

Em seguida, crie duas contas de utilizador no recém-criado inquilino AZure AD, uma conta de administrador global e uma conta de utilizador. A conta de utilizador pode ser usada para testar a autenticação OpenVPN e a conta de administrador Global será usada para conceder consentimento ao registo da aplicação Azure VPN. Depois de ter criado uma conta de utilizador Azure AD, atribui um **Papel de Diretório** ao utilizador para delegar permissões administrativas.

Use os passos [deste artigo](../active-directory/fundamentals/add-users-azure-active-directory.md) para criar os dois utilizadores para o seu inquilino AZURE AD. Certifique-se de alterar o **Papel de Diretório** numa das contas criadas para **administrador global.**

## <a name="3-grant-consent-to-the-azure-vpn-app-registration"></a><a name="enable-authentication"></a>3. Conceder consentimento ao registo da aplicação Azure VPN

1. Inscreva-se no Portal Azure como um utilizador que é atribuído à **função de administrador Global.**

2. Em seguida, conceder o consentimento administrativo para a sua organização, isto permite que a aplicação Azure VPN inscreva-se e leia os perfis do utilizador. Copie e cole o URL que diz respeito à sua localização de implantação na barra de endereço do seu navegador:

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

3. Selecione a conta **de administrador Global** se solicitada.

    ![ID do diretório](./media/openvpn-create-azure-ad-tenant/pick.png)

4. Selecione **Aceitar** quando solicitado.

    ![Screenshot mostra caixa de diálogo com a mensagem Permissões solicitadas Aceite para a sua organização e informações adicionais.](./media/openvpn-create-azure-ad-tenant/accept.jpg)

5. Sob o seu Azure AD, em **aplicações enterprise,** você deve agora ver **Azure VPN** listado.

    ![VPN do Azure](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

## <a name="next-steps"></a>Passos seguintes

Para se ligar às suas redes virtuais utilizando a autenticação Azure AD, tem de criar uma configuração VPN do Utilizador e associá-la a um Hub Virtual. Consulte [a autenticação AD configurada para a ligação ponto-a-local ao Azure](virtual-wan-point-to-site-azure-ad.md).
