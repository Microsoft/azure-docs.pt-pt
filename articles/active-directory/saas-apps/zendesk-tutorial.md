---
title: 'Tutorial: Integração do Azure Ative Directory com o Zendesk Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Zendesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: jeedes
ms.openlocfilehash: 6059f0b3c102fed04c52b87a2c7c226d508184c1
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519061"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zendesk"></a>Tutorial: Azure Ative Directory integração única (SSO) com Zendesk

Neste tutorial, você vai aprender a integrar Zendesk com Azure Ative Directory (Azure AD). Quando integrar o Zendesk com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Zendesk.
* Permita que os seus utilizadores sejam automaticamente inscritos no Zendesk com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por Zendesk (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Zendesk apoia **SP** iniciado SSO
* Zendesk suporta fornecimento [ **automatizado** de utilizadores](zendesk-provisioning-tutorial.md)
* Uma vez configurado Zendesk, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-zendesk-from-the-gallery"></a>Adicionando Zendesk da galeria

Para configurar a integração do Zendesk no AD Azure, precisa adicionar Zendesk da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **da galeria,** digite **Zendesk** na caixa de pesquisa.
1. Selecione **Zendesk** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-zendesk"></a>Configurar e testar Azure AD SSO para Zendesk

Configure e teste Azure AD SSO com Zendesk usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Zendesk.

Para configurar e testar o Azure AD SSO com Zendesk, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Zendesk SSO](#configure-zendesk-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Zendesk test user](#create-zendesk-test-user)** - para ter uma contraparte de B.Simon em Zendesk que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Zendesk,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<subdomain>.zendesk.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<subdomain>.zendesk.com`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.zendesk.com/access/saml`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real em URL, Identifier e URL de resposta. Contacte [a equipa de suporte do Cliente Zendesk](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação Zendesk espera as afirmações DOL num formato específico. Não existem atributos SAML obrigatórios, mas opcionalmente pode gerir a partir da secção **Atributos** do Utilizador na página de integração de aplicações. Na **configuração single Sign-On com** a página SAML, clique no botão **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Utiliza atributos de extensão para adicionar atributos que não estão em Azure AD por padrão. Clique [nos atributos do Utilizador que podem ser definidos em SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) para obter a lista completa de atributos SAML que **o Zendesk** aceita.

1. Na secção **Certificado de Assinatura SAML,** clique em Editar o botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

1. Na secção **Certificado de Assinatura SAML,** copie o **Valor de Impressão Digital** e guarde-o no seu computador.

    ![Valor da impressão digital do polegar da cópia](common/copy-thumbprint.png)

1. Na secção **Configurar Zendesk,** copie os URL(s) apropriados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Zendesk.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Zendesk**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-zendesk-sso"></a>Configurar Zendesk SSO

1. Para automatizar a configuração dentro do **Zendesk,** é necessário instalar a **extensão do navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![image](./media/target-process-tutorial/install_extension.png)

1. Depois de adicionar extensão ao navegador, clique na **configuração Zendesk** irá direcioná-lo para a aplicação Zendesk. A partir daí, forneça as credenciais de administração para assinar em Zendesk. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o Zendesk manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Zendesk como administrador e execute os seguintes passos:

1. No **Centro de Administração Zendesk,** clique nas **definições de Segurança** no separador **Segurança.**

    ![Segurança](./media/zendesk-tutorial/settings.png "Segurança")

1. Aceda à página **de assinatura única** e clique em **Editar** no **SAML**.

    ![Segurança](./media/zendesk-tutorial/saml-sso.png "Segurança")

1. Execute os seguintes passos na página **SSO.**

    ![Início de sessão único](./media/zendesk-tutorial/saml-configuration.png "Início de sessão único")

    a. Na caixa de texto **URL SSO SSO SAML,** cole o valor do URL de **login** que copiou do portal Azure.

    b. Na caixa de texto **de impressão digital de certificado,** cole o valor de impressão **digital** do certificado que copiou do portal Azure.

    c. Na caixa de texto **url de logout remoto,** cole o valor do **URL logout** que copiou do portal Azure.

    d. Clique em **Guardar**.

### <a name="create-zendesk-test-user"></a>Criar utilizador de teste Zendesk

O objetivo desta secção é criar um utilizador chamado Britta Simon em Zendesk. Zendesk suporta o fornecimento automático do utilizador, que é por padrão ativado. Pode encontrar mais detalhes [aqui](Zendesk-provisioning-tutorial.md) sobre como configurar o fornecimento automático do utilizador.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Zendesk no Painel de Acesso, deverá ser automaticamente inscrito no Zendesk para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente Zendesk com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger Zendesk com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)

- [Configurar o Provisionamento do Utilizador](zendesk-provisioning-tutorial.md)