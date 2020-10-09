---
title: 'Tutorial: Integração do Azure Ative Directory com o Zoho One Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Zoho One.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: baa33e79b3bba8eccb2d0fc03e110e1a3c4defde
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546011"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Tutorial: Integração do Diretório Ativo Azure com o Zoho One

Neste tutorial, aprende-se a integrar o Zoho One com o Azure Ative Directory (Azure AD).
A integração do Zoho One com a AD Azure proporciona-lhe os seguintes benefícios:

* Podes controlar em Azure AD que tem acesso ao Zoho One.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Zoho One (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o Zoho One, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Zoho Uma única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Zoho One apoia **SP** e **IDP** iniciado SSO

## <a name="adding-zoho-one-from-the-gallery"></a>Adicionando Zoho One da galeria

Para configurar a integração do Zoho One em AD Azure, precisa adicionar o Zoho One da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Zoho One da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Zoho One**, selecione **Zoho One** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Zoho One na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Zoho One com base em um utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Zoho One.

Para configurar e testar o Azure AD com o Zoho One, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure zoho One Single Sign-On](#configure-zoho-one-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Zoho One test user](#create-zoho-one-test-user)** - para ter uma contraparte de Britta Simon no Zoho One que está ligada à representação AD AZure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com Zoho One, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Zoho One,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![Zoho One Domain e URLs informações únicas de súmis](common/idp-relay.png)

    a. Na caixa de texto **identifier,** digite um URL: `one.zoho.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > O valor **URL de resposta** anterior não é real. Você receberá o `<saml-identifier>` valor de #step4 da secção **Configure Zoho One Single Sign-On** , que é explicada mais tarde no tutorial.

    c. Clique **em Definir URLs adicionais**.

    d. Na caixa de texto **do Estado de Retransmissão,** digite um URL: `https://one.zoho.com`

5. Se desejar configurar a aplicação no modo iniciado pela **SP,** execute o seguinte passo:


    ![Zoho One Domain e URLs informações únicas de súmis](common/both-signonurl.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > O valor **URL de inscrição** anterior não é real. Irá atualizar o valor com o URL de Sign-On real da secção **Configure Zoho One Single Sign-On,** que é explicada mais tarde no tutorial. 

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção **Configurar Zoho One,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-zoho-one-single-sign-on"></a>Configure Zoho One Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no seu site da empresa Zoho One como administrador.

2. No separador **Organização,** clique em **Configuração** sob **autenticação SAML**.

    ![Zoho One org](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. Na página Pop-up execute os seguintes passos:

    ![Zoho One sig](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. Na caixa de texto **URL de entrada,** cole o valor do URL de **login,** que copiou do portal Azure.

    b. Na caixa de texto **URL de assinatura,** cole o valor do **URL logout,** que copiou do portal Azure.

    c. Clique **em Procurar** para fazer o upload do Certificado **(Base64)** que descarregou a partir do portal Azure.

    d. Clique em **Guardar**.

4. Depois de guardar a configuração de autenticação SAML, copie o valor **DO IDENTIFICADOR SAML** e apique-o com o **URL de resposta** em vez `<saml-identifier>` de, como e colar o valor `https://accounts.zoho.com/samlresponse/one.zoho.com` gerado na caixa de texto URL **resposta** na secção **de Configuração SAML Básica.**

    ![Zoho One saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Vá ao **separador Domínios** e, em seguida, clique em **Adicionar Domínio**.

    ![Domínio Zoho One](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. Na página **Add Domain,** execute os seguintes passos:

    ![Zoho One adicionar domínio](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. Na caixa de texto **do Nome de Domínio,** escreva o domínio como contoso.com.

    b. Clique em **Adicionar**.

    >[!Note]
    >Depois de adicionar o domínio siga [estes](https://www.zoho.com/one/help/admin-guide/domain-verification.html) passos para verificar o seu domínio. Assim que o domínio for verificado, utilize o seu nome de domínio em **URL de acesso na** secção de **configuração SAML básica** no portal Azure.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **do nome do utilizador** `brittasimon@yourcompanydomain.extension` . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao Zoho One.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Zoho One**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Zoho One**.

    ![O link Zoho One na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-zoho-one-test-user"></a>Criar utilizador de teste Zoho One

Para permitir que os utilizadores de Azure AD inscrevam-se no Zoho One, devem ser a provisionados no Zoho One. No Zoho One, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no Zoho One como administrador de segurança.

2. No **separador Utilizadores,** clique no **logótipo do utilizador.**

    ![Zoho Um utilizador](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Na página **'Adicionar Utilizador',** execute os seguintes passos:

    ![Zoho One adicionar utilizador](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. Na caixa de texto **Name,** insira o nome de utilizador como **Britta simon**.
    
    b. Na caixa de texto **do Endereço de E-mail,** insira o e-mail do utilizador como brittasimon@contoso.com .

    >[!Note]
    >Selecione o seu domínio verificado na lista de domínios.

    c. Clique em **Adicionar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Zoho One no Painel de Acesso, deverá ser automaticamente inscrito no Zoho One para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

