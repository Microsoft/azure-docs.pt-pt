---
title: 'Tutorial: Integração do Azure Ative Directory com Peakon Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Peakon.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: d9850c83fc949aa3a2b0521dfa0d41d49b7283bd
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515638"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Tutorial: Integração do Diretório Ativo Azure com Peakon

Neste tutorial, você aprende a integrar Peakon com Azure Ative Directory (Azure AD).
A integração de Peakon com Azure AD proporciona-lhe os seguintes benefícios:

* Podes controlar o Azure AD que tem acesso a Peakon.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos em Peakon (Sign-on Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com peakon, você precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada por peakon

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Peakon apoia **SP** e **IDP** iniciado SSO

## <a name="adding-peakon-from-the-gallery"></a>Adicionando Peakon da galeria

Para configurar a integração de Peakon em Azure AD, você precisa adicionar Peakon da galeria à sua lista de aplicações geridas saaS.

**Para adicionar Peakon da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Peakon,** selecione **Peakon** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![Peakon na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Peakon com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Peakon.

Para configurar e testar o Azure AD com peakon, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure peakon Single Sign-On](#configure-peakon-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Peakon test user](#create-peakon-test-user)** - para ter uma contraparte de Britta Simon em Peakon que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com Peakon, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Peakon,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![Informações de sign-on de Domínio Peakon e URLs](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://app.peakon.com/saml/<companyid>/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://app.peakon.com/saml/<companyid>/assert`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Screenshot que mostra o "set adicional U R Ls" selecionado com a caixa de texto "Sing on U R L" realçada.](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL:  `https://app.peakon.com/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real que é explicado mais tarde no tutorial. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Raw)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificateraw.png)

7. Na secção **Configurar Peakon,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-peakon-single-sign-on"></a>Configurar peakon single Sign-On

1. Numa janela diferente do navegador web, inscreva-se em Peakon como administrador.

2. Na barra de menus do lado esquerdo da página, clique em **Configuração**e navegue para **Integrações**.

    ![O Config](./media/peakon-tutorial/tutorial_peakon_config.png)

3. Na página **Integrações,** clique em **'Sign-On' único**.

    ![O Single](./media/peakon-tutorial/tutorial_peakon_single.png)

4. Na secção **'S-On' único,** clique em **Ativar**.

    ![A ativação](./media/peakon-tutorial/tutorial_peakon_enable.png)

5. No **único sinal de inscrição para os colaboradores que utilizem** a secção SAML, execute os seguintes passos:

    ![O saml](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. Na caixa de texto **SSO Login URL,** cole o valor do URL de **Login,** que copiou a partir do portal Azure.

    b. Na caixa de texto **SSO Logout URL,** cole o valor do **URL logout,** que copiou a partir do portal Azure.

    c. Clique **em Escolher** o ficheiro para fazer o upload do certificado que descarregou a partir do portal Azure, para a caixa de Certificado.

    d. Clique no **ícone** para copiar o **ID** da Entidade e cole na caixa de texto **identifier** na secção **de Configuração SAML Básica** no portal Azure.

    e. Clique no **ícone** para copiar o **URL de resposta (ACS)** e cole na caixa de texto **URL resposta** na secção **de configuração SAML básica** no portal Azure.

    f. Clique em **Guardar**

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador****brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Peakon.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Peakon**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Peakon**.

    ![O link Peakon na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-peakon-test-user"></a>Criar utilizador de teste Peakon

Para permitir que os utilizadores de Azure AD inscrevam-se em Peakon, devem ser a provisionados em Peakon.  
No caso de Peakon, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa Peakon como administrador.

2. Na barra de menus do lado esquerdo da página, clique em **Configuração**e, em seguida, navegue para **os Colaboradores**.

    ![O empregado](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. No lado superior direito da página, clique **em Adicionar empregado**.

      ![O colaborador de adicionar](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Na página de diálogo **do novo funcionário,** execute os seguintes passos:

     ![O novo empregado](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. Na caixa de texto **Name,** escreva o primeiro nome como **Britta** e sobrenome como **simon**.

    b. Na caixa de sms **do Email,** digite o endereço de e-mail como **o \@ contoso.com brittasimon**.

    c. Clique **em Criar empregado.**

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Peakon no Painel de Acesso, deverá ser automaticamente inscrito no Peakon para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)