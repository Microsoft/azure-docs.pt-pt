---
title: 'Tutorial: Integração do Diretório Ativo Azure com signo da Adobe [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Adobe Sign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 788c1292b844088f171479b40fc566ff5cfc8a57
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73154035"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Tutorial: Integração de Diretório Ativo Azure com Sinal adobe

Neste tutorial, aprende-se a integrar o Signo adobe com o Azure Ative Directory (Azure AD).
Integrar o Signo Adobe com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Sinal Adobe.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Adobe Sign (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Signo Adobe, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Adobe Sign única subscrição ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Adobe Sign apoia **SP** iniciado SSO

## <a name="adding-adobe-sign-from-the-gallery"></a>Adicionar sinal de Adobe da galeria

Para configurar a integração do Sign o Adobe em Azure AD, precisa adicionar o Signo Adobe da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar Adobe Sign a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **O Sinal Adobe,** selecione **Sinal Adobe** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Sinal de Adobe na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com o Adobe Sign com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Adobe Sign.

Para configurar e testar o único sinal de Azure AD com o Sinal adobe, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sinal Único de Sinal adobe](#configure-adobe-sign-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Adobe Sign test user](#create-adobe-sign-test-user)** - para ter uma contrapartida de Britta Simon no Adobe Sign que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com o Sinal Adobe, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Adobe Sign,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Adobe Sign Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<companyname>.echosign.com/`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<companyname>.echosign.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de [suporte do Cliente Adobe Sign](https://helpx.adobe.com/in/contact/support.html) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

4. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **set up Adobe Sign,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-adobe-sign-single-sign-on"></a>Configure Adobe Assinar um único sinal

1. Antes da configuração, contacte a equipa de suporte do [Cliente Adobe Sign](https://helpx.adobe.com/in/contact/support.html) para adicionar o seu domínio na lista de permitir o Sinal adobe. Aqui está como adicionar o domínio:

    a. A equipa de [suporte ao Cliente Adobe Sign](https://helpx.adobe.com/in/contact/support.html) envia-lhe um token gerado aleatoriamente. Para o seu domínio, o símbolo será como o seguinte: **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publique o token de verificação num registo de texto DNS e notifique a equipa de suporte ao [Cliente de Sinais da Adobe](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Isto pode levar alguns dias, ou mais. Note que os atrasos na propagação do DNS significam que um valor publicado no DNS pode não ser visível durante uma hora ou mais. O seu administrador de TI deve saber como publicar este símbolo num registo de texto dNS.
    
    c. Quando notifica a equipa de suporte do [Cliente Adobe Sign](https://helpx.adobe.com/in/contact/support.html) através do bilhete de apoio, após a publicação do token, eles validam o domínio e adicionam-no à sua conta.
    
    d. Geralmente, aqui está como publicar o símbolo num registo dNS:

    * Inscreva-se na sua conta de domínio
    * Encontre a página para atualizar o registo dNS. Esta página pode ser chamada de Gestão dNS, Gestão de Servidores de Nome ou Definições Avançadas.
    * Encontre os registos TXT para o seu domínio.
    * Adicione um disco TXT com o valor total do token fornecido pela Adobe.
    * Guarde as alterações.

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Adobe Sign como administrador.

1. No menu SAML, selecione **Definições** > **saml**de conta .
   
    ![Screenshot da página de definições saml de sinal de Adobe](./media/adobe-echosign-tutorial/ic789520.png "Conta")

1. Na secção **Definições SAML,** execute os seguintes passos:
  
   ![Screenshot das definições saml](./media/adobe-echosign-tutorial/ic789521.png "Definições SAML")
   
   ![Screenshot das definições saml](./media/adobe-echosign-tutorial/ic789522.png "Definições SAML")

   a. No **modo SAML,** selecione **SAML Obrigatório**.
   
   b. Selecione Permitir que os Administradores da **Conta Echosign entrem em sessão utilizando as suas credenciais de Echosign**.
   
   c. No âmbito **da Criação do Utilizador,** selecione **Adicionar automaticamente utilizadores autenticados através do SAML**.

   d. Identificador de anúncios De **Ad,** que copiou do portal Azure para a caixa de texto **ID Entity ID.**
    
   e. Colar **URL de Login**, que copiou do portal Azure para a caixa de texto **IDP Login** URL.
   
   f. Colar **URL de logout**, que copiou do portal Azure para a caixa de texto **IDP Logout URL.**

   g. Abra o ficheiro Certificado descarregado **(Base64)** no Bloco de Notas. Copie o conteúdo do mesmo na sua pasta e, em seguida, cole-o na caixa de texto **do Certificado IDP.**

   h. Selecione **Guardar Alterações**.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Signo Adobe.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Adobe Sign**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, digite e selecione **Sinal Adobe**.

    ![O link Adobe Sign na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-adobe-sign-test-user"></a>Criar o utilizador do teste Adobe Sign

Para permitir que os utilizadores de Anúncios Azure assinem o Sinal adobe, devem ser aprovisionados no Sinal adobe. Esta é uma tarefa manual.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador da Adobe Sign ou APIs fornecidas pela Adobe Sign para fornecer contas de utilizador da AD Azure. 

1. Inscreva-se no site da empresa **Adobe Sign** como administrador.

2. No menu em cima, selecione **Conta**. Em seguida, no painel esquerdo, selecione **Utilizadores & Grupos** > **Criar um novo utilizador**.
   
    ![Screenshot do site da empresa Adobe Sign, com Conta, Utilizadores &Grupos, e Criar um novo utilizador em destaque](./media/adobe-echosign-tutorial/ic789524.png "Conta")
   
3. Na secção **Criar Novo Utilizador,** execute os seguintes passos:
   
    ![Screenshot de criar nova secção de utilizador](./media/adobe-echosign-tutorial/ic789525.png "Criar Utilizador")
   
    a. Digite o **Endereço de E-mail,** **Primeiro Nome**e **Apelido** de uma conta Azure AD válida que pretende fornecer nas caixas de texto relacionadas.
   
    b. Selecione **Criar utilizador**.

>[!NOTE]
>O titular da conta Azure Ative Directory recebe um e-mail que inclui um link para confirmar a conta, antes de se tornar ativo. 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Adobe Sign no Painel de Acesso, deve ser automaticamente inscrito no Sinal Adobe para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

