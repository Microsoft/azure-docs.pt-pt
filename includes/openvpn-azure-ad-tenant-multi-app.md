---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/21/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2eb2dbc43c59f4f6301c7f5073a73462639d35b2
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797171"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Criar o inquilino da AD Azure

Crie um inquilino DaD Azure usando os passos no [Artigo criar um novo inquilino:](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Nome organizacional
* Nome de domínio inicial

  Exemplo:

   ![Novo inquilino da AD Azure](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. Criar utilizadores de inquilinos

Neste passo, cria-se dois utilizadores de inquilinos Da AD Azure: uma conta Global Admin e uma conta de utilizador principal. A conta de utilizador principal é utilizada como conta de incorporação principal (conta de serviço). Quando cria uma conta de utilizador de inquilinos Azure AD, ajusta a função de Diretório para o tipo de utilizador que pretende criar. Use os passos [deste artigo](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) para criar pelo menos dois utilizadores para o seu inquilino Azure AD. Certifique-se de alterar a **Função de Diretório** para criar os tipos de conta:

* Admin Global
* Utilizador

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. Registe o Cliente VPN

Registe o cliente VPN no inquilino da AD Azure.

1. Localize a id do Diretório do diretório que pretende utilizar para autenticação. Está listado na secção de propriedades da página Ative Directy.

    ![ID do diretório](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Copie o ID do Diretório.

3. Inscreva-se no portal Azure como um utilizador que lhe é atribuído o papel de **administrador global.**

4. Em seguida, dê consentimento à administração. Copie e cole o URL que diz respeito à sua localização de implementação na barra de endereços do seu navegador:

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

6. **Selecione Aceitar** quando solicitado.

    ![Aceitar](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Sob o seu Azure AD, em **aplicações Da Enterprise,** verá **azure VPN** listado.

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. Registar pedidos adicionais

Neste passo, regista aplicações adicionais para vários utilizadores e grupos.

1. Sob o seu Diretório Ativo Azure, clique nas **inscrições da App** e **depois + nova inscrição**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. No Registo de uma página de **inscrição,** insira o **Nome**. Selecione os tipos de **conta suportados desejados**e, em seguida, clique em **Registar**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Uma vez registada a nova aplicação, clique em **Expor uma API** sob a lâmina da aplicação.

4. Clique **+ Adicione um alcance**.

5. Deixe o **ID DE Aplicação**padrão URI . Clique em **Guardar e continuar**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Preencha os campos necessários e certifique-se de que o **Estado** está **ativado**. Clique em **Adicionar âmbito**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Clique em **expor uma API** e depois **+ adicionar uma aplicação de cliente**.  Para **ID do cliente,** insira os seguintes valores dependendo da nuvem:

    - Insira **41b23e61-6c1e-4545-b367-cd054e0ed4b4** para O **Público** Azure
    - Insira **51bb15d4-3a4f-4ebf-9dca-40096fe32426** para o **Governo** azure
    - Insira **538ee9e6-310a-468d-afef-ea97365856a9** para a **Azure Germany**
    - Insira **49f817b6-84ae-4cc0-928c-73f27289b3aa** para Azure **China 21Vianet**

8. Clique na **aplicação Adicionar**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Copie o ID de **Aplicação (cliente)** na página **'Visão Geral'.** Necessitará desta informação para configurar o seu gateway VPN.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Repita os passos nesta secção de [aplicações adicionais](#register-apps) para criar o maior número de aplicações necessárias para o seu requisito de segurança. Cada aplicação será associada a um gateway VPN e pode ter um conjunto diferente de utilizadores. Apenas uma aplicação pode ser associada a um portal.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. Atribuir utilizadores a aplicações

Designar os utilizadores para as suas aplicações.

1. Nas **aplicações Azure AD -> Enterprise,** selecione a aplicação recém-registada e clique em **Propriedades**. Certifique-se de que a **yes** **atribuição do Utilizador exigida?** Clique em **Guardar**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Na página da aplicação, clique em **Utilizadores e grupos,** e clique em **+Adicionar o utilizador**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. Em **Função adicionar,** clique em **Utilizadores e grupos**. Selecione os utilizadores que pretende aceder a esta aplicação VPN. Clique em **Selecionar**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)
