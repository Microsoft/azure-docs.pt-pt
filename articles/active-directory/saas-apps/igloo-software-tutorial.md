---
title: 'Tutorial: Integração do Azure Ative Directory com | de Software Igloo Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Igloo Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: 033561ac3c4a510927691dc8db4f61196f54ec2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460398"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Tutorial: Integração do Azure Ative Directory com o Igloo Software

Neste tutorial, aprende-se a integrar o Software Igloo com o Azure Ative Directory (Azure AD).
A integração do Software Igloo com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Software Igloo.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Igloo Software (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Software Igloo, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Igloo Software assinatura ativada única

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Igloo Software suporta **SSO** iniciado sp
* Igloo Software suporta **provisão de** utilizadores just in time

## <a name="adding-igloo-software-from-the-gallery"></a>Adicionar Software Igloo da galeria

Para configurar a integração do Software Igloo no AD Azure, é necessário adicionar software Igloo da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar software Igloo da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Igloo Software**, selecione **Igloo Software** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Igloo Software na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com o Igloo Software baseado num utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Software Igloo.

Para configurar e testar o Azure AD com um único sinal de acesso com o Igloo Software, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o software Igloo single sign-on](#configure-igloo-software-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Igloo Software test user](#create-igloo-software-test-user)** - para ter uma contraparte de Britta Simon em Igloo Software que está ligada à representação AD AZure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o Igloo Software, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Igloo Software,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Igloo Software Domain e URLs informações únicas de acesso](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<company name>.igloocommmunities.com`

    b. Na caixa **identifier,** digite um URL utilizando o seguinte padrão: `https://<company name>.igloocommmunities.com/saml.digest`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identifier e responder URL. Contacte [a equipa de suporte do Cliente do Igloo Software](https://www.igloosoftware.com/services/support) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **De Configuração do Software Igloo,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-igloo-software-single-sign-on"></a>Configurar o software Igloo single Sign-On

1. Numa janela diferente do navegador web, inicie sessão no site da empresa Igloo Software como administrador.

2. Vá ao **Painel de Controlo.**

     ![Painel de Controlo](./media/igloo-software-tutorial/ic799949.png "Painel de Controlo")

3. No **separador 'C sócio',** clique **em Iniciars Definições**.

    ![Iniciar sposição em Definições](./media/igloo-software-tutorial/ic783968.png "Iniciar sposição em Definições")

4. Na secção configuração SAML, clique em **Configurar Configuração SAML**.

    ![Configuração SAML](./media/igloo-software-tutorial/ic783969.png "Configuração SAML")

5. Na secção **Configuração Geral,** execute os seguintes passos:

    ![Configuração Geral](./media/igloo-software-tutorial/ic783970.png "Configuração Geral")

    a. Na caixa de texto **'Nome de** Ligação', digite um nome personalizado para a sua configuração.

    b. Na caixa de texto **url de login IdP,** cole o valor do URL de **login** que copiou do portal Azure.

    c. Na caixa de texto **IdP Logout URL,** cole o valor do **URL logout** que copiou do portal Azure.

    d. Selecione **Logout Response and Request HTTP Type** como **POST**.

    e. Abra o certificado codificado **base-64** no bloco de notas descarregado do portal Azure, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **do Certificado Público.**

6. Na **Configuração resposta e autenticação,** execute os seguintes passos:

    ![Configuração de resposta e autenticação](./media/igloo-software-tutorial/IC783971.png "Configuração de resposta e autenticação")
  
    a. Como **Fornecedor de Identidade**, selecione Microsoft **ADFS**.

    b. Como **Tipo identificador**, selecione **endereço de e-mail**. 

    c. Na caixa de texto **do Email Attribute,** **escreva e-mailaddress**.

    d. Na caixa de texto **namee Attribute,** **escreva o nome dado**.

    e. Na caixa de texto do **último nome, escreva** **o apelido**.

7. Execute os seguintes passos para completar a configuração:

    ![Criação de utilizador no Iniciar sção](./media/igloo-software-tutorial/IC783972.png "Criação de utilizador no Iniciar sção") 

    a. Como **criação do Utilizador no Iniciar sessão,** selecione **Criar um novo utilizador no seu site quando iniciarem sessão**.

    b. Como **Iniciar sposição em Definições**, selecione **Use O botão SAML no ecrã "Iniciar sposição".**

    c. Clique em **Guardar**.

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

Nesta secção, você permite que Britta Simon utilize o Azure single sign-on, concedendo acesso ao Software Igloo.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Igloo Software**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, **selecione Igloo Software**.

    ![A ligação igloo software na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-igloo-software-test-user"></a>Criar utilizador de teste de software Igloo

Não existe nenhum item de ação para configurar o fornecimento do utilizador ao Software Igloo.  

Quando um utilizador designado tenta iniciar sessão no Igloo Software utilizando o painel de acesso, o Igloo Software verifica se o utilizador existe.  Se ainda não houver conta de utilizador disponível, esta é automaticamente criada pela Igloo Software.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Software Igloo no Painel de Acesso, deverá ser automaticamente inscrito no Software Igloo para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)