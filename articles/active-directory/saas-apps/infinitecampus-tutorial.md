---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Campus Infinito [ Campus Infinito] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Infinite Campus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d54769c1f3265e2cee619520044313fca46855a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100358"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Tutorial: Integração de Diretório Ativo Azure com Campus Infinito

Neste tutorial, aprende-se a integrar o Infinite Campus com o Azure Ative Directory (Azure AD).
Integrar o Infinite Campus com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Campus Infinito.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Infinite Campus (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Infinite Campus, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura de inscrição única do Campus Infinito
* No mínimo, é necessário ser administrador de Diretório Ativo Azure e ter uma função de Segurança de Produto do Campus do "Sistema de Informação de Estudantes (SIS)" para completar a configuração.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Infinite Campus apoia **SP** iniciado SSO

## <a name="adding-infinite-campus-from-the-gallery"></a>Adicionar o Campus Infinito da galeria

Para configurar a integração do Infinite Campus em Azure AD, você precisa adicionar Infinite Campus da galeria à sua lista de aplicações saaS geridas.

**Para adicionar o Campus Infinito da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, clique no novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Infinite Campus,** selecione **Infinite Campus** a partir do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Campus Infinito na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com o Infinite Campus com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Infinite Campus.

Para configurar e testar o único sign-on azure ad com o Infinite Campus, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Single Sign-On](#configure-infinite-campus-single-sign-on)** do Campus Infinito - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Infinite Campus test user](#create-infinite-campus-test-user)** - para ter uma contrapartida de Britta Simon no Infinite Campus que está ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o single sign-on azure com o Infinite Campus, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações do **Campus Infinito,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção de Configuração SAML Básica, execute os seguintes passos (note que o domínio variará com o Modelo de Hospedagem, mas o valor **DOMÍNIO TOTALMENTE QUALIFICADO** deve corresponder à sua instalação do Campus Infinito):

    a. Na caixa de texto **"Sign-on URL",** escreva um URL utilizando o seguinte padrão:`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Infinita Campus Domain e URLs informações únicas de inscrição](common/sp-identifier-reply.png)

5. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Configure Infinite Campus Single Sign-On

1. Numa janela de navegador web diferente, inscreva-se no Infinite Campus como administrador de segurança.

2. No lado esquerdo do menu, clique em **Administração**do Sistema .

    ![O Administrador](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. Navegue **para** > a configuração do prestador de > **serviços SAML****Management SAML**management SSO .

    ![O saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. Na página de Configuração do Prestador de **Serviços SSO,** execute os seguintes passos:

    ![O sso](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. **Selecione ativar o sinal único SAML .**

    b. Editar o **nome do atributo opcional** para conter o **nome**

    c. Na secção de dados do Servidor Do Fornecedor de **Identidade (IDP),** selecione URL de **Metadados,** colá o valor de Url de Metadados da Federação de **Aplicações,** que copiou do portal Azure na caixa e, em seguida, clique em **Sync**.

    d. Depois de **clicar, os** valores ficam povoados automaticamente na página de Configuração do Fornecedor de **Serviços SSO.** Estes valores podem ser verificados para corresponder aos valores observados no Passo 4 acima.

    e. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo `brittasimon@yourcompanydomain.extension`de campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com.

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

> [!NOTE]
> Se quiser que todos os seus utilizadores do Azure tenham acesso único ao Infinite Campus e confie no sistema de permissões internas do Infinite Campus para controlar o acesso, pode definir a propriedade necessária à **atribuição** de utilizador da aplicação para Não e saltar os seguintes passos.

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Infinite Campus.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Infinite Campus**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Infinite Campus**.

    ![O link do Campus Infinito na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-infinite-campus-test-user"></a>Criar utilizador de teste do Campus Infinito

O Infinite Campus tem uma arquitetura centrada na demografia. Contacte a equipa de suporte do [Infinite Campus](mailto:sales@infinitecampus.com) para adicionar os utilizadores à plataforma Infinite Campus.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Infinite Campus no Painel de Acesso, deve ser automaticamente inscrito no Campus Infinito para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
