---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com HRworks Single Sign-On | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o HRworks Single Sign-On.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.openlocfilehash: d6b23213d9d986c62a227b3e182a22896d128222
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92442546"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com HRworks Single Sign-On

Neste tutorial, você vai aprender a integrar HRworks Single Sign-On com Azure Ative Directory (Azure AD). Quando integrar o HRworks Single Sign-On com Azure AD, pode:

* Controlo em Azure AD que tem acesso a HRworks Single Sign-On.
* Ativar os seus utilizadores a serem automaticamente inscritos na HRworks Single Sign-On com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* HRworks Assinatura única Sign-On única inscrição (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* HRworks Single Sign-On suporta **SSO** iniciado SP

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Adicionar HRworks Single Sign-On da galeria

Para configurar a integração da HRworks Single Sign-On em AD Azure, é necessário adicionar hRworks Single Sign-On da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva HRworks Single Sign-On** na caixa de pesquisa.
1. Selecione **HRworks Single Sign-On** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hrworks-single-sign-on"></a>Configure e teste Azure AD único sinal de inscrição para HRworks Single Sign-On

Configure e teste Azure AD SSO com HRworks Single Sign-On utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em HRworks Single Sign-On.

Para configurar e testar o Azure AD SSO com hrworks Single Sign-On, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure hRworks Single Sign-On SSO](#configure-hrworks-single-sign-on-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Crie o utilizador de teste de Sign-On Único HRworks](#create-hrworks-single-sign-on-test-user)** - para ter uma contrapartida de B.Simon em HRworks Single Sign-On que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **de sign-on única HRworks,** encontre a secção **'Gerir'** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de suporte Sign-On cliente da HRworks Single Sign-On](mailto:nadja.sommerfeld@hrworks.de) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **set up HRworks Single Sign-On,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, permitindo o acesso a HRworks Single Sign-On.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **HRworks Single Sign-On**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-hrworks-single-sign-on-sso"></a>Configure HRworks Single Sign-On SSO

1. Para automatizar a configuração dentro do HRworks Single Sign-On, é necessário instalar a **extensão do navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar HRworks Single Sign-On** irá direcioná-lo para a aplicação HRworks Single Sign-On. A partir daí, forneça as credenciais de administração para assinar na HRworks Single Sign-On. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-4.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o HRworks Single Sign-On manualmente, abra manualmente uma nova janela do navegador web e inscreva-se no seu site da empresa HRworks Single Sign-On como administrador e execute os seguintes passos:

1. Clique em **Básicos** de Segurança De  >    >  **Segurança**  >  **Único Sign-on** a partir do lado esquerdo da barra de menu e execute os seguintes passos:

    ![Configurar o início de sessão único](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Verifique a **caixa de inscrição única de utilização.**

    b. Selecione **os metadados XML** como **método de entrada de dados de metalagem**.

    c. Selecione **o identificador Individual NameID** como **Valor para NameID**.

    d. No Bloco de Notas, abra o Metadadata XML que descarregou a partir do portal Azure, copie o seu conteúdo e, em seguida, cole-o na caixa de texto dos **Metadados.**

    e. Clique em **Guardar**.

### <a name="create-hrworks-single-sign-on-test-user"></a>Criar utilizador de teste de Sign-On único da HRworks

Para ativar os utilizadores Azure AD, inscreva-se no HRworks Single Sign-On, devem ser ateados na HRworks Single Sign-On. Na HRworks Single Sign-On, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se na HRworks Single Sign-On como Administrador.

1. Clique em Pessoas **de Administrador**  >    >    >  **Pessoas Nova pessoa** do lado esquerdo da barra de menu.

     ![Screenshot mostra screenshot mostra página de trabalho de H R com Pessoas e Nova pessoa selecionada.](./media/hrworks-single-sign-on-tutorial/configure02.png)

1. No Pop-up, clique em **Seguinte**.

    ![A screenshot mostra uma lista de países para você escolher para a pessoa.](./media/hrworks-single-sign-on-tutorial/configure03.png)

1. No **Create new person with country for legal terms** pop-up, preencha os respetivos detalhes como Primeiro **Nome**, **Apelido** e clique em **Criar**.

    ![A screenshot mostra caixas de texto onde pode introduzir primeiro e último nome para a pessoa.](./media/hrworks-single-sign-on-tutorial/configure04.png)

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Sign-On único da HRworks no Painel de Acesso, deverá ser automaticamente inscrito no Sign-On Único de HRworks para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente hRworks Single Sign-On com Azure AD](https://aad.portal.azure.com/)