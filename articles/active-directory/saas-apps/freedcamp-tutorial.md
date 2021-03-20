---
title: 'Tutorial: Integração do Diretório Ativo Azure com a | da Freedcamp Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Freedcamp.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 19378251408d55868ed844a5505ae48ece55dc3b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92451500"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>Tutorial: Integrar Freedcamp com diretório ativo Azure

Neste tutorial, você vai aprender a integrar Freedcamp com Azure Ative Directory (Azure AD). Quando integrar o Freedcamp com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Freedcamp.
* Permita que os seus utilizadores sejam automaticamente inscritos no Freedcamp com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por freedcamp single sign-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. A Freedcamp apoia **o SP e o IDP** iniciado sSO.

## <a name="adding-freedcamp-from-the-gallery"></a>Adicionando Freedcamp da galeria

Para configurar a integração do Freedcamp no Azure AD, precisa adicionar o Freedcamp da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** escreva **Freedcamp** na caixa de pesquisa.
1. Selecione **Freedcamp** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com Freedcamp usando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Freedcamp.

Para configurar e testar a Azure AD SSO com a Freedcamp, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Freedcamp](#configure-freedcamp)** para configurar as definições SSO no lado da aplicação.
3. **[Crie um utilizador de teste AZure AD](#create-an-azure-ad-test-user)** para testar o Azure AD com Britta Simon.
4. **[Atribua ao utilizador de teste AZure AD](#assign-the-azure-ad-test-user)** para permitir que a Britta Simon utilize um único sinal de Ad AD.
5. Crie o utilizador de **[teste Freedcamp](#create-freedcamp-test-user)** para ter uma contraparte de Britta Simon em Freedcamp que está ligada à representação AD AD do utilizador.
6. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Freedcamp,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    1. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Os utilizadores também podem introduzir os valores de url no que diz respeito ao seu próprio domínio de cliente e podem não ser necessariamente do `freedcamp.com` padrão, podem introduzir qualquer valor específico do domínio do cliente, específico da sua aplicação. Também pode contactar [a equipa de suporte do Cliente Freedcamp](mailto:devops@freedcamp.com) para mais informações sobre padrões de url.

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Freedcamp,** copie os URL(s) apropriados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-freedcamp"></a>Configure Freedcamp

1. Para automatizar a configuração dentro do Freedcamp, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Setup Freedcamp** irá direcioná-lo para a aplicação Freedcamp. A partir daí, forneça as credenciais de administrador para assinar em Freedcamp. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração](common/setup-sso.png)

3. Se quiser configurar o Freedcamp manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Freedcamp como administrador e execute os seguintes passos:

4. No canto superior direito da página, clique no **perfil** e, em seguida, navegue para **a Minha Conta**.

    ![Screenshot que mostra "Profile" e "My Account" selecionados.](./media/freedcamp-tutorial/config01.png)

5. Do lado esquerdo da barra de menu, clique no **SSO** e na página de **ligações SSO** execute os seguintes passos:

    ![Screenshot que mostra "S S O" selecionado na barra de menu do lado esquerdo e na página "As ligações S O" com valores introduzidos e o botão "Enviar" selecionado.](./media/freedcamp-tutorial/config02.png)

    a. Na caixa de texto **do título,** digite o título.

    b. Na caixa de texto **de ID da Entidade,** cole o valor **identificador Azure AD,** que copiou a partir do portal Azure.

    c. Na caixa de texto **URL de login,** cole o valor URL do **Login,** que copiou a partir do portal Azure.

    d. Abra o certificado codificado Base64 no bloco de notas, copie o seu conteúdo e cole-o na caixa de texto **do Certificado.**

    e. Clique **em Submeter.**

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado Britta Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que Britta Simon use a Azure single sign-on, concedendo acesso ao Freedcamp.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Freedcamp**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-freedcamp-test-user"></a>Criar utilizador de teste Freedcamp

Para permitir que os utilizadores da AZure AD, inscrevam-se no Freedcamp, devem ser a provisionados no Freedcamp. Em Freedcamp, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Numa janela diferente do navegador, inscreva-se no Freedcamp como Administrador de Segurança.

2. No canto superior direito da página, clique no **perfil** e, em seguida, navegue para **Gerir o Sistema**.

    ![Configuração freedcamp](./media/freedcamp-tutorial/config03.png)

3. No lado direito da página 'Gerir sistema', execute os seguintes passos:

    ![Screenshot que mostra o botão "Adicionar ou Convidar utilizadores" selecionado, o campo "Email" realçado e o botão "Adicionar Utilizador" selecionado.](./media/freedcamp-tutorial/config04.png)

    a. Clique em **Adicionar ou convidar utilizadores.**

    b. Na caixa de texto **por e-mail,** insira o e-mail do utilizador como `Brittasimon@contoso.com` .

    c. Clique em **Adicionar Utilizador**.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o azulejo Freedcamp no Painel de Acesso, deverá ser automaticamente inscrito no Freedcamp para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)