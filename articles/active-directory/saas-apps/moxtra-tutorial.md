---
title: 'Tutorial: Integração do Azure Active Directory com o Moxtra | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Moxtra.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 7f64597d8da183a24bcf87543a448442052e5f77
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944255"
---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>Tutorial: Integração do Azure Active Directory com o Moxtra

Neste tutorial, você aprenderá a integrar o Moxtra com o Azure Active Directory (Azure AD).
A integração do Moxtra ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Moxtra.
* Você pode permitir que seus usuários sejam conectados automaticamente ao Moxtra (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Moxtra, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Moxtra

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Moxtra dá suporte ao SSO iniciado por **SP**

## <a name="adding-moxtra-from-the-gallery"></a>Adicionando o Moxtra da Galeria

Para configurar a integração do Moxtra ao Azure AD, você precisará adicionar o Moxtra da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Moxtra da galeria, execute as seguintes etapas:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Moxtra**, selecione **Moxtra** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Moxtra na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o Moxtra, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Moxtra.

Para configurar e testar o logon único do Azure AD com o Moxtra, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o logon único do Moxtra](#configure-moxtra-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar usuário de teste do Moxtra](#create-moxtra-test-user)** – para ter um equivalente de Brenda Simon no Moxtra que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Moxtra, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Moxtra** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-** enalimentado para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Moxtra](common/sp-signonurl.png)

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://www.moxtra.com/service/#login`

5. O aplicativo Moxtra espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo **atributos de usuário** .

    ![image](common/edit-attribute.png)

6. Além de acima, o aplicativo Moxtra espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo: 

    | Nome | Atributo de origem|
    | ------------------- | -------------------- |    
    | FirstName | user.givenname |
    | LastName | User. sobrenome |
    | idpid    | < O identificador do Azure AD >

    > [!Note]
    > O valor do atributo **idpid** não é real. Você pode obter o valor real na seção **Configurar Moxtra** da etapa 8. 

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

7. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download de certificado](common/certificatebase64.png)

8. Na seção **Configurar Moxtra** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-moxtra-single-sign-on"></a>Configurar o logon único do Moxtra

1. Em outra janela do navegador, faça logon em seu site de empresa do Moxtra como administrador.

2. Na barra de ferramentas à esquerda, clique em **console do administrador > logon único do SAML**e clique em **novo**.
   
    ![Configurar o início de sessão único](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. Na página **SAML** , execute as seguintes etapas:
   
    ![Configurar o início de sessão único](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. Na caixa de texto **nome** , digite um nome para a sua configuração (por exemplo: *SAML*). 
  
    b. Na caixa de texto **ID da entidade do IDP** , Cole o valor do identificador do **Azure ad** que você copiou do portal do Azure. 
 
    c. Na caixa de texto **URL de logon** , Cole o valor da **URL de logon** que você copiou do portal do Azure. 
 
    d. Na caixa de texto **AuthnContextClassRef** , digite **urn: Oasis: names: TC: SAML: 2.0: AC: classes: Password**. 
 
    e. Na caixa de texto **formato** de NameID, digite **urn: Oasis: names: TC: SAML: 1.1: NameID-Format: EmailAddress**. 
 
    f. Abra o certificado que você baixou de portal do Azure no bloco de notas, copie o conteúdo e cole-o na caixa de texto **certificado** .    
 
    g. Na caixa de texto domínio de email do SAML, digite seu domínio de email do SAML.    
  
    >[!NOTE]
    >Para ver as etapas para verificar o domínio, clique no "**i**" abaixo.

    h. Clique em **Atualizar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , **digite\@brendafernandes yourcompanydomain. Extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Moxtra.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Moxtra**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Moxtra**.

    ![O link do Moxtra na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-moxtra-test-user"></a>Criar usuário de teste do Moxtra

O objetivo desta seção é criar um usuário chamado Brenda Simon no Moxtra.

**Para criar um usuário chamado Brenda Simon no Moxtra, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do Moxtra como administrador.

1. Na barra de ferramentas à esquerda, clique em **console do administrador > gerenciamento de usuário**e, em seguida, **adicione usuário**.
   
    ![Configurar o início de sessão único](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. Na caixa de diálogo **Adicionar usuário** , execute as seguintes etapas:
  
    a. Na caixa de texto **nome** , digite **Brenda**.
  
    b. Na caixa de texto **sobrenome** , digite **Simon**.
  
    c. Na caixa de texto **email** , digite o endereço de email do Brenda, como em portal do Azure.
  
    d. Na caixa de texto **divisão** , digite **dev**.
  
    e. Na caixa de texto **Departamento** , digite **-** o.
  
    f. Selecione **administrador**.
  
    g. Clique em **Adicionar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Moxtra no painel de acesso, você deverá ser conectado automaticamente ao Moxtra para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

