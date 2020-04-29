---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Marketo Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Marketo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 0488fd1e9bc10d61d6660745acfc8c39becf3a89
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73159470"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Tutorial: Integração de Diretório Sonársimo Azure com a Marketo

Neste tutorial, aprende-se a integrar o Marketo com o Azure Ative Directory (Azure AD).
Integrar o Marketo com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Marketo.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Marketo (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Marketo, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por assinatura sinuosa de Marketo

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Marketo suporta **IDP** iniciado SSO

## <a name="adding-marketo-from-the-gallery"></a>Adicionando Marketo da galeria

Para configurar a integração do Marketo em Azure AD, precisa de adicionar Marketo da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar Marketo da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Marketo,** selecione **Marketo** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Marketo na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure com o Marketo com base num utilizador de teste chamado **Britta Simon**.
Para que o início único funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador coligado em Marketo.

Para configurar e testar o único signo da Azure AD com o Marketo, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure marketo single sign-on](#configure-marketo-single-sign-on)** - para configurar as definições de início de sessão única no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Criar o utilizador de **[teste Marketo](#create-marketo-test-user)** - para ter uma contrapartida da Britta Simon em Marketo que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com o Marketo, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **do Marketo,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na configuração de um único sign-on com a página **SAML,** execute os seguintes passos:

    ![Marketo Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://saml.marketo.com/sp`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte a equipa de apoio ao [Cliente marketo](https://investors.marketo.com/contactus.cfm) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **'set up Marketo',** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-marketo-single-sign-on"></a>Configure Marketo Single Sign-On

1. Para obter Munchkin Id da sua aplicação, faça login no Marketo usando credenciais de administração e execute as seguintes ações:
   
    a. Inicie sessão na app Marketo utilizando credenciais de administração.
   
    b. Clique no botão **Admin** no painel de navegação superior.
   
    ![Configurar um único sinal](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue para o menu integração e clique no **link Munchkin**.
   
    ![Configurar um único sinal](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Copie o Id Munchkin mostrado no ecrã e complete o URL de resposta no assistente de configuração do Anúncio Azure.
   
    ![Configurar um único sinal](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Para configurar o SSO na aplicação, siga os passos abaixo:
   
    a. Inicie sessão na app Marketo utilizando credenciais de administração.
   
    b. Clique no botão **Admin** no painel de navegação superior.
   
    ![Configurar um único sinal](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue para o menu integração e clique **em Single Sign On**.
   
    ![Configurar um único sinal](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Para ativar as Definições SAML, clique no botão **Editar.**
   
    ![Configurar um único sinal](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Habilitado** Definições de sinal único.
   
    f. Colar o **identificador AD Azure,** na caixa de texto **ID emitente.**
   
    g. Na caixa de texto ID da `http://saml.marketo.com/sp` **Entidade,** introduza o URL como .
   
    h. Selecione a localização do ID do utilizador como **elemento identificador**de nome .
   
    ![Configurar um único sinal](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Se o seu Identificador utilizador não tiver valor UPN, então altere o valor no separador Atributo.
   
    i. Faça upload do certificado, que descarregou do assistente de configuração da AD Azure. **Guarde** as definições.
   
    j. Editar as definições de Redirecionamento de Páginas.
   
    k. Colar o URL de **Login** na caixa de texto **URL de Login.**
   
    l. Colá-lo na caixa de texto **logout** **URL.**
   
    m. No **URL**error, copie o URL da **sua instância de Marketo** e clique em **Guardar** o botão para guardar as definições.
   
    ![Configurar um único sinal](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Para ativar o SSO para os utilizadores, complete as seguintes ações:
   
    a. Inicie sessão na app Marketo utilizando credenciais de administração.
   
    b. Clique no botão **Admin** no painel de navegação superior.
   
    ![Configurar um único sinal](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue para o menu **'Segurança'** e clique **em Definições de Login**.
   
    ![Configurar um único sinal](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Verifique a opção **Requires SSO** e **guarde** as definições.
   
    ![Configurar um único sinal](./media/marketo-tutorial/tutorial_marketo_14.png)

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Marketo.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Marketo**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Marketo**.

    ![O link Marketo na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-marketo-test-user"></a>Criar utilizador de teste Marketo

Nesta secção, cria-se uma utilizadora chamada Britta Simon em Marketo. siga estes passos para criar um utilizador na plataforma Marketo.

1. Inicie sessão na app Marketo utilizando credenciais de administração.

2. Clique no botão **Admin** no painel de navegação superior.
   
    ![Configurar um único sinal](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Navegue para o menu **De segurança** e clique em Utilizadores **& Funções**
   
    ![Configurar um único sinal](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Clique no link **Convidar novo utilizador** no separador Utilizadores
   
    ![Configurar um único sinal](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. No assistente de novo utilizador convidar preencha as seguintes informações
   
    a. Insira o endereço de **e-mail** do utilizador na caixa de texto
   
    ![Configurar um único sinal](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Introduza o **Primeiro Nome** na caixa de texto
   
    c. Introduza o **Último Nome** na caixa de texto
   
    d. Clique **em Seguinte**

6. No separador **Permissões,** selecione os **userRoles** e clique em **Seguinte**
   
    ![Configurar um único sinal](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Clique no botão **Enviar** para enviar o convite do utilizador
   
    ![Configurar um único sinal](./media/marketo-tutorial/tutorial_marketo_18.png)

8. O utilizador recebe a notificação de e-mail e tem de clicar no link e alterar a palavra-passe para ativar a conta. 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Marketo no Painel de Acesso, deverá ser automaticamente inscrito no Marketo para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

