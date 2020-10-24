---
title: 'Tutorial: Integração do Azure Ative Directory com Procore SSO / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Procore SSO.
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
ms.openlocfilehash: 13f8f1067ce7c9fe55160400d20ec0b20788c17b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515292"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Tutorial: Integração do Azure Ative Directory com Procore SSO

Neste tutorial, aprende-se a integrar o Procore SSO com o Azure Ative Directory (Azure AD).
A integração do Procore SSO com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Procore SSO.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Procore SSO (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Procore SSO, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Procore SSO única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Procore SSO suporta **IDP** iniciado SSO

## <a name="adding-procore-sso-from-the-gallery"></a>Adicionar SSO Procore da galeria

Para configurar a integração do Procore SSO em AD Azure, precisa adicionar Procore SSO da galeria à sua lista de aplicações geridas para o SaaS.

**Para adicionar Procore SSO da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Procore SSO**, selecione **Procore SSO** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Procore SSO na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com Procore SSO com base num utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Procore SSO.

Para configurar e testar o Azure AD com o Procore SSO, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure procore Procore SSO Single Sign-On](#configure-procore-sso-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Procore SSO test user](#create-procore-sso-test-user)** - para ter uma contraparte de Britta Simon em Procore SSO que está ligada à representação AD Ad Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o Procore SSO, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Procore SSO,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

    ![Procore SSO Domain e URLs informações únicas de sindúsia](common/preintegrated.png)

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar SSO Procore,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-procore-sso-single-sign-on"></a>Configurar procore SSO Single Sign-On

1. Para configurar um único sinal no lado **Procore SSO,** inscreva-se no site da empresa procore como administrador.

2. A partir da queda da caixa de ferramentas, clique no **Administrador** para abrir a página de definições SSO.

    ![A screenshot mostra o site da empresa Procore com o Diretório selecionado.](./media/procoresso-tutorial/procore_tool_admin.png)

3. Cole os valores nas caixas como descrito abaixo...

    ![A screenshot mostra a caixa de diálogo Add a Person.](./media/procoresso-tutorial/procore_setting_admin.png) 

    a. Na caixa de texto **URL de sinal único no emitente,** cole o valor do **Identificador AD AD Azure** que copiou a partir do portal Azure.

    b. Na caixa **URL do sinal SAML no alvo,** cole o valor do URL de **login** que copiou a partir do portal Azure.

    c. Abra agora o **Metdata XML** da Federação descarregado acima do portal Azure e copie o certificado na etiqueta **denominada X509Certificate**. Cole o valor copiado na **caixa de certificado Single Sign On x509.**

4. Clique em **Guardar Alterações**.

5. Após estas definições, tem de enviar o nome de **domínio** (por **exemplo, contoso.com**) através do qual está a iniciar sessão no Procore para a equipa de Suporte [Procore](https://support.procore.com/) e ativará sSO federado para esse domínio.

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

Nesta secção, você permite que Britta Simon utilize a Azure single sign-on, concedendo acesso ao Procore SSO.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Procore SSO**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Procore SSO**.

    ![O link Procore SSO na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-procore-sso-test-user"></a>Criar utilizador de teste Procore SSO

Siga os passos abaixo para criar um utilizador de teste Procore no lado Procore SSO.

1. Inscreva-se no site da empresa procore como administrador.    

2. A partir da queda da caixa de ferramentas, clique no **Diretório** para abrir a página do diretório da empresa.

    ![O Screenshot mostra o site da empresa Procore com o Diretório selecionado a partir da caixa de ferramentas.](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Clique em **Adicionar a Opção Pessoa** para abrir o formulário e introduzir executar as seguintes opções -

    ![A screenshot mostra o Add a Person to Boylan Construction onde pode introduzir informações do utilizador.](./media/procoresso-tutorial/Procore_user_add.png)

    a. Na caixa de texto **Name Name,** escreva o primeiro nome do utilizador como **Britta**.

    b. Na caixa de texto **do último nome,** escreva o apelido do utilizador como **Simon**.

    c. Na caixa de texto **do Endereço de E-mail,** escreva o endereço de e-mail do utilizador como BrittaSimon@contoso.com .

    d. Selecione **o modelo de permissão** como **aplicar o modelo de permissão mais tarde.**

    e. Clique em **Criar**.

4. Verifique e atualize os detalhes para o contacto recém-adicionado.

    ![A screenshot mostra uma página de edição onde pode verificar as definições do utilizador.](./media/procoresso-tutorial/Procore_user_check.png)

5. Clique em **Salvar e Enviar Convite** (se for necessário um convite por correio) ou **Guardar** (Guardar diretamente) para completar o registo do utilizador.
    
    ![A screenshot mostra as Definições de Projeto Atuais onde pode guardar e enviar convite.](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Procore SSO no Painel de Acesso, deverá ser automaticamente inscrito no Procore SSO para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)