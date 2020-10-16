---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/21/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8172e0e208d9a780c7676d7cb0e77dbc1c16f493
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606274"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Criar o inquilino AZURE AD

Criar um inquilino AZure AD usando os passos no novo artigo de [inquilino:](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Nome organizacional
* Nome de domínio inicial

  Exemplo:

   ![Novo inquilino da AD AZure](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. Criar utilizadores inquilinos

Neste passo, você cria dois utilizadores inquilinos Azure AD: uma conta Global Admin e uma conta de utilizador principal. A conta principal do utilizador é utilizada como a sua conta principal de incorporação (conta de serviço). Quando cria uma conta de utilizador de inquilino Azure AD, ajusta a função de Diretório para o tipo de utilizador que pretende criar. Use os passos [deste artigo](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) para criar pelo menos dois utilizadores para o seu inquilino AZURE AD. Certifique-se de alterar a **Função de Diretório** para criar os tipos de conta:

* Admin Global
* Utilizador

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. Registar o Cliente VPN

Registe o cliente VPN no inquilino da Ad Azure.

1. Localize a identificação do diretório do diretório que pretende utilizar para autenticação. Está listado na secção de propriedades da página Ative Directory.

    ![ID do diretório](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Copie o ID do Diretório.

3. Inscreva-se no portal Azure como um utilizador que é atribuído à **função de administrador Global.**

4. Em seguida, dê consentimento administrativo. Copie e cole o URL que diz respeito à sua localização de implantação na barra de endereço do seu navegador:

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
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Selecione a conta **Global Admin** se solicitado.

    ![ID do diretório](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Selecione **Aceitar** quando solicitado.

    ![A screenshot mostra uma janela com a mensagem Permissões solicitadas Aceitar para si organização e informações sobre o pedido.](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Sob o seu Azure AD, em **aplicações Enterprise,** você verá **Azure VPN** listado.

     ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. Registar pedidos adicionais

Neste passo, regista-se aplicações adicionais para vários utilizadores e grupos.

1. No seu Diretório Ativo Azure, clique nas **inscrições da App** e, em seguida, **+ Novo registo.**

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Na página **registar uma inscrição,** insira o **Nome**. Selecione os **tipos de conta suportado desejados**e, em seguida, clique em **Registar**.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Uma vez registada a nova aplicação, clique em **Expor uma API** sob a lâmina da aplicação.

4. Clique **+ Adicione um âmbito.**

5. Deixe o **ID URI de aplicação predefinido**. Clique **em Guardar e continuar.**

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Preencha os campos necessários e certifique-se de que **o Estado** está **Ativado**. Clique **em Adicionar âmbito**.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Clique **em Expor uma API** em seguida + Adicionar uma **aplicação ao cliente.**  Para **iD do cliente,** insira os seguintes valores dependendo da nuvem:

    - Insira **41b23e61-6c1e-4545-b367-cd054e0ed4b4** para Azure **Public**
    - Insira **51bb15d4-3a4f-4ebf-9dca-40096fe32426** para **o Governo** de Azure
    - Insira **538ee9e6-310a-468d-afef-ea97365856a9** para Azure **Alemanha**
    - Insira **49f817b6-84ae-4cc0-928c-73f27289b3aa** para Azure **China 21Vianet**

8. Clique **na aplicação Adicionar**.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Copie o **ID de Aplicação (cliente)** na página **'Visão Geral'.** Necessitará desta informação para configurar o seu gateway(s) VPN.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Repita os passos nesta secção [de aplicações adicionais](#register-apps) para criar o número de aplicações necessárias para o seu requisito de segurança. Cada aplicação será associada a um gateway VPN e pode ter um conjunto diferente de utilizadores. Apenas uma aplicação pode ser associada a um portal.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. Atribuir utilizadores a aplicações

Atribua os utilizadores às suas aplicações.

1. Sob **as aplicações Azure AD -> Enterprise,** selecione a aplicação recém-registada e clique em **Propriedades**. Certifique-se de que a **yes**atribuição do utilizador é **necessária?** Clique em **Guardar**.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Na página da aplicação, clique em **Utilizadores e grupos**e, em seguida, clique **em +Adicionar utilizador**.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. Em **'Adicionar Atribuição'** clique nos **Utilizadores e grupos**. Selecione os utilizadores a quem pretende aceder a esta aplicação VPN. Clique em **Selecionar**.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/user4.png)
