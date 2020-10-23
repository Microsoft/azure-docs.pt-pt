---
title: 'Tutorial: Integração do Azure Ative Directory com Avaliação Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Appraisd.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.openlocfilehash: 79db0a7bb769d6138bfeeb4e765621a1cffb4d14
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92457865"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Tutorial: Integrar avaliação com diretório ativo Azure

Neste tutorial, você vai aprender a integrar a Avaliação com O Diretório Ativo Azure (Azure AD). Quando integrar a Avaliação com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Avaliação.
* Permitir que os seus utilizadores sejam automaticamente inscritos na Avaliação com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Avaliação de um único sign-on (SSO) ativada subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. A Avaliação apoia **o SP e o IDP** iniciado sSO.

## <a name="adding-appraisd-from-the-gallery"></a>Adicionar Avaliação da galeria

Para configurar a integração de Appraisd em AD Azure, você precisa adicionar Avaliado da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva avaliação** na caixa de pesquisa.
1. Selecione **Avaliação** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com Avaliação usando um utilizador de teste chamado **B. Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Avaliação.

Para configurar e testar o Azure AD SSO com avaliação, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configurar a Avaliação](#configure-appraisd)** para configurar as definições SSO no lado da aplicação.
3. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B. Simon.
4. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b. Simon utilize um único sinal de Ad AD.
5. **[Crie o utilizador de teste de Avaliação](#create-appraisd-test-user)** para ter uma contraparte de B. Simon em Avaliação que esteja ligada à representação AZure AD do utilizador.
6. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **avaliadas,** encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** a aplicação está pré-configurada e os URLs necessários já estão pré-povoados com Azure. O utilizador precisa de guardar a configuração clicando no botão Guardar e executar os seguintes passos:

    a. Clique **em Definir URLs adicionais**.

    b. Na caixa de texto **do Estado de Retransmissão,** digite um URL: `<TENANTCODE>`

    c. Se desejar configurar a aplicação **no** modo iniciado sp, na caixa de texto **URL sign-on,** digite um URL utilizando o seguinte padrão: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Obtém o valor real de URL e Estado de retransmissão na página de Configuração SSO de avaliação, que é explicado mais tarde no tutorial.

1. A aplicação de avaliação espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem seguinte mostra a lista de atributos predefinidos, onde como **identificador** de nome é mapeado com **user.userprincipalname**. A aplicação avaliada espera que **o identificador** de nomes seja mapeado com **o user.mail**, pelo que é necessário editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![A screenshot mostra o painel de atributos do utilizador com o ícone de edição realçado.](common/edit-attribute.png)

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração de Avaliação,** copie os URL(s) apropriados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Configure Avaliação

1. Para automatizar a configuração dentro da Avaliação, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **'Avaliação de Configuração'** irá direcioná-lo para a aplicação Avaliada. A partir daí, forneça as credenciais de administração para assinar em Avaliação. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Appraisd manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa de Avaliação como administrador e execute os seguintes passos:

4. No topo à direita da página, clique no ícone **Definições** e, em seguida, navegue para **a Configuração**.

    ![A screenshot mostra a ligação de configuração chamada.](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. A partir do lado esquerdo do menu, clique no **único sinal de SEML**.

    ![O screenshot mostra as opções de Configuração com a opção de inscrição única SAML realçada.](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. Na página de **configuração Sign-On única SAML 2.0,** execute os seguintes passos:

    ![A screenshot mostra a página de configuração single Sign-On SAML 2.0 onde pode editar o Estado de Retransmissão padrão e o login iniciado pelo serviço U R L.](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Copie o valor **padrão do Estado do retransmissor** e cole-o na caixa de texto **do Estado de retransmissão** na **Configuração Básica SAML** no portal Azure.

    b. Copie o valor **URL de login iniciado pelo serviço** e **cole-o** na caixa de texto URL de acesso em **Configuração BÁSICA SAML** no portal Azure.

7. Percorra a mesma página em **Identificar utilizadores,** executar os seguintes passos:

    ![A screenshot mostra identificar os utilizadores onde pode introduzir valores a partir deste passo.](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Na caixa de texto de URL Sign-On fornecedor de **identidade,** cole o valor do URL de **login,** que copiou a partir do portal Azure e clique em **Guardar**.

    b. Na caixa de texto URL do **Fornecedor de Identidade,** cole o valor do **Identificador AD AD Azure,** que copiou a partir do portal Azure e clique em **Guardar**.

    c. No Bloco de Notas, abra o certificado codificado base-64 que descarregou a partir do portal Azure, copie o seu conteúdo e, em seguida, cole-o na caixa de **certificados X.509** e clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B. Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B. Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B. Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá a B. Simon usar a Azure um único sinal, permitindo o acesso à Avaliação.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **'Avaliação'.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B. Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-appraisd-test-user"></a>Criar utilizador de teste de avaliação

Para permitir que os utilizadores Azure AD inscrevam-se na Avaliação, devem ser aprovisionados. Em Avaliação, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se na Avaliação como Administrador de Segurança.

2. No topo direito da página, clique no ícone **Definições** e, em seguida, navegue para o **centro de Administração**.

    ![A screenshot mostra as opções de Definições onde pode selecionar o centro de administração.](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Na barra de ferramentas no topo da página, clique em **Pessoas**e, em seguida, navegue para **Adicionar um novo utilizador**.

    ![O Screenshot mostra a página de Avaliação com Pessoas e Adicione um novo utilizador chamado.](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Na página **'Adicionar' um novo utilizador,** execute os seguintes passos:

    ![A screenshot mostra a página de utilizador Adicionar uma nova página de utilizador.](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. Na caixa de texto **do primeiro nome,** insira o primeiro nome do utilizador como **Britta**.

    b. Na caixa de texto **do último nome,** insira o último nome do utilizador como **simon**.

    c. Na caixa de texto **por e-mail,** insira o e-mail do utilizador como `B. Simon@contoso.com` .

    d. Clique **em Adicionar utilizador**.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo de Avaliação no Painel de Acesso, deverá ser automaticamente inscrito na Avaliação para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)