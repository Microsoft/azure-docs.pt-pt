---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Hornbill | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Hornbill.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 4131f1d0ef36eb49641ac184cd3f72fb719ea90e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92443430"
---
# <a name="tutorial-azure-active-directory-integration-with-hornbill"></a>Tutorial: Integração do Diretório Ativo Azure com Hornbill

Neste tutorial, você aprende a integrar Hornbill com Azure Ative Directory (Azure AD).
A integração de Hornbill com Azure AD proporciona-lhe os seguintes benefícios:

* Podes controlar o Azure AD que tem acesso ao Hornbill.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Hornbill (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Hornbill, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por Hornbill

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Hornbill suporta **SP** iniciado SSO
* Hornbill suporta o fornecimento **de utilizadores just in time**

## <a name="adding-hornbill-from-the-gallery"></a>Adicionando Hornbill da galeria

Para configurar a integração de Hornbill em Azure AD, você precisa adicionar Hornbill da galeria à sua lista de aplicações geridas saaS.

**Para adicionar Hornbill da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Hornbill,** selecione **Hornbill** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Hornbill na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Hornbill com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Hornbill.

Para configurar e testar o Azure AD com o Hornbill, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único hornbill](#configure-hornbill-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Hornbill test user](#create-hornbill-test-user)** - para ter uma contraparte de Britta Simon em Hornbill que está ligada à representação AZure AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com Hornbill, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Hornbill,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração Single Sign-On com** a página SAML, clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações de sinalização única do domínio hornbill e URLs](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/lib/saml/auth/simplesaml/module.php/saml/sp/metadata.php/saml`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte a [equipa de apoio ao Cliente Hornbill](https://www.hornbill.com/support/?request/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

### <a name="configure-hornbill-single-sign-on"></a>Configure Hornbill Single Sign-On

1. Numa janela diferente do navegador web, inicie sessão em Hornbill como Administrador de Segurança.

2. Na página inicial, clique em **Sistema**.

    ![Sistema Hornbill](./media/hornbill-tutorial/tutorial_hornbill_system.png)

3. Navegar para a **Segurança.**

    ![Segurança hornbill](./media/hornbill-tutorial/tutorial_hornbill_security.png)

4. Clique em **Perfis SSO**.

    ![Hornbill single](./media/hornbill-tutorial/tutorial_hornbill_sso.png)

5. No lado direito da página, clique em **Adicionar logotipo**.

    ![Hornbill adicionar](./media/hornbill-tutorial/tutorial_hornbill_addlogo.png)

6. Na barra **'Detalhes de Perfil',** clique no **logótipo Import SAML Meta**.

    ![Logotipo hornbill](./media/hornbill-tutorial/tutorial_hornbill_logo.png)

7. Na página Pop-up na caixa de texto **URL,** cole o **Url de metadados da Federação de Aplicações,** que copiou do portal Azure e clique em **Processo**.

    ![Processo hornbill](./media/hornbill-tutorial/tutorial_hornbill_process.png)

8. Após clicar no processo, os valores são automaticamente preenchidos automaticamente na secção **Detalhes do Perfil.**

    ![Página de Hornbill1](./media/hornbill-tutorial/tutorial_hornbill_ssopage.png)

    ![Página hornbill2](./media/hornbill-tutorial/tutorial_hornbill_ssopage1.png)

    ![Página de Hornbill3](./media/hornbill-tutorial/tutorial_hornbill_ssopage2.png)

9. Clique em **Guardar Alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome,** **insira BrittaSimon**.
  
    b. No campo **nome do utilizador,** **escreva brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Hornbill.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Hornbill**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Hornbill**.

    ![O link Hornbill na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-hornbill-test-user"></a>Criar utilizador de teste Hornbill

Nesta secção, um utilizador chamado Britta Simon é criado em Hornbill. A Hornbill suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Hornbill, um novo é criado após a autenticação.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a [equipa de suporte do Cliente Hornbill.](https://www.hornbill.com/support/?request/)

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Hornbill no Painel de Acesso, deverá ser automaticamente inscrito no Hornbill para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)