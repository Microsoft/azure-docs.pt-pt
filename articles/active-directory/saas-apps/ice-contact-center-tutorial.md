---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com ice Contact Center / Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Ice Contact Center.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85c964d8-7c0f-458d-8df5-b7d0768f298d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/15/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37f06bbc91fef0f0d8c1c646082bd95452c7e518
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85605904"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ice-contact-center"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com ice Contact Center

Neste tutorial, você vai aprender a integrar o Ice Contact Center com o Azure Ative Directory (Azure AD). Quando integrar o Centro de Contacto de Gelo com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Centro de Contacto de Gelo.
* Ative os seus utilizadores a serem automaticamente inscritos no Ice Contact Center com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* subscrição ativada pelo Ice Contact Center (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Ice Contact Center suporta **SSO** iniciado SP
* Assim que configurar o Ice Contact Center, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-ice-contact-center-from-the-gallery"></a>Adicionar centro de contacto de gelo da galeria

Para configurar a integração do Ice Contact Center em Azure AD, é necessário adicionar o Ice Contact Center da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva o **Centro de Contacto de Gelo** na caixa de pesquisa.
1. Selecione **ice Contact Center** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ice-contact-center"></a>Configurar e testar Azure AD único sinal de inscrição para o Centro de Contacto de Gelo

Configure e teste Azure AD SSO com ice Contact Center usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Ice Contact Center.

Para configurar e testar o Azure AD SSO com o Ice Contact Center, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Ice Contact Center SSO](#configure-ice-contact-center-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie o utilizador](#create-ice-contact-center-test-user)** de teste ice Contact Center - para ter uma contraparte de B.Simon no Ice Contact Center que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **ice Contact Center,** encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão:`https://<TENANT>.iceuc.com/iceManager`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando um dos seguintes padrões:

        ```https
        https://<TENANT>-imrpool.icescape365.com:PORT/identity
        https://<TENANT>-imrpool.icescape.com:PORT/identity
        https://<TENANT>-imrpool.iceuc.com:PORT/identity
        ```

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando um dos seguintes padrões:

        ```https
        https://<TENANT>-imrpool.icescape365.com:PORT/identity
        https://<TENANT>-imrpool.icescape.com:PORT/identity
        https://<TENANT>-imrpool.iceuc.com:PORT/identity
    ```

    > [!NOTE]
    > These values are not real. Update these values with the actual Sign on URL, Identifier and Reply URL. Contact [ice Contact Center Client support team](mailto:support@computer-talk.com) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. On the **Set up single sign-on with SAML** page, In the **SAML Signing Certificate** section, click copy button to copy **App Federation Metadata Url** and save it on your computer.

    ![The Certificate download link](common/copy-metadataurl.png)

### Create an Azure AD test user

In this section, you'll create a test user in the Azure portal called B.Simon.

1. From the left pane in the Azure portal, select **Azure Active Directory**, select **Users**, and then select **All users**.
1. Select **New user** at the top of the screen.
1. In the **User** properties, follow these steps:
   1. In the **Name** field, enter `B.Simon`.  
   1. In the **User name** field, enter the username@companydomain.extension. For example, `B.Simon@contoso.com`.
   1. Select the **Show password** check box, and then write down the value that's displayed in the **Password** box.
   1. Click **Create**.

### Assign the Azure AD test user

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to ice Contact Center.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **ice Contact Center**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![The "Users and groups" link](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure ice Contact Center SSO

To configure single sign-on on **ice Contact Center** side, you need to send the **App Federation Metadata Url** to [ice Contact Center support team](mailto:support@computer-talk.com). They set this setting to have the SAML SSO connection set properly on both sides.

### Create ice Contact Center test user

In this section, you create a user called Britta Simon in ice Contact Center. Work with [ice Contact Center support team](mailto:support@computer-talk.com) to add the users in the ice Contact Center platform. Users must be created and activated before you use single sign-on.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the ice Contact Center tile in the Access Panel, you should be automatically signed in to the ice Contact Center for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Additional resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is conditional access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try ice Contact Center with Azure AD](https://aad.portal.azure.com/)

- [What is session control in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [How to protect ice Contact Center with advanced visibility and controls](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

