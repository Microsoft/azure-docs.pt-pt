---
title: 'Tutorial: Integração de Diretório Ativo Azure com saba TalentSpace [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Saba TalentSpace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c13a649e1c1888a11e02b83d969255615cdc67
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77561371"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>Tutorial: Azure Ative Directory integração individual (SSO) com saba TalentSpace

Neste tutorial, aprenderá a integrar o Saba TalentSpace com o Azure Ative Directory (Azure AD). Quando integrar o Saba TalentSpace com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Saba TalentSpace.
* Permita que os seus utilizadores sejam automaticamente inscritos no Saba TalentSpace com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada por assinatura (SSO) da Saba TalentSpace permitiu a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Saba TalentSpace apoia **SP** iniciado SSO
* Assim que configurar o Saba TalentSpace, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-saba-talentspace-from-the-gallery"></a>Adicionando Saba TalentSpace da galeria

Para configurar a integração do Saba TalentSpace em Azure AD, você precisa adicionar Saba TalentSpace da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **Saba TalentSpace** na caixa de pesquisa.
1. Selecione **Saba TalentSpace** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-saba-talentspace"></a>Configure e teste Azure AD único sign-on para Saba TalentSpace

Configure e teste Azure AD SSO com Saba TalentSpace usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Saba TalentSpace.

Para configurar e testar o Azure AD SSO com o Saba TalentSpace, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Saba TalentSpace SSO](#configure-saba-talentspace-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie o utilizador](#create-saba-talentspace-test-user)** de teste Saba TalentSpace - para ter uma contrapartida de B.Simon no Saba TalentSpace que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Saba TalentSpace,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://global.hgncloud.com/[companyname]/saml/login`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://global.hgncloud.com/[companyname]/saml/metadata`

    c. Na caixa de texto **"Assertion Consumer Service URL",** escreva um URL utilizando o seguinte padrão:`https://global.hgncloud.com/[companyname]/saml/SSO`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de [suporte do Cliente Saba TalentSpace](https://support.saba.com/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **'Criar' Saba TalentSpace,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, você vai permitir que B.Simon use o único sign-on Azure, concedendo acesso ao Saba TalentSpace.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Saba TalentSpace**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-saba-talentspace-sso"></a>Configure Saba TalentSpace SSO

1. Numa janela de navegador diferente, inscreva-se na sua aplicação **Saba TalentSpace** como administrador.

2. Clique na **opções** separador.
  
    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. No painel de navegação à esquerda, clique na **configuração SAML**.
  
    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. Na página de **Configuração SAML,** execute os seguintes passos:

    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. Como **identificador único,** selecione **NameID**.

    b. Como mapas de **identificador único para**, selecione **username**.
  
    c. Para fazer o upload do ficheiro de metadados descarregados, clique em **Navegar** para selecionar o ficheiro e, em seguida, **carregar o Ficheiro**.

    d. Para testar a configuração, clique no **Teste de Execução**.

    > [!NOTE]
    > Tens de esperar pela mensagem "*O teste SAML está completo. Por favor, fechem esta janela".* Em seguida, feche a janela do navegador aberta. A caixa de verificação **Enable SAML** só está ativada se o teste estiver concluído.

    e. Selecione **ativar saml**.

    f. Clique em **Guardar Alterações**.

### <a name="create-saba-talentspace-test-user"></a>Criar o utilizador de teste Saba TalentSpace

O objetivo desta secção é criar um utilizador chamado Britta Simon no Saba TalentSpace.

**Para criar um utilizador chamado Britta Simon no Saba TalentSpace, execute os seguintes passos:**

1. Inscreva-se na sua aplicação **Saba TalentSpace** como administrador.

2. Clique no separador **User Center** e, em seguida, clique em **Criar utilizador**.

    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. Na página de diálogo **new user,** execute os seguintes passos:

    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. Na caixa de texto **First Name,** digite o primeiro nome do utilizador como **B**.

    b. Na caixa de texto **Last Name,** digite o sobrenome do utilizador como **Simon**.

    c. Na caixa de texto **username,** tipo **B.Simon,** o nome de utilizador como no portal Azure.

    d. Na caixa de texto **password,** digite uma palavra-passe para B.Simon.

    e. Clique em **Guardar**.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Saba TalentSpace no Painel de Acesso, deve ser automaticamente inscrito no Saba TalentSpace para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente saba TalentSpace com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)