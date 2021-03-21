---
title: 'VPN Gateway: Inquilino Azure AD para ligações P2S VPN: Autenticação Azure AD'
description: Saiba como configurar um inquilino AZure AD para a autenticação P2S Open VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: bff1eec0152ab0f57edd212adf6b14f7b588fb51
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100390169"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Criar um inquilino do Azure Active Directory para as ligações do protocolo OpenVPN P2S

Ao ligar-se ao seu VNet, pode utilizar a autenticação baseada em certificados ou a autenticação RADIUS. No entanto, quando utilizar o protocolo Open VPN, também pode utilizar a autenticação do Azure Ative Directory. Este artigo ajuda a configurar um inquilino Azure AD para a autenticação P2S Open VPN.

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Verifique o inquilino da Ad Azure

Verifique se tem um inquilino da AD Azure. Se você não tem um inquilino AD Azure, você pode criar um usando os passos no [artigo criar um novo inquilina:](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Nome organizacional
* Nome de domínio inicial

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/newtenant.png" alt-text="Novo inquilino da AD AZure" border="false":::

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Criar utilizadores inquilinos da AD Azure

O seu inquilino Azure AD precisa das seguintes contas: uma conta Global Admin e uma conta de utilizador principal. A conta principal do utilizador é utilizada como a sua conta principal de incorporação (conta de serviço). Quando cria uma conta de utilizador de inquilino Azure AD, ajusta a função de Diretório para o tipo de utilizador que pretende criar.

Utilize os passos em [Adicionar ou apagar utilizadores - Azure Ative Directy](../active-directory/fundamentals/add-users-azure-active-directory.md) para criar pelo menos dois utilizadores para o seu inquilino AZure AD. Certifique-se de alterar a **Função de Diretório** para criar os tipos de conta:

* Admin Global
* User

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Ativar a autenticação AD AZure no gateway VPN

1. Localize a identificação do diretório do diretório que pretende utilizar para autenticação. Está listado na secção de propriedades da página do Ative Directory.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/directory-id.png" alt-text="Screenshot que mostra as Propriedades do Diretório" lightbox="./media/openvpn-create-azure-ad-tenant/directory-id.png":::

1. Copie o ID do Diretório.

1. Inscreva-se no portal Azure como um utilizador que é atribuído à **função de administrador Global.**

1. Em seguida, dê consentimento administrativo. Copie e cole o URL que diz respeito à sua localização de implantação na barra de endereço do seu navegador:

   Público

   ```
   https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
   ````

   Azure Government

   ```
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
   ````

   Microsoft Cloud Alemanha

   ```
   https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
   ````

    Azure China 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

   > [!NOTE]
   > Se utilizar uma conta de administração global que não seja nativa do inquilino da Azure AD para dar o seu consentimento, por favor substitua o id "comum" com o id do diretório AD Azure no URL. Também pode ter de substituir o "comum" pelo seu id de diretório em certos outros casos.
   >

1. Selecione a conta **Global Admin** se solicitado.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/pick.png" alt-text="Escolher uma conta" border="false":::
1. Selecione **Aceitar** quando solicitado.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/accept.jpg" alt-text="Screenshot mostra a mensagem Permissões solicitadas Aceitar para a sua organização com detalhes e a opção de aceitar." border="false":::
1. Sob o seu Azure AD, em **aplicações enterprise,** você vê **Azure VPN** listado.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azurevpn.png" alt-text="Screenshot que mostra a página todas as aplicações." lightbox="./media/openvpn-create-azure-ad-tenant/azurevpn.png" :::
1. Se ainda não tiver um ambiente local funcional, siga as instruções para criar um. Consulte [Criar uma VPN ponto-a-local](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para criar e configurar uma porta VPN ponto a local.

    > [!IMPORTANT]
    > O SKU Básico não é suportado para a OpenVPN.

1. Ativar a autenticação AZure AD no gateway VPN navegando para a **configuração ponto-a-local** e escolhendo **o OpenVPN (SSL)** como o **tipo de túnel**. Selecione **O Diretório Ativo Azure** como o **tipo de Autenticação,** em seguida, preencha as informações na secção **Diretório Ativo Azure.**

   * **Inquilino:** TenantID para o inquilino da AD Azure ```https://login.microsoftonline.com/{AzureAD TenantID}/```

   * **Público:** AplicaçãoID da app "Azure VPN" Azure AD Enterprise ```{AppID of the "Azure VPN" AD Enterprise app}```

   * **Emitente**: URL do Serviço De Token Seguro ```https://sts.windows.net/{AzureAD TenantID}/```


   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azure-ad-auth-portal.png" alt-text="SAzure VPN" border="false":::

   > [!NOTE]
   > Certifique-se de incluir um corte de fuga no final do `AadIssuerUri` valor. Caso contrário, a ligação pode falhar.
   >

1. Crie e descarregue o perfil clicando no link de **cliente VPN de descarregamento.**

1. Extraia o ficheiro zip descarregado.

1. Navegue na pasta "AzureVPN" desapertada.

1. Tome nota da localização do ficheiro "azurevpnconfig.xml". O azurevpnconfig.xml contém a definição para a ligação VPN e pode ser importado diretamente para a aplicação do Cliente Azure VPN. Também pode distribuir este ficheiro a todos os utilizadores que necessitem de se conectar por e-mail ou outros meios. O utilizador necessitará de credenciais AZure AD válidas para se conectar com sucesso.

## <a name="next-steps"></a>Passos seguintes

Criar e configurar um perfil de cliente VPN. Consulte [configurar um cliente VPN para ligações P2S VPN](openvpn-azure-ad-client.md).
