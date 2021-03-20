---
title: 'Tutorial: Integração do Diretório Ativo Azure com Work.com | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Work.com.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: a77b6472ea91a6ba4b6e31d1b7739aae236a1783
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92636158"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Tutorial: Integração do Diretório Ativo Azure com Work.com

Neste tutorial, aprende-se a integrar Work.com com o Azure Ative Directory (Azure AD).
Integrar Work.com com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Work.com.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos para Work.com (Sign-on Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com Work.com, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Work.com subscrição ativada por cada um

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Work.com apoia SSO iniciado **pela SP**

## <a name="adding-workcom-from-the-gallery"></a>Adicionar Work.com da galeria

Para configurar a integração de Work.com no AD Azure, é necessário adicionar Work.com da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Work.com da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Work.com**, selecione **Work.com** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

    ![Work.com na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com Work.com com base num utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Work.com.

Para configurar e testar o Azure AD com Work.com, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure Work.com único sign-on](#configure-workcom-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Crie Work.com utilizador de teste](#create-workcom-test-user)** - para ter uma contraparte de Britta Simon em Work.com que está ligada à representação AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

>[!NOTE]
>Para configurar um único sinal, é necessário configurar um nome de domínio personalizado Work.com. Tem de definir pelo menos um nome de domínio, testar o nome de domínio e implantá-lo em toda a sua organização.

Para configurar o Azure AD com Work.com, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Work.com,** selecione **'S-on' único**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Work.com Informações de assinatura única do domínio e URLs](common/sp-signonurl.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [Work.com equipa de apoio ao Cliente](https://help.salesforce.com/articleView?id=000159855&type=3) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configuração Work.com,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-workcom-single-sign-on"></a>Configurar Work.com Sign-On único

1. Inscreva-se no seu inquilino Work.com como administrador.

2. Ir para **a Configuração**.
   
    ![A screenshot mostra a configuração selecionada a partir do menu do utilizador.](./media/work-com-tutorial/ic794108.png "Configuração")

3. No painel de navegação à esquerda, na secção Gestão de **Dados,** clique em **Gestão de Domínio** para expandir a secção relacionada e, em seguida, clique em My **Domain** para abrir a página **My Domain.** 
   
    ![A screenshot mostra My Domain Select Domain Management in the Administrar painel.](./media/work-com-tutorial/ic767825.png "Meu Domínio")

4. Para verificar se o seu domínio foi configurado corretamente, certifique-se de que está no "**Passo 4 Implantado para os Utilizadores**" e reveja as suas "**Definições de Domínio**".
   
    ![Domínio implantado para utilizador](./media/work-com-tutorial/ic784377.png "Domínio implantado para utilizador")

5. Inscreva-se no seu inquilino Work.com.

6. Ir para **a Configuração**.
    
    ![A screenshot mostra a configuração selecionada a partir do menu do utilizador.](./media/work-com-tutorial/ic794108.png "Configuração")

7. Expanda o menu **Controlos de Segurança** e, em seguida, clique em **Definições de Sign-On únicas**.
    
    ![Definições de Sign-On única](./media/work-com-tutorial/ic794113.png "Definições de Sign-On única")

8. Na página de diálogo **de definições de Sign-On única,** execute os seguintes passos:
    
    ![ATIVADO SAML](./media/work-com-tutorial/ic781026.png "ATIVADO SAML")
    
    a. Selecione **SAML Ativado**.
    
    b. Clique **em Novo**.

9. Na secção **Definições de Sign-On Única SAML,** execute os seguintes passos:
    
    ![Configuração de Sign-On única saml](./media/work-com-tutorial/ic794114.png "Configuração de Sign-On única saml")
    
    a. Na caixa de texto **'Nome',** digite um nome para a sua configuração.  
       
    > [!NOTE]
    > Fornecer um valor para **nome** preenche automaticamente a caixa de texto **API Name.**
    
    b. Na caixa de texto **emitente,** cole o valor do **Identificador AD Azure** que copiou do portal Azure.
    
    c. Para fazer o upload do certificado descarregado a partir do portal Azure, clique em **Procurar**.
    
    d. Na caixa de texto **Id da Entidade,** escreva `https://salesforce-work.com` .
    
    e. Como **Tipo de Identidade SAML,** **selecione Assertion contém o ID da Federação a partir do objeto do Utilizador**.
    
    f. Como **Local de Identidade SAML,** selecione Identidade está no elemento **NomeIdentfier da declaração do sujeito**.
    
    exemplo, Na caixa de texto **URL do Fornecedor de Identidade,** cole o valor do URL de **login** que copiou do portal Azure.

    h. Na caixa de texto **URL do Fornecedor de Identidade,** cole o valor do URL **logout** que copiou do portal Azure.
    
    i. Como **prestador de serviços iniciou a ligação de pedido**, selecione HTTP **Post**.
    
    j. Clique em **Guardar**.

10. No seu portal clássico Work.com, no painel de navegação à esquerda, clique em **Gestão de Domínio** para expandir a secção relacionada e, em seguida, clique em **My Domain** para abrir a página **My Domain.** 
    
    ![A screenshot mostra o meu domínio selecionado a partir da Gestão de Domínios.](./media/work-com-tutorial/ic794115.png "Meu Domínio")

11. Na página **My Domain,** na secção de Marcação de Página de Início de **Sessão,** clique em **Editar**.
    
    ![A screenshot mostra a secção de marcação de página de login onde pode selecionar editar.](./media/work-com-tutorial/ic767826.png "Marcação de página de login")

12. Na página de **marcação de página de início de sessão,** na secção Serviço de **Autenticação,** é apresentado o nome das suas **Definições SSO SAML.** Selecione-o e, em seguida, clique em **Guardar**.
    
    ![A screenshot mostra o início de página de marcação onde pode selecionar o nome da sua definição, que é P E.](./media/work-com-tutorial/ic784366.png "Marcação de página de login")

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **do nome do utilizador** `brittasimon@yourcompanydomain.extension` . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, permitindo o acesso a Work.com.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Work.com**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Work.com**.

    ![O link Work.com na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-workcom-test-user"></a>Criar Work.com utilizador de teste

Para que os utilizadores do Azure Ative Directory possam inscrever-se, devem ser a provisionados para Work.com. No caso de Work.com, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento do utilizador, execute os seguintes passos:

1. Inscreva-se no site da empresa Work.com como administrador.

2. Ir para **a Configuração**.
   
    ![A screenshot mostra a configuração selecionada a partir do menu do utilizador.](./media/work-com-tutorial/ic794108.png "Configuração")

3. Vá a **Gerir \> utilizadores.**
   
    ![Gerir utilizadores](./media/work-com-tutorial/IC784369.png "Gerir Utilizadores")

4. Clique **em Novo Utilizador**.
   
    ![Todos os Utilizadores](./media/work-com-tutorial/IC794117.png "All Users")

5. Na secção De Edição de Utilizador, execute os seguintes passos, em atributos de uma conta AD Azure válida que pretende apresentar nas caixas de texto relacionadas:
   
    ![Edição de Utilizador](./media/work-com-tutorial/ic794118.png "Edição de Utilizador")
   
    a. Na caixa de texto **Name Name,** digite o **primeiro nome** do utilizador **Britta**.
    
    b. Na caixa de texto **Do Último Nome,** digite o **apelido** do utilizador **Simon**.
    
    c. Na caixa de texto **Alias,** digite o **nome** do utilizador **BrittaS**.
    
    d. Na caixa de texto **por e-mail,** digite o endereço de **e-mail** do Brittasimon@contoso.com utilizador.
    
    e. Na caixa de texto **do Nome do Utilizador,** digite um nome de utilizador do utilizador como Brittasimon@contoso.com .
    
    f. Na caixa de texto **Nick Name,** escreva um **nome nick** do utilizador **Simon**.
    
    exemplo, Selecione **Função,** **Licença de Utilizador** e **Perfil**.
    
    h. Clique em **Guardar**.  
      
    > [!NOTE]
    > O titular da conta AZure AD receberá um e-mail incluindo um link para confirmar a conta antes de ficar ativa.
    > 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Work.com no Painel de Acesso, deverá ser automaticamente inscrito no Work.com para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)