---
title: 'Tutorial: Integração do Diretório Ativo Azure com | de FreshGrade Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o FreshGrade.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 7fa8c35185bb7e20454e8f803c5d3ee476655282
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92451398"
---
# <a name="tutorial-azure-active-directory-integration-with-freshgrade"></a>Tutorial: Integração do Diretório Ativo Azure com FreshGrade

Neste tutorial, aprende-se a integrar o FreshGrade com o Azure Ative Directory (Azure AD).
A integração do FreshGrade com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a FreshGrade.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no FreshGrade (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o FreshGrade, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por um único sign-on freshGrade

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* FreshGrade suporta **SSO** iniciado sp

## <a name="adding-freshgrade-from-the-gallery"></a>Adicionando FreshGrade da galeria

Para configurar a integração do FreshGrade no AD Azure, precisa de adicionar FreshGrade da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar FreshGrade da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite FreshGrade**, **selecione FreshGrade** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![FreshGrade na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com freshGrade com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em FreshGrade.

Para configurar e testar o Azure AD com freshGrade, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único FreshGrade](#configure-freshgrade-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create FreshGrade test user](#create-freshgrade-test-user)** - para ter uma contraparte de Britta Simon em FreshGrade que está ligada à representação AD AZure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com FreshGrade, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **FreshGrade,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na **configuração single Sign-On com página SAML,** execute os seguintes passos:

    ![Informações de assinatura única de FreshGrade Domain e URLs](common/sp-identifier.png)

     a. Na caixa de texto **url de entrada de sinais,** digite um URL utilizando os seguintes padrões:

    ```http       
    https://<subdomain>.freshgrade.com/login
    https://<subdomain>.onboarding.freshgrade.com/login
    ```

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando os seguintes padrões:
    
    ```http
    https://login.onboarding.freshgrade.com:443/saml/metadata/alias/<instancename>
    https://login.freshgrade.com:443/saml/metadata/alias/<instancename>
    ```

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e o identificador de Sign-On reais. Contacte [a equipa de suporte ao Cliente FreshGrade](mailto:support@freshgrade.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

### <a name="configure-freshgrade-single-sign-on"></a>Configurar Sign-On single FreshGrade

Para configurar um único sign-on no lado **FreshGrade,** você precisa enviar o **url de metadados da Federação de Aplicações** para [a equipe de suporte FreshGrade](mailto:support@freshgrade.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador** **brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon utilize a Azure single sign-on, concedendo acesso ao FreshGrade.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **FreshGrade**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, **selecione FreshGrade**.

    ![O link FreshGrade na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-freshgrade-test-user"></a>Criar utilizador de teste FreshGrade

Nesta secção, cria-se um utilizador chamado Britta Simon in FreshGrade. Trabalhe com [a equipa de suporte FreshGrade](mailto:support@freshgrade.com) para adicionar os utilizadores na plataforma FreshGrade. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo FreshGrade no Painel de Acesso, deverá ser automaticamente inscrito no FreshGrade para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)