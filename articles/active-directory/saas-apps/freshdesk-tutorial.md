---
title: 'Tutorial: Integração de Diretório Ativo Azure com freshDesk [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e75a45bc4ccd2614520fda39e09996c2286bdbd0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227621"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Tutorial: Integração de Diretório Ativo Azure com freshDesk

Neste tutorial, aprende-se a integrar o FreshDesk com o Azure Ative Directory (Azure AD).
Integrar o FreshDesk com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao FreshDesk.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no FreshDesk (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a FreshDesk, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada por um único sinal do FreshDesk

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* FreshDesk suporta **SP** iniciado SSO

## <a name="adding-freshdesk-from-the-gallery"></a>Adicionar FreshDesk da galeria

Para configurar a integração do FreshDesk em Azure AD, precisa de adicionar freshDesk da galeria à sua lista de aplicações saaS geridas.

**Para adicionar FreshDesk da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **FreshDesk**, selecione **FreshDesk** do painel de resultados e, em seguida, clique em adicionar o botão **Adicionar** a aplicação.

     ![FreshDesk na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure com o FreshDesk com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no FreshDesk.

Para configurar e testar o único sinal de Azure AD com o FreshDesk, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o FreshDesk Single Sign-On](#configure-freshdesk-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create FreshDesk test user](#create-freshdesk-test-user)** - para ter uma contrapartida de Britta Simon no FreshDesk que está ligada à representação do utilizador azure AD.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com o FreshDesk, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **FreshDesk,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Informação de inscrição única do FreshDesk Domain e URLs](common/sp-identifier.png)

    a. No **Sign on URL** text box, escreva `https://<tenant-name>.freshdesk.com` um URL utilizando o seguinte padrão: ou qualquer outro valor que a Freshdesk sugeriu.

    b. Na caixa de texto **identifier (Id** da entidade), `https://<tenant-name>.freshdesk.com` digite um URL utilizando o seguinte padrão: ou qualquer outro valor sugerido pela Freshdesk.

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte do [Cliente FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. A aplicação FreshDesk espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra um exemplo para isto. O valor padrão do **Identificador** de Utilizador Único é **user.userprincipalname,** mas o FreshDesk espera que este seja mapeado com o endereço de e-mail do utilizador. Para isso pode utilizar o atributo **user.mail** da lista ou utilizar o valor de atributo apropriado com base na configuração da sua organização. 

    ![image](common/edit-attribute.png)

6. Na secção **Reivindicações** do Utilizador no diálogo **de Atributos** do Utilizador, edite as reclamações utilizando o **ícone Editar** ou adicione as reclamações utilizando a adição de **nova alegação** para configurar o atributo token SAML, como mostrado na imagem acima e executar os seguintes passos:
    
    | Nome | Atributo fonte |
    | ---------------| --------------- |
    | Identificador único de utilizador | utilizador.mail |

    a. Clique **Em adicionar nova alegação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Name,** digite o nome do atributo mostrado para essa linha.

    c. Deixe o espaço de **nome em** branco.

    d. Selecione Origem como **Atributo**.

    e. Na lista de **atributos Fonte,** digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

7. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

8. Abra **o Comando Prompt** e execute os seguintes comandos:

    a. Introduza `certutil.exe -dump FreshDesk.cer` o valor no pedido de comando.

    > [!NOTE]
    > Aqui **freshDesk.cer** é o certificado que descarregou do portal Azure.

    b. Copie o valor **Cert Hash (sha256)** e cole-o no Bloco de Notas. 

9. Na secção **Configurar FreshDesk,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-freshdesk-single-sign-on"></a>Configure o signo único do FreshDesk

1. Numa janela diferente do navegador web, inicie sessão no site da sua empresa Freshdesk como administrador.

2. Selecione o **ícone Definições** e na secção **De segurança,** execute os seguintes passos:

    ![Início de Sessão Único](./media/freshdesk-tutorial/IC776770.png "Início de Sessão Único")
  
    a. Para **um único sinal ligado (SSO)**, selecione **On**.

    b. Selecione **SAML SSO**.

    c. Na caixa de texto **URL de Login SAML,** colhe o valor URL de **Login,** que copiou do portal Azure.

    d. Na caixa de texto **logout URL,** cola o valor URL do **Logout,** que copiou do portal Azure.

    e. Na caixa de texto **impressões digitais** do Certificado de Segurança, colar **cert Hash (sha256)** valor que obteve anteriormente.
  
    f. Clique em **Guardar**.

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

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao FreshDesk.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **FreshDesk**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, escreva e selecione **FreshDesk**.

    ![O link FreshDesk na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-freshdesk-test-user"></a>Criar o utilizador de teste FreshDesk

Para permitir que os utilizadores de Anúncios Azure entrem no FreshDesk, devem ser aprovisionados no FreshDesk.  
No caso do FreshDesk, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Faça login no seu inquilino da **Freshdesk.**

2. No menu em cima, clique **em Admin**.

    ![Administração](./media/freshdesk-tutorial/IC776772.png "Administrador")

3. No separador **Definições Gerais,** clique em **Agentes**.
  
    ![Agentes](./media/freshdesk-tutorial/IC776773.png "Agentes")

4. Clique em **Novo Agente**.

    ![Novo Agente](./media/freshdesk-tutorial/IC776774.png "Novo Agente")

5. No diálogo informação do agente, execute os seguintes passos:

    ![Informação do Agente](./media/freshdesk-tutorial/IC776775.png "Informação do Agente")

    a. Na caixa de texto **por e-mail,** digite o endereço de e-mail Azure AD da conta Azure AD que pretende fornecer.

    b. Na caixa de texto **Nome Completo,** digite o nome da conta Azure AD que pretende fornecer.

    c. Na caixa de texto **Title,** digite o título da conta Azure AD que pretende fornecer.

    d. Clique em **Guardar**.

    >[!NOTE]
    >O titular da conta Azure AD receberá um e-mail que inclui um link para confirmar a conta antes de ser ativada.
    >
    >[!NOTE]
    >Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador freshdesk ou APIs fornecidas pela Freshdesk para fornecer contas de utilizador azure AD ao FreshDesk.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo FreshDesk no Painel de Acesso, deverá ser automaticamente inscrito no FreshDesk para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

