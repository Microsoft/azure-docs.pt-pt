---
title: 'Tutorial: Integração do Azure Ative Directory com skyDesk email | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o SkyDesk Email.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 30e94c9737241ff49c29898adcc5e50c6b73a9b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516042"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Tutorial: Integração do Azure Ative Directory com o SkyDesk Email

Neste tutorial, aprende a integrar o SkyDesk Email com o Azure Ative Directory (Azure AD).
A integração do SkyDesk Email com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a SkyDesk Email.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no SkyDesk Email (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o SkyDesk Email, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* SkyDesk Email única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* SkyDesk Email suporta **SP** iniciado SSO

## <a name="adding-skydesk-email-from-the-gallery"></a>Adicionar e-mail SkyDesk da galeria

Para configurar a integração do SkyDesk Email em AD Azure, precisa adicionar o SkyDesk Email da galeria à sua lista de aplicações geridas para o SaaS.

**Para adicionar SkyDesk Email da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SkyDesk Email**, selecione **SkyDesk Email** do painel de resultados e clique em **Adicionar** botão para adicionar a aplicação.

     ![SkyDesk Email na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o SkyDesk Email com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no SkyDesk Email.

Para configurar e testar o Azure AD com o SkyDesk Email, tem de completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o SkyDesk Email Single Sign-On](#configure-skydesk-email-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create SkyDesk Email test user](#create-skydesk-email-test-user)** - para ter uma contraparte de Britta Simon no SkyDesk Email que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com SkyDesk Email, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **SkyDesk Email,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![SkyDesk Email Domain e URLs informações únicas de súbdido](common/sp-signonurl.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a [equipa de suporte do Cliente do SkyDesk email](https://www.skydesk.jp/apps/support/) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção Configurar o **Email SkyDesk,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-skydesk-email-single-sign-on"></a>Configurar skydesk e-mail único Sign-On

1. Num navegador web diferente, inscreva-se na sua conta de Email SkyDesk como administrador.

1. No menu em cima, clique em **Configuração** e selecione **Org**.

    ![A screenshot mostra Org selecionado a partir do menu Configuração.](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. Clique em **Domínios** a partir do painel esquerdo.

    ![A screenshot mostra domínios selecionados do Painel de Controlo.](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. Clique em **Adicionar Domínio**.

    ![A screenshot mostra Add Domain selecionado.](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. Insira o nome de Domínio e, em seguida, verifique o Domínio.

    ![A screenshot mostra o separador 'Adicionar Domínio' onde pode introduzir o seu domínio.](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. Clique na **autenticação SAML** a partir do painel esquerdo.

    ![A screenshot mostra a autenticação SAML selecionada do Painel de Controlo.](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Na página de diálogo de **autenticação SAML,** execute os seguintes passos:

    ![A screenshot mostra a caixa de diálogo detalhes de autenticação SAML onde pode introduzir os valores descritos.](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > Para utilizar a autenticação baseada em SAML, deve ter **verificado o domínio** ou a configuração do URL do **portal.** Pode definir o URL do portal com o nome único.

    ![A screenshot mostra o Portal U R L onde introduz o nome.](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. Na caixa de texto **url de login,** cole o valor do URL de **login,** que copiou do portal Azure.

    b. Na caixa de textos **LOGOUT** URL, cole o valor do **URL logout,** que copiou do portal Azure.

    c. **Alterar o URL da palavra-passe** é opcional, por isso deixe-o em branco.

    d. Clique em **Obter Chave A partir de Ficheiro** para selecionar o certificado descarregado a partir do portal Azure e, em seguida, clique em **Abrir** para carregar o certificado.

    e. Como **algoritmo,** selecione **RSA**.

    f. Clique **em Ok** para guardar as alterações.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador****brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao SkyDesk Email.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **SkyDesk Email**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **SkyDesk Email**.

    ![O link de e-mail SkyDesk na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-skydesk-email-test-user"></a>Criar utilizador de teste de e-mail SkyDesk

Nesta secção, cria-se um utilizador chamado Britta Simon no SkyDesk Email.

Clique no Acesso ao **Utilizador** a partir do painel esquerdo no SkyDesk Email e, em seguida, insira o seu nome de utilizador.

![A screenshot mostra o Acesso ao Utilizador selecionado do Painel de Controlo.](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> Se precisar de criar utilizadores a granel, tem de contactar a equipa de suporte do [Cliente de Email SkyDesk.](https://www.skydesk.jp/apps/support/)

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do SkyDesk no Painel de Acesso, deverá ser automaticamente inscrito no Email SkyDesk para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)