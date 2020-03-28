---
title: 'Tutorial: Integração de Diretório Sonérrio Saem com a ArcGIS Enterprise [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a ArcGIS Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 24809e9d-a4aa-4504-95a9-e4fcf484f431
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/28/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e21b1c72f191f3644975afd511a900667a04ce9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157897"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Tutorial: Integração de Diretório Ativo Azure com a ArcGIS Enterprise

Neste tutorial, aprende-se a integrar a ArcGIS Enterprise com o Azure Ative Directory (Azure AD).
Integrar a ArcGIS Enterprise com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso à ArcGIS Enterprise.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na ArcGIS Enterprise (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a ArcGIS Enterprise, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de inscrição única da ArcGIS Enterprise

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.



* ArcGIS Enterprise apoia **SP e IDP** iniciado SSO
* ArcGIS Enterprise suporta o provisionamento de utilizadores **justo no tempo**


## <a name="adding-arcgis-enterprise-from-the-gallery"></a>Adicionando a ArcGIS Enterprise da galeria

Para configurar a integração da ArcGIS Enterprise em Azure AD, você precisa adicionar a ArcGIS Enterprise da galeria à sua lista de aplicações saaS geridas.

**Para adicionar a ArcGIS Enterprise a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **a ArcGIS Enterprise,** selecione **ArcGIS Enterprise** a partir do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![ArcGIS Enterprise na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único signo da Azure AD com [nome de aplicação] com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em [Nome de aplicação].

Para configurar e testar o único sinal de Azure AD com [nome de aplicação], é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On único da empresa ArcGIS](#configure-arcgis-enterprise-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador de teste Da ArcGIS Enterprise](#create-arcgis-enterprise-test-user)** - para ter uma contrapartida da Britta Simon na ArcGIS Enterprise que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com [nome da aplicação], execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações da **ArcGIS Enterprise,** selecione **single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos, se pretender configurar a aplicação no modo iniciado **IDP:**

    ![ArcGIS Enterprise Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`<EXTERNAL_DNS_NAME>.portal`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![ArcGIS Enterprise Domain e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Contacte a equipa de [suporte do Cliente da ArcGIS Enterprise](mailto:support@esri.com) para obter estes valores. Obterá o valor do Identificador na **secção set Identity Provider,** o que é explicado mais tarde neste tutorial.

5. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="configure-arcgis-enterprise-single-sign-on"></a>Configure ArcGIS Enterprise Single Sign-On

1. Numa janela de navegador web diferente, inicie sessão no site da empresa ArcGIS Enterprise como administrador.

2. Selecione **Organização >DEFINIÇÕES DE EDIÇÃO**.

    ![Configuração da Empresa ArcGIS](./media/arcgisenterprise-tutorial/configure1.png)

3. Selecione o separador **Segurança**.

    ![Configuração da Empresa ArcGIS](./media/arcgisenterprise-tutorial/configure2.png)

4. Desloque-se até aos **Logins da Empresa através** da secção SAML e selecione SET ENTERPRISE **LOGIN**.

    ![Configuração da Empresa ArcGIS](./media/arcgisenterprise-tutorial/configure3.png)

5. Na secção **set Identity Provider,** execute os seguintes passos:

    ![Configuração da Empresa ArcGIS](./media/arcgisenterprise-tutorial/configure4.png)

    a. Por favor, forneça um nome como Teste de **Diretório Ativo Azure** na caixa de texto **Name.**

    b. Na caixa de texto **URL,** colá o valor url de **metadados da Federação** de Aplicações que copiou do portal Azure.

    c. Clique em **Mostrar configurações avançadas** e copiar o valor id da **entidade** e colá-lo na caixa de texto **identificador** na secção **Domínio empresarial arcgis e URLs** no portal Azure.
    
    ![Configuração da Empresa ArcGIS](./media/arcgisenterprise-tutorial/configure5.png)

    d. Clique em **Atualizar fornecedor de identidade**.

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

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso à ArcGIS Enterprise.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **ArcGIS Enterprise**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, digite e selecione **ArcGIS Enterprise**.

    ![O link ArcGIS Enterprise na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-arcgis-enterprise-test-user"></a>Criar o utilizador de teste ArcGIS Enterprise

Nesta secção, um utilizador chamado Britta Simon é criado na ArcGIS Enterprise. A ArcGIS Enterprise suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na ArcGIS Enterprise, um novo é criado após a autenticação.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a equipa de suporte da [ArcGIS Enterprise](mailto:support@esri.com).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo ArcGIS Enterprise no Painel de Acesso, deve ser automaticamente inscrito na ArcGIS Enterprise para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

