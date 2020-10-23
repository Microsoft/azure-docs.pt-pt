---
title: 'Tutorial: Integração do Azure Ative Directory com o Infinite Campus Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Infinite Campus.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 08cee87d94b20e99bcc46d42cbac7eb4272561bb
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460243"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Tutorial: Integração do Diretório Ativo Azure com o Infinite Campus

Neste tutorial, aprende-se a integrar o Infinite Campus com o Azure Ative Directory (Azure AD).
Integrar o Campus Infinito com AZure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Campus Infinito.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Infinite Campus (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Infinite Campus, você precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura ativada única do Campus Infinito
* No mínimo, você precisa ser um administrador do Azure Ative Directory, e ter um Papel de Segurança do Produto do Campus de "Student Information System (SIS)" para completar a configuração.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Campus Infinito suporta **SSO** iniciado SP

## <a name="adding-infinite-campus-from-the-gallery"></a>Adicionar Campus Infinito da galeria

Para configurar a integração do Infinite Campus em Azure AD, você precisa adicionar O Campus Infinito da galeria à sua lista de aplicações geridas saaS.

**Para adicionar O Campus Infinito da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, clique no botão **de aplicação Novo** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Infinite Campus**, selecione **Infinite Campus** a partir do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Campus Infinito na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Infinite Campus baseado em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Infinite Campus.

Para configurar e testar o Azure AD com um único sign-on com o Infinite Campus, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configurar o Sign-On Único do Campus Infinito](#configure-infinite-campus-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Infinite Campus test user](#create-infinite-campus-test-user)** - ter uma contraparte de Britta Simon no Campus Infinito que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o Infinite Campus, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações do **Campus Infinito,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção de Configuração Básica SAML, execute os seguintes passos (note que o domínio variará com o Modelo de Hospedagem, mas o valor **DE DOMÍNIO TOTALMENTE QUALIFICADO** deve corresponder à sua instalação Infinite Campus):

    a. Na caixa de texto **url de entrada de sinais,** digite um URL utilizando o seguinte padrão: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. Na caixa de texto **identifier,** digite um URL utilizando o seguinte padrão: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Domínio do Campus Infinito e INFORMAÇÕES DE SÚMS únicas](common/sp-identifier-reply.png)

5. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Configurar Sign-On Única do Campus Infinito

1. Numa janela diferente do navegador, inscreva-se no Infinite Campus como Administrador de Segurança.

2. No lado esquerdo do menu, clique em **Administração do Sistema.**

    ![O Administrador](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. Navegue para a configuração **do**fornecedor de  >  **SAML Management**  >  **serviços SSO de**gestão de segurança do utilizador.

    ![O saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. Na página **de Configuração do Fornecedor de Serviço sSO,** execute os seguintes passos:

    ![O sso](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Selecione **Ativar O sinal único DO SAML .**

    b. Editar o **Nome do Atributo Opcional** para conter o **nome**

    c. Na secção **De Seleção de uma opção para recuperar a secção de dados do servidor do Fornecedor de Identidade (IDP),** selecione **URL de metadados,** cole o valor **do url de metadados da Federação de Aplicações,** que copiou a partir do portal Azure na caixa e, em seguida, clique em **Sync**.

    d. Depois de clicar em **Sync,** os valores são auto-povoados na página **de Configuração do Fornecedor de Serviço sSO.** Estes valores podem ser verificados de forma a corresponder aos valores observados no Passo 4 acima.

    e. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **do nome do utilizador** `brittasimon@yourcompanydomain.extension` . Por exemplo, BrittaSimon@contoso.com.

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

> [!NOTE]
> Se quiser que todos os seus utilizadores do Azure tenham um único acesso de entrada no Campus Infinito e confiem no sistema de permissões internas do Campus Infinito para controlar o acesso, pode definir a propriedade exigida para a atribuição do **utilizador** da aplicação para Não e saltar os seguintes passos.

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao Infinite Campus.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Infinite Campus**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Infinite Campus**.

    ![O link do Campus Infinito na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-infinite-campus-test-user"></a>Criar utilizador de teste do Campus Infinito

O Campus Infinito tem uma arquitetura centrada na demografia. Contacte a [equipa de apoio do Infinite Campus](mailto:sales@infinitecampus.com) para adicionar os utilizadores na plataforma Infinite Campus.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Campus Infinito no Painel de Acesso, deverá ser automaticamente inscrito no Campus Infinito para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)