---
title: 'Tutorial: Integração do Diretório Ativo Azure com Work.com Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e Work.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7a6dc16eef1bb36a5bd6cbf0502a83481230bc0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67087087"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Tutorial: Integração do Diretório Ativo Azure com Work.com

Neste tutorial, aprende-se a integrar Work.com com o Azure Ative Directory (Azure AD).
Integrar Work.com com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso a Work.com.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos para Work.com (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com Work.com, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Work.com assinatura ativada por um único sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Work.com apoia **SP** iniciado SSO

## <a name="adding-workcom-from-the-gallery"></a>Adicionando Work.com da galeria

Para configurar a integração de Work.com em Azure AD, precisa adicionar Work.com da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Work.com da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Work.com,** selecione **Work.com** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

    ![Work.com na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único signo da Azure AD com Work.com com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Work.com.

Para configurar e testar o único sinal de Azure AD com Work.com, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure Work.com Single Sign-On](#configure-workcom-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie Work.com utilizador](#create-workcom-test-user)** de teste - para ter uma contrapartida de Britta Simon em Work.com que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

>[!NOTE]
>Para configurar um único sinal, precisa de configurar um nome de domínio personalizado Work.com. Tens de definir pelo menos um nome de domínio, testar o teu nome de domínio e implantá-lo para toda a tua organização.

Para configurar o único sinal de Azure AD com Work.com, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Work.com,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Work.com Domínio e URLs informações únicas de inscrição](common/sp-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [Work.com equipa](https://help.salesforce.com/articleView?id=000159855&type=3) de apoio ao Cliente para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **Configurar Work.com,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-workcom-single-sign-on"></a>Configure Work.com único sinal

1. Inscreva-se no seu Work.com inquilino como administrador.

2. Vá para a **Configuração.**
   
    ![Configuração](./media/work-com-tutorial/ic794108.png "Configuração")

3. No painel de navegação à esquerda, na secção **Administração,** clique em **Gestão** de Domínio para expandir a secção relacionada e, em seguida, clique em **My Domain** para abrir a página **My Domain.** 
   
    ![O meu domínio](./media/work-com-tutorial/ic767825.png "O meu domínio")

4. Para verificar se o seu domínio foi configurado corretamente, certifique-se de que está em "**Passo 4 Implantado para Utilizadores**" e reveja as suas**definições**de domínio " My Domain ".
   
    ![Domínio implantado para utilizador](./media/work-com-tutorial/ic784377.png "Domínio implantado para utilizador")

5. Inscreva-se no seu Work.com inquilino.

6. Vá para a **Configuração.**
    
    ![Configuração](./media/work-com-tutorial/ic794108.png "Configuração")

7. Expanda o menu **controlos** de segurança e, em seguida, clique em **definições de sinal único**.
    
    ![Definições únicas de inscrição](./media/work-com-tutorial/ic794113.png "Definições únicas de inscrição")

8. Na página de diálogo **de definições de início de sessão simples,** execute os seguintes passos:
    
    ![SAML Habilitado](./media/work-com-tutorial/ic781026.png "SAML Habilitado")
    
    a. Selecione **SAML Ativado**.
    
    b. Clique em **Novo**.

9. Na secção Definições de **Sinais Únicos SAML,** execute os seguintes passos:
    
    ![Definição de sinal único SAML](./media/work-com-tutorial/ic794114.png "Definição de sinal único SAML")
    
    a. Na caixa de texto **Name,** digite um nome para a sua configuração.  
       
    > [!NOTE]
    > Fornecer um valor para **o nome** povoa automaticamente a caixa de texto **Nome API.**
    
    b. Na caixa de texto **Emitida,** cola o valor do **Identificador Azure AD** que copiou do portal Azure.
    
    c. Para fazer o upload do certificado descarregado do portal Azure, clique em **Navegar**.
    
    d. Na caixa de texto `https://salesforce-work.com` **Id entidade,** escreva .
    
    e. Como Tipo de **Identidade SAML,** selecione **Afirmação contém o ID da Federação do objeto utilizador**.
    
    f. Como **Localização de Identidade SAML,** selecione Identidade está no elemento **NameIdentfier da declaração do Sujeito**.
    
    g. Na caixa de texto URL do Fornecedor de **Identidade Login,** colhe o valor do URL de **Login** que copiou do portal Azure.

    h. Na caixa de texto URL do Fornecedor de **Identidade Logout,** colá o valor do URL de **Logout** que copiou do portal Azure.
    
    i. Como **Prestador de ServiçoS Iniciado Pedido Vinculativo,** selecione **HTTP Post**.
    
    j. Clique em **Guardar**.

10. No seu portal clássico Work.com, no painel de navegação à esquerda, clique em **Domain Management** para expandir a secção relacionada e, em seguida, clique em **My Domain** para abrir a página **My Domain.** 
    
    ![O meu domínio](./media/work-com-tutorial/ic794115.png "O meu domínio")

11. Na página **My Domain,** na secção **'Marcação de página de login',** clique em **Editar**.
    
    ![Marca de página de login](./media/work-com-tutorial/ic767826.png "Marca de página de login")

12. Na página de marcação de página de **login,** na secção Serviço de **Autenticação,** é apresentado o nome das **definições SAML SSO.** Selecione-o e, em seguida, clique em **Guardar**.
    
    ![Marca de página de login](./media/work-com-tutorial/ic784366.png "Marca de página de login")

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo `brittasimon@yourcompanydomain.extension`de campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso a Work.com.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Work.com**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Work.com**.

    ![O link Work.com na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-workcom-test-user"></a>Criar Work.com utilizador de teste

Para que os utilizadores do Diretório Ativo do Azure possam iniciar a sua participação, devem ser aprovisionados para Work.com. No caso de Work.com, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o fornecimento do utilizador, execute os seguintes passos:

1. Inscreva-se no site da empresa Work.com como administrador.

2. Vá para a **Configuração.**
   
    ![Configuração](./media/work-com-tutorial/IC794108.png "Configuração")

3. Vá para **gerir utilizadores \> utilizadores**.
   
    ![Gerir utilizadores](./media/work-com-tutorial/IC784369.png "Gerir Utilizadores")

4. Clique em **Novo Utilizador**.
   
    ![Todos os Utilizadores](./media/work-com-tutorial/IC794117.png "All Users")

5. Na secção Edição do Utilizador, execute os seguintes passos, em atributos de uma conta Azure AD válida que pretende fornecer nas caixas de texto relacionadas:
   
    ![Edição do utilizador](./media/work-com-tutorial/ic794118.png "Edição do utilizador")
   
    a. Na caixa de texto **First Name,** digite o **primeiro nome** da **utilizadora Britta**.
    
    b. Na caixa de texto **Last Name,** digite o **último nome** do utilizador **Simon**.
    
    c. Na caixa de texto **Danas,** digite o **nome** do utilizador **BrittaS**.
    
    d. Na caixa de texto **por e-mail,** escreva o endereço de **e-mail** do utilizador. Brittasimon@contoso.com
    
    e. Na caixa de texto nome do **utilizador,** digite um nome de utilizador como Brittasimon@contoso.com.
    
    f. Na caixa de texto **Nick Name,** escreva um **nome nick** do utilizador **Simon**.
    
    g. Selecione **Função,** **Licença de Utilizador**e **Perfil**.
    
    h. Clique em **Guardar**.  
      
    > [!NOTE]
    > O titular da conta Azure AD receberá um e-mail incluindo um link para confirmar a conta antes de se tornar ativa.
    > 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Work.com no Painel de Acesso, deverá ser automaticamente inscrito no Work.com para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

