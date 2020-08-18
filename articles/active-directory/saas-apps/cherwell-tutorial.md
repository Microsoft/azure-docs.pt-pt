---
title: 'Tutorial: Integração do Azure Ative Directory com a Cherwell Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Cherwell.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: e506a6d8a309f844018d33285b82a490e2a3c93c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88529496"
---
# <a name="tutorial-azure-active-directory-integration-with-cherwell"></a>Tutorial: Integração do Diretório Ativo Azure com Cherwell

Neste tutorial, aprende-se a integrar Cherwell com o Azure Ative Directory (Azure AD).
A integração de Cherwell com Azure AD proporciona-lhe os seguintes benefícios:

* Podes controlar o Azure AD que tem acesso a Cherwell.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos em Cherwell (Sign-on Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a Cherwell, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura ativada única de Cherwell

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Cherwell apoia **SP** iniciado SSO

## <a name="adding-cherwell-from-the-gallery"></a>Adicionando Cherwell da galeria

Para configurar a integração de Cherwell em Azure AD, você precisa adicionar Cherwell da galeria à sua lista de aplicações geridas saaS.

**Para adicionar Cherwell da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Cherwell,** selecione **Cherwell** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

    ![Cherwell na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Cherwell com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Cherwell.

Para configurar e testar o Azure AD com Cherwell, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único de Cherwell](#configure-cherwell-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Cherwell test user](#create-cherwell-test-user)** - ter uma contraparte de Britta Simon em Cherwell que está ligada à representação AZure AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com Cherwell, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Cherwell,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração de 'Sessão única' com** a página SAML, clique em **Editar** o ícone para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute o seguinte passo:

    ![Cherwell Domain e URLs informações únicas de assinatura](common/sp-signonurl.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<companyname>.cherwellondemand.com/cherwellclient`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de inscrição real. Contacte a [equipa de apoio ao Cliente Cherwell](https://cherwellsupport.com/CherwellPortal) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração de 'Sessão Única' com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Cherwell,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-cherwell-single-sign-on"></a>Configurar Cherwell Single Sign-On

Para configurar um único sign-on no lado **cherwell,** você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de [apoio cherwell](https://cherwellsupport.com/CherwellPortal). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

> [!NOTE]
> A tua equipa de apoio cherwell tem de fazer a configuração SSO real. Receberá uma notificação quando o SSO tiver sido ativado para a sua subscrição.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome,** **insira BrittaSimon**.
  
    b. No campo **nome do utilizador,** escreva `brittasimon\@yourcompanydomain.extension` . Por exemplo, BrittaSimon@contoso.com.

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Cherwell.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Cherwell**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Cherwell.**

    ![O link Cherwell na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-cherwell-test-user"></a>Criar utilizador de teste cherwell

Para permitir que os utilizadores da Azure AD inscrevam-se em Cherwell, devem ser adsajados em Cherwell. No caso da Cherwell, as contas de utilizador precisam de ser criadas pela sua equipa de [apoio cherwell.](https://cherwellsupport.com/CherwellPortal)

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador cherwell ou APIs fornecidas pela Cherwell para fornecer contas de utilizador do Azure Ative Directory.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo cherwell no Painel de Acesso, deverá ser automaticamente inscrito no Cherwell para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
