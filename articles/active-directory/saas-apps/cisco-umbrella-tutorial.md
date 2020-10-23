---
title: 'Tutorial: Integração do Azure Ative Directory com a Cisco Umbrella Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Cisco Umbrella.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: ec2c0089295cdd269b5bb3bccf0baaef3cb14849
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456127"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella"></a>Tutorial: Integração do Azure Ative Directory com a Cisco Umbrella

Neste tutorial, aprende-se a integrar a Cisco Umbrella com o Azure Ative Directory (Azure AD).
A integração da Cisco Umbrella com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso à Cisco Umbrella.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Cisco Umbrella (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Cisco Umbrella, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Cisco Umbrella assinatura única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Cisco Umbrella apoia **SP e IDP** iniciado SSO

## <a name="adding-cisco-umbrella-from-the-gallery"></a>Adicionando Cisco Umbrella da galeria

Para configurar a integração da Cisco Umbrella no Azure AD, é necessário adicionar a Cisco Umbrella da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Cisco Umbrella da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Cisco Umbrella,** selecione **Cisco Umbrella** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Cisco Umbrella na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com [Nome de aplicação] com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em [Nome de aplicação].

Para configurar e testar o único sinal de Azure AD com [Nome da aplicação], é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure cisco Umbrella Single Sign-On](#configure-cisco-umbrella-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Cisco Umbrella test user](#create-cisco-umbrella-test-user)** - ter uma contraparte de Britta Simon na Cisco Umbrella que está ligada à representação AD AZure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com [Nome da aplicação], execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Cisco Umbrella,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

    ![Cisco Umbrella Domain e URLs informações únicas de assinatura](common/both-preintegrated-signon.png)

    a. Se desejar configurar a aplicação em modo de intição **SP,** execute os seguintes passos:

    b. Clique **em Definir URLs adicionais**.

    c. Na caixa de texto **url de entrada de inscrição,** digite um URL: `https://login.umbrella.com/sso`

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configuração da Cisco Umbrella,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-cisco-umbrella-single-sign-on"></a>Configurar cisco Umbrella Single Sign-On

1. Numa janela de navegador diferente, inscreva-se no site da empresa Cisco Umbrella como administrador.

2. A partir do lado esquerdo do menu, clique em **Administração** e navegue para **autenticação** e, em seguida, clique em **SAML**.

    ![O Administrador](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_admin.png)

3. Escolha **outro e** clique em **NEXT**.

    ![O Outro](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_other.png)

4. Nos **Metadados da Cisco Umbrella**, página, clique em **SEGUINTE**.

    ![Os metadados](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_metadata.png)

5. No **separador Upload Metadata,** se tiver configurado SAML pré-configurado, **selecione Clique aqui para alterá-los** e siga os passos abaixo.

    ![O Próximo](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_next.png)

6. Na **Opção A: Carregar**o ficheiro XML , carregar o ficheiro **XML dos Metadados da Federação** que descarregou a partir do portal Azure e depois de carregar metadados os valores abaixo são automaticamente preenchidos automaticamente e depois clique em **SEGUINTE**.

    ![O ficheiro de escolha](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_choosefile.png)

7. Na secção **de configuração SAML validado,** clique em **TESTAR a configuração DO SAML**.

    ![O Teste](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_test.png)

8. Clique **em SAVE**.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso à Cisco Umbrella.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Cisco Umbrella**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, escreva e selecione **Cisco Umbrella.**

    ![O link Cisco Umbrella na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-cisco-umbrella-test-user"></a>Criar utilizador de teste Cisco Umbrella

Para permitir que os utilizadores da Azure AD entrem na Cisco Umbrella, devem ser a provisionados na Cisco Umbrella.  
No caso da Cisco Umbrella, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Numa janela de navegador diferente, inscreva-se no site da empresa Cisco Umbrella como administrador.

2. Do lado esquerdo do menu, clique em **Administração** e navegue para **contas.**

    ![A Conta](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_account.png)

3. Na página **'Contas',** clique em **Adicionar** no lado superior direito da página e executar os seguintes passos.

    ![O Utilizador](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_createuser.png)

    a. No campo **Primeiro Nome,** insira o primeiro nome como **Britta.**

    b. No campo **Último Nome,** insira o último nome como **o Simon.**

    c. A partir do **Papel de Administração Delegado,** selecione o seu papel.
  
    d. No campo **'Endereço de e-mail',** insira o endereço de e-mail do utilizador como **o \@ brittasimon contoso.com**.

    e. No campo **Palavra-Passe,** insira a sua palavra-passe.

    f. No campo **Palavra-Passe Confirmar,** introduza novamente a sua palavra-passe.

    exemplo, Clique **em CREATE**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo da Cisco Umbrella no Painel de Acesso, deverá ser automaticamente inscrito no Cisco Umbrella para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)