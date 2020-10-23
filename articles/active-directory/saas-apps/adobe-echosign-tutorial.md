---
title: 'Tutorial: Integração do Azure Ative Directory com a Adobe Signa Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o Adobe Sign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: a7e71f1c146b0e99ffa20cc74546e35e3ff8a5a6
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309095"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Tutorial: Integração do Diretório Ativo Azure com Sinal de Adobe

Neste tutorial, aprende a integrar o Adobe Sign com o Azure Ative Directory (Azure AD).
Integrar o Sinal da Adobe com Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Adobe Sign.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Adobe Sign (Sign-on Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o Adobe Sign, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Adobe Assinar uma única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Adobe Sign suporta **SSO** iniciado SP

## <a name="adding-adobe-sign-from-the-gallery"></a>Adicionar Sinal de Adobe da galeria

Para configurar a integração do Adobe Sign em Azure AD, é necessário adicionar o Adobe Sign da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Adobe Sign da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Adobe Sign**, selecione **Adobe Sign** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![Sinal de Adobe na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o Adobe Sign baseado num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Adobe Sign.

Para configurar e testar o Azure AD com o Adobe Sign, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sinal único do Adobe](#configure-adobe-sign-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Adobe Sign test user](#create-adobe-sign-test-user)** - para ter uma contraparte de Britta Simon no Adobe Sign que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o Adobe Sign, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Adobe Sign,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Adobe Sign Domain e URLs informações únicas de súmis](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.echosign.com/`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Adobe Sign Client](https://helpx.adobe.com/in/contact/support.html) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **De Configuração do Sinal de Adobe,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-adobe-sign-single-sign-on"></a>Configurar o sinal de adobe único Sign-On

1. Antes da configuração, contacte a equipa de suporte do [Adobe Sign Client](https://helpx.adobe.com/in/contact/support.html) para adicionar o seu domínio na lista de autorizações do Adobe Sign. Eis como adicionar o domínio:

    a. A [equipa de suporte do Adobe Sign Client](https://helpx.adobe.com/in/contact/support.html) envia-lhe um token gerado aleatoriamente. Para o seu domínio, o token será como o seguinte: **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publique o token de verificação num registo de texto DNS e notifique a equipa de suporte do [Adobe Sign Client](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Isto pode levar alguns dias, ou mais. Note que os atrasos de propagação do DNS significam que um valor publicado no DNS pode não ser visível por uma hora ou mais. O seu administrador de TI deve saber como publicar este token num registo de texto DNS.
    
    c. Quando notifica a equipa de suporte do [Adobe Sign Client](https://helpx.adobe.com/in/contact/support.html) através do bilhete de suporte, após a publicação do token, eles validam o domínio e adicionam-no à sua conta.
    
    d. Geralmente, eis como publicar o símbolo num registo de DNS:

    * Inscreva-se na sua conta de domínio
    * Encontre a página para atualizar o registo dns. Esta página pode ser chamada de Gestão de DNS, Gestão de Servidores de Nomes ou Definições Avançadas.
    * Encontre os registos TXT para o seu domínio.
    * Adicione um registo TXT com o valor total do token fornecido pela Adobe.
    * Guarde as alterações.

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Adobe Sign como administrador.

1. No menu SAML, selecione **Definições**DE  >  **SAML de Definições**de Contas .
   
    ![Screenshot da página de Definições SAML do Sinal de Adobe](./media/adobe-echosign-tutorial/ic789520.png "Conta")

1. Na secção **Definições SAML,** execute os seguintes passos:
  
   ![Screenshot que destaca as definições DE SAML, incluindo a SAML Mandatory.](./media/adobe-echosign-tutorial/ic789521.png "Definições SAML")
   
   ![Screenshot das Definições SAML](./media/adobe-echosign-tutorial/ic789522.png "Definições SAML")

   a. No **modo SAML**, selecione **SAML Mandatory**.
   
   b. Selecione **Permitir que os administradores de conta echosign iniciem o login utilizando as suas credenciais de ecosign**.
   
   c. Sob **a Criação do Utilizador**, selecione adicionar automaticamente os **utilizadores autenticados através do SAML**.

   d. Pasta **Azure Ad Identifier**, que copiou do portal Azure para a caixa de texto **Idp Entity ID.**
    
   e. Colar **URL de login**, que copiou do portal Azure para a caixa de texto **URL do Idp Login.**
   
   f. Pasta **URL logout,** que copiou do portal Azure para a caixa de texto **Idp Logout URL.**

   exemplo, Abra o seu ficheiro **Certificado (Base64)** descarregado no Bloco de Notas. Copie o conteúdo da sua pasta e, em seguida, cole-o na caixa de texto **do Certificado IdP.**

   h. Selecione **Guardar alterações**.

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

Nesta secção, você permite que Britta Simon utilize o Azure single sign-on, concedendo acesso ao Adobe Sign.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Adobe Sign**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, escreva e selecione **Adobe Sign**.

    ![O link de sinal de Adobe na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-adobe-sign-test-user"></a>Criar utilizador de teste de sinal de Adobe

Para permitir que os utilizadores de Azure AD inscrevam-se no Adobe Sign, devem ser a provisionados no Adobe Sign. Esta é uma tarefa manual.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador do Adobe Sign ou APIs fornecidas pela Adobe Sign para fornecer contas de utilizador Azure AD. 

1. Inscreva-se no site da empresa **Adobe Sign** como administrador.

2. No menu em cima, selecione **Conta.** Em seguida, no painel esquerdo, selecione **Utilizadores & Grupos**  >  **Criar um novo utilizador**.
   
    ![Screenshot do site da empresa Adobe Sign, com Conta, Utilizadores &Grupos e Criar um novo utilizador em destaque](./media/adobe-echosign-tutorial/ic789524.png "Conta")
   
3. Na secção **Criar Novo Utilizador,** execute os seguintes passos:
   
    ![Screenshot da secção criar novo utilizador](./media/adobe-echosign-tutorial/ic789525.png "Criar Utilizador")
   
    a. Digite o **Endereço de E-mail,** **Primeiro Nome**e **Último Nome** de uma conta AD válida que pretenda obter nas caixas de texto relacionadas.
   
    b. Selecione **Criar Utilizador**.

>[!NOTE]
>O titular da conta Azure Ative Directory recebe um e-mail que inclui um link para confirmar a conta, antes de ficar ativa. 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Sinal de Adobe no Painel de Acesso, deverá ser automaticamente inscrito no Sinal de Adobe para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)