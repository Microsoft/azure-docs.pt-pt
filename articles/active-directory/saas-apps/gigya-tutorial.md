---
title: 'Tutorial: Integração do Diretório Ativo Azure com gigya Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Gigya.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2c7d200b-9242-44a5-ac8a-ab3214a78e41
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/18/2019
ms.author: jeedes
ms.openlocfilehash: 824e9c459df75ea0307fe314fbf4118cca6e69d3
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841783"
---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Tutorial: Integração do Diretório Ativo Azure com gigya

Neste tutorial, aprende-se a integrar a Gigya com o Azure Ative Directory (Azure AD).
Integrar a Gigya com a Azure AD proporciona-lhe os seguintes benefícios:

* Podes controlar em Azure AD quem tem acesso à Gigya.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Gigya (Single Sign-On) com as suas contas Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Gigya, precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Gigya única subscrição ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Gigya apoia **SP** iniciado SSO

## <a name="adding-gigya-from-the-gallery"></a>Adicionando Gigya da galeria

Para configurar a integração da Gigya em Azure AD, você precisa adicionar Gigya da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Gigya da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Gigya**, **selecione Gigya** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Gigya na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configura e testa o single sign-on azure com gigya com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Gigya.

Para configurar e testar o único sign-on azure ad com gigya, você precisa completar os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configure Gigya Single Sign-On](#configure-gigya-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Crie um utilizador de teste Gigya](#create-gigya-test-user)** - para ter uma contrapartida de Britta Simon em Gigya que esteja ligada à representação do utilizador da AD Azure.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o single sign-on azure com gigya, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **gigya,** selecione **Single sign-on**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Gigya Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão: `http://<companyname>.gigya.com`

    b. Na caixa de texto **identificador (Id** da entidade), escreva um URL utilizando o seguinte padrão: `https://fidm.gigya.com/saml/v2.0/<companyname>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contacte a equipa de apoio ao [cliente gigya](https://developers.gigya.com/display/GD/Opening+A+Support+Incident) para obter estes valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Na secção **set up Gigya,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-gigya-single-sign-on"></a>Configure Gigya Single Sign-On

1. Numa janela diferente do navegador web, inicie sessão no site da sua empresa Gigya como administrador.

2. Aceda às **Definições \> SAML Login**e, em seguida, clique no botão **Adicionar.**
   
    ![SAML Login](./media/gigya-tutorial/ic789532.png "SAML Login")

3. Na secção **SAML Login,** execute os seguintes passos:
   
    ![Configuração SAML](./media/gigya-tutorial/ic789533.png "Configuração do SAML")
   
    a. Na caixa de texto **Name,** digite um nome para a sua configuração.
   
    b. Na caixa de texto **Emitente,** cola o valor do **Identificador De Ad Azure** que copiou do Portal Azure. 
   
    c. Na caixa de texto URL do serviço de **início de sessão simples,** colá-cola o valor do URL de **Login** que copiou do Portal Azure.
   
    d. No **nome ID Format** textbox, colhe o valor do **formato identificador** de nome que copiou do Portal Azure.
   
    e. Abra o seu certificado codificado base-64 no bloco de notas descarregado do portal Azure, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **x.509 Certificate.**
   
    f. Clique em **salvar configurações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite **brendafernandes\@yourcompanydomain. Extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à Gigya.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Gigya**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Gigya**.

    ![O link Gigya na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-gigya-test-user"></a>Criar o utilizador de teste Gigya

Para permitir que os utilizadores da AD Azure entrem na Gigya, devem ser aprovisionados na Gigya. No caso da Gigya, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1. Inicie sessão no site da sua empresa **Gigya** como administrador.

2. Vá ao **Administrador \> Gerir utilizadores,** e, em seguida, clique **em Convidar utilizadores**.
   
    ![Gerir utilizadores](./media/gigya-tutorial/ic789535.png "Gerir utilizadores")

3. No diálogo Utilizadores Convidados, execute os seguintes passos:
   
    ![Convidar usuários](./media/gigya-tutorial/ic789536.png "Convidar usuários")
   
    a. Na caixa de texto **por e-mail,** digite o pseudónimo de e-mail de uma conta de Diretório Ativo Azure válida que pretende fornecer.
    
    b. Clique em **Convidar utilizador**.
      
    > [!NOTE]
    > O titular da conta Azure Ative Directory receberá um e-mail que inclui um link para confirmar a conta antes de se tornar ativa.
    > 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo Gigya no Painel de Acesso, deve ser automaticamente inscrito no Gigya para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

