---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com a Cloud Academy - SSO Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Cloud Academy - SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 560c86fb-e25c-4428-a1dd-a5711cfece5c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb118e4d2fa811bf88ff13db84848ebb230ed209
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294903"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloud-academy---sso"></a>Tutorial: Azure Ative Directory single sign-on (SSO) integração com Cloud Academy - SSO

Neste tutorial, você vai aprender a integrar cloud Academy - SSO com Azure Ative Directory (Azure AD). Quando integras a Cloud Academy - SSO com Azure AD, podes:

* Controle em Azure AD que tem acesso à Cloud Academy - SSO.
* Permita que os seus utilizadores sejam automaticamente inscritos na Cloud Academy - SSO com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Cloud Academy - SSO única assinatura ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Cloud Academy - SSO suporta **SSO** iniciado SP

* Assim que configurar a Cloud Academy - SSO, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-cloud-academy---sso-from-the-gallery"></a>Adicionar Cloud Academy - SSO da galeria

Para configurar a integração da Cloud Academy - SSO em AZure AD, precisa adicionar Cloud Academy - SSO da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** **digite Cloud Academy - SSO** na caixa de pesquisa.
1. Selecione **Cloud Academy - SSO** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>Configurar e testar Azure AD SSO para cloud Academy - SSO

Configure e teste Azure AD SSO com Cloud Academy - SSO usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Cloud Academy - SSO.

Para configurar e testar Azure AD SSO com Cloud Academy - SSO, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure cloud Academy-SSO SSO](#configure-cloud-academy-sso-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Cloud Academy-SSO test user](#create-cloud-academy-sso-test-user)** - para ter uma contrapartida de B.Simon na Cloud Academy - SSO que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Cloud Academy - SSO,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:`https://cloudacademy.com/login/enterprise/`

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o Url de **metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Create** (Criar).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Cloud Academy - SSO.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Cloud Academy - SSO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-cloud-academy-sso-sso"></a>Configurar cloud academy-sso SSO

1. Numa janela de navegador diferente, inscreva-se no site da cloud Academy - SSO como administrador.

1. Clique no nome da empresa e selecione **Definições & Integrações** do menu.

    ![Configuração ](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. Na página **Definições & Integrações,** aceda ao separador **Integrações** e clique no cartão **SSO.**

    ![Configuração ](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. Execute os seguintes passos na seguinte página:

    ![Configuração ](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. Na caixa de texto **do URL do ID da Entidade,** cole o valor **de ID** da Entidade que copiou a partir do portal Azure.

    b. Na caixa de texto **SSO URL,** cole o valor URL de **login** que copiou a partir do portal Azure.

    c. Abra o Certificado descarregado **(Base64)** do portal Azure para o Bloco de Notas e cole o conteúdo na caixa de texto **do Certificado.**

    d. Na caixa de texto **do formato de identificação** do nome, o valor predefinido,`urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`

1. Clique no botão **Guardar.**

    > [!NOTE]
    > Para obter mais informações sobre como configurar a Cloud Academy - SSO, consulte [o artigo de suporte](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On).

### <a name="create-cloud-academy-sso-test-user"></a>Criar utilizador de teste Cloud Academy-SSO

1. Faça login na **Cloud Academy - SSO** .

1. Clique no nome da empresa e selecione **Membros** do menu.

    ![ Criar utilizador de teste ](./media/cloud-academy-sso-tutorial/create-user.PNG)

1. Clique em **Convidar Membros** e selecione **Convidar um Único Membro**.

    ![ Criar utilizador de teste ](./media/cloud-academy-sso-tutorial/create-user-1.PNG)

1. Introduza os campos necessários e clique no **Convidar.**

    ![ Criar utilizador de teste ](./media/cloud-academy-sso-tutorial/create-user-2.PNG)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar na Cloud Academy - Azulejo SSO no Painel de Acesso, deverá ser automaticamente inscrito na Cloud Academy - SSO para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente cloud academy - SSO com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a Cloud Academy - SSO com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)