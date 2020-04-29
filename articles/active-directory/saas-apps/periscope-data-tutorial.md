---
title: 'Tutorial: Integração do Diretório Ativo Azure com Dados do Periscope [ Dados do Periscópio] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Periscope Data.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f378edb-9ac9-494d-a84a-03357b923ee1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: ffa0176c4dde6334e70b9a56b066a677d1610b48
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73160207"
---
# <a name="tutorial-azure-active-directory-integration-with-periscope-data"></a>Tutorial: Integração do Diretório Ativo Azure com Dados do Periscope

Neste tutorial, aprende-se a integrar dados do Periscope com o Azure Ative Directory (Azure AD).
Integrar dados do Periscópio com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso aos Dados do Periscópio.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos nos Dados do Periscope (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com os Dados do Periscope, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Periscope Data única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Periscope Data suporta **SP** iniciado SSO

## <a name="adding-periscope-data-from-the-gallery"></a>Adicionar dados do Periscópio da galeria

Para configurar a integração dos Dados do Periscópio em Anúncio salé-do-mar, é necessário adicionar dados do Periscope da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar dados do Periscope da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **dados do Periscope,** selecione **Dados do Periscópio** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Dados do Periscópio na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com dados do Periscope com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Dados do Periscope.

Para configurar e testar o único sinal de Azure AD com dados do Periscope, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Single Sign-On](#configure-periscope-data-single-sign-on)** dos dados do Periscope - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-periscope-data-test-user)** de teste periscope Data - para ter uma contrapartida de Britta Simon em Dados periscópios que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da AD Azure com os Dados do Periscópio, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **periscope Data,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Periscope Data Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite qualquer um dos URLs:
    
    | |
    |--|
    | `https://app.periscopedata.com/` |
    | `https://app.periscopedata.com/app/<SITENAME>` |

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://app.periscopedata.com/<SITENAME>/sso`

    > [!NOTE]
    > O valor do Signon on URL não é real. Atualize os valores com o sinal real no URL. Contacte a equipa de suporte do Cliente periscope [data](mailto:support@periscopedata.com) para obter este valor e o valor do Identificador obterá da secção De Acesso Único de Dados **do Periscópio,** o que é explicado mais tarde no tutorial. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="configure-periscope-data-single-sign-on"></a>Configure dados do periscópio único sign-on

1. Numa janela diferente do navegador web, inscreva-se no Periscope Data como Administrador.

2. Abra o menu de engrenagens na parte inferior esquerda e abra o menu de**Segurança** **de Faturação** > e execute os seguintes passos. Apenas os administradores têm acesso a estas definições.

    ![Informação de configuração de dados do periscópio](./media/periscope-data-tutorial/configure01.png)

    a. Copie o URL de **Metadados da Federação** de Aplicações a partir do passo #5 Certificado de **Assinatura SAML** e abra-o num browser. Isto abrirá um documento XML.

    b. Na caixa de texto **Single Sign-On,** selecione **Azure Ative Directory**.

    c. Encontre a etiqueta **SingleSignOnService** e colá o valor de **localização** na caixa de texto **URL SSO.**

    d. Encontre a etiqueta **SingleLogoutService** e colhe o valor de **localização** na caixa de texto **URL SLO.**

    e. Copie o valor **do Identificador** para a sua instância e cole-o na caixa de texto **identificador (Id** da entidade) da secção **basic SAML Configuração** no portal Azure.

    f. Encontre a primeira etiqueta do ficheiro XML, copie o valor da **entidade ID** e cole-o na caixa de texto **emitente.**

    g. Encontre a etiqueta **IDPSSODescriptor** com o protocolo SAML. Dentro desta secção, encontre a etiqueta **KeyDescriptor** com **use=assinatura**. copiar o valor de **X509Certificado** e colar **na** caixa de texto certificado.

    h. Sites com vários espaços podem escolher o espaço predefinido a partir da queda **do Espaço Padrão.** Este será o espaço a que os novos utilizadores são adicionados quando iniciarem sessão nos Dados do Periscope pela primeira vez e forem aprovisionados através do Ative Directory Single Sign On.

    i. Por fim, clique em **Guardar** e **confirmar** a alteração das definições sSO digitando **Logout**.

    ![Informação de configuração de dados do periscópio](./media/periscope-data-tutorial/configure02.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **tipo** de campo de nome utilizador **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso aos Dados do Periscópio.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Dados do Periscópio**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Dados do Periscópio**.

    ![O link de dados do Periscópio na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-periscope-data-test-user"></a>Criar utilizador de teste de dados do Periscope

Para permitir que os utilizadores de Anúncios Azure entrem nos Dados do Periscope, devem ser aprovisionados em Dados do Periscope. Nos Dados do Periscópio, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão nos Dados do Periscópio como Administrador.

2. Clique no ícone **Definições** na parte inferior esquerda do menu e navegue para **permissões**.

    ![Informação de configuração de dados do periscópio](./media/periscope-data-tutorial/configure03.png)

3. Clique no **UTILIZADOR ADD** e execute os seguintes passos:

      ![Informação de configuração de dados do periscópio](./media/periscope-data-tutorial/configure04.png)

    a. Na caixa de texto **First Name,** introduza o primeiro nome de utilizador como **Britta**.

    b. Na caixa de texto **De Apelido,** introduza o último nome de utilizador como **Simon**.

    c. Na caixa de texto **por e-mail,** introduza o e-mail de utilizador como **o\@brittasimon contoso.com**.

    d. Clique em **ADICIONAR**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Periscope Data no Painel de Acesso, deve ser automaticamente inscrito nos Dados do Periscópio para os quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

