---
title: 'Tutorial: Integração do Diretório Ativo Azure com | da LearnUpon Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o LearnUpon.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 297395fbd0a7fd3fc8959eed5c0aeabd7b8e5751
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458698"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Tutorial: Integração do Diretório Ativo Azure com a LearnUpon

Neste tutorial, aprende-se a integrar o LearnUpon com o Azure Ative Directory (Azure AD).
A integração do LearnUpon com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a LearnUpon.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no LearnUpon (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a LearnUpon, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura ativada para um único sinal de LearnUpon

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.


* LearnUpon apoia **IDP** iniciado SSO

* LearnUpon suporta o fornecimento do utilizador **Just In Time**


## <a name="adding-learnupon-from-the-gallery"></a>Adicionar LearnUpon da galeria

Para configurar a integração do LearnUpon no AD AZure, precisa adicionar LearnUpon da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar LearnUpon da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva LearnUpon**, selecione **LearnUpon** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![AprenderApon na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com LearnUpon com base em um utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no LearnUpon.

Para configurar e testar o Azure AD com um único sinal de acesso com a LearnUpon, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure LearnUpon Single Sign-On](#configure-learnupon-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create LearnUpon test user](#create-learnupon-test-user)** - para ter uma contraparte de Britta Simon em LearnUpon que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD um único sinal de inscrição com a LearnUpon, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **LearnUpon,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações de súmis e URLs de aprendizagem e URLs](common/idp-reply.png)

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > O valor não é real. Atualizar o valor com o URL de resposta real. Contacte [a equipa de suporte do Cliente LearnUpon](https://www.learnupon.com/features/support/) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, localize a **impressão digital** - Isto será adicionado às definições DE SAML LearnUpon.

    ![O link de descarregamento de certificado](common/certificateraw.png)

6. Na secção **Configurar a Resposta,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-learnupon-single-sign-on"></a>Configurar learnUpon Single Sign-On

1. Abra outra instância do navegador e inscreva-se no LearnUpon com uma conta de administrador.

1. Clique no separador **definições.**

    ![A imagem mostra o separador de definições.](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Clique **em 'Sign On' único - SAML** e, em seguida, clique em **Definições Gerais** para configurar as definições DE SAML.
   
    ![A screenshot mostra single sign on - SAML selecionado com Definições Gerais selecionadas.](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. Na secção **Definições Gerais,** execute os seguintes passos:
   
    ![A screenshot mostra a secção Definições Gerais onde pode introduzir os valores descritos.](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Selecione **Ativado**.

    b. Selecione **versão** como **2.0**.

    c. Selecione **as condições de skip** como **Nº**.

    d. Na caixa de texto de nome de **param postais SAML,** digite o nome do parâmetro de registo de pedido para o URL de consumo SAML indicado acima que contém a Afirmação SAML a ser verificada e autenticada - por **exemplo, SAMLResponse**.

    e. Na caixa de texto **do formato do identificador de nome,** digite o valor que indica onde na sua Afirmação SAML reside o identificador de utilizadores (endereço de e-mail) - por exemplo `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` .
  
    f. Na caixa de texto **'Localização do Fornecedor de Identificação',** digite o valor que indica para onde os utilizadores são enviados se clicarem no ícone carregado a partir do ecrã de login do portal Azure.
  
    exemplo, Na caixa de texto **URL sign out,** cole o valor **URL logout,** que copiou a partir do portal Azure.

    h. Clique **em Gerir impressões digitais dos dedos** e, em seguida, faça o upload da impressão digital do seu certificado descarregado.

1. Clique nas **Definições do Utilizador** e, em seguida, execute os seguintes passos:

     ![A screenshot mostra a secção Definições do Utilizador onde pode introduzir os valores descritos.](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. Na caixa de texto **do formato do identificador de nome próprio,** escreva o valor que nos diz onde na sua Afirmação SAML reside o primeiro nome dos utilizadores - por exemplo: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .
  
    b. Na caixa de texto **do formato do identificador de nome último,** digite o valor que nos diz onde na sua Afirmação SAML reside o último nome dos utilizadores - por exemplo: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` .

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao LearnUpon.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **LearnUpon**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, **selecione LearnUpon**.

    ![O link LearnUpon na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-learnupon-test-user"></a>Criar utilizador de teste LearnUpon

Nesta secção, um utilizador chamado Britta Simon é criado em LearnUpon. A LearnUpon suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no LearnUpon, um novo é criado após a autenticação. Se precisar de criar um utilizador manualmente, tem de contactar a equipa de [suporte da LearnUpon.](https://www.learnupon.com/features/support/)

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo LearnUpon no Painel de Acesso, deverá ser automaticamente inscrito no LearnUpon para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)